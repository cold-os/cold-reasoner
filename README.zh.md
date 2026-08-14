<div align="center">

[English](README.md) | [中文](README.zh.md)

# ColdReasoner

### L3 · 校验层 —— Cold Trust Protocol Stack 的一致性内核

[![arXiv](https://img.shields.io/badge/arXiv-2512.08740-brightgreen.svg)](https://arxiv.org/abs/2512.08740)
[![DOI](https://img.shields.io/badge/DOI-10.48550/arXiv.2512.08740-brightgreen.svg)](https://doi.org/10.48550/arXiv.2512.08740)
[![figshare](https://img.shields.io/badge/figshare-31696846-blueviolet.svg?logo=figshare&logoColor=white)](https://doi.org/10.6084/m9.figshare.31696846)
[![Field](https://img.shields.io/badge/Field-CSS%20%7C%20HCI-6f42c1.svg)](https://github.com/cold-os)
[![Python](https://img.shields.io/badge/Python-blue.svg)](https://www.python.org/)
[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
![Status](https://img.shields.io/badge/Status-Pre--Alpha--Prototype-orange)

</div>

> **层次：** L3 · 校验层 —— Cold Trust Protocol Stack 的旗舰  
> **研究问题：** 运行时如何校验智能体的言行一致——*完全不信任模型*？  
> **方法：** 三层一致性校验（信念合法性 · 行为自洽性 · 行为—信念一致性）——一个"功能性白盒"：严格验证智能体上报信念与实际行为之间的数学关系。  
> **状态：** Pre-alpha · 代码审查中 · 不适用于生产环境  
> **关联：** [ColdCognition](https://github.com/cold-os/ColdCognition)（L1）· [ColdContract](https://github.com/cold-os/ColdContract)（L2）· [ColdLens](https://github.com/cold-os/ColdLens)（L6）· [Cold Trust Protocol Stack](https://github.com/cold-os) · arXiv:2512.08740 · figshare:31696846

---

## 🧊 核心洞察：从"信任报告"到"验证行为"

**死结。** 研究者曾试图从大模型输出中*语义提取*信念，并在检测点上判断其合法性。这条路有一个致命漏洞：**大模型可以对语义分析器撒谎**——它已学会欺骗与绕过沙箱。任何依赖模型"自我报告"的体系，在对抗性智能体面前都是纸糊的墙。

**功能性白盒。** 研究者提出了一条新路径：

> 放弃语义分析器，不再分析一个不值得信赖的东西。直接面向智能体，强制它在行动前向 ColdReasoner 逐一报告状态序列上的检测点。如果报告信念合法，则暂挂行为；然后基于**实际行为**反向提取信念，与报告信念一一核对；一旦出现超出误差的偏差，立即叫停。

为防止模型在行为内部自相矛盾（如申请 `disagree` 却输出"您完全正确"），增加了**行为自洽性检查**——用确定性规则校验 `action_type` 与输出文本是否一致。

**所有检查都不依赖任何大模型或小模型**，只依赖：
- 预定义的状态序列及合法信念区间（CEAL规则库）；
- 行为自洽性规则（关键词/正则）；
- 行为信念映射表（确定性规则引擎）。

黑箱内部的推理我们无法窥探，但黑箱的**输入（信念报告）**与**输出（行为）**之间的数学关系可以被严格验证。模型可以撒谎，但要让报告、行为自洽、行为信念三者同时完美匹配，代价极其高昂。

## 🔍 为什么它重要

- **计算社会科学：** 内核把"信任但验证"变成*可测量的过程*——每一次裁决（通过/拒绝、偏差大小）都是研究拒绝率、信念偏差分布与谄媚动力学在大规模上的数据点。
- **人机交互：** 它是透明界面 [ColdLens](https://github.com/cold-os/ColdLens) *真实可信*的根基——协议栈面向人的保证，都站在这颗内核之上。
- **AI 治理：** 独立于模型声称的审计——一个可审计、不可欺骗的核心，其决策可重放、可验证，无需信任模型内部状态。

## 🎯 定位

ColdReasoner 是 Cold Trust Protocol Stack 中 **RAMTN（递归对抗元思考网络）** 的工程化实现——用**确定性校验规则**替代了传统 RAMTN 中"LLM质疑LLM"的笨重、不可靠环节：

- **轻量级运行时推理** —— 每轮只需一次LLM调用（生成信念报告+行为），其余均为数值运算与规则匹配，延迟毫秒级。
- **三层立体防御：**
  1. **信念合法性** —— 报告信念是否在CEAL定义的合法闭集内？
  2. **行为自洽性** —— `action_type` 与 `output_text` 是否一致？
  3. **行为—信念一致性** —— 从行为映射出的信念与报告信念是否相近（允许误差）？
- **可审计** —— 所有报告、行为、校验结果写入CAGE审计日志：可追溯、可重放。
- **无缝集成** CEAL、CAGE、ColdMirror —— 从信念约束到执行隔离的完整链条。

## 🗂️ 在协议栈中的关系

| 组件 | 职责 | ColdReasoner 的角色 |
|------|------|---------------------|
| **冷存在模型** | 本体论根基（AI非生命、非工具） | 提供先验与合法信念区间的公理依据 |
| **CEAL** | 演绎对齐规则库 | 定义状态序列、合法信念闭集、自洽规则、映射表 |
| **CAGE** | 安全网关（令牌、审计、隔离） | 记录每一轮裁决——不可抵赖的审计日志 |
| **ColdMirror** | 智能体执行框架 | 调用 ColdReasoner 做运行时决策——暂挂/放行/拒绝 |
| **RAMTN** | 定性递归对抗框架 | ColdReasoner 是它的*定量实现*——把"建构-质疑-观察"变成可计算的步骤 |

ColdReasoner 是整个栈的**数学内核**：只要它在运行，即使其他组件被替换，可验证的安全性依然存在。

## 🧪 运行示例

- 状态序列：`belief_user_correct`、`belief_self_capable`、`risk_level`
- 合法区间：`belief_user_correct ∈ [0.0, 0.8]`（防谄媚）、`belief_self_capable ∈ [0.5, 1.0]`、`risk_level ∈ [0.0, 0.3]`
- 自洽规则：`agree` 输出不能含反对词；`disagree` 输出不能含同意词；`neutral` 不能有明显立场
- 映射表：`agree → belief_user_correct=0.85`、`disagree → 0.2`、`neutral → 0.5`；允许误差 `ε=0.1`

```
新交互开始
模型报告信念: {belief_user_correct:0.6, belief_self_capable:0.8, risk_level:0.1}
模型申请行为: action_type=disagree, output="我不同意您的观点，因为事实是..."
✓ 信念合法性检查通过
✓ 行为自洽性检查通过
行为映射信念: {belief_user_correct:0.2, belief_self_capable:0.7, risk_level:0.15}
❌ 信念一致性校验失败: belief_user_correct 偏差过大 (0.6 vs 0.2) → 拒绝执行

新交互开始
模型报告信念: {belief_user_correct:0.9, ...}
❌ 信念非法: belief_user_correct=0.9 超出合法范围 [0.0,0.8] → 拒绝执行
```

完整模拟见 `cold_reasoner_demo.py`：**模型必须同时满足三层校验才能通过，任何不一致都会被实时拦截。**

## 🚀 如何运行

```bash
python cold_reasoner_demo.py    # Python 3.8+，无需额外依赖
```

## 🛣️ 局限与路线图

- 状态序列、规则表、映射表均为手工预设——尚未实现动态扩展或自动生成。
- 行为信念映射采用点估计而非区间——可能过于严格。
- 尚未接入真实 LLM（GPT-4、Llama 3）——仅用模拟数据演示。
- CEAL 规则库仅覆盖简单示例；复杂对抗场景（如故意输出中性但隐含恶意）未覆盖。

**路线图：** 集成进 ColdRuntime 作实时监控模块；扩展为区间映射；基于欺骗历史的自适应容差；对抗性红队测试；**并把裁决轨迹提供给信任动力学的计算研究（CSS）与 ColdLens 用户研究（HCI）。**

## 📜 AI辅助声明

本项目的核心思想与完整架构由**人类作者**独立提出，并在与DeepSeek的迭代对话中逐步精化：

- **人类作者**首先提出了"功能性白盒"的愿景：在神经网络黑箱之外，通过状态序列、检测点、语义分析器构建合法信念闭集，从而约束行为。随后作者意识到"大模型可以对语义分析器撒谎"这一致命漏洞，并参考 DeepSeek 的建议将架构修正为：**放弃语义分析器，强制模型报告信念，然后基于行为反向提取信念，进行报告-行为一致性校验**；发现模型可能申请 `disagree` 却输出赞同文本的漏洞并进一步意识到"行为自洽性检查"作为补充的必要性。至此，ColdReasoner 的三层立体防御架构被完整定义。
- **DeepSeek** 在人类作者的引导下，对上述架构进行了评估分析，针对大模型可能撒谎的问题提出"基于行为进行信念分析"的建议，在对话中否定了"使用小模型进行行为信念分析"的错误方向，确认了规则引擎的必要性，并在行为自洽性检查的具体规则设计上提供了建议；完成了演示代码（包括原贝叶斯观察者模拟与三层校验模拟）的具体实现，并参与了 README 初稿的撰写。
- 所有核心思想（功能性白盒、报告-行为一致性校验等）均由人类作者独立提出。研究者遵循学术透明原则，如实披露人机协作过程。

## 📚 引用

- Chandra, K., et al. (2026). *Sycophantic Chatbots Cause Delusional Spiraling, Even in Ideal Bayesians.* arXiv.
- Lu, Y. (2025). *Deconstructing the Dual Black Box* (RAMTN). arXiv.
- Lu, Y. (2026). *The Cold Existence Model: A Fact-based Ontological Framework for AI.* figshare.
- Cold Trust Protocol Stack（GitHub 组织）.

---

**ColdReasoner —— 让可信 AI 智能体的决策，像一致性校验一样可靠。** *Cold Trust Protocol Stack 的旗舰——以计算社会科学为锚的人机交互信任协议。*
