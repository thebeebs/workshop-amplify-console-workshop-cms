+++
title = "Connecting to Wordpress"
chapter = false
weight = 10
+++

Now we have a basic site lets connect it up to a CMS. In this exmaple we will use Wordpress.

First we are going to need to add a plugin. This plugin allows us connect to a wordpress website.

```
npm install --save gatsby-source-wordpress
```

{{% notice tip %}}
Further documentation for this plugin can be found here: https://www.gatsbyjs.org/packages/gatsby-source-wordpress/ it includes the different authentication settings that maybe required if you want to connect it to your own self hosted Wordpress Installation. 
{{% /notice %}}

### Configure the Plugin

In the `gatsby.config` add the following to **line 29**.
```
    {
    resolve: "gatsby-source-wordpress",
      options: {
        baseUrl: "amplifyconvert.wordpress.com",
        protocol: "https",
        // Indicates whether the site is hosted on wordpress.com.
        hostingWPCOM: true,
        useACF: false,
        acfOptionPageIds: [],
        auth: {
          wpcom_app_clientSecret: "5ZlJ906Ka5HFYj02TzkvifTZHABgKRKAmTrSngxaWXVbUDGlgTgzaleLgMSuZble",
          wpcom_app_clientId: "64963",
          wpcom_user: "martinbeeby@hotmail.com",
          wpcom_pass: "demoPass$1@",
        },
        searchAndReplaceContentUrls: {
          sourceUrl: "https://source-url.com",
          replacementUrl: "https://replacement-url.com",
        },
        concurrentRequests: 10,
        includedRoutes: [
          "**/categories",
          "**/posts",
          "**/pages",
          "**/media",
          "**/tags",
          "**/taxonomies",
          "**/users",
        ],
        excludedRoutes: ["**/posts/1456"],
        normalizer: function({ entities }) {
          return entities
        },
      }
    },
```

Go to your Terminal and restart the Gatsby server bying pressing `ctrl + c`. Then retart by typing `gatsby develop` you should see in the start up logs that the site now connects to word press when it builds.

![Wordpress Console](/images/wordpress-1.png)

### Query Our WordPress Data

Now we have Wordpress files that have been loaded into the sites database. We can query these new files by going to Gatsbys GraphQL Console, which is often found at http://localhost:8000/___graphql

If you run the following query you should now see 3 files

```
{
  allWordpressPost {
    edges {
      node {
        id
        slug
        title
        content
        excerpt
        date
        modified
      }
    }
  }
}
```

![GraphQL](/images/graphql-1.png)

### Add a Template Page for Posts

We will now add a folder called Templates to the src folder and add a page called **post**. `src/templates/post.js`

This will be a template file for each post. Paste the following into `post.js`

```
import React, { Component } from "react"
import { graphql } from "gatsby"
import PropTypes from "prop-types"
import Layout from "../components/layout"

class PostTemplate extends Component {
  render() {
    const post = this.props.data.wordpressPost

    return (
      <Layout>
        <h1 dangerouslySetInnerHTML={{ __html: post.title }} />
        <div dangerouslySetInnerHTML={{ __html: post.content }} />
      </Layout>
    )
  }
}

PostTemplate.propTypes = {
  data: PropTypes.object.isRequired,
  edges: PropTypes.array,
}

export default PostTemplate

export const pageQuery = graphql`
query($id: String!) {
  wordpressPost(id: { eq: $id }) {
    title
    content
    date(formatString: "MMMM DD, YYYY")
  }
  site {
    id
    siteMetadata {
      title
    }
  }
}
`
```

### Add a Template page for Pages

We will now add a file called `page.js` into. `src/templates/page.js`

The contents of `page.js` should be:

```
import React, { Component } from "react"
import { graphql } from "gatsby"
import Layout from "../components/layout"

class PageTemplate extends Component {
  render() {
    const currentPage = this.props.data.wordpressPage

    return (
      <Layout>
        <h1 dangerouslySetInnerHTML={{ __html: currentPage.title }} />
        <div dangerouslySetInnerHTML={{ __html: currentPage.content }} />
      </Layout>
    )
  }
}

export default PageTemplate

export const pageQuery = graphql`
  query($id: String!) {
    wordpressPage(id: { eq: $id }) {
      title
      content
      date(formatString: "MMMM DD, YYYY")
    }
    site {
      id
      siteMetadata {
        title
      }
    }
  }
`
```

### Create Our Pages on Build

Go to the file gatsby-node.js in the route of the project and paste the following.

{{% notice warning %}}
Because we are replacing the createPages function then our previous markdown system will no longer produce our markdown files. If you require both markdown and wordpress to work at the same time you will need to combine the two createPages functions.
{{% /notice %}}

```
const path = require(`path`)
const slash = require(`slash`)

// Implement the Gatsby API “createPages”. This is
// called after the Gatsby bootstrap is finished so you have
// access to any information necessary to programmatically
// create pages.
// Will create pages for WordPress pages (route : /{slug})
// Will create pages for WordPress posts (route : /post/{slug})
exports.createPages = async ({ graphql, actions }) => {
  const { createPage } = actions

  // The “graphql” function allows us to run arbitrary
  // queries against the local Gatsby GraphQL schema. Think of
  // it like the site has a built-in database constructed
  // from the fetched data that you can run queries against.
  const result = await graphql(`
    {
      allWordpressPage {
        edges {
          node {
            id
            slug
            status
            template
            slug
          }
        }
      }
      allWordpressPost {
        edges {
          node {
            id
            slug
            status
            template
            format
            slug
          }
        }
      }
    }
  `)

  // Check for any errors
  if (result.errors) {
    throw new Error(result.errors)
  }

  // Access query results via object destructuring
  const { allWordpressPage, allWordpressPost } = result.data

  // Create Page pages.
  const pageTemplate = path.resolve(`./src/templates/page.js`)
  // We want to create a detailed page for each
  // page node. We'll just use the WordPress Slug for the slug.
  // The Page ID is prefixed with 'PAGE_'
  allWordpressPage.edges.forEach(edge => {
    // Gatsby uses Redux to manage its internal state.
    // Plugins and sites can use functions like "createPage"
    // to interact with Gatsby.
    createPage({
      // Each page is required to have a `path` as well
      // as a template component. The `context` is
      // optional but is often necessary so the template
      // can query data specific to each page.
      path: `/${edge.node.slug}/`,
      component: slash(pageTemplate),
      context: {
        id: edge.node.id,
      },
    })
  })

  const postTemplate = path.resolve(`./src/templates/post.js`)
  // We want to create a detailed page for each
  // post node. We'll just use the WordPress Slug for the slug.
  // The Post ID is prefixed with 'POST_'
  allWordpressPost.edges.forEach(edge => {
    createPage({
      path: `/${edge.node.slug}/`,
      component: slash(postTemplate),
      context: {
        id: edge.node.id,
      },
    })
  })
}
```

If we stop and start Gatsby (`ctrl+c` then `gatsby develop`). 

The pages have now been created. We can navigate to them on our local site: http://localhost:8000/the-journey-begins

### Listing posts.

Now to have our posts listed on the front page of the site we will need to edit the file `src/pages/index.js`

Paste the following code into that file:

```
import React from "react"
import { graphql } from "gatsby"
import Layout from "../components/layout"

export default ({ data }) => {
  return (
        <Layout>
        <div>
          <h1
          >
            Posts from WordPress:
          </h1>
          {data.allWordpressPost.edges.map(({ node }, index) => (
            <div key={node.id}>
              <h3>
              <a href={node.slug} dangerouslySetInnerHTML={{__html:node.title}}></a>
              </h3>
              <p dangerouslySetInnerHTML={{__html:node.excerpt}}></p>
            </div>
          ))}
        </div>
      </Layout>
  )
}

export const query = graphql`
  query {
        allWordpressPost {
          edges {
            node {
              id
              slug
              title
              excerpt
              date
            }
          }
        }
      }
`
```

File site should now look like this:

![GraphQL](/images/wordpress-2.png)


