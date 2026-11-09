# Converge Challenge — Divisão de funções (Time "Avisa que é Agente")

Produto: **Orçamentista Autônomo**. O arquiteto sobe a lista de materiais, a IA pesquisa preço e imagem de cada item ao vivo, decide o frete pelo CEP da obra e entrega o orçamento.

Backend no ar: https://converge-challenge.vercel.app (`/docs`) · Repo: `github.com/enzoemanuelgl/Converge-Challenge`

## Como dividimos

Ninguém fica preso a "só front" ou "só backend". Cada um é dono de **uma fatia do produto de ponta a ponta** (tela + backend + dados daquela fatia). Assim todo mundo mexe um pouco em tudo, e a carga fica parecida. As tarefas de entrega (submissão, pitch, AWS) são repartidas igualmente, e o que sobra no fim vai pra quem estiver mais livre.

> Os nomes abaixo são sugestão. Troquem entre vocês à vontade: as três fatias têm tamanho parecido.

| Fatia | Dono sugerido | O que o usuário vê |
| --- | --- | --- |
| **A. Entrada** | Samuel | Sobe o arquivo, corrige a tabela, envia |
| **B. Pesquisa de preço** | Enzo | Itens aparecendo ao vivo com preço e imagem |
| **C. Frete e resultado** | João | Decisão de frete, pendentes e o orçamento final |

---

## Fatia A — Entrada (dono: Samuel)

- **Tela:** upload CSV/Excel/PDF, tabela editável, campo de CEP, botão "Enviar para a IA".
- **Backend:** endpoint `POST /orcamentos` que valida o contrato e grava em `orcamentos` / `orcamento_itens`, e dispara o agente.
- **Dados:** rodar os 100 casos de `tests/importacao/` e os arquivos de `docs/arquivos-teste/`; arquivo ruim dá mensagem amigável.
- **Cuidados:** `.txt` do Revit em PT → forçar tab no PapaParse; CSV do Excel é Windows-1252; SheetJS pelo CDN oficial (o `xlsx` do npm é vulnerável).

## Fatia B — Pesquisa de preço (dono: Enzo)

- **Backend:** agente ReAct (`src/agent.py`, `src/tools.py`, `prompts/`) com Tavily (principal), `ddgs` (reserva) e `cache_precos`; buscas em paralelo; preço médio/mín/máx, imagem e fontes por item; Langfuse (região US).
- **Tela:** o componente do item que aparece ao vivo (Supabase Realtime) com preço, imagem e links das lojas.
- **Dados:** tabela `cache_precos`; meta da demo ≈30 s para 15–20 itens, metade em cache.
- **Cuidados:** GLM-5.2 "pensa" antes de responder, então não usar limite de tokens baixo. Reserva: `Qwen/Qwen3-32B`.

## Fatia C — Frete e resultado (dono: João)

- **Backend:** logística em Python determinístico (ViaCEP/BrasilAPI + tabela `fornecedores` com lojas de Recife) com saída `{estrategia: loja_unica|dividir, lojas, frete_total, economia_estimada}`; classificação `definido` × `pendente_especificacao`; texto para negociar desconto.
- **Tela:** resultado final (totais, pendentes, frete, economia).
- **Dados:** cadastrar os fornecedores no Supabase.

---

## Tarefas compartilhadas (cada um leva uma parte)

| Tarefa | Quem | Quando |
| --- | --- | --- |
| Domínio `.com.br` + e-mail no domínio + conta AWS (prêmio) | Samuel | Sáb noite |
| Landing page simples no domínio | Enzo | Sáb noite |
| Texto da submissão (problema, para quem, solução, como funciona, lista de ferramentas/APIs) | João | Dom até 12h |
| Vídeo da demo | Enzo | Dom 10h30–11h30 |
| Slides | Samuel | Sáb noite |
| Pitch: partes faladas | Os três, ~1 min cada | Ensaio dom 13h–16h |
| Testar o link como jurado (outro dispositivo, arquivo novo) | Quem NÃO fez aquela fatia | Sáb 17h e dom manhã |
| Submeter no painel | João (os três conferem juntos) | Dom até 12h30 |

**Revisão cruzada:** cada um testa a fatia de outra pessoa. Quem faz não é quem testa.

---

## Contrato site ↔ backend (fechar juntos ANTES das 10h)

`POST /orcamentos` → `{ "orcamento_id": "..." }`

```json
{
  "obra": { "nome": "...", "cep": "50000000" },
  "origem": { "arquivo": "...", "formato": "csv", "software": "Revit" },
  "itens": [
    { "linha": 1, "categoria": "...", "descricao": "...", "quantidade": 12.5,
      "unidade": "m2", "nivel": "...", "extras": {} }
  ]
}
```

`quantidade` numérica com ponto; unidades `m, m2, m3, un, kg, l, cm, mm`; colunas desconhecidas em `extras`. Modelo: `tests/importacao/contrato.py`. O site acompanha o progresso pelo Supabase Realtime (`orcamento_itens`).

Decidir juntos: framework do site (Next.js é o padrão), quem lê o arquivo no navegador, campos que faltam na tela, arquivo da demo (obra 03 Cozinha ou 11 Studio).

---

## Cronograma

**Sábado 07/11**

| Hora | O que acontece |
| --- | --- |
| 08h30 / 09h30 | Credenciamento / abertura e regras |
| **10h** | **Começa a construção.** Primeiro commit; cada um abre sua fatia |
| 10h–12h | Cada um monta o esqueleto da sua fatia (tela + backend) |
| 12h–14h | Juntar as três fatias. **Marco 14h: caminho feliz ponta a ponta.** Se não rodar, cortar escopo |
| 14h–16h | Pendente de especificação, frete por CEP, acabamento |
| 16h–17h | Mentoria. **17h: demo gravável** + revisão cruzada |
| Noite | Deploy final, domínio, landing, e-mail, slides |

**Domingo 08/11**

| Hora | O que acontece |
| --- | --- |
| até 10h30 | Só corrigir o que quebra |
| **10h30** | **Congela features** |
| 10h30–11h30 | Gravar vídeo da demo (plano B) |
| **12h30** | Submeter (prazo oficial 13h) |
| 13h–16h | Ensaiar o pitch pelo menos 5 vezes |
| 16h / 17h30 | Demo Day (3 min por time) / premiação |

**Roteiro do pitch (3 min):** dor com número (0:00–0:25) → solução (–0:45) → demo ao vivo (–2:00) → negócio: quem paga, preço, mercado, por que agora (–2:35) → ganho × custo e próximo passo (–3:00).

---

## Regras de todos

- Código do produto só a partir das **10h de sábado**.
- **Commits pequenos e frequentes**, cada um na sua branch/arquivos para não dar conflito; juntar nos marcos.
- **Nunca** subir `.env` ou chaves pro GitHub nem pro grupo do evento. Só dados fictícios.
- Cada um cria a própria chave Tavily (app.tavily.com); várias juntas: `TAVILY_API_KEYS=tvly-aaa,tvly-bbb`.
- Variáveis na Vercel **sem aspas**.
- Antes de concluir uma mudança: `python -m scripts.check_setup` e rodar caminho feliz + uma exceção.
- Dúvidas de regra só no WhatsApp oficial do evento.
- Featherless cobra US$ 25/pessoa em 08/11; quem não for continuar cancela antes.

## Checklist antes de sábado

- [ ] Chave Tavily própria
- [ ] `.env` recebido no privado
- [ ] Repo clonado e `python -m scripts.check_setup` passando
- [ ] Contrato validado pelos três
- [ ] Notebook, carregador e extensão na mochila
