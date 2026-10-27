---
title: "演習 1 - カスタム指示 (Cloud agent)"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 前の演習: 前提条件][previous-lesson] · [次の演習: Copilot cloud agent →][next-lesson]

生成 AI を使うときは、コンテキストが重要です。タスクを特定の方法で進める必要がある場合や、Copilot が知っておくべき背景情報がある場合は、その情報を参照できるようにします。[指示ファイル][instruction-files]を使うと、Copilot に何を実行するかだけでなく、どのように実行するかも伝えられます。

この演習では、次のことを行います。

- リポジトリのカスタム指示とパスを指定した指示ファイルを通じて、プロジェクト固有のコンテキスト、コーディングガイドライン、ドキュメント標準が Copilot に伝わる仕組みを確認します。
- `.github/copilot-instructions.md` にリポジトリ全体に適用する標準を追加します。

> [!NOTE]
> VS Code および CLI のハーネスとは異なり、ここでは変更前後のプロンプトを実行しません。Copilot cloud agent は GitHub Issue に対して非同期に動作するため、影響をリアルタイムで並べて示すのは困難です。このハーネスの後半で、cloud agent が作成したプルリクエストをレビューするときに、指示ファイルの効果を確認します。

## 指示ファイル

### シナリオ

Tailspin Toys には、開発手法に関するガイドラインと要件があります。

- データ層には必ずユニットテストを用意します。
- ユーザーインターフェイス (UI) はダークモードで、現代的な見た目にします。
- コードには TypeScript documentation (TSDoc) doc comment 形式のドキュメントを追加します。
- 各ファイルの先頭に、そのファイルの役割を説明するコメントブロックを追加します。

指示ファイルを使い、Copilot がこうした開発手法に沿ってタスクを実行できるようにします。

### カスタム指示

カスタム指示を使うと、コーディングスタイルや要件を Copilot に伝え、より適切な提案やコードを生成できるようにします。好みのコーディング規約、ライブラリ、コメントの形式などを指定できます。リポジトリ全体に適用する指示も、特定のファイル種別に適用する指示も作成できます。

指示ファイルには次の2種類があります。

- `.github/copilot-instructions.md` は、リポジトリに対する**すべての**要求で Copilot に送信される単一の指示ファイルです。技術スタック、開発内容の概要、ベストプラクティスなど、ほとんどのチャットや CLI 要求に関係するプロジェクトレベルの情報を記載します。
- `.github/instructions/*.instructions.md` は、特定のタスクやファイル種別に対する指針を記載します。TypeScript や Astro などの言語、UI コンポーネントの作成、新しいテストの作成などを対象にできます。

> [!NOTE]
> IDE では、指示ファイルは Copilot Chat のコード生成にのみ使われ、コード補完や次の編集候補には使われません。
>
> Copilot Chat、Copilot CLI、Copilot cloud agent は、リポジトリレベルの指示と `applyTo` フロントマターを持つ `*.instructions.md` ファイルの両方をコード生成に使います。
>
> また、Copilot は AGENTS.md や CLAUDE.md など、ほかの規格の指示ファイルにも対応しています。詳細は[対応する指示ファイルの形式][custom-instructions-support]を参照してください。

### 指示ファイルを管理するためのベストプラクティス

指示ファイルの作成方法をすべて説明することは、このワークショップの範囲外です。サンプルプロジェクトの例から、代表的な方法を確認できます。

- `copilot-instructions.md` には、開発する内容、プロジェクト構成、全体的なコーディング標準など、プロジェクト全般の指針を記載します。
- `*.instructions.md` には、ユニットテスト、Astro コンポーネント、データ層など、特定のファイル種別やタスク向けの指示を記載します。
- 自然な言葉で明確に記述し、コードの良い例と避けるべき例を示します。

指示ファイルや AI の使い方に唯一の正解はありません。プロジェクトに合う方法を試しながら見つけてください。

> [!TIP]
> GitHub Copilot を使うプロジェクトでは、充実した指示ファイルを用意しましょう。このプロジェクトにも [UI の更新][ui-instructions]や [Astro][astro-instructions]など、さまざまなタスク向けのファイルがあります。
>
> Copilot に指示ファイルを作成させることもできます。VS Code では **Configure Chat → Generate Agent Instructions**、Copilot CLI では `/init` など、利用する環境によって方法が異なります。必要な場合は各ハーネスの説明を確認してください。
>
> テンプレートやひな形を探している場合は、指示ファイル、カスタムエージェント、その他のリソースをまとめた [awesome-copilot][awesome-copilot] を確認してください。

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[astro-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/astro.instructions.md
[awesome-copilot]: https://github.com/github/awesome-copilot
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support

## このプロジェクトのカスタム指示ファイルを確認する

このリポジトリに含まれる指示ファイルを確認します。中心となる `copilot-instructions.md` と、タスク別の `*.instructions.md` ファイルがあります。エディターまたは GitHub の Web UI で開きます。

1. `.github/copilot-instructions.md` を開きます。
2. プロジェクトの簡単な説明や **Agent notes**、**Code standards**、**Scripts**、**Repository Structure** などのセクションを確認します。**Code standards** 内にある **GitHub Actions Workflows** の指針も確認してください。これらは Copilot とのあらゆるやり取りに適用されます。
3. `.github/instructions` フォルダーを開き、Astro ファイル、Drizzle データ層、テストなどの指示を確認します。
4. `.github/instructions/unit-tests.instructions.md` を開き、先頭の `applyTo` フィールドを確認します。このフィールドはリポジトリルートからの glob を指定します。この例では、`**/*.test.ts` に一致する TypeScript テストファイルに指示が適用されます。
5. このプロジェクトのユニットテスト作成に関する指示を確認します。
6. `.github/instructions/drizzle.instructions.md` を開き、末尾にある他の指示ファイル (`unit-tests.instructions.md` など) やプロジェクト内ファイルへのリンクを確認します。これにより、大きな指示を再利用可能な小さなファイルに分け、コード生成の参考例を指定できます。ここにあるパスはリポジトリルートではなく、指示ファイルからの相対パスです。

> [!NOTE]
> `copilot-instructions.md` の **Code formatting requirements** セクションにはプロジェクトのコーディング標準が記載されていますが、コード内のドキュメントに関する指示はありません。次の手順で TSDoc doc comment とファイル先頭コメントの規則を追加します。

## リポジトリ全体の標準を追加する

ここで行う編集は、コード内のドキュメントを TSDoc doc comment とファイルレベルのコメントブロックで記述するという、プロジェクト全体のルールを追加することです。cloud agent がこのハーネスの後半で Issue に取り組むときに、この指示を参照します。

編集の前に作業ブランチを作成します。cloud agent は Issue の対象ブランチから指示を読み取りますが、ここでは変更を整理してコミットします。

1. codespace のターミナルで、新しいブランチを作成して切り替えます。

   ```bash
   git checkout -b custom-instructions-cloud
   ```

`.github/copilot-instructions.md` は Copilot にプロジェクト全体の情報を提供するファイルです。ここにコーディング標準を記載して、コード提案の精度を高めます。

1. `.github/copilot-instructions.md` を開き直します。
2. 27行目付近にある **Code formatting requirements** セクションを探します。現在のコーディング標準は記載されていますが、コード内ドキュメントの規則はありません。
3. 既存の標準の直後に、次の Markdown を追加します。

   ```markdown
   - Every exported function should have a TSDoc comment describing its purpose, parameters, and return value.
   - Before imports or any code, add a comment block to the file that explains its purpose.
   ```

4. `copilot-instructions.md` を保存します。

> [!TIP]
> 前の演習で確認したように、指示ファイルはリポジトリレベル (`.github/copilot-instructions.md`) に置いて全体に適用することも、言語、ファイル種別、タスクごとの `*.instructions.md` ファイルに記載することもできます。今回追加した doc comment の規則のようなプロジェクト全体の標準は、リポジトリレベルのファイルに適しています。

## 指示の更新をコミット、プッシュ、マージする

cloud agent は、Issue が対象とするブランチから指示を読み込みます。次の演習で Copilot に Issue を割り当てると、`main` からブランチが作成されるため、指示の変更を `main` に取り込む必要があります。

1. 変更をステージしてコミットします。

   ```bash
   git add .github/copilot-instructions.md
   git commit -m "Add doc comment and file-header standards to copilot instructions"
   ```

2. ブランチをプッシュします。

   ```bash
   git push -u origin custom-instructions-cloud
   ```

3. github.com で `custom-instructions-cloud` から `main` へのプルリクエストを開き、マージします。これで次の演習で Issue に取り組む際、cloud agent がこの指示を読み取れるようになります。

> [!TIP]
> このワークショップで `main` に直接作業する場合は、ブランチを作成せず `main` に直接コミットしてもかまいません。ここでは実際のプロジェクトでの作業に近づけるためにブランチを使います。

## まとめと次のステップ

このプロジェクトの指示ファイルが Copilot にコンテキストを与える仕組みを確認し、`.github/copilot-instructions.md` にリポジトリ全体の標準を追加しました。このハーネスの残りで cloud agent が作成するプルリクエストをレビューするときに、この標準が適用されたか確認できます。

次は[最初の Issue を Copilot cloud agent に割り当てます][next-lesson]。

## リソース

- [GitHub Copilot のカスタマイズに使う指示ファイル][instruction-files]
- [カスタム指示を作成するためのベストプラクティス][instructions-best-practices]
- [Copilot 向けのカスタム指示を改善する5つのヒント][copilot-instructions-five-tips]
- [awesome-copilot — 指示ファイルなどのリソース集][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-cloud-agent/
[instruction-files]: https://docs.github.com/copilot/customizing-copilot/about-customizing-github-copilot-chat-responses
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
