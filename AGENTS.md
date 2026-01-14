# AGENTS.md

## Project Overview

FastAPI project template. It uses FastAPI, SQLAlchemy, and modern Python tooling.

## Project Structure

- All REST API related code placed in `./app/api/`
- FastAPI app, models, and core components in `./app/core/`, `./app/users/`, etc. Utils in `./app/utils/`.
- Tests in `./tests/`

## Development Commands

### Package Management

```bash
uv sync                    # Install dependencies
uv sync --upgrade          # Upgrade dependencies
```

### FastAPI Development

```bash
uv run python -m eir.main                        # Start development server
uv run python -m uvicorn eir.main:app --reload   # Start with uvicorn directly
```

### Testing

```bash
# Run all tests
uv run pytest --create-db eir/

# Run single test file
uv run pytest --create-db eir/tests/api/test_sensors.py

# Run single test method
uv run pytest --create-db eir/tests/api/test_sensors.py::TestSensorsAPI::test_sensors__list

# Run with coverage (if available)
uv run pytest --cov=eir --cov-report=term-missing eir/
```

### Code Quality

```bash
# Run all pre-commit hooks
uv run pre-commit run

# Individual tools
uv run ruff check .                 # Lint
uv run ruff format .                # Format
uv run bandit -c pyproject.toml .   # Security analysis
```

## Language & Environment

- Follow PEP 8 style guidelines, use Ruff for formatting (120 char line length)
- Use type hints for all function parameters and return values
- Prefer f-strings for string formatting over .format() or %
- Use list/dict/set comprehensions over map/filter when readable
- Prefer Pathlib over os.path for file operations
- Follow PEP 257 for docstrings (simple summary line plus detailed explanation)
- Use structural pattern matching (match/case) for complex conditionals
- Prefer EAFP (try/except) over LBYL (if checks) for Python idioms

## FastAPI Framework

- Follow FastAPI best practices
- Use SQLAlchemy effectively, avoid raw SQL
- Use dependencies for database sessions and authentication
- Put business logic to services, prefer functions, routers and dependencies should be short and clean

## Code Style Guidelines

### Python Standards

- Python version 3.13+ with type hints encouraged
- Ensure code style consistency using Ruff
- Line length 120 characters
- Indentation 4 spaces

### Import Organization

Use Ruff isort with this order:

1. `__future__` imports
2. Standard library imports
3. Third-party imports
4. First-party imports
5. eir imports

```python
from __future__ import annotations

import os
from decimal import Decimal

from fastapi import APIRouter, Depends
from sqlalchemy.orm import Session

from app.users.models import User
```

### Naming Conventions

- **Models**: `PascalCase` (e.g., `DataLog`)
- **Functions/Variables**: `snake_case` (e.g., `get_data`)
- **Constants**: `UPPER_SNAKE_CASE` (e.g., `MAX_RETRY_COUNT`)
- **File Names**: `snake_case.py` (e.g., `data_utils.py`)
- **Classes**: `PascalCase` (e.g., `DataService`)

### Type Hints

Use type hints for function signatures and complex variables:

```python
from decimal import Decimal


def get_data(data_id: str) -> dict[str, Decimal] | None:
    # Implementation here
    return None
```

### FastAPI Patterns

#### Routes and Dependencies

- Use APIRouter for organizing routes
- Include proper type hints for Request/Response
- Use Depends for dependency injection
- Implement proper status codes and response models

```python
from fastapi import APIRouter, status

router = APIRouter(prefix="/healthcheck", tags=["core"])


@router.get("/", status_code=status.HTTP_200_OK)
def healthcheck() -> dict:
    return {"status": "OK"}
```

#### Request Validation

- Use Pydantic models for request/response validation
- Include validation constraints in field definitions
- Use response models to control API output
- Handle validation errors gracefully

#### API Versioning

- URL-based versioning: `/api/v1/...`
- Use router prefixes for versioning: `router = APIRouter(prefix="/v1", ...)`

## Dependency Management

- Manage dependencies via **[uv](https://github.com/astral-sh/uv)** and **virtual environments**.

## Testing

- Focus testing on APIs and critical code paths
- Write integration tests for API endpoints and external integrations
- Use mocks for external services and slow dependencies
- Test error handling and validation logic
- Use meaningful test names that describe the scenario

### Testing Patterns

#### Test Structure

- Use pytest with pytest-asyncio for async tests
- Create descriptive test method names with double underscores
- Use Factory Boy for test data (if using SQLAlchemy factories)
- Test both success and error cases
- Use parametrized tests for similar scenarios

```python
import pytest
from fastapi.testclient import TestClient

from app.main import app
from app.users.models import User


@pytest.fixture
def client():
    return TestClient(app)


@pytest.fixture
def test_user():
    return User()


def test_logs__list(client, test_user):
    response = client.get("/api/v1/logs")

    assert response.status_code == 200
```

#### Test Data

- Use Factory Boy for creating test instances (if applicable)
- Use meaningful default values in factories
- Test with different data variations

## Logging and Error Handling

### Logging

- Use Python's `logging` module, not print statements
- Create logger at module level: `logger = logging.getLogger(__name__)`
- Use appropriate log levels:
    - `DEBUG`: Detailed debug information
    - `INFO`: Confirmation of expected behavior
    - `WARNING`: Unexpected but handled issues
    - `ERROR`: Failures that need attention

```python
import logging

from app.users.services import authenticate

logger = logging.getLogger(__name__)


class AsyncSession:
    pass
  

def process_file(email: str, password: str, session: AsyncSession):
    logger.info(f"Processing user {email}")
    try:
        user, _ = authenticate(email=email, password=password, session=session)
        logger.info(f"{user} is successfully authenticated")
    except RuntimeError as e:
        logger.error(f"{email}: {e}")
```

### Error Handling

- Use custom exception classes for domain-specific errors
- Use FastAPI's exception handling (HTTPException, RequestValidationError)
- Log exceptions with context before re-raising

```python
class DataError(Exception):
    """Base exception for data-related errors."""


class DataNotFoundError(DataError):
    """Raised when data cannot be found."""


class DataConnectionError(DataError):
    """Raised when connection to data fails."""
```

## Environment Configuration

### Settings Files

- `settings.py`: Main settings file
- Environment variables for configuration
- Use Pydantic Settings for environment management

### Database

- **Development/Production**: PostgreSQL
- **Testing**: SQLite (configured in test settings)

## Security Guidelines

- Never commit secrets or API keys
- Use environment variables for sensitive configuration
- Run `bandit` security analysis before commits
- Validate all user inputs
- Use proper authentication and authorization

## Pre-commit Hooks

The project uses pre-commit hooks for code quality:

- Ruff (linting and formatting)
- Bandit (security analysis)
- pyupgrade (Python 3.13+ syntax)

## Deployment

- Services: gunicorn, worker, scheduler, nginx
- Use systemd for service management

## Common Issues

### Testing

- Use `@pytest.mark.asyncio` for async tests
- Use TestClient for FastAPI endpoint tests

### Database

- Use Alembic for migrations
- Check for pending migrations: `alembic revision --autogenerate`

## Performance Optimization

- Use `selectinload` and `joinedload` for query optimization
- Implement proper database indexing
- Cache frequently accessed data with Redis
- Use async endpoints for I/O-bound operations

## AI Behavior

Response style - Concise and minimal:

- Provide minimal, working code without unnecessary explanation
- Omit comments unless absolutely essential for understanding
- Skip boilerplate and obvious patterns unless requested
- Use type inference and shorthand syntax where possible
- Focus on the core solution, skip tangential suggestions
- Assume familiarity with language idioms and framework patterns
- Let code speak for itself through clear naming and structure
- Avoid over-explaining standard patterns and conventions
- Provide just enough context to understand the solution
- Trust the developer to handle obvious cases independently
