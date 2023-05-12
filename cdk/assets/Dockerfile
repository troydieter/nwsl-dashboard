FROM public.ecr.aws/lambda/provided

ENV R_VERSION=4.0.3

RUN yum -y install wget libcurl libcurl-devel

RUN yum -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm \
  && wget https://cdn.rstudio.com/r/centos-7/pkgs/R-${R_VERSION}-1-1.x86_64.rpm \
  && yum -y install R-${R_VERSION}-1-1.x86_64.rpm \
  && rm R-${R_VERSION}-1-1.x86_64.rpm

ENV PATH="${PATH}:/opt/R/${R_VERSION}/bin/"

# System requirements for R packages
RUN yum -y install openssl-devel

RUN Rscript -e "install.packages(c('httr', 'jsonlite', 'logger', 'logging', 'knitr'), repos = 'https://cloud.r-project.org/')"
RUN Rscript -e "library(knitr); opts_chunk$set(fig.path = 'man/figures/README-')"
RUN Rscript -e "install.packages('remotes', repos = 'https://cloud.r-project.org/'); remotes::install_github('nwslR/nwslR', ref = 'v0.0.0.9002')"

COPY runtime.R functions.R bootstrap.R ${LAMBDA_TASK_ROOT}/
RUN chmod 755 -R ${LAMBDA_TASK_ROOT}/


RUN printf '#!/bin/sh\ncd $LAMBDA_TASK_ROOT\nRscript bootstrap.R' > /var/runtime/bootstrap \
  && chmod +x /var/runtime/bootstrap


CMD [ "functions.myRcode" ]