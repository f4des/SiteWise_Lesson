## First verify Java 8 is installed

`sudo apt update`
`sudo apt install openjdk-8-jre`

`java -version`

## Java8 directory required

`sudo ln -s /usr/bin/java /usr/bin/java8`

# SiteWise Permissions for Group Role 

## policy

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "iotsitewise:BatchPutAssetPropertyValue",
      "Resource": "*"
    }
  ]
}
```

## trust policy

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "greengrass.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```
# SiteWise Gateway Setup 


## Create SiteWise data directory

`sudo mkdir /var/sitewise` 
`sudo chown ggc_user /var/sitewise`
`sudo chmod 700 /var/sitewise`


## Modbus Commands 

`sudo  x86_64-linux-gnu/diagslave -a 1 -m tcp`


`modpoll -r 1201 -c 1 10.0.0.100 1234`

# Common Troubleshooting points 

## Troubleshooting Checklist:

1. Are configuration files present?
2. Does gateway show as IN_SYNC?
3. What error is being seen in  `/greengrass/ggc/var/log/user/us-east-1/aws` for the swCollector, ModbusTCPConnector & swPublisher logs?

## Common issues

Failed to publish messages because an SDK client error occurred: 

- No Service role attached to deployed Greengrass core

Sync Status won't show as IN_SYNC:

- This is most often due to the required connector not being present with the Greengrass core.

swPublisher.log: Unable to load configuration file. Attempting to use environment variables:

- This is often due to the swPublisher.config file being deleted. This won't propagate again, and will need to be manualy recreated or a new gateway created.

Error saving credentials: error storing credentials - err: exit status 1, out: 'Cannot autolaunch D-Bus without X11 $DISPLAY':

  - This one is usually more related to SiteWise edge, however is the result of a missing library that can be installed with:
  
    `sudo apt install gnupg2 pass`

The data is being sent to the cloud but not appearing in my model?

- This is often due to a misconfiguration of the data types in the model compared to the data coming from the source. The data types must match, for example, you can't read data as an int16 from the server, send it as a String to the Model which is expecting an Integer.

- Alternatively this is often due to the property alias that has been configured for the asset. This must match the property tag as it's written in the Modbus source, and in the case of OPC-UA, we must used a prepended '/'


# EDGE Setup 

  - java8
  - python3.7
  - docker
  - docker-compose

  `sudo apt install gnupg2 pass`

## Commands to verify data at edge

  `aws iotsitewise get-asset-property-value --asset-id ... --property-id ... --endpoint-url https://localhost:443 --no-verify-ssl`

  `sudo docker logs iotsitewiseedgeapplicationdeployment_property-state-service_1`
