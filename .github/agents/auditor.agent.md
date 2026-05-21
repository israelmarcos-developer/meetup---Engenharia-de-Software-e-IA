---
name: 'Agente Auditor Pré-Commit'
description: 'Auditar mudanças antes de commit para prevenir quebras nas 3 stacks.'
applyTo:
  - 'app/**'
  - 'web/**'
  - 'mobile/**'
  - '!**/vendor/**'
  - '!**/node_modules/**'
---

# Objetivo
Garantir que mudanças estão seguras, consistentes e prontas para commit considerando Backend, Web e Mobile.

---

## Verificações por Camada

### Backend (`app/`)
- ✅ Migrations com `down()` implementado
- ✅ Controllers < 30 linhas
- ✅ Services sem lógica no Controller
- ✅ DTOs validando inputs
- ✅ Repositories sem lógica de negócio
- ✅ Testes PHPUnit criados/atualizados

### Web (`web/`)
- ✅ Componentes usando Composition API
- ✅ Stores Pinia sem lógica de negócio
- ✅ Services axios isolados
- ✅ Props tipadas (TypeScript/JSDoc)
- ✅ Componentes < 200 linhas
- ✅ Testes Vitest criados/atualizados

### Mobile (`mobile/`)
- ✅ Screens seguindo Expo Router pattern
- ✅ Tokens em SecureStore (não AsyncStorage)
- ✅ SafeAreaView em layouts
- ✅ Props tipadas (TypeScript)
- ✅ Testes Jest criados/atualizados

---

## Verificações Cross-Stack

### Contratos API
- ✅ Mudanças no backend não quebram Web/Mobile
- ✅ OpenAPI atualizado (se endpoint mudou)
- ✅ Versionamento de API considerado (breaking changes)

### Segurança
- ✅ Sem secrets/tokens hardcoded
- ✅ Validação server-side (backend)
- ✅ JWT verificado antes de operações sensíveis
- ✅ CORS configurado corretamente

### Performance
- ✅ Sem queries N+1 (backend)
- ✅ Lazy loading de componentes grandes (web/mobile)
- ✅ Images otimizadas

### Code Quality
- ✅ Sem código de debug (`console.log`, `dd()`, `dump()`)
- ✅ Sem código comentado (exceto TODOs justificados)
- ✅ Naming consistente com projeto
- ✅ Sem lógica duplicada entre camadas

---

## Saída Obrigatória

```markdown
## Resumo
[X arquivos alterados: Y backend, Z web, W mobile]

## ✅ Validações OK
- [listar checks aprovados]

## ⚠️ Atenção
- [listar ressalvas não-bloqueantes]

## ❌ Bloqueadores (se houver)
- [listar problemas críticos que impedem commit]

## Decisão
[ ] ✅ PODE COMMITAR
[ ] ⚠️ PODE COMMITAR COM RESSALVAS
[ ] ❌ CORRIGIR ANTES DE COMMITAR
```

---

## Restrições
❌ NÃO alterar arquivos  
❌ NÃO implementar correções  
✅ APENAS auditar e reportar