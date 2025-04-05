# Docker Practice
 
We will work on two parts: running an R Shiny app in a Docker container and creating a customized bioinformatics Docker image.

### Running an R Shiny App in Docker

We'll be spinning up an existing R Shiny app called JoesFlow to start with. To start:

* Take a look at the [idssniaid/JoesFlow](https://hub.docker.com/repository/docker/idssniaid/joes-flow) Docker image.
* Review the [niaid/JoesFlow](https://github.com/niaid/JoesFlow) repository on GitHub.

As part of this week's homework, you should have pulled the docker image to your Google Cloud Shell, and it should be ready to test out. I'll include the pull command here. Technically, it only needs to be run once, but it won't hurt to run it again. If you do, Docker will see that you already have the latest version and skip the redundant download. Start up and run the following commands:

* `docker pull idssniaid/joes-flow`
* `docker run --user shiny -p 3838:3838 idssniaid/joes-flow`

You should get a message from the Shiny server that says something like

```
[2025-04-04T01:33:04.274] [INFO] shiny-server - Shiny Server v1.5.21.1004 (Node.js v16.18.1)
[2025-04-04T01:33:04.277] [INFO] shiny-server - Using config file "/etc/shiny-server/shiny-server.conf"
[2025-04-04T01:33:04.366] [INFO] shiny-server - Starting listener on http://[::]:3838
```

To open the app in your browser:

* Look on the upper left side of your shell window, there is a "Web Preview" button.
* Click the "Web Preview" and select "Change port".
* Change the port to `3838` and click "Change and Preview".
* Modify the url by adding "JoesFlow" to the end, so that it looks something like: "https://3838-cs-some-numbers-default.cs-us-east1-dogs.cloudshell.dev/JoesFlow"
* Wait for a few seconds for the app to start up and try it out.
* When you are finished, you can exit the Docker container by typing `ctrl-c` in your Cloud Shell window.


### Customizing a Bioinformatics Docker Image

Start by forking today's starter code from ... and open up the `docker-practice.Rproj` file.

#### R installation plus Bioconductor

Our first docker container will be an R installation with a few Bioconductor packages installed. We could install everything from scratch, but it is best practice not to reinvent the wheel if we don't need to. Do the following:

* Create a new folder called `Bioc`
* Create a new text file at `Bioc/Dockerfile`
* Add the following commands to your Dockerfile:

```         
FROM rocker/rstudio:latest

# Update package lists and install dependencies
RUN apt-get update && \
    apt-get install -y zlib1g-dev && \
    # Clean up apt cache to reduce image size
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

RUN R -e "install.packages('BiocManager'); BiocManager::install(c('DESeq2', 'Biostrings'))"

CMD ["R"]
```

* Commit and push your changes to GitHub.
* In your Cloud Shell window, clone your GitHub repo with `git clone`

At this point, we are ready to *build* our Docker images and then *run* a copy.

* Navigate to the `bioc` directory with `cd docker-practice/Bioc`
* Build your image with `docker build -t my-bioc-container .`
* Run the container with `docker run -it my-bioc-container`

Type some commands and verify that the packages were installed correctly.

#### samtools installation

If there is time, build and run a second Docker image with samtools installed. We will use a base Linux image (i.e. ubuntu:latest).

* Create a new folder in your repository called `samtools`
* Create a new text file at `samtools/Dockerfile`
* Add the following commands to your Dockerfile:

```         
FROM ubuntu:latest

RUN apt-get update && apt-get install -y samtools

CMD ["/bin/bash"]
```

* Commit and push your changes to GitHub.
* In your Cloud Shell window, pull your changes from GitHub with `git pull`

At this point, we are ready to *build* our Docker images and then *run* a copy.

* Navigate to the `samtools` directory with `cd ../samtools`
* Build your image with `docker build -t my-samtools-container .`
* Run the container with `docker run -it my-samtools-container`

Type some commands and verify that the packages were installed correctly.