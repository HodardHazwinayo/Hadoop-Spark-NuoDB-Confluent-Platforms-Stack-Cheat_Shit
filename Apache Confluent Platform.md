
# Apache Confluent Platform
This topic provides instructions for installing a production-ready Confluent 
Platform configuration in a multi-node RHEL or CentOS environment 
with a replicated ZooKeeper ensemble

## Port Configuration
By default the Confluent Platform components use these ports to communicate. 
The following ports must be opened before: 
Component                                     	Port
Apache Kafka brokers (plain text)    			9092
Confluent Control Center            			9021
Kafka Connect REST API            				8083
REST Proxy                                    	8082
Schema Registry REST API            			8081
ZooKeeper                                    	2181

1. firewall-cmd --zone=public --add-port=9092/tcp --permanent
2. firewall-cmd --zone=public --add-port=9021/tcp --permanent
3. firewall-cmd --zone=public --add-port=8083/tcp --permanent
4. firewall-cmd --zone=public --add-port=8082/tcp --permanent
5. firewall-cmd --zone=public --add-port=8081/tcp --permanent
6. firewall-cmd --zone=public --add-port=2181/tcp --permanent
7. firewall-cmd --reload

## Confluent installations processes step by step

## Step1. Install the curl and which tools.
sudo yum install curl which
## Step2. Install the Confluent Platform public key. This key is used to sign packages in the YUM repository
sudo rpm --import https://packages.confluent.io/rpm/6.1/archive.key
## Step3. Add the repository to your /etc/yum.repos.d/ directory in a file named confluent.repo
[Confluent.dist]
name=Confluent repository (dist)
baseurl=https://packages.confluent.io/rpm/6.1/$releasever
gpgcheck=1
gpgkey=https://packages.confluent.io/rpm/6.1/archive.key
enabled=1

[Confluent]
name=Confluent repository
baseurl=https://packages.confluent.io/rpm/6.1
gpgcheck=1
gpgkey=https://packages.confluent.io/rpm/6.1/archive.key
enabled=1
## Step4. Clear the YUM caches and install Confluent Platform
sudo yum clean all && sudo yum install confluent-platform
## Optional: Confluent Platform with [RBAC](https://docs.confluent.io/platform/current/security/rbac/index.html#rbac-overview):
sudo yum clean all && \
sudo yum install confluent-platform && \
sudo yum install confluent-security

## Step5. Confluent Platform using only Confluent Community components
sudo yum clean all &&  sudo yum install confluent-community-5.2.2
## Configure Confluent Platform
## Zookeeper. Navigate to the ZooKeeper properties file
/etc/kafka/zookeeper.properties
### Add this script:
1. tickTime=2000
2. dataDir=/var/lib/zookeeper/
3. clientPort=2181
### The below Script are optional in case of no cluster
4. initLimit=5
5. syncLimit=2
6. server.1=zoo1:2888:3888
7. server.2=zoo2:2888:3888
8. server.3=zoo3:2888:3888
9. autopurge.snapRetainCount=3
10. autopurge.purgeInterval=24

## Kafka. Navigate to the Apache Kafka properties file 
/etc/kafka/server.properties
### Add the following script:
zookeeper.connect=zookeeper:2181

## Control Center. Navigate to the Control Center properties file
/etc/confluent-control-center/control-center-production.properties

### -----Begin Customize the following lines:
#### host/port pairs to use for establishing the initial connection to the Kafka cluster
bootstrap.servers=<hostname1:port1,hostname2:port2,hostname3:port3,...>
#### location for Control Center data
confluent.controlcenter.data.dir=/var/lib/confluent/control-center
#### the Confluent license
confluent.license=<your-confluent-license>
#### ZooKeeper connection string with host and port of a ZooKeeper servers
zookeeper.connect=<hostname1:port1,hostname2:port2,hostname3:port3,...>
### ----End of the customization of the lines above

#### ----Navigate to the Kafka server configuration file (/etc/kafka/server.properties) 
and enable Confluent Metrics Reporter----####.

### Begin-------
##################### Confluent Metrics Reporter #######################
#### Confluent Control Center and Confluent Auto Data Balancer integration
#
##### Uncomment the following lines to publish monitoring data for
##### Confluent Control Center and Confluent Auto Data Balancer
##### If you are using a dedicated metrics cluster, also adjust the settings
##### to point to your metrics Kafka cluster.
metric.reporters=io.confluent.metrics.reporter.ConfluentMetricsReporter
confluent.metrics.reporter.bootstrap.servers=localhost:9092
#
##### Uncomment the following line if the metrics cluster has a single broker
confluent.metrics.reporter.topic.replicas=1
### End --------

## On Kafka. Add these lines to the Kafka Connect properties file
/etc/kafka/connect-distributed.properties
### Interceptor setup
consumer.interceptor.classes=io.confluent.monitoring.clients.interceptor.MonitoringConsumerInterceptor
producer.interceptor.classes=io.confluent.monitoring.clients.interceptor.MonitoringProducerInterceptor

## Confluent REST Proxy
/etc/kafka-rest/kafka-rest.properties

### Optional customization:
zookeeper.connect=zookeeper:2181
## Schema Registry
/etc/schema-registry/schema-registry.properties
#### ----Begin
####### Specify the address the socket server listens on, e.g. listeners = PLAINTEXT://your.host.name:9092
listeners=http://0.0.0.0:8081

###### The host name advertised in ZooKeeper. This must be specified if your running Schema Registry
# with multiple nodes.
host.name=192.168.50.1

###### List of Kafka brokers to connect to, e.g. PLAINTEXT://hostname:9092,SSL://hostname2:9092
kafkastore.bootstrap.servers=PLAINTEXT://hostname:9092,SSL://hostname2:9092
#### ----End

## Commands To Start Confluent Platform
1. Start ZooKeeper.
sudo systemctl start confluent-zookeeper
2. Start Kafka 
#### Confluent Platform
sudo systemctl start confluent-server
#### Confluent Platform using only Confluent Community components:
sudo systemctl start confluent-kafka
3. Start Schema Registry
sudo systemctl start confluent-schema-registry
4 Start other Confluent Platform components as desired
### Control Center
sudo systemctl start confluent-control-center
### Kafka Connect
sudo systemctl start confluent-kafka-connect
### Confluent REST Proxy
sudo systemctl start confluent-kafka-rest
### ksqlDB
sudo systemctl start confluent-ksqldb

## You can check service status with this command:
systemctl status confluent*
## Uninstall 
sudo yum autoremove <component-name>
### For example, run this command to remove Confluent Platform:
sudo yum autoremove confluent-platform

# Reference
[Manual Install using Systemd on RHEL and CentOS](https://docs.confluent.io/platform/current/installation/installing_cp/rhel-centos.html)
[How to Install Confluent Kafka on Centos & Ubuntu](https://manastri.blogspot.com/2020/03/how-to-install-confluent-kafka-on.html)