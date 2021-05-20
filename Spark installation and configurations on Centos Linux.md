# Apache Spark Installation processes step by step

wget https://archive.apache.org/dist/hadoop/core/hadoop-3.1.1/hadoop-3.1.1.tar.gz

tar -xvzf hadoop-3.1.1.tar.gz

mv hadoop-3.1.1 /usr/

chmod 755 /usr/hadoop-3.1.1


## How to install Spark3.1.1 without Hadoop in RHEL 7 /Centos 7:
## download the package
wget https://archive.apache.org/dist/spark/spark-3.1.1/spark-3.1.1-bin-without-hadoop.tgz

## Make directory called "hadoop" and under dir mkdir called spark-3.1.1
mkdir hadoop
cd haddoop
mkdir spark-3.1.1

## OR make subdirectory in one line:
mkdir ~/hadoop/spark-3.1.1

## Unzip the downloaded package and copy it directly to the spark-3.1.1 path
tar -xvzf spark-3.1.1-bin-without-hadoop.tgz -C ~/hadoop/spark-3.1.1/ --strip 1

## Setup environment variables

vi ~/.bashrc

## Add the following lines to the end of the file:

export SPARK_HOME=~/hadoop/spark-3.1.1                                
export PATH=$SPARK_HOME/bin:$PATH
# Configure Spark to use Hadoop classpath
export SPARK_DIST_CLASSPATH=$(hadoop classpath)

## Activate the bashrc
source ~/.bashrc

## To run Apache Spark
spark-shell

## Run Spark interactive shell

## How to logout or exit in spark-shell
:q or :quit
# Spark context Web UI available at 
http://dnsname:4040

## Or
http://HostIP:4040

## How to check Spark version in terminal
spark-sql --version
spark-submit --version
spark-shell --version