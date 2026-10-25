# CSV 表结构清单

本清单按用户已确认的转换、列来源和排序规则，从实际数据推导。CSV 编码与文件封装规则已于 2026-10-25 确认；工具尚未实现，命令行运行方式和异常处理另行确定。

数据来源：[v2 固定快照](https://github.com/Null-Signal-Games/netrunner-cards-json/tree/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2)。只读取实际 JSON，排除 translations，不读取或参考上游 schema。行数仅代表这个快照，不是未来导出的固定要求。

决策记录：[确定 CSV 表结构与复杂字段转换规则](https://github.com/eric03742/netrunner-table/issues/3)。

## CSV 文件格式

| 项目 | 已确认规则 |
| --- | --- |
| 编码 | UTF-8，无 BOM |
| 表头 | 第一条 CSV 记录为列名 |
| 列分隔符 | 英文逗号 `,` |
| 记录之间的换行 | CRLF |
| 单元格内的换行 | 原样保留，包括已确认的文本追加规则 |
| 引号 | 含逗号、双引号、CR 或 LF 的单元格用双引号包裹；内部双引号写成两个双引号 |
| 布尔值 | 小写 `true`、`false` |
| 缺失字段、null、空字符串 | 空白单元格 |
| 数字 0 | `0`，不转为空白 |

数组先按已确认规则连接为单元格文本，再进行 CSV 引号转义。例如数组 `["icebreaker","decoder"]` 的单元格值为 `icebreaker,decoder`，CSV 文件中表示为 `"icebreaker,decoder"`。单元格内可能有换行，因此一条 CSV 记录不一定只占一个物理文本行。

保留原始 ID、文本、HTML／Markdown 标记和空白；表格软件按配套导入说明读取，ID 列作为文本导入。Excel／飞书真实导入效果属于后续验收，不由本清单声称已验证。

引号与记录换行依据：[RFC 4180 中的 CSV 格式描述](https://www.rfc-editor.org/rfc/rfc4180.html)。无 BOM UTF-8 文件可通过 Excel 文本／CSV 导入入口读取，参见[微软说明](https://support.microsoft.com/en-us/excel/opening-csv-utf-8-files-correctly-in-excel)。

## 表与列

共 16 个 CSV，5,826 行数据（不含表头）。下表列名从左到右表示输出顺序。

| CSV | 数据行数 | 列名（按顺序） |
| --- | ---: | --- |
| card_cycles.csv | 29 | `id`, `legacy_code`, `name`, `position`, `released_by` |
| card_layouts.csv | 5 | `id`, `description`, `name` |
| card_pools.csv | 30 | `id`, `card_cycle_ids`, `card_set_ids`, `format_id`, `name` |
| card_set_types.csv | 8 | `id`, `description`, `name` |
| card_sets.csv | 75 | `id`, `card_cycle_id`, `card_set_type_id`, `date_release`, `legacy_code`, `name`, `position`, `released_by`, `size` |
| card_subtypes.csv | 106 | `id`, `name` |
| card_types.csv | 11 | `id`, `name`, `side_id` |
| cards.csv | 2,054 | `id`, `advancement_requirement`, `agenda_points`, `attribution`, `base_link`, `card_type_id`, `cost`, `deck_limit`, `designed_by`, `faction_id`, `influence_cost`, `influence_limit`, `is_unique`, `layout_id`, `memory_cost`, `minimum_deck_size`, `mu_limit`, `narrative_text`, `pronouns`, `pronunciation_approx`, `pronunciation_ipa`, `side_id`, `strength`, `stripped_text`, `stripped_title`, `subtypes`, `text`, `title`, `trash_cost` |
| factions.csv | 12 | `id`, `color`, `description`, `is_mini`, `name`, `side_id` |
| formats.csv | 7 | `id`, `name` |
| printings.csv | 2,460 | `id`, `card_id`, `card_set_id`, `copy_quantity`, `flavor`, `illustrator`, `layout_id`, `position`, `quantity`, `released_by` |
| restrictions.csv | 56 | `id`, `banned`, `banned_subtypes`, `date_start`, `format_id`, `max_3_point_agendas`, `name`, `point_limit`, `restricted` |
| ruling_sources.csv | 16 | `id`, `name`, `url` |
| rulings.csv | 886 | `card_id`, `date_update`, `nsg_rules_team_verified`, `question`, `answer`, `text_ruling` |
| sides.csv | 2 | `id`, `name` |
| snapshots.csv | 69 | `id`, `format_id`, `card_pool_id`, `date_start`, `restriction_id`, `active` |

## 列与行的来源

- 列候选来自同类实际记录出现过的字段并集，再应用已确认的排除、重命名、合并和拆表规则。单条记录缺失对应字段时留空。
- formats、snapshots、rulings 保留单独确认的列顺序；其余表 id 首列，其余列按字段名字母顺序。
- 按源文件相对路径的字母顺序读取；源文件内保留原数组顺序。根对象文件贡献一条记录。
- snapshots 按父级 formats 读取顺序及原 snapshots 数组顺序输出。
- rulings 不新增 id 或辅助列，同一卡牌的多条裁定分别保留。
- card_cycles.description 在当前实际数据中从未出现，因此没有该列。

## 数组核查

按已确认规则，以下数组在同一个单元格内以逗号连接，元素顺序保留。

| 输出字段 | 数组数量 | 元素数量 |
| --- | ---: | ---: |
| card_pools.card_cycle_ids | 22 | 175 |
| card_pools.card_set_ids | 30 | 597 |
| cards.subtypes | 1,706 | 2,356 |
| restrictions.banned | 53 | 1,479 |
| restrictions.banned_subtypes | 16 | 16 |
| restrictions.restricted | 10 | 210 |

共 1,837 个数组、4,833 个元素，实际元素均为非空字符串，没有逗号、CR/LF、null 或非字符串元素，也没有空数组。这个事实仅适用于固定快照；未来新数据形态的处理留待异常处理规格决定。

应用已确认的排除、文本合并、拆表与改名规则后，没有剩余的对象字段需要转换。

## 已确认的预览附件

- [cards.faces 的全部 7 张卡牌](cards-faces.md)
- [printings.faces 的全部 7 条印刷记录](printings-faces.md)
- [formats 与 snapshots 的完整表格预览](formats-snapshots.md)
- 实际 CSV 预览：[formats.csv](formats.csv)、[snapshots.csv](snapshots.csv)

以上文件用于复核已确认的转换效果。每个对象的完整规则、字段排除及已接受的信息损失，以决策工单的最终结论为准。
