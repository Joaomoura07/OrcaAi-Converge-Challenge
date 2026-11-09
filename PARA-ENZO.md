# João → Enzo: o que falta para a Fatia C rodar depois da sua pesquisa

Li o que você subiu na `main` (`src/modelos.py`, `src/orcamento.py`). Boa notícia: **o formato dos seus itens já bate** com o que a minha parte lê (`fontes[].dominio`, `fontes[].preco_embalagem`, `qtd_embalagens`, `preco_emb_min/med/max`, `status`, `estimativa`, `pergunta`, `confianca`). Não precisa mudar nada nos seus modelos.

Mas achei **um problema que passaria em silêncio**. É o item 1.

## 1. O gancho `GANCHOS_FIM` chama as funções erradas (precisa de 1 linha)

Em `src/orcamento.py`:

```python
GANCHOS_FIM = [("src.logistica", "calcular_logistica"), ("src.viabilidade", "calcular_viabilidade")]
```

Esse gancho chama `calcular_logistica(orcamento_id)` e `calcular_viabilidade(orcamento_id)` **só com o ID**. Mas essas duas funções minhas são **puras**: recebem `(itens, fornecedores, distancias)` e `(itens, orcamento_cliente, frete_total)`. Depois do merge, o gancho chamaria com o argumento errado, daria erro, o seu `try/except` só registraria no log ("Fatia C: ... falhou") e **o resultado nunca seria calculado**, sem erro visível.

A função que faz tudo a partir do ID (lê o banco, calcula frete + viabilidade + ganho e grava em `orcamentos`) é esta, em `src/rotas_resultado.py`:

```python
GANCHOS_FIM = [("src.rotas_resultado", "resultado")]
```

É **uma linha só** e **uma chamada só** no lugar das duas. Ela é síncrona e faz 1 a 2 consultas de CEP (≈1 a 3 s). O seu gancho já aceita função síncrona; se quiser não bloquear o loop, chame com `await asyncio.to_thread(fn, orcamento_id)`.

Depois de cada `repesquisar_item` (quando o arquiteto responde uma pergunta), chame de novo: é seguro, dá o mesmo resultado se nada mudou.

> Meu branch ainda **não foi enviado** (vamos juntar todos juntos), então `src.rotas_resultado` só existe depois do push. Até lá o seu gancho só registra "ainda não existe; pulando", o que é o comportamento certo.

## 2. Tempo e custo (para o "ganho na tela")

- **Tempo:** já uso as suas colunas `pesquisa_iniciada_em` e `pesquisa_concluida_em` de `orcamentos`. Nada a fazer.
- **Custo:** não vi você gravar `custo_usd`. Sem ele a tela mostra "Custo de IA: R$ 0,00". Peço **somar o `cost_usd` das chamadas ao LLM** do orçamento e gravar em `orcamentos.custo_usd` (coluna `numeric`; o Samuel precisa incluí-la no SQL). Se não der tempo, o resto funciona.

## 3. Ajuste do Langfuse em `src/config.py`

O SDK do Langfuse lê as chaves do ambiente do sistema, mas o `.env` só é carregado dentro de `Settings`. Por isso o `check_setup` falha no Langfuse **localmente** mesmo com chaves certas. Em `get_settings()`, antes do `return`:

```python
if settings.langfuse_active:
    os.environ.setdefault("LANGFUSE_PUBLIC_KEY", settings.langfuse_public_key)
    os.environ.setdefault("LANGFUSE_SECRET_KEY", settings.langfuse_secret_key)
    os.environ.setdefault("LANGFUSE_HOST", settings.langfuse_host)
```

E o `.env` compartilhado traz `LANGFUSE_BASE_URL`, mas o código lê `LANGFUSE_HOST`: com a região **US** é preciso `LANGFUSE_HOST=https://us.cloud.langfuse.com` (na Vercel, **sem aspas**). Você mexeu no `config.py`, então prefiro que você aplique, para não termos conflito.

## 4. Conferir: a lista de domínios da busca

`fontes[].dominio` é a chave que casa com a tabela `fornecedores`. Eu tiro `www.` e `https://`, então `www.leroymerlin.com.br` serve. Mas precisamos da **lista exata de domínios da sua busca** (a "lista fixa de lojas" do Tavily) para cadastrar as lojas de Recife com o mesmo domínio. Pode me mandar?

## Resumo: o que preciso que você me responda

1. Você troca o `GANCHOS_FIM` para `[("src.rotas_resultado", "resultado")]`? (é a mudança que impede o resultado de ficar em branco)
2. Você grava `custo_usd` em `orcamentos`?
3. Você aplica o ajuste do Langfuse?
4. Qual é a lista de domínios da sua busca?
