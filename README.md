# stream-processing-vs-faas

Set up VPC and EC2 instances

SSH into both EC2 instances

On both EC2 instances:
`sudo yum install java-1.8.0-openjdk`
`sudo yum install git -y`
`wget https://apache.claz.org/kafka/2.7.0/kafka_2.13-2.7.0.tgz`
`tar xzf kafka_2.13-2.7.0.tgz`
`ln -s kafka_2.13-2.7.0 kafka`

On first EC2 instance:
`cd kafka`
`mkdir /tmp/zookeeper`
`touch /tmp/zookeeper/myid`
`echo "1" >> /tmp/zookeeper/myid`
`nano config/zookeeper.properties`
And add the IP address of the second EC2 instance
server.2=<<IP address of second instance>>:2888:3888

On second EC2 instance:
`cd kafka`
`mkdir /tmp/zookeeper`
`touch /tmp/zookeeper/myid`
`echo "2" >> /tmp/zookeeper/myid`
And add the IP address of the first EC2 instance
server.1=<<IP address of first instance>>:2888:3888


Example of zookeeper.properties for frst EC2 instance:
```
# Licensed to the Apache Software Foundation (ASF) under one or more
# contributor license agreements.  See the NOTICE file distributed with
# this work for additional information regarding copyright ownership.
# The ASF licenses this file to You under the Apache License, Version 2.0
# (the "License"); you may not use this file except in compliance with
# the License.  You may obtain a copy of the License at
#
#    http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
# the directory where the snapshot is stored.
dataDir=/tmp/zookeeper
# the port at which the clients will connect
clientPort=2181
initLimit=5
syncLimit=2
# list of servers: <ip>:2888:3888
server.1=0.0.0.0:2888:3888
server.2=172.31.2.105:2888:3888
# disable the per-ip limit on the number of connections since this is a non-production config
maxClientCnxns=0
# Disable the adminserver by default to avoid port conflicts.
# Set the port to something non-conflicting if choosing to enable this
admin.enableServer=false
# admin.serverPort=8080
```

Example of zookeeper.properties for second EC2 instance:
```
# Licensed to the Apache Software Foundation (ASF) under one or more
# contributor license agreements.  See the NOTICE file distributed with
# this work for additional information regarding copyright ownership.
# The ASF licenses this file to You under the Apache License, Version 2.0
# (the "License"); you may not use this file except in compliance with
# the License.  You may obtain a copy of the License at
#
#    http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
# the directory where the snapshot is stored.
dataDir=/tmp/zookeeper
# the port at which the clients will connect
clientPort=2181
initLimit=5
syncLimit=2
tickTime=2000
# list of servers: <ip>:2888:3888
server.1=172.31.3.117:2888:3888
server.2=0.0.0.0:2888:3888
# disable the per-ip limit on the number of connections since this is a non-production config
maxClientCnxns=0
# Disable the adminserver by default to avoid port conflicts.
# Set the port to something non-conflicting if choosing to enable this
admin.enableServer=false
# admin.serverPort=8080
```

Start zookeeper on each instance using:
`bin/zookeeper-server-start.sh config/zookeeper.properties`