# 🚀 Guia de Migração para Monorepo - Git e Deploy

Este documento explica como atualizar o repositório Git e configurar o deploy no Vercel após a migração para monorepo.

## 📋 Índice

1. [Atualizando o Repositório Git](#atualizando-o-repositório-git)
2. [Configuração do Vercel para Monorepo](#configuração-do-vercel-para-monorepo)
3. [Atualizando Scripts Git](#atualizando-scripts-git)

---

## 🔄 Atualizando o Repositório Git

### Se você já tem um repositório Git existente:

1. **Verificar o estado atual:**
   ```bash
   git status
   git branch
   ```

2. **Criar uma branch para a migração:**
   ```bash
   git checkout -b feature/migrate-to-monorepo
   ```

3. **Adicionar todas as mudanças:**
   ```bash
   git add .
   git status  # Verificar o que será commitado
   ```

4. **Fazer commit das mudanças:**
   ```bash
   git commit -m "feat: migrate project to Turborepo monorepo structure

   - Move waitlist app to apps/waitlist
   - Add Turborepo configuration (turbo.json)
   - Add pnpm workspace configuration
   - Update root package.json with monorepo scripts
   - Move pnpm overrides to root
   - Update Vercel configuration for monorepo
   - Add root README with monorepo documentation"
   ```

5. **Push da branch:**
   ```bash
   git push origin feature/migrate-to-monorepo
   ```

6. **Criar Pull Request:**
   ```bash
   gh pr create --title "Migrate to Turborepo Monorepo" --body "This PR migrates the project to a Turborepo monorepo structure. The waitlist app has been moved to apps/waitlist and all configurations have been updated."
   ```

### Se você está iniciando um novo repositório:

1. **Inicializar Git (se ainda não foi feito):**
   ```bash
   git init
   git branch -M main
   ```

2. **Adicionar remote (se aplicável):**
   ```bash
   git remote add origin https://github.com/seu-usuario/flowly.git
   ```

3. **Criar branch develop:**
   ```bash
   git checkout -b develop
   ```

4. **Fazer commit inicial:**
   ```bash
   git add .
   git commit -m "feat: initial monorepo setup with Turborepo

   - Turborepo monorepo structure
   - Waitlist app in apps/waitlist
   - pnpm workspace configuration
   - Root package.json with Turborepo scripts"
   ```

5. **Push inicial:**
   ```bash
   git push -u origin develop
   ```

---

## 🚀 Configuração do Vercel para Monorepo

### Opção 1: Configuração via Dashboard do Vercel (Recomendado)

1. **Acesse o projeto no Vercel Dashboard**
   - Vá para [vercel.com](https://vercel.com)
   - Selecione o projeto `waitlist` (ou o nome do seu projeto)

2. **Configurar Settings → General:**
   - **Root Directory**: `apps/waitlist`
   - **Build Command**: `cd ../.. && pnpm --filter waitlist build`
   - **Install Command**: `cd ../.. && pnpm install`
   - **Output Directory**: `dist`

3. **Configurar Environment Variables:**
   - Mantenha as mesmas variáveis de ambiente que você já tinha
   - `LOOPS_API_KEY` (se aplicável)
   - `VITE_APP_URL`, `VITE_APP_NAME`, etc. (se necessário)

4. **Salvar e fazer novo deploy**

### Opção 2: Usar vercel.json (Já configurado)

O arquivo `apps/waitlist/vercel.json` já está configurado para monorepo:

```json
{
  "version": 2,
  "buildCommand": "cd ../.. && pnpm --filter waitlist build",
  "installCommand": "cd ../.. && pnpm install",
  "outputDirectory": "dist"
}
```

**Importante**: No Vercel Dashboard, você **DEVE** configurar:
- **Root Directory**: `apps/waitlist` (esta propriedade não pode estar no vercel.json)

### Opção 3: Usar vercel.json na Raiz (Alternativa)

Se preferir, você pode criar um `vercel.json` na raiz do monorepo:

```json
{
  "version": 2,
  "builds": [
    {
      "src": "apps/waitlist/package.json",
      "use": "@vercel/static-build",
      "config": {
        "distDir": "apps/waitlist/dist"
      }
    }
  ],
  "routes": [
    {
      "src": "/(.*)",
      "dest": "apps/waitlist/$1"
    }
  ]
}
```

**Nota**: A Opção 1 (Dashboard) é geralmente mais simples e recomendada.

---

## 🔧 Atualizando Scripts Git

O script `apps/waitlist/scripts/git-workflow.sh` precisa ser atualizado para funcionar na raiz do monorepo.

### Opção 1: Mover script para a raiz

```bash
# Mover o script para a raiz
mv apps/waitlist/scripts/git-workflow.sh scripts/git-workflow.sh

# Tornar executável
chmod +x scripts/git-workflow.sh
```

### Opção 2: Criar novo script na raiz

Crie um novo script `scripts/git-workflow.sh` na raiz que funciona com o monorepo:

```bash
#!/bin/bash
# Git Workflow Helper Script for Flowly Monorepo
# Usage: ./scripts/git-workflow.sh <command> [options]

set -e

# Colors for output
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
BLUE='\033[0;34m'
NC='\033[0m' # No Color

# Helper functions
print_help() {
    echo -e "${BLUE}Git Workflow Helper for Flowly Monorepo${NC}"
    echo ""
    echo "Usage: $0 <command> [options]"
    echo ""
    echo "Commands:"
    echo "  start <feature-name>    Create a new feature branch from develop"
    echo "  finish                 Complete current feature and create PR"
    echo "  cleanup                Clean up merged feature branches"
    echo "  sync                   Sync with develop branch"
    echo "  status                 Show current git status"
    echo "  help                   Show this help message"
    echo ""
}

start_feature() {
    local feature_name=$1
    if [ -z "$feature_name" ]; then
        echo -e "${RED}Error: Feature name is required${NC}"
        exit 1
    fi
    
    echo -e "${YELLOW}Starting new feature: $feature_name${NC}"
    
    git checkout develop
    git pull origin develop 2>/dev/null || echo "No remote origin set yet"
    git checkout -b "feature/$feature_name"
    
    echo -e "${GREEN}✓ Created feature branch: feature/$feature_name${NC}"
}

finish_feature() {
    local current_branch=$(git branch --show-current)
    
    if [[ ! $current_branch == feature/* ]]; then
        echo -e "${RED}Error: Not on a feature branch${NC}"
        exit 1
    fi
    
    echo -e "${YELLOW}Finishing feature: $current_branch${NC}"
    git push origin "$current_branch"
    
    echo -e "${GREEN}✓ Pushed branch. Create PR on GitHub.${NC}"
}

# Main command handler
case "$1" in
    start)
        start_feature "$2"
        ;;
    finish)
        finish_feature
        ;;
    *)
        print_help
        exit 1
        ;;
esac
```

---

## ✅ Checklist de Migração

- [ ] Repositório Git atualizado com todas as mudanças
- [ ] Branch de feature criada para a migração
- [ ] Pull Request criado (se aplicável)
- [ ] Vercel Dashboard configurado com Root Directory: `apps/waitlist`
- [ ] Build Command no Vercel: `cd ../.. && pnpm --filter waitlist build`
- [ ] Install Command no Vercel: `cd ../.. && pnpm install`
- [ ] Environment Variables verificadas no Vercel
- [ ] Deploy de teste realizado no Vercel
- [ ] Scripts Git atualizados (se necessário)

---

## 🧪 Testando o Deploy

1. **Fazer um commit de teste:**
   ```bash
   git checkout develop
   git pull origin develop
   git checkout -b test/deploy-monorepo
   # Fazer uma pequena mudança
   git add .
   git commit -m "test: verify monorepo deploy"
   git push origin test/deploy-monorepo
   ```

2. **Verificar o deploy no Vercel:**
   - O Vercel deve detectar automaticamente o push
   - Verificar os logs do build
   - Confirmar que o build está usando os comandos corretos

3. **Se o deploy falhar:**
   - Verificar os logs do Vercel
   - Confirmar que o Root Directory está correto
   - Verificar que os comandos de build estão corretos
   - Confirmar que as dependências estão sendo instaladas corretamente

---

## 📚 Recursos Adicionais

- [Turborepo Documentation](https://turbo.build/repo/docs)
- [Vercel Monorepo Guide](https://vercel.com/docs/monorepos)
- [pnpm Workspaces](https://pnpm.io/workspaces)

---

## 🆘 Troubleshooting

### Erro: "Cannot find module" no Vercel
- Verificar que o `rootDirectory` está configurado corretamente
- Confirmar que o `installCommand` está instalando na raiz do monorepo

### Erro: "Build command failed"
- Verificar que o caminho do build está correto
- Confirmar que o `--filter waitlist` está funcionando
- Verificar logs completos no Vercel

### Erro: "Package not found"
- Verificar que todas as dependências estão no `package.json` correto
- Confirmar que o `pnpm install` está sendo executado na raiz

