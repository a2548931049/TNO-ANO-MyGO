# TNO: 沃洛格达军区 — MyGO!!!!! 子模组

> *「千磨万击还坚劲，任尔东西南北风！」*

[![HOI4](https://img.shields.io/badge/Hearts%20of%20Iron%20IV-1.17.3-blue)]()
[![TNO](https://img.shields.io/badge/依赖-TNO:_Last_Days_of_Europe-red)]()
[![License](https://img.shields.io/badge/license-MIT-green)]()

## 简介

这是一个基于 **The New Order: Last Days of Europe (TNO)** 的子模组，以 **BanG Dream! It's MyGO!!!!!** 的角色为核心，在俄罗斯废土的沃洛格达/科斯特罗马地区创建了一个全新的可玩国家——**沃洛格达军区（ANO）**。

在这个时间线中，MyGO!!!!! 的成员们不再是乐队少女，而是在第二次西俄战争后艰难求生的军事领袖。高松灯执笔为诗，椎名立希戎马倥偬，千早爱音纵横政坛，长崎素世殚精竭虑，要乐奈潜行暗影……她们将在冻土之上谱写属于自己的乐章。

## 安装

1. 订阅 [TNO 本体](https://steamcommunity.com/sharedfiles/filedetails/?id=2438003901)
2. 将本仓库克隆或下载到 HOI4 mod 目录：
   ```
   文档/Paradox Interactive/Hearts of Iron IV/mod/
   ```
3. 在启动器中启用 **TNO** 和本模组

## 内容概览

详见 [docs/CONTENT_GUIDE.md](docs/CONTENT_GUIDE.md) 了解完整的已实现内容清单。开发与排错经验见 [docs/HOI4_MODDING_GUIDE.md](docs/HOI4_MODDING_GUIDE.md)。

### 国策树

```
                        [于寒风中而立]                    [邻邦外交] ─── [苟延残喘]
                             │                                │
                      [绝望中又一年]                      [反攻高尔基]
                       /          \                           │
              [忆西俄战争]    [反制轰炸]                [接管高尔基]
                       \          /                           │
                     [残兵犹在窥视]                     [叛徒审判]
                      /            \                          │
            [粉碎斯拉夫运动]  [不愿为奴的人们]           [东望]
             / ｜ \          /        \                       │
          [鼓舞][重建][苏式] [加强执法] [委任爱音]          [西进]
             \ ｜ /    │   \     / ｜ \     │
          [协作WRRF]  [突击SS] [建设] [电厂]
                 \      │      /
                  [重回正轨]              [循环决议机]
                      │                  reset → PP/稳定/演说/征兵
                 [此时不搏]
                / ｜ \
          [放鹰][加班][开发]
                  │
             [号召阿尔汉]
```

### 可玩角色

| 角色 | 身份 | 原型 |
|------|------|------|
| 高松灯 | 初始领袖 / 军级指挥官 | MyGO!!!!! 主唱 |
| 千早爱音 | 继任领袖 / 军级指挥官 | MyGO!!!!! 吉他手 |
| 椎名立希 | 元帅 / 安全部长 | MyGO!!!!! 鼓手 |
| 长崎素世 | 军级指挥官 / 政府首脑 | MyGO!!!!! 贝斯手 |
| 要乐奈 | 军级指挥官 | MyGO!!!!! 吉他手 |
| 丰川祥子 | ??? 彩蛋元帅 | Ave Mujica |

### 特色系统

- **内忧外患系统** — 以 GUI + 决议追踪伊万诺夫残党与西俄革中央压力
- **循环决议机** — 可重复完成的国策，持续获取资源
- **自定义加载界面** — 角色语录和古典诗词
- **完整简体中文本地化** — 包含叙事性的国家描述和事件文本

## 项目结构

```
test2/
├── common/
│   ├── characters/         # 角色定义（12位）
│   ├── countries/          # 国家颜色
│   ├── country_leader/     # 领袖特质
│   ├── country_tags/       # ANO 国家标签
│   ├── decisions/          # 决议（内忧外患 + 彩蛋）
│   ├── ideas/              # 民族精神
│   ├── ideologies/         # 意识形态子类型
│   ├── national_focus/     # 国策树（35个国策）
│   ├── on_actions/         # 触发动作
│   ├── scripted_guis/      # 内忧外患 GUI
│   └── scripted_localisation/
├── events/                 # 事件脚本
├── gfx/
│   ├── flags/              # 国旗（大/中/小）
│   ├── interface/          # 国策图标 + 开场画面
│   └── leaders/            # 肖像（领袖/将领/顾问）
├── history/
│   ├── countries/          # 国家历史
│   ├── states/             # 省份（351/855/856/857/867）
│   └── units/              # OOB 军队编制
├── interface/              # GFX 定义
└── localisation/simp_chinese/  # 简体中文本地化
```

## 协作指南

- 所有本地化 `.yml` 文件必须使用 **UTF-8 BOM** 编码
- 国策图标推荐使用 `.dds` 格式（`.png` 可能显示异常）
- 提交前请确认 `error.log` 无严重解析错误
- 更新记录请同步维护 [CHANGELOG.md](CHANGELOG.md)

## 致谢

- [The New Order: Last Days of Europe](https://steamcommunity.com/sharedfiles/filedetails/?id=2438003901) — 基础框架
- [BanG Dream! It's MyGO!!!!!](https://bang-dream.com/mygo) — 角色原型
- Paradox Interactive — Hearts of Iron IV

---

*这是一个粉丝同人作品，与 Bushiroad、Craft Egg 及 Paradox Interactive 无官方关联。*
