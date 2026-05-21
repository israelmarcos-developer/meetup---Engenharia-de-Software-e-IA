# Sistema de Agentes - Workflow Completo

Este documento explica como usar o sistema de agentes para desenvolvimento automatizado de features nas 3 stacks (Backend, Web, Mobile).

---

## 🎯 Visão Geral do Fluxo

```
Usuário descreve demanda
         ↓
    @Analista
         ↓
    @Engenheiro
         ↓
   @Orquestrador
         ↓
  [@Tester → @Implementador → @Revisor → @Auditor]
         ↓
    ✅ Feature Pronta
```

---

## 📋 Agentes Disponíveis

| Agente | Alias | Responsabilidade |
|--------|-------|------------------|
| **Agente Analista** | `@analista` | Elicitação de requisitos e criação de demanda |
| **Engenheiro de Software** | `@engenheiro` | Validação técnica e aprovação arquitetural |
| **Orquestrador de Features** | `@orquestrador` | Coordenação do workflow completo |
| **Agente Tester** | `@tester` | Criação de testes (TDD - Red) |
| **Agente Implementador TDD** | `@implementador` | Implementação Backend/Web (Green) |
| **Implementador Mobile** | `@implementador-app` | Implementação Mobile (Green) |
| **Agente Revisor** | `@revisor` | Revisão de código e padrões |
| **Agente Auditor Pré-Commit** | `@auditor` | Auditoria final antes de commit |

---

## 🚀 Como Usar (Passo a Passo)

### 1️⃣ Descrever Demanda ao Analista

**Você faz:**
```
@analista

Preciso criar uma funcionalidade de [descrição resumida].

[Detalhes adicionais que você já sabe]
```

**O que acontece automaticamente:**
1. Analista faz perguntas para esclarecer dúvidas
2. Você responde as perguntas
3. Analista cria arquivo de demanda em `docs/demandas/dem-XXX.md`
4. **Analista chama automaticamente o @Engenheiro**

---

### 2️⃣ Engenheiro Valida (Automático)

**O que acontece automaticamente:**
1. Engenheiro lê a demanda completa
2. Valida viabilidade técnica
3. Avalia complexidade (Baixa/Média/Alta)
4. **Se aprovado**, chama automaticamente o @Orquestrador

Se o Engenheiro identificar problemas, ele pedirá ajustes ao Analista.

---

### 3️⃣ Orquestrador Coordena (Automático)

**O que acontece automaticamente:**
1. Orquestrador lê a demanda
2. Identifica stacks impactadas (Backend/Web/Mobile)
3. **Chama @Tester** para criar testes (Red)
4. **Chama @Implementador** para Backend
5. **Chama @Implementador** para Web (se necessário)
6. **Chama @Implementador-app** para Mobile (se necessário)
7. **Chama @Revisor** para revisar tudo
8. **Chama @Auditor** para auditoria final
9. Gera relatório consolidado com comandos finais

---

### 4️⃣ Você Executa Comandos Finais

Após o Orquestrador gerar o relatório, você executa:

**Se houver migrations:**
```bash
docker exec -it events-php php bin/console doctrine:migrations:migrate
```

**Commit:**
```bash
git add .
git commit -m "feat(DEM-XXX): descrição breve"
git push origin feature/dem-xxx
```

**Criar PR no GitHub/GitLab**

---

## 📖 Exemplo Completo

### Passo 1: Você inicia
```
@analista

Preciso criar um endpoint para listar eventos públicos.
Os eventos devem ser filtrados por data e categoria.
```

### Passo 2: Analista interage
```
Analista: Entendi! Vou esclarecer alguns pontos:

1. Quem pode acessar esse endpoint? Usuários autenticados ou público geral?
2. A listagem deve ter paginação? Quantos itens por página?
3. Além de data e categoria, precisa de outros filtros?
4. Essa funcionalidade precisa estar no Web, Mobile ou ambos?
```

### Passo 3: Você responde
```
1. Público geral (não precisa autenticação)
2. Sim, 20 itens por página
3. Não, só data e categoria por enquanto
4. Precisa no Web e Mobile
```

### Passo 4: Sistema trabalha automaticamente
```
✅ Analista cria docs/demandas/backend/dem-003-listar-eventos-publicos.md
✅ Analista chama @Engenheiro
✅ Engenheiro valida e aprova (Complexidade: Baixa)
✅ Engenheiro chama @Orquestrador
✅ Orquestrador chama @Tester → cria testes Backend, Web, Mobile
✅ Orquestrador chama @Implementador → implementa Backend
✅ Orquestrador chama @Implementador → implementa Web
✅ Orquestrador chama @Implementador-app → implementa Mobile
✅ Orquestrador chama @Revisor → revisa tudo
✅ Orquestrador chama @Auditor → auditoria final
✅ Orquestrador gera relatório consolidado
```

### Passo 5: Você finaliza
Copiar comandos do relatório e executar:
```bash
# Commit
git add .
git commit -m "feat(DEM-003): endpoint de listagem de eventos públicos"
git push origin feature/dem-003

# Criar PR
```

---

## 🎨 Casos de Uso Comuns

### Criar Nova Feature
```
@analista
Preciso criar funcionalidade de [X] que faz [Y].
```

### Corrigir Bug
```
@analista
Existe um bug em [local] onde [problema]. 
O comportamento esperado é [X].
```

### Refatorar Código
```
@analista
Preciso refatorar [componente/serviço] para [objetivo].
```

### Adicionar Validação
```
@analista
Preciso adicionar validação em [campo/formulário] para [regra].
```

---

## ⚡ Atalhos

### Se você já tem demanda documentada
Pule o Analista e vá direto ao Orquestrador:
```
@orquestrador
Executar workflow completo para demanda docs/demandas/dem-XXX.md
```

### Se precisa só implementar (sem testes)
Use o Engenheiro diretamente:
```
@engenheiro
Implementar [descrição breve do que precisa]
```

### Se precisa só criar testes
```
@tester
Criar testes para [feature/componente]
```

---

## 🔍 Estrutura de Arquivos de Demanda

As demandas são criadas em:

```
docs/demandas/
├── backend/
│   └── dem-XXX-nome.md
├── web/
│   └── dem-XXX-nome.md
└── mobile/
    └── dem-XXX-nome.md
```

Cada demanda segue o template de `docs/modelo.md` com:
- Tipo, Contexto, Objetivo
- Atores, Regras de Negócio
- Fluxo Funcional, Saída Esperada
- Casos de Teste, Edge Cases
- Integrações, Critérios de Aceite

---

## 🛡️ Garantias do Sistema

✅ **TDD Rigoroso**: Sempre Red → Green  
✅ **Ordem Correta**: Backend → Web → Mobile  
✅ **Validação Contínua**: Testes rodam após cada implementação  
✅ **Padrões Seguidos**: Cada stack segue suas convenções  
✅ **Auditoria Final**: Checklist completo antes de commit  
✅ **Zero Suposições**: Tudo baseado em demanda documentada  

---

## 🆘 Troubleshooting

### "Analista não está criando a demanda"
- Verifique se você está usando `@analista` corretamente
- Responda todas as perguntas do Analista
- Confirme que você está no workspace correto

### "Engenheiro não aprovou"
- Leia as objeções técnicas
- Pode haver inviabilidade ou risco
- Discuta alternativas com o Engenheiro

### "Testes não estão passando"
- O Implementador deve fazer testes passarem
- Se persistir, revise a demanda ou testes
- Use `@revisor` para identificar problemas

### "Orquestrador não está coordenando"
- Certifique-se que o Engenheiro aprovou primeiro
- Verifique se a demanda está bem documentada
- Tente chamar `@orquestrador` manualmente

---

## 📚 Próximos Passos

Após se familiarizar com o fluxo básico:

1. **Personalize o template** de demanda em `docs/modelo.md`
2. **Ajuste os agentes** em `.github/agents/` conforme seu processo
3. **Crie novos agentes** especializados se necessário
4. **Documente padrões** específicos do seu projeto

---

## 🤝 Contribuindo

Para melhorar o sistema de agentes:

1. Identifique gargalos no workflow
2. Proponha melhorias nos arquivos `.agent.md`
3. Teste com demandas reais
4. Documente aprendizados

---

**Dúvidas?** Pergunte ao `@analista` - ele está aqui para ajudar! 😊
