# FoxESS-Canbus-Protocol
FoxESS Canbus Protocol between BMS and H series inverter

Can bus @ 500k - all Extended ID, little endian

## Frames

### Received frame 

> 0x1871 [0x01,  0x00,  0x01,  0x00,  0x00,  0x00,  0x00,  0x00]

### Response frames

| ID     | b0                | b1                | b2                | b3                | b4           | b5           | b6               | b7              |
| ------ | ----------------- | ----------------- | ----------------- | ----------------- | ------------ | ------------ | ---------------- | --------------- |
| 0x1872 | slave_voltage_max | slave_voltage_max | slave_voltage_min | slave_voltage_min | charge_max   | charge_max   | discharge_max    | discharge_max   |
| 0x1873 | voltage           | voltage           | current sensor    | current sensor    | capacity     | capacity     | kwh_remaining    | kwh_remaining   |
| 0x1874 | cells_temp_max    | cells_temp_max    | cells_temp_min    | cells_temp_min    | cells_mv_max | cells_mv_max | cells_mv_min     | cells_mv_min    |
| 0x1875 | int_temp          | pack_temp         | pack_temp         | 0x00              | 0x01 contact | 0x00         | cycle_count      | cycle_count     |
| 0x1876 | 0x01              | 0x00              | cells_volts_max   | cells_volts_max   | 0x00         | 0x00         | cells_volts_min  | cells_volts_min |
| 0x1877 | 0x00              | 0x00              | 0x00              | 0x00              | id           | 0x00         | f/w version      | pack_id 0x10    |
| 0x1878 | pack_voltage_max  | pack_voltage_max  | 0x00              | 0x00              | wh_total     | wh_total     | wh_total         | wh_total        |
| 0x1879 | 0x00              | b1=disc,b2=chg    | 0x00              | 0x00              | wh_total     | wh_total     | wh_total         | wh_total        |

### Received frame 

> 0x1871 [0x02,  0x00,  0x01,  0x00,  0x01,  0x00,  0x00,  0x00]
> 0x1871 [0x05,  0x00,  0x01,  0x00,  0x00,  0x00,  0x00,  0x00]

### Response frames

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

> 0x1871 [0x01,  0x00,  0x01,  0x00,  0x00,  0x00,  0x00,  0x00]
> 0x1871 [0x01,  0x00,  0x01,  0x00,  0x04,  0x00,  0x00,  0x00]

### Response frames

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

then followed by cell temps (decimal 50 offset)

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
















Disclaimers

No Warranty - this information is provided "as is", without warranty of any kind, either express or implied, including, but not limited to, the implied warranties of mechantability and fitness for a particular purpose. I do not warrant the accuracy of the information in the content. I am neither affiliated with, associated, authorized, endorsed by, or in any way officially connected to FoxESS Co. Ltd
