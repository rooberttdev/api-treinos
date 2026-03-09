# Bootcamp Treinos API

API de gerenciamento de planos e sessões de treino construída com **Fastify 5**, **TypeScript**, **Prisma 7** e **Better-Auth**.

## 🎯 Sobre o Projeto

Uma API RESTful completa para criar, gerenciar e acompanhar planos de treino personalizados. A API permite:

- Criar e gerenciar planos de treino estruturados por dias da semana
- Definir exercícios com séries, repetições e tempo de descanso
- Registrar e atualizar sessões de treino em tempo real
- Rastrear estatísticas e progresso do treinamento
- Gerenciar dados biométricos do usuário

## 🛠️ Stack Tecnológico

| Tecnologia  | Versão  |
| ----------- | ------- |
| Node.js     | 24.x    |
| Fastify     | 5.7.4   |
| TypeScript  | 5.9.3   |
| Prisma ORM  | 7.4.0   |
| PostgreSQL  | 16      |
| Better-Auth | 1.4.18  |
| Zod         | 4.3.6   |
| pnpm        | 10.30.0 |

## 📋 Pré-requisitos

- Node.js 24.x
- pnpm 10.30.0
- Docker e Docker Compose (para PostgreSQL)

## 🚀 Quick Start

### 1. Instalação

```bash
# Clonar repositório
git clone <repository-url>
cd treinos-api

# Instalar dependências
pnpm install
```

### 2. Configurar Banco de Dados

```bash
# Iniciar PostgreSQL via Docker
docker-compose up -d

# Executar migrations do Prisma
pnpm exec prisma migrate dev

# (Opcional) Abrir Prisma Studio para visualizar dados
pnpm exec prisma studio
```

### 3. Variáveis de Ambiente

Criar arquivo `.env` na raiz do projeto:

```env
PORT=8080
DATABASE_URL=postgresql://user:password@localhost:5432/treinos_db
BETTER_AUTH_SECRET=seu-secret-key-aqui
BETTER_AUTH_URL=http://localhost:8080
```

### 4. Iniciar Servidor

```bash
# Desenvolvimento com hot-reload
pnpm dev
```

Servidor estará disponível em: `http://localhost:8080`

## 📚 Documentação da API

Quando o servidor está rodando, acesse:

- **Scalar UI** (Recomendado): `http://localhost:8080/docs`
- **Swagger JSON**: `http://localhost:8080/swagger.json`
- **Auth Schema**: `http://localhost:8080/api/auth/open-api/generate-schema`

## 🏗️ Arquitetura

### Padrão em Camadas: Routes → Use Cases → Prisma

```
src/
├── routes/          # Handlers HTTP (validação + autenticação)
├── usecases/        # Lógica de negócio pura
├── schemas/         # Validação com Zod
├── errors/          # Exceções customizadas
├── lib/             # Utilidades (auth, db)
└── generated/       # Prisma Client (auto-gerado)
```

**Fluxo de uma Requisição:**

1. **Route** — Valida request com Zod, extrai sessão de autenticação
2. **Use Case** — Executa lógica de negócio, interage com banco de dados
3. **Response** — Serializa resultado e retorna ao cliente

### Autenticação

Baseada em sessão com Better-Auth:

- Rotas públicas de auth: `/api/auth/*`
- Rotas protegidas extraem sessão via: `auth.api.getSession()`
- CORS configurado para: `http://localhost:3000`

### Banco de Dados

**Entidades Principais:**

- **User** — Usuário autenticado com dados biométricos
- **WorkoutPlan** — Plano de treino com múltiplos dias
- **WorkoutDay** — Dia específico (seg-dom) com exercícios
- **WorkoutExercise** — Exercício individual com séries/reps
- **WorkoutSession** — Registro de execução de um treino
- **Account & Session** — Gerenciados pelo Better-Auth

## 📡 Rotas Principais

### Autenticação

```
POST   /api/auth/sign-up         # Registrar novo usuário
POST   /api/auth/sign-in         # Fazer login
POST   /api/auth/sign-out        # Logout
```

### Planos de Treino

```
GET    /workout-plans             # Listar planos do usuário
GET    /workout-plans/:id         # Obter detalhes do plano
POST   /workout-plans             # Criar novo plano
GET    /workout-plans/:id/days/:dayId  # Obter dia específico
```

### Sessões de Treino

```
POST   /api/workout-sessions      # Iniciar sessão
PATCH  /api/workout-sessions/:id  # Atualizar sessão
```

### Dados do Usuário

```
GET    /me                        # Dados do usuário autenticado
GET    /home                      # Dados para dashboard
GET    /stats                     # Estatísticas de treino
GET    /ai                        # Endpoints de IA (se houver)
```

## 🔧 Scripts Disponíveis

```bash
# Desenvolvimento
pnpm dev                          # Start com hot-reload

# Banco de Dados
pnpm exec prisma migrate dev      # Executar migrations
pnpm exec prisma generate         # Gerar Prisma Client
pnpm exec prisma studio           # Abrir Prisma Studio

# Code Quality
pnpm exec eslint .                # Lint
pnpm exec prettier --write .      # Formatação
```

## 📝 Convenções

### TypeScript

- `strict: true` com target `ES2024`
- Module resolution: `nodenext`

### Code Style

- **Commits**: [Conventional Commits](https://www.conventionalcommits.org/)
  - `feat:` nova feature
  - `fix:` correção de bug
  - `docs:` documentação
  - `refactor:` refatoração

- **Imports**: Ordenados com `simple-import-sort`
- **Linting**: ESLint + Prettier

### Validation

- **Zod 4** com padrão `z.interface()`
- **WeekDay**: Sempre usar enum `z.enum(WeekDay)` (não strings)

## 🔐 Segurança

- CORS restrito a `http://localhost:3000`
- Autenticação baseada em sessão
- Senhas gerenciadas por Better-Auth
- Variáveis sensíveis em `.env` (gitignored)

## 📦 Dependências Principais

```json
{
  "dependencies": {
    "fastify": "5.7.4",
    "prisma": "7.4.0",
    "@prisma/client": "7.4.0",
    "better-auth": "1.4.18",
    "zod": "4.3.6",
    "typescript": "5.9.3",
    "tsx": "4.21.0"
  }
}
```

## 🚦 Status do Projeto

- ✅ Setup base com Fastify + TypeScript
- ✅ Autenticação com Better-Auth
- ✅ Banco de dados com Prisma
- ✅ Documentação OpenAPI (Scalar UI)
- ✅ Rotas de treino, sessões e stats
- ⏳ Testes automatizados (em planejamento)

## 💡 Desenvolvimento

### Adicionar Nova Rota

1. Define schema em `src/schemas/index.ts`
2. Cria use case em `src/usecases/`
3. Implementa rota em `src/routes/`
4. Registra em `src/index.ts`

### Adicionar Novo Modelo ao BD

1. Update schema em `prisma/schema.prisma`
2. Cria migration: `pnpm exec prisma migrate dev`
3. Use case interage com novo modelo

## 👨‍💻 Autor

**Robert Barbosa**
---

**Dúvidas?** Abra uma issue ou consulte a documentação em `/docs` quando o servidor estiver rodando.
