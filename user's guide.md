# OpenBMC User's Guide

##  1   OpenBMC REST API 

The primary management interface for OpenBMC is REST. This document provides some basic structure and usage
examples for the REST interface.
The schema for the rest interface is directly defined by the OpenBMC D-Bus structure. Therefore, the objects, at-
tributes and methods closely map to those in the D-Bus schema.
For a quick explanation of HTTP verbs and how they relate to a RESTful API, see http://www.restapitutorial.com/lessons/httpmethods.html.

### 1.1 Logging in

Before you can do anything you first need to log in: 

  curl -c cjar -k -X POST -H "Content-Type: application/json" \
          -d '{"data": [ "root", "0penBmc" ] }' \
          https://${bmc}/login

This performs a login using the provided username and password, and stores the newly-acquired authentication credentials in the curl “Cookie jar” file (named cjar in this example). We will use this file (with the -b argument) for future requests.

To log out:

 curl -c cjar -b cjar -k -X POST -H "Content-Type: application/json" \
        -d '{"data": [ ] }' \
        https://${bmc}/logout

(or just delete your cookie jar file)

### 1.2 HTTP GET operations & URL structure

There are a few conventions on the URL structure of the OpenBMC rest interface. They are:
• To query the attributes of an object, perform a GET request on the object name, with no trailing slash. For
example:
$ curl -b cjar -k https://${bmc}/xyz/openbmc_project/inventory/system
{
"data": {
    "AssetTag": "",
    "BuildDate": "",
    "Cached": true,
    "FieldReplaceable": true,
    "Manufacturer": "",
    "Model": "",
    "PartNumber": "",
    "Present": true,
    "PrettyName": "",
    "SerialNumber": ""

  },
  "message": "200 OK",
  "status": "ok"
}

• To query a single attribute, use the attr/<name> path. Using the system object from above, we can query just the Name value:
$ curl -b cjar -k https://${bmc}/xyz/openbmc_project/inventory/system/attr/Cached

{
  "data": true,
  "message": "200 OK",
  "status": "ok"
}

• When a path has a trailing-slash, the response will list the sub objects of the URL. For example, using the same object path as above, but adding a slash:
$ curl -b cjar -k https://${bmc}/xyz/openbmc_project/

{
  "data": [
    "/xyz/openbmc_project/Chassis",
    "/xyz/openbmc_project/Hiomapd",
    "/xyz/openbmc_project/Ipmi",
    "/xyz/openbmc_project/certs",
    "/xyz/openbmc_project/console",
    "/xyz/openbmc_project/control",
    "/xyz/openbmc_project/dump",
    "/xyz/openbmc_project/events",
    "/xyz/openbmc_project/inventory",
    "/xyz/openbmc_project/ipmi",
    "/xyz/openbmc_project/led",
    "/xyz/openbmc_project/logging",
    "/xyz/openbmc_project/network",
    "/xyz/openbmc_project/object_mapper",
    "/xyz/openbmc_project/sensors",
    "/xyz/openbmc_project/software",
    "/xyz/openbmc_project/state",
    "/xyz/openbmc_project/time",
    "/xyz/openbmc_project/user"
  ],
  "message": "200 OK",
  "status": "ok"
}

This shows that there are 11 children of the openbmc_project/ object: dump, software, control, network, logging, sensors, inventory, user, time, led, and state. This can be used with the base REST URL (ie., http://${bmc}/), to discover all objects in the hierarchy.
• Performing the same query with /list will list the child objects *recursively*.
$ curl -b cjar -k https://${bmc}/xyz/openbmc_project/network/list

{
  "data": [
    "/xyz/openbmc_project/network/config",
    "/xyz/openbmc_project/network/config/dhcp",
    "/xyz/openbmc_project/network/eth0",
    "/xyz/openbmc_project/network/eth0/ipv4",
    "/xyz/openbmc_project/network/eth0/ipv4/3b9faa36",
    "/xyz/openbmc_project/network/eth0/ipv6",
    "/xyz/openbmc_project/network/eth0/ipv6/ff81b6d6",
    "/xyz/openbmc_project/network/eth1",
    "/xyz/openbmc_project/network/eth1/ipv4",
    "/xyz/openbmc_project/network/eth1/ipv4/3b9faa36",
    "/xyz/openbmc_project/network/eth1/ipv4/66e63348",
    "/xyz/openbmc_project/network/eth1/ipv6",
    "/xyz/openbmc_project/network/eth1/ipv6/ff81b6d6",
    "/xyz/openbmc_project/network/host0",
    "/xyz/openbmc_project/network/host0/intf",
    "/xyz/openbmc_project/network/host0/intf/addr",
    "/xyz/openbmc_project/network/sit0",
    "/xyz/openbmc_project/network/snmp",
    "/xyz/openbmc_project/network/snmp/manager"
  ],
  "message": "200 OK",
  "status": "ok"
}

• Adding /enumerate instead of /list will also include the attributes of the listed objects.
$ curl -b cjar -k https://${bmc}/xyz/openbmc_project/time/enumerate

{
  "data": {
    "/xyz/openbmc_project/time/bmc": {
      "Elapsed": 1563209492098739
    },
    "/xyz/openbmc_project/time/host": {
      "Elapsed": 1563209492101678
    },
    "/xyz/openbmc_project/time/owner": {
      "TimeOwner": "xyz.openbmc_project.Time.Owner.Owners.BMC"
    },
    "/xyz/openbmc_project/time/sync_method": {
      "TimeSyncMethod": "xyz.openbmc_project.Time.Synchronization.Method.NTP"
    }
  },
  "message": "200 OK",
  "status": "ok"
}

### 1.3 HTTP PUT operations

PUT operations are for updating an existing resource (an object or property), or for creating a new resource when the client already knows where to put it. These require a json formatted payload. To get an example of what that looks like:

curl -b cjar -k \
       https://${bmc}/xyz/openbmc_project/state/host0 > host.json

$ cat host.json

{
  "data": {
    "AttemptsLeft": 3,
    "BootProgress": "xyz.openbmc_project.State.Boot.Progress.ProgressStages.Unspecified",
    "CurrentHostState": "xyz.openbmc_project.State.Host.HostState.Off",
    "OperatingSystemState": "xyz.openbmc_project.State.OperatingSystem.Status.OSStatus.Inactive",
    "RequestedHostTransition": "xyz.openbmc_project.State.Host.Transition.Off"
  },
  "message": "200 OK",
  "status": "ok"
}

or

curl -b cjar -k \
      https://${bmc}/xyz/openbmc_project/state/host0/attr/RequestedHostTransition > requested_host.json

$ cat requested_host.json

{
  "data": "xyz.openbmc_project.State.Host.Transition.Off",
  "message": "200 OK",
  "status": "ok"
}

When turning around and sending these as requests, delete the message and status properties.
To make curl use the correct content type header use the -H option to specify that we're sending JSON data:
curl -b cjar -k -H "Content-Type: application/json" -X PUT -d <json> <url>
A PUT operation on an object requires a complete object. For partial updates there is PATCH but that is not imple-mented yet. As a workaround individual attributes are PUTable.
For example, make changes to the requested_host.json file and do a PUT (upload):

$ cat requested_host.json

{
  "data": "xyz.openbmc_project.State.Host.Transition.Off",
  "message": "200 OK",
  "status": "ok"
}

​                     

curl -b cjar -k -H "Content-Type: application/json" -X PUT -T requested_host.json \
        https://${bmc}/xyz/openbmc_project/state/host0/attr/RequestedHosition

Alternatively specify the json inline with -d:
curl -b cjar -k -H "Content-Type: application/json" -X PUT -d '{"data":\                             "xyz.openbmc_project.State.Host.Transition.On"}' \ https://${bmc}/xyz/openbmc_project/state/host0/attr/RequestedHostTransition
When using ‘-d’ just remember that json requires quoting.

### 1.4 HTTP POST operations{待测试}

POST operations are for calling methods, but also for creating new resources when the client doesn’t know where to put it. OpenBMC does not support creating new resources via REST so any attempt to create a new resource will result in a HTTP 403 (Forbidden).
These also require a json formatted payload.
To invoke a method with parameters (Downloading a Tar image via TFTP):

??????????????????????????????????????????????????????????????????????????????????????????????

curl -b cjar -k -X POST -H "Content-Type: application/json" \
-d '{"data": ["<Image Tarball>", "<TFTP Server>"]}' \
https://${bmc}/xyz/openbmc_project/software/action/DownloadViaTFTP

（最后测试）

To invoke a method without parameters (Factory Reset of BMC and Host):（未尝试）

curl -b cjar -k -H 'Content-Type: application/json' -X POST \
-d '{"data":[]}' \
https://${bmc}/xyz/openbmc_project/software/action/Reset

### 1.5 HTTP DELETE operations

DELETE operations are for removing instances. Only D-Bus objects (instances) can be removed. If the underly-
ing D-Bus object implements the xyz.openbmc_project.Object.Delete interface the REST server will call it. If
xyz.openbmc_project.Object.Delete is not implemented, the REST server will return a HTTP 403 (Forbidden)
error.
For example, to delete a event record :

Display logging entries:

curl -b cjar -k -H "Content-Type: application/json" -X GET \ https://${bmc}/xyz/openbmc_project/logging/entry/enumerate -u root:0penBmc
{
  "data": {
    "/xyz/openbmc_project/logging/entry/1": {
      "AdditionalData": [
        "_PID=185"
      ],
      "Id": 1,
      "Message": "xyz.openbmc_project.Common.Error.InternalFailure",
      "Purpose": "xyz.openbmc_project.Software.Version.VersionPurpose.BMC",
      "Resolved": false,
      "Severity": "xyz.openbmc_project.Logging.Entry.Level.Error",
      "Timestamp": 1563191306359,
      "Version": "2.8.0-dev-132-gd1c1b74-dirty",
      "associations": []
    }
  },
  "message": "200 OK",
  "status": "ok"
}

Then delete the event record with ID 1:

curl -b cjar -k -H "Content-Type: application/json" -X DELETE\ https://${bmc}/xyz/openbmc_project/logging/entry/1 -u root:0penBmc
{
  "data": null,
  "message": "200 OK",
  "status": "ok"
}

### 1.6 Uploading images    {{最后测试}}

It is possible to upload software upgrade images (for example to upgrade the BMC or host software) via REST. The content-type should be set to "application/octet-stream".
For example, to upload an image:

curl -c cjar -b cjar -k -H "Content-Type: application/octet-stream" \
-X POST -T <file_to_upload> https://${bmc}/upload/image

In above example, the filename on the BMC will be chosen by the REST server.
It is possible for the user to choose the uploaded file's remote name:

curl -c cjar -b cjar -k -H "Content-Type: application/octet-stream" \
-X PUT -T foo https://${bmc}/upload/image/bar

In above example, the file "foo" will be saved with the name "bar" on the BMC.
The operation will either return the version id (hash) of the uploaded file on success:

{
"data": "ffdaab9b",
"message": "200 OK",
"status": "ok"
}

or an error message:

{
"data": {
"description": "Version already exists or failed to be extracted"
},
"message": "400 Bad Request",
"status": "error"
}

### 1.7 Event subscription protocol(缺失)

??命令不全

## 2 Host Management with OpenBMC

This document describes the host-management interfaces of the OpenBMC object structure, accessible over REST.

### 2.1 Inventory

The system inventory structure is under the /xyz/openbmc_project/inventory hierarchy.
In OpenBMC the inventory is represented as a path which is hierarchical to the physical system topology. Items in the inventory are referred to as inventory items and are not necessarily FRUs (field-replaceable units). If the system contains one chassis, a motherboard, and a CPU on the motherboard, then the path to that inventory item would be: 

inventory/system/chassis0/motherboard0/cpu0

The properties associated with an inventory item are specific to that item. Some common properties are:
• Version: A code version associated with this item.
• Present: Indicates whether this item is present in the system (True/False).
• Functional: Indicates whether this item is functioning in the system (True/False).
The usual list and enumerate REST queries allow the system inventory structure to be accessed. For example, to enumerate all inventory items and their properties:
curl -b cjar -k https://${bmc}/xyz/openbmc_project/inventory/enumerate
To list the properties of one item:
curl -b cjar -k https://${bmc}/xyz/openbmc_project/inventory/system/chassis/motherboard

### 2.2 Sensors

The system sensor structure is under the /xyz/openbmc_project/sensors hierarchy.
This interface allows monitoring of system attributes like temperature or altitude, and are represented similar to the inventory, by object paths under the top-level sensors object name. The path categorizes the sensor and shows what the sensor represents, but does not necessarily represent the physical topology of the system.
For example, all temperature sensors are under sensors/temperature. CPU temperature sensors would be sensors/temperature/cpu
These are some common properties:

Value: Current value of the sensor
Unit: Unit of the value and “Critical” and “Warning” values
Scale: The scale of the value and “Critical” and “Warning” values
CriticalHigh & CriticalLow: Sensor device upper/lower critical threshold bound
CriticalAlarmHigh & CriticalAlarmLow: True if the sensor has exceeded the critical threshold bound
WarningHigh & WarningLow: Sensor device upper/lower warning threshold bound
WarningAlarmHigh & WarningAlarmLow: True if the sensor has exceeded the warning threshold bound

A temperature sensor might look like:

curl -b cjar -k https://${bmc}/xyz/openbmc_project/sensors/temperature/pcie











