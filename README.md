# website

Repository for my personal [website](http://www.akashastrub.com/) & blog posts.

# Documentation

This website was created using [quarto](https://quarto.org/). For a brief introduction on how to do this, see this [website](https://quarto.org/docs/websites/website-blog.html).

## Directory & files

### Content

*Please note that the `posts/` folder in this repo contains its own `README.md` file with additional context.*

-   The `index.qmd` file in the home directory defines the landing page for the blog. In this case, that is the "Blog" page.

-   The `about.qmd` file in the home directory defines the "About" page of the blog.

-   The `posts/` folder contains the `theme.css` file that is used for blog-post's theming. Additional themes can be instantiated in the `styles.css` file.

-   Images and/or files required for the website can be stored in the `images/` folder in the home directory.

### Continuous Integration (CI)

The `_publish.yml` file contains the desired destination used when publishing the quarto project, which is used in the "publishing" GitHub Action. Google Domains and GitHub are then used to re-direct this from `quarto-pub` to `akashastrub.com`.

The `_quarto.yml` file contains high-level YAML information used when `quarto` functions are invoked, be it through the CLI, GitHub Action, or from an IDE. For example, this file logs that this project is a website, the website's title, favicon, links, etc. Importantly, this file contains the `execute: freeze: auto` option. In order to understand this option, it is worth a digression on CI:

#### High-level quarto CI Options

Before you start using a CI server you'll need to think about where you want executable code (e.g. R, Python, or Julia code) to run and where you want quarto render to run. You might reflexively assume that you'll always want to run everything on the CI server, however doing so introduces a number of complexities:

1.  You need to make sure that the appropriate version of Quarto is available in the CI environment.

2.  You need to reconstitute all of the dependencies (required R, Python, or Julia packages) in the CI environment.

3.  If your code needed any special permissions (e.g. database or network access) those permissions need also be present on the CI server.

4.  Your project may contain documents that can no longer be easily executed (e.g. blog posts from several years ago that use older versions of packages).

In light of the above, you can think about rendering as a continuum that extends from running everything (including quarto render) locally all the way up to running everything remotely on CI:

A. Local Execution and Rendering --- Run everything in your local environment and then check output (e.g. the \_site directory) into version control. In this scenario the CI server is merely making sure that the checked in content is copied/deployed to the right place every time you commit. You might choose this approach to place minimal requirements on software that needs to be present on the CI server.

B. Local Execution with CI Rendering --- Execute R, Python, or Julia code locally and use Quarto's ability to freeze computational output to save the results of computations into the \_freeze directory. Render the site on the CI server (which will use the computations stored in \_freeze). Use this approach when its difficult to arrange fully re-executing code on the CI server.

C. CI Execution and Rendering --- Execute all code and perform rendering on the CI server. While this is the gold standard of automation and reproducibility, it will require you to capture your R, Python, or Julia dependencies (e.g. in an renv.lock file or requirements.txt file) and arrange for them to be installed on the CI server. You will also need to make sure that permissions (e.g. database access) required by your code are available on the CI server.

#### Project CI Settings

This project uses option B. This option was selected as it leaves more flexibility on the user side to enforce specific posts have specific environments to be used when rendered.

The `execute: freeze: auto` option in the `_quarto.yml` file ensures the `_freeze/` directory is created when code is executed locally. Because of this, **it is paramount the user renders all files at least once on their development machine, prior to pushing to `main` branch and invoking the GitHub Action.** Doing so will ensure all required computations are in the `_freeze/` folder, and thereby a successful deployment of the website.

-   The `.github/workflows/publish.yaml` file invokes the required GitHub Action when pushing code to the `main` branch.

-   The `_site` folder is automatically created when rendering the blog locally, but is contained in the gitignore folder as it is redundant for the GitHub Action pipeline.

## Publishing the blog

The blog is published using GitHub Pages. Simply pushing a commit to main will invoke the CI pipeline, which will use the `gh-pages` branch to publish (**this branch should not be modified manually**). As a second step, the default URL (`akashastrub-github.io`) is then routed to the desired URL (`www.akashastrub.com`).

### Publishing to GitHub Pages

[This](https://quarto.org/docs/publishing/github-pages.html#publish-command) is the best resource to revisit the process by which the blog is being published via GitHub Actions/Pages. Visit `Settings` \> `Pages` to take a look under the hood and verify the status of your website.

### Routing to Custom domain

The `www.akashastrub.com` domain name was licensed using [Google Domains](https://domains.google.com/). Mapping the website from the default URL to the custom URL entails two steps:

1.  Configure your subdomain on [GitHub](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site#configuring-a-subdomain).

2.  Creating a `CNAME` record that points your custom domain to the default domain for your site on your DNS provider. Click `Manage domains` on your [Google Domains](https://domains.google/) \> `Manage` (under your specific domain) \> `DNS` \> `Custom records`. See link from Step 1 for details.
