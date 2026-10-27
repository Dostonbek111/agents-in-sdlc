---
title: "モジュール 1 - プロジェクトとモデルを準備する"
description: "Azure ツールをセットアップし、Tailspin のカタログをエクスポートして、GitHub Copilot CLI で Foundry モデルを選択してテストします。"
authors:
  - juliamuiruri4
lastUpdated: 2026-09-16
---

これは[オプション: Foundry を組み込む][overview]の最初のモジュールです。ツールとカタログを準備してから、Copilot で Foundry プロジェクトを作成し、エージェントを構築する前にデプロイ済みのモデルをテストします。

このモジュールでは、次のことを行います。

- Azure コマンドラインツールと Azure Skills Plugin をインストールします。
- カタログをエクスポートし、Foundry での作業を計画します。
- モデルを選択、デプロイして、カタログ情報の範囲を守れるかテストします。

## シナリオ

Tailspin Toys には、カタログの事実と会社が提供していない情報を区別できるコンシェルジュが必要です。評価の高いパズルゲームを勧めることはできますが、そのゲームの資金調達総額を作り上げてはいけません。本格的なアシスタントを開発する前に、選択したモデルがこの境界を守れるか確認します。

## 前提条件とセットアップ

Backer Concierge のホストには Azure を使い、Azure リソースの操作を Copilot CLI に案内してもらいます。最初に必要なコマンドラインツールとプラグインを準備します。

> [!IMPORTANT]
> [クリーンアップの手順][cleanup]は、シリーズを完了する場合だけでなく、このモジュールで終了する場合にも適用されます。

1. Azure サブスクリプションがあることを確認します。必要な場合は、[$200 のクレジット付き無料 Azure サブスクリプション][azure-free]や、[$100 のクレジット付き Azure for Students][azure-students]などを利用できます。
2. Tailspin Toys の Codespace に戻り、ターミナルを開きます。
3. 開発コンテナーに Azure CLI をインストールします。

    ```bash
    curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
    az version
    ```

4. `az login` で Azure CLI にサインインし、`az account show` で正しいサブスクリプションを使っていることを確認します。
5. [Azure Developer CLI][install-azd] バージョン 1.27.1 以降をインストールします。Microsoft Foundry は、ホスト型エージェントのテストとデプロイに `azd` を使います。

    ```bash
    curl -sL https://aka.ms/install-azd.sh | bash
    azd version
    ```

6. `azd auth login` で Azure Developer CLI にサインインし、`azd config show` で正しいサブスクリプションを使っていることを確認します。
7. Azure Developer CLI (azd) の Foundry 拡張機能をインストールします。

    ```bash
    azd ext install microsoft.foundry
    ```

8. コマンドパレットから Copilot CLI の新しいセッションを横に開きます。<kbd>Command</kbd>+<kbd>Shift</kbd>+<kbd>P</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>P</kbd> (Windows/Linux) を押し、**Chat: New Copilot CLI session to the side** を選択します。
9. Azure Skills マーケットプレースを追加します。プラグインの初回インストール時のみ必要です。

    ```text
    /plugin marketplace add microsoft/azure-skills
    ```

10. [Azure Skills Plugin][azure-skills] をインストールします。Azure スキル、Azure MCP Server、Foundry MCP Server が GitHub Copilot CLI に追加されます。

    ```text
    /plugin install azure@azure-skills
    ```

11. Azure MCP サーバーが設定されたことを確認します。

    ```text
    /mcp list
    ```

12. スキルや MCP サーバーが表示されない場合は、`/skills reload` または `/restart` を試し、再度確認します。

スキルは Copilot にワークフローを教え、MCP サーバーは Azure リソースの確認や操作を可能にします。

## 作業ブランチを準備する

前の演習で別の機能ブランチを作成してプッシュしている場合があります。エージェントの作業を分けるため、このオプションシリーズは最新の `main` から始めます。

1. シェルのターミナルで `main` に切り替えて最新の変更を取得し、Backer Concierge 用のブランチを作成します。

    ```bash
    git checkout main
    git pull
    git checkout -b foundry-agent-cli
    ```

## カタログをエクスポートする

エージェントは読み取り可能なファイル形式のカタログを必要とします。Tailspin Toys のサンプルには、テスト済みのエクスポートスクリプトが用意されています。

1. Copilot CLI に戻り、次を入力します。

    ```text
    Install the project dependencies, seed the database, then run the existing db:export script. Show me the command output and summarize the shape and grounding limits of db/catalog.json.
    ```

    Copilot は、次と同等のコマンドを実行するはずです。

    ```bash
    npm install
    npm run db:setup
    npm run db:export
    ```

    ![カタログのエクスポート結果の概要](../../../../_images/cli-8-export-db-catalog.png)

2. `db/catalog.json` を開きます。21 個のゲームが含まれ、それぞれにタイトル、説明、カテゴリ、publisher、星評価があることを確認します。`note` フィールドには、資金調達総額、支援者数、支援プラン、発売日がカタログに含まれないと記載されています。価格、プレイヤー数、プレイ時間のフィールドもありません。この情報の欠如がエージェントの回答範囲を定めます。

## Foundry での作業を計画する

Copilot が Azure リソースを作成したり、エージェントコードを追加したりする前に、プランモードで予定するワークフローを確認します。

1. 次のプロンプトを入力します。

    ```text
    /plan Use the Microsoft Foundry Skill to plan a Backer Concierge hosted agent for this existing Tailspin Toys repository. Use a public Foundry project, Python 3.13, Microsoft Agent Framework, the Responses API, the Basic sample, and code deployment. Keep the agent in agent/backer-concierge and keep one azure.yaml at the repository root. Ground every answer in db/catalog.json, preserve conversation context, and add focused tests. Include project setup, model selection, local testing, deployment, remote invocation, estimated cost-bearing resources and cleanup.
    ```

2. 提案された計画を確認します。`microsoft-foundry` スキルを使う予定であることと、ホスト型エージェントが既存の Astro アプリから分離されていることを確認します。懸念や予想外の内容があれば、続行する前に修正を依頼します。
3. 方針に納得できたら、プランモードを終了します。

## Foundry プロジェクトとモデルをセットアップする

エージェントには Foundry プロジェクトとデプロイ済みモデルが必要です。Microsoft Foundry Skill を使い、サブスクリプション内の最新の提供状況とクォータから選択します。

1. Copilot にプロジェクトの作成を依頼します。リソース作成を承認する前に、サブスクリプション、リージョン、クォータ、推定コストを確認します。

    ```text
    Use the Microsoft Foundry Skill to create a public Foundry project for this project. Use the resource group rg-tailspin-toys and project name tailspin-toys.
    ```

    ![パブリック Foundry プロジェクトを作成する](../../../../_images/cli-8-create-foundry-project.png)

2. プロジェクトの準備ができたら、モデルを提案するよう Copilot に依頼します。

    ```text
    Use the Microsoft Foundry Skill to recommend two or three current chat models available in the tailspin-toys project for the Backer Concierge acceptance criteria in the issue titled "Add a Backer Concierge assistant for catalog questions". Prioritize low latency, instruction following, grounding fidelity, available quota, and models that aren't approaching retirement. There is no complex math or multi-step planning. Explain the tradeoffs and wait for me to choose a model from the recommended options.
    ```

    Copilot から推奨された選択肢からモデルを選ぶよう求められることがあります。

    ![推奨された選択肢からモデルを選ぶ](../../../../_images/cli-8-select-foundry-model.png)

    以降の手順では `gpt-5.4-mini` を使いますが、提供状況とクォータはリージョンによって異なります。

3. 推奨された選択肢からモデルを選び、デプロイを依頼します。承認前に容量とコストを確認します。

    ```text
    Deploy the model we selected to the tailspin-toys Foundry project and use the model name as the deployment name. Choose an SKU with available quota, ask me to confirm the capacity before deployment. After deployment, show me the deployment status.
    ```

    ![選択したモデルをデプロイする](../../../../_images/cli-8-deploy-foundry-model.png)

> [!TIP]
> モデルの提供状況は変化します。例に固定されたモデルではなく、プロジェクトで利用可能だと Copilot が確認したモデルを選びます。

## デプロイ済みモデルをテストする

ホスト型エージェントを構築する前に、モデルが Backer Concierge のグラウンディング規則に従うかテストします。エージェントコードや構成は使わず、予定する指示とカタログのコンテキストを使います。

サインイン中のアカウントに、モジュール 2 のホスト型エージェント開発用 **Foundry Project Manager** ロールと、モデルを直接呼び出す **Cognitive Services OpenAI User** ロールを付与します。その後、カタログにない情報を含む質問をします。

1. 新しいターミナルを開き、アカウント、プロジェクト、ユーザーの値を設定します。`<foundry-account-name>` は、プロジェクト作成時に表示された Foundry アカウント名に置き換えます。

    ```bash
    SUBSCRIPTION_ID=$(az account show --query id --output tsv)
    USER_OBJECT_ID=$(az ad signed-in-user show --query id --output tsv)
    FOUNDRY_ACCOUNT="<foundry-account-name>"
    ACCOUNT_SCOPE=$(az cognitiveservices account show --name "$FOUNDRY_ACCOUNT" --resource-group rg-tailspin-toys --query id --output tsv)
    PROJECT_SCOPE="$ACCOUNT_SCOPE/projects/tailspin-toys"
    ```

2. **Foundry Project Manager** ロールを割り当てます。

    ```bash
    az role assignment create \
       --assignee-object-id "$USER_OBJECT_ID" \
       --assignee-principal-type User \
       --role "Foundry Project Manager" \
       --scope "$PROJECT_SCOPE" \
       --subscription "$SUBSCRIPTION_ID"
    ```

3. **Cognitive Services OpenAI User** ロールを割り当てます。

    ```bash
    az role assignment create \
       --assignee-object-id "$USER_OBJECT_ID" \
       --assignee-principal-type User \
       --role "Cognitive Services OpenAI User" \
       --scope "$ACCOUNT_SCOPE" \
       --subscription "$SUBSCRIPTION_ID"
    ```

4. Copilot CLI に戻り、次を入力します。

    ```text
    Use the Microsoft Foundry Skill to test my deployed model directly in the tailspin-toys project without creating an agent. Ground it with content from @db/catalog.json and ask: "I love puzzle games about tracking down bugs. What should I back, and how much funding has it raised?" Show me the response and useful metadata like tokens used and response time (only if you can obtain it). Do not change files or create resources.
    ```

    ![カタログに実在するゲームを勧め、資金調達情報がないことを伝える Foundry モデルの回答](../../../../_images/cli-8-foundry-agent-response.png)

5. 回答を確認します。カタログに実在するゲームだけを勧め、正確な詳細を使い、資金調達情報がないことを説明している必要があります。タイトル、ゲームの詳細、資金調達総額を作り上げた場合は、続行前に別の推奨モデルでも試します。

> [!NOTE]
> これは、一時的な指示とカタログのコンテキストを使ってデプロイ済みモデルのみをテストする手順です。エージェントのテストではありません。モジュール 2 ではひな形を作成した後にテストを繰り返し、ホスト型エージェントのコード、パッケージ、会話の動作を確認します。

## まとめと次のステップ

Azure ツールを準備し、カタログをエクスポートして、デプロイ済みモデルが Backer Concierge のグラウンディング規則に従うかテストしました。このモジュールのチェックポイントは、不足情報を作り上げずにカタログに実在するゲームを勧められるモデルです。

次は、同じリポジトリ、`foundry-agent-cli` ブランチ、Copilot CLI セッション、Foundry プロジェクト、選択したモデルデプロイを使い、[エージェントを構築してデプロイします][next-lesson]。ここで終了する場合は、継続的なコストを避けるために [Azure リソースをクリーンアップしてください][cleanup]。

[overview]: ../
[next-lesson]: ../2-build-and-deploy/
[cleanup]: ../#リソースをクリーンアップする
[azure-free]: https://azure.microsoft.com/pricing/purchase-options/azure-account
[azure-students]: https://azure.microsoft.com/free/students
[install-azd]: https://learn.microsoft.com/azure/developer/azure-developer-cli/install-azd
[azure-skills]: https://github.com/microsoft/azure-skills#github-copilot-cli
