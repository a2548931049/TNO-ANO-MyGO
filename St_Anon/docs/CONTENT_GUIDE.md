# 内容实现清单

> 最后更新：2026-03-29 v0.2.0

本文件详细列出模组中**已实现**和**待实现**的所有内容，供协作者查阅。

---

## 一、角色一览

### 1.1 MyGO!!!!! 成员

| 角色 ID | 名称 | 可用身份 | 肖像 | 备注 |
|---------|------|---------|------|------|
| `ANO_Tomorin` | 高松灯 | 国家领袖 · 军级指挥官 | leader + general | 初始领袖，完成第一个国策后获得指挥官身份 |
| `ANO_chihaya_anon` | 千早爱音 | 国家领袖 · 军级指挥官 | leader + general | 「绝望中又一年」国策后 promote 为领袖 |
| `ANO_rikki` | 椎名立希 | 国家领袖 · 元帅 · 安全部长 | leader + marshal + minister | 技能 5/8/8/8/8，自带 `head_of_government` 特质 |
| `ANO_Soyo` | 长崎素世 | 国家领袖 · 军级指挥官 · 政府首脑 | leader + general + minister | 顾问特质 `Soyo_traits` |
| `ANO_Rana` | 要乐奈 | 军级指挥官 | general | 自带 `commando` 特质，logistics 6 |

### 1.2 其他角色

| 角色 ID | 名称 | 身份 | 备注 |
|---------|------|------|------|
| `KOM_Nikolai_Voznesensky_1` | 沃兹涅先斯基 | 国家领袖 | 进步主义路线副领袖 |
| `ANO_viktor_petrov` | 维克托·彼得罗夫 | 元帅 | 通用将领 |
| `ANO_elena_sorokina` | 叶连娜·索罗金娜 | 军级指挥官 | 通用将领 |
| `ANO_dmitri_volkov` | 德米特里·沃尔科夫 | 军级指挥官 | 通用将领 |
| `ANO_ivan_korolev` | 伊万·科罗廖夫 | 军级指挥官 | 通用将领 |
| `ANO_alexei_morozov` | 阿列克谢·莫罗佐夫 | 军事理论家 | 通用顾问 |

### 1.3 彩蛋角色

| 角色 ID | 名称 | 身份 | 解锁方式 |
|---------|------|------|---------|
| `ANO_sakiko` | 丰川祥子 | 元帅 · 军事理论家 | 决议「神明降临」 |

**丰川祥子数据：**
- 元帅技能：9 / 9 / 9 / 9 / 9
- 特质：`brilliant_strategist` · `offensive_doctrine` · `logistics_wizard` · `winter_specialist`
- 顾问特质「神明的乐章」：科研+15%、组织度+15%、计划速度+25%、最大计划+25%
- 解锁条件：稳定度>80%、战争支持>50%、师团>19、完成 `ANO_back_on_track`

---

## 二、国策树

### 2.1 主线（y0 ~ y12）

| 国策 ID | 名称 | 耗时 | 关键效果 |
|---------|------|------|---------|
| `ANO_stand_in_the_cold` | 于寒风中而立 | 0.3 | 设 WRS 外观、+100PP、任命全部军事角色、社发改良×5 |
| `ANO_another_year_in_despair` | 绝望中又一年 | 4.3 | +100PP、设 ANO 外观、promote 千早爱音为领袖 |
| `ANO_remember_the_west_russian_war` | 忆西俄战争 | 3.6 | 与 WRS 互不侵犯、VOL 自治领、+50 陆军经验 |
| `ANO_counter_luftwaffe_bombing` | 反制空袭 | 3.6 | +2防空、+100 CAS、+25指挥力 |
| `ANO_remnants_watching` | 残兵犹在窥视 | 1 | 步兵全面削弱（过渡性debuff） |
| `ANO_back_on_track` | 重回正轨 | 4.3 | 步兵全面增强（抵消 remnants debuff） |
| `ANO_now_or_never` | 此时不搏 | 1 | 需 `hitler_dead` 且无恐怖轰炸 |
| `ANO_call_to_arkhangelsk` | 号召阿尔汉格尔斯克 | 2 | 启用 Smuta 系统 |

### 2.2 军事分支（左路 y5 ~ y8）

| 国策 ID | 名称 | 关键效果 |
|---------|------|---------|
| `ANO_crush_slavic_movement` | 粉碎斯拉夫运动 | 步兵增强 |
| `ANO_boost_public_confidence` | 鼓舞民心 | +5%稳定、GDP+3% |
| `ANO_rebuild_our_needs` | 重建所需 | +3%稳定、GDP+3% |
| `ANO_soviet_arms` | 苏式武器 | 大量装备替换/补充 |
| `ANO_cooperate_with_wrrf` | 协作 WRRF | 加载 BorderGuard OOB |

### 2.3 内政分支（右路 y5 ~ y8）

| 国策 ID | 名称 | 关键效果 |
|---------|------|---------|
| `ANO_people_who_wont_be_slaves` | 不愿为奴的人们 | +5%稳定、+10%战争支持 |
| `ANO_strengthen_law_enforcement` | 加强执法 | +监狱 |
| `ANO_investigate_traitors` | 调查叛徒 | -200人力、步兵增强 |
| `ANO_expand_army` | 扩军 | +10000人力、加载 Mygo OOB |
| `ANO_strike_ss_terrorists` | 打击SS恐怖分子 | 步兵增强 |
| `ANO_empower_chihaya_anon` | 委任千早爱音 | 行政效率提升 |
| `ANO_let_them_build` | 让她们建设 | GDP+30.5% |
| `ANO_rebuild_local_power_plant` | 重建电厂 | 全省份+热电厂+基建+学校 |

### 2.4 外交分支（y1 ~ y6）

| 国策 ID | 名称 | 关键效果 |
|---------|------|---------|
| `ANO_neighbors` | 邻邦外交 | 与 WRS/KOM 互不侵犯+通行权 |
| `ANO_lingering_on` | 苟延残喘 | VOL 成为 WRS 自治领 |
| `ANO_counterattack_gorky` | 反攻高尔基 | 对 GOR 宣战理由 |
| `ANO_take_over_gorky` | 接管高尔基 | 大量装备+科技+将领+研究槽 |
| `ANO_trial_of_traitors` | 叛徒审判 | 对 SAM 宣战、步兵增强、SAM 步兵削弱 |
| `ANO_look_east` | 东望 | 对全部东部军阀宣战、GDP+10.5% |
| `ANO_go_west` | 西进 | 对 MCW/CAU 宣战 |

### 2.5 循环决议机（y10 ~ y12）

| 国策 ID | 效果 |
|---------|------|
| `ANO_reset` | 重置以下四个国策 |
| `ANO_add_political_power` | +100 PP |
| `ANO_stability_measures` | +3% 稳定 |
| `ANO_give_speech` | +5% 战争支持 |
| `ANO_conscription` | +9669 人力 |

---

## 三、民族精神

| ID | 名称 | 效果 |
|----|------|------|
| `ANO_refugee_crisis` | 难民危机 | 人力+300/周、适役+5%、稳定-10%、战争支持-25%、建造-20%、产出-20% |
| `ANO_double_headed_regime` | 双头政权 | 稳定-10%、效率上限-5%、PP-15%、科研-20% |
| `ANO_neutral_zone` | 缓冲中立区 | 占位用，被国策移除后推进剧情 |

---

## 四、领袖特质

| ID | 名称 | 效果 | 持有者 |
|----|------|------|--------|
| `Tomorin_traits` | — | PP-0.05、稳定-5% | 高松灯 |
| `Soyo_traits` | — | PP+0.15、消费品-10%、稳定+10%、建造+15% | 长崎素世 |
| `Taki_Traits` | — | 陆军进攻+15%、陆军防御+15% | 椎名立希 |
| `Sakiko_theorist_trait` | 神明的乐章 | 科研+15%、组织度+15%、计划速度+25%、最大计划+25% | 丰川祥子 |

---

## 五、GFX 资源

### 5.1 国策图标（自定义）

| GFX 名称 | 文件 | 对应国策 |
|-----------|------|---------|
| `GFX_focus_Tomorin_the_start_3` | `Tomorin_the_start_3.dds` | 于寒风中而立 / 绝望中又一年 |
| `GFX_focus_reme-1955` | `reme-1955.dds` | 忆西俄战争 |
| `GFX_focus_germay_bomber` | `germay_bomber.dds` | 反制空袭 |
| `GFX_focus_He_is_looking_us` | `He_is_looking_us.dds` | 残兵犹在窥视 |
| `GFX_focus_We_Dont_Want_to_be_slave_` | `We_Dont_Want_to_be_slave_.dds` | 不愿为奴的人们 |
| `GFX_focus_Let_Chihaya_Anon_in_Charge` | `Let_Chihaya_Anon_in_Charge.dds` | 委任千早爱音 |
| `GFX_focus_Go_to_construction_2` | `Go_to_construction_2.dds` | 让她们建设 |
| `GFX_focus_Clear_them` | `Clear_them.png` | 粉碎斯拉夫运动 |
| `GFX_focus_Boost_People_Confidences` | `Boost_People_Confidences.png` | 鼓舞民心 |
| `GFX_focus_Rebuild_Our_Needs_tomorin` | `Rebuild_Our_Needs_tomorin.png` | 重建所需 |
| `GFX_focus_Soviet_arms` | `Soviet_arms.png` | 苏式武器 |
| `GFX_portrait_ANO_goal` | `ANO_goals.dds` | 通用国策图标 |

### 5.2 肖像

- 领袖肖像 5 组（高松灯 / 千早爱音 / 椎名立希 / 长崎素世 / 丰川祥子）
- 将领肖像 7 张（含各角色 general 变体）
- 顾问肖像 2 张（椎名立希 / 长崎素世 minister 变体）

---

## 六、本地化

| 文件 | 内容 |
|------|------|
| `ANO_countries_l_simp_chinese.yml` | 国名、政党名、领袖描述、国家信息长文 |
| `ANO_easter_egg_l_simp_chinese.yml` | 彩蛋决议/事件/特质文本 |
| `TNO_General_l_simp_chinese.yml` | 加载阶段文本、内阁槽位、意识形态漂移/禁止 |
| `TNO_Loading_Tips_l_simp_chinese.yml` | 20条加载提示（角色语录+古典诗词） |
| `TNO_menu_tooltips_l_simp_chinese.yml` | 主菜单叙事长文 |

---

## 七、已知问题 & 待办

### 待修复
- [ ] 部分国策图标为 `.png` 格式（`Clear_them`、`Boost_People_Confidences`、`Rebuild_Our_Needs_tomorin`、`Soviet_arms`），可能显示白框，需转为 `.dds`
- [ ] `VOL_BorderGuard`、`VOL_Mygo` OOB 文件缺失，相关国策加载会报错
- [ ] `The_Struggle` 前置国策是否由 TNO 本体提供需确认
- [ ] 自定义将领特质 `War_Hero`、`Media_Personality`、`Logistics_Wizard`、`commando` 需确认 TNO 是否已定义
- [ ] `GOR_1962` OOB 被两个国策引用，需确认是否存在

### 待实现
- [ ] 更多事件链（领袖更替剧情、战后重建等）
- [ ] Ave Mujica 路线？（丰川祥子相关扩展内容）
- [ ] 自定义 GUI 面板（军区状态仪表盘）
- [ ] 音乐模组（MyGO!!!!! BGM 替换）
- [ ] Steam 创意工坊发布准备
