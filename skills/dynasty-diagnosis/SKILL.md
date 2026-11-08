---
name: dynasty-diagnosis
description: Diagnose a codebase's governance structure as a Chinese dynasty, with a Western empire equivalent for international readers — Qin as a unified monorepo, Zhou as feudal microservices, Tang as an open plugin ecosystem, Song as commercial boom with weak defense, Ming as closed borders with pinned deps, Qing as total isolation. Use when the user wants a memorable read on architecture governance, monorepo versus microservices in one picture, or an unusual but evidence-based angle on dependency policy and release cadence.
---

# Dynasty Diagnosis 朝代诊断

判一个代码库的**治理结构**属于哪种形态，用朝代作比喻。判断依据必须是可测量的组织结构，不是感觉。

Diagnose how a codebase is *governed* — where decisions are made, how far orders travel, and whether the borders are open. The dynasty is the label; the governance is the finding.

## 语言 Language

用用户提问的语言回答，**小标题也一起翻译**，不要一半中文一半英文。代码、命令、路径、配置项保持英文。朝代名、治理术语这类**中文语境本身**的概念保留中文（秦/周/唐/宋/明/清），英文读者可同时给出 Empire equivalent。

Reply in the language the user wrote in, including the section headings. Keep code, commands and paths in English. The dynasty names are the concept itself — keep them in Chinese and offer the Western empire equivalent for readers who need it.

## 为什么用朝代

「单体仓库 vs 微服务」这种讨论往往说了半天还是各说各话，因为它其实是一个**权力结构**问题：谁来决定接口、命令能传多远、边关开不开。朝代是中国人一秒钟就能听懂的权力模型，比架构图更快形成共识。

For international readers, every dynasty has an empire equivalent so the same finding travels.

## 判断流程

### 1. 测量治理信号

```bash
# 边界：有多少个独立的发布/构建单元
find . -name 'package.json' -o -name 'pom.xml' -o -name 'Cargo.toml' -o -name 'go.mod' \
  -o -name 'CMakeLists.txt' -o -name 'pyproject.toml' | grep -v node_modules

# 依赖方向：跨模块引用有多少、是否成环
# （用语言对应的工具：madge / pydeps / jdeps / cargo tree）

# 发布节奏
git tag --sort=-creatordate | head -10
git log --format='%ad' --date=format:'%Y-%m' | sort | uniq -c | tail -12

# 外部参与
git shortlog -sn --all | head -10

# 依赖新鲜度（闭关程度）
# 检查 lockfile 的 last-modified 与主要依赖的大版本落后情况
```

### 2. 对号入座

| 朝代 | 西方对应 | 治理特征 | 可测信号 |
|---|---|---|---|
| **秦 Qin** | The Unitary State | 大一统单仓，一个构建、一个发布列车、中央 CI 说了算 | 单一根构建文件；所有代码一个 tag；跨模块引用无约束 |
| **周 Zhou** | The Feudal System | 分封微服务，网关是天子但管不动诸侯 | 多仓库或多包；每个服务自己的锁文件与发布节奏；网关只做转发 |
| **汉 Han** | The Mixed Empire | 核心郡县 + 外围诸侯：单仓主干 + 外包/第三方的独立服务 | 主干 monorepo 内聚，但存在几个完全独立部署的目录 |
| **唐 Tang** | The Open Frontier | 开放盛世：公开 API、插件生态、外部贡献活跃 | 有公开接口文档；外部 contributor 占比高；插件目录存在 |
| **宋 Song** | Commercial Boom, Weak Defense | 商业繁荣、军备废弛：功能多、测试薄、CI 不拦门 | 功能目录数量 vs 测试文件数量严重失衡；CI 存在但只跑 lint |
| **明 Ming** | The Closed Border | 海禁：依赖锁死、代码 vendored、拒绝升级 | 依赖全量 pinned；存在 `vendor/` 或 `third_party/`；12 个月未升级大版本 |
| **清 Qing** | Total Isolation | 闭关锁国：构建离线、无依赖更新、无外部输入 | lockfile 两年未动；构建需要内网镜像；无外部贡献者 |

### 3. 输出

```markdown
# 朝代诊断：<朝代> / <Western equivalent>

**一句话：** <为什么是它，一句话>
**置信度：** 高 / 中 / 低（说明缺了哪个信号）

## 证据
| 信号 | 实测 | 说明 |
|---|---|---|
| 构建单元 | 1 个根 CMakeLists | 中央集权 |
| 发布节奏 | 单一 tag，季度发 | ... |
| 外部贡献者 | 0 | ... |

## 治理风险
<这个朝代最可能出事的环节>

## 一个具体动作
<本季度能做的、具体到一个目录的一步>
```

## 规则

- **先测量再命名。** 没有三个以上信号就别说朝代，说「结构不足以判断」。
- **朝代不是褒贬。** 秦可以是效率极高，清可以是极度稳定。风险要具体：秦的风险是「一处改动波及全域」，不是「秦不好」。
- **混合朝代要说出来。** 「主干是秦，边缘是周」是常见且真实的形态，比硬套一个更有价值。
- **给出一个动作。** 一次只给一个。
