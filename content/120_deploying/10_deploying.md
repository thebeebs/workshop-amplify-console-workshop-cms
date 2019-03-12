+++
title = "Deploying our app using Amplify Console"
chapter = false
weight = 10
+++

### Commit your Application to Source Control.
Create a Repository and commit your code to either GitHub, Bitbucket, GitLab, or AWS CodeCommit repositories. The Repository dosent need to be public since you will connect it in the next step.

### Go to the Amplify Console 
To get started, log in to the [Amplify Console](https://eu-west-1.console.aws.amazon.com/amplify/) and choose Get Started under Deploy.

![Deploy](/images/deploy-1.png)

### Connect Repository
Connect your GitHub, Bitbucket, GitLab, or AWS CodeCommit repositories. After you authorize the Amplify Console, Amplify fetches an access token from the repository provider, but it doesn't store the token on the AWS servers. Amplify accesses your repository using deploy keys installed in a specific repository only.

![Deploy](/images/deploy-2.png)

You will need to select the repository and the branch you want to build from.

![Deploy](/images/deploy-3.png)

### Confirm Build Settings for the Front End

For the selected branch, Amplify inspects your repository to automatically detect the sequence of build commands to be executed. 

![Deploy](/images/deploy-4.png)

### Add Environment Variables

Almost every app needs to get configuration information at runtime. These configurations can be database connection details, API keys, or different parameters. Environment variables provide a means to expose these configurations at build time.

For us these variables are going to be the WordPress connection information used earlier. They are currently hardcoded as an example but you will want to change this.

### Save and Deploy

Review all of your settings to ensure everything is set up correctly. Choose **Save and deploy** to deploy your web app to a global content delivery network (CDN). 
Your front end build typically takes 1 to 2 minutes but can vary based on size of the app. 

![Deploy](/images/deploy-4.png)

Access the build logs screen by selecting a progress indicator on the branch tile. A build has the following stages:

1. **Provision** - Your build environment is set up using a Docker image on a host with 4 vCPU, 7GB memory. Each build gets its own host instance, ensuring that all resources are securely isolated. The contents of the Docker file are displayed to ensure that the default image supports your requirements.

2. **Build** - The build phase consists of three stages: setup (clones repository into container), deploy backend (runs the Amplify CLI to deploy backend resources), and build front end (builds your front-end artifacts). 

3. **Deploy** - When the build is complete, all artifacts are deployed to a hosting environment managed by Amplify. Every deployment is atomic - atomic deployments eliminate maintenance windows by ensuring that the web app is only updated after the entire deployment has completed.

4. **Verify** - To verify that your app works correctly, Amplify renders screen shots of the index.html in multiple device resolutions using Headless Chrome.

![Deploy](/images/deploy-5.png)

![Deploy](/images/deploy-6.png)



