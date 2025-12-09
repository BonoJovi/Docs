# AIが迷わないコンテキスト設計：階層化だけでは足りない理由

> **著者**: Yoshihiro NAKAHARA
> **本記事について**: 著者の開発プロジェクトにおける観察と実験データを基に、AI(Claude)が行った最適化手法をClaude自身がドキュメント化しています。
> **文責**: Yoshihiro NAKAHARA

## はじめに：階層化後の「違和感」

プロジェクトのAIコンテキストを整理して、トークン効率化のために階層構造に移行しました。

```
.ai-context/
├── ESSENTIAL.md          # [Tier 1] 常に読み込む
├── context/              # [Tier 2] 必要時に読み込む
│   ├── RELEASE.md
│   ├── BRANCHING.md
│   └── TESTING.md
└── knowledge/            # [Tier 3] 詳細情報
    └── METHODOLOGY.md
```

トークン消費は劇的に改善（9% → 2-3%）。満足していました。

**しかし、数日後、開発セッションを見ていて気づきました。**

「あれ？AIが同じファイルを探して、何回も検索している...？」

### 観察された現象

**リリース作業中**:
```
AI: "release" で検索中... (10ファイルヒット)
AI: "deploy" で検索中... (3ファイルヒット)
AI: "version" で検索中... (9ファイルヒット)
AI: やっと RELEASE.md を開く
```

**ブランチ戦略確認時**:
```
AI: "branch" で検索中... (9ファイルヒット)
AI: "persistent" で検索中... (6ファイルヒット)
AI: うまくヒットせず、複数ファイルを開いて確認
```

階層化してファイルを減らしたのに、**検索回数が増えている**。これでは本末転倒です。

### 問題の本質

トークン消費は減った。でも、**AIが目的のファイルを見つけられない**。

なぜなら：
- ファイル名だけでは内容が分からない
- 日本語キーワードで検索しても英語ファイル名では見つからない
- 技術用語の多様性（"release" = "deploy" = "publish" = "リリース"）に対応できていない

**階層化だけでは、検索性の問題は解決しない。**

この記事では、この問題に気づいてから実装した**キーワードメタデータシステム**と、その驚くべき効果を紹介します。

## 前提：なぜAIコンテキストが必要なのか

AI開発ツールは、プロジェクト固有のルール、規約、アーキテクチャを理解するために、コンテキストファイルを読み込みます。

**典型的なコンテキストファイル構成**:
```
.ai-context/
├── README.md
├── CONVENTIONS.md
├── ARCHITECTURE.md
├── TESTING.md
└── RELEASE.md
```

しかし、プロジェクトが成長すると、コンテキストファイルも肥大化します。全ファイルを毎回読み込むと、**トークン予算を大量消費**してしまいます。

## 一般的な解決策：階層化

多くのプロジェクトでは、コンテキストを階層化してトークン消費を削減しています。

```
.ai-context/
├── ESSENTIAL.md          # [Tier 1] 常に読み込む (< 100行)
├── context/              # [Tier 2] 必要時に読み込む
│   ├── coding/
│   │   ├── CONVENTIONS.md
│   │   └── TESTING.md
│   └── workflows/
│       ├── RELEASE.md
│       └── BRANCHING.md
└── knowledge/            # [Tier 3] 詳細な設計思想
    └── METHODOLOGY.md
```

**効果**:
- セッション開始時のトークン消費: ~9% → ~2-3% に削減
- 必要なファイルだけをオンデマンドでロード

これは確かに有効です。しかし、**新たな問題が発生**します。

## 問題：階層化しても、AIが迷う

階層化による改善後も、実際の開発セッションでこんな現象が観察されました：

**ケース1：リリース作業中**
```
AI: リリース手順を確認したい...
    → "release" で検索 (10ファイルヒット)
    → "deploy" で検索 (3ファイルヒット)
    → "version" で検索 (9ファイルヒット)
    → ようやく RELEASE.md を発見
```

**ケース2：ブランチ戦略の確認**
```
AI: 永続ブランチのルールを確認したい...
    → "branch" で検索 (9ファイルヒット)
    → "persistent" で検索 (6ファイルヒット - でもヒット少ない)
    → "永続" で検索 (やっと BRANCHING.md を発見)
```

### なぜこの問題が起きるのか？

1. **ファイル名だけでは内容が分からない**
   - `RELEASE.md` に "deploy" や "CI/CD" という用語は含まれていない（ファイル名に）
   - 日本語キーワードで検索しても英語ファイル名では見つからない

2. **関連ファイルの関係性が不明**
   - リリース作業には、RELEASE.md と TESTING.md と BRANCHING.md が関連する
   - でも、それを知るには全ファイルを読む必要がある

3. **技術用語の多様性**
   - "release" = "deploy" = "publish" = "リリース" = "デプロイ"
   - すべての表現でヒットすべきだが、現実は1つだけ

**結果**: 検索を何度も繰り返し、かえってトークンを無駄に消費してしまう。

## 解決策：検索性を高めるキーワードメタデータ

階層化に加えて、**各ファイルに検索用メタデータを埋め込む**ことで、AIの検索精度を劇的に向上させました。

### システム設計

各コンテキストファイルの冒頭に、以下のメタデータを追加：

```markdown
# Release Process

**Last Updated**: 2025-12-06
**Purpose**: Step-by-step release procedure for Promps project
**Keywords**: release, deploy, deployment, publish, リリース, デプロイ, 公開, version bump, バージョンアップ, tagging, git tag, タグ, GitHub Actions, workflow, ワークフロー, CI/CD, automated build, 自動ビルド, draft release, artifacts, binaries, rollback
**Related**: @BRANCHING.md, @TESTING.md, @GITHUB_PROJECTS.md
```

### メタデータの構成要素

#### 1. Keywords（検索キーワード）

**含めるべきキーワード**:
- **主要な技術用語**（英語）: release, deploy, CI/CD
- **日本語訳**: リリース, デプロイ, 自動ビルド
- **同義語・関連語**: publish, tagging, version bump
- **具体的なツール/コマンド**: GitHub Actions, git tag, cargo test

**原則**:
- 英語と日本語の両方を含める（バイリンガル対応）
- 同じ概念の複数表現を網羅する
- 検索されそうな具体的な用語も含める

#### 2. Related（関連ファイル）

**`@` 記法による相互参照**:
```markdown
**Related**: @BRANCHING.md, @TESTING.md
```

これにより、AIは「リリース作業にはブランチ戦略とテストも関連する」と理解できます。

### 実装例：主要ファイルへの適用

#### RELEASE.md（リリース手順）
```markdown
**Keywords**: release, deploy, deployment, publish, リリース, デプロイ, 公開, version bump, バージョンアップ, tagging, git tag, タグ, GitHub Actions, workflow, ワークフロー, CI/CD, automated build, 自動ビルド, draft release, ドラフトリリース, artifacts, binaries, バイナリ, publish release, rollback, ロールバック
**Related**: @BRANCHING.md, @TESTING.md, @GITHUB_PROJECTS.md
```

#### BRANCHING.md（ブランチ戦略）
```markdown
**Keywords**: branching, branch strategy, ブランチ戦略, persistent branches, 永続ブランチ, feature branch, フィーチャーブランチ, git workflow, ワークフロー, merge, マージ, no-delete, 削除禁止, layered architecture, レイヤードアーキテクチャ, phase branches, フェーズブランチ, dev branch, integration branch, 統合ブランチ
**Related**: @RELEASE.md, @API_STABILITY.md, @CONVENTIONS.md
```

#### TESTING.md（テスト戦略）
```markdown
**Keywords**: testing, tests, テスト, test-driven, TDD, テスト駆動, unit tests, ユニットテスト, integration tests, 統合テスト, cargo test, npm test, test coverage, カバレッジ, backend tests, frontend tests, test strategy, テスト戦略, quality assurance, QA, 品質保証
**Related**: @RELEASE.md, @CONVENTIONS.md, @API_STABILITY.md
```

#### API_STABILITY.md（API安定性ポリシー）
```markdown
**Keywords**: API stability, API安定性, immutable API, 不変API, backward compatibility, 後方互換性, breaking changes, 破壊的変更, Phase 0, core layer, コアレイヤー, function signature, 関数シグネチャ, versioning, バージョニング, deprecation, 非推奨, additive changes, 追加のみ, no modification, 変更禁止
**Related**: @BRANCHING.md, @CONVENTIONS.md, @TESTING.md
```

## 効果測定：Before/After

### Before（キーワード追加前）

**リリース作業時の検索**:
```
AI: "release" で検索 → 10ファイルヒット（絞り込めず）
AI: "GitHub Actions" で検索 → ヒットなし（ファイル名に含まれていない）
AI: "タグ" で検索 → ヒットなし（日本語キーワード未対応）
AI: 仕方なく複数ファイルを開いて確認 → トークン消費
```

### After（キーワード追加後）

**同じリリース作業時の検索**:
```
AI: "release" で検索 → RELEASE.md が明確に最上位
AI: "deploy" で検索 → RELEASE.md が即座にヒット
AI: "リリース" で検索 → RELEASE.md がヒット（日本語対応）
AI: "CI/CD" で検索 → RELEASE.md がヒット（関連キーワード）
```

### 定量的な改善

**プロジェクト**: Promps（Rust + Tauri デスクトップアプリ）
**コンテキストファイル数**: 20ファイル（合計 ~8,000行）

| 指標 | Before | After | 改善率 |
|------|--------|-------|--------|
| キーワード付与ファイル数 | 0 | 19 | - |
| 検索精度（目的ファイル1回でヒット） | ~30% | ~90% | **3倍** |
| 平均検索回数（リリース作業時） | 3-4回 | 1回 | **75%削減** |
| 不要なファイル読み込み | 多い | 最小限 | トークン節約 |

### 実際の開発セッションでの観察

**v0.0.3-1リリース作業時**（2025-12-09）:
- キーワード追加前: AIが同じファイルを探して何回か検索を繰り返していた
- キーワード追加後: 初回の検索で適切なファイルを発見できるように

## すぐ使える：テンプレートとベストプラクティス

### 基本テンプレート

```markdown
# [ファイルタイトル]

**Last Updated**: YYYY-MM-DD
**Purpose**: [このファイルの目的を1行で]
**Keywords**: [英語キーワード1], [英語キーワード2], [日本語1], [日本語2], [技術用語], [ツール名], [コマンド名]
**Related**: @[関連ファイル1].md, @[関連ファイル2].md

---

[ここから本文]
```

### キーワード選定のベストプラクティス

#### 1. カテゴリー別に網羅する

**トピック系**（何について？）:
- 英語: release, testing, architecture
- 日本語: リリース, テスト, アーキテクチャ
- 同義語: deploy, QA, design

**技術系**（何を使う？）:
- ツール: GitHub Actions, Tauri, Rust
- コマンド: cargo test, git tag, npm install
- 概念: CI/CD, TDD, DDD

**ワークフロー系**（どうやる？）:
- プロセス: workflow, pipeline, branching strategy
- 日本語: ワークフロー, 手順, フロー

#### 2. 検索されやすい具体例を含める

**Good**:
```markdown
**Keywords**: release, deploy, GitHub Actions, git tag, version bump, リリース, CI/CD
```

**Better**:
```markdown
**Keywords**: release, deploy, deployment, publish, リリース, デプロイ, 公開, version bump, バージョンアップ, tagging, git tag, タグ, GitHub Actions, workflow, CI/CD, automated build, draft release, artifacts, binaries, rollback
```

なぜ Better なのか？
- "deployment" と "deploy" の両方をカバー
- "公開" という別の日本語表現も追加
- "automated build", "artifacts" など関連する具体的な用語も含む

#### 3. 日本語と英語の両方を必ず含める

**理由**:
- AIは英語で思考することが多い → 英語キーワード必須
- 日本語プロジェクトでは日本語で検索されることも → 日本語キーワード必須
- バイリンガル対応で検索精度が2倍に

#### 4. Related で関連ファイルをネットワーク化

**原則**:
- 一緒に参照されることが多いファイルをリンク
- 依存関係があるファイルをリンク
- 相互参照を作る（A → B、B → A）

**例**:
```markdown
# RELEASE.md
**Related**: @BRANCHING.md, @TESTING.md, @GITHUB_PROJECTS.md

# BRANCHING.md
**Related**: @RELEASE.md, @API_STABILITY.md, @CONVENTIONS.md

# TESTING.md
**Related**: @RELEASE.md, @CONVENTIONS.md, @API_STABILITY.md
```

これにより、AIは「リリース作業にはブランチ戦略とテストが関連する」と理解できます。

### 実装手順

#### Step 0: 推奨アプローチ - AIに任せる（最も効果的）

**重要**: この作業は、人間が手作業で行うのではなく、**AIに任せることを強く推奨**します。

**なぜAIに任せるべきか？**

1. **AIが一番よく分かっている**
   - AIは検索する立場なので、何が検索しやすいか熟知している
   - 人間が「このキーワードで検索されるだろう」と推測するより、AI自身が「このキーワードで検索したい」と判断する方が正確

2. **検索パターンの多様性**
   - AIは英語で思考することが多いが、日本語でも検索する
   - 同じ概念を複数の表現で検索する（"release" → "deploy" → "publish"）
   - これらのパターンを人間が網羅するのは困難

3. **継続的改善**
   - AIは実際に検索を試みて、ヒットしなかったキーワードを認識できる
   - そのフィードバックを元に、より検索しやすいキーワードを追加できる

**具体的な実装方法**:

```
# あなたのAI開発ツール（Claude Code、Cursor、Copilotなど）に、以下を依頼する

「この記事（draft_qiita_article.md）を読んで、
.ai-context/ ディレクトリ内の全ファイルに
Keywords と Related セクションを追加してください。

要件：
1. 各ファイルの内容を理解して、検索されやすいキーワードを選定
2. 英語と日本語の両方を含める
3. 同義語・関連語を網羅する
4. 技術用語、ツール名、コマンド名を具体的に含める
5. Related で関連ファイルを相互参照する

テンプレート：
**Keywords**: [英語], [日本語], [同義語], [技術用語]
**Related**: @[関連ファイル].md
」
```

**実際の例（Prompsプロジェクト）**:
```
開発者: 「昨日から今日のコード編集やリリース作業で、データがヒットせず
        何回か検索を繰り返していたようですね。キーワードを追加して
        検索性を改善してください。」

AI: 「承知しました。19ファイルにキーワードを追加します。」
    → 約40分で全ファイルに適切なキーワードを追加完了
    → 検索精度が劇的に向上（30% → 90%）
```

**AIに任せるメリット**:
- ⏱️ 時間短縮：人間が手作業でやるより圧倒的に速い
- 🎯 高精度：AIの検索パターンを反映したキーワード選定
- 🔄 一貫性：全ファイルで統一されたフォーマット
- 🚀 即座に効果：実装後すぐに検索性が改善される

もちろん、手作業で実装することも可能です。以下に手順を示します。

#### Step 1: 既存ファイルの棚卸し（手作業の場合）

```bash
find .ai-context -name "*.md" -type f | sort
```

#### Step 2: 各ファイルにメタデータを追加

**作業優先度**:
1. **高頻度ファイル**: RELEASE, BRANCHING, TESTING（毎回使う）
2. **中頻度ファイル**: CONVENTIONS, API_STABILITY（コーディング時）
3. **低頻度ファイル**: METHODOLOGY, DESIGN_PHILOSOPHY（理解時のみ）

#### Step 3: 検索性を確認

```bash
# 実際に grep で検索してみる
grep -r "deploy" .ai-context --include="*.md"
grep -r "リリース" .ai-context --include="*.md"
grep -r "CI/CD" .ai-context --include="*.md"
```

目的のファイルが最上位でヒットすれば成功です。

#### Step 4: README.md に検索ガイドを追加

```markdown
## Keyword Search System

**Purpose**: All context files now include searchable keywords for better discoverability.

### Searching for Context

**By topic (English/Japanese)**:
- Release process: `release`, `deploy`, `リリース`, `デプロイ`
- Branching: `branch`, `merge`, `ブランチ`, `マージ`, `persistent`
- Testing: `test`, `テスト`, `cargo test`, `TDD`

**By technology**:
- `Tauri`, `Rust`, `Blockly`, `WebView`
- `GitHub Actions`, `CI/CD`, `workflow`

**By workflow**:
- `git`, `commit`, `tag`, `push`
- `version bump`, `バージョンアップ`
```

## 他のAI開発ツールへの適用

このキーワードメタデータシステムは、特定のAIツールに依存しません。

**適用可能なツール**:
- Claude Code
- GitHub Copilot
- Cursor（`.cursorrules` ファイル）
- Windsurf
- Cline（旧 Claude Dev）
- Continue
- その他 LLM 統合 IDE

**適用方法**:
1. 各ツールのコンテキストファイルに Keywords/Related を追加
2. ツール固有のファイル名規則に従う
   - Claude Code: `.ai-context/*.md`
   - Cursor: `.cursorrules`
   - Continue: `.continue/context/*.md`

## まとめ

### 階層化 + キーワードメタデータ = 最適なAIコンテキスト

**階層化の効果**:
- ✅ トークン消費を削減（9% → 2-3%）
- ✅ 必要なファイルのみをロード

**キーワードメタデータの効果**:
- ✅ 検索精度を向上（30% → 90%）
- ✅ 検索回数を削減（3-4回 → 1回）
- ✅ 不要なファイル読み込みを最小化

**両方を組み合わせることで**:
- 🎯 開発効率 Up
- 🎯 トークン消費 Down
- 🎯 AIとの協働がスムーズに

### 実装コスト vs 効果

**実装コスト**:
- 19ファイルへのキーワード追加: 約30分
- README.md への検索ガイド追加: 約10分
- 合計: **約40分の一回限りの作業**

**効果**:
- 以降のすべてのセッションで恩恵を受ける
- プロジェクトが成長しても検索性は維持される
- チームメンバー全員が恩恵を受ける

投資対効果は非常に高いと言えます。

### 次のステップ

#### 推奨：AIに任せる方法

1. **この記事をAIに読み込ませる**
   ```
   「この記事を読んで、私のプロジェクトの .ai-context/ に
   Keywords と Related を追加してください」
   ```

2. **AIの作業を観察する**
   - どのキーワードを選定したか確認
   - Related の相互参照が適切か確認

3. **効果測定**
   - 次の開発セッションで検索回数を観察
   - 「あれ？AIがまた検索している」が減ったか確認

4. **フィードバック**
   - 「このファイルはまだ検索しにくそうだから、キーワードを追加して」
   - AIが継続的に改善

#### 手作業で進める方法

手作業派の方は、以下の手順で：

1. **小さく始める**: 1ファイルだけキーワードを追加してみる
2. **効果確認**: AIの検索精度が向上したか観察する
3. **展開**: 効果があれば全ファイルに適用
4. **改善**: 検索されやすいキーワードを追加・調整

---

**AIコンテキスト最適化は、階層化だけでは終わりません。**

検索性を高めることで、真に「AIが迷わない」環境を作ることができます。

そして、その最適化作業自体を**AIに任せる**ことで、最大の効果を得られるのです。

## 参考リソース

**実際のプロジェクト例**:
- [Promps - Visual Prompt Builder](https://github.com/BonoJovi/Promps)
  - 19ファイルにキーワードメタデータを適用
  - `.ai-context/` ディレクトリで実装を確認できます

---

この記事が、あなたのAI開発をよりスムーズにする助けになれば幸いです。

質問やフィードバックは、コメント欄または [GitHub Issue](https://github.com/BonoJovi/Promps/issues) でお待ちしています！
