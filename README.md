# git travis latex

Automatic latex complie and release by following the option Docker image with Tectonic: https://github.com/PHPirates/travis-ci-latex-pdf

## Docker image with Tectonic

Docker provides the ability to download a pre-installed Tectonic and then run it on your LaTeX files.

#### Pro:
* Tectonic is ready really fast, because downloading the image is all it takes
* The Docker image is really small, around 75MB
* Works with bibtex automatically
* Can automatically deploy pdfs
* Works with biber

#### Con:
* Slower than the other methods because all packages have to be reinstalled every time

Build time example file: two minutes


## Instructions for building with Docker and Tectonic

### First time setup
* Install the Travis GitHub App by going to the [Marketplace](https://github.com/marketplace/travis-ci), scroll down, select Open Source (also when you want to use private repos) and select 'Install it for free', then 'Complete order and begin installation'. 
* Now you should be in Personal settings | Applications | Travis CI | Configure and you can allow access to repositories, either select repos or all repos.

### For every new project

* Create `.travis.yml` with the following contents:

```yml
  sudo: required
  language: generic
  services: docker
  script:
  - docker run --mount src=$TRAVIS_BUILD_DIR,target=/usr/src/tex,type=bind dxjoke/tectonic-docker
    /bin/sh -c "tectonic main.tex"
```

* In the above example, the name of latex file to be compiled is `main.tex`, which can be changed if needed.
* If your tex file is in `doc/` filter, the path should be chaged into `$TRAVIS_BUILD_DIR/doc`
* Commit and push, you can view your repositories at [travis-ci.com](https://travis-ci.com/).

## To automatically deploy pdfs to GitHub release</a>
### First time setup


* (Mac) Run `brew install travis` to install the Travis Command-line Tool.

### For every new project
* Run `travis login --pro --github-token mytoken` where `mytoken` is a token from https://github.com/settings/tokens (or leave the `--github-token` flag out if you want to use your password). 
* Run `travis setup releases --pro` and leave File to Upload empty.
* The above step updates `.travis.yml`. Replace everything below your encryped api key with

```yml
  file: 
  - "./main.pdf"
  skip_cleanup: true
  on:
    repo: fdcl-gwu/***
    tags: true
    branch: master
```
* The name and the location of `main.pdf` above can be updated. 


### How to use and deploy
* Commit and push as usual
* If you are ready to release a particular commit with the generated `pdf`, checkout, tag, and push the tag. For example,

```
   git tag v0.01
   git push origin v0.01
```
* The `main.pdf` should be avaialble at the release section of your repository at Github.