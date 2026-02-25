---
name: fastify-scaffolding
description: Scaffold a production-grade Fastify service using the latest stable versions of all dependencies
---

## Purpose

Scaffold a production-grade Fastify service using the latest stable versions of all dependencies with the following requirements:

- Node.js: Latest LTS
- Package Manager: pnpm
- Language: TypeScript (strict mode)
- Module System: ESM
- Single service architecture
- Source root directory: `src`
- Linting & Formatting: @biomejs/biome
- Testing: Vitest
- Schema validation: TypeBox with fastify-type-provider-typebox
- Logging: pino
- Graceful shutdown: close-with-grace
- Load management: @fastify/under-pressure
- Environment management: @fastify/env (with dotenv)
- Database: pg, @fastify/postgres
- Migrations: postgrator
- Documentation: @fastify/swagger, @fastify/swagger-ui
- Autoloading: @fastify/autoload
- Plugin encapsulation: fastify-plugin
- Docker: multi-stage, minimal image

---

## Scaffolding Rules

### 1. Project Initialization

- Use `pnpm init`
- Add `"type": "module"` to `package.json`
- Set `"engines"` to Node latest LTS
- Use strict TypeScript configuration
- Root source directory must be `src`

---

### 2. Dependencies (latest stable versions)

#### Production Dependencies

- fastify
- @fastify/autoload
- @fastify/postgres
- @fastify/swagger
- @fastify/swagger-ui
- @fastify/env
- @fastify/under-pressure
- fastify-plugin
- fastify-type-provider-typebox
- typebox
- pino
- close-with-grace
- postgrator
- pg
- dotenv

#### Development Dependencies

- typescript
- @types/node
- @types/pg
- vitest
- @biomejs/biome

---

### 3. TypeScript Configuration

- `"strict": true`
- `"module": "ESNext"`
- `"moduleResolution": "NodeNext"`
- `"target": "ES2022"` (or latest supported by Node LTS)
- `"rootDir": "src"`
- `"outDir": "dist"`
- `"esModuleInterop": true`
- `"skipLibCheck": true`
- `"forceConsistentCasingInFileNames": true`

---

## Project Structure

.
├── src
│   ├── app.ts
│   ├── server.ts
│   ├── plugins
│   │   ├── external
│   │   └── internal
│   ├── routes
│   ├── schemas
│   ├── services
│   ├── utils
│   ├── migrations
│   └── tests
├── Dockerfile
├── .dockerignore
├── tsconfig.json
├── biome.json
└── package.json

---

## Fastify Configuration

### Autoload Setup

Use `@fastify/autoload` to load plugins and routes.

```ts
await server.register(fastifyAutoload, {
  dir: join(import.meta.dirname, "plugins/external"),
  options: { ...options },
});

await server.register(fastifyAutoload, {
  dir: join(import.meta.dirname, "plugins/internal"),
  options: { ...options },
});

server.register(fastifyAutoload, {
  dir: join(import.meta.dirname, "routes"),
  autoHooks: true,
  cascadeHooks: true,
  ignorePattern: /.*(shared|utils)-?(.+)?\.(t|j)s$/,
  options: { ...options },
});


⸻

Plugin Rules
	•	All plugins must use fastify-plugin
	•	External dependency plugins go in plugins/external
	•	Internal owned plugins go in plugins/internal
	•	Plugins must be encapsulated
	•	Use autoConfig when required (e.g., PostgreSQL)

PostgreSQL Plugin Pattern

export const autoConfig = (fastify: FastifyInstance): PoolConfig => {
  if (process.env.DATABASE_TEST_URL) {
    return { connectionString: process.env.DATABASE_TEST_URL };
  }

  return {
    host: fastify.config.POSTGRES_HOST,
    port: fastify.config.POSTGRES_PORT,
    user: fastify.config.POSTGRES_USER,
    password: fastify.config.POSTGRES_PASSWORD,
    database: fastify.config.POSTGRES_DATABASE,
  };
};

export default pg;


⸻

Environment Configuration
	•	Use @fastify/env
	•	Configure with:

dotenv: { debug: false, quiet: true }

	•	Register as an external plugin
	•	Expose typed config via Fastify instance

⸻

Schema & Type Safety
	•	Use typebox
	•	Use fastify-type-provider-typebox
	•	Always define:
	•	request body schema
	•	query schema
	•	response schema
	•	Define "4xx" and "5xx" error schemas
	•	Use Static<typeof Schema> for inferred types

⸻

Route Rules
	•	Use FastifyPluginAsyncTypebox
	•	Always define operationId
	•	Always define tags
	•	Use preValidation for permission checks
	•	Use autoPrefix for route versioning
	•	Return typed responses

⸻

Logging
	•	Use pino
	•	Configure structured logging
	•	Enable in Fastify logger option

⸻

Graceful Shutdown
	•	Use close-with-grace
	•	Properly close:
	•	Fastify server
	•	PostgreSQL pool

⸻

Load Management
	•	Register @fastify/under-pressure
	•	Configure health checks and memory thresholds

⸻

Swagger
	•	Register:
	•	@fastify/swagger
	•	@fastify/swagger-ui
	•	Generate OpenAPI docs from schemas
	•	Expose /docs endpoint

⸻

Database Migrations
	•	Use postgrator
	•	Migrations stored in /migrations
	•	Run on startup before server listens

⸻

Testing
	•	Use vitest
	•	Test server using Fastify inject
	•	Isolate database when testing
	•	Support DATABASE_TEST_URL
  • Use TestContainers for NodeJs when possible for realistic integration tests

⸻

Linting & Formatting
	•	Use @biomejs/biome
	•	Enforce formatting and lint rules
	•	Add scripts:
    •	lint
    •	format
    •	check

⸻

Docker

Requirements
	•	Multi-stage build
	•	Smallest possible production image
	•	Use Node LTS Alpine variant
	•	Install only production dependencies in final stage
	•	Use non-root user
	•	Expose application port
	•	Build TypeScript before runtime
	•	Copy only dist, node_modules, and required files

⸻

Scripts

Add to package.json:
	•	dev
	•	build
	•	start
	•	test
	•	lint
	•	format
	•	typecheck
	•	migrate

⸻

TSConfig

Non negotiable TypeScript settings:
	•	strict: true
	•	module: ESNext
	•	moduleResolution: NodeNext
	•	target: ES2022 (or latest supported by Node LTS)
	•	rootDir: src
	•	outDir: dist
	•	esModuleInterop: true
	•	skipLibCheck: true
	•	forceConsistentCasingInFileNames: true
	•	include: ["src"]
⸻

Non-Negotiable Rules
	•	Strict TypeScript only
	•	ESM only
	•	Source code only in src
	•	No CommonJS
	•	No implicit any
	•	No default exports except Fastify plugins
	•	All routes must have schemas
	•	All plugins must use fastify-plugin
	•	Always use latest stable versions
