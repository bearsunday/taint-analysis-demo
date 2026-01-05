# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

BEAR.Security の Psalm Taint Analysis プラグインの検証用デモリポジトリ。脆弱なコードパターンと安全なコードパターンを含み、taint analysis の動作を検証する。

## Common Commands

```bash
# Install dependencies
composer install

# Run taint analysis on vulnerable code (expect errors)
./vendor/bin/psalm --taint-analysis demo/src/Resource/App/Vulnerable/

# Run taint analysis on safe code (expect 0 errors)
./vendor/bin/psalm --taint-analysis demo/src/Resource/App/Safe/

# Full test suite
composer tests
```

## Architecture

### Key Directories

- `demo/src/Resource/App/Vulnerable/` - 脆弱なパターン（検出される）
- `demo/src/Resource/App/Safe/` - 安全なパターン（エラーなし）
- `stubs/PDO.phpstub` - PDO の SQL sink 定義

### Taint Analysis

プラグインは `bear/security` パッケージで提供:
- `BEAR\Security\Psalm\ResourceTaintPlugin`

ResourceObject の `on*` メソッドパラメータを taint source として登録。

## Configuration

- `psalm.xml` - BEAR.Security プラグインを使用
- `composer.json` - `bear/security` を require-dev に含む
