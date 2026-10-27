---
title: "レッスン 3 - エージェントモード: Plan と Autopilot"
description: "Plan で進め方を合意し、Autopilot で Issue に基づくフィルターを作成して、Interactive モードで結果をレビューします。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

最初は小さな機能を追加しました。より大きな変更には、さらに堅実なプロセスが必要です。GitHub Copilot CLI は組織の既存の開発フローに沿って作業できます。このレッスンから数回にわたり、Issue から機能を作成し、コードの妥当性と動作を確認して、プロジェクトにマージするまでの一般的なエージェント主導の開発を行います。

> [!NOTE]
> 機能開発を続ける間、同じ会話とブランチを使います。通常はファイル種別ごとに異なるブランチや PR を使いますが、ここでは主な概念に集中するため手順を簡略化します。

このレッスンでは、次のことを行います。

- GitHub Issue から新しい Copilot 会話を開始します。
- Plan モードで要件を定義します。
- Autopilot モードで機能を実装します。
- コードをレビューします。
- 転送されたブラウザーで機能を手動確認します。

機能開発を続けながら、リポジトリの指示を更新し、既存の `quality-checks` スキルをカスタマイズし、MCP による検証を追加して QA エージェントを作成し、機能の PR を開きます。

## シナリオ

Tailspin Toys のカタログが増え、訪問者がカテゴリと publisher でゲームを絞り込める必要があります。バックログの Issue には機能が説明されていますが、カテゴリを組み合わせる方法など、コーディング前に合意が必要な詳細があります。Plan モードで決定し、範囲を定めた実装を Autopilot に任せます。

## 背景

AI コーディングエージェントを開発フローに導入しても、基本的な手順は変わりません。一般的な開発フローは次のとおりです。

1. 実装内容を説明する Issue から始めます。
2. 作成するものを計画します。
3. コードを作成してレビューします。
4. テストを実行してコードを検証します。
5. 新機能を手動で確認します。
6. プルリクエスト (PR) を作成します。
7. コードレビューと継続的インテグレーションが成功したら、コードをマージします。

> [!NOTE]
> 手順の詳細は、チームや組織によって異なります。多くの場合、上記の流れを調整したものになります。

標準的な進め方を守ることで、AI が生成したコードが要件を満たし、手作業で書いたコードと同じレビューを受けるようにできます。

## 会話モード

**会話モード**は、エージェントにどの程度の自律性を与えるかを制御します。<kbd>Shift</kbd>+<kbd>Tab</kbd> を押すとモードを切り替えられます。

- **Interactive**: 開発者とエージェントが協力して作業します。エージェントは変更を提案し、次に進む前に入力を待ちます。
- **Plan**: エージェントが計画を作成します。プロジェクトファイルの編集はできません。
- **Autopilot**: エージェントが自律的にコードを書き、テストを実行し、タスクが完了するまで反復します。

最初に Plan モードで計画を確認し、その後 Autopilot で実装します。

## Issue から始める

フィルター作業を始める前に、Codespace とリポジトリを準備します。

1. Codespace に戻ります。停止している場合は再起動します。
2. 星評価の PR がマージ済みであることを確認します。
3. ターミナルが開いていない場合は、<kbd>Control</kbd>+<kbd>`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux) を押します。
4. `main` を更新し、フィルター作業用のブランチを作成します。

   ```bash
   git checkout main
   git pull --ff-only
   git checkout -b game-filters-cli
   ```

5. Copilot CLI を起動します。

   ```bash
   copilot --yolo
   ```

6. <kbd>Tab</kbd> を2回押して **Issues** タブを開きます。
7. <kbd>A</kbd> を押してすべての Issue を表示します。
8. 矢印キーを使い、**Allow users to filter games by category and publisher** というタイトルの Issue を選択します。
9. <kbd>C</kbd> を押して Issue をプロンプトに追加し、**Session** タブに戻ります。

プロンプトが `#7` (または同様の番号) で始まることを確認します。`#` を使うと、GitHub 上の Issue やプルリクエスト (PR) をコンテキストに追加できます。

## フィルター機能を計画する

計画を立てることで、Copilot に実装を任せる前に機能やタスクの進め方を決められます。複雑な作業では、時間をかけて計画することをお勧めします。Plan モードに切り替え、Copilot に計画を作成してもらいます。

1. <kbd>Shift</kbd>+<kbd>Tab</kbd> を押して Plan モードに切り替えます。プロンプト下のモード表示が **Plan** であることを確認します。
2. 前の手順で追加した Issue 参照の後に、次のプロンプトを入力します。

   ```plaintext
   Create a plan for implementing this feature.
   ```

Copilot はプロジェクトを調べ、適切な方法を決めて計画を作成します。

3. フィルターの動作について質問された場合は、希望に応じて回答します。唯一の正解はありません。
4. 計画が完成したら、<kbd>Control</kbd>+<kbd>E</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>E</kbd> (Windows/Linux) を押して計画を展開します。
5. 計画を上下にスクロールして確認します。
6. 決定した内容と合わない箇所があれば、Copilot に修正を依頼します。

## Autopilot を承認する

計画を作成してレビューしたら、実装に進みます。Autopilot を使うと、Copilot が問題を解決したと判断するまで反復して作業します。

1. **Accept plan and build on autopilot (recommended)** またはインストール済みバージョンで同様の名前の選択肢を選びます。
2. プロンプト下のモード表示が **Autopilot** であることを確認します。
3. Copilot が計画に沿ってコードを生成し、テストを実行する様子を確認します。

> [!NOTE]
> 承認するとすぐに実装が始まる場合があります。事前に計画を確認してください。依存関係が不足している、またはポートが競合していると報告された場合は、セットアップの問題を解決してからチェック結果を確認します。

## 実装をレビューして検証する

他のコードと同様に、生成されたコードはマージ前にレビューします。コードを確認し、サイトを起動して問題がないか確認します。

1. <kbd>Shift</kbd>+<kbd>Tab</kbd> を押して Interactive モードに切り替えます。モード表示が **Plan** または **Autopilot** ではないことを確認します。
2. `/diff` を入力して、フィルターの実装とテストを確認します。
3. 結果を Issue と計画時の決定に照らし合わせます。
4. コードのレビューが終わったら <kbd>Esc</kbd> を押して差分画面を閉じます。
5. プロジェクトのチェック結果を確認し、失敗があれば Copilot に修正を依頼します。

## 新機能を確認する

コードに問題がないことを確認したら、アプリを起動し、Codespaces で転送されたポートからサイトを開きます。

1. Copilot に次のプロンプトを送信してアプリを起動します。

   ```plaintext
   Start the app so I can try the filtering feature in my browser. Tell me the URL and leave the server running.
   ```

2. Codespaces でポート `4321` が利用可能と表示されたら、**Open in Browser** を選択します。
3. カテゴリ、publisher、および計画で合意した組み合わせによるフィルターを試します。
4. リセットと該当なしの場合の動作が、Issue と決定内容に合っていることを確認します。
5. Codespace に戻り、起動した開発サーバーを停止するよう Copilot に依頼します。

## まとめと次のステップ

会話モードを使い分けて、機能を作成してレビューしました。

- GitHub Issue から新しい Copilot 会話を開始しました。
- Plan モードで要件を定義しました。
- Autopilot モードで機能を実装しました。
- コードをレビューしました。
- 転送されたブラウザーで機能を手動確認しました。

次は[カスタム指示を使って][next-lesson]、コードが文書化された規約に従うようにします。

## リソース

- [GitHub Copilot CLI の Autopilot][autopilot]
- [Copilot CLI コマンドリファレンス][cli-reference]

[autopilot]: https://docs.github.com/copilot/concepts/agents/copilot-cli/autopilot
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[previous-lesson]: ../2-add-star-rating/
[next-lesson]: ../4-custom-instructions/
