---
title: "演習 4 - エージェントの監視と管理"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

前の2つの演習で、ユーザー体験を改善して機能を追加する3つのタスクを Copilot cloud agent に依頼しました。cloud agent は非同期かつ自律的に動作しますが、タスクの進行状況を監視することも重要です。

cloud agent に割り当てたタスクは、GitHub.com の[エージェントページ][agents-page]など、さまざまなツールで管理できます。この Mission control では、オープンなプルリクエスト (PR) があるすべてのエージェントタスクを確認できます。実行内容を調べたり、進行中のセッションに指示を加えたりできます。

この演習では、次のことを行います。

- エージェントページで cloud agent のタスクを監視します。
- 進行中のセッションに追加機能を依頼します。

## シナリオ

ハイコントラストモードを作成するようエージェントに依頼した後、チームはライトモードも追加したいと考えました。サイトのスタイルと切り替え機能をすでに更新しているため、一緒に実装するのが合理的です。エージェントの作業を誘導し、ハイコントラストとライトモードの両方を追加します。

## Copilot cloud agent のタスクを確認する

Copilot cloud agent に割り当てたすべてのタスクの状況を確認します。

1. [Copilot agents ページ](https://github.com/copilot/agents)に移動します。
2. メインペインと左ペインのタスクリストを確認します。次のような、Copilot に割り当てたタスクが表示されます。
   - コードベースのドキュメント更新。
   - ゲーム変更用 API の生成。
   - Web サイトへのハイコントラストモード追加。
3. 実行中のタスクを選択し、Copilot が実施した作業を確認します。リポジトリのチェックアウト、作業環境の作成、MCP サーバーの設定、タスクを完了するための各種手順などが含まれます。

> [!NOTE]
> 表示される手順は、Copilot の作業状況や採用した方法によって異なります。

4. 右側に表示されるプルリクエスト (PR) ペインも確認します。PR と変更されたファイルを表示し、進行状況をさらに監視できます。

## cloud agent を誘導する

実行中のタスクを確認したので、ハイコントラストモードの作業中にライトモードも追加するよう Copilot に依頼します。

1. ハイコントラストモードの追加に関するセッションを選択します。Copilot が付けた名前や作業状況によって、タイトルは異なります。

    ![Mission control のアクセシビリティセッション](../../../_images/ex6-accessibility-session.png)

2. セットアップが完了し、作業を始めるまで数分待ちます。次のようなメッセージが表示されたら、作業を開始しています。
3. **Steer active session while Copilot is working** ダイアログに次のプロンプトを入力します。

    ```plaintext
    While we are working on a high contrast mode, let's also add a light mode. There should be a switch for this mode as well where users can select their desired display mode.
    ```

    ![エージェントページで作業中の cloud agent に指示するダイアログ](../../../_images/ex6-steer-cloud-agent-task.png)

4. <kbd>Enter</kbd> を押してプロンプトを送信します。
5. Copilot がプロンプトを受け取り、作業に反映したことを確認します。

## Copilot に作業を続けてもらう

Copilot は更新されたタスクに取り組みます。メッセージを送信した時点で進行中の手順を終えると、新しい要望を作業に加えます。

今回も数分かかるため、これまでに学んだことを振り返りましょう。

## まとめと次のステップ

この演習では、GitHub Copilot cloud agent のタスクを監視し、誘導する中心的な場所である Copilot agents ページを確認しました。ここで実行中・完了済みのタスクを追跡し、実行中のセッションの方向を変更できます。

この演習では、次のことを行いました。

- Mission control とエージェントページで cloud agent のタスクを監視しました。
- 進行中のセッションに追加機能を依頼しました。

Copilot がアクセシビリティ機能を完成させる間に、次は Copilot が作成した[プルリクエストを改善します][next-lesson]。

## リソース

- [Copilot agents ページ][agents-page]
- [カスタムエージェントについて][custom-agents]

---

| [← 前の演習: カスタムエージェント][previous-lesson] | [次の演習: Copilot の作業を反復改善する →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
