# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **BEAR.Sunday Psalm Taint Plugin** - a Psalm plugin that enables taint analysis for BEAR.Sunday ResourceObject. The project also contains demo code with intentionally vulnerable patterns (`demo-app/src/Resource/App/Vulnerable/`) and secure patterns (`demo-app/src/Resource/App/Safe/`) to validate detection.

## Common Commands

```bash
# Install dependencies
composer install

# Run taint analysis on vulnerable code (expect errors)
./vendor/bin/psalm --taint-analysis demo-app/src/Resource/App/Vulnerable/

# Run taint analysis on safe code (expect 0 errors)
./vendor/bin/psalm --taint-analysis demo-app/src/Resource/App/Safe/

# Full test suite (code style, static analysis, quality checks)
composer tests

# Static analysis only (Psalm + PHPStan)
composer sa

# Run PHPUnit tests
composer test

# Check/fix code style (PSR-12 + Doctrine)
composer cs
composer cs-fix

# Start development server
composer serve
```

## Architecture

### BEAR.Sunday ResourceObject Pattern

Resources are RESTful endpoints with DI-injected dependencies:

```php
class MyResource extends ResourceObject
{
    public function __construct(private PDO $pdo) {}

    public function onGet(string $id): static
    {
        // Handle GET request
        return $this;
    }
}
```

Methods must return `$this` for response transfer.

### Taint Analysis Concepts

- **Sources**: User input (`$_GET`, `$_POST`, `$_SERVER`)
- **Sinks**: Dangerous operations (PDO::query, echo, shell_exec, file_get_contents)
- **Escapes**: Sanitization functions with Psalm taint annotations

### Key Directories

- `src/` - Psalm plugin (`ResourceTaintPlugin.php`, `ResourceTaintHandler.php`)
- `demo-app/src/Resource/App/Vulnerable/` - SQL injection, XSS, shell injection, SSRF examples
- `demo-app/src/Resource/App/Safe/` - Prepared statements, HTML escaping, JsonRenderer patterns
- `stubs/PDO.phpstub` - Custom Psalm stub marking PDO methods as SQL sinks

### Security Patterns

**Vulnerable (detected by Psalm):**
- Direct SQL concatenation: `$pdo->query("SELECT * FROM users WHERE id = " . $id)`
- Unescaped output: `echo $userInput`
- Shell commands: `shell_exec("ping " . $host)`

**Safe (passes Psalm):**
- Ray.MediaQuery prepared statements
- Qiq HTML escaping: `$this->h($input)`
- JsonRenderer (auto-safe for API responses)
- Twig with autoescape enabled

## Configuration

- `psalm.xml` - Error level 2, taint analysis enabled, custom PDO stub
- `phpstan.neon` - Level 6 strict analysis
- `phpcs.xml` - PSR-12 + Doctrine coding standard
