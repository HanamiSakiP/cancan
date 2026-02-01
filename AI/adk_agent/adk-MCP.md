## MCP_py readme
```py
# excel-mcp-server
# npm i @negokaz/excel-mcp-server
# go转py必学知识点
""" go-0. 导包
import os # 1. Python内置库
from dotenv import load_dotenv # 2. 第三方库
from mcp import types as mcp_types # 3. 别名导入
"""

"""go-1. 装饰器语法 AND 类实例化
    @符号是装饰器，用于修改函数行为
    类似Go的中间件，但语法更简洁
# 类实例化
Server("adk-tool-exposing-mcp-server")  # 创建实例，无需new关键字
LlmAgent(model='gemini-2.0-flash', ...) # 命名参数
"""

"""go-2. 函数定义 AND 列表和字典(map)
    func == def
    async def：定义异步函数（类似Go的goroutine）
    """文档字符串"""：函数文档，类似Go 注释 但更正式
name: str                    # 变量类型 提示
arguments: dict             # 字典类型
-> list[mcp_types.Content]  # 返回类型 Python可选，Go必须
# 列表和字典
return [mcp_tool_schema]  # 列表，Go用[]interface{}{...}
args=[PATH_TO...]        # 列表参数
{ "type": "text" }       # 字典字面量，Go用map[string]interface{}{}
"""

""" go-3. 异步编程
async def run_mcp_stdio_server():  # 定义异步函数
async with ... as ...:            # 异步上下文管理器
await app.run(...)                 # 等待异步操作
# 一般在main函数那
asyncio.run(...)                  # 运行异步主函数
"""

""" go-4. main
    Go的package main → Python的if __name__ == "__main__":作为入口
"""

""" go-5. 异常处理
    as e将异常赋值给变量
    finally同Go
try:
    # 可能出错的代码
except Exception as e:  # 捕获异常
    # 处理异常
finally:
    # 无论是否异常都执行
"""

""" go-0. iferr
var = "string"
    printf("err")
"""
```
> ## 结构
> ```bash
> a2a_basic/
> ├── adk_agent_samples/
> │   └── mcp_client_agent/
> │   │    ├── __init__.py
> │   │    └── agent.py
> │   └── .env
> └── my_adk_mcp_server.py # 本地mcp_server
> ```
## 0. package_py mcp_server
```py
""" go-0. 导包
import os # 1. Python内置库
from dotenv import load_dotenv # 2. 第三方库
from mcp import types as mcp_types # 3. 别名导入
"""
# my_adk_mcp_server.py
import asyncio
import json
import os
from dotenv import load_dotenv
# 1. MCP 服务器导入
from mcp import types as mcp_types # 使用别名以避免冲突
# mcp_server AND 服务器能力
from mcp.server.lowlevel import Server, NotificationOptions
from mcp.server.models import InitializationOptions
import mcp.server.stdio # 用于作为 stdio 服务器运行
# 2. ADK 工具导入
from google.adk.tools.function_tool import FunctionTool
from google.adk.tools.load_web_page import load_web_page # 示例 ADK 工具
# ADK <-> MCP 转换工具
from google.adk.tools.mcp_tool.conversion_utils import adk_to_mcp_tool_type
```

## home. func mcp_server
```py
# ./adk_agent_samples/my_adk_mcp_server.py
# --- 加载环境变量（如 ADK 工具需要，如 API key） ---
load_dotenv() # 如有需要，在同目录下创建 .env 文件
# 0. ADK_Tool
adk_tool_to_expose = FunctionTool(load_web_page)
# home. mcp_server
app = Server("adk-tool-exposing-mcp-server")
"""go-1. 装饰器语法 AND 类实例化
    @符号是装饰器，用于修改函数行为
    类似Go的中间件，但语法更简洁
# 类实例化
Server("adk-tool-exposing-mcp-server")  # 创建实例，无需new关键字
LlmAgent(model='gemini-2.0-flash', ...) # 命名参数
"""
@app.list_tools()
# 1. list_mcp_tools
async def list_mcp_tools() -> list[mcp_types.Tool]:
    """MCP handler to list tools this server exposes."""
    mcp_tool_schema = adk_to_mcp_tool_type(adk_tool_to_expose)
    return [mcp_tool_schema]
# 2. call_mcp_tool

"""go-2. 函数定义 AND 列表和字典(map)
    func == def
    async def：定义异步函数（类似Go的goroutine）
    """文档字符串"""：函数文档，类似Go 注释 但更正式
name: str                    # 变量类型 提示
arguments: dict             # 字典类型
-> list[mcp_types.Content]  # 返回类型 Python可选，Go必须
# 列表和字典
return [mcp_tool_schema]  # 列表，Go用[]interface{}{...}
args=[PATH_TO...]        # 列表参数
{ "type": "text" }       # 字典字面量，Go用map[string]interface{}{}
"""
@app.call_tool()
async def call_mcp_tool(
    name: str, arguments: dict
) -> list[mcp_types.Content]:
    """MCP handler to execute a tool call requested by an MCP client."""
    if name == adk_tool_to_expose.name:
        try:
            adk_tool_response = await adk_tool_to_expose.run_async(
                args=arguments,
                tool_context=None,
            )
            response_text = json.dumps(adk_tool_response, indent=2)
            return [mcp_types.TextContent(type="text", text=response_text)]
        except Exception as e:
            error_text = json.dumps({"error": f"执行工具 '{name}' 失败：{str(e)}"})
            return [mcp_types.TextContent(type="text", text=error_text)]
    else:
        error_text = json.dumps({"error": f"工具 '{name}' 未在此服务器中实现。"})
        return [mcp_types.TextContent(type="text", text=error_text)]
# 3. run_mcp_stdio_server

""" go-3. 异步编程
async def run_mcp_stdio_server():  # 定义异步函数
async with ... as ...:            # 异步上下文管理器
await app.run(...)                 # 等待异步操作
# 一般在main函数那
asyncio.run(...)                  # 运行异步主函数
"""
async def run_mcp_stdio_server():
    async with mcp.server.stdio.stdio_server() as (read_stream, write_stream):
        await app.run(
            read_stream,
            write_stream,
            InitializationOptions(
                server_name=app.name, # 使用上面定义的服务器名
                server_version="0.1.0",
                capabilities=app.get_capabilities(
                    # 定义服务器能力 - 参见 MCP 文档
                    notification_options=NotificationOptions(),
                    experimental_capabilities={},
                ),
            ),
        )
        print("MCP Stdio 服务器：运行循环完成或客户端断开连接。")
# end. START mcp_server

""" go-4. main
    Go的package main → Python的if __name__ == "__main__":作为入口
"""
if __name__ == "__main__":
    print("正在启动 MCP 服务器以通过 stdio 暴露 ADK 工具...")
""" go-5. 异常处理
    as e将异常赋值给变量
    finally同Go
try:
    # 可能出错的代码
except Exception as e:  # 捕获异常
    # 处理异常
finally:
    # 无论是否异常都执行
"""
    try:
        asyncio.run(run_mcp_stdio_server())
    except KeyboardInterrupt:
        print("\nMCP 服务器（stdio）被用户停止。")
    except Exception as e:
        print(f"MCP 服务器（stdio）遇到错误：{e}")
    finally:
        print("MCP 服务器（stdio）进程退出。")
```

## end. iferr McpToolset
```py
# 1. .env 文件放在 ./adk_agent_samples 目录的父目录中。
# 2. args 列表中的 "/path/to/your/folder" 替换为 MCP 服务器 实际文件夹的绝对路径。
# 3. McpToolset 直接在你的 LlmAgent 的 tools 列表中实例化。
# ./adk_agent_samples/mcp_client_agent/agent.py
import os # 用于路径操作
# llm
from google.adk.agents import Agent
# 关键：导入LiteLLM适配器
from google.adk.models.lite_llm import LiteLlm
# mcp
from google.adk.tools.mcp_tool import McpToolset
from google.adk.tools.mcp_tool.mcp_session_manager import StdioConnectionParams
from mcp import StdioServerParameters

""" go-0. iferr
var = "string"
    printf("err")
"""
PATH_TO_YOUR_MCP_SERVER_SCRIPT = "/path/to/your/my_adk_mcp_server.py" # <<< 替换
# D:/mygo/MCP/my_adk_mcp_server.py
if PATH_TO_YOUR_MCP_SERVER_SCRIPT == "/path/to/your/my_adk_mcp_server.py":
    print("警告：PATH_TO_YOUR_MCP_SERVER_SCRIPT 未设置。请在 agent.py 中更新它。")
    # 如路径必需可报错

root_agent = Agent(
    model=LiteLlm(model="deepseek/deepseek-chat"),
    name='web_reader_mcp_client_agent',
    instruction="使用 'load_web_page' 工具从用户提供的 URL 获取内容。",
    tools=[
        McpToolset(
            connection_params=StdioConnectionParams(
                server_params = StdioServerParameters(
                    command='python3', # 运行你的 MCP 服务器脚本的命令
                    args=[PATH_TO_YOUR_MCP_SERVER_SCRIPT], # 参数是脚本的路径
                )
            )
            # tool_filter=['load_web_page'] # 可选：仅加载特定工具
        )
    ],
)
# ./adk_agent_samples/mcp_agent/__init__.py
# from . import agent
# ./adk_agent_samples/.env
```

## mcp_server example
```py
# my_adk_mcp_server.py
import asyncio
import json
import os
from dotenv import load_dotenv
# 1. MCP 服务器导入
from mcp import types as mcp_types # 使用别名以避免冲突
# mcp_server AND 服务器能力
from mcp.server.lowlevel import Server, NotificationOptions
from mcp.server.models import InitializationOptions
import mcp.server.stdio # 用于作为 stdio 服务器运行

# 2. ADK 工具导入
from google.adk.tools.function_tool import FunctionTool
from google.adk.tools.load_web_page import load_web_page # 示例 ADK 工具
# ADK <-> MCP 转换工具
from google.adk.tools.mcp_tool.conversion_utils import adk_to_mcp_tool_type
# --- 加载环境变量（如 ADK 工具需要，如 API key） ---
load_dotenv() # 如有需要，在同目录下创建 .env 文件
# 0. ADK_Tool
adk_tool_to_expose = FunctionTool(load_web_page)
# home. mcp_server
app = Server("adk-tool-exposing-mcp-server")
@app.list_tools()
# 1. list_mcp_tools
async def list_mcp_tools() -> list[mcp_types.Tool]:
    """MCP handler to list tools this server exposes."""
    mcp_tool_schema = adk_to_mcp_tool_type(adk_tool_to_expose)
    return [mcp_tool_schema]
# 2. call_mcp_tool
@app.call_tool()
async def call_mcp_tool(
    name: str, arguments: dict
) -> list[mcp_types.Content]:
    """MCP handler to execute a tool call requested by an MCP client."""
    if name == adk_tool_to_expose.name:
        try:
            adk_tool_response = await adk_tool_to_expose.run_async(
                args=arguments,
                tool_context=None,
            )
            response_text = json.dumps(adk_tool_response, indent=2)
            return [mcp_types.TextContent(type="text", text=response_text)]
        except Exception as e:
            error_text = json.dumps({"error": f"执行工具 '{name}' 失败：{str(e)}"})
            return [mcp_types.TextContent(type="text", text=error_text)]
    else:
        error_text = json.dumps({"error": f"工具 '{name}' 未在此服务器中实现。"})
        return [mcp_types.TextContent(type="text", text=error_text)]
# 3. run_mcp_stdio_server
async def run_mcp_stdio_server():
    async with mcp.server.stdio.stdio_server() as (read_stream, write_stream):
        await app.run(
            read_stream,
            write_stream,
            InitializationOptions(
                server_name=app.name, # 使用上面定义的服务器名
                server_version="0.1.0",
                capabilities=app.get_capabilities(
                    # 定义服务器能力 - 参见 MCP 文档
                    notification_options=NotificationOptions(),
                    experimental_capabilities={},
                ),
            ),
        )
        print("MCP Stdio 服务器：运行循环完成或客户端断开连接。")
# end. START mcp_server
if __name__ == "__main__":
    print("正在启动 MCP 服务器以通过 stdio 暴露 ADK 工具...")
    try:
        asyncio.run(run_mcp_stdio_server())
    except KeyboardInterrupt:
        print("\nMCP 服务器（stdio）被用户停止。")
    except Exception as e:
        print(f"MCP 服务器（stdio）遇到错误：{e}")
    finally:
        print("MCP 服务器（stdio）进程退出。")
```
