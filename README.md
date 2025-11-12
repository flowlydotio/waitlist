# Flowly Monorepo

<div align="center">
  **A Turborepo monorepo for Flowly applications and packages.**
  
  [![Built with Turborepo](https://img.shields.io/badge/Built%20with-Turborepo-EF4444?style=for-the-badge&logo=turborepo)](https://turbo.build/repo)
  [![Package Manager](https://img.shields.io/badge/pnpm-10.20.0-F69220?style=for-the-badge&logo=pnpm)](https://pnpm.io/)
</div>

## About / Overview

Flowly is an AI-powered financial guidance platform. This monorepo contains all Flowly applications and shared packages, managed with Turborepo for optimal build performance and developer experience.

The monorepo structure enables:
- **Shared code** across multiple applications
- **Faster builds** with intelligent caching via Turborepo
- **Consistent tooling** and dependencies across projects
- **Simplified development** workflow with unified scripts

## Tech Stack

- **Monorepo Tool**: Turborepo
- **Package Manager**: pnpm (with workspaces)
- **TypeScript**: Shared TypeScript configuration
- **Build System**: Vite (for frontend applications)

## Setup / Installation

### Prerequisites
- Node.js 18+
- pnpm 10.0.0+

### Local Development

1. **Clone the repository**
   ```bash
   git clone https://github.com/your-username/flowly.git
   cd flowly
   ```

2. **Install dependencies**
   ```bash
   pnpm install
   ```

3. **Start development servers**
   ```bash
   # Run all apps in development mode
   pnpm dev
   
   # Or run a specific app
   pnpm --filter waitlist dev
   ```

## Project Structure

```
flowly/
├── apps/
│   └── waitlist/          # Flowly waitlist landing page
├── packages/              # Shared packages (future)
├── package.json          # Root package.json with Turborepo
├── turbo.json            # Turborepo configuration
├── pnpm-workspace.yaml   # pnpm workspace configuration
└── README.md
```

## Usage

### Available Scripts

Run scripts from the root to execute across all workspaces:

```bash
# Development
pnpm dev                  # Start all apps in dev mode
pnpm build                # Build all apps
pnpm build:staging        # Build all apps for staging
pnpm build:production    # Build all apps for production

# Code Quality
pnpm lint                 # Lint all workspaces
pnpm lint:fix            # Fix linting issues
pnpm type-check          # Type check all workspaces

# Maintenance
pnpm clean               # Clean all build artifacts
```

### Running Commands in Specific Workspaces

Use pnpm's filter flag to run commands in specific workspaces:

```bash
# Run dev in waitlist app only
pnpm --filter waitlist dev

# Build waitlist app only
pnpm --filter waitlist build

# Run lint in waitlist app only
pnpm --filter waitlist lint
```

## Applications

### Waitlist (`apps/waitlist`)

The Flowly waitlist landing page with glassmorphism design, SEO optimization, and Loops email integration.

- **Tech**: React 19, Vite, Tailwind CSS 4
- **Live**: [https://getflowly.io](https://getflowly.io)

See `apps/waitlist/README.md` for detailed documentation.

## Packages

Shared packages will be added to the `packages/` directory as the monorepo grows.

## Deploy

### Vercel Configuration

Para o deploy no Vercel funcionar corretamente com o monorepo:

1. **No Vercel Dashboard**, configure:
   - **Root Directory**: `apps/waitlist`
   - **Build Command**: `pnpm --filter waitlist build`
   - **Install Command**: `pnpm install`
   - **Output Directory**: `dist`

2. O arquivo `apps/waitlist/vercel.json` já está configurado para monorepo.

3. **Environment Variables**: Mantenha as mesmas variáveis que você já tinha configuradas.

📖 **Guia completo**: Veja `docs/MONOREPO_MIGRATION.md` para instruções detalhadas.

## Contributing

### Git Workflow

Use o script helper na raiz do monorepo:

```bash
# Criar nova feature branch
./scripts/git-workflow.sh start feature-name

# Finalizar feature e criar PR
./scripts/git-workflow.sh finish

# Ver status
./scripts/git-workflow.sh status
```

**Ou manualmente:**
1. Always work from feature branches
2. Create feature branch from `develop`: `git checkout -b feature/your-feature-name`
3. Make changes and commit frequently with clear messages
4. Push branch and create Pull Request to `develop`
5. Never commit directly to `develop` or `main`

### Code Standards
- Follow TypeScript best practices
- Use ESLint for code quality
- Write small, focused modules
- Add proper error handling
- Include accessibility attributes
- Test on multiple devices and browsers

## Turborepo Features

This monorepo leverages Turborepo for:

- **Intelligent Caching**: Build outputs are cached and reused across runs
- **Parallel Execution**: Tasks run in parallel when possible
- **Task Dependencies**: Automatic dependency resolution between tasks
- **Remote Caching**: (Optional) Share cache across team and CI/CD

## License

This project is proprietary software. All rights reserved.

