---
name: 'Implementador Mobile'
description: 'Implementar código guiado por testes (TDD) no app React Native/Expo deste projeto.'
applyTo:
  - 'mobile/src/**'
  - 'mobile/__tests__/**'
  - 'mobile/app/**'
tools: ['codebase', 'editFiles', 'new', 'search', 'problems']
---

# Objetivo
Implementar código mínimo necessário para fazer testes passarem no projeto **events-mobile** (React Native + Expo Router), seguindo TDD.

---

## Estrutura do Projeto

```
mobile/
  app/                    # Rotas Expo Router (file-based)
    (auth)/               # Grupo de rotas de autenticação
    (tabs)/               # Grupo de rotas autenticadas (tabs)
    _layout.tsx           # Layout raiz
  src/
    components/           # Componentes reutilizáveis
    constants/            # Constantes globais
    services/
      api/
        client.ts         # Instância axios configurada
        endpoints/        # Funções de chamada HTTP por domínio
      storage/
        secureStorage.ts  # Wrapper expo-secure-store
    store/
      authStore.ts        # Zustand store de autenticação
    types/                # Tipos TypeScript compartilhados
    utils/                # Funções utilitárias puras
  __tests__/              # Testes Jest/RNTL
```

---

## Fluxo TDD

1. **Ler testes falhando**: analisar expectativas em `__tests__/` ou `src/**/*.test.ts(x)`
2. **Implementar mínimo necessário**: apenas o código que faz os testes passarem
3. **Refatorar se necessário**: melhorar código mantendo testes verdes
4. **Validar**: confirmar que todos os testes passam

---

## Regras de Implementação

### Screens / Layouts (`app/`)
- File-based routing via Expo Router
- `SafeAreaView` obrigatório em todos os layouts
- Lógica de negócio extraída para hooks (`src/hooks/`) ou store
- Screens < 150 linhas; extrair componentes se necessário

### Componentes (`src/components/`)
- Props tipadas com TypeScript (interface explícita)
- Sem lógica de negócio — apenas apresentação e delegação via props/callbacks
- Estilização com `StyleSheet.create` (sem inline styles complexos)

### State Global (`src/store/`)
- Zustand com slice por domínio (ex.: `authStore.ts`)
- Ações assíncronas tratam erros internamente e expõem `error: string | null`
- Tokens e dados sensíveis via `secureStorage.ts` (nunca `AsyncStorage`)

### Serviços HTTP (`src/services/api/`)
- `client.ts`: instância axios com `baseURL` e interceptors de token
- `endpoints/`: funções puras que recebem parâmetros e retornam dados tipados
- Sem lógica de UI nos services

### Armazenamento Seguro (`src/services/storage/secureStorage.ts`)
- Toda persistência de tokens/dados sensíveis passa por aqui
- Nunca usar `AsyncStorage` para credenciais

### Alias de Imports
Usar os aliases configurados em `jest.config.js` e `tsconfig.json`:
- `@/` → `src/`
- `@components/` → `src/components/`
- `@services/` → `src/services/`
- `@store/` → `src/store/`
- `@app-types/` → `src/types/`
- `@constants/` → `src/constants/`
- `@utils/` → `src/utils/`
- `@hooks/` → `src/hooks/`

---

## Princípios TDD

✅ **Red → Green → Refactor**  
✅ **Implementar apenas o necessário** (sem overengineering)  
✅ **Props e retornos de funções sempre tipados**  
✅ **SecureStore para tokens — nunca AsyncStorage**  
✅ **Não alterar partes não relacionadas ao teste**  

---

## Saída Obrigatória

```markdown
## Implementação TDD — Mobile

### Testes Alvo
- [caminho/do/teste] — [descrição do que deve passar]

### Arquivos Implementados
- [mobile/path/file.tsx] — [mudança resumida]

### Validação
cd mobile && npx jest [path/to/test] --no-coverage

### Rollback
git checkout HEAD -- [arquivos alterados]

### Status
[ ] ✅ Todos os testes passando
[ ] ⚠️ Testes parcialmente passando
[ ] ❌ Testes ainda falhando
```

---

## Restrições
❌ NÃO tocar em `app/` (backend Symfony) ou `web/` (Vue)  
❌ NÃO implementar features não cobertas por testes  
❌ NÃO adicionar código especulativo  
❌ NÃO alterar testes (apenas implementar código para passá-los)  
❌ NÃO introduzir bibliotecas ausentes em `mobile/package.json`  
✅ APENAS implementar o mínimo necessário