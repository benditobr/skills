# Supabase Schema — Lead Researcher Bendito

Projeto: **myfood**
Project ID: `qteiubyvvespwifzumwu`
Tabela principal: `public.leads`

---

## Colunas de entrada (já existem)

| Coluna       | Tipo        | Conteúdo                                      |
|--------------|-------------|-----------------------------------------------|
| id           | uuid        | Chave primária, gerada automaticamente        |
| user_id      | uuid        | Referência ao usuário dono do lead            |
| name         | text        | Nome do estabelecimento ou responsável        |
| address      | text        | Endereço no formato "Bairro, Cidade"          |
| phone        | text        | Telefone do lead                              |
| segment      | text        | Segmento do negócio (preenchido após pesquisa)|
| status       | text        | Status do pipeline — ver valores válidos      |
| lead_type    | text        | Interno ou Externo                            |
| created_at   | timestamptz | Data de criação                               |
| updated_at   | timestamptz | Última atualização                            |

---

## Status válidos

| Status     | Significado                                                    |
|------------|----------------------------------------------------------------|
| Novo       | Lead recém-cadastrado ou pesquisado com fit médio/baixo        |
| Triagem    | Aguardando pesquisa pelo pipeline                              |
| Contato    | Pesquisado, fit alto — prioridade de contato                   |
| Descartado | Negócio não identificável ou sem alinhamento                   |
| Proposta   | Em negociação ativa                                            |
| Fechado    | Convertido em cliente                                          |

---

## Migration necessária

Rodar uma única vez no SQL Editor do Supabase antes de usar o pipeline:

```sql
-- 1. Remover constraint atual de status
ALTER TABLE leads DROP CONSTRAINT IF EXISTS leads_status_check;

-- 2. Adicionar novo constraint com todos os valores válidos
ALTER TABLE leads
  ADD CONSTRAINT leads_status_check
  CHECK (status = ANY (ARRAY[
    'Novo',
    'Triagem',
    'Contato',
    'Descartado',
    'Proposta',
    'Fechado'
  ]));

-- 3. Adicionar colunas de resultado de pesquisa
ALTER TABLE leads
  ADD COLUMN IF NOT EXISTS establishment_type   text,
  ADD COLUMN IF NOT EXISTS maps_url             text,
  ADD COLUMN IF NOT EXISTS maps_rating          text,
  ADD COLUMN IF NOT EXISTS maps_reviews         integer,
  ADD COLUMN IF NOT EXISTS instagram_url        text,
  ADD COLUMN IF NOT EXISTS instagram_followers  text,
  ADD COLUMN IF NOT EXISTS instagram_active     text,
  ADD COLUMN IF NOT EXISTS site_url             text,
  ADD COLUMN IF NOT EXISTS delivery_platforms   text,
  ADD COLUMN IF NOT EXISTS search_hypotheses    text,
  ADD COLUMN IF NOT EXISTS confirmed_data       text,
  ADD COLUMN IF NOT EXISTS inconsistencies      text,
  ADD COLUMN IF NOT EXISTS strategic_summary    text,
  ADD COLUMN IF NOT EXISTS fit_label            text,
  ADD COLUMN IF NOT EXISTS priority_score       integer,
  ADD COLUMN IF NOT EXISTS score_justification  text,
  ADD COLUMN IF NOT EXISTS cold_approach_notes  text,
  ADD COLUMN IF NOT EXISTS researched_at        timestamptz,
  ADD COLUMN IF NOT EXISTS research_status      text;
```

---

## Ler fila de triagem (Passo 0)

```sql
SELECT id, name, address, phone, segment
FROM leads
WHERE status = 'Triagem'
ORDER BY created_at ASC
LIMIT 7;
```

---

## Atualizar lead após pesquisa (Passo 5)

```sql
UPDATE leads SET
  establishment_type   = $1,
  maps_url             = $2,
  maps_rating          = $3,
  maps_reviews         = $4,
  instagram_url        = $5,
  instagram_followers  = $6,
  instagram_active     = $7,
  site_url             = $8,
  delivery_platforms   = $9,
  search_hypotheses    = $10,
  confirmed_data       = $11,
  inconsistencies      = $12,
  strategic_summary    = $13,
  fit_label            = $14,
  priority_score       = $15,
  score_justification  = $16,
  cold_approach_notes  = $17,
  researched_at        = now(),
  research_status      = $18,
  status               = $19,
  updated_at           = now()
WHERE id = $20;
```

---

## Mapa de colunas de pesquisa

| Coluna               | O que registrar                                                                 |
|----------------------|---------------------------------------------------------------------------------|
| establishment_type   | Padaria / Lanchonete / Hamburgueria / Cafeteria / Bistrô / Pizzaria / Restaurante / Outro |
| maps_url             | URL completa do Maps ou null                                                    |
| maps_rating          | Ex: `4.3` ou null                                                               |
| maps_reviews         | Número inteiro de avaliações ou null                                            |
| instagram_url        | URL do perfil ou null                                                           |
| instagram_followers  | Número aproximado em texto (ex: `"12.4k"`) ou null                             |
| instagram_active     | `Sim` (últimos 30 dias) / `Inativo` / `Privado` / null                         |
| site_url             | URL do site próprio ou null (iFood/Rappi **não** contam)                       |
| delivery_platforms   | `iFood` / `Rappi` / `Ambos` / null                                             |
| search_hypotheses    | Variações de busca tentadas, separadas por `;`                                 |
| confirmed_data       | Fatos verificados com fonte, separados por `;`                                 |
| inconsistencies      | Divergências entre fontes ou null                                               |
| strategic_summary    | 3–5 linhas com achados, fit percebido e sugestão de abordagem                  |
| fit_label            | `Alto` / `Médio` / `Baixo` / `Inconclusivo`                                   |
| priority_score       | Número de 1 a 5                                                                 |
| score_justification  | Frase curta (1–2 linhas) justificando o score                                  |
| cold_approach_notes  | Notas úteis mesmo para baixo fit (ex: "ligar pela manhã")                      |
| researched_at        | Timestamp automático via `now()`                                               |
| research_status      | `Concluído` / `Parcial` / `Sem dados`                                          |

---

## Consultas de verificação

```sql
-- Distribuição por status
SELECT status, count(*) FROM leads GROUP BY status ORDER BY count DESC;

-- Leads prontos para contato
SELECT name, address, phone, maps_rating, instagram_url, priority_score, strategic_summary
FROM leads
WHERE status = 'Contato'
ORDER BY priority_score DESC;

-- Fila atual de triagem
SELECT name, address, phone, created_at
FROM leads
WHERE status = 'Triagem'
ORDER BY created_at ASC;
```

---

## Notas de robustez

- Nunca alterar `name`, `phone`, `address`, `user_id` ou `lead_type` durante a pesquisa
- Se o UPDATE falhar, o lead permanece em `Triagem` e pode ser reprocessado na próxima rodada
- O campo `updated_at` deve ser sempre atualizado junto com o UPDATE principal
