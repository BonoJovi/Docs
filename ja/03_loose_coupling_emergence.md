# レイヤードアーキテクチャで粗結合が勝手に生まれた話

## はじめに

前回の記事では、プロジェクト規模に応じたアーキテクチャ選択の判断基準を説明しました。

今回は、レイヤードアーキテクチャで**なぜ粗結合が「自動的に」生まれるのか**、その仕組みを詳しく解説します。

---

## 粗結合は「目標」ではなく「結果」

まず重要な発見から。

**粗結合は設計の目標ではなく、創発特性（Emergent Property）である**

これはどういう意味でしょうか？

### 従来の理解

```
設計目標: 粗結合を達成したい
  ↓
手法: インターフェース、依存性注入、疎な参照...
  ↓
結果: 粗結合（意識的な努力の結果）
```

### Prompsでの発見

```
設計目標: レイヤードアーキテクチャ + 非破壊拡張
  ↓
手法: 下位レイヤーを変更しない
  ↓
結果: 粗結合（勝手に生まれた！）
```

**粗結合を「目指して」いなかったのに、結果として得られた**のです。

---

## 非破壊拡張原則とは

### 原則の定義

```
Phase N（新機能）の実装 = Phase 0（コア）の上に追加する
                    ≠ Phase 0を修正する
```

これは**Open-Closed Principle**（開放閉鎖原則）の実践です。

```
Open for extension（拡張に対して開いている）
Closed for modification（修正に対して閉じている）
```

### 具体例: Prompsでの実装

**Phase 0（コアレイヤー）- 変更しない**

```rust
// src/lib.rs (Phase 0)
pub fn parse_input(input: &str) -> Vec<PromptPart> {
    // 元の実装
    // 変更不要
}

pub fn generate_prompt(parts: &[PromptPart]) -> String {
    // 元の実装
    // 変更不要
}
```

**Phase N（検証レイヤー）- 新規追加**

```rust
// src/modules/validation.rs (Phase N)
use crate::{parse_input, PromptPart};

pub fn parse_input_checked(input: &str) -> Result<Vec<PromptPart>, ValidationError> {
    // 1. Phase 0のコアを再利用（修正なし）
    let parts = parse_input(input);

    // 2. 上に検証を追加
    validate_pattern(&parts)?;
    validate_noun_relationships(&parts)?;

    // 3. 検証済み結果を返す
    Ok(parts)
}

fn validate_pattern(parts: &[PromptPart]) -> Result<(), ValidationError> {
    // パターンマッチングロジック
}

fn validate_noun_relationships(parts: &[PromptPart]) -> Result<(), ValidationError> {
    // 関係性チェックロジック
}
```

### 重要なポイント

```
Phase 0: parse_input()は一切変更されていない
Phase N: parse_input()を「使う」だけ
  ↓
Phase 0に触れない = Phase 0は変わらない
  ↓
Phase 0に依存する他のコード = 壊れない
```

---

## なぜ粗結合が自動的に生まれるのか

### 1. 一方向依存の強制

```
Phase N → Phase 0 (呼び出しのみ)
Phase 0 ← Phase N (変更なし)
```

**従来の開発（密結合）**:
```rust
// 密結合の例
fn process_data(data: &mut Data) {
    validate(data);      // validateはdataを変更する？
    transform(data);     // transformはdataを変更する？
    save(data);          // saveはdataを変更する？
}
// → 関数が相互に影響し合う
```

**レイヤードアーキテクチャ（粗結合）**:
```rust
// Phase 0: コア（不変）
pub fn parse_input(input: &str) -> Vec<PromptPart> {
    // 純粋関数: 入力 → 出力のみ
}

// Phase N: 検証（上位レイヤー）
pub fn parse_input_checked(input: &str) -> Result<Vec<PromptPart>, ValidationError> {
    let parts = parse_input(input);  // Phase 0を「使う」だけ
    validate_pattern(&parts)?;        // Phase N独自のロジック
    Ok(parts)
}
// → Phase 0は触られない = 粗結合
```

### 2. 安定したインターフェース

```
Phase 0 API = 契約
  ↓
上位レイヤーはこの契約に依存
  ↓
契約が変わらない = 低い結合度
```

**例**:
```rust
// この契約は不変
pub fn parse_input(input: &str) -> Vec<PromptPart>

// 上位レイヤー（Phase 1, 2, 3...）はこれに依存
let parts = parse_input(input);  // どこでも同じ

// Phase 0が変わらない → 上位レイヤーも壊れない
```

### 3. 関心の分離

```
Phase 0: パース処理（DSL → データ構造）
Phase N: 検証処理（データ構造 → エラーチェック）
Phase N+1: ファイルI/O（データ構造 → JSON）
  ↓
各レイヤーは1つのことだけをする（Single Responsibility Principle）
  ↓
変更がローカル = 粗結合
```

---

## 粗結合の実証: モジュール分離が簡単

### 1. 自然なファイル分割

```
src/
├── lib.rs              # Phase 0（コア）
├── modules/
│   ├── validation.rs   # Phase N
│   ├── serialization.rs # Phase N+1
│   └── layout.rs       # Phase N+2
```

**各Phase = 独立したファイル → 直接的にモジュールになる**

### 2. テストの独立性

```rust
// Phase 0のテスト（Phase Nに依存しない）
#[test]
fn test_parse_input() {
    let result = parse_input("_N:User");
    assert_eq!(result.len(), 1);
}

// Phase Nのテスト（Phase 0を使うが、テストはPhase Nのみ）
#[test]
fn test_validate_pattern() {
    let parts = parse_input("_N:User が _N:Order を");  // Phase 0を使う
    assert!(validate_pattern(&parts).is_ok());          // Phase Nをテスト
}
```

**Phase 0が壊れていても、Phase Nのテストは書ける（モック可能）**

### 3. 簡単なリファクタリング

```rust
// Phase Nの実装を完全に変更
// Phase 0は無傷！

// 変更前
pub fn validate_pattern(parts: &[PromptPart]) -> Result<()> {
    // 実装A
}

// 変更後（完全に異なるアルゴリズム）
pub fn validate_pattern(parts: &[PromptPart]) -> Result<()> {
    // 実装B（まったく違うアルゴリズム）
}

// Phase 0: 変更なし！
```

---

## 粗結合の実践的メリット

### 1. 並列開発

```
開発者A: Phase 3（動詞ブロック）を開発
開発者B: Phase N（ロジックチェック）を開発
  ↓
Phase 0が安定していれば、競合なし
```

### 2. 段階的置き換え

```
Phase N 実装Aが遅い
  ↓
別ブランチでPhase N 実装Bを開発
  ↓
実装Bが速い → 入れ替え
  ↓
Phase 0, N+1, N+2: 影響なし
```

### 3. 簡単な機能トグル

```rust
// Phase Nを無効化したい場合
pub fn parse_input_checked(input: &str) -> Result<Vec<PromptPart>, ValidationError> {
    let parts = parse_input(input);
    // validate_pattern(&parts)?;  // ← コメントアウトするだけ
    Ok(parts)
}
```

### 4. リスク隔離

```
Phase Nに致命的なバグ
  ↓
Phase Nを一時的に無効化
  ↓
Phase 0, Phase 1, Phase 2は動作し続ける
  ↓
システムは部分的に機能
```

---

## 伝統的アプローチとの比較

### 伝統的アプローチ（破壊的変更）

```
Phase 0: parse_input() → Vec<PromptPart>
  ↓ (Phase Nで変更)
Phase N: parse_input() → Result<Vec<PromptPart>, ValidationError>
  ↓
問題: 既存のコードがすべて壊れる！
```

**影響**:

| 側面 | 結果 |
|------|------|
| **結合度** | 高い（すべてのコードが影響を受ける） |
| **変更の影響** | 波及効果が全体に広がる |
| **テスト** | 複雑なモック必要 |
| **リファクタリング** | 高リスク、広範な影響 |
| **並列開発** | 困難（マージコンフリクト） |
| **モジュール抽出** | 慎重な計画が必要 |

### レイヤードアプローチ（非破壊的拡張）

```
Phase 0: parse_input() → Vec<PromptPart>  ← 変更なし
  ↓
Phase N: parse_input_checked() → Result<...>  ← 新規追加
```

**影響**:

| 側面 | 結果 |
|------|------|
| **結合度** | 低い（各レイヤーが独立） |
| **変更の影響** | 局所的（単一レイヤーのみ） |
| **テスト** | シンプル、独立したテスト |
| **リファクタリング** | 低リスク、局所的影響 |
| **並列開発** | 簡単（レイヤー隔離） |
| **モジュール抽出** | 自然なファイル境界 |

---

## コード品質メトリクスの自動的改善

レイヤードアーキテクチャでは、以下のメトリクスが**自動的に**改善されます：

### 凝集度（Cohesion）: ⬆️ 高い
- 各レイヤーは単一の明確な目的を持つ
- レイヤー内の関数は密接に協力

### 結合度（Coupling）: ⬇️ 低い
- レイヤーは安定したインターフェースのみで相互作用
- 双方向依存がない

### テスタビリティ（Testability）: ⬆️ 高い
- 各レイヤーを独立してテスト可能
- モック依存が最小限

### 保守性（Maintainability）: ⬆️ 高い
- 変更は単一レイヤーに局所化
- 理解するには関連レイヤーのみ読めばよい

### 再利用性（Reusability）: ⬆️ 高い
- 下位レイヤー（例: Phase 0）は異なるコンテキストで再利用可能
- 各レイヤーは自己完結

---

## なぜPrompに特有なのか

### Prompsでうまくいく理由

**1. フェーズ数が有限**（5-10ブランチ最大）
- 粗結合が管理可能
- 「スパゲッティアーキテクチャ」のリスクがない

**2. 各Phaseに明確な責務**
- Phase 0: パース処理
- Phase 1: GUI
- Phase 2+: ブロックタイプ
- Phase N: 検証
- モジュール境界が明白

**3. API安定性ポリシーが粗結合を強化**
- Phase 0 APIは不変
- 上位レイヤーは下位レイヤーを変更しない
- 結合度は時間経過で増加しない

**4. ブランチ戦略がモジュール構造と一致**
- 各feature/phase-Nブランチ = 1つのモジュール
- Gitワークフローがアーキテクチャ設計を反映
- マージコンフリクトが稀（モジュール隔離）

---

## 創発パターン

```
開始: レイヤードアーキテクチャ
  ↓
強制: 非破壊拡張原則
  ↓
結果: 粗結合（自動的）
  ↓
メリット:
  - 簡単なモジュール分離
  - 独立したテスト
  - 並列開発
  - シンプルなリファクタリング
  - リスク隔離
```

**重要な洞察**: 粗結合を直接設計したわけではありません。安定したインターフェースでレイヤードアーキテクチャを設計したら、粗結合が自然な結果として現れました。

これが**アーキテクチャパターンの力**です - 明示的に設計していないメリットが得られます。

---

## 実例: Phase 1完了後のテスト追加

### 背景

Phase 1（GUI実装）完了後、Phase 2に進む前にテストを追加しました。

**タイムライン**:
```
Day 1, セッション1:
  - Phase 1機能実装（Blockly統合、リアルタイムプレビュー）
  - 手動テストで「完成」とマーク

Day 1, セッション2:
  - ユーザー: 「Phase 2の前にテストを実装しよう」
  - フロントエンドテスト作成: Jest + JSDOM（21テスト）
  - バックエンド統合テスト作成: Rust（11テスト）
  - テストで重大な設計問題を発見
```

### 発見された問題

**テストの期待値**:
```
入力:  "_N:User _N:Order"
出力: "User (NOUN) が Order (NOUN) を 作成"
       ↑               ↑
       2つの別々の名詞マーカーが期待される
```

**実際の出力**:
```
"User Order (NOUN)"
 ↑
 文全体に1つのマーカーのみ
```

**問題の特定**:
```
実装: 文レベル
要件: トークンレベル
```

### リファクタリングの影響

**変更されたファイル**:
```
- src/lib.rs: parse_input()の完全リファクタリング
- src/lib.rs: generate_prompt()の出力フォーマット変更
- src/commands.rs: 11統合テストの更新
- docs/*: 6ドキュメントファイルの更新
```

**コード変更の規模**:
```
- コアアルゴリズム: ~50行変更
- テスト期待値: ~20行更新
- ドキュメント: ~30セクション更新
```

**修正にかかった時間**:
```
発見から完了まで: ~45分
42テストすべて100%パス
```

### もしテストを延期していたら？

```
テストなしで継続:
  Phase 1 → Phase 2（さらにブロックタイプ追加）
    ↓
  Phase 3 → Phase 4（さらに機能実装）
    ↓
  Phase N（ようやくテスト追加）
    ↓
  文レベルが機能しないことを発見
    ↓
  すべてのPhaseを遡ってリファクタリングが必要
    ↓
  推定影響: 数日～数週間の手戻り

即時テストで:
  Phase 1 → テスト（すぐに問題を発見）
    ↓
  Phase 2の前に修正
    ↓
  推定影響: 45分
    ↓
  節約時間: 数日分のデバッグと手戻り
```

### 粗結合がリファクタリングを救った

**もし密結合だったら**:
```
Phase 1のコードがPhase 0に組み込まれている
  ↓
Phase 0の変更 → Phase 1も壊れる
  ↓
Phase 1の修正 → Phase 0も影響受ける
  ↓
循環的デバッグ → 数日
```

**粗結合のおかげで**:
```
Phase 0を修正
  ↓
Phase 1は独立 → 壊れない（Phase 0の契約は維持されている）
  ↓
テストを更新
  ↓
完了 → 45分
```

---

## 粗結合を維持する実践的ガイドライン

### ルール1: 既存APIを変更しない

**禁止**:
```rust
// ❌ 悪い例: 既存関数のシグネチャを変更
pub fn parse_input(input: &str) -> Vec<PromptPart>
    ↓
pub fn parse_input(input: &str, options: ParseOptions) -> Vec<PromptPart>
```

**許可**:
```rust
// ✅ 良い例: 既存関数を維持、新関数を追加
pub fn parse_input(input: &str) -> Vec<PromptPart> {
    // 元の実装（変更なし）
}

pub fn parse_input_with_options(input: &str, options: ParseOptions) -> Vec<PromptPart> {
    // 新機能
}
```

### ルール2: 既存データ構造を変更しない

**禁止**:
```rust
// ❌ 悪い例: 構造体フィールドを変更
pub struct PromptPart {
    pub is_noun: bool,
    pub text: String,
}
    ↓
pub struct PromptPart {
    pub part_type: PartType,  // is_nounを置き換え
    pub text: String,
}
```

**許可**:
```rust
// ✅ 良い例: 新フィールド追加、既存を維持
pub struct PromptPart {
    pub is_noun: bool,        // 既存を維持
    pub text: String,         // 既存を維持
    pub metadata: Option<Metadata>,  // 新フィールド（オプション）
}
```

### ルール3: 上位レイヤーは下位レイヤーを変更しない

**例**:
```
Phase N (feature/phase-n):
  ✅ parse_input()を呼び出す
  ✅ PromptPartを使用
  ❌ parse_input()の実装を変更
  ❌ PromptPart定義を変更
```

**ファイルレベルのルール**:
```
feature/phase-n:
  ✅ src/modules/validation.rs作成（新ファイル）
  ✅ src/lib.rsを読む（エクスポートを使用）
  ❌ src/lib.rsを編集（Phase 0コードを変更）
```

---

## まとめ

### 重要な発見

**1. 粗結合は創発特性**
- 明示的な目標ではなく、設計の結果
- レイヤードアーキテクチャ + 非破壊拡張 → 粗結合（自動的）

**2. 一方向依存が鍵**
```
Phase N → Phase 0（呼び出しのみ）
Phase 0 ← Phase N（変更なし）
  ↓
粗結合が自然に生まれる
```

**3. メリットは自動的に付いてくる**
- モジュール分離が簡単
- 独立したテスト
- 並列開発
- シンプルなリファクタリング
- リスク隔離

**4. Prompsで機能する理由**
- フェーズ数が有限（5-10）
- 明確な責務
- API安定性ポリシー
- ブランチ戦略がモジュール構造と一致

**5. 実例が証明**
- Phase 1のリファクタリング: 45分で完了
- もし密結合なら: 数日～数週間
- 粗結合が時間を節約

---

## 次回予告

次回は、この粗結合を維持するための**Git戦略**を解説します。

具体的には：
- なぜフィーチャーブランチを削除しないのか
- ブランチとレイヤーの完璧な対応
- マージコンフリクトが稀な理由
- 永続ブランチ戦略の実践

お楽しみに！

---

## 参考資料

この記事の内容は、OSSプロジェクト「Promps」の開発ドキュメントから抽出しています。

- **GitHub**: https://github.com/BonoJovi/Promps
- **詳細ドキュメント**:
  - [DESIGN_PHILOSOPHY.md](https://github.com/BonoJovi/Promps/blob/dev/.ai-context/core/DESIGN_PHILOSOPHY.md)
  - [API_STABILITY.md](https://github.com/BonoJovi/Promps/blob/dev/.ai-context/development/API_STABILITY.md)
- **ライセンス**: MIT License (2025 Yoshihiro NAKAHARA)

---

## 執筆について

この記事は、著者（Yoshihiro NAKAHARA）の設計思想と実践経験をAI（Claude）とのセッションを通じて言語化し、原稿に書き起こしたものです。

- **思考の整理**: 複数プロジェクト（KakeiBon、Promps）での実践を通じて得た暗黙知を、対話を通じて明文化
- **原稿執筆**: Claudeが構成・執筆を担当
- **内容検証**: 著者が技術的正確性とニュアンスを確認

すべての設計判断と技術的洞察は著者の実務経験に基づいています。
