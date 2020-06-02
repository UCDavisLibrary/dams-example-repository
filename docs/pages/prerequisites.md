---
layout: page
title: Prerequisites
permalink: /prerequisites/
---

First, we'll need to clone this repository. This is a good way to get your
example data downloaded, and it is also a good starting point to fork this
repository, later when you want to save site specific preferences. So, we'll
need have have `git` on your development machine. If you're new to git, you my
follow the installation instructions in this [GIT Book](https://git-scm.com/),
or perhaps try github's [desktop](https://desktop.github.com/) application if
you are using windows. The examples below, will use `git`'s command line
features.

The server is implemented as suite of docker containers, so in order to run this
example, you need to have `docker` and `docker-compose` installed on our system.
Follow the [installation notes](https://docs.docker.com/compose/install/) from
docker, if you are new to docker.  **Note**, if you are new to docker,
understand that you will need to learn about this system.  Some of the example
configuration below, will be unclear, and it will be difficult to modify the
examples below for your setup unless  you understand this environment.

Finally, we will be running a nodejs based tool `fin-cli` to work with our
repository. After installing [nodejs](https://nodejs.org/en/download/), you can
install this tool with `npm install -g @ucd-lib/fin-cli`.
