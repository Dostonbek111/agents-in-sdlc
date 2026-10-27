---
title: "练习 5：迭代完善 GitHub Copilot 的工作"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 上一课：管理智能体][previous-lesson] |
|:--|

## 审查工作成果

在本实验中，你与 GitHub Copilot 协作完成了多项任务，以改进用户体验并添加功能：为代码添加文档、实现供设计团队迭代的相关游戏功能，以及实现包含高对比度和浅色模式开关的无障碍功能。现在检查代码更改；必要时向 Copilot 提供反馈，进一步改进结果。

### 场景

如前所述，加入生成式 AI 并不会改变软件设计和开发与运维（DevOps）的基本原则。始终需要审查生成的代码，并遵循常规 DevOps 流程。现在先审查 GitHub Copilot 为文档、相关游戏功能和无障碍功能提出的更改，再向团队开放代码审查。

## 安全与 GitHub Copilot 云智能体

Copilot 云智能体会在无人监督的情况下异步执行任务，因此设置了一些安全限制：

- Copilot 对存储库只有读取权限，对代码分支只有写入权限。
- 云智能体在 GitHub Actions 中运行，并在独立的临时环境中工作。
- GitHub Actions 工作流必须经过人工批准后才能运行。
- 默认情况下，[对外部资源的访问受到限制](https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall)，模型上下文协议（MCP）服务器也包括在内。

## 审查生成的文档

先查看 GitHub Copilot 云智能体生成的第一个拉取请求（PR），其中包含为代码添加文档的更改。使用 GitHub.com 中的标准 PR 界面完成审查。

> [!NOTE]
> 查看 PR 时，可能会看到 GitHub Copilot 被防火墙阻止的警告。这是**预期行为**，因为 Copilot 默认只能有限访问外部资源，包括外部 MCP 服务器。你可以选择[自定义或禁用 Copilot 云智能体的防火墙][agent-firewall]。

1. 返回 github.com 上的存储库。
2. 选择 **Pull Requests**，打开拉取请求列表。
3. 打开标题类似 **Add missing documentation** 的拉取请求。

> [!NOTE]
> 如果 Copilot 仍在处理任务，PR 会包含 **[WIP]** 标记。此时请等待 Copilot 完成工作，这可能需要几分钟。等待期间可以休息一下，或回顾已学内容。

4. PR 准备就绪后，选择 **Files changed** 选项卡并审查更改。

   ![Files changed 选项卡](../../../_images/shared-pr-files-changed.png)

5. 查看更新后的代码，其中包含新生成的 TSDoc 文档注释和其他文档。具体更改可能因结果而异。

   查看更改时，留意 TSDoc 文档注释、TypeScript 规范和文件注释头。这些来自练习 2 开始时查看的自定义指令文件。

6. 审查更新并确认符合要求后，返回 **Conversation** 选项卡并向下滚动。
7. 查看是否有工作流等待批准的指示器。
8. 如果有工作流等待批准，选择 **Approve and run workflows**。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

9. 工作流应会进入 PR 检查队列。如果一切正常，单个 Astro 应用的项目检查将通过。此过程可能需要几分钟。

## 请求 GitHub Copilot 修改

与 Copilot 协作并非单向过程。你可以像在团队 PR 中一样，在 PR 评论或代码内联评论中提及 Copilot。Copilot 会读取评论并启动新会话处理反馈。由于结果具有非确定性，这里无法提供固定提示文本。可以要求 Copilot：

- 在每个代码文件顶部添加简要说明文件用途的注释头。
- 为 TypeScript 和 Astro 文件添加 TSDoc 文档注释。
- 创建 README，介绍 Astro 应用结构。

1. 添加评论并提及 **@copilot**，请求它修改生成的文档。可使用上述任一建议，也可以提出其他文档改进要求。
2. 选择 **View Session**，观察 Copilot 如何开始新会话并执行更新。
3. 选择 **Back to pull request** 返回 PR。

   ![返回拉取请求](../../../_images/ex7-back-to-pr.png)

4. Copilot 完成更改后，PR 中应会出现新的提交。
5. 选择 **Files changed** 选项卡审查更改。

可以继续迭代，直到满意为止。之后可将 PR 从草稿转换为准备审查状态，并合并到 main 分支。

![将 PR 转为准备审查状态](../../../_images/ex7-ready-for-review.png)

## 审查相关游戏功能

接下来查看 Copilot 为解决游戏详情页显示相关游戏议题而创建的 PR。

1. 返回 GitHub.com 上的存储库。
2. 选择 **Pull Requests** 选项卡。
3. 选择标题类似 **Show related games on the game details page** 的 PR。
4. 选择 **Files changed** 选项卡，审查生成的代码。
5. 审查更新并确认符合要求后，返回 **Conversation** 选项卡并向下滚动。
6. 查看是否有工作流等待批准的指示器。
7. 如果有工作流等待批准，选择 **Approve and run workflows**。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

8. 工作流应会进入 PR 检查队列。如果一切正常，单个 Astro 应用的项目检查将通过。此过程可能需要几分钟。
9. **可选：** 可以在 Codespace 中切换到此分支，手动测试相关游戏功能。打开 Codespace 终端并运行以下命令（将 `<branch-name>` 替换为 Copilot 创建的分支名称，例如 **copilot/fix-8**）：

   ```bash
   git fetch origin
   git checkout <branch-name>
   ```

Copilot 已实现相关游戏功能。与之前一样，可以继续迭代并请求更新，例如调整显示的相关游戏数量，或添加文件注释头和 TSDoc 文档注释（请记住，分配此议题时尚未更新自定义指令）。在 **Conversation** 选项卡中发表评论即可提出请求，Copilot 会读取评论并启动新会话。

## 审查无障碍功能

最后，审查通过无障碍自定义智能体实现的功能。此 PR 应同时包含练习 3 中分配的高对比度模式，以及练习 4 在 Mission control 中请求的浅色模式。

1. 返回 GitHub.com 上的存储库。
2. 选择 **Pull Requests** 选项卡。
3. 选择标题类似 **Add high contrast mode to website** 的 PR。

> [!NOTE]
> 如果 Copilot 仍在处理任务，PR 会包含 **[WIP]** 标记。请等待 Copilot 完成，这可能需要几分钟。

4. 选择 **Files changed** 选项卡，审查生成的代码。
5. 查看实现，重点检查：
   - 用于切换模式的用户界面（UI）组件。
   - 用于保存用户偏好的本地存储。
   - 高对比度和浅色模式的 CSS 或样式更改。
   - 无障碍富互联网应用程序（ARIA）属性、键盘导航等无障碍功能。
   - 管理模式切换的 JavaScript/TypeScript 代码。
6. 审查更新并确认符合要求后，返回 **Conversation** 选项卡并向下滚动。
7. 查看是否有工作流等待批准的指示器。
8. 如果有工作流等待批准，选择 **Approve and run workflows**。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

9. 工作流应会进入 PR 检查队列。如果一切正常，单个 Astro 应用的项目检查将通过。此过程可能需要几分钟。
10. **可选：** 可以在 Codespace 中切换到此分支，手动测试无障碍功能。打开终端并运行以下命令（将 `<branch-name>` 替换为 Copilot 创建的分支名称）：

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

    然后启动应用，在浏览器中测试高对比度和浅色模式切换开关，并确认页面重新加载后设置仍然有效。

留意无障碍自定义智能体如何引导 Copilot 按照无障碍最佳实践实现功能。如果发现无障碍问题或改进点，可像审查其他 PR 一样在评论中提及 **@copilot**，请求更新。

## 可选练习 - 继续委派工作

云智能体适合处理真实待办事项，让你有时间专注其他工作。为培养这种工作方式，可在存储库中创建更多议题并分配给 Copilot。例如：

- 在游戏详情页创建支持者意向表单。
- 为游戏列表页实现分页。
- 为数据访问辅助函数添加输入验证和错误处理。

## 总结

你已完成云智能体工作环境的练习。在这些课程中，你：

- **检查了存储库自带的自定义指令文件**，以便之后观察它们对云智能体输出的影响。
- **将议题分配给 Copilot 云智能体**，观察它设置环境、制定计划并异步执行任务。
- **创建并使用自定义智能体**处理无障碍工作，添加高对比度和浅色模式切换开关。
- **使用 Copilot 智能体页面作为 Mission control**，监控并引导进行中的无障碍会话。
- **审查并迭代云智能体创建的拉取请求**，通过提及 `@copilot` 请求更改并批准工作流。

## 回顾和后续步骤

云智能体工作环境练习已完成。若要继续探索，其他工作环境可以补充本次实践：

- 🖥️ **[VS Code 工作环境](../../vscode/)**：直接在集成开发环境（IDE）中探索 Copilot Chat 智能体模式和 MCP 集成。
- 💻 **[CLI 工作环境](../../cli/)**：在终端中交付两项经过审查的更改，体验 Plan 和 Autopilot 模式、智能体技能、自定义质量保证（QA）智能体、Playwright 模型上下文协议（MCP）、Agent Merge，以及可选的 `/delegate` 云智能体桥接。

也可以在自己的存储库中尝试以下后续任务：

- 将重构或测试覆盖率议题分配给云智能体。
- 为其他领域创建自定义智能体。
- 为其他技术栈设置 `copilot-setup-steps.yml`。

## 资源

- [GitHub Copilot][github-copilot]
- [关于 Copilot 智能体][copilot-agents]
- [将 GitHub 议题分配给 Copilot][assign-issue]
- [Copilot 云智能体设置工作流最佳实践][cloud-agent-best-practices]
- [配置 Copilot 云智能体防火墙][agent-firewall]

---

| [← 上一课：管理智能体][previous-lesson] |
|:--|

[github-copilot]: https://github.com/features/copilot
[cloud-agent-overview]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[agent-firewall]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall
[previous-lesson]: ../4-managing-agents/
