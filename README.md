# aging_KB_Bili
a knowledge base in aging based on videos in Bilibili, and analysis result based on the knowledge base
> 基于 DeepSeek V4 Flash，利用 `bilibili-local-json-processing` skill（Mode B），将 Bilibili AI 字幕 JSON 文件批量处理为结构化分析 Markdown。

## Pipeline

```
new/ (原始字幕JSON)                       deepseek/ (结构化分析)
  ├── 原始字幕_BVxxx.json                  ├── BVxxx_关键词.md
  ├── 原始字幕_BVyyy.json                  ├── BVyyy_关键词.md
  └── ...                                 ├── ...
                                          ├── README.md (本文档)
                                          └── deepseek_summary_report.md (知识库分析)
```

---

## Step 1: JSON → MD 信息提取

### 输入

| 项目 | 内容 |
|------|------|
| **源格式** | Bilibili AI 字幕 JSON（`body` 数组，含 `from`/`to`/`content` 字段） |
| **源目录** | `~/bilibili视频文字材料/new/` |
| **处理方式** | `bilibili-local-json-processing` skill Mode B（LLM Content Analysis） |

### 处理流程

```
JSON 文件
  → json.load() 解析
  → 统计段数/时长
  → LLM 分析（DeepSeek V4 Flash）
    · 识别内容类型（科普/播客/采访/VLOG/学术）
    · 提取核心观点、数据、对比关系、金句
  → 按统一模板生成结构化 Markdown
  → 保存到 deepseek/
```

### 输出模板

每个分析文件包含：

| 部分 | 说明 |
|------|------|
| **元数据** | BV号、时长、来源、关键词 |
| **核心观点** | 结构化的发现/结论列表 |
| **机制解析** | 分子通路/因果关系链 |
| **金句摘录** | 原文关键引用 |
| **建议** | 内容创作层面 + 个人行动方向 |

### 内容类型分类

| 类型 | 特征 | 典型示例 |
|------|------|----------|
| **学术科普** | 讲论文/科学原理 | BV1z45g6vE97 衰老断崖期 |
| **播客访谈** | 长时长，对话体 | BV1Kj5g6VE3K 宠物医生对话 |
| **生活VLOG** | 个人化叙事 | BV1dJzKB3EH4 结婚九周年 |
| **医美/体验** | 第一人称全过程 | BV1dm4y1M7ZV 超声炮体验 |
| **街头采访** | 短问答体 | BV18C4y1r7yn 百岁老人 |
| **口述历史** | 个人经历叙事 | BV1FL5Sz4E6y 老石油人 |
| **政策科普** | 数据密集 | BV1AS4y1D7Jr 养老金危机 |
| **人生感悟** | 情感叙事 | BV1Gz4y1A7N6 中年身材 |

### 关键技术细节

- **自适应 Gap 阈值**：`median(gaps) <= 0.1s 且 segment count > 50 → 0.1s`，否则 `1.0s`
- **去重**：检查 `原始字幕_{BVID}.json` 是否存在
- **保存路径**：`deepseek/{BVID}_{关键词}.md`

---

## Step 2: 知识库全景分析

基于 `knowledge-base-analysis` skill，对 77 个分析文件进行：

| 阶段 | 内容 |
|:----:|------|
| **Phase 1** | 列出所有文件 → 标题提取 → 关键词打标签（20 个类别） |
| **Phase 2** | 构建核心概念网络（4 层分层结构） |
| **Phase 3** | 跨类别桥梁分析 + 反常识观点识别 |
| **Phase 4** | 生成 `deepseek_summary_report.md` |

---

## 文件命名约定

```
deepseek/
  BV12a4y1V7rA_衰老断崖.md           ← B站视频 (BV + 关键词)
  ...
```

---

## 工具链

| 工具 | 用途 |
|------|------|
| `bilibili-local-json-processing` (Mode B) | JSON 解析 + 分析模板 + 内容分类法 |
| `knowledge-base-analysis` | 知识库全景分析和总结报告 |
| DeepSeek V4 Flash | 底层 LLM 驱动分析 |
| `execute_code` (Python) | 批量文件读取、去重、统计 |

---
