# CONTEXTO GLOBAL DO PROJETO

## Arquitetura Multi-Camada

Projeto com 3 stacks independentes que se comunicam via API REST:

```
┌──────────────────────────────────────────────────────┐
│  Backend (API)          │  Frontend Web  │  Mobile   │
│  PHP/Symfony (Docker)   │  Vue (Docker)  │  Expo     │
│  app/                   │  web/          │  mobile/  │
└──────────────────────────────────────────────────────┘
```

---

## Stack por Camada

### Backend (`app/`)
* **Runtime**: PHP 8.4 · Symfony 8.0 · FPM
* **Database**: PostgreSQL 18 (Doctrine ORM 3.6 + Migrations 4.0)
* **Auth**: Lexik JWT 3.2 + Refresh Tokens
* **API**: REST (JSON) + Nelmio ApiDoc (OpenAPI 3.0)
* **Testes**: PHPUnit 12 · Mockery 2
* **Ambiente**: Docker (`php:8.4-fpm-alpine`)
* **Estrutura**: DDD-light (Entity, Repository, Service, Controller, DTO)

### Frontend Web (`web/`)
* **Framework**: Vue 3.4 (Composition API + `<script setup>`)
* **UI**: Quasar 2.19 (Material Design Components)
* **State**: Pinia 3.0
* **Router**: Vue Router 4.6
* **HTTP**: Axios 1.15
* **Build**: Vite 5.4 + HMR
* **Testes**: Vitest 1.6 · Vue Test Utils 2.4
* **Ambiente**: Docker (Node 22 Alpine)

### Mobile (`mobile/`)
* **Framework**: React Native 0.81.5 · Expo 54
* **Routing**: Expo Router 6.0 (file-based)
* **UI**: React Native Paper 5.14
* **State**: Zustand 5.0
* **Storage**: Expo SecureStore 15.0
* **HTTP**: Axios 1.7
* **Testes**: Jest 29 · Testing Library (React Native 12.9)
* **Ambiente**: Local (Metro Bundler)

---

## Regras Arquiteturais Globais

* **Contrato único**: OpenAPI gerado pelo backend é a fonte de verdade
* **Autenticação stateless**: JWT em `Authorization: Bearer <token>`
* **Sem duplicação de lógica**: validação e regras de negócio ficam no backend
* **Código simples e legível**: prefer clareza sobre cleverness
* **Seguir padrão existente**: analisar código similar antes de criar novo

---

## Regras por Camada

### Backend (`app/`)
* Controllers **devem** ser finos (máximo 30 linhas)
* Regras de negócio **devem** ficar em Services ou Entity methods
* Validação **deve** ser via Symfony Validator (DTO) ou Assert
* Migrations **nunca** são executadas por agentes (gerar SQL manual)
* Commands **devem** ter descrição e help text
* Repositories **não** devem conter lógica de negócio

### Frontend Web (`web/`)
* Componentes **devem** usar Composition API + `<script setup>`
* State global **somente** via Pinia (evitar prop drilling desnecessário)
* Services HTTP **devem** estar em `src/services/`
* Componentes **devem** ser Small Single Responsibility (< 200 linhas)
* Quasar components **preferir** aos nativos (exceto quando desnecessário)
* Rotas **devem** ter `meta` para guards e títulos

### Mobile (`mobile/`)
* Screens **devem** seguir padrão Expo Router (file-based em `app/`)
* State **preferir** Zustand para global, React hooks para local
* Tokens **devem** usar SecureStore (nunca AsyncStorage para credenciais)
* Layouts **devem** usar SafeAreaView
* Componentes **devem** estar em `src/components/`
* Navigation **usar** Expo Router (não React Navigation direto)

---

## Regras de Desenvolvimento

* **Toda demanda** deve vir de arquivo em `/docs/demandas/`
* **Nenhuma implementação** sem análise prévia do impacto
* **Testes primeiro** (TDD) para novas funcionalidades
* **Branch separada** sempre (nunca commit direto em `main`)
* **Rollback plan** obrigatório em toda mudança estrutural
* **Evidências primeiro**: decisões baseadas em código existente (linhas, paths, padrões)

---

## Regras de Testes

### Backend
* Testes unitários (Services, Entities) com mocks de dependências
* Testes de feature (Controllers) com `WebTestCase`
* Coverage mínimo: 80% em Services críticos
* Mocks para: emails, filas, APIs externas

### Frontend Web
* Testes unitários (composables, stores) com Vitest
* Testes de componente com Vue Test Utils
* Mocks para: axios, router, stores

### Mobile
* Testes unitários (hooks, utils) com Jest
* Testes de componente com Testing Library
* Mocks para: SecureStore, axios, Expo APIs

### Cobertura Obrigatória
* Fluxo principal (happy path)
* Validações e erros (4xx, 5xx)
* Edge cases (valores extremos, null, undefined)

---

## Comunicação Entre Camadas

```
Mobile/Web → Backend API
   ↓              ↓
JWT Auth    PostgreSQL
   ↓
SecureStore/localStorage
```

**Fluxo padrão**:
1. Frontend faz requisição com `Authorization: Bearer <token>`
2. Backend valida JWT, executa lógica, retorna JSON
3. Frontend atualiza state e UI

**Contratos**:
* Backend expõe OpenAPI em `/api/doc`
* Frontends **devem** consumir exatamente as rotas documentadas
* Mudanças de contrato **devem** ser versionadas (`/api/v1`, `/api/v2`)

---

## Estrutura de Diretórios

```
app/
  src/
    Controller/Api/    → Endpoints REST
    Entity/            → Modelos Doctrine
    Repository/        → Queries customizadas
    Service/           → Lógica de negócio
    Dto/               → Validação de input
  tests/
    Feature/           → Testes de API
    Unit/              → Testes de Services

web/
  src/
    components/        → Componentes Vue reutilizáveis
    composables/       → Logic reutilizável (Composition API)
    pages/             → Views mapeadas em rotas
    router/            → Definição de rotas
    services/          → HTTP clients (axios)
    stores/            → State management (Pinia)

mobile/
  app/
    (auth)/            → Screens de autenticação
    (tabs)/            → Screens principais (bottom tabs)
  src/
    components/        → Componentes React Native
    services/          → HTTP clients (axios)
    store/             → State management (Zustand)
    types/             → TypeScript interfaces
```

---

## Restrições de Agentes

### Permissões
✅ Criar arquivos (`new`)  
✅ Editar arquivos (`editFiles`)  
✅ Propor refatorações  
✅ Gerar SQL/comandos CLI (entregar no chat)

### Proibições
❌ Executar comandos no terminal  
❌ Executar migrations  
❌ Executar seeds  
❌ Fazer deploys  
❌ Modificar estado de sistemas externos  
❌ Introduzir bibliotecas não presentes no projeto sem aprovação  

---

## Objetivo

Garantir desenvolvimento **consistente, testável e escalável** em **3 stacks independentes** utilizando IA como ferramenta de automação, mantendo controle humano sobre operações críticas.
