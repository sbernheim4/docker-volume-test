# About

This is an example of having a shared folder across multiple docker services when using docker compose. All the magic happens in the `docker-compose.yml` file

## How it Works

Below is the code for the `docker-compose.yml` file.
```
version: '2'
services:
  app:
    build: ./app
    volumes:
      - data:/usr/workdir/common
      - ./app:/usr/workdir
    container_name: app-container
  test:
    build: ./test
    volumes:
      - data:/usr/workdir/common
      - ./test:/usr/workdir
    container_name: test-container
    links:
      - app
  build:
    build: ./build
    volumes:
      - data:/usr/workdir/common
      - ./build:/usr/workdir
    container_name: build-container
volumes:
  data:
```

The last two lines
```
volumes:
  data:
```
create a named volume called data. For each service that needs access to shared files, add a mapping in that service's volumes declaration like `data:/usr/workdir/common`. `data` refers to the named volume defined in those last two lines and `/usr/workdir/common` specifies that the shared volume (really just a folder) should be stored in `/usr/workdir/common` in the docker container for this service. This will create a folder in `/usr/workdir` called `common`. Anything in this folder is accessible by any service that has this volume mapping.

## Installation

`git clone git@github.mlbam.net:sbernheim/docker-volume-test.git`

`cd docker-volume-test`

## Usage

`./launch.sh`

Note: Make sure the launch script has exectuable permissions. If not run `chmod +x launch.sh` and then try running the file again

The 3 containers will sleep for 30 minutes to give you time to explore each container and play around in them. To enter a bash session inside the containers use either the exec button in kitematic or run

`bash -c "clear && docker exec -it CONTAINER_NAME sh`

replacing `CONTAINER_NAME` with the name of the container (`app-container`, `build-container`, or `test-container` for the basic setup of this repo). These names are specified in `docker-compose.yml` as the `container_name` property
