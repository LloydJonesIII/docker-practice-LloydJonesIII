FROM rocker/rstudio:latest

# Update package lists and install dependencies
RUN apt-get update && \
    apt-get install -y zlib1g-dev && \
    # Clean up apt cache to reduce image size
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

RUN R -e "install.packages('BiocManager'); BiocManager::install(c('DESeq2', 'Biostrings'))"

CMD ["R"]