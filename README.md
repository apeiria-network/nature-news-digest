# Nature News Walkman

一个面向 **Nature 新闻阅读与听力练习** 的 Claude Code skill 项目。

当前项目已经拆分为 3 个并列子 skill，用同一套共享检索与 TTS 支撑逻辑来提供不同输出模式：

- [nature-news-brief/SKILL.md](nature-news-brief/SKILL.md) — 摘要模式，输出最新 Nature 新闻 shortlist 的英文摘要
- [nature-news-text/SKILL.md](nature-news-text/SKILL.md) — 全文阅读模式，先展示 shortlist，再按用户选择输出英文原文
- [nature-news-sound/SKILL.md](nature-news-sound/SKILL.md) — 听读模式，先展示 shortlist，再按用户选择输出英文原文并生成 `.mp3`

---

## 子 Skill 说明

### 1. `nature-news-brief`
适合需要快速浏览最新 Nature 新闻的场景。

特点：
- 拉取或复用最新 Nature 新闻 shortlist
- 输出最多 10 条排序后的新闻摘要
- 每条摘要控制在约 75–100 词
- 可按用户要求附加中文提示或要点

入口文件：
- [nature-news-brief/SKILL.md](nature-news-brief/SKILL.md)

### 2. `nature-news-text`
适合需要做英文精读的场景。

特点：
- 先展示 shortlist 摘要预览
- 等待用户选择新闻编号
- 仅对所选新闻输出完整英文原文
- 可按用户要求附加中文说明

入口文件：
- [nature-news-text/SKILL.md](nature-news-text/SKILL.md)

### 3. `nature-news-sound`
适合需要做英文听力或听读结合练习的场景。

特点：
- 先展示 shortlist 摘要预览
- 等待用户选择新闻编号
- 输出所选新闻完整英文原文
- 生成并交付英文 `.mp3` 音频
- 在 gTTS 不可用时自动回退到 edge-tts

入口文件：
- [nature-news-sound/SKILL.md](nature-news-sound/SKILL.md)

---

## 共享文档

### 检索与缓存规则
- [references/search_guide.md](references/search_guide.md)

用于统一定义：
- Nature 最新新闻发现方式
- top-10 shortlist 排序规则
- 缓存结构与复用策略
- 通用错误处理说明

### TTS 规则
- [references/TTS_guide.md](references/TTS_guide.md)

用于统一定义：
- 本地 `.venv` 运行时约定
- gTTS / edge-tts 的使用方式
- 自动 fallback 策略
- 音频生成相关说明

---

## 目录结构

```text
nature-news-walkman/
├─ nature-news-brief/
│  └─ SKILL.md
├─ nature-news-text/
│  └─ SKILL.md
├─ nature-news-sound/
│  └─ SKILL.md
├─ references/
│  ├─ search_guide.md
│  └─ TTS_guide.md
├─ scripts/
│  ├─ nature_digest.py
│  ├─ summary_cache.py
│  └─ local_venv.py
└─ requirements.txt
```

---

## 脚本说明

### [scripts/nature_digest.py](scripts/nature_digest.py)
TTS 辅助脚本，负责：
- 文本清洗
- gTTS 生成
- edge-tts 生成
- 自动 fallback
- 默认输出目录解析

### [scripts/summary_cache.py](scripts/summary_cache.py)
shortlist 缓存读写辅助脚本，负责：
- 读取最新缓存批次
- 过滤无效 new 条目
- 写入规范化后的缓存结构

### [scripts/local_venv.py](scripts/local_venv.py)
本地 Python 环境辅助脚本，负责：
- 检查 `.venv` 是否存在
- 检查 Python 版本是否满足要求
- 检查 `gtts` / `edge_tts` 模块是否齐全
- 在需要时安装 [requirements.txt](requirements.txt) 中的依赖

---

## 依赖与运行时

### Python
- 支持 Python 3.10+
- 本地 `.venv` 复用/创建逻辑由 [scripts/local_venv.py](scripts/local_venv.py) 管理

### 依赖
见 [requirements.txt](requirements.txt)：
- `gTTS`
- `edge-tts`

安装示例：

```bash
python -m venv .venv
.venv/Scripts/python -m pip install -r requirements.txt
```

> 在 Windows + bash 环境下，上面的路径写法仅用于说明；实际 skill 运行时会优先通过项目脚本自动处理本地 `.venv`。

---

## 输出文件与缓存

### 缓存文件
- `Nature_LatestBatch_Cache.json`

用于保存最近一次共享 shortlist 批次，便于在 `brief / text / sound` 之间复用。

### 音频文件
- `Nature_News{N}_English.mp3`

用于保存 `nature-news-sound` 生成的英文新闻音频。

默认优先输出到：
- `D:/nature-news-digest-sounds`

如果不可用，则回退到：
- `C:/nature-news-digest-sounds`

---

## 维护说明

如果后续继续扩展项目，建议遵循以下约定：

- 每个输出模式一个独立子 skill
- 检索与 shortlist 规则统一放在 [references/search_guide.md](references/search_guide.md)
- TTS 规则统一放在 [references/TTS_guide.md](references/TTS_guide.md)
- Python 运行时和缓存逻辑尽量继续复用 [scripts/](scripts/)
- 不再恢复旧的“根 SKILL + 子命令 guide”结构
