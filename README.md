# git_travis_latex

Automatic latex complie and release following: https://github.com/PHPirates/travis-ci-latex-pdf

## 1. Docker image with Tectonic

Thanks to [Norbert Pozar (@rekka)](https://tectonic.newton.cx/t/small-docker-image-for-tectonic/133?u=phpirates) for providing the original Docker image.
[Manuel (@WtfJoke)](https://github.com/WtfJoke) extended it by integrating biber.
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


## <a name="tectonic-docker">Instructions for building with Docker and Tectonic</a>

* Install the Travis GitHub App by going to the [Marketplace](https://github.com/marketplace/travis-ci), scroll down, select Open Source (also when you want to use private repos) and select 'Install it for free', then 'Complete order and begin installation'. 
* Now you should be in Personal settings | Applications | Travis CI | Configure and you can allow access to repositories, either select repos or all repos.
* Copy [`.travis.yml`](1-tectonic-docker/.travis.yml) and specify the right tex file in the line with `docker run`. If your tex file is in the `src/` folder, you also need to change the path in that line into `$TRAVIS_BUILD_DIR/src`.
* If you want to compile multiple files, you can replace `tectonic main.tex` by `tectonic main.tex; tectonic main2.tex`.
* If you want to use biber, you can use `tectonic --keep-intermediates --reruns 0 main.tex; biber main; tectonic main.tex`
* Commit and push, you can view your repositories at [travis-ci.com](https://travis-ci.com/).
* For deploying to GitHub releases, see the notes [below](#deploy).
* Have a look at the [Tips](#tips).
* If your build doesn't start, see [Troubleshooting](#troubleshooting).

## <a name="deploy">To automatically deploy pdfs to GitHub release</a>
### First time setup

We will add a configuration to the `.travis.yml` such that a pdf will be automatically uploaded to GitHub releases when you tag a commit, also see the [Travis documentation](https://docs.travis-ci.com/user/deployment/releases/).

* We will generate a GitHub OAuth key so Travis can push to your releases, with the important difference (compared to just gettting it via GitHub settings) that it's encryped so you can push it safely.
* (Mac) Run `brew install travis` to install the Travis Command-line Tool.
### For every new project
* Go to the directory of your repository, open the command prompt (Windows: <kbd>SHIFT</kbd>+<kbd>F10</kbd> <kbd>W</kbd> <kbd>ENTER</kbd>) and run `travis login --pro --github-token mytoken` where `mytoken` is a token from https://github.com/settings/tokens (or leave the `--github-token` flag out if you want to use your password). 
* Run `travis setup releases --pro` and leave File to Upload empty.
* Replace everything below your encryped api key with (changing the path to your pdf file, probably the same folder as your tex file is in)
```yml
  file: 
  - ./nameofmytexfile.pdf
  skip_cleanup: true
  on:
    tags: true
    branch: master
```
* Commit and push.
* If you are ready to release, just tag and push.
