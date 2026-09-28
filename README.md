<div align="center">

# 🔧 expRepair

### Repair items using your XP — passively or on demand.

![](https://img.shields.io/badge/Fabric-DBA463?style=for-the-badge&logoColor=white)&nbsp;![](https://img.shields.io/badge/NeoForge-F16436?style=for-the-badge&logoColor=white)&nbsp;

[![](https://img.shields.io/badge/Download_on-Modrinth-00AF5C?style=for-the-badge&logo=modrinth&logoColor=white)](https://modrinth.com/project/exprepair)&nbsp;[![](https://img.shields.io/badge/Download_on-CurseForge-F16436?style=for-the-badge&logo=curseforge&logoColor=white)](https://www.curseforge.com/minecraft/mc-mods/exprepair-multi)

![](https://img.shields.io/badge/Minecraft-26.x_%7C_1.21.x-62B47A?style=flat-square) ![](https://img.shields.io/badge/Side-Single_Player_%26_Server-8E44AD?style=flat-square) ![](https://img.shields.io/badge/Fabric_API-required_on_Fabric-4A90D9?style=flat-square) ![](https://img.shields.io/badge/License-MIT-blue?style=flat-square)

<sub><b>English</b> · <a href="#-exprepair-1">简体中文</a></sub>

</div>

---

> [!NOTE]
> expRepair turns experience into durability. Enchant your gear with **Mending**, then let it top itself
> off automatically as you play — or repair the tool in your hand on demand with a sneak + right-click. No
> XP orbs to chase, no anvils, no grindstones. Per-version code and changelog live on the
> [`multi_*`](#-versions--downloads) branches.

> [!IMPORTANT]
> **This fork** carries one compatibility change on top of upstream: an elytra worn in
> **Elytra Slot!** (3.0.0+) is now repaired by the existing repair logic. That mod keeps a worn elytra in
> the vanilla `BODY` equipment slot, which the scan previously skipped; it is now included. No dependency
> is added, and nothing changes when Elytra Slot! is not installed. The change is `EquipmentSlot.BODY`
> added to the scanned slot list on the
> [`multi_26.1-3`](https://github.com/koutakutenn-sys/expRepair/tree/multi_26.1-3) branch.

## ✨ Features

Vanilla Mending only heals the item you're holding, and only when an XP orb happens to land — so your
armor rots while you fight and your off-hand shield never sees a repair. expRepair fixes that by spending
your **stored** XP the moment gear needs it:

- **Two repair modes, one at a time.** Every player picks **Passive** (hands-off, always-on) or **Manual**
  (deliberate, on-demand). Turning one on turns the other off, so there's never any doubt about what's
  spending your levels.
- **Passive repair — the whole loadout.** Once per second, every damaged **Mending** item you have
  equipped — main hand, off-hand, and all four armor slots — is topped off from your XP pool, up to a
  configurable budget. Your kit stays healthy in the background while you mine, fight, or fly.
- **Manual repair — just the tool in hand.** Sneak + right-click in the air while holding a damaged
  Mending item and it repairs on the spot, spending up to the same per-action XP budget. Nothing happens
  passively, so you decide exactly when your levels get used.
- **A protective XP floor.** Set a **threshold** and passive repair will never spend XP that would drop you
  below that many levels — bank enough for that next enchant while the overflow keeps your gear alive.
- **Mending is the key.** Only items enchanted with **Mending** are ever repaired — the mod respects the
  vanilla enchant as the opt-in, so nothing touches gear you didn't intend to auto-repair.
- **Survival only, and only while alive.** Creative and spectator players are skipped, and no XP is spent
  on the death screen — your levels are only ever converted while you're actually playing.
- **A clickable login summary.** On join you get a compact status line — passive/manual state, your XP
  threshold, and inline buttons to toggle each — which every player can switch off with one command.
- **Full admin control.** Operators set server-wide defaults, force a mode on or off for any player, and
  can **block** either mode entirely so nobody can enable it. Config **hot-reloads** with no restart.
- **Plays nice with PvP mods.** expRepair exposes a suppression hook that companion mods (e.g. pvpOption)
  can use to pause repairs mid-fight, so combat mods can keep XP-repair from kicking in during a duel.
- **Server-side.** Everything runs on the server — a **vanilla client** can connect and it just works, and
  it runs the same in single-player.

## 🔧 How it works

XP is converted to durability at a fixed, transparent rate: **1 XP point restores 2 durability**. Each
repair action spends at most `maxXpPerRepair` XP (default **8**, so up to **16 durability**), drawn from
your **total** experience — levels *and* the progress bar — not just loose orbs.

- **Passive** fires on a **1-second** tick. The `maxXpPerRepair` budget is a **total per tick**, shared
  across every equipped Mending item in slot order (hand, off-hand, head, chest, legs, feet) until the
  budget or the damage runs out. Before spending, it subtracts your **threshold** floor: if your XP is at
  or below that level, passive repair simply waits until you've earned more.
- **Manual** applies the full `maxXpPerRepair` budget to the **single** Mending item in your hand, per
  sneak + right-click. It ignores the threshold — you asked for it, so it spends what it can.
- Partially-damaged items get partial repairs; the overlay tells you whether the item was **fully
  repaired** or by how much durability, and manual repair warns you when you're out of XP.

Per-player choices (mode, threshold, login-message toggle) persist to `config/exprepair/playerdata.json`,
and server settings live in `config/exprepair.json`.

## ⌨️ Commands

The base command is **`/exprepair`**, with **`/er`** as a shortcut alias. Running it bare opens a clickable
help screen.

### 👤 Player

| Command | What it does |
|---|---|
| `/exprepair` | Clickable help — every command with inline buttons. |
| `/exprepair passive` | Toggle **passive** repair on/off (turns manual off). |
| `/exprepair manual` | Toggle **manual** repair on/off (turns passive off). |
| `/exprepair threshold` | Show your current XP floor. |
| `/exprepair threshold <levels>` | Set the XP floor passive repair won't spend below (`0` clears it). |
| `/exprepair status` | Show your passive, manual, and threshold settings. |
| `/exprepair serverdefaults` | View the server's default settings for new players. |
| `/exprepair loginmessage` | Toggle the on-join status message for yourself. |
| `/exprepair version` | Show the installed mod version. |

### 🛡️ Admin

All admin commands require **game-master permission** (op level 2+).

| Command | What it does |
|---|---|
| `/exprepair admin` | View server-wide defaults and allow-flags. |
| `/exprepair admin <player>` | View that player's live settings. |
| `/exprepair admin <player> reset` | Reset a player to the current server defaults. |
| `/exprepair admin <player> passive on\|off` | Force passive on/off for a player. |
| `/exprepair admin <player> manual on\|off` | Force manual on/off for a player. |
| `/exprepair admin <player> threshold <levels>` | Set a player's XP floor. |
| `/exprepair admin passive on\|off` | Set the **default** passive state for new players. |
| `/exprepair admin passive allow on\|off [silent]` | Allow or **block** passive repair server-wide. |
| `/exprepair admin manual on\|off` | Set the **default** manual state for new players. |
| `/exprepair admin manual allow on\|off [silent]` | Allow or **block** manual repair server-wide. |
| `/exprepair admin threshold <levels>` | Set the default XP floor for new players. |
| `/exprepair admin maxXpPerRepair <xp>` | Set the max XP spent per repair action (min `1`). |
| `/exprepair admin reload [silent]` | Re-read `exprepair.json` from disk — no restart. |

> [!TIP]
> Add `silent` to `allow`, `reload`, and the like to apply the change **without** broadcasting it to
> everyone online — handy for quiet mid-session tweaks.

## 💡 Use cases

- **Set-and-forget survivalist.** Enchant your armor and tools with Mending, run `/exprepair passive`, and
  your whole loadout maintains itself from ambient XP while you play — no more mid-cave gear failures.
- **XP-hungry enchanter.** You're saving levels for a big enchant. Run `/exprepair threshold 30` so passive
  repair only ever spends the XP **above** level 30 — your gear still heals, but your enchanting fund is
  protected.
- **Deliberate hardcore player.** You want repairs on *your* terms. Flip to `/exprepair manual` and top off
  the tool in your hand with a sneak + right-click exactly when you choose — no XP leaves your bar
  otherwise.
- **PvP-focused server.** Admins run `/exprepair admin passive allow off` to keep passive auto-repair out
  of the arena, or lean on the PvP-suppression hook so a combat mod pauses repairs during fights — while
  still letting players repair freely elsewhere.

## ⚙️ Configuration

Server settings live in **`config/exprepair.json`**, created on first launch and editable in-game via
`/exprepair admin …`. Changes made through commands save immediately; edits made on disk apply with
`/exprepair admin reload` — no restart required.

| Key | Default | Meaning |
|---|:---:|---|
| `maxXpPerRepair` | `8` | Max XP spent per repair action — per tick for passive, per click for manual. 1 XP = 2 durability, so `8` = up to 16 durability. Minimum `1`. |
| `defaultPassive` | `false` | Whether new players start with passive repair on. |
| `defaultManual` | `false` | Whether new players start with manual repair on. |
| `defaultThreshold` | `0` | Default XP-level floor for new players (`0` = no floor). |
| `allowPassive` | `true` | Master switch — if `false`, **no** player can use passive repair. |
| `allowManual` | `true` | Master switch — if `false`, **no** player can use manual repair. |

> [!NOTE]
> `defaultPassive` and `defaultManual` can't both be on — the two modes are mutually exclusive, so if both
> are set to `true`, manual wins and passive is forced off. Per-player state (chosen mode, threshold, and
> login-message toggle) is stored separately in `config/exprepair/playerdata.json`.

## 📦 Versions &amp; downloads

> [!NOTE]
> This repo uses a **branch-per-version** layout. This `main` branch is **documentation only** — the code for each Minecraft version lives on its own branch, each with an independent history and its own `CHANGELOG.md`.

| Branch | Minecraft | Loaders | Dependencies | Log |
|:------:|:---------:|:-------:|:------------:|:---:|
| [`multi_26.1-3`](https://github.com/LunixiaLIVE/expRepair/tree/multi_26.1-3) | 26.1 – 26.3 *(one jar for all)* | Fabric · NeoForge | Fabric API *(Fabric only)* | [📄](https://github.com/LunixiaLIVE/expRepair/blob/multi_26.1-3/CHANGELOG.md) |
| [`multi_1.21.11`](https://github.com/LunixiaLIVE/expRepair/tree/multi_1.21.11) | 1.21.11 | Fabric · NeoForge | Fabric API *(Fabric only)* | [📄](https://github.com/LunixiaLIVE/expRepair/blob/multi_1.21.11/CHANGELOG.md) |
| [`multi_1.21.5`](https://github.com/LunixiaLIVE/expRepair/tree/multi_1.21.5) | 1.21.5–1.21.10 | Fabric · NeoForge | Fabric API *(Fabric only)* | [📄](https://github.com/LunixiaLIVE/expRepair/blob/multi_1.21.5/CHANGELOG.md) |
| [`multi_26.2`](https://github.com/LunixiaLIVE/expRepair/tree/multi_26.2) | 26.2.x *(archived)* | Fabric · NeoForge | Fabric API *(Fabric only)* | [📄](https://github.com/LunixiaLIVE/expRepair/blob/multi_26.2/CHANGELOG.md) |
| [`multi_26.1`](https://github.com/LunixiaLIVE/expRepair/tree/multi_26.1) | 26.1, 26.1.1, 26.1.2 *(archived)* | Fabric · NeoForge | Fabric API *(Fabric only)* | [📄](https://github.com/LunixiaLIVE/expRepair/blob/multi_26.1/CHANGELOG.md) |

> [!TIP]
> Every `multi_*` branch builds **one `-multi.jar` that runs on both Fabric and NeoForge**. On 26.x it's a single merged jar (Minecraft is unobfuscated there); on 1.21.x it's a jar-in-jar bundle with the Fabric and NeoForge builds nested inside, each loader picking its own. Per-loader `-fabric` / `-neoforge` jars are produced too (`build/staging/`). Fully self-contained — **no extra library mods to install**.

<details>
<summary>🛠️ <b>Building from source</b></summary>

Each code branch is a self-contained Gradle project. Grab the branch for your Minecraft version:

```bash
git clone -b multi_26.1-3 https://github.com/LunixiaLIVE/expRepair.git
cd expRepair
./gradlew build
```

The `-multi` jar lands in `build/libs/` — drop it into your `mods/` folder on either loader.
</details>

## 📄 License

Released under the **MIT License**.

<div align="center"><sub>⛏️ Part of <a href="https://github.com/LunixiaLIVE/Lunixia-Minecraft-QOL-Mods">Lunixia's Minecraft QOL Mods</a>.</sub></div>

---

<div align="center">

# 🔧 expRepair

### 用经验值修理物品 —— 自动或手动皆可。

<sub><a href="#-exprepair">English</a> · <b>简体中文</b></sub>

</div>

---

> [!NOTE]
> expRepair 把经验值变成耐久度。给装备附上**经验修补**，它就会在你游玩时自动补满 —— 或者用「潜行 +
> 右键」按需修复手中的工具。不用去追经验球，不用铁砧，不用砂轮。各版本的代码与更新日志在
> `multi_*` 分支上。

> [!IMPORTANT]
> **本 fork** 在上游基础上带了一处兼容性改动：穿在 **Elytra Slot!**（3.0.0+）鞘翅槽里的鞘翅，现在也会
> 被现有的修理逻辑修复。该模组把穿戴中的鞘翅存放在原版 `BODY` 装备槽里，而原来的扫描跳过了它；现在
> 已包含。本改动不引入任何依赖，未安装 Elytra Slot! 时行为完全不变。改动内容就是把
> `EquipmentSlot.BODY` 加入扫描槽位表，位于
> [`multi_26.1-3`](https://github.com/koutakutenn-sys/expRepair/tree/multi_26.1-3) 分支。

## ✨ 功能

原版经验修补只修复你手里的物品，而且必须刚好有经验球落到身上 —— 于是你在战斗中盔甲一点点损坏，副手的
盾牌永远等不到修复。expRepair 的做法是：装备一需要，就花掉你**已存**的经验值。

- **两种修理模式，二选一。** 每位玩家选择**被动**（放手不管、常开）或**手动**（自己掌控、按需触发）。
  开启其中一个会自动关掉另一个，所以经验值花在哪儿永远清清楚楚。
- **被动修理 —— 整套装备。** 每秒一次，把你身上所有带**经验修补**且已损坏的装备 —— 主手、副手和四个
  盔甲槽 —— 从经验池补满，上限可配置。你挖矿、战斗、飞行时，整套装备都在后台保持健康。
- **手动修理 —— 只管手里的那一件。** 手持带经验修补的损坏物品，在空中「潜行 + 右键」，它当场被修复，
  花费与单次相同的经验预算。平时不会有任何被动消耗，什么时候花经验完全由你决定。
- **经验保护线。** 设定一个**阈值**，被动修理就不会花掉会让你掉到该等级以下的经验 —— 既攒得下下一次
  附魔的本钱，溢出的部分又继续养着装备。
- **经验修补是唯一门槛。** 只有附有**经验修补**的物品会被修复 —— 本模组沿用原版附魔作为开关，绝不碰
  你不想自动修理的装备。
- **仅生存模式，且仅在活着时。** 创造与旁观模式玩家会被跳过，死亡界面也不会消耗经验 —— 只有你真正在
  游玩时，等级才会被转换成耐久。
- **可点击的登录摘要。** 进入服务器时会收到一条紧凑的状态行 —— 被动/手动状态、你的经验阈值，以及
  可直接点击的内联按钮，每位玩家都能用一条命令关掉它。
- **完整的管理员控制。** 管理员可设置全服默认值、为任意玩家强制开关某个模式，也能**完全禁用**任一
  模式，让所有人都开不了。配置支持**热重载**，无需重启。
- **与 PvP 模组友好共存。** expRepair 暴露了一个抑制钩子，配套模组（例如 pvpOption）可以用它在战斗中
  暂停修理，让战斗模组能阻止决斗期间触发经验修理。
- **服务端运行。** 一切都在服务端进行 —— **原版客户端**即可连接使用，在单人游戏里表现完全一致。

## 🔧 工作原理

经验值以固定且透明的比例转换成耐久：**1 点经验恢复 2 点耐久**。每次修理最多花费 `maxXpPerRepair`
点经验（默认 **8**，即最多 **16 点耐久**），从你的**总**经验里扣除 —— 包括等级*和*进度条，而不只是
散落的经验球。

- **被动**每秒触发一次。`maxXpPerRepair` 是**每 tick 的总预算**，按槽位顺序（主手、副手、头盔、胸甲、
  护腿、靴子）在所有已装备的经验修补物品之间共享，直到预算用尽或损坏修完。花费前会先扣掉你的**阈值**
  底线：如果经验处于或低于该等级，被动修理就等你赚到更多经验再动手。
- **手动**把完整的 `maxXpPerRepair` 预算用在手里的**单件**经验修补物品上，每次「潜行 + 右键」触发一次。
  它忽略阈值 —— 既然你主动要求，它就会尽量花。
- 部分损坏的物品只会被部分修复；动作栏提示会告诉你物品是**完全修好**还是修了多少耐久，手动修理在经验
  不足时也会给出提示。

每位玩家的选择（模式、阈值、登录消息开关）保存在 `config/exprepair/playerdata.json`，服务器设置保存在
`config/exprepair.json`。

## ⌨️ 命令

基础命令是 **`/exprepair`**，快捷别名 **`/er`**。不带参数运行会打开一个可点击的帮助界面。

### 👤 玩家

| 命令 | 作用 |
|---|---|
| `/exprepair` | 可点击帮助 —— 每条命令都带内联按钮。 |
| `/exprepair passive` | 开关**被动**修理（同时关闭手动）。 |
| `/exprepair manual` | 开关**手动**修理（同时关闭被动）。 |
| `/exprepair threshold` | 显示你当前的经验底线。 |
| `/exprepair threshold <levels>` | 设置被动修理不会花到该等级以下（`0` 表示清除）。 |
| `/exprepair status` | 显示你的被动、手动与阈值设置。 |
| `/exprepair serverdefaults` | 查看服务器对新玩家的默认设置。 |
| `/exprepair loginmessage` | 为你自己开关登录时的状态提示。 |
| `/exprepair version` | 显示已安装的模组版本。 |

### 🛡️ 管理员

所有管理员命令都需要**游戏管理员权限**（op 等级 2 及以上）。

| 命令 | 作用 |
|---|---|
| `/exprepair admin` | 查看全服默认值与允许开关。 |
| `/exprepair admin <player>` | 查看该玩家当前的实时设置。 |
| `/exprepair admin <player> reset` | 把某位玩家重置为当前服务器默认值。 |
| `/exprepair admin <player> passive on\|off` | 为某位玩家强制开启/关闭被动。 |
| `/exprepair admin <player> manual on\|off` | 为某位玩家强制开启/关闭手动。 |
| `/exprepair admin <player> threshold <levels>` | 设置某位玩家的经验底线。 |
| `/exprepair admin passive on\|off` | 设置新玩家的**默认**被动状态。 |
| `/exprepair admin passive allow on\|off [silent]` | 全服允许或**禁止**被动修理。 |
| `/exprepair admin manual on\|off` | 设置新玩家的**默认**手动状态。 |
| `/exprepair admin manual allow on\|off [silent]` | 全服允许或**禁止**手动修理。 |
| `/exprepair admin threshold <levels>` | 设置新玩家的默认经验底线。 |
| `/exprepair admin maxXpPerRepair <xp>` | 设置每次修理最多花费的经验（最小 `1`）。 |
| `/exprepair admin reload [silent]` | 从磁盘重新读取 `exprepair.json` —— 无需重启。 |

> [!TIP]
> 在 `allow`、`reload` 之类的命令后加上 `silent`，可以**不**向全服在线玩家广播就应用改动 —— 适合会话
> 中途悄悄调整。

## 💡 使用场景

- **一劳永逸的生存玩家。** 给盔甲和工具附上经验修补，执行 `/exprepair passive`，整套装备就会在你游玩时
  靠周遭的经验自我维持 —— 再也不会在洞穴深处装备突然报废。
- **缺经验的附魔师。** 你正在为一次大附魔攒等级。执行 `/exprepair threshold 30`，被动修理就只会花掉
  **高于** 30 级的经验 —— 装备照样在修，附魔本钱却被保住。
- **讲究掌控的硬核玩家。** 你想按自己的节奏修理。切到 `/exprepair manual`，在你决定的那一刻用「潜行 +
  右键」把手中工具补满 —— 除此之外，经验条一滴不流。
- **以 PvP 为主的服务器。** 管理员执行 `/exprepair admin passive allow off`，把被动自动修理挡在竞技场
  之外，或者借助 PvP 抑制钩子让战斗模组在战斗期间暂停修理 —— 同时在别处仍允许玩家自由修理。

## ⚙️ 配置

服务器设置保存在 **`config/exprepair.json`**，首次启动时生成，可在游戏内用 `/exprepair admin …` 编辑。
通过命令做的修改会立即保存；直接改文件的内容则执行 `/exprepair admin reload` 后生效 —— 无需重启。

| 键 | 默认值 | 含义 |
|---|:---:|---|
| `maxXpPerRepair` | `8` | 每次修理最多花费的经验 —— 被动为每 tick，手动为每次点击。1 经验 = 2 耐久，所以 `8` = 最多 16 耐久。最小 `1`。 |
| `defaultPassive` | `false` | 新玩家是否默认开启被动修理。 |
| `defaultManual` | `false` | 新玩家是否默认开启手动修理。 |
| `defaultThreshold` | `0` | 新玩家的默认经验等级底线（`0` = 不设底线）。 |
| `allowPassive` | `true` | 总开关 —— 若为 `false`，**任何**玩家都不能使用被动修理。 |
| `allowManual` | `true` | 总开关 —— 若为 `false`，**任何**玩家都不能使用手动修理。 |

> [!NOTE]
> `defaultPassive` 与 `defaultManual` 不能同时为开 —— 两种模式互斥，如果都设为 `true`，则手动优先、
> 被动被强制关闭。每位玩家的状态（所选模式、阈值、登录消息开关）单独保存在
> `config/exprepair/playerdata.json`。

## 📦 版本与下载

> [!NOTE]
> 本仓库采用**每个版本一个分支**的结构。这个 `main` 分支**只有文档** —— 每个 Minecraft 版本的代码都在
> 各自的分支上，各自拥有独立的历史与 `CHANGELOG.md`。

| 分支 | Minecraft | 加载器 | 依赖 | 日志 |
|:------:|:---------:|:-------:|:------------:|:---:|
| [`multi_26.1-3`](https://github.com/LunixiaLIVE/expRepair/tree/multi_26.1-3) | 26.1 – 26.3 *（一个 jar 通吃）* | Fabric · NeoForge | Fabric API *（仅 Fabric）* | [📄](https://github.com/LunixiaLIVE/expRepair/blob/multi_26.1-3/CHANGELOG.md) |
| [`multi_1.21.11`](https://github.com/LunixiaLIVE/expRepair/tree/multi_1.21.11) | 1.21.11 | Fabric · NeoForge | Fabric API *（仅 Fabric）* | [📄](https://github.com/LunixiaLIVE/expRepair/blob/multi_1.21.11/CHANGELOG.md) |
| [`multi_1.21.5`](https://github.com/LunixiaLIVE/expRepair/tree/multi_1.21.5) | 1.21.5–1.21.10 | Fabric · NeoForge | Fabric API *（仅 Fabric）* | [📄](https://github.com/LunixiaLIVE/expRepair/blob/multi_1.21.5/CHANGELOG.md) |
| [`multi_26.2`](https://github.com/LunixiaLIVE/expRepair/tree/multi_26.2) | 26.2.x *（已归档）* | Fabric · NeoForge | Fabric API *（仅 Fabric）* | [📄](https://github.com/LunixiaLIVE/expRepair/blob/multi_26.2/CHANGELOG.md) |
| [`multi_26.1`](https://github.com/LunixiaLIVE/expRepair/tree/multi_26.1) | 26.1, 26.1.1, 26.1.2 *（已归档）* | Fabric · NeoForge | Fabric API *（仅 Fabric）* | [📄](https://github.com/LunixiaLIVE/expRepair/blob/multi_26.1/CHANGELOG.md) |

> [!TIP]
> 每个 `multi_*` 分支都会构建出**一个 `-multi.jar`，同时支持 Fabric 与 NeoForge**。在 26.x 上它是单个
> 合并 jar（那里的 Minecraft 没有混淆）；在 1.21.x 上则是 jar-in-jar 打包，把 Fabric 与 NeoForge 构建
> 嵌在里面，由各自的加载器取用。也会产出各加载器单独的 `-fabric` / `-neoforge` jar（在
> `build/staging/`）。完全自包含 —— **无需额外安装任何前置库模组**。

<details>
<summary>🛠️ <b>从源码构建</b></summary>

每个代码分支都是一个自包含的 Gradle 项目。检出与你 Minecraft 版本对应的分支：

```bash
git clone -b multi_26.1-3 https://github.com/LunixiaLIVE/expRepair.git
cd expRepair
./gradlew build
```

`-multi` jar 会出现在 `build/libs/` —— 把它放进任一加载器的 `mods/` 文件夹即可。
</details>

## 📄 许可

以 **MIT 许可证**发布。

<div align="center"><sub>⛏️ 属于 <a href="https://github.com/LunixiaLIVE/Lunixia-Minecraft-QOL-Mods">Lunixia's Minecraft QOL Mods</a> 的一部分。</sub></div>
