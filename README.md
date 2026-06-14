# Agent Intelligent System - 智能体系统

[![Python Version](https://img.shields.io/badge/python-3.11+-blue.svg)](https://www.python.org/downloads/)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

基于 LangChain 和 LangGraph 的智能体系统，实现三种不同架构的 AI 助手，专注于金融投资领域应用。

## 📑 目录

- [项目概述](#项目概述)
- [架构介绍](#架构介绍)
- [快速开始](#快速开始)
- [模块说明](#模块说明)
- [技术栈](#技术栈)
- [贡献指南](#贡献指南)
- [许可证](#许可证)

## 项目概述

本项目展示了三种不同的智能体架构在金融投资领域的实际应用，包括反应式智能体、深思熟虑型智能体以及混合架构智能体，为用户提供全方位的智能化金融服务。

### 核心特点

- **多架构支持**：实现了 Reactive、Deliberative 和 Hybrid 三种智能体架构
- **金融场景聚焦**：专注于私募基金、投资研究和财富管理
- **中文友好**：全中文界面和文档，基于通义千问模型
- **可扩展**：模块化设计，易于添加新功能和优化

## 架构介绍

本项目实现了三种经典的智能体架构：

### 1. 反应式智能体 (Reactive Agent)
- **特点**：快速响应、工具调用、基于规则
- **应用**：私募基金问答助手
- **实现**：使用 Agent 模式和工具选择机制

### 2. 深思熟虑型智能体 (Deliberative Agent)
- **特点**：内部建模、多步推理、方案评估、长期规划
- **应用**：智能投研助手
- **实现**：基于 LangGraph 的状态机工作流

### 3. 混合智能体 (Hybrid Agent)
- **特点**：结合反应式和深思熟虑架构的优势
- **应用**：财富管理投顾AI助手
- **实现**：三层架构，动态切换处理模式

## 快速开始

### 环境要求

- Python 3.11+
- 通义千问 API Key

### 安装依赖

```bash
# 克隆项目
cd Agent_Intelligent_System

# 建议使用虚拟环境
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# 安装依赖
pip install -r Deliberative/requirements.txt
# 或者安装所有模块的依赖
pip install dashscope==1.22.1 langchain==0.3.25 langchain_community==0.3.24 langchain_core==0.3.59 langgraph==0.4.3 qwen_agent==0.0.19 langchain_classic
```

### 配置 API Key

在各模块的 `.env` 文件中配置你的通义千问 API Key：

```env
DASHSCOPE_API_KEY=your_api_key_here
```

或者直接在代码中配置：

```python
DASHSCOPE_API_KEY = 'your_api_key_here'
```

### 运行示例

#### 私募基金问答助手 (Reactive)
```bash
cd Reactive
python fund_qa_langgraph.py
```

#### 智能投研助手 (Deliberative)
```bash
cd Deliberative
python deliberative_research_langgraph.py
```

#### 财富管理投顾助手 (Hybrid)
```bash
cd Hybrid
python hybrid_wealth_advisor_langgraph.py
```

## 模块说明

### 1. Reactive/ - 反应式智能体模块

#### 私募基金运作指引问答助手

```
Reactive/
├── fund_qa_langgraph.py      # 基于 LangChain 经典 Agent 的实现
├── fund_qa_qwen_agent.py     # 基于 Qwen Agent 的实现
└── .env                      # 环境变量配置
```

**功能特点**：
- 关键词搜索私募基金规则
- 按类别查询监管规定
- 直接回答用户问题
- 知识库问答系统

**内置工具**：
- `search_rules_by_keywords`: 通过关键词搜索相关规则
- `search_rules_by_category`: 按类别查询规则
- `answer_question`: 直接回答用户问题

### 2. Deliberative/ - 深思熟虑型智能体模块

#### 智能投研助手

```
Deliberative/
├── deliberative_research_langgraph.py      # 基于 LangGraph 的实现
├── deliberative_research_qwen_agent-2.py   # 基于 Qwen Agent 的实现
├── deliberative_research_agent_documentation.md  # 详细文档
├── deliberative_research_agent_comparison.md    # 架构对比
├── requirements.txt                  # 依赖列表
└── .env                             # 环境变量配置
```

**工作流程**：
1. **感知阶段**：收集市场数据和信息
2. **建模阶段**：构建内部世界模型
3. **推理阶段**：生成多个候选分析方案
4. **决策阶段**：选择最优投资观点
5. **报告阶段**：生成完整研究报告

**核心输出**：结构化的投资研究报告，包含市场分析、投资建议和风险评估

### 3. Hybrid/ - 混合智能体模块

#### 财富管理投顾 AI 助手

```
Hybrid/
├── hybrid_wealth_advisor_langgraph.py   # 混合架构实现
├── hybrid_wealth_advisor_qwen_agent.py  # Qwen Agent 版本
├── requirements.txt                  # 依赖列表
└── .env                             # 环境变量配置
```

**三层架构**：
- **底层（反应式）**：即时响应客户查询
- **中层（协调）**：评估任务类型，动态选择处理模式
- **顶层（深思熟虑）**：复杂投资分析和长期财务规划

**特色功能**：
- 客户画像分析
- 市场数据整合
- 个性化投资建议
- 风险评估与管理

## 技术栈

### 核心框架
- **LangChain**: LLM 应用开发框架
- **LangGraph**: 构建状态智能体的图形框架
- **Qwen Agent**: 通义千问官方 Agent 框架

### 大语言模型
- **通义千问 (Tongyi/Qwen)**: 阿里云大语言模型

### 其他工具
- **Pydantic**: 数据验证和设置管理
- **Python-dotenv**: 环境变量管理

## 项目结构详解

```
Agent_Intelligent_System/
├── README.md                    # 项目文档（本文件）
├── Reactive/                    # 反应式智能体
│   ├── fund_qa_langgraph.py
│   ├── fund_qa_qwen_agent.py
│   └── .env
├── Deliberative/                # 深思熟虑型智能体
│   ├── deliberative_research_langgraph.py
│   ├── deliberative_research_qwen_agent-2.py
│   ├── deliberative_research_agent_documentation.md
│   ├── deliberative_research_agent_comparison.md
│   ├── requirements.txt
│   └── .env
├── Hybrid/                      # 混合智能体
│   ├── hybrid_wealth_advisor_langgraph.py
│   ├── hybrid_wealth_advisor_qwen_agent.py
│   ├── requirements.txt
│   └── .env
└── research_report_*.txt        # 生成的研究报告（示例）
```

## 使用示例

### 智能投研助手使用流程

```python
from Deliberative.deliberative_research_langgraph import run_research_agent

# 运行研究
result = run_research_agent(
    topic="新能源汽车行业投资机会",
    industry="电动汽车制造、电池技术",
    horizon="中期"
)

# 查看报告
print(result.get("final_report"))
```

### 私募基金问答助手使用流程

```python
# 脚本会交互式询问
# 请输入研究主题: 私募基金合格投资者标准
# 助手会返回相关答案
```

## 贡献指南

欢迎贡献！如果你想：

1. 报告 Bug
2. 提交功能请求
3. 贡献代码

请遵循以下步骤：

1. Fork 本项目
2. 创建你的特性分支 (`git checkout -b feature/AmazingFeature`)
3. 提交你的更改 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 开启一个 Pull Request

## 开发计划

- [ ] 添加更多金融工具和数据源
- [ ] 优化 LLM 提示，提高回答质量
- [ ] 添加 Web 界面
- [ ] 支持更多大语言模型
- [ ] 添加单元测试和集成测试
- [ ] 提供更多实际金融场景示例

## 许可证

本项目采用 MIT 许可证 - 详见 [LICENSE](LICENSE) 文件

## 免责声明

本项目仅供学习和研究使用。所有生成的投资建议和分析仅为演示目的，不构成任何投资建议。请在实际投资决策前咨询专业金融顾问。

## 联系方式

如有问题或建议，欢迎通过以下方式联系：

- 提交 Issue
- 发送邮件至项目维护者

---

**注意**：本项目是一个教学和研究项目，展示了如何使用现代 AI 技术构建金融领域的智能体。请在实际应用中进行充分测试和验证。
