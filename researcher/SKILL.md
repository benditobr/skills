---
name: lead-researcher-v3
description: Pipeline de triagem e qualificação de leads B2B para a Bendito, operado via Supabase MCP. Use esta skill SEMPRE que o usuário pedir qualquer coisa relacionada a leads: "rodar triagem", "processar leads", "pesquisar leads", "qualificar leads", "triagem de leads", "rodar o pipeline", "ver leads pendentes". A skill lê os leads com status "Triagem" na tabela leads do Supabase, pesquisa cada um e grava os resultados de volta no próprio registro. Processa em blocos de 7 por rodada. Resposta no chat: apenas "Triagem realizada." Nada mais.
---

# Lead Researcher v3

Pipeline silencioso de qualificação de leads B2B da Bendito.

## Arquivos de referência

Consulte conforme indicado em cada passo — não carregue antecipadamente:

- `references/supabase-schema.md` — schema da tabela, SQL da migration, queries de leitura e atualização. **Leia antes do Passo 1.**
- `references/research-playbook.md` — estratégias de busca por tipo de estabelecimento, sinais de fit, heurísticas de score. **Leia antes do Passo 3.**

---

## Regras invioláveis

- Nunca alterar: `name`, `phone`, `address`, `user_id`, `lead_type`
- Se o UPDATE falhar, o lead permanece em `Triagem` para reprocessamento
- Resposta final ao usuário: apenas `Triagem realizada.` — nada mais
- Se a fila estiver vazia, encerre silenciosamente, sem nenhuma mensagem

---

## Fluxo principal

### Passo 0 — Verificar fila

Leia `references/supabase-schema.md` (seção "Ler fila de triagem").
Execute a query. Se retornar 0 leads, encerre sem emitir mensagem.

---

### Passo 1 — Iniciar lote

Processe até 7 leads por rodada.
Para cada lead, execute os Passos 2–5 integralmente antes de passar ao próximo.

---

### Passo 2 — Pesquisar na web

Leia `references/research-playbook.md` (seção "Estratégias de busca") antes de começar.

Para cada lead, busque:
- Google Maps — nome + bairro/cidade
- Instagram — perfil oficial
- iFood e Rappi — presença em delivery
- Site próprio (iFood/Rappi não contam como site)

Registre em `search_hypotheses` todas as variações de busca tentadas, separadas por `;`.

---

### Passo 3 — Avaliar fit

Consulte `references/research-playbook.md` (seção "Sinais de fit e score").

Atribua:
- `fit_label`: Alto / Médio / Baixo / Inconclusivo
- `priority_score`: 1 a 5
- `score_justification`: 1–2 linhas explicando o score
- `strategic_summary`: 3–5 linhas com achados, fit percebido e sugestão de abordagem
- `cold_approach_notes`: notas úteis mesmo para baixo fit

---

### Passo 4 — Definir status de saída

| Condição                        | Status final  |
|---------------------------------|---------------|
| Score 4–5                       | `Contato`     |
| Score 2–3                       | `Novo`        |
| Score 1                         | `Descartado`  |
| Negócio não identificável        | `Descartado`  |
| Dados insuficientes              | `Novo`        |

---

### Passo 5 — Gravar no Supabase

Leia `references/supabase-schema.md` (seção "Atualizar lead após pesquisa").
Execute o UPDATE com todos os campos preenchidos.
Sempre inclua `updated_at = now()` e `researched_at = now()`.

---

## Resposta ao usuário

Após processar todos os leads do lote, responda apenas:

```
Triagem realizada.
```
