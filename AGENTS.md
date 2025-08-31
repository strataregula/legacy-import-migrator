# AGENTS

自動化エージェントの責務・手順・Runログ様式を定義します。

## 共通原則
- **1 PR = 1 目的**
- **Runログは必ず作成**（Summary は非空、JSTで保存）

## Run Log Agent
- 目的: コマンド実行結果を `docs/run/*.md` に記録して可観測性を高める
- 優先度:
  1) `scripts/new_run_log.py` がある場合（例: world-simulation）
  2) 無ければ `tools/runlog.py`（軽量版）
  3) さらに無ければ `tools/runlog.sh`

### 使い方
```bash
# Python (軽量版)
python tools/runlog.py \
  --label smoke \
  --summary "smoke for cli/tests" \
  --intent "verify unified env works"

# シェル版（最短）
./tools/runlog.sh smoke "smoke for cli/tests" "verify unified env works"
```

## Migration Progress Agent
- 目的: レガシーimport除去とnamespace移行の進捗可視化
- 進捗追跡: 残存するレガシーパターンの自動検出
- 移行検証: 新旧パターンの動作同等性確認

### 使い方
```bash
# 移行進捗チェック
python -m legacy_import_migrator scan --roots src tests --format progress

# レガシーパターン検索
python -m legacy_import_migrator find --pattern "from legacy" --scope all

# 移行実行
python -m legacy_import_migrator migrate --dry-run --target modern
```

## AST Analysis Agent
- 目的: 抽象構文木解析による高精度な移行処理
- 静的解析: インポート依存関係の完全なマッピング
- 安全性保証: 型安全性とセマンティクス保持の検証

### 使い方
```bash
# AST解析
python -m legacy_import_migrator analyze --ast --file path/to/module.py

# 依存関係グラフ生成
python -m legacy_import_migrator deps --graph --format dot

# 型安全性チェック
python -m legacy_import_migrator typecheck --strict --target-version modern
```

## ログサンプル（JST）

```markdown
# Run Log - migration-progress
- When: 2025-08-30T20-00JST
- Repo: legacy-import-migrator
- Summary: Legacy import cleanup progress check

## Intent
assess remaining legacy patterns and migration readiness

## Commands
python -m legacy_import_migrator scan --roots src tests --print-files
python -m legacy_import_migrator find --pattern "from legacy" --count

## Results
- legacy imports found: 23 files, 147 occurrences
- migration coverage: 78% complete
- AST analysis: 5 complex cases require manual review

## Next actions
- prioritize high-impact legacy patterns
- create migration plan for complex cases
- add regression tests for migrated modules
```

**タグ**: #automation #agents #runlog #migration #ast #legacy
