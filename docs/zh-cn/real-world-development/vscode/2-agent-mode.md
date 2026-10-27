---
title: "练习 2 - 使用 Copilot Agent 模式添加功能"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

即使是最简单的应用更新，通常也需要修改多个文件并运行测试。开发者往往需要找到所有相关文件、进行修改、运行测试、调试、找出遗漏的文件，再继续修改。这个过程可能会反复进行。

Copilot Agent 模式可以帮助处理这些工作。

Copilot Agent 模式可在集成开发环境（IDE）中自主工作，类似开发者：先探索项目结构，再执行必要的更新、运行测试等任务，并自动修复发现的问题。现在了解如何使用 Agent 模式为网站添加功能。

> [!NOTE]
> Agent 模式和云智能体的名称相似，但适用于不同的工作方式。Agent 模式在 IDE 中执行任务，适合快速反馈和交互；云智能体则像团队成员一样异步工作，通过议题和拉取请求与你协作。

本练习将介绍如何：

- 使用 Copilot Agent 模式探索项目、识别相关文件并协调更改。
- 使用 GitHub Copilot Agent 模式跨用户界面（UI）和数据层实现新功能。
- 在将更改合并到代码库前，审查 Agent 模式生成的更改和测试。

## 场景

随着游戏列表不断增长，需要允许用户按类别和发行商筛选。上一练习已添加发行商辅助函数，现在将使用 Copilot Agent 模式完成剩余的数据层、UI 和测试工作。

## 运行 Tailspin Toys 网站

修改任何内容之前，先浏览 Tailspin Toys 网站，了解现有功能。

该网站是一个以开发者为主题的桌游众筹平台，可列出游戏并显示其详情。它是一个 Astro 应用，在构建时将页面渲染为静态 HTML。页面通过 Drizzle ORM 在 frontmatter 中直接查询本地 SQLite 数据库；没有独立后端应用程序编程接口（API）或客户端用户界面（UI）框架。可复用的数据访问辅助函数位于 `src/lib/`，交互功能通过使用标准文档对象模型（DOM）API 的小型作用域 Astro `<script>` 实现。

### 启动网站

项目提供了 `npm run dev` 任务，用于启动单个 Astro 开发服务器。通过以下步骤在 GitHub Codespace 中运行网站：

1. 返回 Codespace。上一练习结束后应已位于 `main`。
2. 按 <kbd>Control</kbd>+<kbd>\`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>\`</kbd>（Windows/Linux），在 Codespace 中打开新终端。
3. 更新 `main` 并创建筛选功能分支：

   ```bash
   git checkout main
   git pull
   git checkout -b filtering-vscode
   ```

4. 运行以下命令启动网站：

   ```bash
   npm run dev
   ```

   Astro 开发服务器就绪后，会显示类似以下的横幅：

   ```bash
   🚀 Tailspin Toys is ready!
      Astro server: http://localhost:4321
      Press Ctrl-C to stop.
   ```

> [!NOTE]
> 如果弹出对话框，询问是否打开 `http://localhost:4321` 的浏览器窗口，请选择 **x** 将其关闭。

5. 按住 <kbd>Command</kbd>（Mac）或 <kbd>Ctrl</kbd>（Windows/Linux），然后选择终端中的 Astro 服务器地址 `http://localhost:4321`，打开网站。

> [!NOTE]
> 在 Codespace 中选择终端里的 localhost 链接时，会自动重定向到 `https://<your-codespace-name>-4321.app.github.dev/`。这是指向当前运行 Web 服务器的 Codespace 的私有隧道。

### 浏览网站

网站启动后，可以浏览现有功能：

- **主页**：显示桌游列表、标题、图片和描述。
- **游戏详情页**：选择游戏后打开详情页，其中包含标题、描述、发行商和类别等信息。

## 使用 Copilot 查看待办事项

> [!TIP]
> **打开 Copilot Chat**
>
> 开始以下练习前，返回 Codespace，打开 Copilot Chat 面板并选择 **New Chat**，启动干净的对话。模式和模型选择因练习而异；相关步骤会在需要时说明。

网站的初始实现已经可用，但还可以添加新功能。先查看待办事项。根据模板设置项目时，系统已自动创建议题列表；让 GitHub Copilot 显示这些项目。

1. 在 Chat 视图的智能体下拉列表中选择 **Agent**。Agent 智能体可自主规划并跨文件实现更改、运行终端命令和调用工具。

   ![Chat 视图中的智能体选择器示意图。](../../../_images/shared-chat-mode-selector.png)

2. 从可用模型列表中选择 **Claude Sonnet 4.5**。

> [!CAUTION]
> 本工作坊作者并非推荐某个模型。编写本工作坊时使用了 Claude Sonnet 4.5，因此将它列入步骤中，以尽可能保持代码建议的一致性。不过，大语言模型（LLM）的输出具有概率性，收到的建议可能与示例不同，这是正常且预期的情况。

> [!NOTE]
> 由于 LLM 输出具有概率性，Copilot 可能会使用不同的 MCP 命令，但仍应能完成任务。

3. 发送以下提示，让 Copilot 显示议题待办事项：

   ```plaintext
   Please show me the backlog of items from my GitHub repository. Help me prioritize them based on those which will be most useful to the user.
   ```

4. 选择 **Continue** 以运行列出所有议题的命令。
5. 审查生成的议题列表。

Copilot 也会按它认为对用户最有用的程度排列事项优先级。

## 查看指令文件

让智能体生成代码前，先查看用于提供工作上下文的指令文件。项目的[用户界面（UI）指令][ui-instructions]说明了向网站添加功能时应遵循的方法。

1. 在 Codespace 中打开 `.github/instructions/ui.instructions.md`。
2. 查看整体指导，包括：
   - 架构概述。
   - 组件设计、可测试性和无障碍原则。
   - 指向各类文件专用指令的链接，包括 Astro、Tailwind CSS 和 Drizzle。

> [!TIP]
> 指令文件可以引用其他指令文件和项目文件。路径相对于指令文件所在位置。这样可以复用指导、拆分复杂指令，并提供示例和模板。

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md

## 实现筛选功能

完成筛选至少需要对应用进行三项更改：

- 在数据层（`src/lib/`）添加或完善剩余筛选逻辑。
- 添加或更新筛选行为测试。
- 更新游戏列表页并添加筛选 UI。

合并前还需要运行并通过测试。Copilot Agent 模式可以执行这些任务。

1. 可继续当前 Copilot 对话，也可以选择 **New Chat** 启动新对话。
2. 选择 **Add Context**、**Instructions** 和 **ui** 指令文件。

   ![选择 UI 指令文件的示意图](../../../_images/ex3-select-instructions-file.png)

3. 确认 Chat 视图的智能体下拉列表中仍选中 **Agent**。

   ![Chat 视图中的智能体选择器示意图。](../../../_images/shared-chat-mode-selector.png)

4. 确认仍选择 **Claude Sonnet 4.5** 模型。
5. 使用以下提示，让 Copilot 根据待办事项中的相关议题实现功能：

   ```plaintext
   Please update the site to include filtering by publisher and category based on the requirements from the related GitHub issue in the backlog. A publishers helper already exists from the previous exercise, so preserve and refine that work as needed while completing the remaining data-layer, UI, and tests. Ensure all tests are passing before completion. The server is already running, so you do not need to start it up.
   ```

6. 观察 Copilot 如何探索项目并找到相关文件，包括数据层辅助函数、UI 和测试。随后它会修改文件并运行测试。

   ![Copilot 正在探索项目文件的示意图](../../../_images/ex3-agent-mode-explores.png)

> [!NOTE]
> Copilot 会探索项目、修改文件并运行测试，可能需要几分钟，具体取决于任务复杂度和代码库大小。过程中，代码编辑器可能会出现 **Keep** 和 **Undo** 按钮。工作完成后，所有更改都会显示对应的 **Keep** 或 **Undo** 按钮，因此无需在生成过程中选择。

7. Copilot 提示运行测试时，选择 **Continue**。

   ![Copilot Chat 面板提示确认运行测试的对话框示意图](../../../_images/ex3-agent-mode-run-tests.png)

8. 过程中可能会暂停，甚至出现测试失败。这没有问题；Copilot 会在代码生成和测试之间反复迭代，直到任务完成且未发现错误。

   ![Copilot Agent 模式完整聊天会话的示意图](../../../_images/ex3-agent-mode-proposed-changes.png)

9. 检查生成的代码，查找潜在问题。

> [!CAUTION]
> 始终要审查 Copilot 或其他 AI 工具生成的代码。

10. 返回正在运行网站的浏览器，体验新功能。
11. 确认功能正常且已审查代码后，在 Copilot Chat 窗口中选择 **Keep**。

## 总结和后续步骤

本练习介绍了如何使用 GitHub Copilot Agent 模式为 Tailspin Toys 网站添加功能。- 让 Copilot Agent 模式探索项目、识别相关文件并协调更改。
- 使用 GitHub Copilot Agent 模式跨 UI 和数据层实现新功能。
- 在将更改合并到代码库前，审查 Agent 模式生成的更改和测试。

接下来，[使用 Playwright MCP 服务器测试功能][next-lesson]并为其创建拉取请求。

### 附加探索练习 - 实现分页

随着游戏列表增长，网站可能需要支持分页。运用本练习中学到的技能，提示 Copilot 为网站添加分页。编写提示时可考虑：

- 遵循现有最佳实践，包括使用已有指令文件。
- 确定分页方式，例如是否允许用户选择每页大小，或采用固定值。
- 为 Copilot 提供实现所需的指导，明确期望的行为。
- 可能需要通过额外提示和上下文与 GitHub Copilot 迭代。这是使用 Copilot 的正常流程。

## 资源

- [Copilot 的 ask、edit 和 agent 模式：功能及适用时机][choose-mode]
- [VS Code 中的 Agent 模式][vs-code-agent-mode]

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-mcp/
[choose-mode]: https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/
[vs-code-agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
