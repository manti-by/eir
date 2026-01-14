# EIR - FastAPI Project Template

Modern FastAPI project template with SQLAlchemy, PostgreSQL, and uv for package management.

## Features

- FastAPI with modern Python 3.13+
- SQLAlchemy 2.0 with async support
- PostgreSQL database
- Alembic migrations
- Docker & Docker Compose deployment
- pytest testing
- Ruff linting and formatting
- Pre-commit hooks

## Requirements

- Python 3.13+
- [uv](https://github.com/astral-sh/uv) for package management
- Docker (for containerized deployment)

## Quick Start

### Install Dependencies

```bash
uv sync
```

### Development Server

```bash
uv run python -m eir.main
# or
uv run python -m uvicorn eir.main:app --reload
```

Access at http://localhost:8000

API Documentation:
- Swagger UI: http://localhost:8000/docs
- ReDoc: http://localhost:8000/redoc

### Docker Development

```bash
docker compose up -d
```

### Testing

```bash
# Run all tests
uv run pytest --create-db eir/

# Run with coverage
uv run pytest --cov=eir --cov-report=term-missing eir/
```

### Code Quality

```bash
# Run pre-commit hooks
uv run pre-commit run

# Lint
uv run ruff check .

# Format
uv run ruff format .
```

### Security Analysis

```bash
uv run bandit -c pyproject.toml .
```

## Project Structure

```
eir/
├── alembic/           # Database migrations
├── app/
│   ├── api/           # API routes
│   ├── core/          # Core application components
│   ├── users/         # User module
│   ├── utils/         # Utility functions
│   └── main.py        # Application entry point
├── tests/             # Test suite
├── pyproject.toml     # Project configuration
├── docker-compose.yml # Docker Compose configuration
└── Dockerfile         # Docker image definition
```

## License

BSD 3-Clause "New" or "Revised"
