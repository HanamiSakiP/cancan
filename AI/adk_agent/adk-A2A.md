## A2A 安装与配置环境
> ```bash
> pip install google-adk[a2a]
> ```
> 
> ## 0. A2A_create
> ```bash
> # 快速创建命令
> mkdir a2a_root
> cd a2a_root
> adk create remote_a2a
> 2
> cd remote_a2a
> mkdir hello_world
> move *.py hello_world
> # mv *.py hello_world
> ```

> ## 1. 远程A2A
> ### 结构
> ```bash
> a2a_root/
> ├── remote_a2a/
> │   └── hello_world/    
> │       ├── __init__.py
> │       └── agent.py    # 远程 Hello World 智能体
> ├── README.md
> └── agent.py            # 根智能体
> ```
> 
> ### 远程A2A实现
> ```py
> # a2a_root/remote_a2a/hello_world/agent.py
> # llm
> from google.adk.agents import Agent
> # 关键：导入LiteLLM适配器(gemini不用)
> from google.adk.models.lite_llm import LiteLlm
> # to_a2a
> from google.adk.a2a.utils.agent_to_a2a import to_a2a
> from a2a.types import AgentCard
> 
> root_agent = Agent(
>     model=LiteLlm(model="deepseek/deepseek-chat"),
>     name='hello_world_agent',
>     # model=LiteLlm(model="ollama/qwen3-vl:2b"),
>     description='A helpful assistant for user questions.',
>     instruction='Answer user questions to the best of your knowledge',
> )
> # Define A2A agent card
> my_agent_card = AgentCard(
>     name="file_agent",
>     url="http://example.com",
>     description="Test agent from file",
>     version="1.0.0",
>     capabilities={},
>     skills=[],
>     defaultInputModes=["text/plain"],
>     defaultOutputModes=["text/plain"],
>     supportsAuthenticatedExtendedCard=False,
> )
> # card/JSON
> a2a_app = to_a2a(root_agent, port=8001, agent_card=my_agent_card)
> # .env配置
> # 1. OLLAMA API配置
> # OLLAMA_API_BASE=http://localhost:11434
> # 2. Deepseek API配置
> # DEEPSEEK_API_KEY=sk-KEY
> # Google AI Studio
> # GOOGLE_GENAI_USE_VERTEXAI=FALSE
> # GOOGLE_API_KEY=PASTE_YOUR_ACTUAL_API_KEY_HERE
> ```
> 
> ### 远程A2A启动
> ```bash
> # 确保当前工作目录是 adk-A2A/
> # 使用 uvicorn 启动远程智能体
> uvicorn a2a_root.remote_a2a.hello_world.agent:a2a_app --host localhost --port 8001
> # 查看检查你的远程智能体是否正在运行
> curl  http://localhost:8001/.well-known/agent-card.json
> ```

> ## 2. 消费与远程
> ### 结构
> ```bash
> a2a_basic/
> ├── remote_a2a/
> │   └── .env
> │   └── check_prime_agent/
> │        ├── __init__.py
> │        ├── agent.json
> │        └── agent.py
> ├── README.md
> ├── root_agent_a2a/
> │   └── .env
> │   └── root_agent/
> │       ├── __init__.py
> └──     └── agent.py # 本地根智能体
> ```

> ### 远程
> ```py
> # a2a_root/remote_a2a/hello_world/agent.py
> # llm
> from google.adk.agents import Agent
> # 关键：导入LiteLLM适配器(gemini不用)
> from google.adk.models.lite_llm import LiteLlm
> # to_a2a
> from google.adk.a2a.utils.agent_to_a2a import to_a2a
> from a2a.types import AgentCard
> # tool
> from google.adk.tools.tool_context import ToolContext
> import random
> from google.genai import types
> 
> async def check_prime(nums: list[int]) -> str:
>   """Check if a given list of numbers are prime.
> 
>   Args:
>     nums: The list of numbers to check.
> 
>   Returns:
>     A str indicating which number is prime.
>   """
>   primes = set()
>   for number in nums:
>     number = int(number)
>     if number <= 1:
>       continue
>     is_prime = True
>     for i in range(2, int(number**0.5) + 1):
>       if number % i == 0:
>         is_prime = False
>         break
>     if is_prime:
>       primes.add(number)
>   return (
>       'No prime numbers found.'
>       if not primes
>       else f"{', '.join(str(num) for num in primes)} are prime numbers."
>   )
> 
> root_agent = Agent(
>     model=LiteLlm(model="deepseek/deepseek-chat"),
>     name='check_prime_agent',
>     description='check prime agent that can check whether numbers are prime.',
>     instruction="""
>       You check whether numbers are prime.
>       When checking prime numbers, call the check_prime tool with a list of integers. Be sure to pass in a list of integers. You > > > should never pass in a string.
>       You should not rely on the previous history on prime results.
>     """,
>     tools=[
>         check_prime,
>     ],
>     # planner=BuiltInPlanner(
>     #     thinking_config=types.ThinkingConfig(
>     #         include_thoughts=True,
>     #     ),
>     # ),
>     generate_content_config=types.GenerateContentConfig(
>         safety_settings=[
>             types.SafetySetting(  # avoid false alarm about rolling dice.
>                 category=types.HarmCategory.HARM_CATEGORY_DANGEROUS_CONTENT,
>                 threshold=types.HarmBlockThreshold.OFF,
>             ),
>         ]
>     ),
> )
> # card/JSON
> a2a_app = to_a2a(root_agent, port=8001, agent_card="D:/mygo/MCP/A2A/a2a_basic/remote_a2a/check_prime_agent/agent.json")
> # .env配置
> # 1. OLLAMA API配置
> # OLLAMA_API_BASE=http://localhost:11434
> # 2. Deepseek API配置
> # DEEPSEEK_API_KEY=sk-KEY
> # Google AI Studio
> # GOOGLE_GENAI_USE_VERTEXAI=FALSE
> # GOOGLE_API_KEY=PASTE_YOUR_ACTUAL_API_KEY_HERE
> ```
> * 启动远程
> ```bash
> adk api_server --a2a --port 8001  a2a_basic/remote_a2a 
> # 查看检查你的远程智能体是否正在运行
> curl http://localhost:8001/a2a/check_prime_agent/.well-known/agent-card.json
> ```

> ### 消费
> ```py
> # a2a_root/agent.py
> # 随机整数
> import random
> from google.genai import types
> # llm
> from google.adk.agents import Agent
> # 关键：导入LiteLLM适配器(gemini不用)
> from google.adk.models.lite_llm import LiteLlm
> # root_tool
> from google.adk.tools.example_tool import ExampleTool
> # 1. root_RemoteA2aAgent
> from google.adk.agents.remote_a2a_agent import AGENT_CARD_WELL_KNOWN_PATH
> from google.adk.agents.remote_a2a_agent import RemoteA2aAgent
> 
> # --- Roll Die Sub-Agent ---
> def roll_die(sides: int) -> int:
>   """Roll a die and return the rolled result."""
>   return random.randint(1, sides)
> roll_agent = Agent(
>     # model=LiteLlm(model="deepseek/deepseek-chat"),
>     name="roll_agent",
>     description="Handles rolling dice of different sizes.",
>     instruction="""
>       You are responsible for rolling dice based on the user's request.
>       When asked to roll a die, you must call the roll_die tool with the number of sides as an integer.
>     """,
>     tools=[roll_die],
>     generate_content_config=types.GenerateContentConfig(
>         safety_settings=[
>             types.SafetySetting(  # avoid false alarm about rolling dice.
>                 category=types.HarmCategory.HARM_CATEGORY_DANGEROUS_CONTENT,
>                 threshold=types.HarmBlockThreshold.OFF,
>             ),
>         ]
>     ),
> )
> 
> # root_tool
> example_tool = ExampleTool([
>     {
>         "input": {
>             "role": "user",
>             "parts": [{"text": "Roll a 6-sided die."}],
>         },
>         "output": [
>             {"role": "model", "parts": [{"text": "I rolled a 4 for you."}]}
>         ],
>     },
>     {
>         "input": {
>             "role": "user",
>             "parts": [{"text": "Is 7 a prime number?"}],
>         },
>         "output": [{
>             "role": "model",
>             "parts": [{"text": "Yes, 7 is a prime number."}],
>         }],
>     },
>     {
>         "input": {
>             "role": "user",
>             "parts": [{"text": "Roll a 10-sided die and check if it's prime."}],
>         },
>         "output": [
>             {
>                 "role": "model",
>                 "parts": [{"text": "I rolled an 8 for you."}],
>             },
>             {
>                 "role": "model",
>                 "parts": [{"text": "8 is not a prime number."}],
>             },
>         ],
>     },
> ])
> # 1. 连接远程agent
> prime_agent = RemoteA2aAgent(
>     name="prime_agent",
>     description="Agent that handles checking if numbers are prime.",
>     agent_card=(
>         f"http://localhost:8001/a2a/check_prime_agent{AGENT_CARD_WELL_KNOWN_PATH}"
>     ),
> )
> # root_agent
> root_agent = Agent(
>     model=LiteLlm(model="deepseek/deepseek-chat"),
>     name="root_agent",
>     instruction="""
>       You are a helpful assistant that can roll dice and check if numbers are prime.
>       You delegate rolling dice tasks to the roll_agent and prime checking tasks to the prime_agent.
>       Follow these steps:
>       1. If the user asks to roll a die, delegate to the roll_agent.
>       2. If the user asks to check primes, delegate to the prime_agent.
>       3. If the user asks to roll a die and then check if the result is prime, call roll_agent first, then pass the result to > prime_agent.
>       Always clarify the results before proceeding.
>     """,
>     global_instruction=(
>         "You are DicePrimeBot, ready to roll dice and check prime numbers."
>     ),
>     sub_agents=[roll_agent,prime_agent],
>     tools=[example_tool],
>     generate_content_config=types.GenerateContentConfig(
>         safety_settings=[
>             types.SafetySetting(  # avoid false alarm about rolling dice.
>                 category=types.HarmCategory.HARM_CATEGORY_DANGEROUS_CONTENT,
>                 threshold=types.HarmBlockThreshold.OFF,
>             ),
>         ]
>     ),
> )
> ```
> * 消费启动
> ```bash
> adk web ./a2a_basic/root_agent_a2a
> ```
