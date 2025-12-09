# Docs Directory Context

このファイルはClaude Codeセッション開始時に**最小限**のプロジェクトコンテキストを自動ロードします。

**トークン最適化**: デフォルトでは必須情報のみをロード。追加コンテキストは必要に応じて`@`参照で読み込みます。

---

## 常にロード（必須コンテキストのみ）

### 必須情報 - 現在の状態と重要なルール
@.ai-context/ESSENTIAL.md

---

## 必要に応じてロード（オンデマンドコンテキスト）

**ドキュメント作業時**:
- ドキュメント規約: `@.ai-context/context/docs/CONVENTIONS.md`
- 言語別ガイドライン: `@.ai-context/context/docs/I18N.md`
- ディレクトリ構造: `@.ai-context/context/docs/STRUCTURE.md`

**プロジェクト参照時**:
- Rustプロジェクト一覧: `@.ai-context/context/projects/RUST_PROJECTS.md`
- プロジェクト別コンテキスト: プロジェクトディレクトリの`.ai-context/`を参照

**ワークフロー作業時**:
- Git運用: `@.ai-context/context/workflows/GIT.md`
- リリースプロセス: `@.ai-context/context/workflows/RELEASE.md`

---

**パフォーマンスノート**: この構成により、セッション起動時のトークン使用量を最小化します（初期ロード < 100行）。
