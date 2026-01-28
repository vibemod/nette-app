# AGENTS.md

This file provides guidance to agents when working with code in this repository.

## Project

- Web application
- PHP 8.4
- Nette Framework
- Contributte packages
- Nette Tester
- Doctrine ORM/DBAL

## Commands

### Setup
```bash
make init        # Copy config/local.neon.example → config/local.neon
make project     # composer install + create var/tmp and var/log directories
```

### Development
```bash
make dev         # Start PHP dev server at localhost:8080 (NETTE_DEBUG=1)
make docker-up   # Start Docker container (dockette/web:php-84)
make docker-in   # Shell into Docker container
```

### Quality Assurance
```bash
make qa          # Run both cs and phpstan
make cs          # CodeSniffer check (app + tests)
make csf         # CodeSniffer auto-fix
make phpstan     # Static analysis (level 9, PHP 8.4)
```

### Testing
```bash
make tests                                              # Run all tests with Nette Tester
vendor/bin/tester -s -p php --colors 1 -C tests/Unit    # Run only unit tests
vendor/bin/tester -s -p php --colors 1 -C tests/path/to/TestFile.phpt  # Run single test
make coverage                                           # Generate coverage report
```

## Architecture

```
├── www/
│   └── index.php             — Web entry point (bootstraps container, runs Nette\Application\Application)
├── bin/
│   └── console               — CLI entry point (bootstraps container, runs Symfony Console)
├── app/
│   ├── Bootstrap.php         — Uses Contributte\Nella\Boot\Bootloader with NellaPreset to create the DI container
│   ├── Domain/               — Doctrine entities and repositories (PHP 8 ORM attributes, scanned under App\Domain)
│   ├── Model/                — Infrastructure: routing (RouterFactory), custom exceptions
│   └── UI/                   — Nette presenters and Latte templates
│       ├── BasePresenter     — Extends Contributte\Nella\UI\NellaPresenter
│       └── @Templates/       — Layout template (@layout.latte), presenter templates in Templates/ subdirs
├── config/
│   ├── config.neon           — Main config: imports doctrine.neon, registers services, timezone Europe/Prague
│   ├── doctrine.neon         — Doctrine DBAL/ORM, migrations, fixtures extensions (SQLite at db/db.sqlite)
│   └── local.neon            — Local overrides (gitignored, created from local.neon.example via make init)
├── db/
│   └── db.sqlite             — SQLite database
└── tests/                    — Nette Tester (not PHPUnit), .phpt extension for integration tests
    ├── bootstrap.php         — Loads autoloader and Contributte\Tester\Environment::setup()
    ├── Toolkit/Tests.php     — Shared constants (ROOT_PATH, APP_PATH)
    ├── Unit/                 — Unit tests for domain entities
    └── App/                  — Integration tests: DI container, Doctrine mapping, Latte compilation
```
