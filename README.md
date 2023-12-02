# GitHub Pages build and deployment action

This is a reusable GitHub Actions workflow that we use to build and deploy Node.js websites to GitHub Pages.

## Why?

When creating separate repositories for our different websites, we noticed that we were copy-pasting our GitHub Actions workflows into each of them. This meant that whenever we needed to change something in the workflow, we needed to go through all of the repositories and change it in each one. [Reusable workflows](https://docs.github.com/en/actions/using-workflows/reusing-workflows) are a much better way to do this because it allows one workflow to be reused and configured for multiple different repositories.

## How to use

1. Ensure that your website successfully builds on `npm run build`.
2. Enable GitHub Pages in your repository and set the source to GitHub Actions. This will be for "real" public deployments.
3. Create a new project in Cloudflare Pages for the new repository. This will be for test deployments to preview changes before making them public.
    1. Go to the **Workers & Pages** section in the Cloudflare dashboard
    2. Create a new Pages application using the **direct upload** option, not the connect to git option.
    3. Name the project `xbhs-robotics-[repository-name]`, replacing `[repository-name]` with the actual name of the repository, and create it. You do not need to actually upload any assets yet, so close the page.
4. Create two Actions secrets in the new repo.
    1. `CLOUDFLARE_ACCOUNT_ID` is the account ID that owns the Cloudflare Pages project.
    2. `CLOUDFLARE_API_TOKEN` is a Cloudflare API token with Cloudflare Pages edit permissions.
    3. If you are using Dependabot, copy these secrets over to the Dependabot secrets as well.
5. Create a new GitHub Actions workflow using the following template:

```yaml
name: Deploy static content to Pages

on:
  push:
  workflow_dispatch:
  pull_request:

jobs:
  pages:
    uses: XaverianTeamRobotics/github-pages-deployment-action/.github/workflows/pages.yml@main
    with:
      build-output-directory:
      website-base-path:
      cloudflare-pages-project-name:
    secrets: inherit
```

6. Configure the inputs.
    1. `build-output-directory`: Where are the build files when you run `npm run build`? By default, this is `build`.
    2. `website-base-path`: Where does the website expect to be when deployed? If you use a custom domain, remove this. If you are using github.io, this is your repository name.
    3. `cloudflare-pages-project-name`: What project name did you create in step 3?

For more information, see [the action source code](https://github.com/XaverianTeamRobotics/github-pages-deployment-action/blob/main/.github/workflows/pages.yml)
