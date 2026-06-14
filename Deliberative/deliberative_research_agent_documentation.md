# 深思熟虑型智能体 - 智能投研助手

## 项目描述

智能投研助手是一个基于LangGraph实现的深思熟虑型智能体(Deliberative Agent)，专为投资研究场景设计。该智能体能够整合市场数据，进行多步骤分析和推理，生成投资观点和研究报告。

与反应式智能体不同，深思熟虑型智能体通过内部建模、推理和规划，制定最优行动方案，特别适合需要多步骤思考和长期目标优化的任务。

### 核心特点

- **内部建模**：构建市场模型和行业理解
- **多方案生成**：基于市场模型创建多个候选投资策略
- **方案评估**：对比分析各方案优劣，考虑多维度因素
- **长期规划**：优化整体投资回报，而非单点决策
- **推理透明**：能够解释决策过程和选择理由

## 实现步骤

智能投研助手的实现遵循深思熟虑型智能体的核心流程，分为五个关键阶段：

### 1. 感知阶段

收集和整理市场数据与信息，包括：
- 市场概况和最新动态
- 关键经济和市场指标
- 近期重要新闻
- 行业趋势分析

### 2. 建模阶段

基于收集的数据构建内部世界模型，分析：
- 当前市场状态评估
- 经济周期判断
- 主要风险因素
- 潜在机会领域
- 市场情绪分析

### 3. 推理阶段

生成多个候选投资分析方案，每个方案包含：
- 投资假设
- 分析方法
- 预期结果
- 置信度
- 方案优缺点

### 4. 决策阶段

评估候选方案并选择最优投资观点，形成：
- 投资论点
- 支持证据
- 风险评估
- 投资建议
- 时间框架

### 5. 报告阶段

生成完整的投资研究报告，包含：
- 报告标题和摘要
- 市场和行业背景
- 核心投资观点
- 详细分析论证
- 风险因素
- 投资建议
- 时间框架和预期回报

## 代码逻辑

### 技术栈

- **LangGraph**：构建智能体工作流
- **LangChain**：实现LLM调用和提示工程
- **Tongyi (通义千问)**：底层大语言模型

### 状态定义

项目使用`ResearchAgentState`类型定义智能体状态，包含：

```python
class ResearchAgentState(TypedDict):
    # 输入
    research_topic: str  # 研究主题
    industry_focus: str  # 行业焦点
    time_horizon: str  # 时间范围(短期/中期/长期)
    
    # 处理状态
    perception_data: Optional[Dict[str, Any]]  # 感知阶段收集的数据
    world_model: Optional[Dict[str, Any]]  # 内部世界模型
    reasoning_plans: Optional[List[Dict[str, Any]]]  # 候选分析方案
    selected_plan: Optional[Dict[str, Any]]  # 选中的最优方案
    
    # 输出
    final_report: Optional[str]  # 最终研究报告
    
    # 控制流
    current_phase: Literal["perception", "modeling", "reasoning", "decision", "report"]
    error: Optional[str]  # 错误信息
```

### 输出模型

使用Pydantic模型严格定义各阶段输出，确保结构化数据：

```python
class PerceptionOutput(BaseModel):
    """感知阶段输出的市场数据和信息"""
    market_overview: str
    key_indicators: Dict[str, str]
    recent_news: List[str]
    industry_trends: Dict[str, str]

class ModelingOutput(BaseModel):
    """建模阶段输出的内部世界模型"""
    market_state: str
    economic_cycle: str
    risk_factors: List[str]
    opportunity_areas: List[str]
    market_sentiment: str

class ReasoningPlan(BaseModel):
    """推理阶段生成的候选分析方案"""
    plan_id: str
    hypothesis: str
    analysis_approach: str
    expected_outcome: str
    confidence_level: float
    pros: List[str]
    cons: List[str]

class DecisionOutput(BaseModel):
    """决策阶段选择的最优投资观点"""
    selected_plan_id: str
    investment_thesis: str
    supporting_evidence: List[str]
    risk_assessment: str
    recommendation: str
    timeframe: str
```

### 工作流实现

核心工作流通过LangGraph的`StateGraph`实现，将各阶段连接成一个有向图：

```python
def create_research_agent_workflow() -> StateGraph:
    # 创建状态图
    workflow = StateGraph(ResearchAgentState)
    
    # 添加节点
    workflow.add_node("perception", perception)
    workflow.add_node("modeling", modeling)
    workflow.add_node("reasoning", reasoning)
    workflow.add_node("decision", decision)
    workflow.add_node("report", report_generation)
    
    # 设置入口点
    workflow.set_entry_point("perception")
    
    # 设置边和转换条件
    workflow.add_edge("perception", "modeling")
    workflow.add_edge("modeling", "reasoning")
    workflow.add_edge("reasoning", "decision")
    workflow.add_edge("decision", "report")
    workflow.add_edge("report", END)
    
    # 编译工作流
    return workflow.compile()
```

### 各阶段处理逻辑

每个阶段都遵循类似的处理模式：
1. 检查前置条件（上一阶段数据）
2. 准备LLM提示
3. 调用LLM进行推理
4. 解析结果并更新状态
5. 错误处理与恢复机制

例如，推理阶段实现：

```python
def reasoning(state: ResearchAgentState) -> ResearchAgentState:
    try:
        # 检查前置条件
        if not state.get("world_model"):
            return {
                **state,
                "error": "推理阶段缺少世界模型",
                "current_phase": "modeling"  # 回到建模阶段
            }
        
        # 准备提示
        prompt = ChatPromptTemplate.from_template(REASONING_PROMPT)
        
        # 构建输入
        input_data = {
            "research_topic": state["research_topic"],
            "industry_focus": state["industry_focus"],
            "time_horizon": state["time_horizon"],
            "world_model": json.dumps(state["world_model"], ensure_ascii=False, indent=2)
        }
        
        # 调用LLM
        chain = prompt | llm | JsonOutputParser()
        result = chain.invoke(input_data)
        
        # 更新状态
        return {
            **state,
            "reasoning_plans": result,
            "current_phase": "decision"
        }
    except Exception as e:
        return {
            **state,
            "error": f"推理阶段出错: {str(e)}",
            "current_phase": "reasoning"  # 保持在当前阶段
        }
```

### 错误处理机制

每个阶段都实现了健壮的错误处理：
- 检查前置条件，确保阶段间数据依赖
- 捕获异常并记录错误信息
- 提供恢复机制，允许回到前一阶段或保持当前阶段

### 进阶特性

1. **提示工程**：每个阶段使用精心设计的提示模板，引导模型输出高质量结果
2. **结构化输出**：使用JSON解析器确保模型输出符合预定义结构
3. **状态持久化**：每个阶段结果都保存在状态中，支持后续分析和审计
4. **报告生成**：最终输出结构化研究报告，并保存到文件系统

## 使用方法

使用智能投研助手非常简单：

```python
if __name__ == "__main__":
    print("=== 深思熟虑智能体 - 智能投研助手 ===\n")
    
    # 用户输入
    topic = input("请输入研究主题 (例如: 新能源汽车行业投资机会): ")
    industry = input("请输入行业焦点 (例如: 电动汽车制造、电池技术): ")
    horizon = input("请输入时间范围 [短期/中期/长期]: ")
    
    print("\n智能投研助手开始工作...\n")
    
    # 运行智能体
    result = run_research_agent(topic, industry, horizon)
    
    # 处理结果
    if result.get("error"):
        print(f"\n发生错误: {result['error']}")
    else:
        print("\n=== 最终研究报告 ===\n")
        print(result.get("final_report", "未生成报告"))
```

## 架构优势

相比简单的工作流或反应式智能体，深思熟虑型智能体具有以下优势：

1. **多步骤决策**：将复杂问题分解为多个阶段，逐步推进
2. **前瞻性规划**：基于世界模型进行前瞻性分析，而非简单响应
3. **多方案对比**：生成并评估多个方案，而非单一答案
4. **内部模型构建**：维护对问题领域的深度理解
5. **透明决策过程**：每个阶段的推理和决策过程可追溯

## 潜在应用场景

深思熟虑型智能体架构不仅适用于投资研究，还可扩展到多种需要深度思考的场景：

1. 战略决策制定
2. 复杂项目规划
3. 医疗诊断与治疗方案
4. 风险评估与管理
5. 产品研发路线规划

## 总结

智能投研助手展示了深思熟虑型智能体在复杂决策场景中的强大能力。通过结构化的多阶段处理流程，该智能体能够像资深分析师一样思考，综合多维度信息形成投资观点，生成专业研究报告，为投资决策提供有价值的支持。 