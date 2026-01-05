# BEAR.Sunday Taint Analysis Demo

[BEAR.Security](https://github.com/bearsunday/BEAR.Security) の Psalm Taint Analysis プラグインの検証用デモリポジトリです。

## 概要

このリポジトリは、BEAR.Sunday アプリケーションにおける脆弱なコードパターンと安全なコードパターンを含み、Psalm の taint analysis が正しく脆弱性を検出することを検証します。

## 使用方法

```bash
# 依存関係のインストール
composer install

# 脆弱なコードの解析（エラーが検出される）
./vendor/bin/psalm --taint-analysis demo/src/Resource/App/Vulnerable/

# 安全なコードの解析（エラーなし）
./vendor/bin/psalm --taint-analysis demo/src/Resource/App/Safe/
```

## ディレクトリ構成

```
demo/src/Resource/App/
├── Vulnerable/              # 脆弱なパターン（検出される）
│   ├── SqlInjection.php     # SQL インジェクション
│   ├── AuraSqlInjection.php # Aura.Sql 経由の SQL インジェクション
│   ├── MethodParamInjection.php # メソッドパラメータ経由
│   ├── Xss.php              # XSS
│   ├── ShellInjection.php   # シェルインジェクション
│   └── Ssrf.php             # SSRF
└── Safe/                    # 安全なパターン（エラーなし）
    ├── SqlPrepared.php      # プリペアドステートメント
    ├── AuraSqlPrepared.php  # Aura.Sql の安全な使用
    ├── MethodParamPrepared.php
    ├── HtmlEscaped.php      # HTML エスケープ
    ├── QiqEscapedEcho.php
    ├── JsonOutput.php       # JsonRenderer
    ├── JsonRendererEcho.php
    └── TwigEscaped.php      # Twig autoescape
```

## 検出される脆弱性

| ファイル | 脆弱性 | 検出タイプ |
|---------|--------|-----------|
| SqlInjection.php | SQL インジェクション | TaintedSql |
| AuraSqlInjection.php | SQL インジェクション | TaintedSql |
| MethodParamInjection.php | SQL インジェクション | TaintedSql |
| Xss.php | XSS | TaintedHtml |
| ShellInjection.php | コマンドインジェクション | TaintedShell |
| Ssrf.php | SSRF | TaintedFile, TaintedSSRF |

## 関連リンク

- [BEAR.Security](https://github.com/bearsunday/BEAR.Security) - Psalm Taint Plugin
- [Psalm Taint Analysis](https://psalm.dev/docs/security_analysis/)
- [BEAR.Sunday Security](https://bearsunday.github.io/manuals/1.0/en/security.html)
