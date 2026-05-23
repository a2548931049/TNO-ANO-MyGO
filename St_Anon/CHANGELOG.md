# 更新日志

本文件记录模组的所有重要变更，便于协作者了解项目进展。

格式参考 [Keep a Changelog](https://keepachangelog.com/zh-CN/)。

---

## [0.2.0] - 2026-03-29

### 新增
- **彩蛋将领：丰川祥子**
  - 全属性 9 级元帅，自带 `brilliant_strategist` / `offensive_doctrine` / `logistics_wizard` / `winter_specialist` 四大特质
  - 可兼任军事理论家顾问，特质「神明的乐章」（科研+15%、组织度+15%、计划速度+25%）
  - 通过决议「神明降临」解锁（需稳定度>80%、战争支持>50%、师团>19、完成"重回正轨"国策）
  - 专属事件 `ANO_easter.1`，含完整叙事文本
- **GFX 资源大量补充**（35个文件）
  - 国旗：`gfx/flags/` 大/中/小三尺寸
  - 领袖肖像：高松灯、千早爱音、椎名立希、长崎素世、要乐奈（含将领/顾问变体）
  - 国策图标：12个自定义图标（Tomorin_the_start_3、reme-1955、He_is_looking_us 等）
  - 开场画面：`TNO_OpeningScreen_mygo_ANO.png`
- **GFX 定义文件** `interface/ano_portraits.gfx` 扩充至 22 个 spriteType 条目
- 项目文档：`README.md`、`CHANGELOG.md`、`docs/CONTENT_GUIDE.md`

### 修复
- **国策树 62 处解析错误**（此前导致国策树无法加载、本地化全部失效）：
  - 移除 `activate = yes`（7处）— `add_field_marshal_role` / `add_corps_commander_role` / `add_advisor_role` 不支持此参数
  - `set_country_leader` → `promote_character`（TNO 兼容写法）
  - `add_puppet` 移除（2处）— `set_autonomy` 已覆盖
  - `add_army_experience` → `army_experience`
  - `create_non_aggression_pact` → `diplomatic_relation`（2处）
  - `movement` 属性移除（8处）— 非合法 unit modifier
  - `type = cas` 移除 — 无效装备类型，保留 `CAS_equipment_1`
- `OLD.txt` 重命名为 `.bak` — 废弃的繁体中文 ID 国策文件不再被解析

---

## [0.1.0] - 2026-03-29

### 新增
- **国家 ANO（沃洛格达军区）**
  - TAG: ANO，首都: State 351 (Manturovo)
  - 5 个核心省份：351-Manturovo / 855-Semyonov / 856-Kostroma / 857-Vologda / 867-Sharya

- **角色系统**（12位角色）
  - MyGO!!!!! 五人：高松灯（领袖+指挥官）、千早爱音（领袖+指挥官）、椎名立希（领袖+元帅+安全部长）、长崎素世（领袖+指挥官+政府首脑）、要乐奈（指挥官）
  - 副领袖：沃兹涅先斯基
  - 通用将领/顾问 5 人：petrov / sorokina / volkov / korolev / morozov

- **国策树**（35个国策）
  - 起始线：于寒风中而立 → 绝望中又一年
  - 军事分支：忆西俄战争 → 粉碎斯拉夫运动 → 鼓舞/重建/苏式武器 → 协作WRRF
  - 内政分支：不愿为奴的人们 → 加强执法 / 委任爱音 → 多条建设路线
  - 转折线：重回正轨 → 此时不搏 → 放鹰/加班/开发 → 号召阿尔汉格尔斯克
  - 外交分支：邻邦外交 → 反攻高尔基 → 东望 → 西进
  - 循环机：reset ↔ PP/稳定/演说/征兵

- **3 个领袖特质**
  - `Tomorin_traits`：PP-0.05、稳定-5%
  - `Soyo_traits`：PP+0.15、消费品-10%、稳定+10%、建造+15%
  - `Taki_Traits`：进攻+15%、防御+15%

- **3 个民族精神**
  - `ANO_refugee_crisis`：人力+300/周、适役+5%、稳定-10%、战争支持-25%、建造-20%、产出-20%
  - `ANO_double_headed_regime`：稳定-10%、效率上限-5%、PP-15%、科研-20%
  - `ANO_neutral_zone`：占位用

- **影响力系统**
  - 决议分类 + GUI + 事件 + on_actions

- **简体中文本地化**
  - 国家/政党名称、领袖描述、国家信息（5段叙事散文）
  - 自定义加载提示 20 条（角色语录 + 古典诗词）
  - 主菜单悬浮文本（叙事长文 + 普希金/MyGO 语录）
  - 加载阶段文本替换（"召开live……"、"享用抹茶芭菲中……" 等）
  - 所有 `.yml` 已添加 UTF-8 BOM

- **意识形态子类型**
  - `progressivism_subtype`、`progressivism_social_ano_subtype`

---

## 版本命名规则

| 版本段 | 含义 |
|--------|------|
| 0.x.x | 开发阶段，未完成 |
| 1.0.0 | 首个完整可玩版本 |
| x.Y.0 | 新增内容（国策/角色/系统） |
| x.x.Z | Bug 修复 / 文本修正 |
