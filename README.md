# BEAR.Sunday Taint Analysis Demo

Psalm taint analysis demonstration for BEAR.Sunday ecosystem security validation.

## Goals

This project validates that Psalm's taint analysis correctly detects security vulnerabilities in BEAR.Sunday applications.

### Detection Goals (Vulnerable Code)

| Vulnerability | Expected Detection | File | Status |
|--------------|-------------------|------|--------|
| SQL Injection | TaintedSql | SqlInjection.php | Detected |
| XSS (echo) | TaintedHtml | Xss.php | Detected |
| Shell Injection | TaintedShell | ShellInjection.php | Detected |
| SSRF (file_get_contents) | TaintedSSRF | Ssrf.php | Detected |
| SSRF (curl) | TaintedSSRF | Ssrf.php | Detected |

### Safe Code Goals

| Pattern | File | Expected Result | Status |
|---------|------|----------------|--------|
| MediaQuery prepared statements | SqlPrepared.php | No TaintedSql | Pass |
| Qiq escape helpers | HtmlEscaped.php | No TaintedHtml | Pass |
| Qiq escape + echo | QiqEscapedEcho.php | No TaintedHtml | Pass |
| JsonRenderer | JsonOutput.php | No TaintedHtml | Pass |
| JsonRenderer + echo | JsonRendererEcho.php | No TaintedHtml | Pass |
| Twig autoescape | TwigEscaped.php | No TaintedHtml | Pass |

## Quick Start

```bash
# Install dependencies
composer install

# Run taint analysis on Vulnerable (expect 7 errors)
./vendor/bin/psalm --taint-analysis src/Resource/App/Vulnerable/

# Run taint analysis on Safe (expect 0 errors)
./vendor/bin/psalm --taint-analysis src/Resource/App/Safe/
```

## Directory Structure

```
src/Resource/App/
├── Vulnerable/              # Intentionally vulnerable code
│   ├── SqlInjection.php     # Direct SQL concatenation
│   ├── Xss.php              # Unescaped HTML output
│   ├── ShellInjection.php   # Direct shell command execution
│   └── Ssrf.php             # SSRF via file_get_contents/curl
└── Safe/                    # Secure patterns
    ├── SqlPrepared.php      # Using MediaQuery prepared statements
    ├── HtmlEscaped.php      # Using Qiq escape helpers
    ├── QiqEscapedEcho.php   # Qiq escape with echo output
    ├── JsonOutput.php       # Using JsonRenderer
    ├── JsonRendererEcho.php # JsonRenderer with echo output
    └── TwigEscaped.php      # Using Twig autoescape
```

## Running Tests

### Vulnerable Code Only

```bash
./vendor/bin/psalm --taint-analysis src/Resource/App/Vulnerable/
# Expected: 7 errors (TaintedSql, TaintedHtml, TaintedShell, TaintedSSRF x2, TaintedTextWithQuotes)
```

### Safe Code Only

```bash
./vendor/bin/psalm --taint-analysis src/Resource/App/Safe/
# Expected: No errors found
```

## Required Taint Annotations

For full E2E functionality, the following packages need taint annotations:

| Package | PR | Annotations |
|---------|-----|-------------|
| ray/media-query | [#78](https://github.com/ray-di/Ray.MediaQuery/pull/78) | `@psalm-taint-escape sql` |
| bear/resource | Pending | `@psalm-taint-source input`, `@psalm-taint-escape html` |
| ray/aura-sql-module | Pending | `@psalm-taint-sink sql`, `@psalm-taint-escape sql` |
| qiq/qiq | Pending | `@psalm-taint-escape html` |
| madapaja/twig-module | Pending | `@psalm-taint-escape html` |

## PDO Stub

This project includes a PDO stub (`stubs/PDO.phpstub`) that marks `PDO::query()` and `PDO::exec()` as SQL sinks. This is required because Psalm's default stubs don't include taint annotations for PDO.

## CI Integration

The project includes a GitHub Actions workflow that:

1. Verifies Vulnerable code triggers expected detections (TaintedSql, TaintedHtml, TaintedShell, TaintedSSRF)
2. Verifies Safe code has no taint errors

## References

- [Psalm Taint Analysis Documentation](https://psalm.dev/docs/security_analysis/)
- [BEAR.Sunday Framework](https://bearsunday.github.io/)
