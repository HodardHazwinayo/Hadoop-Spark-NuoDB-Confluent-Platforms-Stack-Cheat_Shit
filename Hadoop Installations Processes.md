# Create Hadoop user
useradd -m -d /home/hadoop -s /bin/bash hadoop
passwd hadoop
su hadoop
cd
# Generate SSH for hadoop user connection
ssh-keygen -t rsa
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
ssh localhost

# Install Hadoop 3.1.1
wget https://archive.apache.org/dist/hadoop/core/hadoop-3.1.1/hadoop-3.1.1.tar.gz
tar -zxvf hadoop-3.1.1.tar.gz 
mv hadoop-3.1.1 hadoop

# Set environment variable uses by Hadoop
vi ~/.bashrc

export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.282.b08-1.el7_9.x86_64/jre
export HADOOP_HOME=/home/hadoop/hadoop  
export HADOOP_INSTALL=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export HADOOP_YARN_HOME=$HADOOP_HOME
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin

# exit and access user hadoop again
exit
su hadoop
cd

# Modify Configuration files and change JAVA_HOME
vi $HADOOP_HOME/etc/hadoop/hadoop-env.sh

export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.282.b08-1.el7_9.x86_64/jre

# check hadoop version
hadoop version

# Setup Hadoop Configuration Files
cd $HADOOP_HOME/etc/hadoop
vi core-site.xml

<configuration>
    <property>
        <name>fs.default.name</name>
        <value>hdfs://localhost:51003</value>
    </property>
</configuration>

vi hdfs-site.xml

<configuration>
        <property>
                <name>dfs.replication</name>
                <value>1</value>
        </property>
        <property>
                <name>dfs.name.dir</name>
                <value>file:///home/hadoop/hadoopdata/hdfs/namenode</value>
        </property>
        <property>
                <name>dfs.data.dir</name>
                <value>file:///home/hadoop/hadoopdata/hdfs/datanode</value>
        </property>
</configuration>

# Create NameNode and DataNode directories in hadoop user’s home directory.
mkdir -p ~/hadoopdata/hdfs/{namenode,datanode}

vi mapred-site.xml

<configuration>
        <property>
                <name>mapreduce.framework.name</name>
                <value>yarn</value>
        </property>
</configuration>

vi yarn-site.xml

<configuration>
        <property>
                <name>yarn.nodemanager.aux-services</name>
                <value>mapreduce_shuffle</value>
        </property>
</configuration>

# check the storage directory and initiate hadoop changes
hdfs namenode -format

# In dir /home/hadoop/hadoop/sbin or $HADOOP_HOME/sbin/, run start-dfs.sh (NameNode daemon and DataNode daemon) port:9870
start-dfs.sh

# One command to start all hadoop  components
./start-all.sh
# start-yarn (Start ResourceManager daemon and NodeManager daemon) port:8088
start-yarn.sh
# To test hadoop running in the browser : 
http://10.24.37.24:9870/ Or http://dns names:9870/
# Test Hadoop Single Node Cluster Setup
hdfs dfs -mkdir -p /user/ts

# Test upload a file into HDFS directory
hdfs dfs -put ~/.bashrc /user/ts

# Copy the files from HDFS to your local file systems.

$ hdfs dfs -get /user/ts /tmp/

# Delete the files and directories using the following commands.
hdfs dfs -rm -f /user/ts/.bashrc
hdfs dfs -rmdir /user/ts

# References: 
[Install Hadoop-3.2.1 Version on RHEL / Centos 7.x](https://github.com/TSeekasamit/bigdata/blob/master/install-hadoop3.2.1-centos7)

[Install Hadoop-3.2.1 Version on RHEL / Centos 7.x](https://www.edureka.co/blog/install-hadoop-single-node-hadoop-cluster?utm_source=youtube&utm_campaign=hadoop-installation-single-node-051216-wr&utm_medium=description)

[Other link to check](https://www.programmersought.com/article/30564579240/)

[Other link to check](https://www.programmersought.com/article/66612310024/)

