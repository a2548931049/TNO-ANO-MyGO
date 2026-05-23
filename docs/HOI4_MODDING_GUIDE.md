# HOI4 Mod 制作经验指南

> 面向本项目 `test2` / ANO 子模组的开发笔记。
> 目标不是替代官方文档，而是把本仓库已经踩过的坑、可复用写法和检查流程集中到一起。

---

## 1. 项目开发原则

### 1.1 先让游戏能解析

HOI4 mod 的第一优先级是“不要让解析器炸掉”。一个很小的语法错误可能导致整棵国策树、本地化或 GUI 全部失效。

开发时推荐顺序：

1. 先写最小可解析版本。
2. 进游戏或看 `error.log`。
3. 确认无严重错误后再扩内容。
4. 每次只改一类系统，例如国策、事件、GUI、本地化，不要一次全动。

### 1.2 玩法逻辑和展示层分开

本项目的“内忧外患系统”就是一个典型例子：

- 变量、事件、决议负责实际玩法。
- scripted GUI 负责展示和快捷按钮。
- 本地化负责动态文本。
- GFX 只负责视觉素材。

不要把所有逻辑塞进 UI 文本或决议描述里。UI 看起来能点，不代表它真的有作用；按钮必须在 `common/scripted_guis/` 里绑定 `_click_enabled` 和 `_click`。

### 1.3 优先复用现有系统

如果 TNO 或 HOI4 已经有对应机制，优先使用现成效果、idea、modifier、trait 和 GUI 风格。

本项目适合复用的已有系统：

- TNO 经济变量与 GDP 修改。
- TNO 俄罗斯军阀 Smuta 系统。
- HOI4 决议系统。
- HOI4 scripted localisation。
- HOI4 character 系统。

---

## 2. 文件与编码规范

### 2.1 推荐目录

```text
test2/
├── common/
│   ├── characters/              # 角色、领袖、将领、顾问
│   ├── countries/               # 国家颜色
│   ├── country_tags/            # TAG 注册
│   ├── decisions/               # 决议和决议分类
│   ├── ideas/                   # 民族精神、顾问 trait idea
│   ├── national_focus/          # 国策树
│   ├── on_actions/              # 开局、周期触发
│   ├── scripted_guis/           # GUI 按钮逻辑
│   └── scripted_localisation/   # 动态文本
├── events/                      # 事件链、延迟结算
├── gfx/                         # 图片资源
├── history/                     # 国家、地区、OOB
├── interface/                   # .gui 和 .gfx 定义
├── localisation/simp_chinese/   # 简体中文本地化
└── docs/                        # 项目文档
```

### 2.2 编码要求

- `.yml` 本地化文件必须使用 **UTF-8 BOM**。
- `.yml` 第一行必须是语言声明，例如：

```yml
l_simp_chinese:
```

- `.txt`、`.gui`、`.gfx` 推荐统一 UTF-8。
- 本地化 key 不要重复。HOI4 通常不会报错，只会用后定义的覆盖前定义的。

检查 BOM 的 PowerShell 示例：

```powershell
$bytes = [System.IO.File]::ReadAllBytes("localisation\simp_chinese\ANO_countries_l_simp_chinese.yml")
"{0:X2} {1:X2} {2:X2}" -f $bytes[0],$bytes[1],$bytes[2]
```

输出应为：

```text
EF BB BF
```

---

## 3. Paradox Script 基础经验

### 3.1 语法要点

```txt
key = value
block = {
    nested_key = yes
}
```

注意：

- 布尔值是 `yes` / `no`，不是 `true` / `false`。
- 注释使用 `#`。
- 大括号必须配平。
- 同名 ID 通常是全局唯一，不要重复定义。

快速检查大括号数量：

```powershell
$text = Get-Content -Raw -Encoding UTF8 "common\national_focus\ANO.txt"
$open = ([regex]::Matches($text, "\{")).Count
$close = ([regex]::Matches($text, "\}")).Count
"open=$open close=$close balanced=$($open -eq $close)"
```

### 3.2 常见坑

| 问题 | 后果 | 建议 |
|------|------|------|
| 大括号不配平 | 整个文件解析失败 | 每次大改后静态检查 |
| ID 重复 | 后定义覆盖或解析异常 | 所有 ID 加 `ANO_` 前缀 |
| 本地化 key 重复 | 文本显示错乱 | 提交前搜 key |
| 引用不存在的 GFX | 白图、问号或报错 | `.gfx` 和实际文件同步 |
| 引用不存在的 OOB | `load_oob` 静默失败 | 检查 `history/units/` |
| modifier 拼错 | 效果无效 | 对照已知可用 modifier |

---

## 4. 国家、TAG 与历史文件

### 4.1 TAG 注册

文件：

```text
common/country_tags/00_countries.txt
```

示例：

```txt
ANO = "countries/ANO_Country.txt"
```

### 4.2 国家颜色

文件：

```text
common/countries/ANO_Country.txt
```

示例：

```txt
color = rgb { 193 182 255 }
color_ui = rgb { 193 182 255 }
```

### 4.3 国家历史

文件：

```text
history/countries/ANO - ANOCountry.txt
```

常见内容：

- 首都。
- 科技。
- 政策。
- 起始民族精神。
- 起始领袖。
- 起始 OOB。
- TNO 经济变量。

注意：OOB 的 `location` 用的是 **province ID**，不是 state ID。

---

## 5. 国策树经验

### 5.1 基础结构

```txt
focus_tree = {
    id = ANO_focus_tree
    country = {
        factor = 0
        modifier = {
            add = 10
            tag = ANO
        }
    }
    default = no

    focus = {
        id = ANO_example_focus
        icon = GFX_portrait_ANO_goal
        x = 0
        y = 0
        cost = 10

        completion_reward = {
            add_political_power = 100
        }
    }
}
```

### 5.2 国策 ID 命名

推荐：

```text
ANO_stand_in_the_cold
ANO_back_on_track
ANO_call_to_arkhangelsk
```

不要用中文 ID 或混用旧版本 ID。中文可以放本地化，不要放脚本 ID。

### 5.3 前置关系

AND：

```txt
prerequisite = { focus = ANO_focus_a }
prerequisite = { focus = ANO_focus_b }
```

OR：

```txt
prerequisite = {
    focus = ANO_focus_a
    focus = ANO_focus_b
}
```

### 5.4 循环国策

本项目使用过 `uncomplete_national_focus` 实现循环机：

```txt
completion_reward = {
    add_political_power = 100
    uncomplete_national_focus = ANO_add_political_power
}
```

注意：

- 循环国策容易被玩家刷爆资源，要控制数值。
- 如果有子国策，注意 `uncomplete_children` 的影响。
- 循环国策最好在 UI 或描述里明确说明。

### 5.5 本项目已踩过的国策坑

- `add_field_marshal_role` / `add_corps_commander_role` / `add_advisor_role` 不支持 `activate = yes`。
- TNO 中领袖切换更推荐使用 `promote_character`。
- `add_army_experience` 应写成 `army_experience`。
- `movement` 不是合法 unit modifier。
- `type = cas` 不是有效装备类型，使用具体装备 ID。
- `load_oob = "VOL_Mygo"` 必须保证文件存在于 `history/units/VOL_Mygo.txt`。

---

## 6. Character、领袖与顾问

### 6.1 基础模板

```txt
characters = {
    ANO_character_id = {
        name = ANO_character_id

        portraits = {
            civilian = {
                large = "gfx/leaders/ANO/example.png"
            }
            army = {
                large = "gfx/leaders/ANO/generals/example_general.png"
                small = "gfx/leaders/ANO/generals/example_general.png"
            }
        }

        country_leader = {
            ideology = progressivism_subtype
            expire = "1999.1.1"
            traits = { Tomorin_traits }
        }

        corps_commander = {
            skill = 3
            attack_skill = 3
            defense_skill = 3
            planning_skill = 3
            logistics_skill = 3
            traits = { }
        }
    }
}
```

### 6.2 顾问

```txt
advisor = {
    slot = theorist
    idea_token = ANO_example_theorist
    ledger = army
    allowed = {
        original_tag = ANO
    }
    traits = { military_theorist }
    cost = 100
}
```

经验：

- `idea_token` 必须唯一。
- 顾问 trait 需要在 ideas 或对应 trait 文件中定义。
- 角色可以同时是领袖、将领、顾问，但要确保对应角色被添加或晋升。

### 6.3 开局领袖必须匹配执政意识形态

只写 `promote_character = ANO_Tomorin` 不一定能保证开局国家面板显示高松灯。HOI4 会根据 `set_politics` 的执政意识形态寻找对应的 `country_leader`。如果执政党是 `progressivism`，而千早爱音的领袖意识形态是 `progressivism_subtype`，游戏仍可能把爱音显示为开局领袖。

本项目的修正方式：

```txt
set_popularities = {
    liberal_conservatism = 100
}

set_politics = {
    ruling_party = liberal_conservatism
    last_election = "1959.3.5"
    election_frequency = 48
    elections_allowed = yes
}

recruit_character = ANO_Tomorin
promote_character = ANO_Tomorin
```

排查开局领袖问题时同时检查：

- `history/countries/*.txt` 的 `ruling_party`。
- 目标角色 `country_leader` 的 `ideology`。
- 是否有其他角色拥有当前执政意识形态。
- 是否有开局 `on_action` 或国策立即 `promote_character` 覆盖。

---

## 7. Ideas 与 Modifier

### 7.1 民族精神模板

```txt
ideas = {
    country = {
        ANO_example_idea = {
            allowed = { always = no }
            removal_cost = -1
            picture = GFX_portrait_ANO_icon

            modifier = {
                stability_factor = 0.05
                political_power_factor = 0.10
            }
        }
    }
}
```

### 7.2 常用 modifier

| Modifier | 作用 |
|----------|------|
| `stability_factor` | 稳定度加成 |
| `war_support_factor` | 战争支持度加成 |
| `political_power_factor` | 政治点获取倍率 |
| `research_speed_factor` | 科研速度 |
| `industrial_capacity_factory` | 工厂产出 |
| `production_speed_buildings_factor` | 建造速度 |
| `production_factory_max_efficiency_factor` | 生产效率上限 |
| `army_attack_factor` | 陆军攻击 |
| `army_defence_factor` | 陆军防御 |
| `weekly_manpower` | 每周人力 |
| `recruitable_population_factor` | 适役人口比例 |
| `consumer_goods_factor` | 消费品工厂比例 |

注意：不要凭感觉造 modifier 名。拼错通常不会产生效果。

---

## 8. 决议系统

### 8.1 决议分类

文件：

```text
common/decisions/categories/ANO_decision_categories.txt
```

示例：

```txt
ANO_influence_category = {
    icon = generic_political_actions

    allowed = { tag = ANO }
    visible = { tag = ANO }

    visible_when_empty = no
    priority = 50

    scripted_gui = ANO_influence_sg
}
```

### 8.2 决议模板

```txt
ANO_influence_category = {
    ANO_example_decision = {
        icon = generic_political_discourse

        available = {
            has_political_power > 99
        }
        visible = {
            tag = ANO
        }

        fire_only_once = no
        days_remove = 30

        complete_effect = {
            add_political_power = -100
        }

        remove_effect = {
            add_to_variable = { ANO_influence = 10 }
        }
    }
}
```

### 8.3 决议设计经验

- `complete_effect` 是点击时发生。
- `remove_effect` 是倒计时结束时发生。
- 需要延迟结算的行动，可以用决议，也可以用 GUI 按钮触发事件。
- 玩家常用行动可以做成 GUI 快捷按钮，完整列表仍保留在决议里。

---

## 9. Scripted GUI 制作经验

### 9.1 GUI 文件只负责布局

文件：

```text
interface/ANO_influence.gui
```

按钮示例：

```txt
buttonType = {
    name = "ANO_influence_support_button"
    position = { x = 111 y = 194 }
    quadTextureSprite = "GFX_ANO_influence_icon_support"
    buttonText = ""
    buttonFont = "hoi_16mbs"
    tooltip = "ANO_influence_support_button_tt"
}
```

经验：

- 相同功能的一排按钮必须统一 `y` 坐标。
- 按钮间距要固定，例如 48px 图标 + 3px 间隔。
- 文本区要留足高度，不要让说明文字压到按钮。
- 面板只是展示，按钮是否能点和点了做什么在 `common/scripted_guis/`。

### 9.2 Scripted GUI 绑定按钮

文件：

```text
common/scripted_guis/ANO_influence_gui.txt
```

示例：

```txt
scripted_gui = {
    ANO_influence_sg = {
        context_type = decision_category
        window_name = "ANO_influence_panel"

        visible = { tag = ANO }

        triggers = {
            ANO_influence_support_button_click_enabled = {
                has_political_power > 99
            }
        }

        effects = {
            ANO_influence_support_button_click = {
                add_political_power = -100
                country_event = { id = ANO_influence.20 days = 30 }
            }
        }

        ai_enabled = { always = no }
    }
}
```

命名规则：

```text
按钮名 = ANO_influence_support_button
可点击条件 = ANO_influence_support_button_click_enabled
点击效果 = ANO_influence_support_button_click
```

### 9.3 GUI 最常见问题

| 问题 | 原因 | 解决 |
|------|------|------|
| 按钮显示但不能点 | 没有 `_click_enabled` 或条件不满足 | 检查 scripted GUI triggers |
| 点了没效果 | 没有 `_click` effect | 检查 effects 块 |
| 图标错位 | 坐标不统一 | 统一按钮 y 和间距 |
| 文本重叠 | `maxHeight` 太大或区域太近 | 缩短文案，调整 y |
| 白图 | sprite 没定义或路径错 | 检查 `.gfx` 和文件名 |
| GUI 出现在决议下方但决议也很长 | 面板和列表都显示 | 将常用行动做按钮，完整行动保留为列表 |
| 数值不显示 | 变量未初始化、scripted localisation 未接入，或本地化 key 不存在 | 在历史文件初始化变量，给 GUI 文本绑定 `[DefinedText]`，补齐 key |
| 决议消失 | 重做 GUI 时只保留 scripted GUI，遗漏原决议文件或分类绑定 | 决议分类继续挂 `scripted_gui`，实际行动仍放在 `common/decisions/` |
| 风格接近但字体不对 | 使用了不适合 TNO 决议面板的 font | 优先用 `hoi_18b`、`hoi_18mbs`、`hoi_20b` 等现有字体实机比对 |

---

## 10. 事件与延迟结算

### 10.1 自循环事件

本项目内忧外患系统每 14 天 tick 一次：

```txt
country_event = {
    id = ANO_influence.1
    hidden = yes
    fire_only_once = no

    trigger = {
        tag = ANO
        exists = yes
    }

    immediate = {
        add_to_variable = { ANO_remnant_power = 50 }
        add_to_variable = { ANO_central_power = 50 }
        clamp_variable = { var = ANO_remnant_power min = 0 max = 1000 }
        clamp_variable = { var = ANO_central_power min = 0 max = 1000 }

        if = {
            limit = {
                check_variable = { ANO_remnant_power > 999 }
                NOT = { has_country_flag = ANO_remnant_coup_countdown }
            }
            set_country_flag = ANO_remnant_coup_countdown
            country_event = { id = ANO_influence.30 days = 1 }
            country_event = { id = ANO_influence.31 days = 14 }
        }

        country_event = { id = ANO_influence.1 days = 14 }
    }
}
```

启动方式：

```txt
on_actions = {
    on_startup = {
        effect = {
            ANO = {
                country_event = { id = ANO_influence.1 days = 14 }
            }
        }
    }
}
```

### 10.2 GUI 按钮延迟结算

GUI 按钮没有 `days_remove`，如果要做“30 天后生效”，可以点击时触发一个延迟事件：

```txt
effects = {
    ANO_influence_support_button_click = {
        add_political_power = -100
        set_country_flag = ANO_support_operation_underway
        country_event = { id = ANO_influence.20 days = 30 }
    }
}
```

事件：

```txt
country_event = {
    id = ANO_influence.20
    hidden = yes
    is_triggered_only = yes
    fire_only_once = no

    immediate = {
        clr_country_flag = ANO_support_operation_underway
        add_to_variable = { ANO_influence = 10 }
    }
}
```

经验：

- 用 country flag 防止重复点击同一行动。
- 延迟事件结束时清 flag。
- 随机结算用 `random_list`。
- 隐藏事件也要有 `option`，避免奇怪日志问题。

---

## 11. Scripted Localisation

### 11.1 动态文本模板

```txt
defined_text = {
    name = ANO_GetGreatGameStatus

    text = {
        trigger = {
            tag = ANO
            check_variable = { WRS_influence > 89 }
        }
        localization_key = ANO_great_game_status_critical
    }

    text = {
        trigger = { tag = ANO }
        localization_key = ANO_great_game_status_contested
    }

    text = {
        localization_key = ANO_influence_display_hidden
    }
}
```

GUI 中调用：

```txt
text = "[ANO_GetGreatGameStatus]"
```

本地化中显示变量：

```yml
ANO_great_game_score_line:0 "§H阿诺支持度§! §Y[?ANO_influence]§! / 55"
```

经验：

- 条件从严格到宽松排列。
- 最后放 fallback。
- 动态文本不要太长，GUI 很容易挤压。

---

## 12. GFX 与界面贴图

### 12.1 Sprite 定义

文件：

```text
interface/ANO_influence.gfx
```

示例：

```txt
spriteTypes = {
    spriteType = {
        name = "GFX_ANO_influence_panel_bg"
        texturefile = "gfx/interface/ano_influence/ANO_influence_panel_bg.png"
    }
}
```

GUI 引用：

```txt
iconType = {
    name = "ANO_influence_panel_bg"
    position = { x = 0 y = 0 }
    spriteType = "GFX_ANO_influence_panel_bg"
}
```

按钮引用：

```txt
quadTextureSprite = "GFX_ANO_influence_icon_support"
```

### 12.2 图像格式经验

- 国旗通常使用 `.tga`。
- 国策图标推荐 `.dds`，`.png` 有时会显示异常。
- GUI 贴图可以用 `.png`，但要实机确认。
- 文件名大小写保持一致。
- 尽量不要用空格和中文文件名。

### 12.3 UI 视觉经验

从本项目“内忧外患”面板总结：

- 先画背景框，再放文字，再放按钮，再放地图。
- 一排按钮必须统一基线。
- 文字说明要短，不要让叙事文本占满操作区域。
- 按钮 tooltip 写完整说明，面板正文只写状态。
- 面板中“看起来像按钮”的元素必须真的是 `buttonType`。

---

## 13. OOB 与军队

### 13.1 OOB 模板

```txt
division_template = {
    name = "Infantry Division"
    regiments = {
        infantry = { x = 0 y = 0 }
        infantry = { x = 0 y = 1 }
    }
    support = {
        engineer = { x = 0 y = 0 }
    }
}

units = {
    division = {
        name = "1st Infantry Division"
        location = 1234
        division_template = "Infantry Division"
        start_experience_factor = 0.3
        start_equipment_factor = 0.8
    }
}
```

### 13.2 OOB 检查

如果国策中写：

```txt
load_oob = "VOL_Mygo"
```

那么必须存在：

```text
history/units/VOL_Mygo.txt
```

经验：

- OOB 文件缺失通常不会直接崩溃，但会在日志里报错或静默失败。
- `location` 是 province，不是 state。
- 模板名必须和 division 引用完全一致。

---

## 14. TNO 特有经验

### 14.1 经济系统

```txt
set_variable = { TNO_economy_type = token:Econ_Type_Capitalism }
set_variable = { TNO_economy_subtype = token:Econ_Subtype_Dirigisme }
set_temp_variable = { gdp_growth_temp = 10.5 }
econ_gdp_growth_change = yes
```

### 14.2 俄罗斯系统

```txt
set_country_flag = is_russian_nation
set_country_flag = is_west_russian_warlord
RUS_Smuta_Enable = yes
RUS_Smuta_Disable = yes
```

### 14.3 常用 gate

```txt
available = {
    has_global_flag = hitler_dead
    NOT = { has_idea = RUS_terror_bombing }
}
```

经验：

- TNO 的效果和变量很多是自定义的，原版 HOI4 不一定有。
- 引用 TNO 内容前，确认本体是否定义了对应 idea、trait、scripted effect。
- 子模组尽量少覆盖 TNO 原文件，优先追加自己的文件。

---

## 15. 本地化写作规范

### 15.1 Key 命名

推荐：

```text
ANO_focus_id
ANO_focus_id_desc
ANO_decision_id
ANO_decision_id_desc
ANO_event.1.t
ANO_event.1.desc
ANO_event.1.a
```

### 15.2 文案长度

- 国策描述可以长。
- 决议描述中等。
- GUI 面板文本必须短。
- Tooltip 可以详细解释成本、效果、耗时。

### 15.3 颜色代码

```text
§G绿色§!
§R红色§!
§Y黄色§!
§H高亮§!
§L灰色说明§!
```

注意：

- 每个颜色开始符号最好有对应 `§!` 结束。
- 不要在 GUI 小文本框里塞太多颜色代码，容易溢出。

---

## 16. 调试清单

### 16.1 每次改脚本后

检查：

- 大括号是否配平。
- 新 ID 是否重复。
- 新 GFX 是否有 sprite 定义。
- 新本地化 key 是否存在。
- `.yml` 是否仍有 BOM。
- 国策引用的 OOB 是否存在。

### 16.2 进游戏后

检查：

- 启动是否报错。
- 国家是否能选。
- 国策树是否显示。
- 本地化是否缺 key。
- 决议是否出现。
- GUI 是否错位。
- 按钮是否能点。
- 点按钮后变量、idea、flag 是否变化。
- 延迟事件是否按时结算。

### 16.3 日志重点

查看：

```text
Documents/Paradox Interactive/Hearts of Iron IV/logs/error.log
```

重点搜：

```text
Unexpected token
Unknown effect
Unknown trigger
Unknown modifier
Could not find sprite
Invalid focus
Could not load OOB
localisation
```

---

## 17. 提交前清单

### 17.1 本次内忧外患更新排错记录

2026-05-24 这轮更新中遇到的问题和处理方式：

| 问题 | 原因 | 处理 |
|------|------|------|
| 内忧外患 GUI 有画面但力量数值为空 | 新 GUI 只摆了视觉元素，没有完整接回变量文本与变量初始化 | 增加 `ANO_remnant_power`、`ANO_central_power` 开局变量，在本地化中使用 `[?ANO_remnant_power|0]` / `[?ANO_central_power|0]`，并用 `ANO_GetInternalExternalCountdown` 显示倒计时状态 |
| 旧决议系统看起来“不见了” | GUI 重做时把展示层和决议层割裂，决议分类仍显示面板但行动未融合 | 保留 `ANO_influence_category`，恢复并改写 13 个决议，让它们操作残党/中央两条压力 |
| 参考图中两侧装饰框顺序相反 | `frame.png` 与 `frame_2.png` 的左右用途写反 | `GFX_ANO_ie_frame_left` 使用 `frame.png`，`GFX_ANO_ie_frame_right` 使用 `frame_2.png` |
| 高松灯开局头像没有使用新素材 | character portrait 和 `ano_portraits.gfx` 仍指向旧 PNG | 新增 `gfx/leaders/ANO/ANO_Tomorin_leader.dds`，并同步更新角色与 sprite |
| 开局领袖仍是千早爱音 | ANO 开局执政党是 `progressivism`，爱音的 leader ideology 匹配该党派；高松灯是 `liberal_conservatism_big_tent_subtype` | 将开局 `set_popularities` 与 `ruling_party` 改为 `liberal_conservatism`，并保留 `promote_character = ANO_Tomorin` |
| 国策引用 OOB 可能报错 | `VOL_BorderGuard`、`VOL_Mygo`、`GOR_1962` 曾经未纳入仓库 | 补充对应 `history/units/*.txt`，避免 `Could not load OOB` |

### 17.2 通用提交前清单

提交前建议确认：

- [ ] 游戏能正常启动。
- [ ] `error.log` 没有本次新增内容导致的严重错误。
- [ ] 新增本地化为 UTF-8 BOM。
- [ ] 没有重复 key。
- [ ] 没有引用不存在的图片。
- [ ] 没有引用不存在的 OOB。
- [ ] GUI 文字没有重叠。
- [ ] 按钮不只是显示，确实绑定了效果。
- [ ] `README.md` 或 `CHANGELOG.md` 已记录重要变化。

---

## 18. 本项目当前重点待办

从已有文档和开发过程看，优先级较高的事项：

1. 进游戏实测“内忧外患” GUI，确认力量文本、倒计时文本和决议列表都能正常显示。
2. 检查 `error.log` 是否有 GUI sprite、buttonType、scripted localisation 报错。
3. 将仍为 `.png` 的国策图标转为 `.dds`，避免白框。
4. 实测残党/中央力量到达 1000 后的 14 天倒计时与结局效果。
5. 清理或归档旧版/重复文件，避免同名 ID 冲突。
6. 将立希线占位结局扩展为完整剧情。

---

## 19. 推荐工作流

### 新增一个国策

1. 在 `common/national_focus/ANO.txt` 加 focus。
2. 在本地化文件加标题和描述。
3. 确认 icon sprite 存在。
4. 加 completion reward。
5. 检查前置和坐标。
6. 进游戏看国策树。

### 新增一个决议

1. 确认决议分类存在。
2. 在 `common/decisions/` 加 decision。
3. 写 `visible`、`available`、`complete_effect`。
4. 如需延迟，用 `days_remove` + `remove_effect`。
5. 加本地化。
6. 进游戏点击测试。

### 新增一个 GUI 按钮

1. 在 `.gui` 里添加 `buttonType`。
2. 在 `.gfx` 里定义按钮贴图。
3. 在 `common/scripted_guis/` 里添加 `_click_enabled`。
4. 在 `common/scripted_guis/` 里添加 `_click`。
5. 加 tooltip 本地化。
6. 进游戏确认按钮可点、条件正确、效果生效。

### 新增一个事件链

1. 在 `events/` 文件中 `add_namespace`。
2. 编写 `country_event`。
3. 写 title、desc、option 本地化。
4. 从国策、决议、GUI 或 on_action 触发。
5. 如果有随机结果，使用 `random_list`。
6. 如果有周期循环，确保不会重复启动多个循环。

---

## 20. 一句话总结

HOI4 mod 开发最重要的是：**ID 清楚、文件可解析、本地化可读、引用都存在、按钮真的有逻辑、每次改动都看日志。**
