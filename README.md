# FoxESS-Canbus-Protocol
FoxESS Canbus Protocol between BMS and H series inverter (extended V1 supporting cell mV and temps)

Can bus @ 500k - all Extended ID, little endian

## Frames

### Received frame 

> 0x1871 [0x01,  0x00,  0x01,  0x00,  0x00,  0x00,  0x00,  0x00]

### Response frames (complete pack statistics)

| ID     | b0                | b1                | b2                | b3                | b4           | b5           | b6               | b7              |
| ------ | ----------------- | ----------------- | ----------------- | ----------------- | ------------ | ------------ | ---------------- | --------------- |
| 0x1872 | slave_voltage_max | slave_voltage_max | slave_voltage_min | slave_voltage_min | charge_max   | charge_max   | discharge_max    | discharge_max   |
| 0x1873 | pack_voltage      | pack_voltage      | pack_current sense| pack_current sense| pack_SoC     | 0x00         | pack_kwh_remain  | pack_kwh_remain |
| 0x1874 | cells_temp_max    | cells_temp_max    | cells_temp_min    | cells_temp_min    | cells_mv_max | cells_mv_max | cells_mv_min     | cells_mv_min    |
| 0x1875 | int_temp          | pack_temp         | pack_temp         | 0x00              | 0x01 contact | 0x00         | cycle_count      | cycle_count     |
| 0x1876 | 0x01              | 0x00              | cells_volts_max   | cells_volts_max   | 0x00         | 0x00         | cells_volts_min  | cells_volts_min |
| 0x1877 | 0x00              | 0x00              | 0x00              | 0x00              | h/w version? | 0x00         | ** See Note 2    | pack_id 0x10    |
| 0x1878 | pack_voltage_max  | pack_voltage_max  | 0x00              | 0x00              | wh_total     | wh_total     | wh_total         | wh_total        |
| 0x1879 | 0x00              | ** See Note1       | 0x00              | 0x00              | 0x00         | 0x00         | 0x00             | 0x00            |

** Note1: bits 1 & 3 are set if pack discharging, bits 2 & 4 set if pack charging

** Note2: This is firmware version, the top nibble is major version, bottom nibble is minor version however BMS and packs represent it differently
i.e. 

for the packs (b7 =10,20,30,40,50,60,70,80) then 0x1F = 0001 1111, version is v1.15, 0x20 = 0010 0000 = v2.0, 

for the BMS (b7=01) then convert hex to decimal 0x12 = 018 , 0x14 = 020
     
** Note3: 0x1876 cell volts min / max - these appear to be used by the inverter to populate Home Assistant sensors (not 0x1874 which stays at a constant 3,300mV hi/lo)

### Screenshot
![Screenshot](https://github.com/FozzieUK/FoxESS-Canbus-Protocol/blob/main/bmspack.jpg)

### Received frames

> 0x1871 [0x02,  0x00,  0x01,  0x00,  0x01,  0x00,  0x00,  0x00]
> 
> 0x1871 [0x05,  0x00,  0x01,  0x00,  0x00,  0x00,  0x00,  0x00]


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

### Response frames (individual pack data)

| ID     | b0                | b1                | b2                | b3                | b4           | b5           | b6               | b7              |
| ------ | ----------------- | ----------------- | ----------------- | ----------------- | ------------ | ------------ | ---------------- | --------------- |
| 0x0C05 | Pack_1_Current    | Pack_1_Current    | ??                | ??                | pack_1_SoC   |b4-7chg/dis?  | pack_1_volts     | pack_1_volts    |
| 0x0C06 | Pack_2_Current    | Pack_2_Current    | ??                | ??                | pack_2_SoC   |b4-7chg/dis?  | pack_2_volts     | pack_2_volts    |
| 0x0C07 | Pack_3_Current    | Pack_3_Current    | ??                | ??                | pack_3_SoC   |b4-7chg/dis?  | pack_3_volts     | pack_3_volts    |
| 0x0C08 | Pack_4_Current    | Pack_4_Current    | ??                | ??                | pack_4_SoC   |b4-7chg/dis?  | pack_4_volts     | pack_4_volts    |
| 0x0C09 | Pack_5_Current    | Pack_5_Current    | ??                | ??                | pack_5_SoC   |b4-7chg/dis?  | pack_5_volts     | pack_5_volts    |
| 0x0C0A | Pack_6_Current    | Pack_6_Current    | ??                | ??                | pack_6_SoC   |b4-7chg/dis?  | pack_6_volts     | pack_6_volts    |
| 0x0C0B | Pack_7_Current    | Pack_7_Current    | ??                | ??                | pack_7_SoC   |b4-7chg/dis?  | pack_7_volts     | pack_7_volts    |

### Screenshot
![Screenshot](https://github.com/FozzieUK/FoxESS-Canbus-Protocol/blob/main/packinf.jpg)


### Received frame 

> 0x1871 [0x01,  0x00,  0x01,  0x00,  0x02,  0x00,  0x00,  0x00]
> 
> 0x1871 [0x01,  0x00,  0x01,  0x00,  0x04,  0x00,  0x00,  0x00]

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

This is broadcast by the inverter every 'n' seconds and contains a timestamp in bytes 2-7 i.e. ```<YY>,<MM>,<DD>,<HH>,<SS>,<mm>```





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
