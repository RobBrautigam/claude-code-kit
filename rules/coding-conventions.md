# Coding Conventions

## Languages
Adopt language preferences to your stack. Recommended defaults:
- Python (primary backend), JavaScript / TypeScript (primary frontend)

## Standards
- Use async / await, never raw `.then()` chains
- All API calls wrapped in try / catch with meaningful error messages
- Log inputs and outputs at every routing step for debugging
- Keep each module self-contained with its own `requirements.txt` or `package.json` when it makes sense

## Testing
- Python: `pytest` for backend tests
- Frontend: Vitest for unit/component tests, Playwright for E2E
- Write tests for critical paths: API endpoints, data sync functions, auth flows
- Run tests before shipping. Don't claim something works without evidence.

## Linting & formatting
- Frontend: ESLint + TypeScript strict mode
- Python: follow PEP 8 conventions
- Use path aliases (`@/components`, `@/lib`, `@/utils`) for clean imports in frontend projects

## Logging
- Python: use the built-in `logging` module with structured output
- Frontend: `console.error` for errors only, no `console.log` in production code
- Include context (function name, input IDs) in log messages for traceability

## Security
- Never commit `.env` files. Use `.env.example` for templates.
- All secrets go in environment variables, never hardcoded.

## Architecture
- Every module must work independently before integrating.
