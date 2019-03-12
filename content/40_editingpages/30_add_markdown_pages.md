+++
title = "Add a page via Markdown"
chapter = false
weight = 30
+++

Gatsby supports transformer plugins which take raw
content from source plugins and _transform_ it into something more usable.

For example, markdown files. Markdown is nice to write in but when you build a
page with it, you need the markdown to be HTML.

Add a markdown file to your site at
`src/pages/sweet-pandas-eating-sweets.md` (This will become your first markdown
blog post) and learn how to _transform_ it to HTML using transformer plugins and
GraphQL.

```markdown:title=src/pages/sweet-pandas-eating-sweets.md
---
title: "Sweet Pandas Eating Sweets"
date: "2017-08-10"
path: "pandas-eating-sweets"
---

Pandas are really sweet.

Here's a video of a panda eating sweets.

<iframe width="560" height="315" src="https://www.youtube.com/embed/4n0xNbfJLR8" frameborder="0" allowfullscreen></iframe>
```

First we need to add a plugin that will read all the files on disk.

```shell
npm install --save gatsby-source-filesystem
```

Add a transformer plugin that can transform markdown files:

```shell
npm install --save gatsby-transformer-remark
```

Then add it to the `gatsby-config.js` the plugin `gatsby-transformer-remark`:
{{% notice tip %}}
Your file will have different plugins already in this file. Do not delete them but add this new one and make sure you get your commas in the right place.
{{% /notice %}}

```
plugins: [
  {
    resolve: `gatsby-source-filesystem`,
    options: {
      name: `src`,
        path: `${__dirname}/src/`,
    },
  },
  `gatsby-transformer-remark`,
]
```

Restart the development server then refresh (or open again) http://localhost:8000/___graphql and look
at the autocomplete:

![markdown-autocomplete](/images/markdown-autocomplete.png)

Select `allMarkdownRemark` again and run it. You'll
see there the markdown file you recently added. Explore the fields that are
available on the `MarkdownRemark` node.

![markdown-query](/images/markdown-query.png)

### Create a list of your site's markdown files 

Now you'll have to create a list of your markdown files on the front page. Like many
blogs, you want to end up with a list of links on the front page pointing to each
blog post. With GraphQL you can _query_ for the current list of markdown blog
posts so you won't need to maintain the list manually.

Replace `src/pages/index.js` with
the following to add a GraphQL query with some initial HTML and styling.

```jsx:title=src/pages/index.js
import React from "react"
import { graphql } from "gatsby"
import Layout from "../components/layout"

export default ({ data }) => {
  console.log(data)
  return (
    <Layout>
      <div>
        <h1
        >
          Amazing Pandas Eating Things
        </h1>
        <h4>{data.allMarkdownRemark.totalCount} Posts</h4>
        {data.allMarkdownRemark.edges.map(({ node }) => (
          <div key={node.id}>
            <h3>
              {node.frontmatter.title}{" "}
              <span>
                — {node.frontmatter.date}
              </span>
            </h3>
            <p>{node.excerpt}</p>
          </div>
        ))}
      </div>
    </Layout>
  )
}

export const query = graphql`
  query {
    allMarkdownRemark {
      totalCount
      edges {
        node {
          id
          frontmatter {
            title
            date(formatString: "DD MMMM, YYYY")
          }
          excerpt
        }
      }
    }
  }
`
```

Now the frontpage should look like:

![frontpage](/images/frontpage.png)

But your one blog post looks a bit lonely. So let's add another one at
`src/pages/pandas-and-bananas.md`

```markdown:title=src/pages/pandas-and-bananas.md
---
title: "Pandas and Bananas"
date: "2017-08-21"
path: "pandas-and-bananas"
---

Do Pandas eat bananas? Check out this short video that shows that yes! pandas do
seem to really enjoy bananas!

<iframe width="560" height="315" src="https://www.youtube.com/embed/4SZl1r2O_bY" frameborder="0" allowfullscreen></iframe>
```

![two-posts](/images/two-posts.png)

Which looks great! Except… the order of the posts is wrong.

But this is easy to fix. When querying a connection of some type, you can pass a
variety of arguments to the GraphQL query. You can `sort` and `filter` nodes, set how
many nodes to `skip`, and choose the `limit` of how many nodes to retrieve. With
this powerful set of operators, you can select any data you want—in the format you
need.

In your index page's GraphQL query, change `allMarkdownRemark` to
`allMarkdownRemark(sort: { fields: [frontmatter___date], order: DESC })`. _Note: There are 3 underscores between `frontmatter` and `date`._ Save
this and the sort order should be fixed.

#### Note on creating markdown files.

When you create a Markdown file, at the top of the file, add the block below. You can have different key value pairs that are relevant to your website. This block will be parsed by `gatsby-transformer-remark` as `frontmatter`. The GraphQL API will provide this data in our React components.

```
---
title: "Pandas and Bananas"
date: "2017-08-21"
path: "pandas-and-bananas"
---
```

### Create a page template for the markdown data.

Create a folder in the `/src` directory of your Gatsby application called `templates`.
Now create a `blogTemplate.js` inside it with the following content.

```jsx:title=src/templates/blogTemplate.js
import React, { Component } from "react"
import { graphql } from "gatsby"
import Layout from "../components/layout"

class BlogTemplate extends Component {
    render() {
      const currentPage = this.props.data.markdownRemark
      return (
        <Layout>
            <h1 dangerouslySetInnerHTML={{ __html: currentPage.frontmatter.title }} />
        <div dangerouslySetInnerHTML={{ __html: currentPage.html }} />
        </Layout>
      )
    }
  }
  
export default BlogTemplate

export const pageQuery = graphql`
  query($id: String!) {
    markdownRemark(frontmatter: { path: { eq: $id } }) {
      html
      frontmatter {
        date(formatString: "MMMM DD, YYYY")
        title
        path
      }
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

Two things are important in the file above.

1.  A GraphQL query is made in the second half of the file to get the Markdown data. Gatsby has automagically given you all the Markdown metadata and HTML in this query's result.

2.  The result of the query is injected by Gatsby into the `Template` component as `data`. `markdownRemark` is the property that we find has all the details of the Markdown file. We can use that to construct a template for our blogpost view. Since it's a React component, you could style it with any of the recommended styling systems in Gatsby.

{{% notice tip %}}
To learn more about GraphQL, consider this [excellent resource](https://www.howtographql.com/)
{{% /notice %}}

### Create static pages using Gatsby's Node API.

Gatsby exposes a powerful Node.js API, which allows for functionality such as creating dynamic pages. This API is available in the `gatsby-node.js` file in the root directory of your project, at the same level as `gatsby-config.js`. Each export found in this file will be run by Gatsby, as detailed in its [Node API specification](/docs/node-apis/). However, we only care about one particular API in this instance, `createPages`.

Gatsby calls the `createPages` API (if present) at build time with injected parameters, `actions` and `graphql`. Use the `graphql` to query Markdown file data as below. Next use `createPage` action creator to create a page for each of the Markdown files using the `blogTemplate.js` we created in the previous step.

```javascript:title=gatsby-node.js
const path = require("path")

exports.createPages = ({ actions, graphql }) => {
  const { createPage } = actions

  const blogPostTemplate = path.resolve(`src/templates/blogTemplate.js`)

  return graphql(`
    {
      allMarkdownRemark(
        sort: { order: DESC, fields: [frontmatter___date] }
        limit: 1000
      ) {
        edges {
          node {
            frontmatter {
              path
            }
          }
        }
      }
    }
  `).then(result => {
    if (result.errors) {
      return Promise.reject(result.errors)
    }

    result.data.allMarkdownRemark.edges.forEach(({ node }) => {
      
      createPage({
        path: node.frontmatter.path,
        component: slash(blogPostTemplate),
        context: {
          id: node.frontmatter.path,
        },
      })
    })
  })
}
```

This should get you started on some basic markdown power in your Gatsby site. You can further customise the `frontmatter` and the template file to get desired effects!
