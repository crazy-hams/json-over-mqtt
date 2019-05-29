# JSON over MQTT
Specification documentation on the usage of JSON over MQTT for DX Cluster telemetry data

## JSON format
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


### The timestamp
The timestamp has been defined in ISO format with 

```
  "tstamp" : "20190529T124733.170851",
``` 


### Basic data
Basic data is composed of the typical data any ham radio operator handles during radio operations. In essence, basic data is the bare minimum data the cluster can process to provide the necessary insights ham radio operators can use to correctly operate their rigs, either manyally or mechanically by means of CAT protocol. 

```
  "b-data" : {
    "dx" : "4X6TU",
    "qrg" : "21150.1",
    "src" : "CT1BOH-#",
    "cmt" : "CW 3 dB 22 WPM NCDXF BCN"
  },
``` 


### Extended data
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

