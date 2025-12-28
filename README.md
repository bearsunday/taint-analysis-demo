# BEAR.Sunday Taint Analysis Demo

Psalm taint analysis demonstration for BEAR.Sunday ecosystem security validation.

## Goals

This project validates that Psalm's taint analysis correctly detects security vulnerabilities in BEAR.Sunday applications.

### Detection Goals (Vulnerable Code)

| Vulnerability | Expected Detection | Status |
|--------------|-------------------|--------|
| SQL Injection | TaintedSql | Detected |
| XSS (echo) | TaintedHtml | Detected |
| Shell Injection | TaintedShell | Detected |

### Safe Code Goals

| Pattern | Expected Result | Status |
|---------|----------------|--------|
| MediaQuery (prepared statements) | No TaintedSql | Pass |
| Qiq escape helpers | No TaintedHtml | Pass |
| JsonRenderer | No TaintedHtml | Pass |

## Quick Start

```bash
# Install dependencies
composer install

# Run taint analysis
./vendor/bin/psalm --taint-analysis

# Expected: 5 errors in Vulnerable/, 0 errors in Safe/
```

## Directory Structure

```
src/Resource/App/
├── Vulnerable/           # Intentionally vulnerable code
│   ├── SqlInjection.php  # Direct SQL concatenation
│   ├── Xss.php           # Unescaped HTML output
│   └── ShellInjection.php # Direct shell command execution
└── Safe/                 # Secure patterns
    ├── SqlPrepared.php   # Using MediaQuery prepared statements
    ├── HtmlEscaped.php   # Using Qiq escape helpers
    └── JsonOutput.php    # Using JsonRenderer
```

## Running Tests

### Full Analysis

```bash
./vendor/bin/psalm --taint-analysis
```

### Vulnerable Code Only

```bash
./vendor/bin/psalm --taint-analysis src/Resource/App/Vulnerable/
```

### Safe Code Only

```bash
./vendor/bin/psalm --taint-analysis src/Resource/App/Safe/
```

## Required Taint Annotations

For full functionality, the following packages need taint annotations:

| Package | PR | Annotations |
|---------|-----|-------------|
| ray/media-query | [#78](https://github.com/ray-di/Ray.MediaQuery/pull/78) | `@psalm-taint-escape sql` |
| bear/resource | Pending | `@psalm-taint-source input`, `@psalm-taint-escape html` |
| ray/aura-sql-module | Pending | `@psalm-taint-sink sql`, `@psalm-taint-escape sql` |
| qiq/qiq | Pending | `@psalm-taint-escape html` |

## PDO Stub

This project includes a PDO stub (`stubs/PDO.phpstub`) that marks `PDO::query()` and `PDO::exec()` as SQL sinks. This is required because Psalm's default stubs don't include taint annotations for PDO.

## CI Integration

Add to your GitHub Actions workflow:

```yaml
- name: Run Psalm Taint Analysis
  run: ./vendor/bin/psalm --taint-analysis
```

## References

- [Psalm Taint Analysis Documentation](https://psalm.dev/docs/security_analysis/)
- [BEAR.Sunday Framework](https://bearsunday.github.io/)
