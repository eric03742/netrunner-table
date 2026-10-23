# 非翻译 Netrunner v2 数据：表边界与转换约束研究

研究日期：2026-10-23（Asia/Shanghai）。对应 [核实非翻译 v2 数据的表边界与转换约束](https://github.com/eric03742/netrunner-table/issues/2)，属于 [规划：非翻译 Netrunner v2 数据导出 CSV 的实现规格](https://github.com/eric03742/netrunner-table/issues/1)。本报告记录数据事实和规格需要处理的边界，不决定 CSV 编码方案，不实现转换器。

固定上游版本：[6ff880724741ea6ac062acf77e0cf979bb44e67e](https://github.com/Null-Signal-Games/netrunner-cards-json/commit/6ff880724741ea6ac062acf77e0cf979bb44e67e)。以下源码链接均固定在这个提交；计数只代表这一快照，不声称是任意未来版本的固定规模。原始归档 SHA-256：`0ac89efec2e7c0b460ea095b3f3476cb759a187f68d6679fcf65dbac9ea3e92f`。

## 结论与核查范围

- 全量遍历 [v2](https://github.com/Null-Signal-Games/netrunner-cards-json/tree/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2)，排除路径中的整个 `translations` 子树，得到 **2,861 个 JSON 文件、15 类、5,757 个顶层记录**；范围内没有其他扩展名文件。下文按类别定义记录：根对象算一条，根数组的每个对象算一条；嵌套 `faces`、`snapshots` 等不额外计为顶层记录。这是研究统计单位，不是已定稿的 CSV 行模型。
- [schema/v2](https://github.com/Null-Signal-Games/netrunner-cards-json/tree/6ff880724741ea6ac062acf77e0cf979bb44e67e/schema/v2) 共 21 个 schema，其中 8 个是翻译 schema；非翻译的 **13 个 schema 覆盖 2,207 文件、4,855 个顶层记录**。使用 Python `jsonschema 4.26.0` 的 `Draft202012Validator` 校验全部这 2,207 个 JSON 文档，结果 **0 错误**。另外的 `rulings`、`ruling_sources` 共 **654 文件、902 记录没有对应 schema，未做 schema 校验**；对它们做了全量形态、字段、重复和引用核查。
- 15 类的数据对象都存在于范围清单中。所有顶层字段和递归嵌套值都经过遍历，并非抽样；文中展示的个别值是从全量检查中选出的例子。JSON 解码成功，额外检查了对象内重复键，发现 0 处。
- 14 类有 `id`，均为非空字符串、在各自类别内唯一；`rulings` 没有独立 `id`，886 条裁定覆盖 653 张卡；`(card_id, date_update)` 有 103 组重复，不能直接当唯一键。
- schema 不等于完整字段目录：`restrictions.points` 真实存在 `"4"`，schema 的 `properties` 只列 `"1"`、`"2"`、`"3"`，但没有禁止该嵌套层的额外属性，因此仍通过校验；`cards.faces` 与 `printings.faces` 内部字段也没有完整枚举。单纯依照 schema 递归挑选字段会丢失真实数据。
- 人工与机器读取有实际冲突点：822 个印刷版本 ID 带前导零；25 个显式 null、1 个空字符串、许多缺失字段需要区分；共有 2,279 个字符串值含 LF 换行；144 个字符串值以 `+` 等公式敏感前缀集合中的字符开头（实际 `+` 142 个、`-` 2 个）。这些是后续格式决策的输入。

## 完整范围、文件形状与记录边界

同名目录中的分片共享 schema，文件数量不等于表数量。`restrictions` 必须递归读取格式子目录；不能只读取该目录的直接子文件。所有有 schema 的根数组验证整个数组；根对象验证整个对象。数据、schema 与上游读取/测试的对应关系参见下表以及 [上游 v2 校验测试](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/test/validate_v2.test.ts)、[上游读取函数](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/src/index.ts)。

| 类别 | 数据路径 | 文件数 | 根形状／提取单位 | 顶层记录数 | schema |
|---|---|---:|---|---:|---|
| `card_cycles` | [card_cycles](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/card_cycles.json) | 1 | 对象数组；每元素一条 | 29 | [card_cycles_schema](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/schema/v2/card_cycles_schema.json) |
| `card_layouts` | [card_layouts](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/card_layouts.json) | 1 | 对象数组；每元素一条 | 5 | [card_layouts_schema](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/schema/v2/card_layouts_schema.json) |
| `card_pools` | [card_pools](https://github.com/Null-Signal-Games/netrunner-cards-json/tree/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/card_pools) | 7 | 对象数组；每元素一条 | 30 | [card_pools_schema](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/schema/v2/card_pools_schema.json) |
| `card_set_types` | [card_set_types](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/card_set_types.json) | 1 | 对象数组；每元素一条 | 8 | [card_set_types_schema](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/schema/v2/card_set_types_schema.json) |
| `card_sets` | [card_sets](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/card_sets.json) | 1 | 对象数组；每元素一条 | 75 | [card_sets_schema](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/schema/v2/card_sets_schema.json) |
| `card_subtypes` | [card_subtypes](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/card_subtypes.json) | 1 | 对象数组；每元素一条 | 106 | [card_subtypes_schema](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/schema/v2/card_subtypes_schema.json) |
| `card_types` | [card_types](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/card_types.json) | 1 | 对象数组；每元素一条 | 11 | [card_types_schema](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/schema/v2/card_types_schema.json) |
| `cards` | [cards](https://github.com/Null-Signal-Games/netrunner-cards-json/tree/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/cards) | 2,054 | 对象；每文件一条 | 2,054 | [cards_schema](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/schema/v2/cards_schema.json) |
| `factions` | [factions](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/factions.json) | 1 | 对象数组；每元素一条 | 12 | [factions_schema](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/schema/v2/factions_schema.json) |
| `formats` | [formats](https://github.com/Null-Signal-Games/netrunner-cards-json/tree/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/formats) | 7 | 对象；每文件一条 | 7 | [formats_schema](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/schema/v2/formats_schema.json) |
| `printings` | [printings](https://github.com/Null-Signal-Games/netrunner-cards-json/tree/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/printings) | 75 | 对象数组；每元素一条 | 2,460 | [printings_schema](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/schema/v2/printings_schema.json) |
| `restrictions` | [restrictions](https://github.com/Null-Signal-Games/netrunner-cards-json/tree/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/restrictions) | 56 | 对象；每文件一条，递归子目录 | 56 | [restrictions_schema](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/schema/v2/restrictions_schema.json) |
| `ruling_sources` | [ruling_sources](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/ruling_sources.json) | 1 | 对象数组；每元素一条 | 16 | 无 |
| `rulings` | [rulings](https://github.com/Null-Signal-Games/netrunner-cards-json/tree/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/rulings) | 653 | 对象数组；每元素一条 | 886 | 无 |
| `sides` | [sides](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/sides.json) | 1 | 对象数组；每元素一条 | 2 | [sides_schema](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/schema/v2/sides_schema.json) |
| **合计** | 排除全部 `translations` | **2,861** |  | **5,757** | **13** |

若采用“每个 schema 一表，同时为两类无 schema 数据各建一表”，会得到 15 张基础表；这是据事实推导的可选规格，不是本研究替用户确定的产物清单。是否把嵌套对象额外拆表由决策工单处理。

## 字段来源与实际类型

下表以记录对象的 schema `properties` 为字段候选（数组 schema 使用 `items.properties`），与全量实际字段并列。schema 唯一已定义但完全未出现的顶层字段是 `card_cycles.description`；13 类均未发现 schema 之外的顶层字段，不能由此推论嵌套字段也全部被 schema 枚举。字段顺序是本报告为了比较而排序，不是 CSV 列顺序决策。schema 的 required／条件约束请以链接原文为准：**“本快照每行都有”不表示“schema 强制必填”**。

### schema 字段全集及差异

| 类别（schema／数据依据） | 顶层字段候选 | 仅 schema 有、数据未出现 |
|---|---|---|
| [card_cycles](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/schema/v2/card_cycles_schema.json) | `description`、`id`、`legacy_code`、`name`、`position`、`released_by` | `description` |
| [card_layouts](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/schema/v2/card_layouts_schema.json) | `description`、`id`、`name` | 无 |
| [card_pools](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/schema/v2/card_pools_schema.json) | `card_cycle_ids`、`card_set_ids`、`format_id`、`id`、`name` | 无 |
| [card_set_types](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/schema/v2/card_set_types_schema.json) | `description`、`id`、`name` | 无 |
| [card_sets](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/schema/v2/card_sets_schema.json) | `card_cycle_id`、`card_set_type_id`、`date_release`、`id`、`legacy_code`、`name`、`position`、`released_by`、`size` | 无 |
| [card_subtypes](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/schema/v2/card_subtypes_schema.json) | `id`、`name` | 无 |
| [card_types](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/schema/v2/card_types_schema.json) | `id`、`name`、`side_id` | 无 |
| [cards](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/schema/v2/cards_schema.json) | `advancement_requirement`、`agenda_points`、`attribution`、`base_link`、`card_type_id`、`cost`、`deck_limit`、`designed_by`、`faces`、`faction_id`、`id`、`influence_cost`、`influence_limit`、`is_unique`、`layout_id`、`memory_cost`、`minimum_deck_size`、`mu_limit`、`narrative_text`、`pronouns`、`pronunciation_approx`、`pronunciation_ipa`、`side_id`、`strength`、`stripped_text`、`stripped_title`、`subtypes`、`text`、`title`、`trash_cost` | 无 |
| [factions](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/schema/v2/factions_schema.json) | `color`、`description`、`id`、`is_mini`、`name`、`side_id` | 无 |
| [formats](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/schema/v2/formats_schema.json) | `id`、`name`、`snapshots` | 无 |
| [printings](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/schema/v2/printings_schema.json) | `card_id`、`card_set_id`、`copy_quantity`、`faces`、`flavor`、`id`、`illustrator`、`layout_id`、`position`、`quantity`、`released_by` | 无 |
| [restrictions](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/schema/v2/restrictions_schema.json) | `banned`、`date_start`、`format_id`、`global_penalty`、`id`、`max_3_point_agendas`、`name`、`point_limit`、`points`、`restricted`、`subtypes`、`universal_faction_cost` | 无 |
| [ruling_sources](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/ruling_sources.json) | `id`、`name`、`url` | 无 |
| [rulings](https://github.com/Null-Signal-Games/netrunner-cards-json/tree/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/rulings) | `answer`、`card_id`、`date_update`、`nsg_rules_team_verified`、`question`、`text_ruling` | 无 |
| [sides](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/schema/v2/sides_schema.json) | `id`、`name` | 无 |

### 全量字段类型与缺失计数

`string × N` 等表示该值类型实际出现 N 次；missing 是该键不在对象内的记录数，与 JSON null 不同。只列可能缺失或具有复杂／多种值类型的字段；上表其余字段均在本类所有记录中出现，且类型统一。固定整型字段包括 `card_cycles.position`、`card_sets.position/size`、`cards.deck_limit`、`printings.position/quantity`；固定布尔字段包括 `cards.is_unique`、`factions.is_mini`、`rulings.nsg_rules_team_verified`；其余未列的固定字段为字符串。

| 类别 | 字段 | 观察到的类型及数量 | missing |
|---|---|---|---:|
| `card_pools` | `card_cycle_ids` | array × 22 | 8 |
| `card_pools` | `card_set_ids` | array × 30 | 0 |
| `cards` | `advancement_requirement` | integer × 180；null × 1 | 1,873 |
| `cards` | `agenda_points` | integer × 181 | 1,873 |
| `cards` | `attribution` | string × 25 | 2,029 |
| `cards` | `base_link` | integer × 67 | 1,987 |
| `cards` | `cost` | integer × 1,717；null × 8 | 329 |
| `cards` | `faces` | array × 7 | 2,047 |
| `cards` | `influence_cost` | integer × 1,760 | 294 |
| `cards` | `influence_limit` | integer × 134；null × 14 | 1,906 |
| `cards` | `layout_id` | string × 31 | 2,023 |
| `cards` | `memory_cost` | integer × 255 | 1,799 |
| `cards` | `minimum_deck_size` | integer × 148 | 1,906 |
| `cards` | `mu_limit` | integer × 67 | 1,987 |
| `cards` | `narrative_text` | string × 18 | 2,036 |
| `cards` | `pronouns` | string × 28 | 2,026 |
| `cards` | `pronunciation_approx` | string × 69 | 1,985 |
| `cards` | `pronunciation_ipa` | string × 69 | 1,985 |
| `cards` | `strength` | integer × 454；null × 2 | 1,598 |
| `cards` | `stripped_text` | string × 2,052 | 2 |
| `cards` | `subtypes` | array × 1,706 | 348 |
| `cards` | `text` | string × 2,052 | 2 |
| `cards` | `trash_cost` | integer × 350 | 1,704 |
| `factions` | `description` | string × 8 | 4 |
| `formats` | `snapshots` | array × 7 | 0 |
| `printings` | `copy_quantity` | integer × 9 | 2,451 |
| `printings` | `faces` | array × 7 | 2,453 |
| `printings` | `flavor` | string × 1,800 | 660 |
| `printings` | `illustrator` | string × 2,439 | 21 |
| `printings` | `layout_id` | string × 1 | 2,459 |
| `restrictions` | `banned` | array × 53 | 3 |
| `restrictions` | `global_penalty` | object × 2 | 54 |
| `restrictions` | `max_3_point_agendas` | integer × 5 | 51 |
| `restrictions` | `point_limit` | integer × 8 | 48 |
| `restrictions` | `points` | object × 8 | 48 |
| `restrictions` | `restricted` | array × 10 | 46 |
| `restrictions` | `subtypes` | object × 16 | 40 |
| `restrictions` | `universal_faction_cost` | object × 1 | 55 |
| `ruling_sources` | `url` | string × 12 | 4 |
| `rulings` | `answer` | string × 718 | 168 |
| `rulings` | `question` | string × 718 | 168 |
| `rulings` | `text_ruling` | string × 168 | 718 |

以上所有字段计数均由范围表中固定提交的数据全量扫描得到；示例见以下复杂值和空值小节。

## 嵌套、数组与 schema 覆盖边界

| 字段 | 全量观察 | 来源及边界 |
|---|---|---|
| `cards.subtypes` | 1,706 个数组，共 2,356 个字符串元素 | [卡牌例子](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/cards/419_amoral_scammer.json)；缺失 348 条，不等于空数组 |
| `cards.faces` | 7 个数组、11 个对象；实际内部键为 `base_link`, `stripped_text`, `stripped_title`, `subtypes`, `text`, `title`；其中 `subtypes` 还是字符串数组 | [Dewi 例子](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/cards/dewi_subrotoputri_pedagogical_dhalang.json)；[schema](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/schema/v2/cards_schema.json) 主要限制数组／对象类型、布局和数量，没有枚举 face 内部字段 |
| `printings.faces` | 7 个数组、15 个对象；`flavor` 15 次，`copy_quantity` 5 次 | [Elevation 数据](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/printings/elevation.json)；[schema](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/schema/v2/printings_schema.json) 同样没有完整定义内部属性 |
| `card_pools.card_cycle_ids` / `card_set_ids` | 22 / 30 个数组，共 175 / 597 个字符串元素 | [卡池目录](https://github.com/Null-Signal-Games/netrunner-cards-json/tree/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/card_pools)；前者 8 条缺失，后者全部存在 |
| `formats.snapshots` | 7 个数组、69 个对象；69 个都有 `id`、`card_pool_id`、`date_start`，52 个有 `restriction_id`，10 个有 `active` | [赛制目录](https://github.com/Null-Signal-Games/netrunner-cards-json/tree/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/formats)；[schema](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/schema/v2/formats_schema.json) 对 snapshot 仅强制 `card_pool_id` 与 `date_start`；快照 `id` 全局唯一是当前数据事实 |
| `restrictions.banned` / `restricted` | 53 / 10 个数组，共 1,479 / 210 个字符串元素 | [限制目录](https://github.com/Null-Signal-Games/netrunner-cards-json/tree/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/restrictions) |
| `restrictions.points` | 8 个对象，键集合为 `"1"`、`"2"`、`"3"`、`"4"`，值全为卡牌 ID 字符串数组 | [26.03 数据](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/restrictions/eternal/eternal_points_list_26_03.json)；3 个文件含键 `"4"`，其数组元素合计 5 个 |
| `restrictions.global_penalty` | 2 个对象，实际键 `"1"`，值为字符串数组 | [MWL 1.0](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/restrictions/standard/napd_mwl_1_0.json) |
| `restrictions.universal_faction_cost` | 1 个对象，实际键 `"1"`、`"3"`，值为字符串数组 | [MWL 1.2](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/restrictions/standard/napd_mwl_1_2.json) |
| `restrictions.subtypes` | 16 个对象，键 `banned`，值为字符串数组 | [20.06 数据](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/restrictions/standard/standard_ban_list_20_06.json) |

动态数值键必须保留为对象键，不能误读为固定数组下标。[restrictions schema](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/schema/v2/restrictions_schema.json) 的 `points` 仅声明 1/2/3；实际键 4 出现在 [eternal_points_list_25_04](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/restrictions/eternal/eternal_points_list_25_04.json), [eternal_points_list_25_07](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/restrictions/eternal/eternal_points_list_25_07.json), [eternal_points_list_26_03](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/restrictions/eternal/eternal_points_list_26_03.json)。该对象未声明 `additionalProperties: false`，所以“全部通过 schema”与“schema 没有列全实际字段”同时成立。

目前没有发现任何空数组、空对象或 JSON 浮点数；这仅描述本快照，不能据此排除未来数据中的这些形态。数组中的先后位置可能承载 faces／snapshots／裁定等顺序；本研究没有将其改排或去重。

## 裁定与裁定来源：无 schema 的两类

`rulings` 是按卡牌分片的对象数组，不是“一文件一裁定”。全量 653 文件、886 条，单文件 1–21 条；530 文件只有 1 条，其余 123 文件有多条。886 条恰好分为 **718 条 question+answer** 与 **168 条 text_ruling**，没有同时属于两型或缺少两型文本的条目。全部都有 `card_id`、`date_update`、`nsg_rules_team_verified`，后者 true 275 条、false 611 条。数据依据为 [v2/rulings](https://github.com/Null-Signal-Games/netrunner-cards-json/tree/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/rulings)；生成逻辑见 [上游 fetch-rulings.ts](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/src/fetch-rulings.ts)，它明确把同一卡牌的条目放入数组，按日期等进行排序。

全部裁定对象没有 `id`，也没有 `source_id` 等结构化来源关联。全量比较 JSON 内容未发现完全相同的裁定对象，但这不能提供稳定身份保证。`(card_id,date_update)` 的 103 组重复中，[Accelerated Diagnostics](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/rulings/accelerated_diagnostics.json) 在同一天有 6 条不同裁定。因此，若要输出一裁定一行，必须决定是否新增来源路径／数组序号／内容散列／生成 ID，以及这些列仅用于一次导出定位还是承诺跨版本稳定。

`ruling_sources.json` 有 16 条：`id`、`name` 全部存在，`url` 12 条存在、4 条缺失，均为字符串；没有 null。[完整裁定来源列表](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/ruling_sources.json)。不能凭来源列表推断裁定已经具有可直接 join 的来源外键；实际裁定中仅有正文内 Markdown／HTML 的文本出处，如 [419 裁定](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/rulings/419_amoral_scammer.json) 的 question。解析这些文本建立外键会引入额外语义转换，不是本次调查已得到的结构化字段。

## 标识、路径与关联完整性

所有有 `id` 的 14 类在类别内唯一，且是字符串。`printings.id` 的 2,460 个值全部为纯数字字符串，其中 **822 个带前导零**；例如 [Core Set printings](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/printings/core_set.json) 中的 `"01001"`。字段的 JSON 字符串类型应进入后续导入契约：不能仅凭外观将其归类为整数。

本次全量检查了以下 26 组、17,689 个引用值，全部能在目标类别 ID 集合中找到。这里验证的是“引用存在”，不声称穷尽游戏领域的所有约束或证明正文链接有效。

| 引用字段 | 目标类别 | 检查值数量 | 未找到 |
|---|---|---:|---:|
| `factions.side_id` | `sides` | 12 | 0 |
| `card_types.side_id` | `sides` | 11 | 0 |
| `card_sets.card_cycle_id` | `card_cycles` | 75 | 0 |
| `card_sets.card_set_type_id` | `card_set_types` | 75 | 0 |
| `cards.side_id` | `sides` | 2,054 | 0 |
| `cards.faction_id` | `factions` | 2,054 | 0 |
| `cards.card_type_id` | `card_types` | 2,054 | 0 |
| `cards.layout_id` | `card_layouts` | 31 | 0 |
| `printings.card_id` | `cards` | 2,460 | 0 |
| `printings.card_set_id` | `card_sets` | 2,460 | 0 |
| `printings.layout_id` | `card_layouts` | 1 | 0 |
| `card_pools.format_id` | `formats` | 30 | 0 |
| `restrictions.format_id` | `formats` | 56 | 0 |
| `rulings.card_id` | `cards` | 886 | 0 |
| `cards.subtypes` | `card_subtypes` | 2,356 | 0 |
| `card_pools.card_cycle_ids` | `card_cycles` | 175 | 0 |
| `card_pools.card_set_ids` | `card_sets` | 597 | 0 |
| `restrictions.banned` | `cards` | 1,479 | 0 |
| `restrictions.restricted` | `cards` | 210 | 0 |
| `formats.snapshots.card_pool_id` | `card_pools` | 69 | 0 |
| `formats.snapshots.restriction_id` | `restrictions` | 52 | 0 |
| `restrictions.points` | `cards` | 419 | 0 |
| `restrictions.global_penalty` | `cards` | 26 | 0 |
| `restrictions.universal_faction_cost` | `cards` | 20 | 0 |
| `restrictions.subtypes.banned` | `card_subtypes` | 16 | 0 |
| `cards.faces.subtypes` | `card_subtypes` | 11 | 0 |

引用关系的结构依据是上文链接的各类源数据与 schema；上游也在 [validate_v2.test.ts](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/test/validate_v2.test.ts) 中检查其中多项。表中每组都实际检查了本次全部数据，不是仅从测试源码推断。

文件名不能统一替代记录内的标识。全量比较得到：

- 两个裁定文件名与内部 `card_id` 不同：[harishchandra 文件](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/rulings/harishchandra_ent_where_you_re_the_star.json) 内是 `harishchandra_ent_where_youre_the_star`；[i_ve_had_worse 文件](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/rulings/i_ve_had_worse.json) 的两条记录内都是 `ive_had_worse`。这三个引用仍全部有效。
- 两个限制文件名与内部 `id` 不同：[24.01 文件](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/restrictions/standard/startup_ban_list_24_01.json) 与 [24.09 文件](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/restrictions/standard/startup_ban_list_24_09.json) 的内部 ID 都多出 `_for_classic_only`。
- 本快照的 `cards` 文件名均等于 `id`；`printings` 文件名均等于各元素 `card_set_id`；`card_pools` 文件名均等于各元素 `format_id`；`formats` 文件名均等于 `id`。这些是已核查事实，不是允许未来忽略内部字段的约定。

## 空值、文本与双用途 CSV 的事实约束

- **显式 null 25 次，全在 cards**：`advancement_requirement` 1、`cost` 8、`influence_limit` 14、`strength` 2；这些字段还存在正常整数和缺失两种情况。例如 [Ad Blitz](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/cards/ad_blitz.json) 的 `cost: null`，其 `advancement_requirement` 缺失；[cards schema](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/schema/v2/cards_schema.json) 允许这些 null，并按卡牌类型施加不同 required 条件。把 null、缺失、0 都写成同一种空白会失去不同的原始状态。
- **空字符串 1 次**：[factions.json](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/factions.json) 的 `adam.description` 为 `""`，另有 4 个阵营缺少 description。这与空字段和 null 的表示相关。
- **文本缺失并非解析失败**：[Vanity Project](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/cards/vanity_project.json) 和 [Sunny Lebeau](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/cards/sunny_lebeau_security_specialist.json) 都没有 `text` 与 `stripped_text`；两者仍通过 schema 校验。
- **LF 换行 2,279 个字符串值**：cards（包含嵌套 faces）1,299、printings 208、rulings 765，factions 7；未发现 CR。例如 [15 Minutes text](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/cards/15_minutes.json) 和 [A Study in Static flavor](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/printings/a_study_in_static.json)。这里统计的是含换行的字符串数量，不是换行字符个数，也不是行数。
- **逗号、双引号、非 ASCII 都真实存在**：[24/7 News Cycle](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/cards/24_7_news_cycle.json)、[A Teia 的 IPA](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/cards/a_teia_ip_recovery.json) 与 [23 Seconds flavor](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/printings/23_seconds.json) 是可用于转义／编码验收的例子。
- **标记文本不可忽略**：`cards.text` 含 `<strong>` 等标签、`[credit]` 等符号；`rulings.question/answer/text_ruling` 可含 Markdown、URL 和 HTML。例如 [Pantograph](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/cards/pantograph.json) 与 [419 裁定](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/rulings/419_amoral_scammer.json)。`stripped_text` 已存在但不是每种文本都提供同类字段，不能无说明地用 stripped 字段替换原字段。
- **公式敏感前缀是实际数据问题**：递归扫描所有字符串值，以 `= + - @ TAB CR` 中任一字符开头的值共 144 个，均位于 cards，其中 142 个以 `+` 开头、2 个以 `-` 开头，例如 [Access to Globalsec](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/cards/access_to_globalsec.json) 的 `+1[link]`。本研究没有测试 Excel／飞书是否在具体导入入口执行或改写这些字符串；后续规格需要明确导入操作、原样保留与表格软件处理之间的约定。

CSV 的 quoting 能包裹换行和引号，但文件本身不能声明单元格类型；“已正确 CSV 转义”与“应用导入时不会自动改写 ID／日期／文本”必须分别验收。这里的后半句是由数据类型要求推导的设计约束，未将某一 Excel／飞书版本的未测试行为当作事实。

## 已证实、未覆盖与待决策事项

**已证实**：本固定快照的完整非翻译文件／顶层记录清单；所有顶层键、递归类型及上述计数；13 类全部源文档的 Draft 2020-12 校验；14 类 id 的类别内唯一性；69 个 snapshot id 唯一性；全部裁定形态、内容重复和候选复合键重复；表列 26 组引用存在性；指定文件名字段一致性；schema 覆盖不足的实际例子。

**没有执行或证明**：上游完整 `npm test`、Ajv 严格模式与 Python 校验器行为完全等价；上游所有游戏语义测试；没有 schema 的两类数据符合某个尚未定义的自建 schema；所有 URL 可访问；Excel／飞书真实导入；源数据跨提交的身份稳定性；未来上游新目录、新字段或类型变化。没有修改或修复上游数据。

**需要后续人机决策，不能由本报告默认为已决定**：

1. 无 schema 两类的最终表名、记录单位和裁定身份；每 schema 一表与嵌套数组／对象的表达方式（JSON 单元格、展开列或附表）的关系。
2. 列来自 schema 与实际字段的合并方式；嵌套动态键、未来额外字段、新类别如何处理；固定列顺序和输出可复现性。
3. 缺失／null／空字符串及布尔值、整数、ID 字符串的序列化契约；是否需要字段类型与导入说明等伴随文件。
4. 原始带标记文本、换行与公式敏感前缀如何保存和导入；机器可逆性与人工查看之间的明确承诺。
5. 来源固定到 commit、在线／本地输入、失败与部分输出策略、校验强度和后续验收样本。这些是规格问题，研究未实现 CLI。

这些问题属于现有 CSV、CLI 与验收决策工单的范围；本报告没有创建实现任务或替用户完成这些选择。

## 复核方法与可复用检查代码

获取上述提交的 GitHub 源码归档，展开到任意临时目录。在独立 Python 环境安装 `jsonschema==4.26.0`。下面的两个检查脚本是本次实际使用的临时审计代码，嵌在单个报告中供复核；只需将各自 `root` 路径改为该源码目录。运行前先执行 `mkdir -p /private/tmp/netrunner-research-data` 创建输出目录；它们把统计 JSON 写入该目录，不修改源码。数据来自该固定版本而非网络 API 的浮动响应。对象内重复键另用 `json.loads(..., object_pairs_hook=...)` 全量计数检查，结果为 0。

<details>
<summary>全量文件、字段、复杂值与 schema 校验脚本</summary>

```python
import json, collections, pathlib, re
from jsonschema import Draft202012Validator
root=pathlib.Path('/private/tmp/netrunner-research-data/netrunner-cards-json-6ff880724741ea6ac062acf77e0cf979bb44e67e')
types={str:'string',int:'integer',bool:'boolean',float:'number',type(None):'null',dict:'object',list:'array'}
allfiles=sorted(p for p in (root/'v2').rglob('*') if p.is_file() and 'translations' not in p.relative_to(root/'v2').parts)
groups=collections.defaultdict(list)
for p in allfiles:
    rel=p.relative_to(root/'v2'); groups[rel.parts[0].removesuffix('.json')].append(p)
records={}; summary={}; errors=[]
for group,files in groups.items():
    rows=[];docs=[]
    for p in files:
        d=json.loads(p.read_text()); docs.append((p,d))
        rows.extend((str(p.relative_to(root)),i,x) for i,x in enumerate(d if isinstance(d,list) else [d]))
    records[group]=rows
    schema_path=root/'schema/v2'/f'{group}_schema.json'
    sc=json.loads(schema_path.read_text()) if schema_path.exists() else None
    if sc:
        Draft202012Validator.check_schema(sc);v=Draft202012Validator(sc)
        for p,d in docs:
            for e in v.iter_errors(d):errors.append({'file':str(p.relative_to(root)),'path':list(e.absolute_path),'message':e.message})
    props=(sc.get('items',{}) if sc and sc['type']=='array' else sc or {}).get('properties',{})
    keys=sorted({k for _,_,x in rows for k in x})
    fields={k:{'types':dict(collections.Counter(types[type(x[k])] for _,_,x in rows if k in x)), 'missing':sum(k not in x for _,_,x in rows)} for k in keys}
    nested={};special=collections.defaultdict(list)
    def walk(x,path,loc):
        t=types[type(x)];n=nested.setdefault(path,{'types':collections.Counter(),'samples':[]});n['types'][t]+=1
        if len(n['samples'])<2: n['samples'].append({'location':loc,'value':x if not isinstance(x,(dict,list)) else ('keys='+','.join(x) if isinstance(x,dict) else f'len={len(x)}')})
        if x is None:special['null'].append((loc,path,x))
        if x=='':special['empty_string'].append((loc,path,x))
        if isinstance(x,(list,dict)) and not x:special['empty_'+t].append((loc,path,x))
        if isinstance(x,str):
            for name,test in [('newline','\n' in x),('cr','\r' in x),('quote','"' in x),('comma',',' in x),('nonascii',not x.isascii()),('markup',bool(re.search(r'<[^>]+>',x))),('formula_prefix',bool(x) and x[0] in '=+-@\t\r')]:
                if test:special[name].append((loc,path,x))
        if isinstance(x,dict):
            for k,v in x.items():walk(v,f'{path}.{k}' if path else k,loc)
        if isinstance(x,list):
            for i,v in enumerate(x):walk(v,path+'[]',loc+f'[{i}]')
    for p,i,x in rows:walk(x,'',p+f'[{i}]')
    ids=[x['id'] for _,_,x in rows if 'id' in x]
    summary[group]={'files':len(files),'rows':len(rows),'root_types':dict(collections.Counter(types[type(d)] for _,d in docs)),'schema':str(schema_path.relative_to(root)) if sc else None,'keys':keys,'schema_keys':list(props),'unused_schema_keys':sorted(set(props)-set(keys)),'extra_keys':sorted(set(keys)-set(props)) if sc else keys,'fields':fields,'id_count':len(ids),'duplicate_ids':[k for k,n in collections.Counter(ids).items() if n>1],'nested':nested,'special':{k:{'count':len(v),'examples':v[:3]} for k,v in special.items()}}
out={'total_files':len(allfiles),'total_rows':sum(len(x) for x in records.values()),'groups':summary,'schema_errors':errors}
pathlib.Path('/private/tmp/netrunner-research-data/audit.json').write_text(json.dumps(out,ensure_ascii=False,indent=2))
print(json.dumps({'total_files':out['total_files'],'total_rows':out['total_rows'],'groups':{g:{k:v for k,v in s.items() if k not in ['fields','nested','special']} for g,s in summary.items()},'schema_errors':errors},ensure_ascii=False,indent=2))
```

</details>

<details>
<summary>引用、裁定唯一性、文件名和印刷 ID 检查脚本</summary>

```python
import json,pathlib,collections,datetime
root=pathlib.Path('/private/tmp/netrunner-research-data/netrunner-cards-json-6ff880724741ea6ac062acf77e0cf979bb44e67e')
R=collections.defaultdict(list)
for p in sorted((root/'v2').rglob('*.json')):
 rel=p.relative_to(root/'v2')
 if 'translations' in rel.parts:continue
 d=json.loads(p.read_text());g=rel.parts[0].removesuffix('.json');R[g].extend((str(p.relative_to(root)),i,r) for i,r in enumerate(d if isinstance(d,list) else [d]))
ids={g:{r['id'] for p,i,r in rows if 'id' in r} for g,rows in R.items()};checks=[]
def chk(label,values,target):
 vals=list(values);bad=[(p,v) for p,v in vals if v not in ids[target]];checks.append({'label':label,'values':len(vals),'target':target,'invalid':bad})
for g,key,target in [('factions','side_id','sides'),('card_types','side_id','sides'),('card_sets','card_cycle_id','card_cycles'),('card_sets','card_set_type_id','card_set_types'),('cards','side_id','sides'),('cards','faction_id','factions'),('cards','card_type_id','card_types'),('cards','layout_id','card_layouts'),('printings','card_id','cards'),('printings','card_set_id','card_sets'),('printings','layout_id','card_layouts'),('card_pools','format_id','formats'),('restrictions','format_id','formats'),('rulings','card_id','cards')]:chk(g+'.'+key,((p,r[key]) for p,i,r in R[g] if key in r),target)
for g,key,target in [('cards','subtypes','card_subtypes'),('card_pools','card_cycle_ids','card_cycles'),('card_pools','card_set_ids','card_sets'),('restrictions','banned','cards'),('restrictions','restricted','cards')]:chk(g+'.'+key,((p,v) for p,i,r in R[g] for v in r.get(key,[])),target)
for key,target in [('card_pool_id','card_pools'),('restriction_id','restrictions')]:chk('formats.snapshots.'+key,((p,s[key]) for p,i,r in R['formats'] for s in r.get('snapshots',[]) if key in s),target)
for key in ['points','global_penalty','universal_faction_cost']:chk('restrictions.'+key,((p,v) for p,i,r in R['restrictions'] for a in r.get(key,{}).values() for v in a),'cards')
chk('restrictions.subtypes.banned',((p,v) for p,i,r in R['restrictions'] for v in r.get('subtypes',{}).get('banned',[])),'card_subtypes')
chk('cards.faces.subtypes',((p,v) for p,i,r in R['cards'] for f in r.get('faces',[]) for v in f.get('subtypes',[])),'card_subtypes')
rulings=R['rulings'];dups=collections.defaultdict(list)
for p,i,r in rulings:dups[json.dumps(r,sort_keys=True)].append([p,i])
pairdups=collections.defaultdict(list)
for p,i,r in rulings:pairdups[(r['card_id'],r['date_update'])].append([p,i])
filename_mismatches={g:[(p,i,r.get(key)) for p,i,r in R[g] if pathlib.Path(p).stem!=r.get(key)] for g,key in [('rulings','card_id'),('cards','id'),('printings','card_set_id'),('formats','id'),('restrictions','id'),('card_pools','format_id')]}
source=dict(checks=checks,filename_mismatches=filename_mismatches,rulings={
 'types':dict(collections.Counter('qa' if {'question','answer'}<=r.keys() and 'text_ruling' not in r else 'text' if 'text_ruling' in r and not {'question','answer'}&r.keys() else 'other' for p,i,r in rulings)),
 'distinct_card_ids':len({r['card_id'] for p,i,r in rulings}),
 'records_per_file':dict(collections.Counter(collections.Counter(p for p,i,r in rulings).values())),
 'duplicate_records':[v for v in dups.values() if len(v)>1],
 'duplicate_card_date_groups':sum(len(v)>1 for v in pairdups.values()),
 'duplicate_card_date_examples':[v for v in pairdups.values() if len(v)>1][:3],
 'verified':dict(collections.Counter(r['nsg_rules_team_verified'] for p,i,r in rulings))},
 snapshot_duplicate_ids=[k for k,n in collections.Counter(s['id'] for p,i,r in R['formats'] for s in r['snapshots']).items() if n>1],
 points4=[(p,r['points']['4']) for p,i,r in R['restrictions'] if '4' in r.get('points',{})],
 numeric_printing_ids={'count':sum(r['id'].isdigit() for p,i,r in R['printings']),'leading_zero':sum(r['id'].startswith('0') for p,i,r in R['printings']), 'examples':[r['id'] for p,i,r in R['printings']][:5]})
pathlib.Path('/private/tmp/netrunner-research-data/relations.json').write_text(json.dumps(source,ensure_ascii=False,indent=2))
print(json.dumps(source,ensure_ascii=False,indent=2))
```

</details>
