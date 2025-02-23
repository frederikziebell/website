---
title: rstudio, tidyverse, verse, geospatial
---

## Quick reference

- Source repository: [rocker-org/rocker-versioned2](https://github.com/rocker-org/rocker-versioned2)
- Dockerfile
  - [rocker/rstudio](https://github.com/rocker-org/rocker-versioned2/blob/master/dockerfiles/rstudio_devel.Dockerfile)
  - [rocker/tidyverse](https://github.com/rocker-org/rocker-versioned2/blob/master/dockerfiles/tidyverse_devel.Dockerfile)
  - [rocker/verse](https://github.com/rocker-org/rocker-versioned2/blob/master/dockerfiles/verse_devel.Dockerfile)
  - [rocker/geospatial](https://github.com/rocker-org/rocker-versioned2/blob/master/dockerfiles/geospatial_devel.Dockerfile)
- tags
  - rocker/rstudio
    - [DockerHub](https://hub.docker.com/r/rocker/rstudio/tags)
    - [GitHub Container Registry](https://github.com/rocker-org/rocker-versioned2/pkgs/container/rstudio/versions)
  - rocker/tidyverse
    - [DockerHub](https://hub.docker.com/r/rocker/tidyverse/tags)
    - [GitHub Container Registry](https://github.com/rocker-org/rocker-versioned2/pkgs/container/tidyverse/versions)
  - rocker/verse
    - [DockerHub](https://hub.docker.com/r/rocker/verse/tags)
    - [GitHub Container Registry](https://github.com/rocker-org/rocker-versioned2/pkgs/container/verse/versions)
  - rocker/geospatial
    - [DockerHub](https://hub.docker.com/r/rocker/geospatial/tags)
    - [GitHub Container Registry](https://github.com/rocker-org/rocker-versioned2/pkgs/container/geospatial/versions)
- Published image details: [rocker-org/rocker-versioned2's wiki](https://github.com/rocker-org/rocker-versioned2/wiki)
- Non-root default user: `rstudio`

## Overview

These images are based on [`rocker/r-ver`](r-ver.md),
and [RStudio Server](https://www.rstudio.com/products/rstudio/download-server/) is already installed.

The basic usage of these images is the same, with the difference being the amount of additional (R) packages installed.
(See [image details](https://github.com/rocker-org/rocker-versioned2/wiki) for lists of installation packages)

- `rocker/tidyverse` has already installed many R packages and their dependencies apt packages.
  e.g. [the `tidyverse` package](https://www.tidyverse.org/),
  [the `devtools` package](https://devtools.r-lib.org/), [the `rmarkdown` package](https://rmarkdown.rstudio.com/),
  some [R Database Interface](https://dbi.r-dbi.org/) packages,
  [the `data.table` package](https://rdatatable.gitlab.io/data.table/), [the `fst` package](https://www.fstpackage.org/),
  and [the Apache Arrow R package](https://arrow.apache.org/docs/r/).
- `rocker/verse` has already installed TeX Live and some publishing-related R packages,
  in addition to the packages installed in `rocker/tidyverse`.
- `rocker/geospatial` has already installed some geospatial R packages in addition to the packages installed in `rocker/verse`.

These images start RStudio Server with the default command.
Since the RStudio Server port is set to `8787`,
you can open the RStudio screen on `localhost:8787` from your browser with the following command.

```sh
docker run --rm -ti -p 8787:8787 rocker/rstudio
```

The non-root default user `rstudio` is set up as RStudio Server user,
so please enter the username `rstudio` and a randomly generated password
which is displayed in the console to the RStudio login form.

RStudio will not start if the default command (`/init`) is overridden.
To use R on the command line, specify the `R` command as follows.

```sh
docker run --rm -ti rocker/tidyverse R
```

:::{.callout-note}

This document is for R 4.0.0 >= images. For R <= 3.6 images,
please check the [rocker-org/rocker-versioned](https://github.com/rocker-org/rocker-versioned) repository
and the [rocker-org/geospatial](https://github.com/rocker-org/geospatial) repository.

:::

## Spatial tags

### `devel`

The `devel` images are based on `rocker/r-ver:devel`,
witch install [the latest R-devel daily snapshot](https://cloud.r-project.org/src/base-prerelease/).

`devel` tags are available for `rocker/rstudio`, `rocker/tidyverse`, and `rocker/verse`.

### `latest-daily`

The `latest-daily` images are based on `rocker/r-ver:latest` and install [the latest RStudio daily build](https://dailies.rstudio.com/).

`latest-daily` tags are available for `rocker/rstudio`, `rocker/tidyverse`, and `rocker/verse`.

### Spacial tags for geospatial toolkit

`rocker/geospatial:ubuntugis` (`rocker/geospatial:X.Y.Z-ubuntugis`) and `rocker/geospatial:dev-osgeo` are special images
that differ slightly from the regular `rocker/geospatial`.

- `ubuntugis` is built on packages installed from [the ubuntugis-unstable PPA](https://launchpad.net/~ubuntugis/+archive/ubuntu/ubuntugis-unstable).
- `dev-osgeo` is built on the latest release of [PROJ](https://proj.org/), [GDAL](https://gdal.org/), and [GEOS](https://libgeos.org/).

## How to use

### Environment variables

Several special environment variables can be set to modify RStudio Server's behavior.

:::{.callout-note}

The process of referencing these environment variables is done by the `/init` command,
which is the default command set for the container.
Therefore, if the `/init` command is not executed, nothing will happen.

For example, if you enter the container with the following command,
the uid of the user `rstudio` is unchanged and remains 1000.

```sh
docker run --rm -ti -e USERID=1001 -e GROUPID=1001 --user rstudio rocker/tidyverse bash
```

:::

#### `PASSWORD`

You can set a custom passoword to log in the RStudio instance.
Please set your password as an environmental variable `PASSWORD` like this:

```sh
docker run --rm -ti -e PASSWORD=yourpassword -p 8787:8787 rocker/rstudio
```

#### `ROOT`

If `ROOT` is set to `true`,
the default non-root user will be added to the `sudoers` group when the server init process.

```sh
docker run --rm -ti -e ROOT=true -p 8787:8787 rocker/rstudio
```

This configuration allows you to execute `sudo` commands, like `sudo apt update`, on the terminal on RStudio.

:::{.callout-note}

When using the `sudo` command,
you must enter the same password you used to log into RStudio.

:::

#### `DISABLE_AUTH`

You can disable authentication for RStudio Server
by setting an environmental variable `DISABLE_AUTH=true`.

```sh
docker run --rm -ti -e DISABLE_AUTH=true -p 127.0.0.1:8787:8787 rocker/rstudio
```

With this example, when you visit `localhost:8787`,
you will now automatically be logged in as the user `rstudio` without having to first enter a user name and password.

:::{.callout-warning}

Use this setting only in a secure environment.
Without authentication, anyone who has access to that port can log in the RStudio Server.

If you are using a container on your local computer,
it is recommended that you configure the port publishing as `-p 127.0.0.1:8787:8787`, as in the example,
so that it can only be accessed from the same computer.

:::

:::{.callout-note}

`DISABLE_AUTH=true` setting only skips the RStudio log in page.
So you will still need to enter the password when use the   `sudo` command with `ROOT=true` option.

:::

#### `USERID` and `GROUPID`

The UID and GID of the default non-root user can be changed as follows:

```sh
docker run --rm -ti -e USERID=1001 -e GROUPID=1001 -p 8787:8787 rocker/rstudio
```

:::{.callout-warning}

If these are set, ownership of the `/home/rstudio` directory in the container is updated by the root user.
This will also overwrite the ownership of any files that are bind-mounted under the `/home/rstudio` directory.

:::

### Editing work on RStudio Server

If you want to make repeated edits on RStudio Server,
It would be useful to be able to share files edited on the container with the Docker host.

Here are some hints for doing this and a sample compose file (for [docker compose](https://docs.docker.com/compose/)).

1. Recent RStudio Server's configuration files are saved in the `~/.config/rstudio/` directory[^rstudio_customizing].
2. It is not recommended to bind-mount whole home directory on the container (`/home/rstudio`);
   RStudio Server may not work properly.
3. Since RStudio Server opens the user's home directory (`/home/rstudio`) by default,
   it is easier to use if a working directory is set up under `/home/rstudio`, e.g. `/home/rstudio/workspace`.
   However, for example, another directory such as the one containing CSV files
   should not necessarily have to be under the home directory,
   so it is recommended to bind-mount it under its own name directly under the root, e.g. `/other_dir`.

[^rstudio_customizing]: [RStudio Workbench Administration Guide](https://docs.rstudio.com/ide/server-pro/r_sessions/customizing_session_settings.html)

```{.yml filename="compose.yml"}
services:
  rstudio:
    image: rocker/verse:4
    ports:
      - "8787:8787"
    environment:
      PASSWORD: yourpassword
    volumes:
      - ./.rstudio_config:/home/rstudio/.config/rstudio
      - ~/workspace:/home/rstudio/workspace
      - /other_dir:/other_dir
```

### See also

- [Networking](../../use/networking.md)
- [Shared Volumes](../../use/shared_volumes.md)
