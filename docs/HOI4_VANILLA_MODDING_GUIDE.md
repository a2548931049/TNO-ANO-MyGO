# HOI4 原版 Mod 制作指南

> 适用于不依赖 TNO、Kaiserreich、RT56 等大型框架的 **原版 Hearts of Iron IV** 项目。
> 写作目标：让一个新项目从“空文件夹”走到“能进游戏、能选国家、能点国策、能触发事件、能排错”。

---

## 1. 原版项目的基本思路

原版 mod 开发和大型框架子模组最大的区别是：**你不能默认某个系统已经存在**。

在 TNO 子模组里可以调用 TNO 的经济、俄罗斯、GUI、社会发展度、专属 idea 和 scripted effect；原版项目里这些都没有。原版项目应优先使用 HOI4 本体已有系统：

- 国家 TAG
- 国策树
- 事件
- 决议
- ideas / national spirits
- characters
- OOB
- 装备、科技、单位
- scripted triggers / effects / localisation
- interface / GFX

建议开发顺序：

1. 建立 mod 骨架。
2. 让启动器识别 mod。
3. 新增或修改一个国家。
4. 加本地化、国旗、国家历史。
5. 加最小国策树。
6. 加事件与决议。
7. 再扩展角色、顾问、科技、装备、GUI。
8. 每一步都进游戏或看 `error.log`。

---

## 2. 推荐项目结构

一个原版 mod 的典型结构：

```text
YourMod/
├── descriptor.mod
├── common/
│   ├── characters/
│   ├── countries/
│   ├── country_tags/
│   ├── decisions/
│   │   └── categories/
│   ├── ideas/
│   ├── national_focus/
│   ├── on_actions/
│   ├── scripted_effects/
│   ├── scripted_guis/
│   ├── scripted_localisation/
│   ├── scripted_triggers/
│   ├── technologies/
│   └── units/
├── events/
├── gfx/
│   ├── flags/
│   │   ├── medium/
│   │   └── small/
│   ├── interface/
│   └── leaders/
├── history/
│   ├── countries/
│   ├── states/
│   └── units/
├── interface/
├── localisation/
│   └── simp_chinese/
└── map/                    # 只有做地图或省份大改时才需要
```

经验：

- 只创建你实际需要的目录。
- 不要整包复制原版 `common/`、`history/`、`events/`，容易制造冲突。
- 能追加就追加，必须覆盖时再考虑 `replace_path`。

---

## 3. Mod 描述文件

### 3.1 descriptor.mod

mod 根目录需要：

```text
descriptor.mod
```

示例：

```txt
version="0.1.0"
tags={
    "Gameplay"
}
name="My Vanilla HOI4 Mod"
supported_version="1.17.*"
```

如果有依赖：

```txt
dependencies={
    "Some Required Mod"
}
```

原版项目通常不写 `dependencies`。

### 3.2 启动器 .mod 文件

在用户文档目录：

```text
Documents/Paradox Interactive/Hearts of Iron IV/mod/
```

通常还需要一个同名 `.mod` 文件指向项目路径：

```txt
version="0.1.0"
tags={
    "Gameplay"
}
name="My Vanilla HOI4 Mod"
supported_version="1.17.*"
path="C:/Users/YourName/Documents/Paradox Interactive/Hearts of Iron IV/mod/MyMod"
```

注意：

- `descriptor.mod` 放在 mod 文件夹里面。
- 外层 `.mod` 文件放在 `mod/` 目录下。
- `path` 用 `/` 更稳，不要漏引号。
- Steam 创意工坊发布后可能会改用 `archive` 或 `remote_file_id`。

---

## 4. 编码与本地化

### 4.1 本地化文件

简体中文路径：

```text
localisation/simp_chinese/my_mod_l_simp_chinese.yml
```

文件必须使用 **UTF-8 BOM**，第一行：

```yml
l_simp_chinese:
```

示例：

```yml
l_simp_chinese:
 MYMOD_test_focus:0 "测试国策"
 MYMOD_test_focus_desc:0 "这是一个测试国策。"
 MYMOD_event.1.t:0 "测试事件"
 MYMOD_event.1.desc:0 "事件描述。"
 MYMOD_event.1.a:0 "好的"
```

### 4.2 检查 BOM

PowerShell：

```powershell
$bytes = [System.IO.File]::ReadAllBytes("localisation\simp_chinese\my_mod_l_simp_chinese.yml")
"{0:X2} {1:X2} {2:X2}" -f $bytes[0],$bytes[1],$bytes[2]
```

应输出：

```text
EF BB BF
```

### 4.3 本地化常见问题

| 现象 | 常见原因 |
|------|----------|
| 游戏里显示 key 而不是中文 | key 没写、文件名/路径错误、首行语言错误 |
| 整个本地化文件不生效 | 没有 BOM、首行前有空字符、缩进异常 |
| 文字被另一个文本覆盖 | key 重复 |
| 事件标题不显示 | 事件脚本引用的 title key 和 yml 不一致 |

---

## 5. 新增国家

新增一个国家通常至少需要：

```text
common/country_tags/00_countries.txt
common/countries/TAG_Country.txt
history/countries/TAG - CountryName.txt
gfx/flags/TAG.tga
gfx/flags/medium/TAG.tga
gfx/flags/small/TAG.tga
localisation/simp_chinese/*.yml
```

### 5.1 TAG 注册

```txt
AAA = "countries/AAA_Country.txt"
```

### 5.2 国家颜色

```txt
color = rgb { 120 80 200 }
color_ui = rgb { 120 80 200 }
```

### 5.3 国家历史

```txt
capital = 123

set_politics = {
    ruling_party = democratic
    last_election = "1936.1.1"
    election_frequency = 48
    elections_allowed = yes
}

set_popularities = {
    democratic = 60
    fascism = 10
    communism = 10
    neutrality = 20
}

set_research_slots = 3

add_ideas = {
    AAA_starting_spirit
}

set_technology = {
    infantry_weapons = 1
    tech_support = 1
    tech_engineers = 1
}

oob = "AAA_1936"
```

### 5.4 国家本地化

```yml
 AAA_democratic:0 "阿尔法共和国"
 AAA_democratic_DEF:0 "阿尔法共和国"
 AAA_democratic_ADJ:0 "阿尔法"
 AAA_fascism:0 "阿尔法国"
 AAA_fascism_DEF:0 "阿尔法国"
 AAA_communism:0 "阿尔法公社"
 AAA_communism_DEF:0 "阿尔法公社"
 AAA_neutrality:0 "阿尔法"
 AAA_neutrality_DEF:0 "阿尔法"
```

经验：

- 原版四大意识形态：`democratic`、`fascism`、`communism`、`neutrality`。
- `_DEF` 是完整国名。
- `_ADJ` 是形容词。
- 如果不写某意识形态国名，切 ideology 后可能显示 key。

---

## 6. 修改或新增州

州历史文件在：

```text
history/states/
```

示例：

```txt
state = {
    id = 123
    name = "STATE_123"
    manpower = 500000

    state_category = town

    resources = {
        steel = 8
    }

    history = {
        owner = AAA
        add_core_of = AAA

        buildings = {
            infrastructure = 3
            arms_factory = 1
            industrial_complex = 2
        }
    }

    provinces = {
        1001 1002 1003
    }
}
```

经验：

- 如果只是让国家拥有原版州，改 `history/states` 即可。
- 如果新增 province、修改地图颜色、地形、补给节点，那是地图 modding，复杂度会大幅上升。
- `owner`、`controller`、`add_core_of` 要分清。
- OOB 里的 `location` 是 province ID，不是 state ID。

---

## 7. OOB 与起始部队

文件：

```text
history/units/AAA_1936.txt
```

模板：

```txt
division_template = {
    name = "步兵师"
    regiments = {
        infantry = { x = 0 y = 0 }
        infantry = { x = 0 y = 1 }
        infantry = { x = 0 y = 2 }
        infantry = { x = 1 y = 0 }
        infantry = { x = 1 y = 1 }
        infantry = { x = 1 y = 2 }
    }
    support = {
        engineer = { x = 0 y = 0 }
    }
}

units = {
    division = {
        name = "第1步兵师"
        location = 1001
        division_template = "步兵师"
        start_experience_factor = 0.3
        start_equipment_factor = 0.8
    }
}
```

常见问题：

- `division_template` 名称必须完全一致。
- `location` 必须是该国控制范围内的 province。
- 装备不足会导致师团开局缺装备，但不是语法错误。
- 文件名要和国家历史里的 `oob = "AAA_1936"` 一致。

---

## 8. 角色、领袖、将领与顾问

原版现代版本推荐使用 character 系统。

文件：

```text
common/characters/AAA.txt
```

示例：

```txt
characters = {
    AAA_john_smith = {
        name = AAA_john_smith

        portraits = {
            civilian = {
                large = "gfx/leaders/AAA/john_smith.png"
            }
            army = {
                large = "gfx/leaders/AAA/john_smith_general.png"
                small = "gfx/leaders/AAA/john_smith_general.png"
            }
        }

        country_leader = {
            ideology = liberalism
            expire = "1965.1.1"
            traits = { }
        }

        corps_commander = {
            skill = 3
            attack_skill = 3
            defense_skill = 2
            planning_skill = 3
            logistics_skill = 2
            traits = { infantry_leader }
        }

        advisor = {
            slot = political_advisor
            idea_token = AAA_john_smith_advisor
            allowed = { original_tag = AAA }
            traits = { silent_workhorse }
            cost = 150
        }
    }
}
```

历史文件中设置领袖：

```txt
recruit_character = AAA_john_smith
promote_character = AAA_john_smith
```

本地化：

```yml
 AAA_john_smith:0 "约翰·史密斯"
```

经验：

- `name = AAA_john_smith` 对应本地化 key。
- 将领和顾问需要先 `recruit_character`。
- 领袖 ideology 不是四大 ideology，而是其子意识形态，例如 `liberalism`、`marxism`、`despotism` 等，具体以原版定义为准。
- 顾问 trait 必须是原版已有或你自己定义的合法 trait。

---

## 9. Ideas 与民族精神

文件：

```text
common/ideas/AAA_ideas.txt
```

模板：

```txt
ideas = {
    country = {
        AAA_starting_spirit = {
            allowed = { always = no }
            removal_cost = -1
            picture = generic_political_reform

            modifier = {
                stability_factor = 0.05
                political_power_factor = 0.10
            }
        }
    }
}
```

本地化：

```yml
 AAA_starting_spirit:0 "新生国家"
 AAA_starting_spirit_desc:0 "这个国家正在寻找自己的方向。"
```

常用 modifier：

| Modifier | 作用 |
|----------|------|
| `stability_factor` | 稳定度加成 |
| `war_support_factor` | 战争支持度加成 |
| `political_power_factor` | 政治点获取倍率 |
| `research_speed_factor` | 科研速度 |
| `production_speed_buildings_factor` | 建筑建造速度 |
| `industrial_capacity_factory` | 军工厂产出 |
| `industrial_capacity_dockyard` | 船坞产出 |
| `army_attack_factor` | 陆军攻击 |
| `army_defence_factor` | 陆军防御 |
| `conscription_factor` | 征兵人口 |
| `consumer_goods_factor` | 消费品工厂比例 |

经验：

- 不要自己猜 modifier 名。
- `consumer_goods_factor` 正数通常更差，负数通常更好。
- idea 图片如果用自定义 GFX，要在 `.gfx` 中定义。

---

## 10. 国策树

文件：

```text
common/national_focus/AAA_focus.txt
```

最小模板：

```txt
focus_tree = {
    id = AAA_focus_tree

    country = {
        factor = 0
        modifier = {
            add = 10
            tag = AAA
        }
    }

    default = no

    focus = {
        id = AAA_start
        icon = GFX_goal_generic_political_pressure
        x = 0
        y = 0
        cost = 10

        completion_reward = {
            add_political_power = 100
        }

        ai_will_do = {
            factor = 1
        }
    }
}
```

本地化：

```yml
 AAA_start:0 "国家起步"
 AAA_start_desc:0 "我们必须先建立基本秩序。"
```

### 10.1 前置关系

AND：

```txt
prerequisite = { focus = AAA_a }
prerequisite = { focus = AAA_b }
```

OR：

```txt
prerequisite = {
    focus = AAA_a
    focus = AAA_b
}
```

互斥：

```txt
mutually_exclusive = { focus = AAA_left_path }
```

### 10.2 常用国策效果

```txt
completion_reward = {
    add_political_power = 100
    add_stability = 0.05
    add_war_support = 0.05
    army_experience = 25
    add_ideas = AAA_new_spirit
    remove_ideas = AAA_old_spirit
    add_tech_bonus = {
        name = AAA_industry_bonus
        bonus = 0.5
        uses = 1
        category = industry
    }
}
```

经验：

- `cost = 10` 通常约 70 天。
- focus ID 必须全局唯一。
- 坐标 `x`、`y` 决定国策树布局，不要重叠。
- 自定义国策图标建议使用 `.dds`。

---

## 11. 事件

文件：

```text
events/AAA_events.txt
```

模板：

```txt
add_namespace = AAA

country_event = {
    id = AAA.1
    title = AAA.1.t
    desc = AAA.1.desc
    picture = GFX_report_event_generic_political

    is_triggered_only = yes

    option = {
        name = AAA.1.a
        add_political_power = 50
    }
}
```

触发：

```txt
country_event = { id = AAA.1 }
```

延迟触发：

```txt
country_event = { id = AAA.1 days = 30 }
```

本地化：

```yml
 AAA.1.t:0 "新的局势"
 AAA.1.desc:0 "国内局势正在变化。"
 AAA.1.a:0 "我们会处理"
```

经验：

- `is_triggered_only = yes` 表示不会自动触发，只能由脚本调用。
- 隐藏事件用 `hidden = yes`。
- 多结果事件用多个 `option` 或 `random_list`。
- 大事件链要用 flag 防止重复触发。

---

## 12. 决议

### 12.1 决议分类

文件：

```text
common/decisions/categories/AAA_decision_categories.txt
```

```txt
AAA_category = {
    icon = generic_political_actions

    allowed = {
        tag = AAA
    }

    visible = {
        tag = AAA
    }

    priority = 50
}
```

### 12.2 决议

文件：

```text
common/decisions/AAA_decisions.txt
```

```txt
AAA_category = {
    AAA_raise_support = {
        icon = generic_political_discourse

        visible = {
            tag = AAA
        }

        available = {
            has_political_power > 49
        }

        fire_only_once = no
        days_remove = 30

        complete_effect = {
            add_political_power = -50
        }

        remove_effect = {
            add_stability = 0.03
        }

        ai_will_do = {
            factor = 1
        }
    }
}
```

经验：

- `complete_effect` 是点击时执行。
- `remove_effect` 是倒计时结束后执行。
- `days_remove` 会让决议变成限时任务。
- `visible` 控制是否显示，`available` 控制是否可点。

---

## 13. Scripted Trigger 与 Scripted Effect

当条件或效果重复出现时，应抽成脚本。

### 13.1 Scripted Trigger

文件：

```text
common/scripted_triggers/AAA_scripted_triggers.txt
```

```txt
AAA_can_begin_reform = {
    tag = AAA
    has_stability > 0.4
    has_political_power > 49
}
```

使用：

```txt
available = {
    AAA_can_begin_reform = yes
}
```

### 13.2 Scripted Effect

文件：

```text
common/scripted_effects/AAA_scripted_effects.txt
```

```txt
AAA_add_reform_progress = {
    add_to_variable = { AAA_reform_progress = 10 }
    clamp_variable = { var = AAA_reform_progress min = 0 max = 100 }
}
```

使用：

```txt
completion_reward = {
    AAA_add_reform_progress = yes
}
```

经验：

- 复杂逻辑抽出来后，国策和决议更干净。
- scripted trigger/effect 命名必须有项目统一前缀。
- 参数化写法更高级，初期可以先不用。

---

## 14. 变量、Flag 与进度系统

### 14.1 变量

```txt
set_variable = { AAA_progress = 0 }
add_to_variable = { AAA_progress = 10 }
subtract_from_variable = { AAA_progress = 5 }
clamp_variable = { var = AAA_progress min = 0 max = 100 }
```

判断：

```txt
check_variable = { AAA_progress > 49 }
```

本地化显示：

```yml
 AAA_progress_text:0 "当前进度：[?AAA_progress|0]"
```

### 14.2 Country Flag

```txt
set_country_flag = AAA_reform_started
has_country_flag = AAA_reform_started
clr_country_flag = AAA_reform_started
```

经验：

- 变量适合数值进度。
- flag 适合记录阶段、开关、是否触发过。
- 延迟事件或 GUI 按钮最好用 flag 防重复点击。

---

## 15. On Actions

文件：

```text
common/on_actions/AAA_on_actions.txt
```

示例：

```txt
on_actions = {
    on_startup = {
        effect = {
            AAA = {
                set_variable = { AAA_progress = 0 }
            }
        }
    }

    on_daily = {
        effect = {
            AAA = {
                if = {
                    limit = { has_country_flag = AAA_reform_started }
                    add_to_variable = { AAA_progress = 1 }
                }
            }
        }
    }
}
```

经验：

- `on_daily`、`on_weekly` 可能影响性能，条件要写窄。
- 不要对所有国家每天跑复杂脚本。
- 周期系统可以用自循环隐藏事件替代。

---

## 16. 科技、装备与单位

### 16.1 科技

科技文件在：

```text
common/technologies/
```

原版科技树结构比较复杂。新手建议：

- 先给国家已有科技。
- 先做国策科技 bonus。
- 不要一开始重做科技树。

国策科技加成：

```txt
add_tech_bonus = {
    name = AAA_industry_bonus
    bonus = 0.5
    uses = 1
    category = industry
}
```

### 16.2 装备

装备定义涉及：

```text
common/units/equipment/
```

新手建议：

- 如果只是给装备，使用原版装备 ID。
- 如果新增装备，要同步科技、生产、图标、本地化。

常见原版装备示例：

```txt
add_equipment_to_stockpile = {
    type = infantry_equipment_1
    amount = 1000
    producer = AAA
}
```

### 16.3 单位

单位定义在：

```text
common/units/
```

新手通常不用改单位定义，只需 OOB 使用原版 battalion：

- `infantry`
- `artillery_brigade`
- `cavalry`
- `motorized`
- `light_armor`
- `engineer`
- `recon`

---

## 17. GFX 与界面资源

### 17.1 GFX 定义

文件：

```text
interface/AAA_sprites.gfx
```

```txt
spriteTypes = {
    spriteType = {
        name = "GFX_AAA_leader_john_smith"
        texturefile = "gfx/leaders/AAA/john_smith.png"
    }
    spriteType = {
        name = "GFX_goal_AAA_reform"
        texturefile = "gfx/interface/goals/AAA_reform.dds"
    }
}
```

### 17.2 常见资源尺寸

| 资源 | 常见格式 |
|------|----------|
| 国旗 | `.tga`，含大/中/小三种 |
| 领袖肖像 | `.png` 或 `.dds` |
| 国策图标 | 推荐 `.dds` |
| 决议图标 | 可复用原版 generic 图标 |
| GUI 贴图 | `.png` 或 `.dds`，实机检查 |

经验：

- 文件路径区分大小写的问题在不同环境可能表现不同，保持严格一致。
- 国策图标白框时，优先检查 `.gfx` 名称和图片格式。
- 不要让 UI 元素只是图片，玩家能点的地方必须是 `buttonType` 或决议。

---

## 18. Scripted GUI 简明规则

原版也可以做 scripted GUI，但建议等国策、事件、决议稳定后再做。

GUI 文件：

```text
interface/AAA_panel.gui
```

脚本绑定：

```text
common/scripted_guis/AAA_scripted_gui.txt
```

按钮命名规则：

```text
按钮名 = AAA_action_button
可点击条件 = AAA_action_button_click_enabled
点击效果 = AAA_action_button_click
```

示例：

```txt
scripted_gui = {
    AAA_panel_sg = {
        context_type = decision_category
        window_name = "AAA_panel"

        visible = {
            tag = AAA
        }

        triggers = {
            AAA_action_button_click_enabled = {
                has_political_power > 49
            }
        }

        effects = {
            AAA_action_button_click = {
                add_political_power = -50
                add_stability = 0.05
            }
        }
    }
}
```

经验：

- GUI 只写布局，逻辑写 scripted GUI。
- 一排按钮要统一坐标和间距。
- tooltip 写成本和效果。
- 按钮有图标不等于有功能，必须绑定 `_click`。

---

## 19. AI 与历史路线

### 19.1 国策 AI

```txt
ai_will_do = {
    factor = 1
    modifier = {
        factor = 0
        has_war = yes
    }
}
```

### 19.2 AI 策略

可以使用：

```text
common/ai_strategy/
```

也可以在国策、事件、决议里设置 AI 倾向。

经验：

- 先让玩家路径可玩，再做 AI。
- 历史路线要和 `Historical AI Focuses` 兼容。
- AI 复杂度高，别在项目早期过度投入。

---

## 20. Replace Path 与覆盖原版

`replace_path` 可以让 mod 替换整个原版目录，例如：

```txt
replace_path = "common/national_focus"
```

慎用。

风险：

- 会让整个目录下其他原版文件失效。
- 容易破坏 DLC 或其他国家内容。
- 与其他 mod 兼容性极差。

建议：

- 新增国家、新增事件、新增决议：不要用 `replace_path`。
- 修改单个国家国策：优先复制并覆盖对应文件，谨慎处理。
- 大规模重做世界线或地图：才考虑 `replace_path`。

---

## 21. Debug 与日志

### 21.1 启动参数

Steam 启动选项可加：

```text
-debug
```

常用调试方式：

- 看 `error.log`。
- 游戏内控制台触发事件。
- 临时给国策/决议加明显效果。
- 用变量文本显示当前数值。

### 21.2 日志路径

```text
Documents/Paradox Interactive/Hearts of Iron IV/logs/error.log
```

重点搜索：

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

### 21.3 快速静态检查

检查大括号：

```powershell
$files = Get-ChildItem -Recurse -File -Include *.txt,*.gui,*.gfx
foreach ($f in $files) {
    $text = Get-Content -Raw -Encoding UTF8 $f.FullName
    $open = ([regex]::Matches($text, "\{")).Count
    $close = ([regex]::Matches($text, "\}")).Count
    if ($open -ne $close) {
        "$($f.FullName): open=$open close=$close"
    }
}
```

---

## 22. 新项目最小可运行清单

如果你要做一个新的原版国家，先做到这些：

- [ ] 启动器能识别 mod。
- [ ] `descriptor.mod` 正确。
- [ ] 外层 `.mod` 文件路径正确。
- [ ] 新国家 TAG 注册。
- [ ] 国家颜色文件存在。
- [ ] 国家历史文件存在。
- [ ] 至少一个州属于该国家。
- [ ] 国旗三尺寸存在。
- [ ] 国家名本地化存在。
- [ ] 国家能在 1936 剧本出现。
- [ ] 有一个最小国策树。
- [ ] 国策标题和描述正常显示。
- [ ] 至少一个事件可由国策触发。
- [ ] 至少一个决议可显示并点击。
- [ ] `error.log` 没有严重解析错误。

---

## 23. 推荐开发工作流

### 23.1 新增国家

1. 添加 TAG。
2. 添加国家颜色。
3. 添加国家历史。
4. 修改州归属。
5. 添加国旗。
6. 添加本地化。
7. 启动游戏确认国家存在。

### 23.2 新增国策树

1. 写 `focus_tree` 最小模板。
2. 绑定 `tag`。
3. 加 1 个起始国策。
4. 加本地化。
5. 进游戏确认显示。
6. 再扩分支。

### 23.3 新增事件链

1. 新建 namespace。
2. 写第一个 `country_event`。
3. 加本地化。
4. 从国策或决议触发。
5. 确认事件弹出。
6. 再做多选项、随机结果、后续事件。

### 23.4 新增决议系统

1. 新建 category。
2. 写一个简单 decision。
3. 确认 visible/available 正确。
4. 测试点击成本和效果。
5. 再加倒计时和阶段。

### 23.5 新增 GUI

1. 先用决议实现逻辑。
2. 逻辑稳定后做 GUI 展示。
3. 需要点击时用 scripted GUI 绑定。
4. 实机检查对齐和 tooltip。

---

## 24. 常见错误速查

| 问题 | 可能原因 | 处理 |
|------|----------|------|
| 启动器不显示 mod | 外层 `.mod` 缺失或 path 错 | 检查 Documents/mod 目录 |
| 国家不出现 | TAG 没注册、州没有 owner | 检查 country_tags 和 state history |
| 国旗不显示 | 文件名不对、尺寸缺失 | 检查 `gfx/flags` 三个路径 |
| 国策树不显示 | focus_tree 没绑定 tag | 检查 `country = { modifier = { tag = AAA } }` |
| 国策显示 key | 本地化缺失或 yml 无 BOM | 检查 localisation |
| 事件不弹 | 没触发、ID 错、namespace 错 | 用控制台测试事件 |
| 决议不可见 | `visible` 条件不满足 | 临时改成 `always = yes` 测试 |
| 决议不可点 | `available` 条件不满足 | 检查资源和变量 |
| OOB 不加载 | 文件名不匹配、province 错 | 检查 `history/units` |
| GUI 按钮没反应 | 没写 `_click` effect | 检查 scripted GUI |
| 日志 unknown effect | 写了非原版/TNO 专属效果 | 换成原版 effect |

---

## 25. 原版与大型框架子模组的区别

原版项目不要直接套用大型框架的专属内容。

| 大型框架常见内容 | 原版是否可用 |
|------------------|--------------|
| TNO GDP 系统 | 不可直接用 |
| TNO 社会发展度 | 不可直接用 |
| TNO 俄罗斯 Smuta | 不可直接用 |
| KR 专属 scripted effects | 不可直接用 |
| RT56 自定义科技 | 不可直接用 |
| 原版 focus/effect/trigger | 可用 |
| 原版 ideologies | 可用 |
| 原版 character 系统 | 可用 |
| 原版 decisions/events | 可用 |

如果你从其他大型 mod 迁移代码，必须逐个确认：

- effect 是否原版存在。
- trigger 是否原版存在。
- idea 是否原版存在。
- trait 是否原版存在。
- GFX 是否随你的 mod 一起提供。
- 本地化 key 是否随你的 mod 一起提供。

---

## 26. 提交前检查清单

- [ ] 没有无关文件被误改。
- [ ] 没有整目录复制原版文件。
- [ ] 新增脚本大括号配平。
- [ ] 本地化有 UTF-8 BOM。
- [ ] `error.log` 没有严重错误。
- [ ] 新国家、国策、事件、决议都实机测试过。
- [ ] 自定义 GFX 路径和 sprite 名称一致。
- [ ] 所有引用的 OOB、idea、trait、event、focus 都存在。
- [ ] README 或开发文档记录了新增系统。

---

## 27. 最小示例：一个国家、一条国策、一个事件

### 27.1 TAG

```txt
AAA = "countries/AAA_Country.txt"
```

### 27.2 国家颜色

```txt
color = rgb { 120 80 200 }
color_ui = rgb { 120 80 200 }
```

### 27.3 国策

```txt
focus_tree = {
    id = AAA_focus_tree
    country = {
        factor = 0
        modifier = {
            add = 10
            tag = AAA
        }
    }
    default = no

    focus = {
        id = AAA_start
        icon = GFX_goal_generic_political_pressure
        x = 0
        y = 0
        cost = 10

        completion_reward = {
            add_political_power = 100
            country_event = { id = AAA.1 }
        }
    }
}
```

### 27.4 事件

```txt
add_namespace = AAA

country_event = {
    id = AAA.1
    title = AAA.1.t
    desc = AAA.1.desc
    picture = GFX_report_event_generic_political
    is_triggered_only = yes

    option = {
        name = AAA.1.a
        add_stability = 0.05
    }
}
```

### 27.5 本地化

```yml
l_simp_chinese:
 AAA_democratic:0 "阿尔法共和国"
 AAA_democratic_DEF:0 "阿尔法共和国"
 AAA_democratic_ADJ:0 "阿尔法"
 AAA_start:0 "国家起步"
 AAA_start_desc:0 "我们必须建立基本秩序。"
 AAA.1.t:0 "新政府"
 AAA.1.desc:0 "新的政府开始运转。"
 AAA.1.a:0 "很好"
```

---

## 28. 一句话总结

原版 HOI4 mod 的核心不是一开始做大系统，而是先搭出一条可靠链路：**TAG → 国家历史 → 本地化 → 国策 → 事件/决议 → 日志无错**。这条链跑通后，再扩内容就稳得多。
