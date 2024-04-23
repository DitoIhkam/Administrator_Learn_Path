![alt text](?raw=true)
# Introduction

One of the easiest ways to get started with local development is to install Confluent Platform via TAR archive. In this module, you will do a tarball installation of Confluent Platform and use the confluent local CLI to do some local experiments.

# Activity
In this activity, you will:

* Perform a **tarball installation of Confluent Platform**

* Configure the Confluent CLI

* Start Confluent Platform services locally

* Produce and consume Avro data with the help of Schema Registry

## Tarball Installation of Confluent Platform

1. Install Java 11 with sudo
```
sudo apt-get install openjdk-11-jre-headless
```

> need java 11 or more (17?)

2. Download and extract the Confluent Platform 7.1.1 TAR archive (or more cp version?)
```
curl -O http://packages.confluent.io/archive/7.1/confluent-7.1.1.tar.gz
```
```
tar xvzf confluent-7.1.1.tar.gz
```
(image installation)
![alt text](https://github.com/DitoIhkam/Administrator_Learn_Path/blob/main/3.%20Install%20Confluent%20Platform/img/1.png?raw=true)

3. Take a moment to explore via vscode
```
code ~/confluent-7.1.1
```
(image installation)
![alt text](https://github.com/DitoIhkam/Administrator_Learn_Path/blob/main/3.%20Install%20Confluent%20Platform/img/2.png?raw=true)
## Configure the Confluent CLI

1. Set the `CONFLUENT_HOME` variable to the location of the install and add it to the `.bashrc.`
```
export CONFLUENT_HOME=${HOME}/confluent-7.1.1 \
&& echo "export CONFLUENT_HOME=$CONFLUENT_HOME" >> ~/.bashrc
```
(image installation)

2. Add the Confluent Platform binaries (which includes the `confluent` CLI) to the `PATH` variable.
```
echo "export PATH=$CONFLUENT_HOME/bin:${PATH}" >> ~/.bashrc
```
(image installation)
![alt text](https://github.com/DitoIhkam/Administrator_Learn_Path/blob/main/3.%20Install%20Confluent%20Platform/img/3.png?raw=true)

3. Enable bash completion for the `confluent` CLI and source the `.bashrc` for all the changes to take effect.
```
~/confluent-7.1.1/bin/confluent completion bash | sudo tee /etc/bash_completion.d/confluent \
&& echo "source /etc/bash_completion.d/confluent" >> ~/.bashrc \
&& source ~/.bashrc
```

(image installation)
![alt text](https://github.com/DitoIhkam/Administrator_Learn_Path/blob/main/3.%20Install%20Confluent%20Platform/img/4.png?raw=true)

## Start Confluent Local Service

1. Start the entire Confluent Platform.
```
confluent local services start
```

2. The output of the previous command shows the location of the data and configurations in `/tmp/confluent.<some number>`. Briefly explore that directory in Visual Studio Code.
```
code /tmp/confluent.<number>
```
![alt text](https://github.com/DitoIhkam/Administrator_Learn_Path/blob/main/3.%20Install%20Confluent%20Platform/img/5.png?raw=true)

## Produce and Consume Avro Data

1. Create an Avro schema file for the data you will produce to Kafka.
```
cat <<EOF > ~/temperature_reading.avsc
{
 "namespace": "io.confluent.examples",
 "type": "record",
 "name": "temperature_reading",
 "fields": [
    {"name": "city", "type": "string"},
    {"name": "temp", "type": "int", "doc": "temperature in Fahrenheit"} ]
}
EOF
```

2. Open a second terminal window and set the two windows side by side.

3. In the right terminal window, set up a command line consumer to read keys and values from messages in the `temperatures` Kafka topic.

```
confluent local services \
kafka consume temperatures \
--property print.key=true \
--property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer \
--value-format avro
```


> confluent local services kafka knows to look for Kafka at localhost:9092. The print.key=true property means we will see each event’s key in addition to its value. We provide a deserializer for the key. The --value-format avro means we are expecting Avro serialized data for the value.

4. In the left terminal, start a command line producer to produce events to the temperatures Kafka topic.

```
confluent local services \
kafka produce temperatures \
--property parse.key=true --property key.separator=, \
--property key.serializer=org.apache.kafka.common.serialization.StringSerializer \
--value-format avro \
--property value.schema.file=$HOME/temperature_reading.avsc
```
![alt text](https://github.com/DitoIhkam/Administrator_Learn_Path/blob/main/3.%20Install%20Confluent%20Platform/img/6.1.png?raw=true)

>The parse.key=true means we are producing events with keys and values rather than just values. The key.separator=, means we are using a comma to separate keys and values. We specify a String serializer for the key. The value format is Avro and the schema for the value is provided with the temperature_reading.avsc file we created earlier.

5. Write a few `temperature` events to the temperatures topic. Press `Enter` to submit each event.
```
alameda,{"city":"alameda","temp":58}
ashland,{"city":"ashland","temp":62}
nairobi,{"city":"nairobi","temp":65}
sydney,{"city":"sydney","temp":75}
```

![alt text](https://github.com/DitoIhkam/Administrator_Learn_Path/blob/main/3.%20Install%20Confluent%20Platform/img/6.2.png?raw=true)

6. Stop any running producer and consumer processes with Ctrl+C.

## (Optional) Explore Confluent Control Center
1. Open a browser and navigate to http://localhost:9021 to open Confluent Control Center.

a. Select Cluster 1 from the left menu.

b. Take a moment to consider each section:

* Overview

* Brokers

* Topics → temperatures → Schema

* Connect

* ksqlDB

* Consumers

* Cluster Settings

![alt text](https://github.com/DitoIhkam/Administrator_Learn_Path/blob/main/3.%20Install%20Confluent%20Platform/img/7.png?raw=true)
![alt text](https://github.com/DitoIhkam/Administrator_Learn_Path/blob/main/3.%20Install%20Confluent%20Platform/img/7.1.png?raw=true)
![alt text](https://github.com/DitoIhkam/Administrator_Learn_Path/blob/main/3.%20Install%20Confluent%20Platform/img/7.2.png?raw=true)
![alt text](https://github.com/DitoIhkam/Administrator_Learn_Path/blob/main/3.%20Install%20Confluent%20Platform/img/7.3.png?raw=true)

## Stop Confluent Local Services
1. Destroy your local Confluent Platform deployment.

```
confluent local destroy
```
## Destroy Your Lab VM
You can safely destroy your lab virtual machine when you are finished.

## Activity Debrief
* Confluent Platform requires Java 8 or 11.

* It’s straightforward to download and extract the Confluent Platform TAR archive for local development

* The confluent local CLI is a great way to get the entire Confluent Platform running quickly for local development

## Summary
In this module, you:

* Performed a tarball installation of Confluent Platform

* Started Confluent Platform services locally with the confluent local CLI

* Produced and consumed Avro data with the help of Schema Registry

### References
* TAR Archive Installation Documentation

* Confluent CLI Producer Documentation and Examples

* Confluent CLI Consumer Documentation and Examples
