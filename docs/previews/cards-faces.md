# cards.faces 合并效果预览

状态：用户已检查并确认这 7 张卡牌的预览，cards.faces 按下述规则处理。完整表结构与文件格式见 [CSV 表结构清单](csv-table-structure.md)。

数据来源固定为上游提交 [6ff880724741ea6ac062acf77e0cf979bb44e67e](https://github.com/Null-Signal-Games/netrunner-cards-json/commit/6ff880724741ea6ac062acf77e0cf979bb44e67e)，包含全部 7 张具有 `faces` 的卡牌。

按原数组顺序，每个 face 向父级 `text` 追加 `<strong>Face:</strong> `、face 的 `title`、一个换行及 face 的 `text`；向父级 `stripped_text` 同样追加 `Face: `、face 的 `stripped_title` 及 `stripped_text`。父级正文与第一个 face 段落之间，以及相邻 face 段落之间，均用一个空行（两个换行符）分隔；每个 face 段落内部的标题与正文之间仍为一个换行。

face 缺少 `title` 时使用父级 `title`，缺少 `stripped_title` 时使用父级 `stripped_title`，两种标题分别判断。只有字段缺失触发该回退；显式 null 和空字符串仍按之前规则留空。当前实际发生标题回退的只有 SYNC。

下方代码块展示单元格的完整内容，HTML 标记原样显示；这还不是 CSV 引号转义后的文件内容。原文中的换行与空格保留。

父级 `title`、`stripped_title` 及其他字段保持原值；处理后的记录移除整个 `faces`。face 的 `subtypes`、`base_link` 等其他字段不会由这条规则单独导出或合并到父级。

## 1. Dewi Subrotoputri: Pedagogical Dhalang

[查看原始记录](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/cards/dewi_subrotoputri_pedagogical_dhalang.json) · `dewi_subrotoputri_pedagogical_dhalang` · 1 个 face

### text

```text
Whenever you make a successful run, if your [mu] is full, you may flip this identity and gain 1[credit].

<strong>Face:</strong> Dewi Subrotoputri: Shadow Guide
Whenever you make a successful run, if you have at least 1 unused [mu], you may flip this identity and draw 1 card.
```

### stripped_text

```text
Whenever you make a successful run, if your  mu is full, you may flip this identity and gain 1 credit.

Face: Dewi Subrotoputri: Shadow Guide
Whenever you make a successful run, if you have at least 1 unused  mu, you may flip this identity and draw 1 card.
```

## 2. Earth Station: SEA Headquarters

[查看原始记录](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/cards/earth_station_sea_headquarters.json) · `earth_station_sea_headquarters` · 1 个 face

### text

```text
Limit 1 remote server.
As an additional cost to run HQ, the Runner must pay 1[credit].
<strong>[click]:</strong> Flip this identity.

<strong>Face:</strong> Earth Station: Ascending to Orbit
Limit 1 remote server.
As an additional cost to run a remote server, the Runner must pay 6[credit].
When the Runner makes a successful run on HQ, flip this identity.
```

### stripped_text

```text
Limit 1 remote server. As an additional cost to run HQ, the Runner must pay 1 credit. click: Flip this identity.

Face: Earth Station: Ascending to Orbit
Limit 1 remote server. As an additional cost to run a remote server, the Runner must pay 6 credits. When the Runner makes a successful run on HQ, flip this identity.
```

## 3. Hoshiko Shiro: Untold Protagonist

[查看原始记录](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/cards/hoshiko_shiro_untold_protagonist.json) · `hoshiko_shiro_untold_protagonist` · 1 个 face

### text

```text
When your turn ends, if you accessed a card this turn, gain 2[credit] and flip this identity.

<strong>Face:</strong> Hoshiko Shiro: Mahou Shoujo
When your turn begins, draw 1 card and lose 1[credit].
When your turn ends, if you did not access any cards this turn, flip this identity.
```

### stripped_text

```text
When your turn ends, if you accessed a card this turn, gain 2 credits and flip this identity.

Face: Hoshiko Shiro: Mahou Shoujo
When your turn begins, draw 1 card and lose 1 credit. When your turn ends, if you did not access any cards this turn, flip this identity.
```

## 4. Jinteki Biotech: Life Imagined

[查看原始记录](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/cards/jinteki_biotech_life_imagined.json) · `jinteki_biotech_life_imagined` · 3 个 face

### text

```text
Before taking your first turn, you may switch this identity with any copy of Jinteki Biotech.
<strong>[click][click][click]:</strong> Flip this identity.

<strong>Face:</strong> The Brewery: Jinteki Biotech
When you flip this identity, do 2 net damage.

<strong>Face:</strong> The Tank: Jinteki Biotech
When you flip this identity, shuffle all cards in Archives into R&D.

<strong>Face:</strong> The Greenhouse: Jinteki Biotech
When you flip this identity, place 4 advancement counters on 1 installed card that you can advance.
```

### stripped_text

```text
Before taking your first turn, you may switch this identity with any copy of Jinteki Biotech. click click click: Flip this identity.

Face: The Brewery: Jinteki Biotech
When you flip this identity, do 2 net damage.

Face: The Tank: Jinteki Biotech
When you flip this identity, shuffle all cards in Archives into R&D.

Face: The Greenhouse: Jinteki Biotech
When you flip this identity, place 4 advancement counters on 1 installed card that you can advance.
```

## 5. Méliès U: Only the Brightest

[查看原始记录](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/cards/melies_u_only_the_brightest.json) · `melies_u_only_the_brightest` · 3 个 face

### text

```text
When your discard phase ends, secretly set your identity to any copy of Méliès U: Only the Brightest.
When the Runner makes a successful run on a central server, flip this identity.
When the Runner’s action phase ends, gain 1[credit].

<strong>Face:</strong> Tenure Floors: Méliès U
When you flip this identity to this side during a run on HQ, look at the top card of R&D. You may trash that card. If you do, add 1 card from Archives to HQ.
When the Runner’s discard phase ends, flip this identity.

<strong>Face:</strong> Subsurface Labs: Méliès U
When you flip this identity to this side during a run on R&D, look at the top card of R&D. You may trash that card. If you do, add 1 card from Archives to HQ.
When the Runner’s discard phase ends, flip this identity.

<strong>Face:</strong> Disposal Grounds: Méliès U
When you flip this identity to this side during a run on Archives, look at the top card of R&D. You may trash that card. If you do, add 1 card from Archives to HQ.
When the Runner’s discard phase ends, flip this identity.
```

### stripped_text

```text
When your discard phase ends, secretly set your identity to any copy of Melies U: Only the Brightest. When the Runner makes a successful run on a central server, flip this identity. When the Runner's action phase ends, gain 1 credit.

Face: Tenure Floors: Melies U
When you flip this identity to this side during a run on HQ, look at the top card of R&D. You may trash that card. If you do, add 1 card from Archives to HQ. When the Runner's discard phase ends, flip this identity.

Face: Subsurface Labs: Melies U
When you flip this identity to this side during a run on R&D, look at the top card of R&D. You may trash that card. If you do, add 1 card from Archives to HQ. When the Runner's discard phase ends, flip this identity.

Face: Disposal Grounds: Melies U
When you flip this identity to this side during a run on Archives, look at the top card of R&D. You may trash that card. If you do, add 1 card from Archives to HQ. When the Runner's discard phase ends, flip this identity.
```

## 6. Nebula Talent Management: Making Stars

[查看原始记录](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/cards/nebula_talent_management_making_stars.json) · `nebula_talent_management_making_stars` · 1 个 face

### text

```text
When your action phase ends, if you played an operation this turn, gain 1[credit] and flip this identity.

<strong>Face:</strong> Gemilang Arena: Burning Bright
The first time each turn you play an operation, gain [click].
When the Runner makes a successful run on HQ or R&D, flip this identity.
```

### stripped_text

```text
When your action phase ends, if you played an operation this turn, gain 1 credit and flip this identity.

Face: Gemilang Arena: Burning Bright
The first time each turn you play an operation, gain click. When the Runner makes a successful run on HQ or R&D, flip this identity.
```

## 7. SYNC: Everything, Everywhere

[查看原始记录](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/cards/sync_everything_everywhere.json) · `sync_everything_everywhere` · 1 个 face

此 face 的两个标题字段缺失，以下分别使用卡牌原本的 `title`、`stripped_title` 作为标题。

### text

```text
[click]: Flip this identity.
The Runner pays 1[credit] more when spending a [click] to remove a tag (not through a card ability).

<strong>Face:</strong> SYNC: Everything, Everywhere
[click]: Flip this identity.
You may pay 2[credit] fewer when spending a [click] to trash a resource (not through a card ability).
```

### stripped_text

```text
click: Flip this identity. The Runner pays 1 credit more when spending a click to remove a tag (not through a card ability).

Face: SYNC: Everything, Everywhere
click: Flip this identity. You may pay 2 credits fewer when spending a click to trash a resource (not through a card ability).
```
