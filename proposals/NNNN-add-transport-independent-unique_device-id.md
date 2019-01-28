# Add transport independent unique device ID

* Proposal: [SDL-NNNN](NNNN-add-transport-independent-unique_device-id.md)
* Author: [Andriy Byzhynar](https://github.com/AByzhynar)
* Status: **Waiting for review**
* Impacted Platforms: [Core / iOS / Android / RPC]

## Introduction

This proposal is to add a new unique device id parameter independent from transport type to `RegisterAppInterface` request.  
This `device_id` must be the same for all transports types of one physical device.

## Motivation
Currently, when application registers with SDL - SDL receives all app related data in `RegisterAppInterface` request and generates unique device id based on transport used by device.  
As SDL supports the following transport types: Bluetooth, USB-AOA, and TCP (WiFi) - SDL generates different `unique` device ids for each transport inspite of it is one physical device.

- For Bluetooth: device id will be based on bluetooth MAC-ADDRESS
- For WiFi: device id will be based on IP address
- For USB: it will be based on internal usb serial number of device.

Therefore when new application tries to register - it is impossible for SDL to distinguish  
if this new application tries to register from different physical device  
or just from differrent transport from the same device.

Having unique device id independent from transport type (the same for all transports of one physical device)
will give the following benefits:
- It will allow SDL clearly and definitely determine if app tries to register within the same device.
- SDL can apply application policies more effectively using device permissions as well.
- SDL can allow multiple application registration from different devices.


## Proposed solution
The solution is to add a new parameter `device_id` to `RegisterAppInterface` request.
Mobile API update:
```xml
<function name="RegisterAppInterface" functionID="RegisterAppInterfaceID" messagetype="request" since="1.0">
        <description>
            Establishes an interface with a mobile application.
            Before registerAppInterface no other commands will be accepted/executed.
        </description>

+       <param name="device_id" type="String" maxlength="100" mandatory="true">
+           <description>Unique device identifier</description>
+       </param>
```

## Potential downsides

This change will require mobile app manufacturers to update their applications.

## Impact on existing code

This is a major version change.

## Alternatives considered

1) `RegisterAppInterface` request already has convenient parameter `deviceInfo`
 which can be considered as alternative place for `device_id` parameter
The problem is that `device_id` parameter must be mandatory but
the entire `deviceInfo` is optional - so this alternative would require to
change `deviceInfo` presence from `optional` to `mandatory`

2) Adding unique `device_id` to bson instead of extending Mobile API.
This will allow SDL to get information about device identifier even before
application registration.

