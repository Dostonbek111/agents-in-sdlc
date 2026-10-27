---
title: "モジュール 3 - エージェントを Web サイトに接続する"
description: "ローカルの Azure Functions プロキシとアクセシブルなチャットウィジェットを通じて、ホスト型の Backer Concierge を Tailspin Toys に接続します。"
authors:
  - juliamuiruri4
lastUpdated: 2026-09-16
next: false
---

[モジュール 2][previous-lesson]では、Backer Concierge をデプロイしてテストしました。[コンシェルジュを作るオプションシリーズ][overview]の最後のモジュールでは、そのエージェントをローカルの Tailspin Toys Web サイトから利用できるようにします。

このモジュールでは、次のことを行います。

- Foundry の資格情報をサーバー内に保持する、ローカルの Azure Functions プロキシを構築します。
- アクセシブルなチャットウィジェットをサイトに追加します。
- 会話の一連の流れを検証し、リソースをクリーンアップします。

## シナリオ

支援者がゲームを見つけるのは、開発者のターミナルや Azure ポータルではなく、Tailspin Toys の Web サイトです。チームは、カタログと並べてコンシェルジュを提供したいと考えています。チャットは追加の質問に対応し、サービスの資格情報を保護する必要があります。

## ホスト型エージェントを引き続き使う

Web サイトとの統合には、モジュール 2 でデプロイしたエージェントが必要です。エージェントは Foundry で実行したままにし、プロキシと Web サイトをローカルで実行します。

1. Codespace に戻り、`foundry-agent-cli` ブランチの Tailspin Toys リポジトリと既存の Copilot CLI セッションを開きます。
2. Backer Concierge がデプロイされており、[エージェントを構築してデプロイする][previous-lesson]でリモート呼び出しが成功したことを確認します。Azure リソースをすでに削除した場合は、前のモジュールを再実行してから続行します。

> [!IMPORTANT]
> このモジュールのプロキシと Web サイトはローカルで実行します。本番 Web サイトのデプロイではありません。[クリーンアップ][cleanup]を完了するまで、モデルとホスト型エージェントは課金対象の Azure リソースです。

## サーバー側プロキシを構築する

Tailspin Toys はすべて事前レンダリングされています。ブラウザーのコードからホスト型エージェントを直接呼び出したり、Foundry の資格情報をブラウザーに渡したりしてはいけません。ローカルの Azure Functions を追加し、**サーバー側で資格情報を隔離する境界**にします。Foundry への認証を行い、エージェントの回答だけをブラウザーに返します。

`microsoft-foundry` スキルはホスト型エージェントのワークフローを担当し、同じプラグインに含まれる Azure スキルはローカル Function プロジェクトの準備に使えます。それらのスキルでプロキシを構築し、資格情報を公開せずにエージェントに接続できることを確認します。

1. Copilot CLI に次のプロンプトを入力します。

    ```text
    Use the Azure skills to add an Azure Functions v4 Node.js and TypeScript project in api with one POST /api/concierge endpoint that invokes my deployed Backer Concierge hosted agent. This Function will run locally only; don't add it to azure.yaml or create Azure deployment infrastructure. Use DefaultAzureCredential with my local Azure sign-in. Keep the HTTP trigger thin, isolate the Foundry client in a unit-testable module, validate and limit request bodies, set explicit timeouts, and return sanitized errors. Store the Foundry project endpoint and agent name in local server-side settings that are excluded from version control. Never return credentials or access tokens to the browser. The Astro site is `output: 'static'` with no dev proxy, so also add a local-only Vite dev-server proxy for /api to the Function's port in astro.config.mjs, so relative /api/concierge requests reach it during `astro dev`.

    For conversation state, generate a high-entropy handle on the server, map it to the Foundry conversation server-side with an expiration, and never expose a raw Foundry conversation or thread identifier. Reject malformed, expired, and unknown handles. Add focused unit tests.
    ```

    ![Azure Functions のローカルプロキシを設定する画面](../../../../_images/cli-8-azure-functions-proxy.png)

2. 別のターミナルを開き、Copilot が提示したコマンドでローカル Function を起動します。Function は実行したままにします。
3. Copilot CLI に戻り、次のプロンプトでローカルプロキシをテストします。

    ```text
    Send a request to the local /api/concierge endpoint asking "Which games are under $30?" and show me the sanitized JSON response. Confirm that the request reaches the deployed Backer Concierge through DefaultAzureCredential.
    ```

4. 応答を確認します。カタログに価格が含まれないことを説明している必要があります。Foundry トークン、資格情報、プロジェクトのエンドポイント、Foundry の会話識別子そのもの、スタックトレースが含まれていてはいけません。

    ![ローカルのコンシェルジュエンドポイントから返された、機密情報を除去済みの JSON 応答](../../../../_images/cli-8-sanitized-json-response.png)

## チャットウィジェットを構築する

プロキシにより、ブラウザーからコンシェルジュに安全に接続できます。次に、サイトにチャットウィジェットを追加し、Playwright で会話の流れ全体を確認します。

1. 次のプロンプトを Copilot に送り、サイトとの統合を依頼します。

    ```text
    Add an accessible Backer Concierge chat widget as an Astro component and render it site-wide from Layout.astro. It should POST to /api/concierge and thread the conversation using the returned opaque conversation handle, follow the dark theme in style.instructions.md, support Escape to close, and include data-testid attributes.
    ```

2. ローカル Function は実行したままにして、別のターミナルで Copilot が提示したコマンドを使い Astro サイトを起動します。
3. Copilot CLI に戻ります。[レッスン 6][playwright-lesson]で確認または追加した Playwright MCP サーバーは、すでに利用できます。次のプロンプトでウィジェットをテストします。

    ```text
    Use the Playwright MCP server to test the Backer Concierge widget end to end in the running Tailspin Toys site. Verify its core chat flow, conversation continuity, accessibility, error handling, grounding boundaries, and secure use of the local proxy. Report the results and include evidence for any failures.
    ```

    ![Tailspin Toys サイト内の Backer Concierge ウィジェットのスクリーンショット](../../../../_images/cli-8-backer-concierge-widget.png)

4. 報告された根拠と照らし合わせて結果を確認します。チェックに失敗した場合は、該当するプロキシまたはウィジェットの動作を修正するよう Copilot に依頼し、終了前に失敗したチェックを再実行します。

## リソースをクリーンアップする

最終チェックポイントに到達しました。ローカル Web サイトでコンシェルジュが動作しています。シリーズ共通のクリーンアップ手順で、ローカルサービスと Azure リソースを削除します。

1. [リソースをクリーンアップする][cleanup]の手順を完了します。ローカルサービスを停止し、Azure リソースの削除が完了したことも確認します。

## まとめと次のステップ

ローカルのサーバー側プロキシとアクセシブルなチャットウィジェットを通じて、ホスト型の Backer Concierge を Tailspin Toys に接続しました。シリーズ全体を通じて、GitHub Copilot CLI と Foundry を使い、モデルを準備し、エージェントを構築してデプロイし、Web サイトとの統合全体を検証しました。

CLI ワークショップを締めくくるために、[まとめと次のステップ][review]に進みます。

[overview]: ../
[previous-lesson]: ../2-build-and-deploy/
[review]: ../../10-review/
[playwright-lesson]: ../../6-mcp-playwright/
[cleanup]: ../#リソースをクリーンアップする
