# UriBeacon Configuration Service 

***Revision v2r0***

The material contained on this page is informative only and subject to change. 

## 1 Introduction

The UriBeacon Config Service allows setting UriBeacon fields and transmission characteristics. This information includes the following:

*   Lock code
*   Uri
*   Flags
*   Transmit Power
*   Duty Cycle

This document is formatted according to the Bluetooth 
[service](https://developer.bluetooth.org/TechnologyOverview/Pages/ANS.aspx) and 
[profile](https://developer.bluetooth.org/gatt/profiles/Pages/ProfileViewer.aspx?u=org.bluetooth.profile.blood_pressure.xml) formatting styles.


### 1.1 Service Dependencies

This service is not dependent upon any other [services](https://developer.bluetooth.org/gatt/services/Pages/ServicesHome.aspx).

### 1.2 Transport Dependencies

|Transport   | Supported |
|:-----------|-----------|
| Classic	| false	 |
| Low Energy | true	  |

### 1.3 Error Codes

| Code | Description  |
|:-----|:-------------|
| 0xA0 | The beacon is not writable. |


## 2 Service Declaration

The assigned number for `<<UriBeacon Config Service>>` is

        ee0c2080-8786-40ba-ab96-99b91ac981d8

## 3 Service Characteristics


| Characteristic     | Ref.     | Requirement  | 
|:-------------------|:-----|:--------------------|
| Lock               | [3.1](#31-lock) | Mandatory| 
| Unlock             | [3.2](#32-unlock) | Mandatory|
| Uri Data           | [3.3](#33-uri-data) | Mandatory|
| Uri Flags          | [3.4](#34-flags)  | Mandatory|
| Advertised Tx Power Level | [3.5](#35-advertised-tx-power-level)  | Mandatory|
| Tx Power Mode      | [3.6](#36-tx-power-mode) | Mandatory |
| Beacon Period      | [3.7](#37-beacon-period)| Mandatory|
| Reset              | [3.8](#38-reset) | Mandatory|

||<sup>Broadcast</sup>|<sup>Read</sup>|<sup>Write without Response</sup>|<sup>Write</sup>|<sup>Notify</sup>|<sup>Indicate</sup>|<sup>Signed Write</sup>|<sup>Reliable Write</sup>|<sup>Writable Auxiliaries</sup>|
|-------------------------:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
|Lock                      |X|M|X|M\*|X|X|X|X|X|
|Unlock                    |X|X|X|M  |X|X|X|X|X|
|Uri Data                  |X|M|X|M\*|X|X|X|X|X|
|Advertised Tx Power Level |X|M|X|M\*|X|X|X|X|X|
|Tx Power Mode             |X|M|X|M\*|X|X|X|X|X|
|Beacon Period             |X|M|X|M\*|X|X|X|X|X|
|Reset                     |X|X|M\*|X|X|X|X|X|X|

\* Must be in unlock state.

### 3.1 Lock

| Name | Lock |
|:-----|:----------|
| UUID  | ee0c<b>2081</b>-8786-40ba-ab96-99b91ac981d8|
|  Description| Locks the beacon or reads the lock state. |
|  Type | uint128 (write) boolean (read) |
|  Lock State | Must be unlocked. Will be locked after successful write.|

Read returns true if the device is locked.

### 3.2 Unlock

| Name | Unlock |
|:-----|:----------|
| UUID  | ee0c<b>2082</b>-8786-40ba-ab96-99b91ac981d8|
|  Description| Unlocks the beacon. |
|  Type | uint128 |
|  Lock State | Will be unlocked after successful write.|

If the beacon is unlocked then the write will return success regardless of the
contents of the parameter.

### 3.3 Uri Data

| Name | Uri Data |
|:-----|:----------|
| UUID  | ee0c<b>2083</b>-8786-40ba-ab96-99b91ac981d8|
|  Description| Reads/writes the Uri. |
|  Type | uint8[] |
|  Lock State | For write, must be unlocked.|

The Uri Data characteristic is a variable length structure. The first byte contains the [Uri Scheme Prefix](https://github.com/google/uribeacon/tree/master/specification#uribeacon-uri-scheme-prefix).
The remaining bytes contain either [urn:uuid encoding](https://github.com/google/uribeacon/tree/master/specification#uribeacon-urnuuid-encoding) or  [HTTP URL encoding](https://github.com/google/uribeacon/tree/master/specification#uribeacon-http-url-encoding).

### 3.4 Flags

| Name | Flags |
|:-----|:----------|
| UUID  | ee0c<b>2084</b>-8786-40ba-ab96-99b91ac981d8|
|  Description| Reads/writes the flags. |
|  Type | uint8 |
|  Lock State | For write, must be unlocked.|

The Flags characteristic is a sinlge unsigned byte value containing the
[UriBeacon Flags](https://github.com/google/uribeacon/tree/master/specification#uribeacon-flags).

### 3.5 Advertised TX Power Level

| Name | Advertised TX Power Level |
|:-----|:----------|
| UUID  | ee0c<b>2085</b>-8786-40ba-ab96-99b91ac981d8|
|  Description| Reads/writes the Advertised Power Level. |
|  Type | uint8[] |
|  Lock State | For write, must be unlocked.|

This characteristic is a variable length array of byte pairs. The first byte is [TX Power Mode](#36-tx-power-mode). The second byte is the TX Power Level, in dBm, to be included in
the [UriBeacon TX Power](https://github.com/google/uribeacon/tree/master/specification#uribeacon-tx-power-level) field of the advertisement when that mode is active.

The TX Power Level represents a transmit power level in dBm and has values from -100 dBm to +20 dBm to a resolution of 1 dBm.

### 3.6 TX Power Mode

| Name | TX Power Mode |
|:-----|:----------|
| UUID  | ee0c<b>2086</b>-8786-40ba-ab96-99b91ac981d8|
|  Description| Reads/writes the TX Power Mode. |
|  Type | uint8 |
|  Lock State | For write, must be unlocked.|

Sets the transmission power mode to one of:

| Power Level | Value |
|:-----|:----------|
| TX_POWER_MODE_HIGH | 3 |
| TX_POWER_MODE_MEDIUM | 2 | 
| TX_POWER_MODE_LOW | 1 | 
| TX_POWER_MODE_LOWEST | 0 |


### 3.7 Beacon Period

| Name | Beacon Period |
|:-----|:----------|
| UUID  | ee0c<b>2087</b>-8786-40ba-ab96-99b91ac981d8|
|  Description| The period in milliseconds that a UriBeacon packet is transmitted. |
|  Type | uint8  |
|  Lock State | For write, must be unlocked.|

The period in milliseconds that a UriBeacon packet is transmitted. A value of zero disables UriBeacon transmissions.

### 3.8 Reset

| Name | Reset |
|:-----|:----------|
| UUID  | ee0c<b>2088</b>-8786-40ba-ab96-99b91ac981d8|
|  Description| Reset to default values. |
|  Type | boolean  |
|  Lock State | Must be unlocked.|

Writing a non-zero value to this characteristic will set all characteristics to their initial values and reset the GATT connection:

| Characteristic | Default Value |
|:---------------|:--------------|
| Uri Data | None |
| Uri Flags | 0 |
| TX Power Mode | TX_POWER_MODE_LOW |
| Beacon Period | 0 (disabled) |
| Lock | 00000000-00000-0000-0000-000000000000 |

