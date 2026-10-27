---
title: "演習 2 - GitHub Copilot cloud agent"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 前の演習: カスタム指示][previous-lesson] |
|:--|

多くの組織が、未解決のセキュリティ問題、更新が必要なレガシーコード、実装する時間がなくバックログに残った機能要求などの技術的負債に直面しています。GitHub Copilot cloud agent は、コードの更新や機能の追加といったタスクを自律的に実行できます。作業が完了すると、人間の開発者がレビューできるドラフトのプルリクエスト (PR) を作成します。手間のかかる作業を任せて開発を加速し、開発者はより大きな課題に集中できます。

Copilot cloud agent を使って、次の内容を確認します。

- コード生成に使う環境をカスタマイズする方法。
- 操作を安全に行う方法。
- 範囲を明確に定めた Issue の重要性。
- Issue を Copilot に割り当てる方法。

## シナリオ

Tailspin Toys には対処したい技術的負債があります。最初のサイトを開発した委託先がドキュメントを適切に整備しませんでした。実際、ドキュメントがまったくありません。まず、アプリケーションのエクスポートされたすべての関数に TypeScript documentation (TSDoc) doc comment を追加します。

また、デザインチームはゲームを見つけやすくしたいと考えています。各ゲームの詳細ページに、同じカテゴリの別タイトルを表示して閲覧を続けられるようにします。現時点では完成度の高い実装ではなく、ユーザー体験 (UX) の受け入れテストに使えるものを求めています。この作業は現在の阻害要因ですが、より優先度の高い Issue もあります。

こうした作業は優先順位が下がりやすく、Copilot cloud agent に適しています。cloud agent に非同期で作業させれば、開発者は別のタスクに取り組み、後から Copilot の成果をレビューできます。

## GitHub Copilot cloud agent の概要

[GitHub Copilot cloud agent](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent) は、人間の開発者と同様にバックグラウンドで作業できます。たとえば、[GitHub Issue を Copilot に割り当て][assign-issue]ます。割り当てられると、Copilot は進行状況を追跡するドラフトのプルリクエストを作成し、環境を設定してタスクに取りかかります。作業中でも完了後でも、Copilot のセッションを確認できます。提案された解決策のレビュー準備ができると、プルリクエストで通知されます。

## 範囲を適切に定めた指示の重要性

GitHub Copilot に魔法はありません。考える作業を省略できるわけではありません。一見簡単な操作でも、詳しく調べると複雑さがあります。[Copilot cloud agent に依頼するタスクの範囲][cloud-agent-best-practices]を慎重に決めてください。AI ペアプログラマーとして扱い、段階を踏んで学び、試し、調整します。生成 AI を導入しても、ソフトウェア開発の基本は変わりません。

## このリポジトリのカスタム指示

前の演習では、カスタム指示と Copilot の作業への影響を確認しました。カスタム指示を実際に試した場合は `.github/copilot-instructions.md` が機能することを確認済みです。まだの場合は、この機会に簡単に確認してください。cloud agent に作業を割り当てる前に、このリポジトリに含まれる指示ファイルを読み取り専用で確認しておくと、後で影響を把握できます。

GitHub の Web UI または起動済みの codespace で、次のファイルを開きます。

- `.github/copilot-instructions.md` — **Code standards** セクション、特に TypeScript の規約と TSDoc doc comment の要件を確認します。
- `.github/instructions/unit-tests.instructions.md` — `applyTo` フロントマターを確認します。この指示は `**/*.test.ts` に一致するファイルに適用されます。

次のセクションで `Code lacks documentation` Issue を cloud agent に割り当てるとき、生成されたプルリクエストに TSDoc doc comment、TypeScript の規約、ファイル先頭コメントが含まれているか確認してください。後の演習で PR をレビューするときに、改めて確認します。

## Copilot cloud agent 用の開発環境を設定する

コードを作成するときは、必要な環境とセットアップスクリプトを用意します。Copilot にタスクを割り当てる場合も同様です。Copilot はソフトウェアエンジニアのように作業します。

cloud agent は作業環境に [GitHub Actions][github-actions] を使います。`.github/workflows/copilot-setup-steps.yml` に設定する[専用のセットアップワークフロー][setup-workflow]を作成すると、作業開始前に必要な開発ツールや依存関係を利用できるようになります。このラボでは、Node.js、プロジェクトの依存関係、エンドツーエンドテスト用ブラウザー、単一 Astro アプリ用にマイグレーションとシードを適用した SQLite データベースを cloud agent が使えるよう、あらかじめ設定されています。

```yaml
name: "Copilot Setup Steps"

# リポジトリの「Actions」タブからセットアップ手順をテストできます
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # 自分の手順に必要な最小限の権限を設定します。Copilot の操作には専用のトークンが付与されます。
    permissions:
      # セットアップ手順でリポジトリをクローンする場合 (依存関係のインストールなど) は、contents: read が必要です。クローンしない場合は、手順の完了後に Copilot が自動でクローンします。
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # フロントエンド / アプリのセットアップ (アプリは Astro + Drizzle/libSQL)
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

      # ローカル SQLite データベースをマイグレーションしてシードし、ビルドとテスト用のデータを用意します。
      - name: Set up the database
        run: npm run db:setup
```

このファイルは一般的な GitHub ワークフローと似ていますが、いくつか重要な点があります。

- `copilot-setup-steps` というジョブが1つあります。このジョブは、Copilot がプルリクエストの作業を始める前に GitHub Actions で実行されます。
- `workflow_dispatch` トリガーを使うと、リポジトリの **Actions** タブからワークフローを手動実行できます。Copilot の実行を待たずに、正常に動作するかテストできます。

## ドキュメントを追加する

ドキュメントの重要性は広く理解されていますが、情報が古かったり、まったく整備されていなかったりするプロジェクトは少なくありません。こうした技術的負債は生産性を下げ、コードベースの保守や新しいメンバーの参加を難しくします。Copilot はドキュメント作成が得意なため、cloud agent に依頼するのに適したタスクです。バックグラウンドで必要なドキュメントを作成してもらい、後の演習で成果をレビューします。

1. 新しいブラウザータブで github.com の自分のリポジトリに移動します。
2. **Issues** タブを選択します。
3. **New issue** を選択して、新しい Issue のダイアログを開きます。
4. **Blank issue** を選択します。
5. **Title** に `Code lacks documentation` と入力します。
6. **Description** に次の内容を入力します。

    ```plaintext
    Our organization has a requirement that functions and methods include TSDoc doc comments where helpful. Unfortunately, recent updates haven't followed this standard. We need to update the existing code to ensure doc comments are included where they clarify behavior.
    ```

7. **Create** を選択して Issue を作成します。
8. 右側の **Assign to Copilot** を選択して、割り当てダイアログを開きます。

  ![Copilot を Issue に割り当てる画面](../../../_images/shared-assign-copilot.png)

9. **Assign** を選択します。

  ![Copilot の割り当て詳細画面](../../../_images/ex4-assign-copilot-details.png)

10. **Pull Requests** タブを選択します。
11. 新しく作成されたプルリクエスト (PR) を開きます。タイトルは `[WIP]: Code lacks documentation` のようなものです。PR が一覧に表示されない場合は、少し待ってからブラウザーを更新します。
12. 数分後、Copilot が TODO リストを作成したことを確認します。

> [!NOTE]
> Copilot の TODO リストが PR に表示されるまで数分かかることがあります。Copilot は環境を作成し (先ほど確認したワークフローを実行)、プロジェクトを分析して、問題に取り組む方法を決めています。

13. TODO リストを確認し、Copilot が実行するタスクを確認します。
14. プルリクエストのタイムラインを下へスクロールし、Copilot が Issue に取り組み始めたことを示す更新を確認します。
15. **View session** ボタンを選択します。

  ![Copilot セッションの表示画面](../../../_images/ex4-view-session.png)

> [!CAUTION]
> 表示が更新されない場合は、ウィンドウを更新してください。

16. ライブセッションをスクロールし、Copilot がどのように問題を解決するか確認します。コードを調査して状況を把握し、計画を検討してからコードを作成する様子を確認できます。

この作業には数分かかる場合があります。Copilot cloud agent の主な目的は、作業を非同期に行い、開発者が別のタスクに集中できるようにすることです。この機能を活用し、もう1つのタスクを cloud agent に割り当てた後、アプリへの機能追加にも取り組みます。

## ゲーム詳細ページに関連ゲームを追加する

GitHub Copilot cloud agent の利点の1つは、作業を分担できることです。デザインチーム向けに関連ゲームを表示する作業自体は長くかからないかもしれませんが、その時間を別の作業に使えます。cloud agent に依頼しましょう。

1. github.com の自分のリポジトリに戻ります。
2. **Issues** タブを選択します。
3. **New issue** を選択して、新しい Issue のダイアログを開きます。
4. 空のテンプレートを使うため、**Blank issue** を選択します。
5. **Title** に `Show related games on the game details page` と入力します。
6. **Description** に次の内容を入力します。

    ```markdown
    We want to help people discover more games by showing related games on each game's details page. The design team wants to explore the UX and do some acceptance testing. Our requirements are:

   - Add a data-access helper in `src/lib/` that returns other games in the same category as a given game, excluding that game
   - Show a "Related games" section on the game details page that uses the helper
   - Handle the case where a game has no related games
   - There should be unit tests created for the new helper
   - Before creating the PR, ensure all tests pass
    ```

7. **Create** を選択して Issue を作成します。
8. 右側の **Assign to Copilot** を選択して、割り当てダイアログを開きます。

  ![Copilot を Issue に割り当てる画面](../../../_images/shared-assign-copilot.png)

9. **Assign** を選択します。

まもなく、Issue の最初のコメントに 👀 が表示され、Copilot が作業を始めたことがわかります。

![目の絵文字で Copilot が Issue に取り組んでいることを示す画面](../../../_images/ex4-issue-eyes-emoji.png)

Copilot は2つ目の依頼に取り組み始めました。Copilot cloud agent はソフトウェアエンジニアと同じように作業するため、常に監視する必要はありません。完了後にレビューできます。次はカスタムエージェントの作成と使用を確認します。

## まとめと次のステップ

[GitHub Copilot cloud agent][copilot-agents] を使うと、Issue を Copilot に割り当て、非同期に作業させることができます。技術的負債への対処、新機能の作成、別のフレームワークへのコード移行などを依頼できます。

この演習では、次の内容を確認しました。

- コード生成用の環境をカスタマイズする。
- 操作を安全に行う。
- 範囲を明確に定めた Issue の重要性。
- Issue を Copilot に割り当てる。

Copilot がバックグラウンドで作業している間に、次はカスタムエージェントを作成して使用します。[Copilot cloud agent は MCP サーバーも利用できます][cloud-agent-mcp]。前のモジュールで確認したカスタム指示も利用できます。

## リソース

- [Copilot cloud agent について][copilot-agents]
- [GitHub Issue を Copilot に割り当てる][assign-issue]
- [Copilot cloud agent セットアップワークフローのベストプラクティス][cloud-agent-best-practices]

| [次の演習: カスタムエージェント →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions
