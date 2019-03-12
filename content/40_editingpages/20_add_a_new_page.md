+++
title = "Add a New Page"
chapter = false
weight = 20
+++

There are a few ways to create pages in Gatsby. 

1. Firstly, Gatsby core automatically turns React components in `src/pages` into pages. We will look at that in this section.

2. Secondly, you can use the createPages API in the file `gatsby-node.js` we will do this later.

Let's add a third page. Go to `src/pages/` and make a new page called `page-3.js` You can either copy page two and make changes to it, or add the following:

```
import React from "react"
import { Link } from "gatsby"

import Layout from "../components/layout"
import SEO from "../components/seo"

const ThirdPage = () => (
  <Layout>
    <SEO title="Page Three" />
    <h1>Hi from the third page</h1>
    <p>Welcome to page 3</p>
    <Link to="/">Go back to the homepage</Link>
  </Layout>
)

export default ThirdPage
```

{{% notice tip %}}
Make sure that the last line exports the class **ThirdPage** (or what ever name you gave the const on line 7). If you accidentally export **SecondPage** you will not get an error, but instead **page-3** will have the contents of **page-2**
{{% /notice %}}

When you save the file, you should now be able to navigate to http://localhost:8000/page-3/ and see the new file. 

![Page 3](/images/add-page-1.png)

