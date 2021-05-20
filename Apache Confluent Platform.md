
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
tickTime=2000
dataDir=/var/lib/zookeeper/
clientPort=2181
initLimit=5
syncLimit=2
server.1=zoo1:2888:3888
server.2=zoo2:2888:3888
server.3=zoo3:2888:3888
autopurge.snapRetainCount=3
autopurge.purgeInterval=24

## Kafka. Navigate to the Apache Kafka properties file 
/etc/kafka/server.properties
### Add the following script:
zookeeper.connect=zookeeper:2181

## Control Center. Navigate to the Control Center properties file
/etc/confluent-control-center/control-center-production.properties
