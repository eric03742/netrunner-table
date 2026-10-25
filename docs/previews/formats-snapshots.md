# formats 与 snapshots CSV 预览

状态：用户已确认本次完整数据预览；formats.snapshots 独立导出为 snapshots.csv，按以下表结构和转换规则处理。

来源：[固定版本的 v2/formats](https://github.com/Null-Signal-Games/netrunner-cards-json/tree/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/formats)，提交 6ff880724741ea6ac062acf77e0cf979bb44e67e。

- formats.csv：每个赛制一行，保留 id、name，移除 snapshots 字段。
- snapshots.csv：每个快照一行，增加父级赛制的 format_id，与 formats.id 关联。
- 保留快照原字段值；缺失值留空，active 的 true/false 分别保留，不将缺失值补成 false。
- 已确认按源文件相对路径排序赛制；每个赛制内的快照保留源数组顺序。
- 预览文件使用已确认的 UTF-8（无 BOM）、逗号分隔和 CRLF 行结束符。

核对结果：7 个赛制、69 条快照；快照 ID 全部唯一，format_id 全部能关联到赛制。active 有 7 个 true、3 个 false、59 个空白；restriction_id 有 17 个空白。

## formats.csv（全部 7 行）

| id | name |
| --- | --- |
| core | Core |
| eternal | Eternal |
| ram | Random Access Memories |
| snapshot | Snapshot |
| standard | Standard |
| startup | Startup |
| system_gateway | System Gateway |

## snapshots.csv（全部 69 行）

| id | format_id | card_pool_id | date_start | restriction_id | active |
| --- | --- | --- | --- | --- | --- |
| core_0 | core | core | 2025-04-24 |  | true |
| eternal_0 | eternal | eternal | 2017-10-01 |  |  |
| eternal_1 | eternal | eternal | 2021-07-10 | eternal_points_list_1_0 |  |
| eternal_2 | eternal | eternal | 2021-11-12 | eternal_points_list_21_11 |  |
| eternal_3 | eternal | eternal | 2022-09-26 | eternal_points_list_22_09 |  |
| eternal_4 | eternal | eternal | 2023-03-31 | eternal_points_list_23_03 |  |
| eternal_5 | eternal | eternal | 2023-12-08 | eternal_points_list_23_11 |  |
| eternal_6 | eternal | eternal | 2025-04-04 | eternal_points_list_25_04 |  |
| eternal_7 | eternal | eternal | 2025-07-31 | eternal_points_list_25_07 |  |
| eternal_8 | eternal | eternal | 2026-03-02 | eternal_points_list_26_03 | true |
| ram_0 | ram | ram_0 | 2021-12-07 |  |  |
| ram_1 | ram | ram_1 | 2021-12-18 |  |  |
| ram_2 | ram | ram_2 | 2022-01-01 |  |  |
| ram_3 | ram | ram_3 | 2022-01-15 |  |  |
| ram_4 | ram | ram_4 | 2022-01-29 |  |  |
| ram_5 | ram | ram_5 | 2022-02-12 |  |  |
| ram_6 | ram | ram_6 | 2022-02-26 |  |  |
| ram_7 | ram | ram_7 | 2022-11-12 |  | true |
| snapshot_0 | snapshot | snapshot | 2018-11-16 | snapshot_ban_list_1_0 | true |
| standard_0 | standard | pre_rotation | 2012-09-06 |  |  |
| standard_1 | standard | pre_rotation | 2016-02-01 | napd_mwl_1_0 |  |
| standard_2 | standard | pre_rotation | 2016-08-01 | napd_mwl_1_1 |  |
| standard_3 | standard | pre_rotation | 2017-04-12 | napd_mwl_1_2 |  |
| standard_4 | standard | rotation_2017 | 2017-10-01 | napd_mwl_2_0 |  |
| standard_5 | standard | rotation_2017 | 2018-02-26 | napd_mwl_2_1 |  |
| standard_6 | standard | rotation_2017 | 2018-09-06 | napd_mwl_2_2 |  |
| standard_7 | standard | rotation_2018 | 2018-12-21 | standard_mwl_3_0 |  |
| standard_8 | standard | rotation_2018 | 2019-02-22 | standard_mwl_3_1 |  |
| standard_9 | standard | rotation_2018 | 2019-05-10 | standard_mwl_3_2 |  |
| standard_10 | standard | rotation_2018 | 2019-07-19 | standard_mwl_3_3 |  |
| standard_11 | standard | rotation_2018 | 2019-09-20 | standard_mwl_3_4 |  |
| standard_12 | standard | rotation_2019 | 2019-12-27 | standard_mwl_3_4 |  |
| standard_13 | standard | rotation_2019 | 2019-12-31 | standard_mwl_3_4_b |  |
| standard_14 | standard | rotation_2019 | 2020-06-26 | standard_ban_list_20_06 |  |
| standard_15 | standard | rotation_2019 | 2020-09-25 | standard_ban_list_20_09 |  |
| standard_16 | standard | rotation_2020 | 2020-12-18 | standard_ban_list_20_09 |  |
| standard_17 | standard | rotation_2021 | 2021-04-09 | standard_ban_list_21_04 |  |
| standard_18 | standard | rotation_2021 | 2021-05-01 | standard_ban_list_21_05 |  |
| standard_19 | standard | rotation_2021 | 2021-06-19 | standard_ban_list_21_06 |  |
| standard_20 | standard | rotation_2021 | 2021-10-29 | standard_ban_list_21_10 |  |
| standard_21 | standard | rotation_2022 | 2022-08-05 | standard_ban_list_22_08 |  |
| standard_22 | standard | rotation_2022 | 2022-09-23 | standard_ban_list_22_09 |  |
| standard_23 | standard | rotation_2022 | 2023-03-03 | standard_ban_list_23_03 |  |
| standard_24 | standard | rotation_2023 | 2023-08-11 | standard_ban_list_23_08 |  |
| standard_25 | standard | rotation_2023 | 2023-09-22 | standard_ban_list_23_09 | false |
| standard_26 | standard | rotation_2023 | 2024-03-30 | standard_ban_list_24_03 | false |
| standard_27 | standard | rotation_2023 | 2024-05-31 | standard_ban_list_24_05 | false |
| standard_28 | standard | rotation_2023 | 2024-09-13 | standard_ban_list_24_09 |  |
| sunset_01 | standard | rotation_2023 | 2023-09-21 | sunset_ban_list_24_01 |  |
| standard_29 | standard | rotation_2023 | 2024-12-26 | standard_ban_list_24_12 |  |
| standard_30 | standard | rotation_2025 | 2025-04-24 | standard_ban_list_25_04 |  |
| standard_31 | standard | rotation_2025 | 2025-08-01 | standard_ban_list_25_08 |  |
| standard_32 | standard | rotation_2025 | 2025-10-03 | standard_ban_list_25_10 |  |
| standard_33 | standard | rotation_2025 | 2025-12-01 | standard_ban_list_25_12 |  |
| standard_34 | standard | standard_2026_vantage_point | 2026-03-13 | standard_ban_list_26_03 | true |
| standard_35 | standard | standard_2026_vantage_point | 2026-05-01 | standard_ban_list_26_05 |  |
| standard_36 | standard | standard_2026_vantage_point | 2026-08-01 | standard_balance_update_26_08 |  |
| startup_0 | startup | startup_ashes | 2019-03-18 |  |  |
| startup_1 | startup | startup_ashes_plus | 2022-03-04 |  |  |
| startup_2 | startup | startup_ashes_plus_midnight_sun | 2022-07-22 |  |  |
| startup_3 | startup | startup_borealis | 2022-12-09 |  |  |
| startup_4 | startup | startup_borealis_plus_the_automata_initiative | 2023-08-11 |  |  |
| startup_5 | startup | startup_borealis_plus_the_automata_initiative | 2024-01-19 | startup_ban_list_24_01 |  |
| startup_6 | startup | startup_liberation | 2024-03-30 | startup_ban_list_24_09 |  |
| startup_7 | startup | startup_elevation | 2025-04-24 | startup_balance_update_25_04 |  |
| startup_8 | startup | startup_elevation | 2025-11-03 | startup_balance_update_25_11 |  |
| startup_9 | startup | startup_vantage_point | 2026-03-02 | startup_balance_update_26_03 | true |
| startup_10 | startup | startup_vantage_point | 2026-05-01 | startup_balance_update_26_05 |  |
| system_gateway_0 | system_gateway | system_gateway | 2021-04-09 |  | true |
