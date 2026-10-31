# MCP para criação de cadernos de questões e simulados na plataforma Qconcursos

O **Traki**, a corujinha robótica do [Track Concursos](https://trackconcursos.vercel.app/), conecta um assistente de IA ao catálogo oficial de filtros de questões do **Qconcursos** (Web e Mesa de Estudos **QC Elite**). Você informa o assunto ou até mesmo um edital inteiro que está estudando, e o assistente usa as ferramentas MCP para encontrar os filtros cirúrgicos correspondentes e entregar links prontos de questões filtradas por disciplina, assunto, banca, escolaridade e ano.

<p align="center">
  <img src="assets/traki-apresentando-qconcursos.png" width="248" alt="Traki apresentando o Qconcursos">
  &nbsp;&nbsp;&nbsp;&nbsp;
  <img src="assets/traki-qconcursos.png" width="198" alt="Super Traki com os superpoderes do Qconcursos">
</p>

<p align="center"><sub>Traki apresentando o Qconcursos e o Super Traki com os superpoderes do Qconcursos.</sub></p>

<p align="center">
  <a href="https://claude.ai"><img src="https://img.shields.io/badge/Claude-MCP%20Ready-D97757?style=flat&logo=anthropic&logoColor=white&labelColor=111827" alt="Claude MCP Ready"></a>
  <a href="https://docs.typesafe.ai"><img src="https://img.shields.io/badge/TypeSafe%20AI-JEV%20System%20One-0284C7?style=flat&labelColor=111827" alt="TypeSafe JEV System One"></a>
  <a href="https://modelcontextprotocol.io"><img src="https://img.shields.io/badge/MCP-Streamable%20HTTP-2563EB?style=flat&logo=json&logoColor=white&labelColor=111827" alt="MCP Streamable HTTP"></a>
  <a href="https://aws.amazon.com"><img src="https://img.shields.io/badge/AWS-EC2%20Hosted-FF9900?style=flat&logo=amazon-aws&logoColor=white&labelColor=111827" alt="AWS EC2 Hosted"></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/Licen%C3%A7a-MIT-10B981?style=flat&labelColor=111827" alt="Licença MIT"></a>
</p>

---

## 🎯 Para que serve

Essa ferramenta MCP foi criada para ajudar quem estuda pelo **Track Concursos** ou por outras plataformas a criar cadernos de questões e simulados completos no **Qconcursos** com auxílio de uma IA.

Em vez de selecionar manualmente disciplina por disciplina, expandir dezenas de subtópicos na árvore do Qconcursos, configurar banca e escolaridade, o estudante apenas informa o tópico do edital (ou cola o conteúdo programático de uma disciplina/edital inteiro). O assistente de IA interpreta o pedido, aciona o Traki e entrega instantaneamente dois links oficiais prontos:
1. **Qconcursos Web Tradicional (`www.qconcursos.com`)**
2. **Mesa de Estudos QC Elite (`elite.qconcursos.com`)**

Assim, o concurseiro gasta menos tempo montando filtros e mais tempo resolvendo questões!

---

## 💡 Como esse projeto é possível?

O Qconcursos permite aplicar filtros avançados diretamente pela URL através de parâmetros estruturados (`discipline_ids[]`, `subject_ids[]`, `examining_board_ids[]`, `scholarity_ids[]`, `publication_year[]`, além das travas `exclude_outdated=true` e `exclude_nullified=true`).

Cada uma das **208 disciplinas** e **11.645 etiquetas de assuntos** do Qconcursos possui um identificador único estruturado em árvore. Com esses IDs combinados na URL, é possível abrir a plataforma com desde 1 subtópico cirúrgico até **mais de 290 filtros simultâneos** (cobrindo um edital inteiro para gerar simulados precisos na plataforma).

---

## 🧠 Arquitetura Técnica Simplificada: A Revolução JEV e a Economia Ultimate de Tokens

Nos servidores MCP convencionais, o assistente de IA (como o Claude) precisa fazer várias chamadas seguidas (*ping-pong*): primeiro lista disciplinas, depois pesquisa palavras-chave, lê centenas de linhas de árvores de assuntos, tenta adivinhar IDs e por fim monta o link. Isso consome **de 15.000 a 40.000 tokens por pedido**, demora até 1 minuto e esgota rapidamente o limite diário da conta gratuita do Claude.

Para resolver isso na raiz, este projeto foi arquitetado em parceria com o **Gemini 4 Argon (no Google Antigravity)** utilizando uma **Arquitetura Neuro-Simbólica de 3 Camadas (Sistema 2 + Sistema 0 + Sistema 1 com JEV)**:

```text
[1. Sistema 2: Claude (LLM)] 
   │  Traduz o "bancanês" do edital para termos objetivos e faz 1 única chamada MCP
   ▼
[2. Sistema 0: Motor Lexical em RAM na AWS (< 5ms)]
   │  Varre 11.645 assuntos, aplica Stemming Português e Clusters Multidisciplinares
   ▼
[3. Sistema 1: JEV (TypeSafe AI — System One)]
   │  Julgamento probabilístico calibrado (Choice + Noul) para cravar Nós-Folha cirúrgicos
   ▼
[Link Oficial Qconcursos Web + QC Elite entregue em 1 único Round-Trip!]
```

### Como cada camada trabalha para economizar 95%+ dos seus tokens:

1. **Sinergia Sistema 2 (Claude) → Tradução do "Bancanês":**
   Bancas como FCC, Cebraspe e FGV adoram descrições subjetivas no edital (ex.: *"Dedução de novas informações das relações fornecidas"* ou *"Correlação de tempos e modos verbais"*). O Claude atua como **Sistema 2**: ele traduz esse jargão para os termos concretos da árvore (`termos_busca`) e faz **uma única chamada** para a ferramenta do Traki.
2. **Sistema 0 (Busca Determinística em Memória RAM):**
   Hospedado em uma instância AWS EC2 sempre aquecida, o motor em Node.js filtra os candidatos em menos de **5 milissegundos**, aplicando:
   - **Stemmer Universal em Português:** Normaliza plurais complexos (`-ões`, `-ais`, `-res`, `-ntes`) e separa o peso do nome próprio do nó (`ownNodeTokens`) do contexto do nó-pai (`contextTokens`).
   - **Clusters Multidisciplinares Automáticos:** Tópicos de *Informática/TI* varrem automaticamente 13 disciplinas correlatas (Redes, Bancos de Dados, Engenharia de Software, Segurança da Informação, etc.); *Exatas* integra Raciocínio Lógico, Matemática, Matemática Financeira e Estatística; *Carreiras Bancárias* integra Conhecimentos Bancários, Vendas e Atendimento.
3. **Sistema 1 com JEV (`@typesafe-ai/sdk`):**
   Em vez de devolver dezenas de candidatos brutos para o Claude ler (o que gastaria milhares de tokens), o servidor aciona internamente o **JEV (modelo System One da TypeSafe AI)**. Usando primitivas estruturadas de decisão (`choice` e `noul`), o JEV avalia a aderência semântica dos candidatos em milissegundos e decide automaticamente a estratégia taxonômica:
   - **`folha_especifica`:** Seleciona o subtópico cirúrgico exato quando o edital pede um conceito pontual.
   - **`uniao_folhas`:** Combina múltiplos nós-folha (inclusive de ramos diferentes da mesma disciplina) quando o tópico do edital traz vários conceitos separados por vírgula.
   - **`subarvore_completa`:** Expande o nó-pai e todos os seus descendentes quando o edital cobra um capítulo inteiro.

**Resultado prático:** O Claude recebe o card final já pronto e auditado em **1 única chamada de ferramenta (~400 tokens)**, permitindo dezenas de vezes mais consultas na versão gratuita do Claude!

---

## ⚙️ Como funciona no dia a dia

1. **Você envia o seu tópico ou edital.** Pode digitar livremente no chat do Claude ou copiar um tópico direto do seu edital verticalizado no [Track Concursos](https://trackconcursos.vercel.app/).
2. **O Claude aciona o Traki Qconcursos na AWS.**
3. **O servidor executa o pipeline JEV** e devolve o Card Oficial do Traki contendo os dois links clicáveis (**Qconcursos Web** e **Mesa de Estudos QC Elite**), a trilha taxonômica escolhida e o **Parecer do Traki**.
4. **Loop de Autocorreção sob Demanda:** Achou que o filtro ficou muito amplo ou quer um recorte diferente? Basta dizer ao Claude dando mais contexto! Ele acionará automaticamente a ferramenta `pesquisar_filtros_qc`, inspecionará a árvore da disciplina e recriará o caderno cravando os IDs exatos via `subject_ids_forcados`.
5. **Super-Link de Disciplina Inteira ou Edital Completo:** Se você colar o conteúdo programático inteiro de uma matéria (ou de todas as matérias do seu edital), o Traki processa todos os subtópicos em paralelo e gera um **Super-Link Consolidado** (com até 300 filtros combinados) pronto para você salvar na plataforma e gerar simulados completos!

---

### 📋 Exemplo de utilização

> “Traki, estou estudando o tópico "Denotação e conotação" de Língua Portuguesa, banca FCC, nível médio. Gere o caderno no Qconcursos.”

O Claude se conectará ao MCP e retornará a resposta padronizada:

> 🎯 **Caderno Qconcursos Gerado com Sucesso!**
> 🔗 [Clique aqui para abrir o Caderno no Qconcursos](https://www.qconcursos.com/questoes-de-concursos/questoes?discipline_ids%5B%5D=1&subject_ids%5B%5D=16331&examining_board_ids%5B%5D=1&scholarity_ids%5B%5D=2&exclude_outdated=true&exclude_nullified=true)
> 🔗 [Clique aqui para abrir na Mesa de Estudos (QC Elite)](https://elite.qconcursos.com/questoes-de-concursos/questoes?discipline_ids%5B%5D=1&subject_ids%5B%5D=16331&examining_board_ids%5B%5D=1&scholarity_ids%5B%5D=2&exclude_outdated=true&exclude_nullified=true)
> **Disciplina:** Português
> **Banca:** FCC (ID: 1)
> **Nível:** Médio
> **Total de Assuntos na Árvore:** 1
> **Tópicos Incluídos:** Denotação e Conotação (Trilha: Interpretação de Textos > Denotação e Conotação)
> **Parecer do Traki:** O Qconcursos encontrou correspondência cirúrgica para "Denotação e conotação" no assunto "Denotação e Conotação". O caderno está filtrado para FCC (ID: 1) e nível Médio.

---

## 🔌 Como Conectar ao Claude (100% Gratuito)

Você pode utilizar esse MCP gratuitamente no **Claude.ai (Web)** ou no **Claude Desktop** sem precisar instalar nada no seu computador:

1. Acesse a central oficial **[A Toca do Traki](https://tocadotraki.vercel.app/)** ou vá direto no Claude em **Settings → Connectors → Add custom connector**.
2. Defina o nome como **Traki Qconcursos**.
3. No campo **URL do MCP**, cole:

```text
https://tocadotraki.vercel.app/mcp-qconcursos
```

*(Ou diretamente pelo endpoint do servidor AWS: `https://18-221-134-140.sslip.io/qc/mcp`)*

4. Em **Autenticação**, mantenha **None (Nenhuma)** e confirme!

---

## 🛠️ Ferramentas MCP Disponíveis

| Ferramenta | O que faz |
| :--- | :--- |
| **`gerar_caderno_qc`** | Mapeia um tópico específico com leaf-matching via JEV e devolve as URLs oficiais (Qconcursos Web + QC Elite). Suporta override manual via `subject_ids_forcados`. |
| **`gerar_simulado_disciplina_ou_edital_qc`** | Lê o conteúdo programático verticalizado de uma disciplina inteira ou de um edital completo e monta Super-Links consolidados para criação de simulados no Qconcursos. |
| **`pesquisar_filtros_qc`** | Consulta diretamente a árvore de assuntos e hierarquia de uma disciplina no Qconcursos para inspeção e refinamento de filtros. |
| **`mapear_edital_qc`** | Processa uma lista de tópicos em lote gerando uma tabela comparativa com links individuais por assunto. |

---

## 🤝 Créditos de Engenharia e IA

Este projeto foi idealizado e construído para a comunidade de concurseiros do **[Track Concursos](https://trackconcursos.vercel.app/)**, contando com engenharia de software e pareamento agentivo do **Gemini 4 Argon** no ambiente **Google Antigravity**, integrando o motor de decisões **JEV (TypeSafe AI System One)** para alcançar precisão cirúrgica em filtros de editais com consumo mínimo de tokens.

*O Traki faz parte do ecossistema Track Concursos e não possui vínculo oficial com o Qconcursos. Para resolver as questões ao abrir os links gerados, basta estar logado na sua conta em `qconcursos.com`.*

Licença: [MIT](LICENSE).

---

## ⭐ Apoie o projeto

O Traki e o Track Concursos são projetos gratuitos feitos para ajudar concurseiros a estudar com mais organização e alto rendimento. Se eles forem úteis para você, apoie:

- ⭐ Deixe uma estrela [neste repositório do MCP](./), no [MCP do Gran Questões](https://github.com/michel-softwares/mcp-gran-questoes) e no [repositório oficial do Track Concursos](https://github.com/michel-softwares/track-concursos).
- 🦉 Visite e compartilhe **[A Toca do Traki](https://tocadotraki.vercel.app/)** e o **[Track Concursos](https://trackconcursos.vercel.app/)**.
- 💬 Participe da nossa comunidade no **[Telegram](https://t.me/TrackConcursos)**.
- ☕ Se quiser contribuir financeiramente com a manutenção do servidor e próximas melhorias, a chave PIX é **`michelaraujo100@gmail.com`**.

Obrigado pelo apoio e bons estudos! 🚀🦉
