---
title: "论文阅读AutoDroid"
date: 2025-05-16
categories: [论文阅读]
tags: [自动测试, LLM, Agent]
math: true
mermaid: true
---

### AutoDroid 论文内容概述

论文标题为《AutoDroid: LLM-powered Task Automation in Android》，作者提出了一种基于大型语言模型（LLMs）的安卓任务自动化系统 AutoDroid，旨在通过结合通用知识和应用特定知识，实现无需手动干预的任意安卓应用任务自动化。以下是对论文内容的详细总结，包括其解决的问题、使用的方法，以及与前述论文《Mobile-Agent-v2》的对比。

---

### 解决的问题

移动设备任务自动化（如在安卓手机上执行任务：删除日历事件、发送消息等）是提升用户体验和实现智能助手的重要技术。然而，现有方法面临以下挑战：

1. **GUI 表示（GUI Representation）**：
   - LLMs 擅长处理文本，但安卓应用的图形用户界面（GUI）是视觉和结构化的，难以直接输入到 LLMs。
   - GUI 状态（如屏幕上的按钮、文本框）通常包含大量信息（平均约 40k 标记），需要简化和结构化以供 LLMs 理解。

2. **知识整合（Knowledge Integration）**：
   - LLMs 拥有通用知识，但缺乏特定应用的领域知识。例如，完成“删除日历中所有事件”需要知道点击“更多选项”和“设置”才能到达目标界面，单靠通用知识难以推断。
   - 应用通常是复杂的有限状态机，任务需要跨多个界面导航，LLMs 需要应用特定知识来规划路径。

3. **成本优化（Cost Optimization）**：
   - 查询 LLMs 的计算成本高（例如，LLaMA-7B 每标记需要 67 亿 FLOPs），而复杂任务可能需要多次查询。
   - 冗长的 GUI 描述和频繁的 LLM 调用导致延迟和成本增加，影响实时任务自动化的响应性。

4. **可扩展性和手动努力**：
   - 传统方法（如 Siri、Google Assistant）依赖开发者定义任务意图，需要大量手动配置。
   - 基于演示或强化学习的方法需要大规模人类演示或明确奖励函数，扩展性差，无法处理未见任务或黑盒应用。

AutoDroid 旨在通过结合 LLMs 的通用知识和通过动态分析获取的应用特定知识，实现无监督、任意任务的自动化，减少手动努力。

---

### 提出的方法：AutoDroid

AutoDroid 是一个端到端的移动任务自动化系统，分为离线阶段（获取应用知识）和在线阶段（执行任务）。其核心是通过 GUI 表示、探索性记忆注入和多粒度查询优化，结合 LLMs 和应用特定知识。以下是方法的详细描述：

#### 1. **框架结构**
AutoDroid 包含以下主要组件：

- **GUI 解析模块**：
  - 将安卓 GUI 转换为简化的 HTML 风格文本表示，包含五种标签（`<button>`、`<checkbox>`、`<scroller>`、`<input>`、`<p>`），保留关键属性（如 ID、标签、文本、点击后状态）。
  - 通过自动滚动收集可滚动界面的所有元素，纳入当前 GUI 状态，减少滚动操作的 LLM 查询。

- **探索性记忆注入模块**：
  - **离线阶段**：
    - 使用随机探索生成 UI 转换图（UI Transition Graph, UTG），记录 UI 状态和动作。
    - 通过 LLMs 分析 UTG 中的 UI 元素，生成模拟任务表（Simulated Task Table），包含每个元素的任务功能、所需 UI 状态和元素序列。
    - 生成 UI 功能表，总结每个 UI 状态的功能。
  - **在线阶段**：
    - 使用嵌入模型（Instructor-XL）计算用户任务与模拟任务的余弦相似度，选择 k 个最相关的模拟任务。
    - 在提示中添加相关 UI 元素的“onclick”属性，提示点击后可能的功能，指导 LLMs 决策。

- **提示生成器**：
  - 生成包含任务描述、当前 GUI 状态（HTML 格式）、历史动作和应用记忆的提示。
  - 限制 LLMs 输出格式为 `<id, action, input text>`，确保动作可执行。

- **隐私过滤器**：
  - 使用 PII 扫描器检测提示中的敏感信息（如姓名、电话号码），替换为占位符（如 `<name>` 替换为“Alice”），保护用户隐私。

- **任务执行器**：
  - 解析 LLMs 的动作输出，执行点击、输入或滑动。
  - 检测高风险动作（如删除数据），要求用户确认。

- **多粒度查询优化模块**：
  - **剪枝和合并**：删除无用元素，合并功能等价的 UI 元素（如 UTG 中指向同一状态的元素），减少提示标记数。
  - **GUI 合并**：将可滚动界面的所有元素纳入单一提示，减少滚动相关的 LLM 调用。
  - **快捷路径**：对于与模拟任务高度相似的用户任务，直接执行记忆中的动作序列，跳过 LLM 查询。

- **本地 LLM 微调**：
  - 对本地小模型（如 Vicuna-7B）使用应用特定数据进行微调，生成（问题，答案）对。
  - 使用大模型（如 GPT-4）生成零样本思维链（Zero-shot CoT）答案，包含选择动作的推理过程，提升小模型的推理能力。

#### 2. **工作流程**
- **离线阶段**：
  1. 随机探索应用，生成 UTG。
  2. 分析 UTG，生成模拟任务表和 UI 功能表，存储在应用记忆中。
  3. 使用嵌入模型将模拟任务映射为向量，用于在线查找。
- **在线阶段**：
  1. 用户输入自然语言任务。
  2. 提示生成器根据任务、当前 GUI 和应用记忆生成提示。
  3. 隐私过滤器处理提示，发送至 LLM（在线 GPT-4/GPT-3.5 或本地 Vicuna）。
  4. LLM 返回动作，任务执行器验证并执行。
  5. 重复直到任务完成。

#### 3. **模型选择**
- **在线 LLM**：GPT-4、GPT-3.5，通过提示增强。
- **本地 LLM**：Vicuna-7B，通过应用特定数据微调。
- **嵌入模型**：Instructor-XL，用于任务相似度计算。
- **硬件**：在 OnePlus ACE 2 Pro（Snapdragon 8 Gen2）上运行本地 LLM，边缘服务器（NVIDIA 3090）支持推理。

#### 4. **评估方法**
- **基准测试（DroidTask）**：
  - 包含 158 个任务，覆盖 13 个开源安卓应用（如日历、联系人、短信）。
  - 任务来自 PixelHelp 数据集和应用常见功能，标注了每一步的 GUI 状态和动作。
  - 提供可重现的环境（安卓虚拟机快照），支持动态分析。
- **指标**：
  - 动作准确率（Action Accuracy）：动作与真实动作匹配的比例。
  - 完成率（Completion Rate）：完整正确执行任务的比例。
- **基线**：
  - META-GUI：基于训练的对话代理。
  - LLM-framework：基于 LLM 的 UI 任务自动化框架。
  - 随机选择和相似度选择（基于嵌入模型）。

#### 5. **实验结果**
- **动作准确率**：
  - AutoDroid（GPT-4）达到 90.9%，比基线（LLM-framework）提高 36.4%。
  - 点击（91.2%）、输入（82.5%）、完成判断（93.7%）均优于基线。
  - 自动滚动消除显式滚动动作，提升整体准确率。
- **任务完成率**：
  - AutoDroid（GPT-4）达到 71.3%，比基线提高 39.7%。
  - 随着步骤数增加，完成率下降，因多步骤任务可能有多种完成方式。
- **成本优化**：
  - 提示标记数从 625.3 降至 339.0，推理延迟降低 21.3%。
  - GUI 合并和快捷路径减少 13.7% 的 LLM 调用，正确快捷路径节省 38.02% 步骤。
- **消融研究**：
  - 记忆注入显著提升完成率（小模型更明显），因关键步骤依赖应用知识。
  - 零样本 CoT 微调提升输入和完成判断的准确率。
- **安全与隐私**：
  - 检测高风险动作的精度为 75.0%，召回率为 80.5%。
  - 隐私替换和安全确认略降低准确率（约 3%），但可接受。

#### 6. **创新点与贡献**
1. **首创结合 LLM 和应用知识**：
   - 通过动态分析获取应用特定知识，增强 LLM 的任务自动化能力。
2. **新型 GUI 表示**：
   - HTML 风格的 GUI 表示保留结构信息，适合 LLM 处理。
3. **探索性记忆注入**：
   - 通过 UTG 和模拟任务增强 LLM 的应用理解，解决知识不足问题。
4. **多粒度查询优化**：
   - 剪枝、GUI 合并和快捷路径降低计算成本，提升响应性。
5. **DroidTask 基准**：
   - 提供 158 个任务和可重现环境，促进 LLM 驱动的自动化研究。

---

### 与 Mobile-Agent-v2 的对比

以下从目标、方法、实现和性能等方面对比 AutoDroid 和 Mobile-Agent-v2：

#### 1. **目标与应用场景**
- **共同点**：
  - 两者都旨在实现移动设备任务自动化，减少手动干预，处理复杂多步骤任务。
  - 都结合 LLMs 的通用知识和特定知识（Mobile-Agent-v2 强调多代理协作，AutoDroid 强调动态分析）。
- **差异**：
  - **平台**：Mobile-Agent-v2 针对 Harmony OS 和 Android，支持非英语（中文）和英语场景；AutoDroid 专注于 Android，任务以英语为主。
  - **任务范围**：Mobile-Agent-v2 强调多应用场景（如从天气应用获取信息后在另一应用使用）；AutoDroid 更关注单应用任务，但支持任意黑盒应用的未见任务。
  - **知识来源**：Mobile-Agent-v2 通过规划、决策和反思代理协作，依赖记忆单元存储焦点内容；AutoDroid 通过随机探索和 UTG 分析，生成模拟任务表。

#### 2. **解决的问题**
- **共同问题**：
  - **GUI 表示**：两者都需要将 GUI 转换为 LLMs 可处理的文本。Mobile-Agent-v2 使用视觉感知模块（文本和图标识别），AutoDroid 使用 HTML 风格表示。
  - **知识不足**：两者都解决 LLMs 缺乏应用特定知识的问题，通过外部知识增强导航能力。
  - **错误处理**：Mobile-Agent-v2 通过反思代理纠正错误，AutoDroid 通过记忆注入和快捷路径减少错误。
- **差异**：
  - **成本优化**：AutoDroid 明确针对 LLM 查询的高成本问题，提出多粒度优化（剪枝、GUI 合并、快捷路径）；Mobile-Agent-v2 未重点讨论成本，仅通过规划代理简化决策输入。
  - **隐私与安全**：AutoDroid 引入隐私过滤器和高风险动作确认，Mobile-Agent-v2 未提及相关机制。
  - **可扩展性**：AutoDroid 强调无监督自动化，支持黑盒应用；Mobile-Agent-v2 更依赖多代理协作和手动知识注入（如操作知识）。

#### 3. **方法与实现**
- **架构**：
  - **Mobile-Agent-v2**：
    - 多代理架构（规划、决策、反思代理），每个代理有特定功能。
    - 视觉感知模块增强屏幕识别，记忆单元存储焦点内容。
    - 操作空间固定（点击、滑动、输入等六种）。
  - **AutoDroid**：
    - 单代理架构，依赖 LLM 和外部模块（GUI 解析、记忆注入、查询优化）。
    - HTML 风格 GUI 表示，UTG 和模拟任务表提供知识。
    - 操作空间包括点击、输入、滑动，自动滚动减少显式操作。
- **知识获取**：
  - **Mobile-Agent-v2**：通过规划代理总结历史操作，反思代理分析操作结果，记忆单元存储任务相关内容。
  - **AutoDroid**：通过离线随机探索生成 UTG，LLM 分析生成模拟任务表，在线阶段通过嵌入模型匹配相关知识。
- **错误纠正**：
  - **Mobile-Agent-v2**：反思代理比较操作前后屏幕，检测错误并指导回退或重试。
  - **AutoDroid**：依赖记忆注入减少错误，快捷路径跳过简单步骤，LLM 推理纠正导航错误。
- **优化**：
  - **Mobile-Agent-v2**：通过规划代理简化决策输入，减少长序列负担。
  - **AutoDroid**：多粒度优化（剪枝、GUI 合并、快捷路径）显著降低标记数和查询次数。

#### 4. **性能与评估**
- **Mobile-Agent-v2**：
  - **基准**：88 条指令，覆盖 5 个系统应用、5 个外部应用，测试多应用场景。
  - **指标**：成功率（55% 高级指令）、完成率、决策准确率、反思准确率。
  - **结果**：比单代理 Mobile-Agent 提高 30%（非英语）、27%（英语），多应用任务提高 37.5%。
  - **优势**：多代理协作和反思机制在长序列和多应用任务中表现优异。
- **AutoDroid**：
  - **基准**：DroidTask，158 个任务，13 个开源应用，提供可重现环境。
  - **指标**：动作准确率（90.9%）、完成率（71.3%）。
  - **结果**：比 LLM-framework 提高 36.4%（准确率）、39.7%（完成率），查询成本降低 51.7%。
  - **优势**：高准确率和完成率，成本优化显著，适合黑盒应用。
- **对比**：
  - AutoDroid 的动作准确率（90.9%）高于 Mobile-Agent-v2（未明确报告整体准确率，但决策准确率较高）。
  - AutoDroid 的完成率（71.3%）优于 Mobile-Agent-v2 的高级指令成功率（55%）。
  - AutoDroid 的基准更全面（158 vs. 88 任务），支持可重现环境，适合学术研究。
  - Mobile-Agent-v2 在多应用场景和非英语环境中表现更强，AutoDroid 更适合单应用和成本敏感场景。

#### 5. **优缺点**
- **Mobile-Agent-v2**：
  - **优点**：
    - 多代理协作分工明确，适合复杂任务。
    - 反思机制有效纠正错误，提升鲁棒性。
    - 支持多应用和非英语场景，应用范围广。
  - **缺点**：
    - 未优化 LLM 查询成本，计算开销可能较高。
    - 依赖手动知识注入，扩展性略逊。
    - 未考虑隐私和安全问题。
- **AutoDroid**：
  - **优点**：
    - 无监督自动化，支持黑盒应用，扩展性强。
    - 成本优化显著，适合实时应用。
    - 隐私和安全机制完善，适合实际部署。
  - **缺点**：
    - 单代理架构可能在多应用场景中效率较低。
    - 对多语言支持未明确测试。
    - 离线阶段的 UTG 生成需较长时间（0.5-1 小时/应用）。

#### 6. **适用场景**
- **Mobile-Agent-v2**：适合需要多应用协作、长序列任务或非英语环境的场景，如跨应用信息传递或复杂操作。
- **AutoDroid**：适合单应用任务、黑盒应用或成本敏感的场景，如快速部署在安卓设备上的自动化助手。

---

### 总结

**AutoDroid** 通过结合 LLMs 和动态分析生成的應用特定知識，實現了安卓設備上任意任務的無監督自動化。其核心創新在於 HTML 風格的 GUI 表示、探索性記憶注入和多粒度查詢優化，顯著提升了動作準確率（90.9%）和任務完成率（71.3%），並降低了51.7%的查詢成本。與 **Mobile-Agent-v2** 相比，AutoDroid 在可擴展性、成本優化和隱私安全方面更具優勢，適合黑盒應用和單應用場景；而 Mobile-Agent-v2 的多代理架構和反思機制在多應用和長序列任務中表現更強。兩者均代表了 LLM 驅動移動任務自動化的前沿進展，適用場景各有側重。
