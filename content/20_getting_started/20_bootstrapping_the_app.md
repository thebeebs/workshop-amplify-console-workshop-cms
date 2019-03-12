+++
title = "Bootstrapping the App"
chapter = false
weight = 20
+++

### Creating a Gatsby site
We'll get things started by building a new Gatsby app using the **gatsby** CLI tool. 

### In the terminal (locally or in Cloud 9), run

 ```
 npm install -g gatsby-cli
 ```

 {{% notice tip %}}
You can learn more about gatsby cli at [https://www.gatsbyjs.org/docs/recipes/](https://www.gatsbyjs.org/docs/recipes/).
{{% /notice %}}


### Create a new gatsby site 

```
gatsby new gatsby-site
``` 
Then navigate to the newly created directory `cd gatsby-site` you could call your site whatever you like, simply replace the term gatsby-site.

### Start development server.
```
gatsby develop
```
Gatsby will start a hot-reloading development environment accessible by default at http://localhost:8000.

### Create a production build.
```
gatsby build
```
Gatsby will perform an optimized production build for your site, generating static HTML and per-route JavaScript code bundles.

### Serve the production build locally.
```
gatsby serve
```

{{% notice note %}}
At this point, in your browser you should have a very basic site with one page that links through to a second page.
{{% /notice %}}