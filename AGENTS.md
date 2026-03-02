- To regenerate the JavaScript SDK, run `./packages/sdk/js/script/build.ts`.
- ALWAYS USE PARALLEL TOOLS WHEN APPLICABLE.
- The default branch in this repo is `dev`.
- Local `main` ref may not exist; use `dev` or `origin/dev` for diffs.
- Prefer automation: execute requested actions without confirmation unless blocked by missing info or safety/irreversibility.

## Build Commands

- `bun typecheck` - Type check all packages (uses tsgo, turbo)
- `bun dev` - Run CLI dev mode (`packages/opencode`)
- `bun dev:web` - Run web app dev server (`packages/app`)
- `bun dev:desktop` - Run desktop app dev mode (`packages/desktop`)
- `bun turbo build` - Build all packages

## Test Commands

**IMPORTANT: Tests cannot run from repo root.**

- `cd packages/opencode && bun test` - Run opencode tests
- `cd packages/opencode && bun test --timeout 30000` - Run with timeout
- `cd packages/opencode && bun test src/path/to/file.test.ts` - Run single test file
- `cd packages/app && bun test:unit` - Run app unit tests
- `cd packages/app && bun test:unit:watch` - Run app unit tests in watch mode
- `cd packages/app && bun test:e2e` - Run Playwright E2E tests
- `cd packages/app && bun test:e2e:ui` - Run E2E tests with UI

## Lint/Format

- `bun prettier --write .` - Format all files
- Prettier config: `{ "semi": false, "printWidth": 120 }`

## Style Guide

### General Principles

- Keep things in one function unless composable or reusable
- Avoid `try`/`catch` where possible
- Avoid using the `any` type
- Prefer single word variable names where possible
- Use Bun APIs when possible, like `Bun.file()`
- Rely on type inference when possible; avoid explicit type annotations or interfaces unless necessary for exports or clarity
- Prefer functional array methods (flatMap, filter, map) over for loops; use type guards on filter to maintain type inference downstream

### Naming

Prefer single word names for variables and functions. Only use multiple words if necessary.

```ts
// Good
const foo = 1
function journal(dir: string) {}

// Bad
const fooBar = 1
function prepareJournal(dir: string) {}
```

Reduce total variable count by inlining when a value is only used once.

```ts
// Good
const journal = await Bun.file(path.join(dir, "journal.json")).json()

// Bad
const journalPath = path.join(dir, "journal.json")
const journal = await Bun.file(journalPath).json()
```

### Destructuring

Avoid unnecessary destructuring. Use dot notation to preserve context.

```ts
// Good
obj.a
obj.b

// Bad
const { a, b } = obj
```

### Variables

Prefer `const` over `let`. Use ternaries or early returns instead of reassignment.

```ts
// Good
const foo = condition ? 1 : 2

// Bad
let foo
if (condition) foo = 1
else foo = 2
```

### Control Flow

Avoid `else` statements. Prefer early returns.

```ts
// Good
function foo() {
  if (condition) return 1
  return 2
}

// Bad
function foo() {
  if (condition) return 1
  else return 2
}
```

### Imports

- Use absolute imports for cross-package dependencies via workspace aliases
- Within a package, use relative imports
- Import type-only imports with `import type { ... }` when possible

### Types

- Avoid explicit type annotations when type inference works
- Use `satisfies` instead of `as` for type assertions when possible
- Prefer interfaces over type aliases for object shapes

### Error Handling

- Prefer throwing errors over returning error values
- Use descriptive error messages
- Avoid try/catch unless absolutely necessary

### Schema Definitions (Drizzle)

Use snake_case for field names so column names don't need to be redefined as strings.

```ts
// Good
const table = sqliteTable("session", {
  id: text().primaryKey(),
  project_id: text().notNull(),
  created_at: integer().notNull(),
})

// Bad
const table = sqliteTable("session", {
  id: text("id").primaryKey(),
  projectID: text("project_id").notNull(),
  createdAt: integer("created_at").notNull(),
})
```

## Testing

- Avoid mocks as much as possible
- Test actual implementation, do not duplicate logic into tests
- Tests cannot run from repo root (guard: `do-not-run-tests-from-root`); run from package dirs like `packages/opencode`.
