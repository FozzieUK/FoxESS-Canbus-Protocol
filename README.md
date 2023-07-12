# FoxESS-Canbus-Protocol
FoxESS Canbus Protocol between BMS and H series inverter (extended V1 supporting cell mV and temps)

Can bus @ 500k - all Extended ID, little endian

For the BMS to HV2600 Battery RS485 protocol, see the following repo  [FoxESS BMS Battery RS485](https://github.com/FozzieUK/FoxESS-BMS-to-Battery-Protocol-RS485)

## Basic Protocol

When starting up, the Foxess battery storage system unlike other systems does not have a formal adoption process, the inverter 'polls' the BMS every 1 second and if the BMS responds with a valid message then it is assumed available.

The BMS responds to the inverter polls, but only after it has sent the inverter one copy of the BMS data and all pack data will it close it's contactor and become operational.

The inverter sends a number of different requests with specific time intervals and the BMS responds with the data requested.

## Frames

### Received frame 

> 0x1871 [0x01,  0x00,  0x01,  0x00,  0x00,  0x00,  0x00,  0x00]   

This is sent by the inverter every 1 second and appears to be the poll for bms_send_pack_statistics.

### Response frames (complete pack statistics)

| ID     | b0              | b1            | b2                | b3                | b4               | b5            | b6               | b7              |
| ------ | --------------- | ------------- | ----------------- | ----------------- | ---------------- | ------------- | ---------------- | --------------- |
| 0x1872 | batt_volt_max   | batt_volt_max | batt_volt_min     | batt_volt_min     | max_charge_A     | max_charge_A  | max_discharge_A  | max_discharge_A |
| 0x1873 | pack_volt_now   | pack_volt_now | pack_current sense| pack_current sense| pack_SoC         | 0x00          | pack_kwh_remain  | pack_kwh_remain |
| 0x1874 | pack_temp_max   | pack_temp_max | pack_temp_min     | pack_temp_min     | pack_mv_max      | pack_mV_max   | pack_mV_min      | pack_mV_min     |
| 0x1875 | BMS_temp        | BMS_temp      | pack_state(note5) | number_packs      | 0x01 contact     | 0x00          | cycle_count      | cycle_count     |
| 0x1876 | 0x01 (Note6)    | 0x00          | cells_volts_max   | cells_volts_max   | 0x00             | 0x00          | cells_volts_min  | cells_volts_min |
| 0x1877 | packError(note7)| 0x00          | 0x00              | 0x00              | batt type(note 8)| 0x00          | ** See Note 3    | pack_id 0x10    |
| 0x1878 | AC_volts_max    | AC_volts_max  | 0x00              | 0x00              | wh_total         | wh_total      | wh_total         | wh_total        |
| 0x1879 | ErrorCode(note2)| FLAGS (Note1) | 0x00              | 0x00              | 0x00             | 0x00          | 0x00             | 0x00            |

** Note1: FLAGS are binary bits 

bits 0,1 & 2 are status, bit 3 set if discharging, bit 4 set if charging, bit 5 is 1 if on-line, bit 7 so far always 0
| bit        | Off-line |  On-line  |   On-line   | Error      |         Notes             |
| ---------- | -------- | --------- | ------------| ---------- | ------------------------- |
|            |          |  (charge) | (discharge) |            |  Status                   |
| bit0       |    0     |     1     |      1      |     0      |  02=Error, 03=ok_Discharge|
| bit1       |    1     |     0     |      1      |     1      |  04=idle?                 |
| bit2       |    1     |     1     |      0      |     0      |  05=ok_Charge, 06=offline |
| bit3       |    1     |     0     |      1      |     1      |     discharge flag        |
| bit4       |    1     |     1     |      0      |     1      |     charge flag           |
| bit5       |    0     |     1     |      1      |     0      |     operational           |
| bit6       |    0     |     0     |      0      |     1      |     active error          |
| bit7       |    0     |     0     |      0      |     0      |                           |


** Note2: Error Code are binary bits

don't now what all states mean yet, x03 generated when forced pack comms error and contactor dropped out - inverter reports bat volt volt.

| bit        |  On-line  |   Error  |         Notes             |
| ---------- | --------- | ---------| ------------------------- |
| bit0       |     0     |     1    |  03=Pack Comms Error      |
| bit1       |     0     |     1    |                           |
| bit2       |     0     |     0    |                           |
| bit3       |     0     |     0    |                           |
| bit4       |     0     |     0    |                           |
| bit5       |     0     |     0    |                           |
| bit6       |     0     |     0    |                           |
| bit7       |     0     |     0    |                           |




** Note3: 0x1877 b6 this is firmware version, the top nibble is major version, bottom nibble is minor version however BMS and packs represent it differently
i.e. 

for the packs (b7 =10,20,30,40,50,60,70,80) then b6 0x1F = 0001 1111, version is v1.15, and if b6 was 0x20 = 0010 0000 then = v2.0, 

for the BMS (b7=01) then convert b6 hex to decimal 0x12 = 018 , and if b6 was 0x14 then = 020
     
** Note4: 0x1876 cell volts min / max - these appear to be used by the inverter to populate Home Assistant sensors (not 0x1874 b4-b7 which stay at a constant 3,300mV hi/lo)

** Note5: 0x1875 b2 contains status for operational packs (responding) in binary so 01111111 is pack 8 not operational, 11101101 is pack 5 & 2 not operational

** Note6: 0x1876 b0 bit 0 appears to be 1 when at maxsoc and BMS says charge is not allowed - when at 0 indicates charge is possible - addn'l note there is something more to it than this, it's not as straight forward - needs more testing to find what sets/unsets bit0 of byte0

** Note7: 0x1877 b0 appears to be an error code, 0x02 when pack is in error.

** Note8: 0x1877 b4 is battery type, where 0x82 is HV2600 V1, 0x83 is ECS4100  v1, 0x84 is HV2600 V2

### Screenshot
![Screenshot](https://github.com/FozzieUK/FoxESS-Canbus-Protocol/assets/113460294/fd08a1b5-706c-4a09-b4d5-318ee1627928)


### Received frames

> 0x1871 [0x02,  0x00,  0x01,  0x00,  0x01,  0x00,  0x00,  0x00] 

This is sent 0.5 seconds after the bms_send_pack_statistics appears to be a bms_stop_sending (or an 'ack'), it **does not** elicit a BMS response


### Received frames

> 0x1871 [0x05,  0x00,  0x01,  0x00,  0x00,  0x00,  0x00,  0x00]

This is only sent after BMS communications is established (a valid response to a poll request) and occurs 0.5 seconds after the bms_stop_sending message, the BMS responds with BMS and each battery pack serial numbers.


### Response frames (pack serial numbers)

| ID     | b0                | b1                | b2                | b3                | b4           | b5           | b6               | b7              |
| ------ | ----------------- | ----------------- | ----------------- | ----------------- | ------------ | ------------ | ---------------- | --------------- |
| 0x1881 | pack_id (0=BMS)   | BMS_SN (ASC)      | BMS_SN (ASC)      | BMS_SN (ASC)      | BMS_SN (ASC) | BMS_SN (ASC) | BMS_SN (ASC)     | BMS_SN (ASC)    |
| 0x1882 | pack_id (0=BMS)   | BMS_SN (ASC)      | BMS_SN (ASC)      | BMS_SN (ASC)      | BMS_SN (ASC) | BMS_SN (ASC) | BMS_SN (ASC)     | BMS_SN (ASC)    |
| 0x1883 | pack_id (0=BMS)   | BMS_SN (ASC)      | BMS_SN (ASC)      | BMS_SN (ASC)      | BMS_SN (ASC) | BMS_SN (ASC) | BMS_SN (ASC)     | BMS_SN (ASC)    |
| 0x1881 | pack_id (1=1)     | Pack1 SN (ASC)    | Pack1 SN (ASC)    | Pack1 SN (ASC)    | Pack1 SN(ASC)| Pack1 SN(ASC)| Pack1 SN(ASC)    | Pack1 SN(ASC)   |
| 0x1882 | pack_id (1=1)     | Pack1 SN (ASC)    | Pack1 SN (ASC)    | Pack1 SN (ASC)    | Pack1 SN(ASC)| Pack1 SN(ASC)| Pack1 SN(ASC)    | Pack1 SN(ASC)   |
| 0x1883 | pack_id (1=1)     | Pack1 SN (ASC)    | Pack1 SN (ASC)    | Pack1 SN (ASC)    | Pack1 SN(ASC)| Pack1 SN(ASC)| Pack1 SN(ASC)    | Pack1 SN(ASC)   |

repeats up [num_packs]

| ID     | b0                | b1                | b2                | b3                | b4           | b5           | b6               | b7              |
| ------ | ----------------- | ----------------- | ----------------- | ----------------- | ------------ | ------------ | ---------------- | --------------- |
| 0x1881 | pack_id (8=1)     | Pack8 SN (ASC)    | Pack8 SN (ASC)    | Pack8 SN (ASC)    | Pack8 SN(ASC)| Pack8 SN(ASC)| Pack8 SN(ASC)    | Pack8 SN(ASC)   |
| 0x1882 | pack_id (8=1)     | Pack8 SN (ASC)    | Pack8 SN (ASC)    | Pack8 SN (ASC)    | Pack8 SN(ASC)| Pack8 SN(ASC)| Pack8 SN(ASC)    | Pack8 SN(ASC)   |
| 0x1883 | pack_id (8=1)     | Pack8 SN (ASC)    | Pack8 SN (ASC)    | Pack8 SN (ASC)    | Pack8 SN(ASC)| Pack8 SN(ASC)| Pack8 SN(ASC)    | Pack8 SN(ASC)   |


### Received frame 

> 0x1871 [0x01,  0x00,  0x01,  0x00,  0x01,  0x00,  0x00,  0x00]

This is only sent after BMS communications is established (a valid response to a poll request) and occurs 6 seconds, the BMS responds with the individual battery pack status.


### Response frames (individual pack data)

| ID     | b0                | b1                | b2                | b3                | b4           | b5           | b6               | b7              |
| ------ | ----------------- | ----------------- | ----------------- | ----------------- | ------------ | ------------ | ---------------- | --------------- |
| 0x0C05 | Pack_1_Current    | Pack_1_Current    | Pack_1_avg_hitemp | Pack_1_avg_lotemp | pack_1_SoC   |b4-7chg/dis?  | pack_1_volts     | pack_1_volts    |
| 0x0C06 | Pack_2_Current    | Pack_2_Current    | Pack_2_avg_hitemp | Pack_2_avg_lotemp | pack_2_SoC   |b4-7chg/dis?  | pack_2_volts     | pack_2_volts    |
| 0x0C07 | Pack_3_Current    | Pack_3_Current    | Pack_3_avg_hitemp | Pack_3_avg_lotemp | pack_3_SoC   |b4-7chg/dis?  | pack_3_volts     | pack_3_volts    |
| 0x0C08 | Pack_4_Current    | Pack_4_Current    | Pack_4_avg_hitemp | Pack_4_avg_lotemp | pack_4_SoC   |b4-7chg/dis?  | pack_4_volts     | pack_4_volts    |
| 0x0C09 | Pack_5_Current    | Pack_5_Current    | Pack_5_avg_hitemp | Pack_5_avg_lotemp | pack_5_SoC   |b4-7chg/dis?  | pack_5_volts     | pack_5_volts    |
| 0x0C0A | Pack_6_Current    | Pack_6_Current    | Pack_6_avg_hitemp | Pack_6_avg_lotemp | pack_6_SoC   |b4-7chg/dis?  | pack_6_volts     | pack_6_volts    |
| 0x0C0B | Pack_7_Current    | Pack_7_Current    | Pack_7_avg_hitemp | Pack_7_avg_lotemp | pack_7_SoC   |b4-7chg/dis?  | pack_7_volts     | pack_7_volts    |
| 0x0C0C | Pack_8_Current    | Pack_8_Current    | Pack_8_avg_hitemp | Pack_8_avg_lotemp | pack_8_SoC   |b4-7chg/dis?  | pack_8_volts     | pack_8_volts    |

### Screenshot
![Screenshot](https://github.com/FozzieUK/FoxESS-Canbus-Protocol/blob/main/packinf.jpg)


### Received frame 

> 0x1871 [0x01,  0x00,  0x01,  0x00,  0x02,  0x00,  0x00,  0x00]
> 
> 0x1871 [0x01,  0x00,  0x01,  0x00,  0x04,  0x00,  0x00,  0x00]

These messages are only sent after BMS communications is established (a valid response to a poll request), they are sent together as a pair (100mS apart). They are sent by the inverter every 3 seconds and is the poll message for the bms to send extended data for individual cell data - bms_send_pack_cell_volts and bms_send_pack_cell_temps.

### Response frames (cell mv values)

| ID     | b0                | b1                | b2                | b3                | b4           | b5           | b6               | b7              |
| ------ | ----------------- | ----------------- | ----------------- | ----------------- | ------------ | ------------ | ---------------- | --------------- |
| 0x0C1D | cell_1_mv         | cell_1_mv         | cell_2_mv         | cell_2_mv         | cell_3_mv    | cell_3_mv    | cell_4_mv        | cell_4_mv       |
| 0x0C21 | cell_5_mv         | cell_5_mv         | cell_6_mv         | cell_6_mv         | cell_7_mv    | cell_7_mv    | cell_8_mv        | cell_8_mv       |
| 0x0C25 | cell_9_mv         | cell_9_mv         | cell_10_mv        | cell_10_mv        | cell_11_mv   | cell_11_mv   | cell_12_mv       | cell_12_mv      |
| 0x0C29 | cell_13_mv        | cell_13_mv        | cell_14_mv        | cell_14_mv        | cell_15_mv   | cell_15_mv   | cell_16_mv       | cell_16_mv      |
| 0x0C2D | cell_17_mv        | cell_17_mv        | cell_18_mv        | cell_18_mv        | cell_19_mv   | cell_19_mv   | cell_20_mv       | cell_20_mv      |
| 0x0C31 | cell_21_mv        | cell_21_mv        | cell_22_mv        | cell_22_mv        | cell_23_mv   | cell_23_mv   | cell_24_mv       | cell_24_mv      |

repeats up [144_cells]

| ID     | b0                | b1                | b2                | b3                | b4           | b5           | b6               | b7              |
| ------ | ----------------- | ----------------- | ----------------- | ----------------- | ------------ | ------------ | ---------------- | --------------- |
| 0x0CA5 | cell_133_mv        | cell_133_mv      | cell_134_mv       | cell_134_mv       | cell_135_mv  | cell_135_mv  | cell_136_mv      | cell_136_mv     |
| 0x0CA9 | cell_137_mv        | cell_137_mv      | cell_138_mv       | cell_138_mv       | cell_139_mv  | cell_139_mv  | cell_140_mv      | cell_140_mv     |
| 0x0CAD | cell_141_mv        | cell_141_mv      | cell_142_mv       | cell_142_mv       | cell_143_mv  | cell_143_mv  | cell_144_mv      | cell_144_mv     |

then immediately followed by cell temps (decimal 50 offset)

| ID     | b0                | b1                | b2                | b3                | b4           | b5           | b6               | b7              |
| ------ | ----------------- | ----------------- | ----------------- | ----------------- | ------------ | ------------ | ---------------- | --------------- |
| 0x0D21 | cell_1_temp        | cell_2_temp      | cell_3_temp       | cell_4_temp       | cell_5_temp  | cell_6_temp  | cell_7_temp      | cell_8_temp     |
| 0x0D29 | cell_9_temp        | cell_10_temp     | cell_11_temp      | cell_12_temp      | cell_13_temp | cell_14_temp | cell_15_temp     | cell_16_temp    |
| 0x0D31 | cell_17_temp       | cell_18_temp     | cell_19_temp      | cell_20_temp      | cell_21_temp | cell_22_temp | cell_23_temp     | cell_24_temp    |
| 0x0D39 | cell_25_temp       | cell_26_temp     | cell_27_temp      | cell_28_temp      | cell_29_temp | cell_30_temp | cell_31_temp     | cell_32_temp    |
| 0x0D41 | cell_33_temp       | cell_34_temp     | cell_35_temp      | cell_36_temp      | cell_37_temp | cell_38_temp | cell_39_temp     | cell_40_temp    |
| 0x0D49 | cell_41_temp       | cell_42_temp     | cell_43_temp      | cell_44_temp      | cell_45_temp | cell_46_temp | cell_47_temp     | cell_48_temp    |
| 0x0D51 | cell_49_temp       | cell_50_temp     | cell_51_temp      | cell_52_temp      | cell_53_temp | cell_54_temp | cell_55_temp     | cell_56_temp    |
| 0x0D59 | cell_57_temp       | cell_58_temp     | cell_59_temp      | cell_60_temp      | cell_61_temp | cell_62_temp | cell_63_temp     | cell_64_temp    |

### Screenshots
![Screenshot](https://github.com/FozzieUK/FoxESS-Canbus-Protocol/blob/main/packmv.jpg)

![Screenshot](https://github.com/FozzieUK/FoxESS-Canbus-Protocol/blob/main/packtemp.jpg)


### Received frame 

> 0x1871 [0x03,  0x06,  0x17,  0x05,  0x09,  0x09,  0x28,  0x22]

This message is sent by the inverter every '6' seconds (0.5s after the pack serial numbers) and contains a timestamp in bytes 2-7 i.e. ```<YY>,<MM>,<DD>,<HH>,<mm>,<ss>```





.....................
  
.
.
.
.
.
```
**Disclaimer**: Any information on this wiki is informal advice, and is not supported nor endorsed by FoxESS.
There is no warranty expressed or implied: you take sole responsibility for everything you do with this information and your equipment.
There is no warranty to the accuracy of the content, nor any affiliation, or in any way a connection to FoxESS Co. Ltd
```
