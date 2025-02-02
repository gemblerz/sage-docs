---
sidebar_position: 3
---

# Access and Use Data

![Data Movement](./images/data_movement.svg)


Raw sensor data is collected by edge code. This edge code can either talk to sensor hardware directly or may obtain data from an abstraction layer (not show in image above). Edge code may forward unprocessed sensor data, do light processing to convert raw sensor values into final data products, or may use CPU/GPU-intensive workloads (e.g. AI application) to extract information from data-intensive sensors such as cameras, microphone or LIDAR.  

Sensor data from nodes that comes in numerical or textual form (e.g. temperature) is stored natively in our time series database. Sensor data in form of large files (images, audio, movies..) is stored in the Sage object store, but is referenced in the time series data (thus the dashed arrow in the figure above). Thus, the primary way to find all data (sensor and large files) is via the Sage sensor query API described below.

Currently the SAGE sensor database contains data such as:

- relative humidity, barometric pressure, ambient temperature and gas (VOC) [BME680](https://www.bosch-sensortec.com/products/environmental-sensors/gas-sensors/bme680/)
- rainfall measurements [(Hydreon RG-15)](https://sage-commons.sdsc.edu/dataset/rg-15) 
- AI-based cloud coverage estimation from camera images
- AI-based object counts from camera images
- (system data of the nodes, free disk space etc.)

Data can be accessed via "data bundles"  or by API calls:

**Data bundles** are static collections of sensor data which includes all metadata needed to understand how the data was generated. Data bundles are targeted at scientists that want to be able to cite data they used in their publications. To find data bundles, please use the Sage commons web portal (link to be provided)

The **Sage sensor query API** allows for flexible access to historical and "real time" data in Sage.  This example shows how to retrieve data the latest data from a specific sensor (you can adjust the `start` field if you do not get any recent data):

```console
curl -H 'Content-Type: application/json' https://sdr.sagecontinuum.org/api/v1/query -d '
{
    "start": "-10s",
    "filter": {
        "sensor": "bme680"
    }
}
'
```
Example results:
```json
{"timestamp":"2021-08-09T19:26:03.880781217Z","name":"iio.in_humidityrelative_input","value":70.905,"meta":{"node":"000048b02d15bdcd","plugin":"plugin-metsense:0.1.1","sensor":"bme680"}}
{"timestamp":"2021-08-09T19:26:03.878659392Z","name":"iio.in_pressure_input","value":975.78,"meta":{"node":"000048b02d15bdcd","plugin":"plugin-metsense:0.1.1","sensor":"bme680"}}
{"timestamp":"2021-08-09T19:26:03.872652127Z","name":"iio.in_resistance_input","value":93952,"meta":{"node":"000048b02d15bdcd","plugin":"plugin-metsense:0.1.1","sensor":"bme680"}}
{"timestamp":"2021-08-09T19:26:03.874998057Z","name":"iio.in_temp_input","value":27330,"meta":{"node":"000048b02d15bdcd","plugin":"plugin-metsense:0.1.1","sensor":"bme680"}}
```

:::tip
More details on how to use the query API can be found [here](https://github.com/waggle-sensor/waggle-beehive-v2/blob/main/docs/querying-measurements.md#query-api)
:::




## Data Model

A detailed description of the data model can be found [here](https://github.com/waggle-sensor/waggle-beehive-v2/blob/main/docs/querying-measurements.md#data-model).


## Accessing Large Files (i.e. Training Data)
A query to the Sage sensor query API can give a result that looks like this:
```json
{
  "timestamp":"2021-02-24T21:14:33.094407Z",
  "name":"env.camera",
  "value":"https://osn.sagecontinuum.org/api/v1/objects/0000000000000001-sage-camera-v1.2-20210224/1621658141892137216-image.jpg",
  "meta": {
    "node":"0000000000000001",
    "plugin":"sage/camera:1.0.2"
  }
}
```

The file, referenced in the field `value` can be downloaded like this: (Note: This specifc example file does not exist yet)
```console
curl -O https://osn.sagecontinuum.org/api/v1/objects/0000000000000001-sage-camera-v1.2-20210224/1621658141892137216-image.jpg
```