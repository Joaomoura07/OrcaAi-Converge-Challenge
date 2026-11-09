# OrçaAI: contexto para o deck do pitch

Material para gerar os slides do pitch no Claude Design. Junto deste arquivo vão a logo (`logo-orca.png`) e os prints do site (`prints/`). **O deck deve parecer o próprio site:** mesmas cores, mesmas fontes, mesma logo.

---

## 1. O que é o produto

**OrçaAI** é um AI Agent para arquitetos. O arquiteto sobe a lista de materiais do projeto (Excel, CSV, TXT do Revit, PDF com texto, DXF, IFC) e o agente:

1. revisa a lista e aponta erros (unidade errada, quantidade absurda, item duplicado) e itens que costumam faltar (argamassa, rejunte, selador…). Nada muda sem o clique do arquiteto;
2. pesquisa o preço de cada item na internet, ao vivo, **sempre com a fonte (loja e link)**;
3. transforma item vago ("tinta branca") em pergunta, sem chutar o preço;
4. entrega o veredito: **cabe ou não no orçamento do cliente**, mais o plano de compra para o CEP da obra e a exportação em Excel.

- **Slogan do site:** "Menos tempo cotando material. Mais tempo projetando."
- **Subtítulo:** "A OrçaAI é um AI Agent que pesquisa os preços da sua obra e entrega o plano de compra pronto."
- **Contexto:** Converge Challenge powered by Tako, Porto Digital, Recife, 7 e 8/11/2026. Pitch de **3 minutos**. A banca avalia protótipo, viabilidade como startup, pitch e criatividade, com peso igual. Na banca estão a Canastra (fundo) e a Tako.

## 2. Estrutura do deck (8 slides + apêndice)

| # | Slide | Mensagem |
|---|---|---|
| 1 | Gancho | Um número grande: horas gastas por orçamento hoje. **[NÚMERO A PREENCHER]** |
| 2 | Por que dói | Tempo não faturado · erro de quantidade vira prejuízo · cliente desiste esperando |
| 3 | Solução | "Sobe a lista do projeto e recebe o estudo de viabilidade pronto, com preço e fonte." Print do site |
| 4 | Demo | Só "▶ Demo" grande (ao vivo ou vídeo) |
| 5 | Por que confiar | Todo preço tem fonte · item vago vira pergunta · nada muda sem o clique do arquiteto · cada decisão é auditável (Langfuse) |
| 6 | Negócio | Arquiteto paga assinatura · lojas recebem pedido pronto (lead/comissão) · mercado **[A PREENCHER: arquitetos no Brasil, CAU/BR]** |
| 7 | Ganho × custo | Manual: X horas · OrçaAI: minutos e centavos de IA por orçamento **[A PREENCHER]** |
| 8 | Fechamento | Próximo passo (piloto em Recife) · time · QR code para o site |
| A | Apêndice | Como o agente funciona: lista → revisão → agente ReAct (LLM GLM-5.2 na Featherless + busca Tavily/DuckDuckGo + cache) → Supabase em tempo real → Langfuse |

**Regras:** um número ou uma frase por slide, pouco texto e nada de bullets longos. Números marcados com [A PREENCHER] não podem ser inventados: deixe um espaço visível para eles.

## 3. Identidade visual (tirada do código do site)

Tema escuro, sóbrio e "técnico", com a referência de um banco alpino na hora azul. Há **um único azul**, usado só para a ação principal ou o destaque.

### Cores

| Papel | Hex | Uso |
|---|---|---|
| Fundo (ônix) | `#171721` | Fundo de todos os slides |
| Cartão (grafite) | `#1E1E2A` | Caixas e painéis, sem sombra |
| Superfície interativa | `#272735` | Botões secundários e chips |
| Divisória | `#2E2E3D` | Linhas finas |
| Borda forte | `#70707D` | Contornos de pílula |
| Texto principal (marfim) | `#EDEDF3` | Títulos e texto |
| Texto secundário | `#C3C3CC` | Subtítulos |
| Legenda | `#8E8E9C` | Notas pequenas e fontes |
| **Azul cobalto (destaque único)** | `#5266EB` | CTA, número-chave, destaque |
| Azul hover / claro | `#6577F2` / `#8F9CFF` | Variações raras |
| Definido (verde) | `#6FD39B` | Status "Definido" / "Dentro do orçamento" |
| Pendente (âmbar) | `#F0B35A` | Status "Pendente de especificação" |
| Erro (coral) | `#FF8B80` | Erros e "Não cabe" |

No hero há um **gradiente** de ônix para azul-índigo na base da tela, com uma planta baixa em wireframe 3D pontilhado (linhas e pontos azulados) ao fundo. Ele serve como fundo de capa e de slide de impacto.

### Tipografia

- **Títulos:** Bricolage Grotesque, peso ~480, sem itálico e sem palavra em outra cor (Google Fonts).
- **Texto:** Inter, peso 400. Os números usam algarismos tabulares.
- Escala: display 65px · título 42/32px · subtítulo 21px · corpo 16–18px · legenda 12px.

### Forma

- Botões e chips em **pílula** (raio 32–40px). Botão principal azul cobalto sólido; secundário com contorno fino.
- Cartões com raio de 12px, um tom acima do fundo e **sem sombra**.
- Espaço generoso, conteúdo centralizado e largura máxima de ~1200px.

### Logo

`logo-orca.png`: uma orca em traço fino marfim, sobre fundo transparente. No cabeçalho do site ela aparece pequena (~34px), centralizada, ao lado da palavra **"OrçaAI"** em Inter semibold marfim. Use o mesmo par logo + nome na capa e no rodapé dos slides.

## 4. Prints do site (em `prints/`)

| Arquivo | Tela | Bom para |
|---|---|---|
| `01-inicio-hero.jpg` | Página inicial com o slogan, os CTAs "Visualizar Demo" / "Conhecer projeto" e a planta 3D | Capa e slide da solução |
| `02-nova-obra.jpg` | "Do desenho ao quantitativo.": nome da obra, CEP e orçamento do cliente | Slide de como funciona (passo 1) |
| `03-importar-lista.jpg` | Importação: exemplo, colar lista do WhatsApp, "5 itens lidos de apto-boa-viagem.csv" | Passo 2 (aceita qualquer formato) |
| `04-tabela-revisao.jpg` | Tabela editável com marcas reais (Tramontina, Tigre, Quartzolit, Suvinil) e os painéis "Possíveis erros na lista" / "Itens que costumam faltar" | Passo 3 (revisão com IA) |

Mostre os prints dentro de uma moldura de navegador escura, com cantos de 12px.
