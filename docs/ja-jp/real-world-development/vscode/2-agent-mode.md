---
title: "演習 2 - Copilot エージェントモードで機能を追加する"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

アプリケーションの簡単な更新でも、複数のファイルを変更し、テストを実行する必要があります。開発者は必要なファイルを探して変更し、テストを実行し、問題を調査して、見落としたファイルを修正します。この作業は何度も繰り返されることがあります。

ここで役立つのが Copilot エージェントモードです。

Copilot エージェントモードは IDE 内でより自律的に作業します。開発者と同様にプロジェクト構成を調べ、必要な変更を行い、テストなどのタスクを実行し、見つかった問題を自動的に修正します。エージェントモードを使ってサイトに機能を追加する方法を確認しましょう。

> [!NOTE]
> 名前は似ていますが、エージェントモードと cloud agent は異なる用途向けです。エージェントモードは IDE 内で作業し、すばやいフィードバックと対話を可能にします。cloud agent はチームメンバーのように非同期で作業し、Issue やプルリクエストを通じてやり取りします。

この演習では、次のことを確認します。

- Copilot エージェントモードがプロジェクトを調べ、関連ファイルを特定し、複数のファイルを連携して変更する方法。
- GitHub Copilot エージェントモードが UI とデータ層にまたがる機能を実装する方法。
- Copilot エージェントモードが生成した変更とテストを、コードベースにマージする前にレビューする方法。

## シナリオ

ゲーム一覧が増えてきたため、カテゴリと publisher で絞り込めるようにします。前の演習で publisher ヘルパーを追加しました。ここでは Copilot エージェントモードを使い、残りのデータ層、UI、テストを完成させます。

## Tailspin Toys の Web サイトを実行する

変更を加える前に、Tailspin Toys の Web サイトを確認して、現在の機能を把握します。

この Web サイトは、開発者をテーマにしたボードゲームのクラウドファンディングプラットフォームです。ゲーム一覧と詳細を表示します。単一の Astro アプリで、ビルド時に静的 HTML としてページを生成します。ページは frontmatter 内で Drizzle ORM を使ってローカルの SQLite データベースを直接参照します。独立したバックエンド API やクライアント側 UI フレームワークはありません。再利用可能なデータアクセスヘルパーは `src/lib/` にあり、対話機能は標準 DOM API を使った小規模な Astro `<script>` で追加します。

### Web サイトを起動する

Web サイトを簡単に起動できるよう、単一の Astro 開発サーバーを起動する `npm run dev` タスクが用意されています。GitHub Codespace で次の手順を実行します。

1. codespace に戻ります。前の演習を終えていれば、`main` にいるはずです。
2. Codespace 内で <kbd>Control</kbd>+<kbd>`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux) を押して新しいターミナルを開きます。
3. `main` を更新し、フィルター作業用のブランチを作成します。

   ```bash
   git checkout main
   git pull
   git checkout -b filtering-vscode
   ```

4. 次のコマンドを実行して Web サイトを起動します。

   ```bash
   npm run dev
   ```

   Astro 開発サーバーの準備ができると、次のような URL を示すバナーが表示されます。

   ```bash
   🚀 Tailspin Toys is ready!
      Astro server: http://localhost:4321
      Press Ctrl-C to stop.
   ```

> [!NOTE]
> `http://localhost:4321` を開くよう促すダイアログが表示された場合は、**x** を選択して閉じます。

5. <kbd>Command</kbd> (Mac) または <kbd>Ctrl</kbd> (Windows/Linux) を押しながら、ターミナルに表示された Astro サーバーのアドレス `http://localhost:4321` を選択して Web サイトを開きます。

> [!NOTE]
> codespace でターミナルの localhost URL を選択すると、`https://<your-codespace-name>-4321.app.github.dev/` に自動的にリダイレクトされます。これは Web サーバーを実行している codespace へのプライベートトンネルです。

### Web サイトを確認する

サイトを起動したら、機能を確認します。主な機能は次のとおりです。

- **ホームページ**: ボードゲームのタイトル、画像、説明を一覧表示します。
- **ゲーム詳細ページ**: ゲームを選択すると、タイトル、説明、publisher、カテゴリなどの詳細が表示されます。

## Copilot でバックログを確認する

> [!TIP]
> **Copilot Chat を開く**
>
> 次の操作を始める前に codespace に戻り、Copilot Chat パネルを開いて **New Chat** を選択し、新しい会話を始めます。モードとモデルの選択は演習によって異なります。必要な場合は各手順で説明します。

Web サイトの初期実装は動作しますが、さらに機能を追加します。テンプレートからプロジェクトを作成したときに、Issue のバックログも自動で作成されています。GitHub Copilot に内容を表示してもらいます。

1. Chat ビューのエージェントドロップダウンから **Agent** を選択します。エージェントモードはファイルをまたぐ変更を計画・実装し、ターミナルコマンドを実行してツールを呼び出します。

   ![Chat ビューでエージェントを選択する画面](../../../_images/shared-chat-mode-selector.png)

2. 利用可能なモデル一覧から **Claude Sonnet 4.5** を選択します。

> [!CAUTION]
> このワークショップの作成者が特定のモデルを推奨しているわけではありません。ワークショップの作成時に Claude Sonnet 4.5 を使用したため、手順に含めています。提案の一貫性を高めることが目的ですが、大規模言語モデル (LLM) の出力は確定的ではないため、説明と異なる提案が表示されることがあります。これは通常の動作です。

> [!NOTE]
> 確率的な動作のため、Copilot が別の MCP コマンドを使う場合がありますが、タスクは完了できるはずです。

3. 次のプロンプトを Copilot に送信して、Issue のバックログを確認します。

   ```plaintext
   Please show me the backlog of items from my GitHub repository. Help me prioritize them based on those which will be most useful to the user.
   ```

4. **Continue** を選択して、すべての Issue を一覧表示するコマンドを実行します。
5. 生成された Issue の一覧を確認します。

Copilot がユーザーに最も役立つと思われる項目を優先順位付けしたことも確認できます。

## 指示ファイルを確認する

Copilot にコードを生成させる前に、作業のコンテキストを与える指示ファイルを確認します。Web サイトへの機能追加方法を説明する[ユーザーインターフェイス (UI) 指示ファイル](https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md)を使います。

1. codespace で `.github/instructions/ui.instructions.md` を開きます。
2. 機能追加に関する全体的な指針を確認します。アーキテクチャの概要、コンポーネント設計・テスト容易性・アクセシビリティの原則、ファイル種別ごとの指示ファイルへのリンク (Astro、Tailwind CSS、Drizzle など) が含まれます。

> [!TIP]
> 指示ファイルから、他の指示ファイルやプロジェクト内ファイルを参照できます。パスは指示ファイルからの相対パスです。これにより、複雑な指示を再利用しやすい小さなファイルに分け、例やひな形を示せます。

## フィルター機能を実装する

フィルター機能を完成させるには、少なくとも次の3種類の変更が必要です。

- データ層 (`src/lib/`) に残りのフィルター処理を追加または調整します。
- フィルターの動作を確認するテストを追加または更新します。
- ゲーム一覧ページにフィルター UI を追加します。

さらに、コードベースにマージする前にテストを実行し、成功させる必要があります。Copilot エージェントモードに作業を依頼しましょう。

1. 現在の Copilot 会話を続けるか、**New Chat** を選択して新しい会話を始めます。
2. **Add Context**、**Instructions**、**ui** の順に選択して指示ファイルを追加します。

   ![UI 指示ファイルを選択する画面](../../../_images/ex3-select-instructions-file.png)

3. Chat ビューで **Agent** が選択されていることを確認します。

   ![Chat ビューでエージェントを選択する画面](../../../_images/shared-chat-mode-selector.png)

4. モデルが **Claude Sonnet 4.5** のままであることを確認します。
5. バックログの関連 Issue に基づいて機能を実装するよう、次のプロンプトを送信します。

   ```plaintext
   Please update the site to include filtering by publisher and category based on the requirements from the related GitHub issue in the backlog. A publishers helper already exists from the previous exercise, so preserve and refine that work as needed while completing the remaining data-layer, UI, and tests. Ensure all tests are passing before completion. The server is already running, so you do not need to start it up.
   ```

6. Copilot がプロジェクトを調べ、機能に関連するファイルを特定します。データ層のヘルパー、UI、テストを探し、ファイルを変更してテストを実行する様子を確認します。

   ![Copilot がプロジェクトファイルを調べている画面](../../../_images/ex3-agent-mode-explores.png)

> [!NOTE]
> Copilot はプロジェクトの調査、ファイル変更、テスト実行などを行うため、タスクとコードベースの複雑さによって数分かかる場合があります。作業中、エディターに **Keep** と **Undo** が表示されることがあります。作業中に選択する必要はありません。Copilot が完了すると、変更ごとに **Keep** または **Undo** を選べるようになります。

7. Copilot に求められたら **Continue** を選択してテストを実行します。

   ![Copilot Chat がテスト実行の確認を求める画面](../../../_images/ex3-agent-mode-run-tests.png)

8. 作業中に停止したり、テストが失敗したりすることがあります。Copilot はコード生成とテストを繰り返し、エラーが見つからなくなるまで作業します。

   ![Copilot エージェントモードの完了したチャットセッション](../../../_images/ex3-agent-mode-proposed-changes.png)

9. 生成されたコードに問題がないか確認します。

> [!CAUTION]
> Copilot などの AI ツールが生成したコードは、必ずレビューしてください。

10. Web サイトを開いているブラウザーに戻り、新機能を試します。
11. 動作とコードを確認したら、Copilot Chat ウィンドウで **Keep** を選択します。

## まとめと次のステップ

この演習では、GitHub Copilot エージェントモードを使って Tailspin Toys の Web サイトに機能を追加しました。

- GitHub Copilot エージェントモードで UI とデータ層にまたがる機能を実装しました。
- Copilot エージェントモードでプロジェクトを調べ、関連ファイルを特定し、連携して変更しました。
- Copilot エージェントモードが生成した変更とテストを、コードベースにマージする前にレビューしました。

次は[Playwright MCP サーバーで機能をテストし][next-lesson]、プルリクエストを作成します。

### 追加の探索演習 — ページネーションを実装する

ゲーム一覧が増えると、ページネーションが必要になります。この演習で学んだ方法を使い、Copilot にページネーションを追加してもらいます。既存のベストプラクティスと指示ファイルに従い、ページサイズをユーザーが選べるようにするか固定値にするかを検討してください。望む実装に必要な情報をプロンプトに含め、必要に応じて Copilot に変更や追加コンテキストを依頼します。Copilot と反復して作業するのは通常の進め方です。

## リソース

- [Copilot の ask、edit、agent モード: 機能と使い分け][choose-mode]
- [VS Code のエージェントモード][vs-code-agent-mode]

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-mcp/
[choose-mode]: https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/
[vs-code-agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
