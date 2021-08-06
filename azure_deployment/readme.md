
## Create a new deployment

az group create --name <resource_group_name> --location <location>

az deployment group create -g <resource_group_name> --template-file template.json --parameters @parameters.json

## Create database schema

From deployed resource collect:
 - IoT Hub connection string
 - SQL Database connection string

## Use VS Code to deploy the function app to the created Azure Functions site, setting connection strings for IoT Hub and SQL Database collected above as env variables

## Install Docker on the device simulator VM

## Provision IoT simulated devices
sudo docker run -it -e "IotHubConnectionString=<iot_hub_connection_string>" -e DeviceCount=26000 iottelemetrysimulator/azureiot-simulatordeviceprovisioning

## Start generating messages with predefined format on provisioned devices
sudo docker run -it -e "IotHubConnectionString=<iot_hub_connection_string>" -e Template="{ \"deviceId\": \"$.DeviceId\", \"temp\": $.Temp, \"Ticks\": $.Ticks, \"Counter\": $.Counter, \"time\": \"$.Time\", \"engine\": \"$.Engine\" }" -e Variables="[{name: \"Temp\", \"random\": true, \"max\": 42, \"min\": 10}, {\"name\":\"Counter\", \"min\":1}, {name:\"Engine\", values: [\"on\", \"off\"]}]" -e MessageCount=0 -e DeviceCount=26000 -e Interval=100 iottelemetrysimulator/azureiot-telemetrysimulator

## Connect to the database using SSMS and start running queries to see captured events

-- Number of events in the data store

SELECT rowcnt,name,* FROM sys.sysindexes WHERE id = OBJECT_ID('dbo.events')

-- Latency of data ingested

SELECT TOP 1 * FROM dbo.events ORDER BY timestamp DESC

-- Typical time series query

SELECT * FROM vTimeSeriesBuckets WHERE deviceid IN ('sim000001','sim000002','sim000004','sim000011') ORDER BY timeslot DESC, deviceid

-- Resource consumption

SELECT * FROM sys.dm_db_resource_stats ORDER BY end_time DESC