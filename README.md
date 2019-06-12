# JOM: JSON over MQTT specification
Specification documentation on the usage of JSON over MQTT for DX Cluster telemetry data

## Proposed solution

### General description
TBD

### MQTT layer
At the core of the proposed solution to the old-fashioned PC protocol ruling the DX Cluster telemetry data in use today (since the 80's past century) **we’re going to deploy a MQTT layer.** 

MQTT is a standardised protocol designed for quick data interchange, based around a publish/subscribe model. MQTT producers need to connect to a broker to publish their data. We propose a MQTT layer based on Mosquitto or emqX open source software. 

In order to avoid a network break-up between the new technology and the legacy one we’re going to implement a kind of gateway, which will subscribe to the relevant topics on our broker, get telemetry data from the current DX Cluster servers, reformat the spot data and then write into the MQTT network layer. For persistence it's also designed a process that will take the information out of MQTT layer and will replicate the telemetry data set on the backend. For that backend a microservice wirtten in Python will persist data to a NoSQL database, in essence MongoDB or CouchDB. 

Since we may theoretically have many thousands or millions of ham radio operators on a single moment it’s important the acquisition layer is scalable. We’re going to achieve this on the MQTT side by load balancing multiple instances of Mosquitto endpoints using a named VIP, being possible implementations virtual machines or containers on a container cluster. 

Whilst this will work for the device facing side, if we were to use the VIP from our gateway layer, and we want that also to be scalable, then we could potentially end up with missing data since our gateways could be reading from any of the Mosquitto instances, and the MQTT layer isn’t clustered. To solve this problem, we’ll deploy each Mosquitto instance together with it’s own dedicated gateway microservice in a pod, and have the gateway microservice connect to Mosquitto over the localhost connection, ensuring each gateway only gets data from a single Mosquitto instance. So for a device connecting via the loadbalancer address, it can connect to any of our deployed Mosquitto instances and our storage layer will get the data.


### JSON layer
#### JSON FORMAT
The proposed JSON format is composed of the most useful information for the ham radio operator, but, at the same time, it was also equally defined thinking in additional uses and application developers. 

```
{
  "tstamp" : "20190529T124733.170851",
  "b-data" : {
    "dx" : "4X6TU",
    "qrg" : "21150.1",
    "src" : "CT1BOH-#",
    "cmt" : "CW 3 dB 22 WPM NCDXF BCN"
  },
  "e-data" : {
    "type" : "by-skim",
    "cluster" : "RBN",
    "src-ip" : "0.0.0.0",
    "src-dxcc" : "4X",
    "dst-dxcc" : "CT"
  }
}
```

In essense, the JSON format can be divided into 3 different senctions:
* a timestamp (tstamp)
* basic data (b-data clause)
* extended data, useful for applications, filtering and scientific research (e-data)


##### THE TIMESTAMP AND ITS USAGE
The timestamp has been defined in ISO format with 

```
  "tstamp" : "20190529T124733.170851",
``` 


##### BASIC DATA
Basic data is composed of the typical data any ham radio operator handles during radio operations. In essence, basic data is the bare minimum data the cluster can process to provide the necessary insights ham radio operators can use to correctly operate their rigs, either manyally or mechanically by means of CAT protocol. 

```
  "b-data" : {
    "dx" : "4X6TU",
    "qrg" : "21150.1",
    "src" : "CT1BOH-#",
    "cmt" : "CW 3 dB 22 WPM NCDXF BCN"
  },
``` 


##### EXTENDED DATA
Extended data includes that information that can be valuable for applications to provide filtering capabilities, personalization options and valuable data for scientific research, like path openings, statistical usage of the network or propagation predictions. 

```
  "e-data" : {
    "type" : "by-skim",
    "cluster" : "RBN",
    "src-ip" : "0.0.0.0",
    "src-dxcc" : "4X",
    "dst-dxcc" : "CT"
  }
``` 

