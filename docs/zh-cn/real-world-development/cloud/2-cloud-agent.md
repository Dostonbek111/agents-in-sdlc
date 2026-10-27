---
title: "练习 2 - GitHub Copilot 云智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 上一课：自定义指令][previous-lesson] |
|:--|

几乎所有组织都会面对技术债务，例如尚未解决的安全问题、需要更新的旧代码，或因缺少时间而长期搁置的功能请求。GitHub Copilot 云智能体可以自主执行更新代码和添加功能等任务。完成后，它会创建草稿拉取请求（PR），供开发者审查。这样可以将繁琐任务交给智能体，加快开发流程，让开发者专注于更重要的工作。

本练习将介绍如何使用 Copilot 云智能体：

- 自定义代码生成环境。
- 确保安全地执行操作。
- 编写范围明确的议题。
- 将议题分配给 Copilot。

## 场景

Tailspin Toys 有一些需要解决的技术债务。最初受雇开发网站的承包商没有妥善编写文档，事实上，代码完全缺少文档。团队希望首先为应用程序中所有导出的函数添加 TSDoc 文档注释。

此外，设计团队希望改进游戏发现体验。他们希望在每款游戏的详情页显示相关游戏，即同一类别中的其他游戏，方便用户继续浏览。当前不需要完善的实现，只需要能用于用户体验验收测试的功能。虽然这项工作目前受阻，但还有优先级更高的议题。

这类任务很容易被搁置，适合交给 Copilot 云智能体异步处理。开发者可以先处理其他工作，之后再回来审查 Copilot 的成果。

## 了解 GitHub Copilot 云智能体

[GitHub Copilot 云智能体](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent)可以像开发者一样在后台执行任务。可以通过多种方式向它分配工作，例如[将 GitHub 议题分配给 Copilot](https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions)。分配任务后，Copilot 会创建草稿拉取请求以跟踪进度、设置工作环境并开始处理任务。你可以在会话进行中或完成后查看 Copilot 会话。解决方案准备好供审查时，它会在 PR 中提及你。

## 编写范围明确的指令

GitHub Copilot 并非魔法；你仍需思考任务本身。即使看似简单的操作，也可能包含复杂细节，因此要谨慎确定交给 Copilot 云智能体的任务范围，并遵循[云智能体最佳实践][cloud-agent-best-practices]。将它视为 AI 结对程序员：分阶段工作、学习、试验并逐步调整。生成式 AI 不会改变软件开发的基本原则。

## 本存储库中的自定义指令

前面的练习介绍了自定义指令及其如何引导 Copilot。如果已完成自定义指令练习，就已经看到 `.github/copilot-instructions.md` 的作用；否则，现在可以快速查看。分配任务前，以只读方式检查存储库中的指令文件，稍后便能观察它们的影响。

在 GitHub 网页界面或已启动的 Codespace 中打开以下文件：

- `.github/copilot-instructions.md`：查看 **Code standards** 部分，尤其是 TypeScript 规范和 TSDoc 文档注释要求。
- `.github/instructions/unit-tests.instructions.md`：留意 `applyTo` frontmatter，它将指令限定到 `**/*.test.ts` 文件。

下一部分将 `Code lacks documentation` 议题分配给云智能体时，留意 PR 中的 TSDoc 文档注释、TypeScript 规范和文件注释头。这些要求来自指令文件；后续练习会再次讨论。

## 为 Copilot 云智能体设置开发环境

无论由谁编写代码，通常都需要特定环境并运行设置脚本，确保环境就绪。将任务分配给 Copilot 时也是如此，因为它会像软件工程师一样工作。

云智能体使用 [GitHub Actions][github-actions] 执行任务。可以创建[专用设置工作流][setup-workflow]，配置在 `.github/workflows/copilot-setup-steps.yml` 中，并在智能体开始工作前运行，以安装所需开发工具和依赖项。实验已预先配置此工作流，以确保流程顺利并提供学习机会。它会为单个 Astro 应用安装 Node.js、项目依赖项、端到端测试所需的浏览器二进制文件，以及已迁移和填充数据的 SQLite 数据库：

```yaml
name: "Copilot Setup Steps"

# Allows you to test the setup steps from your repository's "Actions" tab
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # Set the permissions to the lowest permissions possible needed for *your steps*. Copilot will be given its own token for its operations.
    permissions:
      # If you want to clone the repository as part of your setup steps, for example to install dependencies, you'll need the `contents: read` permission. If you don't clone the repository in your setup steps, Copilot will do this for you automatically after the steps complete.
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # Frontend / app setup - Node.js (the whole app is now Astro + Drizzle/libSQL)
      - name: Set up Node.js
        uses: actions/setup-node@v6
        with:
          node-version: "lts/*"
          cache: "npm"
          cache-dependency-path: "./package-lock.json"

      - name: Install JavaScript dependencies
        run: npm ci

      - name: Install Playwright browsers
        run: npx playwright install --with-deps chromium

      # Migrate + seed the local SQLite database so builds and tests have data.
      - name: Set up the database
        run: npm run db:setup
```

此文件与其他 GitHub 工作流类似，但有几个关键点：

- 它只包含一个名为 `copilot-setup-steps` 的作业。Copilot 开始处理 PR 前，GitHub Actions 会运行此作业。
- 其中的 `workflow_dispatch` 触发器可让你从存储库的 **Actions** 选项卡手动运行工作流。在等待 Copilot 运行前，可用它测试工作流。

## 添加文档

大家都知道文档很重要，但多数项目的文档不是过时就是完全缺失。这类技术债务经常无人处理，会拖慢工作效率，也让代码库维护和团队新成员上手更困难。Copilot 擅长生成文档，因此很适合将这项任务交给 Copilot 云智能体，让它在后台生成所需文档。后续练习会审查其成果。

1. 在新的浏览器选项卡中打开 github.com 上的存储库。
2. 选择 **Issues** 选项卡。
3. 选择 **New issue** 打开新议题对话框。
4. 选择 **Blank issue** 创建空白议题。
5. 在 **Title** 中输入 `Code lacks documentation`。
6. 在 **Description** 中输入：

   ```plaintext
   Our organization has a requirement that functions and methods include TSDoc doc comments where helpful. Unfortunately, recent updates haven't followed this standard. We need to update the existing code to ensure doc comments are included where they clarify behavior.
   ```

7. 选择 **Create** 创建议题。
8. 在右侧选择 **Assign to Copilot**，打开分配对话框。

   ![将议题分配给 Copilot](../../../_images/shared-assign-copilot.png)

9. 选择 **Assign**。

   ![Copilot 议题分配详情](../../../_images/ex4-assign-copilot-details.png)

10. 选择 **Pull Requests** 选项卡。
11. 打开新创建的拉取请求（PR），标题可能类似 `[WIP]: Code lacks documentation`。如果列表中没有新 PR，请稍等片刻并刷新浏览器。
12. 几分钟后，Copilot 应会创建待办列表。

> [!NOTE]
> Copilot 生成待办列表可能需要几分钟。它需要创建环境（运行前面介绍的工作流）、分析项目并确定合适的处理方式。

13. 查看待办列表和计划执行的任务。
14. 向下滚动 PR 时间线，查看 Copilot 已开始处理议题的更新。
15. 选择 **View session** 按钮。

   ![Copilot 会话视图](../../../_images/ex4-view-session.png)

> [!CAUTION]
> 可能需要刷新窗口才能看到更新后的状态指示器。

16. 浏览实时会话，查看 Copilot 如何探索代码、了解当前状态、暂停思考并确定方案，以及编写代码。

这可能需要几分钟。云智能体的主要目标之一是异步执行任务，让开发者可以专注于其他工作。现在利用这个优势，再分配一项任务，然后转而编写应用功能。

## 在游戏详情页添加相关游戏部分

GitHub Copilot 云智能体的一大优势是可以拆分工作：你专注于一组任务，它处理另一组。即使为设计团队添加相关游戏部分不会花太长时间，也可以将时间用于其他工作。现在把它分配给云智能体。

1. 返回 github.com 上的存储库。
2. 选择 **Issues** 选项卡。
3. 选择 **New issue** 打开新议题对话框。
4. 选择 **Blank issue** 使用空白模板。
5. 在 **Title** 中输入 `Show related games on the game details page`。
6. 在 **Description** 中输入：

   ```markdown
   We want to help people discover more games by showing related games on each game's details page. The design team wants to explore the UX and do some acceptance testing. Our requirements are:

   - Add a data-access helper in `src/lib/` that returns other games in the same category as a given game, excluding that game
   - Show a "Related games" section on the game details page that uses the helper
   - Handle the case where a game has no related games
   - There should be unit tests created for the new helper
   - Before creating the PR, ensure all tests pass
   ```

7. 选择 **Create** 创建议题。
8. 在右侧选择 **Assign to Copilot**，打开分配对话框。

   ![将议题分配给 Copilot](../../../_images/shared-assign-copilot.png)

9. 选择 **Assign**。

不久后，议题的第一条评论上会出现一组 👀，表示 Copilot 已开始处理。

![Copilot 使用眼睛表情符号表示正在处理议题](../../../_images/ex4-issue-eyes-emoji.png)

Copilot 正在处理第二项请求。它像软件工程师一样工作，因此无需一直监控；等它完成后再审查即可。现在可以转而创建和使用自定义智能体。

## 总结和后续步骤

本课介绍了 [GitHub Copilot 云智能体][copilot-agents]。可以将议题分配给 Copilot，让它异步处理技术债务、创建新功能，或协助将代码迁移到其他框架。

你了解了：

- 自定义代码生成环境。
- 确保安全地执行操作。
- 编写范围明确的议题。
- 将议题分配给 Copilot。

云智能体正在后台处理任务，现在可以转而创建并使用自定义智能体。[Copilot 云智能体也可以使用模型上下文协议（MCP）服务器][cloud-agent-mcp]，并可使用前面模块介绍的自定义指令。

## 资源

- [关于 Copilot 云智能体][copilot-agents]
- [将 GitHub 议题分配给 Copilot][assign-issue]
- [Copilot 云智能体设置工作流最佳实践][cloud-agent-best-practices]

| [下一课：自定义智能体 →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions
