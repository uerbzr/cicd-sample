# CICD

## Steps to deploy a new site

1. Create repository in GitHub by visiting repo.new
2. Create code inside repository
3. Generate Netlify site and connect to your GitHub repo
4. Create a new Build Hook:
   ![Alt](/img/buildhooks.png "Title")

5. Add the Build Hook as a secret to GitHub:
   ![Alt](/img/addhooktogithub.png "Title")

6. On the repository page in Github go to Actions, click New Workflow then click set up a workflow yourself.

7. Add a workflow similar to this. Note this executes on push to main. Ensure you have named your NETLIFY_BUILD_HOOK the same as the one in the script.

```yml
name: Trigger Netlify Build
on:
  push:
    branches:
      - main
jobs:
  build:
    name: Request Netlify Webhook
    runs-on: ubuntu-latest
    steps:
      - name: Curl request
        run: curl -X POST -d {} ${{secrets.NETLIFY_BUILD_HOOK}}
```

8.  Now you can run push a change and you should see your code deployed to Netlify.

## Consider:

- Try another script. You will need to add some other secrets and get the info from your site in Netlify. Also when you run the ng build (if an Angular app) then you'll need to make sure where the site is being built locally and adjust the build_directory accordingly as that would match the target directory on the server:

```yml
name: "Deploy"

on:
  pull_request:
    types: closed

jobs:
  deploy:
    if: github.event.pull_request.merged == true
    runs-on: ubuntu-latest
    name: "Deploy to Netlify"
    steps:
      - uses: actions/checkout@v3
      - uses: jsmrcaga/action-netlify-deploy@v2.1.0
        with:
          NETLIFY_AUTH_TOKEN: ${{ secrets.MY_AUTH_TOKEN }}
          NETLIFY_SITE_ID: ${{ secrets.MY_SITE_ID }}
          NETLIFY_DEPLOY_TO_PROD: true
          build_directory: dist/hello-ci-cd/browser
```
