# Research Playbook — Lead Researcher Bendito

Estratégias de busca, sinais de fit e heurísticas de score para qualificação de leads B2B da Bendito.

---

## Contexto do produto

A **Bendito** vende açaí bebível estilo Oakberry — funcional, saudável, sem xarope, em garrafa plástica — no modelo B2B2C.

O cliente ideal é um estabelecimento que:
- Já vende bebidas ou produtos saudáveis
- Tem fluxo de clientes presenciais ou delivery ativo
- Busca diferenciar o cardápio com produto premium
- Tem operação organizada o suficiente para receber um fornecedor

---

## Estratégias de busca

### Sequência padrão por lead

1. **Google Maps** — buscar `{name} {bairro}` e variações:
   - `{name} {cidade}`
   - Nome abreviado + bairro
   - Tipo de negócio + bairro se o nome for genérico
   
2. **Instagram** — buscar `@{nome_sem_espaços}`, `{nome} {cidade}`, `{nome}oficial`
   - Verificar se o perfil é o estabelecimento (não pessoa física)
   - Checar data do último post para classificar como Ativo / Inativo / Privado

3. **iFood** — buscar nome do estabelecimento dentro do app/site
   - Confirmar se é a mesma unidade (mesmo bairro)

4. **Rappi** — idem iFood

5. **Site próprio** — tentar `{nome}.com.br`, `{nome}.com`, busca Google `site {nome}`
   - iFood e Rappi não contam como site próprio

### Registro de hipóteses

Documente todas as variações tentadas em `search_hypotheses`, separadas por `;`:

```
Café do Zé São Paulo; Café do Zé Pinheiros; @cafedoze; @cafedozepinheiros; cafedoze.com.br
```

---

## Sinais de fit — por tipo de estabelecimento

### Alto fit (score 4–5)

| Tipo                  | Sinais positivos                                                                 |
|-----------------------|---------------------------------------------------------------------------------|
| Cafeteria / Bistrô    | Cardápio com bowls, sucos naturais, smoothies — já vende proposta saudável      |
| Hamburgueria gourmet  | Posicionamento premium, preço médio alto, público jovem                         |
| Lanchonete saudável   | Açaí no cardápio atual (oportunidade de upgrade) ou alternativas funcionais     |
| Academia / Smart Fit  | Público fitness direto, alto giro de bebidas funcionais                         |
| Mercado / Empório     | Seção de bebidas refrigeradas, marca própria ou curadoria de produtos           |

### Médio fit (score 2–3)

| Tipo                  | Situação                                                                        |
|-----------------------|---------------------------------------------------------------------------------|
| Restaurante por quilo | Pode vender bebidas avulsas, mas giro incerto                                   |
| Padaria tradicional   | Fluxo bom, mas cardápio voltado a pão/café — resistência possível               |
| Pizzaria              | Delivery forte, mas associação com bebidas calóricas                            |
| Lanchonete comum      | Margem apertada, possível resistência a produto premium                         |

### Baixo fit / Descarte (score 1)

- Estabelecimento não identificado após múltiplas buscas
- Telefone / endereço inconsistente entre fontes
- Negócio fechado (Maps mostra "permanentemente fechado")
- Perfil exclusivamente de delivery sem presença física verificável
- Bar / botequim sem foco em alimentação saudável
- Restaurante institucional (escola, hospital, empresa) — ciclo de venda diferente

---

## Heurísticas de score

### Score 5 — Contato imediato

- Estabelecimento facilmente identificado com dados consistentes
- Instagram ativo com engajamento saudável (>500 seguidores, posts recentes)
- Tipo de negócio com fit direto (cafeteria, saudável, gourmet)
- Já vende bebidas funcionais ou alternativas premium
- Presença em delivery (iFood/Rappi) — canais de venda já estruturados

### Score 4 — Contato prioritário

- Identificado com bons dados, mas 1 sinal de fit faltando
- Instagram ativo, mas engajamento baixo
- Tipo com fit alto, mas sem confirmação de cardápio saudável

### Score 3 — Novo (monitorar)

- Fit indireto — tipo de negócio compatível mas sem sinais claros
- Dados incompletos mas negócio identificado
- Instagram inativo ou privado

### Score 2 — Novo (baixa prioridade)

- Fit fraco mas não descartável
- Poucas avaliações no Maps, estabelecimento pequeno
- Sem presença digital relevante

### Score 1 — Descarte

- Negócio não identificado após todas as buscas
- Tipo de negócio incompatível
- Estabelecimento fechado ou com dados corrompidos

---

## Preenchimento de strategic_summary

O campo deve ter 3–5 linhas respondendo:

1. O que é o estabelecimento (tipo, tamanho percebido, bairro)
2. O que foi encontrado (Maps, Instagram, delivery)
3. Por que tem ou não tem fit com a Bendito
4. Sugestão de abordagem (canal, argumento, timing)

**Exemplo:**
```
Cafeteria no Leblon com ~4.6 no Maps e 2.3k seguidores no Instagram ativo. 
Cardápio inclui smoothies e bowls — público alinhado com produto Bendito. 
Presente no iFood com bom volume de avaliações (180+). 
Fit alto: proposta saudável, delivery estruturado, posicionamento premium. 
Abordar via Instagram DM apresentando o produto como upgrade do cardápio atual.
```

---

## cold_approach_notes

Preencher sempre, mesmo para score baixo. Exemplos úteis:

- `"Ligar pela manhã antes do pico de movimento"`
- `"Perguntar pelo dono — perfil pessoal identificado: @nome"`
- `"Já vende açaí de concorrente — abordar com diferencial funcional"`
- `"Delivery muito forte — apresentar o produto como item de margem alta no iFood"`
- `"Instagram privado — tentar contato por telefone diretamente"`

---

## Classificação de research_status

| Status      | Quando usar                                                        |
|-------------|--------------------------------------------------------------------|
| Concluído   | Todas as buscas realizadas, dados suficientes para decisão         |
| Parcial     | Negócio encontrado mas com lacunas relevantes (sem Instagram, etc) |
| Sem dados   | Negócio não identificado após todas as tentativas de busca         |
