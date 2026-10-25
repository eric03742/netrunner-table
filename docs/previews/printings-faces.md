# printings.faces 合并效果预览

状态：用户已检查并确认全部 7 条印刷记录的预览，printings.faces 按下述规则处理。完整表结构与文件格式见 [CSV 表结构清单](csv-table-structure.md)。

数据来源固定为上游提交 [6ff880724741ea6ac062acf77e0cf979bb44e67e](https://github.com/Null-Signal-Games/netrunner-cards-json/commit/6ff880724741ea6ac062acf77e0cf979bb44e67e)。包含全部 7 条具有 `faces` 的印刷记录，共 15 个 face。

各个 face 的 flavor 按原数组顺序追加到父级 flavor，段落之间插入一个换行，不添加空行或任何标记。父级 flavor 缺失、null 或空字符串时，直接从第一个 face 的 flavor 开始。各段内部原有文字、换行和标记保留。

处理后移除整个 faces 字段；face 内的 copy_quantity 不导出，也不生成 face_copy_quantities 列。父级 copy_quantity 与其他字段保持原值。下方展示的是 CSV 单元格的完整内容，尚未进行 CSV 文件层面的引号转义。

## 1. Dewi Subrotoputri: Pedagogical Dhalang

印刷 ID：`35023` · 卡牌 ID：`dewi_subrotoputri_pedagogical_dhalang` · 卡包：`elevation` · 1 个 face

[查看原始印刷数据](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/printings/elevation.json)

### flavor

```text
Who else will teach the stories of good and evil?
We can’t leave the corps’ dirty deeds in the shadows.
```

## 2. Earth Station: SEA Headquarters

印刷 ID：`26120` · 卡牌 ID：`earth_station_sea_headquarters` · 卡包：`uprising` · 1 个 face

[查看原始印刷数据](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/printings/uprising.json)

### flavor

```text
The First Step…
…Further Beyond
```

## 3. Hoshiko Shiro: Untold Protagonist

印刷 ID：`26066` · 卡牌 ID：`hoshiko_shiro_untold_protagonist` · 卡包：`uprising` · 1 个 face

[查看原始印刷数据](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/printings/uprising.json)

### flavor

```text
Please, let me have this dream.
I’m going to be my own kind of hero.
```

## 4. Jinteki Biotech: Life Imagined

印刷 ID：`08012` · 卡牌 ID：`jinteki_biotech_life_imagined` · 卡包：`the_valley` · 3 个 face

[查看原始印刷数据](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/printings/the_valley.json)

父级没有非空 flavor，以下直接从第一个 face 的文本开始。

### flavor

```text
The vats in Building C can create-and destroy-life itself.
The best and brightest genengineers spends their days in the Tank dreaming up a better world.
The Greenhouse was built to enhance natural beauty.
```

## 5. Matryoshka

印刷 ID：`33094` · 卡牌 ID：`matryoshka` · 卡包：`parhelion` · 5 个 face

[查看原始印刷数据](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/printings/parhelion.json)

父级 `copy_quantity` 保留为 `1`；face 内的 `copy_quantity` 不导出。

### flavor

```text
She leads and they all follow.
Always there to back up her big sister.
Sheʼs a typical middle child, really.
Just as brave as his sisters.
Sheʼs little—but fierce.
Look at the little baby, even she helps!
```

## 6. Méliès U: Only the Brightest

印刷 ID：`36036` · 卡牌 ID：`melies_u_only_the_brightest` · 卡包：`vantage_point` · 3 个 face

[查看原始印刷数据](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/printings/vantage_point.json)

父级没有非空 flavor，以下直接从第一个 face 的文本开始。

### flavor

```text
Above all else.
Beneath any notice.
Off the record.
```

## 7. Nebula Talent Management: Making Stars

印刷 ID：`35057` · 卡牌 ID：`nebula_talent_management_making_stars` · 卡包：`elevation` · 1 个 face

[查看原始印刷数据](https://github.com/Null-Signal-Games/netrunner-cards-json/blob/6ff880724741ea6ac062acf77e0cf979bb44e67e/v2/printings/elevation.json)

### flavor

```text
Igniting your potential.
Never stop shining.
```
