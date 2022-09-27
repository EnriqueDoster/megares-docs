---
search:
  exclude: true
---

# Docker Commands Reference Guide

!!! warning
    This documentation is for AMR++ version 1.1. Click [here](./../latest/introduction.md) for the latest docs.

## Overview
Below, we will cover some of the basic Docker commands that you will find useful when interacting with Docker containers. Many of these commands can be found on the Docker commands homepage and from the Galaxy Docker Github repository. They are simply reviewed here for convenience.

## Container Commands

??? example "Runs the chrisd/amrplusplus container in the background with no FTP server"
    ```
    $ docker run -d -p 8080:80 chrisd/amrplusplus
    ```

??? example "Runs the chrisd/amrplusplus container in the background with an FTP server"
    ```
    $ docker run -d -p 8080:80 -p 8021:21 chrisd/amrplusplus
    ```

??? example "Provides information about currently running containers"
    ```
    $ docker ps    
    CONTAINER ID        IMAGE                COMMAND              CREATED             STATUS              PORTS                                                            NAMES
    6296a2688b71        chrisd/amrplusplus   "/usr/bin/startup"   10 hours ago        Up 10 hours         8800/tcp, 9002/tcp, 0.0.0.0:8021->21/tcp, 0.0.0.0:8080->80/tcp   elated_cray
    ```

??? example "Stops the currently running container specified by the CONTAINER ID"
    ```
    $ docker stop 6296a2688b71
    $ docker ps
    CONTAINER ID        IMAGE                COMMAND              CREATED             STATUS              PORTS             NAMES
    ```

??? example "Provides information about currently saved images"
    ```
    $ docker images

    REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
    chrisd/amrplusplus           latest              976681113852        3 days ago          1.623 GB
    ```

??? example "Interact with a running container"
    You will be launched into a Bash shell and allowed to explore the container.

    ```
    $ docker run -i -t -p 8080:80 chrisd/amrplusplus /bin/bash
    root@d49e4fa1bd22:/galaxy-central#
    ```

??? example "Give a running Galaxy container persistent storage"
    By DEFAULT, Galaxy containers are volatile, meaning that once they are stopped, all data and uploaded files will be removed. This command can be used to give a running Galaxy container persistent storage
    ```
    $ docker run -d -p 8080:80 -v /home/user/galaxy_storage/:/export/ chrisd/amrplusplus
    ```