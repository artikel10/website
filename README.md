# website

The [Hugo]-driven website for Artikel10 e.V.

## Editing

Install [Hugo] and clone the repository:

    git clone --recurse-submodules REPO_URL

To preview your edits locally in a browser:

    cd website
    hugo server

To update your local repository:

    git pull --recurse-submodules

## Deployment

To deploy the updated website, push your changes as a branch and create a PR
to `master`. Once your changes are reviewed and merged, the website will be
deployed automatically.

[Hugo]: https://gohugo.io/
