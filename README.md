# Pydantic-from-Scratch
## 介绍 PydanticAI
PydanticAI 是一个基于 Python 的框架，旨在简化使用生成式人工智能开发生产级应用的过程。它利用 Pydantic（一个数据验证库）来简化人工智能代理（尤其是基于大语言模型的代理）的创建和管理。PydanticAI 的核心优势在于确保数据的一致性和验证，从而帮助开发者构建可靠且易于维护的 AI 应用。
## 入门课程
### [B站视频：PydanticAI初体验 - 类型安全的Agent构建框架](https://www.bilibili.com/video/BV1kmBgYNEbt/?spm_id_from=333.337.search-card.all.click&vd_source=1277714f59054c70b72a05d48634f339)
- **视频简介**：本期视频介绍了 PydanticAI，一个用于构建类型安全的 Agent 的框架，旨在简化生成式AI应用的开发过程。
- **相关资源**：
  - [视频代码示例](https://github.com/sugarforever/pydantic-tutorials/blob/main/pydantic_get_started.ipynb)：由于gpt4o国内使用有限制，本仓库将视频范例修改为gemini-1.5-flash模型（国内可直接免费使用）。
  - [PydanticAI官方文档](https://ai.pydantic.dev/)


## PydanticAI 特性详解
**动态设置系统提示词**
通过装饰器设置动态系统提示词，RunContext 是 PydanticAI 所规定的数据类，其中 deps 表示上下文表示的依赖项。这个机制被称为“依赖注入”。
```python
@dynamic_prompt_agent.system_prompt
def set_agent_name(ctx: RunContext[str]) -> str:
    return f"Your name is {ctx.deps}."

response = dynamic_prompt_agent.run_sync("Hey, dude! Who are you?", deps="Jarvis")
print(response.data)
```
**双依赖注入**
需要引入 Python 中的 dataclass 模块，此处案例定义的两个依赖分别为球员的姓名和进球数，最后输出为一个 bool 变量。
```python
from dataclasses import dataclass
@dataclass
class Player:
    name: str
    goals: int

dual_deps_agent = Agent(
    model,
    deps_type=Player,
    result_type=bool,
)
    
```
**函数工具@agent.tool**
函数工具在 PydanticAI 中主要充当RAG检索增强生成中的R功能，主要检索额外信息以帮助 Agent 生成响应。通过 `@agent.tool` 装饰器 — 用于需要访问代理上下文的工具。通过 `@agent.tool_plain` 装饰器 — 用于不需要访问代理上下文的工具
```python
@agent.tool
def get_player_goals(ctx: RunContext[str], player_name: str) -> str:
    print(f"Getting the goals of player {player_name} so far")
    if player_name == 'Messi':
        return '2'
    elif player_name == 'Ronaldo':
        return '100'
    else:
        return '0'
```
## Typo
`[待解决]`代码最后的agent.tool模块似乎并没有接受额外的检索信息输入，但代码与范例代码一致存在一定概率是gemini-1.5模型的问题。
