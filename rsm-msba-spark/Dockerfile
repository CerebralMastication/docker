FROM vnijs/rsm-msba:latest

MAINTAINER Vincent Nijs "radiant@rady.ucsd.edu"
ENV DEBIAN_FRONTEND noninteractive

## installing java
RUN apt-add-repository ppa:webupd8team/java
RUN apt-get update \
  && apt-get -y upgrade

RUN echo debconf shared/accepted-oracle-license-v1-1 select true | debconf-set-selections
RUN echo debconf shared/accepted-oracle-license-v1-1 seen true | debconf-set-selections
RUN apt-get install -y oracle-java8-installer || true
RUN apt-get install -y oracle-java8-set-default
RUN R CMD javareconf

ENV SPARK_VERSION=2.3.2
ENV HADOOP_VERSION=2.7

## install the R kernel for Jupyter Lab
RUN R -e 'options(spark.install.dir = "/opt")' \
      -e 'sparklyr::spark_install(version = Sys.getenv("SPARK_VERSION"), hadoop_version = Sys.getenv("HADOOP_VERSION"))'

ENV SPARK_HOME=/opt/spark-${SPARK_VERSION}-bin-hadoop${HADOOP_VERSION}
ENV JAVA_HOME=/usr/lib/jvm/java-8-oracle/jre
RUN echo "SPARK_HOME=${SPARK_HOME}" >> /etc/R/Renviron.site
RUN echo "JAVA_HOME=${JAVA_HOME}" >> /etc/R/Renviron.site

## install python packages
COPY py_requirements.txt /home/rstudio/py_requirements.txt
RUN pip3 install --upgrade -r /home/rstudio/py_requirements.txt

ENV IPYTHON=1
ENV PYSPARK_PYTHON=/usr/bin/python3
ENV PYSPARK_DRIVER_PYTHON=ipython3
ENV PYSPARK_DRIVER_PYTHON_OPTS="notebook"

EXPOSE 80 8787 8888

CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]