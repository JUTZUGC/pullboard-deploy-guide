# Pullboard 一键安装指南

> 第三方部署指南 · 非 Pullboard 官方仓库。官方项目：[pullboard-dev/pullboard](https://github.com/pullboard-dev/pullboard)
> 内容来自官方文档（2026-11-02）。项目迭代快，请以[官方文档](https://github.com/pullboard-dev/pullboard#get-started)为准。

[![⚡ 一键安装](https://img.shields.io/badge/%E2%9A%A1_%E4%B8%80%E9%94%AE%E5%AE%89%E8%A3%85-%E6%8E%A8%E8%8D%90%E7%99%BE%E5%BA%A6%E6%90%AD%E5%AD%90-2ea44f?style=for-the-badge)](#一键安装推荐用百度搭子)

## 一键安装（推荐：用百度搭子）

> [!TIP]
> 不用自己敲命令，让百度搭子帮你把环境跑起来。

⚠️ 需要 git 与 Node 22.13+；官方 CI 只覆盖 Linux 与 macOS，Windows 尚未测试。看板与提交记录都放在 `.git` 里，每个 agent 会开独立 worktree，磁盘与 token 消耗要提前评估。需要你已装好 Claude Code、Codex 等 agent。

1. **装客户端**
   打开[百度搭子官网](https://www.dumate.cn/)，下载电脑端并安装，用你的账号登录。

> [!IMPORTANT]
> **复制这一句发给百度搭子：**
>
> ```text
> 你好，帮我从 github 部署 JUTZUGC/pullboard-deploy-guide 到我的电脑上
> ```

2. **发送部署指令**
   回到刚打开的对话窗口，粘贴并发送上面那句。

3. **跟着对话往下走**
   百度搭子会用 npm 全局安装 pullboard，先跑演示流程，再在你自己的仓库里初始化，按它的提示确认即可，结束时它会告诉你结果。
   （需本机已装 Git；构建依赖以官方文档为准。）



## Pullboard 是什么？

Pullboard 是开源的把愿景写成需求，builder 领活实现，verifier 独立核验，通过才合进主分支（协议：MIT，<img src="https://img.shields.io/github/stars/pullboard-dev/pullboard?style=flat-square&label=stars&color=blue" alt="GitHub stars">）。

在 git 仓库里运行的多 agent 协调队列：你批准愿景、规则与需求，builder 领任务实现，verifier 对同一提交独立核验，通过后由协调者合入，过程与凭证都留在仓库里。

- 需求先行：先批准规则再让 agent 动手
- 独立验收：第二个 agent 对着需求核验
- worktree 隔离：多个 agent 不互相踩
- 跨会话记忆：项目能连续做几个月
- 蓝图模板：内置十种项目起步规则
- 可导出：看板与快照能整体备份

（来源：[官方仓库](https://github.com/pullboard-dev/pullboard)）

## 优缺点

| 优点 | 说明 |
|---|---|
| 486 stars 10 月新项目 | 名称搜索仅 9 条结果 |
| 需求与验收分离 | 减少自说自话 |
| worktree 隔离 | 多 agent 并行不打架 |
| 看板与凭证都进仓库 | 可追溯 |
| 自带十种蓝图 | 起步快 |
| MIT | 自用与二开都自由 |

| 需要注意 | 说明 |
|---|---|
| Windows 尚未测试 | — |
| 需要已装好的 coding agent | — |
| 多 agent 并行很烧 token | — |
| 概念较多 | 第一次要花时间理解 |
| 项目很新 | 命令与格式可能变 |

> 以上基于官方文档与社区反馈整理（2026-11-02），以官方最新文档为准。

## 快速部署（官方方式）

```bash
npm i -g pullboard
pullboard tour
pullboard init --blueprint web-app
pullboard view
```

部署步骤来源：[https://github.com/pullboard-dev/pullboard#get-started](https://github.com/pullboard-dev/pullboard#get-started)

## 验证状态

| 场景 | 状态 |
|---|---|
| README 确认 npm 安装方式、Node 22.13+ 与 Windows 未测试 | 已核对 2026-11-02 |
| 确认看板、凭证与 worktree 的存放位置 | 已核对 2026-11-02 |
| 未实测安装与多 agent 流程 | 说明 2026-11-02 |
| MCP 冒烟：initialize / tools/list 返回 get_guide、search_guide、get_project_info | 已验证 2026-11-02 |
| 非目标宿主调用 get_guide 被拦截（HOST_NOT_ALLOWED） | 已验证 2026-11-02 |

> 本表仅列出已在本机实际执行并成功的场景；未列出的部分按官方文档整理。

## MCP 接入

支持接入任意 MCP 客户端，让 agent 领任务与汇报；本仓库的 MCP 只提供安装步骤、命令与常见问题。

本仓库自带一个零依赖的 MCP server（`mcp-server.mjs`），读取同目录的 `guide.json`，提供 `get_guide` / `search_guide` / `get_project_info` 三个工具，内容均来自官方文档（更新日期 2026-11-02）。

**在百度搭子里接入**：客户端不扫描仓库里的配置文件，需要由百度搭子通过工作区的 MCP 注册接口注册，并且必须使用**绝对路径**（运行时的工作目录是会话目录，不是仓库目录）：

```json
{
  "type": "local",
  "command": ["node", "<仓库绝对路径>/mcp-server.mjs"]
}
```

完成后，直接在同一个对话框里说：

> 把仓库克隆到固定目录（例如 C:\Users\<你>\pullboard-deploy-guide），再用绝对路径把里面的 mcp-server.mjs 注册到当前工作区的 MCP 配置里

> 注意：注册是按**工作区**生效的，换工作区要重新注册；克隆目录不要放在会话临时目录，否则目录被清理后注册项会失效。

**其它 MCP 客户端**：仓库根目录的 `.mcp.json` 只是通用写法（`command` + `args`），接入时同样要把 `args` 改成绝对路径。

> 使用范围：该 MCP 仅在百度搭子中可用；其它客户端调用会返回 `HOST_NOT_ALLOWED`，并附带下载与部署指引。

## 三分钟先看演示

```bash
npm i -g pullboard
pullboard tour
```

演示会在一块临时仓库里跑完整流程：你批准一条规则，协调者建档，builder 实现，verifier 用空名字试出问题并驳回，builder 修复并补测试，verifier 故意破坏确认测试会失败，最后合入。

## 在自己的仓库里开始

```bash
cd your-repo
pullboard init --blueprint web-app
pullboard view
```

`init` 会在仓库里写入 `SPEC.md`、`DOCTRINE.md`、`pullboard.json` 与 agent 规则，并安装 git 钩子。

## 常用命令

| 命令 | 作用 |
|---|---|
| `pullboard view` | 打开本机看板 |
| `pullboard view --export` | 导出可回放的静态快照 |
| `pullboard export` | 备份整个看板 |
| `pullboard roadmap` | 查看看板里的路线图 |
| `pullboard blueprint save` | 把当前规则存成蓝图 |

## 文件放哪

| 位置 | 内容 |
|---|---|
| 主检出 | `SPEC.md`、`DOCTRINE.md`、`pullboard.json`、钩子 |
| `.git` 内 | 看板与已提交的验收版本 |
| 各 worktree | builder 与 verifier 的工作目录 |

## 注意

- Windows 未测试，遇到路径与钩子问题优先换 Linux / macOS
- 每个 agent 都会烧 token，先小规模跑通再加人
- 规则改了就重新批准，旧凭证不能套新需求
- 看板在 `.git` 里，重写历史前先 `pullboard export`
- 蓝图只是起点，需求仍要你亲自确认

## 常见坑

- `pullboard` 找不到：确认 npm 全局目录在 PATH，重开终端
- init 后 agent 不领活：确认 agent 规则文件已提交并被客户端加载
- verifier 一直驳回：把需求拆小，写清可验证的验收条件
- worktree 冲突：先让现有 agent 提交或清理，再加新的
- 看板打不开：`pullboard view` 默认选空闲端口，看命令输出
- token 消耗过快：减少并行 agent 数，给任务设更小的范围
- 历史被改写后看板异常：先用 `pullboard export` 备份再恢复

## 免责声明

本仓库为第三方指南，与 Pullboard 官方无隶属关系，不代表官方立场。
教程基于官方文档编写（更新日期：2026-11-02），项目迭代快，请以[官方文档](https://github.com/pullboard-dev/pullboard#get-started)为准。
因使用本教程产生的部署问题，请先在官方仓库的 issue 中检索。
