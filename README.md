[sretreats.github.io](sretreats.github.io)

A statically generated site by Hugo

## Project structure
This repo contains 2 branches
- `master` - is the branch where you will make all the changes, put new articles.
- `main` - contains the statically generated content only, so DON'T COMMIT TO THIS BRANCH DIRECTLY


## Deploying new changes.
There is a Github action setup in the repo that gets triggered when you merge
and push code to master. It generated the static site and updates the `main` 
branch automatically, which will publish new changes to the site.

### Local deployment
Use make file commands.