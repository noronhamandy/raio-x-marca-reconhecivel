---
name: "raio-x-marca-reconhecivel"
description: "Executa o Raio-x da marca reconhecível de Amanda Noronha, metodologia 1.1.0: 92 verificações em 7 sintomas, nota de 0 a 35 com cobertura e confiabilidade, relatório em PDF, planilha e JSON. Use para auditar uma marca ou domínio."
---

# Raio-x da marca reconhecível · metodologia 1.1.0

Método e ferramenta de Amanda Noronha®, organizados pelas cinco camadas da Arquitetura Cognitiva do SEO. Quem roda a auditoria é a pessoa usuária; Amanda Noronha aparece como criadora do método e da ferramenta. Regras de origem: documento 01 · Knowledge Core & Governance (metodologia 1.1.0, aprovada em 04/11/2026). Página pública: https://amandanoronha.com.br/ferramentas/raio-x-da-marca-reconhecivel/

A skill executa as verificações que as ferramentas da sessão permitem, registra evidência em cada item, calcula a nota em código e entrega PDF, planilha e JSON. O que depende de acesso ou dado que a sessão não tem fica como **não avaliado**, com o que falta. Nunca marcar "cumpre" sem evidência coletada nesta auditoria.

## 1. Entrada

Antes de coletar, reunir o que falta. Quando o prompt já trouxer todas as entradas, começar sem perguntar. Quando faltar algo, perguntar tudo de uma vez (AskUserQuestion quando disponível) e inferir o que der pelo próprio site.

- **Domínio** e, se houver, URLs de amostra. Sem amostra, montar pelo sitemap e pelo menu, respeitando a amostra mínima do perfil (seção 3).
- **Grafia canônica da marca** e variações conhecidas (incluindo razão social).
- **Categoria, uso e região**, para escrever as perguntas da rodada de IA.
- **Três a cinco concorrentes.**
- **Perfil do site** (seção 3): tem preço público? tem blog com autor? Antes de fixar, procurar o blog em /blog, em blog.DOMINIO, no menu (inclusive no JSON do menu montado por JavaScript) e numa busca site:DOMINIO. Blog em subdomínio ou em outra plataforma conta como conteúdo da marca.
- **Nome de quem audita** (opcional): entra na capa como "Auditoria:". Sem nome, a linha não aparece.
- **Rodada de IA:** rodar agora, usar dados já coletados (aba Rodada de IA, xlsx ou texto colado) ou deixar para depois.
- **Acessos opcionais:** servidor para teste de user-agent, painel do CDN, ferramenta de visibilidade em IA.

Modelo de prompt com todas as entradas, para colar numa conversa nova:

```
Rode o raio-x da marca reconhecível no [domínio]. As informações de entrada estão todas aqui, então comece sem me perguntar nada.
Domínio: [domínio]. Monte a amostra pelo menu e pelo sitemap, com a amostra mínima do perfil.
Grafia canônica: [marca]. Variações conhecidas: [variações e razão social].
Categoria: [categoria, uso e região].
Concorrentes: [três a cinco].
Perfil: [loja sem conteúdo editorial | loja com blog | site com blog e autor | institucional sem preço e sem blog].
Auditoria: [nome de quem audita, opcional].
Rodada de IA: [rodar agora | não rodar nesta coleta].
Navegador: use o navegador integrado e deixe cada página visível. Antes de responder cada item, escreva na conversa uma linha só com o código, o que checou e a evidência.
Entrega: PDF, planilha e JSON.
```

## 2. Estados de cada item

Cada item recebe dois registros independentes. O resultado entra na nota; a origem entra na confiabilidade.

- **Resultado:** cumpre (`pass`), sintoma detectado (`fail`), não se aplica (`na`, exige motivo), não avaliado (`unassessed`, exige o que falta). "Parcial" não existe: quando passa em parte, é sintoma, e a observação diz o que passou.
- **Origem:** declarada (`declared`, resposta sem evidência anexada), observada (`observed`, a skill coletou trecho, cabeçalho, status, print ou resposta de plataforma), validada (`validated`, uma pessoa conferiu a evidência contra o critério, com data). A skill chega no máximo a observada.
- Item com origem observada guarda a evidência e a data da coleta.

## 3. Perfis de site

O perfil é declarado na entrada e fixa os itens que não se aplicam e a amostra mínima. Duas execuções sobre o mesmo site e o mesmo perfil chegam à mesma contagem de não se aplica.

| Perfil | Não se aplica fixo | Amostra mínima |
|---|---|---|
| Loja sem conteúdo editorial (`loja_sem_conteudo`) | RX41, RX42, RX43, RX45, RX46, RX47, RX48, RX54, RX70, RX75 | 7: home, sobre, contato, trocas, uma categoria, dois produtos |
| Loja com blog (`loja_com_conteudo`) | nenhum | 9: as sete da loja, índice do blog, um artigo |
| Site com blog e autor (`site_com_blog`) | RX15, RX16, RX19, RX20 | 8: home, sobre, contato, uma página de serviço, índice do blog, dois artigos, página de autor |
| Institucional sem preço e sem blog (`institucional`) | RX15, RX16, RX19, RX20 e os dez da loja sem conteúdo | 5: home, sobre, contato, uma página de serviço, política de privacidade |

Fora da lista fixa, não se aplica só com motivo escrito, ligado ao objeto do item. Abaixo da amostra mínima, a nota sai provisória. Um perfil escolhido errado muda até dez itens de uma vez; na dúvida entre loja sem conteúdo e loja com blog, vale o blog que existir em qualquer endereço da marca, e as páginas dele entram na amostra.

## 4. Ferramentas e métodos

Códigos da coluna Método:

- **NAV:** navegador integrado (ou Claude in Chrome, conforme o preferido da sessão). Carregar a skill do navegador antes do primeiro uso. DOM renderizado, print, JavaScript, busca no Google sem login.
- **HTML:** HTML inicial, antes do JavaScript, pelo `fetch(location.href)` dentro da página. Texto que só existe dentro de `<script>` conta como fora do HTML inicial.
- **LD:** JSON-LD do DOM renderizado e do HTML inicial. Dois blocos com o mesmo `@id` e dados divergentes contam como sintoma no RX09.
- **WEB:** WebSearch e WebFetch.
- **IA:** respostas de plataformas de IA (seção 5).
- **SRV:** requisição feita por servidor, com user-agent escolhido. O navegador não troca de user-agent; sem servidor, o item fica como não avaliado com o script abaixo.
- **USR:** depende de acesso ou informação de quem audita. Entregar o passo a passo pronto.

Script de extração por página, para rodar no navegador:

```js
(async () => {
  const ld = [...document.querySelectorAll('script[type="application/ld+json"]')].map(s => { try { return JSON.parse(s.textContent) } catch (e) { return 'ERRO' } });
  const raw = await fetch(location.href, { credentials: 'omit' }).then(r => r.text());
  const semScript = raw.replace(/<script[\s\S]*?<\/script>/gi, ' ').replace(/<style[\s\S]*?<\/style>/gi, ' ').replace(/<[^>]+>/g, ' ').replace(/\s+/g, ' ');
  const hs = [...document.querySelectorAll('h1,h2,h3,h4,h5,h6')].map(h => h.tagName + ' ' + h.textContent.trim().replace(/\s+/g, ' ').slice(0, 80));
  const main = document.querySelector('main, article, [role=main]') || document.body;
  const palavras = main.innerText.replace(/\s+/g, ' ').trim().split(' ');
  const amostras = [0.2, 0.5, 0.8].map(f => palavras.slice(Math.floor(palavras.length * f), Math.floor(palavras.length * f) + 6).join(' '));
  const imgs = [...main.querySelectorAll('img')].filter(i => i.naturalWidth > 400).map(i => ({ src: i.currentSrc.slice(0, 120), alt: i.alt }));
  const q = s => document.querySelector(s);
  return JSON.stringify({ url: location.href, title: document.title, metaDesc: q('meta[name=description]')?.content, ogSite: q('meta[property="og:site_name"]')?.content,
    canonical: q('link[rel=canonical]')?.href, robots: q('meta[name=robots]')?.content, ld, ldNoHtmlInicial: (raw.match(/application\/ld\+json/gi) || []).length,
    headings: hs, amostrasNoHtmlInicial: amostras.map(a => [a, semScript.includes(a)]), imagensGrandes: imgs });
})()
```

Conferência de arquivos para máquina (RX26), também no navegador, na origem do site:

```js
(async () => { const t = async p => { try { const r = await fetch(p, { credentials: 'omit' }); const b = await r.text(); return { p, status: r.status, tipo: r.headers.get('content-type'), inicio: b.slice(0, 160) } } catch (e) { return { p, erro: String(e) } } };
  return JSON.stringify(await Promise.all(['/robots.txt', '/llms.txt', '/feed', '/sitemap.xml', '/wp-json'].map(t))); })()
```

Teste de user-agent (RX25 e RX27), para rodar num servidor ou por quem audita:

```bash
URL="https://dominio.com.br/"
for UA in "Mozilla/5.0 (Windows NT 10.0; Win64; x64) Chrome/130.0 Safari/537.36" "Mozilla/5.0 (compatible; GPTBot/1.2; +https://openai.com/gptbot)" "Mozilla/5.0 (compatible; ClaudeBot/1.0; +claudebot@anthropic.com)" "Mozilla/5.0 (compatible; PerplexityBot/1.0; +https://perplexity.ai/perplexitybot)"; do
  printf '%s | ' "$UA"; curl -sL -A "$UA" -o /tmp/r.html -w '%{http_code} | ' "$URL"; wc -c < /tmp/r.html
done
```

Status diferente de 200 ou tamanho muito menor para o agente indica bloqueio ou conteúdo reduzido. Cabeçalhos `cf-ray` e `server: cloudflare` identificam o CDN.

Wikidata (RX11): `https://www.wikidata.org/w/api.php?action=wbsearchentities&search=NOME&language=pt&format=json`, depois `https://www.wikidata.org/wiki/Special:EntityData/QID.json` e conferir `claims.P856`. Pela API no navegador quando o WebFetch recusar. Homônimos no resultado (pessoa, filme, lugar) entram na observação do RX02 e do RX11.

## 5. Rodada de IA

O sintoma 03 só recebe nota quando a rodada cumpre os requisitos (itens com papel "requisito": RX32 a RX36, RX38 e RX40). Requisitos não entram na nota. Sem eles, o 03 fica sem nota, entra com 0 no total, e o relatório avisa. RX13, RX59, RX64 a RX67 e RX80 também usam a rodada; RX76 a RX79 pedem três medições separadas no tempo.

Como rodar, quando a entrada pedir:

1. Escrever de cinco a dez perguntas: categoria, comparação entre marcas, decisão (presente, compra, contratação) e pergunta direta pela marca. Usar a linguagem do público, sem o nome da marca nas de categoria.
2. Rodar sem login, no navegador, e registrar data e hora. Plataformas: Modo IA do Google (`https://www.google.com/search?q=PERGUNTA&hl=pt-BR&gl=br&udm=50`), Perplexity (`https://www.perplexity.ai/search?q=PERGUNTA`) e ChatGPT (`https://chatgpt.com/?q=PERGUNTA&hints=search`). Plataforma que exigir login ou CAPTCHA fica de fora, com o registro.
3. Repetir cada pergunta de categoria três vezes na primeira rodada. A variação entre rodadas é dado.
4. Registrar por resposta: marcas citadas em ordem, posição da marca, domínios citados como fonte e observação.
5. Modo IA do Google: quando a primeira carga devolve "Algo correu mal", recarregar uma vez antes de registrar falha. Ler a resposta com o texto da página depois de 15 a 20 segundos, para não pegar a resposta pela metade.
6. Perplexity sem login pode pedir cadastro a partir da segunda pergunta. Registrar a resposta que veio e o bloqueio, e seguir nas outras plataformas.

**Regra de posição:** posição é a ordem da primeira menção no texto da resposta. Marcas da mesma entidade contam uma vez (por exemplo, uma loja e a coleção que ela hospeda). Marketplace conta como posição e recebe a marcação "marketplace". Menção no texto e citação como fonte ficam em colunas separadas. Cartão de fonte com o nome do site (por exemplo "Chico Rei +2" logo após um parágrafo) conta como fonte, nunca como menção.

**Conferência da resposta direta (RX29):** cada afirmação factual da resposta sobre a marca é conferida contra o site. Uma afirmação errada já é sintoma, com a frase e a fonte da correção.

**Medição recorrente, ao fim da entrega:**

- **Plano pago:** oferecer uma tarefa agendada semanal com as mesmas perguntas, plataformas, dia e horário, que acrescenta as respostas ao mesmo histórico (aba Rodada de IA e JSON). Como a rodada usa o navegador integrado, a tarefa precisa depender do computador, ligado e com o app do Claude aberto no horário. Criar só com o aceite de quem audita.
- **Plano gratuito:** tarefa agendada não está disponível. Entregar um arquivo .ics com evento semanal e o prompt da rodada pronto para colar numa conversa nova.
- Na terceira medição, recalcular RX76 a RX79 com o histórico.

## 6. Nota, cobertura, confiabilidade e prioridade

Calcular sempre em código (seção 11), nunca de cabeça.

- **Nota do sintoma, de 0 a 5:** 5 × P ÷ (P + F). P e F somam os pesos dos itens com papel "nota" que cumprem e que têm sintoma. Não se aplica, não avaliado e requisitos saem da conta. Peso 2 em todos, até a aplicação do critério de dano.
- **Total, de 0 a 35:** soma das sete notas. Sintoma sem nota entra com 0.
- **Faixas:** Ausente até 14; Citada acima de 14 e abaixo de 26; Reconhecida de 26 a 35. Provisórias até calibração.
- **Cobertura:** itens com resposta (cumpre ou sintoma) sobre os itens com papel "nota" que se aplicam. Abaixo de 60%, ou com amostra abaixo do mínimo do perfil, a nota é provisória e a faixa não aparece; o relatório diz qual seria.
- **Confiabilidade:** parte das respostas com origem observada ou validada.
- **Nota por camada:** a mesma fórmula sobre os itens dos sintomas daquela camada; a camada 4 junta 01, 03 e 06 (o 03 só entra quando tem nota). Camada abaixo de 2,5 marca as camadas de cima como dependentes dela.
- **Prioridade:** primeiro os sintomas da camada abaixo de 2,5, se houver; depois o sintoma de menor nota, começando pelo nível 1.
- Rodadas com o primeiro número de versão diferente não são comparadas lado a lado.

**Critérios de corte padrão**, quando quem audita não definir outro:

- **Publicação recente (RX68):** última publicação há até 60 dias.
- **Cadência regular (RX69):** publicação em pelo menos 8 dos últimos 12 meses.
- **Perfil ativo (RX08, RX74):** publicação nos últimos 90 dias.
- **Variação grande (RX31, RX79):** marca presente só em parte das rodadas, ou posição variando mais de duas casas.
- **Divergência grande (RX39):** presença manual e da ferramenta diferindo em mais de um terço das perguntas.
- **Página órfã (RX62):** cumpre quando a amostra não tem página sem ligação com o cluster.
- **Série no tempo (RX76 a RX79):** não avaliado até existir a terceira medição separada no tempo; medições no mesmo dia contam como uma.

## 7. Verificações

Papel "nota" entra na conta; "requisito" e "requisito opcional" conferem a rodada de IA. Código aposentado nunca volta a ser usado: RX22 foi fundido no RX17. Fontes pelos códigos da seção 8.

### Sintoma 01 · Entidade inconsistente (Camada 4 · Memória)

| Código | Nível | Papel | O que checar | Sinal de falha | O que fazer | Método | Fonte |
|---|---|---|---|---|---|---|---|
| RX01 | 1 | nota | O nome no title da home bate com o do logo e das redes | Três grafias diferentes | Definir a grafia canônica e propagar | NAV: title, texto ou alt do logo, nome nos perfis oficiais | F1 |
| RX02 | 1 | nota | Busca pelo nome exibe painel de conhecimento | Sem painel, ou painel de outra entidade | Trabalhar os níveis 2 e 3 | NAV na busca do Google sem login; com CAPTCHA, USR com print |  |
| RX03 | 1 | nota | As variações de grafia levam ao mesmo resultado | Resultados divergentes | Declarar as variações em alternateName | WEB: uma busca por variação, comparar o domínio no topo | F1 |
| RX04 | 1 | nota | A página Sobre declara nome, fundação e razão social | Texto genérico sem dado verificável | Reescrever com dados conferíveis | NAV ou WEB na página institucional | F2 |
| RX05 | 2 | nota | Existe JSON-LD de Organization | Ausente, ou presente só no HTML cru | Implementar e validar no Rich Results | LD na home, renderizado e inicial | F1 |
| RX06 | 2 | nota | O campo name traz a grafia canônica | Nome comercial diferente do usado no mercado | Alinhar com a grafia que o público usa | LD | F1 |
| RX07 | 2 | nota | Existe alternateName com as variações reais | Campo ausente | Listar todas as grafias em circulação | LD | F1 |
| RX08 | 2 | nota | O sameAs aponta para os perfis ativos | Perfis mortos, ou faltando os principais | Atualizar e remover os inativos | LD e abertura de cada perfil; rede com login vira USR | F1 |
| RX09 | 2 | nota | O @id da Organization é estável | @id diferente por página | Fixar um @id e referenciar | LD em todas as páginas da amostra |  |
| RX10 | 2 | nota | Os outros blocos de schema referenciam a Organization pelo @id | Cada página declara uma Organization própria, sem @id em comum | Fixar um @id na declaração principal e referenciar a partir de Article, Product e WebPage | LD das páginas internas | F1 |
| RX11 | 3 | nota | Existe entrada no Wikidata apontando para o site | Sem entrada, ou sem a propriedade de site oficial | Criar ou corrigir, com fonte | WEB: API do Wikidata, conferir P856 | F14, F15 |
| RX12 | 3 | nota | O nome usado pela imprensa bate com o declarado | Imprensa usa outra grafia | Incluir a grafia da imprensa em alternateName | WEB: busca em notícias |  |
| RX13 | 3 | nota | As plataformas de IA escrevem o nome como o schema declara | Grafia divergente | Registrar e reavaliar a canônica | IA | F1 |

### Sintoma 02 · Dados ilegíveis por agente (Camada 2 · Estrutura)

| Código | Nível | Papel | O que checar | Sinal de falha | O que fazer | Método | Fonte |
|---|---|---|---|---|---|---|---|
| RX15 | 1 | nota | Preço e disponibilidade são texto selecionável | Valor dentro de imagem | Publicar como texto e marcar no schema | NAV na página de produto ou serviço | F4, F5 |
| RX16 | 1 | nota | Tabelas de especificação são tabelas HTML | Captura de tela no lugar da tabela | Refazer como tabela, com cabeçalho | NAV na página de produto | F4 |
| RX18 | 2 | nota | Existe schema do tipo correspondente à página | Só WebPage genérico | Implementar Product, Service ou Article | LD por tipo de página | F4 |
| RX19 | 2 | nota | O schema traz price, priceCurrency e availability | Campos ausentes em página transacional | Completar e manter sincronizado | LD | F5 |
| RX20 | 2 | nota | O schema traz os atributos que decidem a compra | Só nome e imagem | Incluir os atributos comparáveis | LD | F5 |
| RX21 | 2 | nota | Os headings seguem hierarquia real | Nível pulado, ou vários H1 | Refazer a hierarquia | NAV: lista de H1 a H6 |  |
| RX23 | 2 | nota | Imagem com informação tem o dado também em texto | Dado existe só na arte | Duplicar em texto, abaixo ou no alt | NAV: imagens grandes do conteúdo principal e seus alt | F4 |
| RX26 | 3 | nota | Existe feed, API ou llms.txt para consumo por máquina | Nada disponível | Publicar se o custo for baixo, sabendo que a adoção comprovada do llms.txt é de quem publica, e que a proposta não afirma que plataformas de IA leiam o arquivo | WEB em /llms.txt, /feed, /wp-json e link rel alternate. Conta como presente só com status 200 e tipo de conteúdo do arquivo (text/plain ou text/markdown, XML, JSON); 200 com HTML é sintoma, "resposta genérica" | F12, F13 |

### Sintoma 03 · Ausência entre plataformas (Camada 4 · Memória)

| Código | Nível | Papel | O que checar | Sinal de falha | O que fazer | Método | Fonte |
|---|---|---|---|---|---|---|---|
| RX28 | 1 | nota | A marca aparece na pergunta de categoria | Ausência em alguma | Registrar em qual, e seguir para o nível 2 | IA, sessão limpa por plataforma |  |
| RX29 | 1 | nota | A pergunta direta pela marca traz resposta correta | Informação errada ou desatualizada | Corrigir na origem, sintomas 01 e 04 | IA, conferida contra o site |  |
| RX30 | 1 | nota | A posição da marca na lista é registrada | Sempre no fim | Comparar com quem está na frente | IA, regra de posição da seção 5 |  |
| RX31 | 1 | nota | A mesma pergunta repetida três vezes dá o mesmo resultado | Variação grande entre rodadas | Aumentar o número de execuções antes de concluir | IA |  |
| RX32 | 2 | requisito | Existem de cinco a dez perguntas que cobrem a jornada | Só pergunta de topo | Escrever perguntas de comparação e de decisão | Lista de perguntas de quem audita |  |
| RX33 | 2 | requisito | A mesma lista roda em todas as plataformas na mesma janela | Datas diferentes por plataforma | Refazer na mesma janela | IA: coluna Data |  |
| RX34 | 2 | requisito | Está registrado em quais perguntas a marca some | Sem registro por pergunta | Registrar pergunta a pergunta | IA |  |
| RX35 | 2 | requisito | Está registrado quem aparece onde a marca não aparece | Só a própria marca acompanhada | Acompanhar de três a cinco concorrentes | IA |  |
| RX36 | 3 | requisito | Os domínios exibidos como fonte estão registrados | Sem registro | Registrar domínio e ordem de aparição | IA |  |
| RX37 | 3 | nota | Algum domínio da própria marca aparece entre as fontes | Nunca aparece | Trabalhar o sintoma 05 | IA |  |
| RX38 | 3 | requisito | A presença na Visão geral criada por IA foi conferida | Não conferida | Incluir na rodada | NAV no Google sem login, ou Semrush se conectado; senão USR | F16 |
| RX39 | 3 | requisito opcional | O resultado manual bate com o painel da ferramenta | Divergência grande | Conferir com qual grafia a ferramenta monitora | USR ou Semrush; sem ferramenta, não se aplica |  |
| RX40 | 3 | requisito | A plataforma de maior volume no setor está coberta | Plataforma relevante fora da medição | Acompanhar essa à mão | IA e USR |  |

### Sintoma 04 · Confiança não verificável (Camada 5 · Ação)

| Código | Nível | Papel | O que checar | Sinal de falha | O que fazer | Método | Fonte |
|---|---|---|---|---|---|---|---|
| RX41 | 1 | nota | Os artigos têm autor nomeado | Assinado pela marca, ou sem assinatura | Atribuir a uma pessoa real | NAV em três artigos | F2 |
| RX42 | 1 | nota | O autor tem página própria | Sem página | Criar página de autor | NAV no link da assinatura | F17, F2 |
| RX43 | 1 | nota | A página do autor traz credenciais verificáveis | Biografia sem nada conferível | Incluir formação, atuação e links | NAV | F2 |
| RX44 | 1 | nota | Existe contato com endereço e razão social | Só formulário | Publicar dados completos | NAV | F2 |
| RX45 | 1 | nota | Existe política editorial ou de revisão | Ausente | Publicar, mesmo que curta | NAV no rodapé e busca no site | F2 |
| RX46 | 2 | nota | Existe schema de Person ligado ao artigo | author como texto solto | Marcar como Person, com @id | LD do artigo | F17 |
| RX47 | 2 | nota | O autor tem sameAs para perfis externos | Campo ausente | Apontar para LinkedIn, ORCID, redes | LD de Person | F17 |
| RX48 | 2 | nota | Existe data de publicação e de atualização | Só a data de publicação | Marcar dateModified e exibir | LD e página | F17 |
| RX49 | 2 | nota | Avaliações e depoimentos reais têm marcação | Depoimento em imagem, ou avaliação que só existe dentro do próprio site | Publicar em texto e marcar, e construir avaliação em plataforma de terceiro, porque avaliação que a própria marca controla não gera estrela | NAV, LD e WEB no perfil público de avaliação | F18, F4 |
| RX50 | 2 | nota | As credenciais aparecem como texto | Selo só em imagem | Duplicar em texto | NAV nas páginas institucionais | F4 |
| RX51 | 3 | nota | As credenciais são conferíveis em fonte independente | Não há onde conferir | Trocar por credencial verificável | WEB no emissor |  |
| RX52 | 3 | nota | Existe cobertura de imprensa citando a marca | Nenhuma menção | Trabalhar assessoria ou conteúdo citável | WEB: busca em notícias |  |
| RX53 | 3 | nota | A reputação em base pública é consultável | Perfil abandonado | Assumir e responder | WEB: Reclame Aqui, Consumidor.gov.br |  |
| RX54 | 3 | nota | O autor existe fora do site | Nenhuma presença externa | Construir presença mínima | WEB: busca pelo nome do autor | F17 |

### Sintoma 05 · Distância vetorial do público (Camada 3 · Interpretação)

| Código | Nível | Papel | O que checar | Sinal de falha | O que fazer | Método | Fonte |
|---|---|---|---|---|---|---|---|
| RX55 | 1 | nota | Os termos do público estão no site | Vocabulário só técnico, ou só comercial | Incluir as duas camadas de vocabulário | WEB: perguntas reais (sugestões de busca, fóruns, prompts) contra o texto do site |  |
| RX56 | 1 | nota | O site responde à pergunta na forma em que ela é feita | Conteúdo feito para a palavra-chave, sem responder à pergunta | Criar blocos de resposta direta | Páginas principais contra as perguntas |  |
| RX57 | 1 | nota | Existe conteúdo para pergunta em linguagem natural | Só páginas de palavra-chave | Abrir perguntas longas em conteúdo próprio | Títulos do sitemap e do blog |  |
| RX58 | 1 | nota | O vocabulário do setor convive com o do público | Só jargão | Traduzir o jargão na primeira aparição | Texto das páginas |  |
| RX59 | 2 | nota | As entidades que coocorrem com o tema estão mapeadas | Sem mapeamento | Listar as entidades citadas junto do tema | IA; sem rodada, os dez primeiros resultados orgânicos como substituto declarado |  |
| RX60 | 2 | nota | O site menciona essas entidades | Ausentes | Incluir onde fizer sentido | WEB: busca site: e texto da amostra |  |
| RX61 | 2 | nota | O cluster cobre todas as etapas da jornada | Só topo, ou só fundo | Preencher a etapa faltante | Títulos do sitemap classificados por etapa |  |
| RX62 | 2 | nota | Existem páginas órfãs de tema | Página sem ligação com o cluster | Ligar ou arquivar | Links internos da amostra contra o sitemap; varredura completa é USR |  |
| RX63 | 2 | nota | Título e H1 usam a linguagem da pergunta | Título institucional | Reescrever a partir da pergunta | NAV nas páginas principais |  |
| RX64 | 3 | nota | O texto do site foi comparado com o das fontes citadas | Sem comparação | Ler as três primeiras e comparar | IA para as fontes, WEB para ler as três primeiras; a skill faz a comparação |  |
| RX65 | 3 | nota | As fontes respondem algo que o site não responde | Lacuna clara | Escrever a resposta que falta | Resultado da linha 64 |  |
| RX66 | 3 | nota | As perguntas de seguimento sugeridas estão registradas | Sem registro | Registrar como dado de intenção | IA |  |
| RX67 | 3 | nota | Essas perguntas de seguimento têm resposta no site | Nenhuma tem | Virar pauta | Linha 66 contra busca no site |  |

### Sintoma 06 · Presença não recorrente (Camada 4 · Memória)

| Código | Nível | Papel | O que checar | Sinal de falha | O que fazer | Método | Fonte |
|---|---|---|---|---|---|---|---|
| RX68 | 1 | nota | A última publicação é recente | Meses sem publicar | Definir cadência possível e cumprir | Blog, feed ou lastmod do sitemap |  |
| RX69 | 1 | nota | Existe cadência regular nos últimos doze meses | Publicação em rajada e silêncio | Distribuir ao longo do tempo | Datas do arquivo ou do feed, contadas por mês em código |  |
| RX70 | 1 | nota | O conteúdo antigo é atualizado, com data visível | Nada atualizado | Escolher as principais e revisar | LD e página: dateModified contra datePublished | F17 |
| RX71 | 1 | nota | Existe conteúdo dos últimos noventa dias sobre o tema central | Tema central parado | Publicar no tema que define a marca | Datas e títulos do cluster principal |  |
| RX72 | 2 | nota | A marca é mencionada em veículos externos com frequência | Menções raras | Trabalhar pauta e presença | WEB: notícias e blogs |  |
| RX73 | 2 | nota | As menções estão distribuídas no tempo | Tudo concentrado num período | Planejar continuidade | Datas da busca da linha 72 |  |
| RX74 | 2 | nota | Os perfis oficiais têm atividade recente | Perfil parado no sameAs | Reativar ou remover da marcação | Perfis do sameAs; rede com login vira USR |  |
| RX75 | 2 | nota | O autor publica com recorrência | Só a marca publica | Construir recorrência da pessoa | Perfil do autor; rede com login vira USR |  |
| RX76 | 3 | nota | Existem ao menos três medições separadas no tempo | Uma medição só | Repetir por três semanas | IA: histórico |  |
| RX77 | 3 | nota | O que variou entre as medições está registrado | Sem comparação | Comparar rodada a rodada | IA: histórico |  |
| RX78 | 3 | nota | A marca aparece nas três medições | Aparece em uma só | Tratar como presença não recorrente | IA: histórico |  |
| RX79 | 3 | nota | A posição na lista se manteve | Oscilação grande | Registrar e acompanhar | IA: histórico |  |
| RX80 | 3 | nota | Alguma resposta trouxe informação desatualizada sobre a marca | Dado antigo apresentado como atual | Corrigir na origem e reconferir | IA conferida contra o site |  |

### Sintoma 07 · Conteúdo fora de alcance (Camada 1 · Rastreabilidade)

| Código | Nível | Papel | O que checar | Sinal de falha | O que fazer | Método | Fonte |
|---|---|---|---|---|---|---|---|
| RX14 | 1 | nota | O conteúdo principal aparece com JavaScript desativado | Página em branco ou só menu | Renderizar no servidor o conteúdo que importa | HTML comparado ao renderizado | F3 |
| RX17 | 1 | nota | O conteúdo principal está no HTML inicial | Texto só aparece ao clicar em aba ou acordeão | Carregar no HTML e esconder por CSS | HTML | F3 |
| RX24 | 3 | nota | O robots.txt permite os rastreadores de IA que interessam | Bloqueio amplo sem decisão consciente, ou decisão tomada sem separar treino de busca | Decidir rastreador por rastreador e documentar com comentário datado, sabendo que o robots.txt resolve treino e busca e não resolve agente a pedido do usuário | WEB em /robots.txt, tabela por user-agent | F6, F7, F8, F9, F10 |
| RX25 | 3 | nota | O firewall ou CDN não bloqueia por user-agent | Agente bloqueado sem constar no robots | Alinhar a regra do CDN com a decisão | SRV: teste de user-agent pelo servidor; sem servidor, não avaliado com o script pronto | F11 |
| RX27 | 3 | nota | A resposta ao rastreador de IA tem o mesmo conteúdo | Conteúdo reduzido para o agente | Igualar, ou assumir a diferença por escrito | SRV: teste de user-agent pelo servidor; sem servidor, não avaliado com o script pronto |  |
| RX81 | 1 | nota | As páginas principais abrem sem login | Login exigido ou página de erro | Liberar o acesso público ao que deve ser encontrado | Janela anônima | F19 |
| RX82 | 1 | nota | As páginas que devem aparecer na busca não têm noindex | noindex em página que deveria indexar | Remover a diretiva | Código-fonte, meta robots | F20 |
| RX83 | 1 | nota | O robots.txt não bloqueia o site inteiro | Disallow: / para todos os rastreadores | Restringir o bloqueio ao que não deve ser rastreado | /robots.txt | F6 |
| RX84 | 1 | nota | Existe sitemap, declarado no robots.txt | Sem sitemap ou sem declaração | Publicar e declarar com a linha Sitemap | /robots.txt e /sitemap.xml | F21 |
| RX85 | 1 | nota | As páginas importantes estão a poucos cliques da home | Página principal só alcançável pela busca interna | Linkar no menu ou numa página central do tema | Navegação |  |
| RX86 | 1 | nota | Menu e links do conteúdo são links de verdade | Navegação feita só por JavaScript, sem href | Usar elemento a com href | Inspecionar elemento | F22 |
| RX87 | 2 | nota | As URLs principais respondem 200, sem cadeia de redirecionamento | 3xx em cadeia, 4xx ou 5xx | Corrigir para resposta direta | Rastreador ou curl -I | F19, F23 |
| RX88 | 2 | nota | Disallow e noindex não estão na mesma URL | Os dois juntos | Liberar o rastreio e manter o noindex | robots.txt e meta robots | F20 |
| RX89 | 2 | nota | O sitemap lista só URLs canônicas que respondem 200 | URLs com redirecionamento, 404 ou noindex | Limpar o sitemap | Amostra do sitemap | F21 |
| RX90 | 2 | nota | Só uma versão de host e protocolo responde | Duas versões respondendo 200 | Redirecionar com 301 num salto só | http, https, com e sem www | F23 |
| RX91 | 2 | nota | Busca interna e filtros não geram URLs indexáveis | Página de resultado indexável | noindex nessas páginas, sem bloquear o rastreio | URL de busca e de filtro |  |
| RX92 | 3 | nota | O Search Console não mostra páginas principais fora do índice | Páginas principais excluídas ou bloqueadas | Inspecionar cada URL e tratar a causa | Search Console, relatório de indexação |  |
| RX93 | 3 | nota | A busca site: traz as páginas principais | Página principal ausente | Inspecionar a URL no Search Console | Google, site:dominio |  |

## 8. Fontes

- F1: Organization schema markup (Google Search Central): https://developers.google.com/search/docs/appearance/structured-data/organization
- F2: Creating helpful, reliable, people-first content (Google Search Central): https://developers.google.com/search/docs/fundamentals/creating-helpful-content
- F3: Understand JavaScript SEO basics (Google Search Central): https://developers.google.com/search/docs/crawling-indexing/javascript/javascript-seo-basics
- F4: General structured data guidelines (Google Search Central): https://developers.google.com/search/docs/appearance/structured-data/sd-policies
- F5: Product snippet structured data (Google Search Central): https://developers.google.com/search/docs/appearance/structured-data/product-snippet
- F6: Robots.txt introduction and guide (Google Search Central): https://developers.google.com/search/docs/crawling-indexing/robots/intro
- F7: List of Google's common crawlers (Google for Developers): https://developers.google.com/crawling/docs/crawlers-fetchers/google-common-crawlers
- F8: Overview of OpenAI crawlers (OpenAI): https://developers.openai.com/api/docs/bots
- F9: Does Anthropic crawl data from the web, and how can site owners block the crawler? (Claude Help Center): https://support.claude.com/en/articles/8896518-does-anthropic-crawl-data-from-the-web-and-how-can-site-owners-block-the-crawler
- F10: Perplexity crawlers (Perplexity): https://docs.perplexity.ai/guides/bots
- F11: Bot reference (Cloudflare AI Crawl Control docs): https://developers.cloudflare.com/ai-crawl-control/reference/bots/
- F12: The /llms.txt file, v2 (llmstxt.org): https://llmstxt.org/
- F13: Your site, your rules: new AI traffic options for all customers (Cloudflare Blog): https://blog.cloudflare.com/content-independence-day-ai-options/
- F14: Wikidata:Notability (Wikidata): https://www.wikidata.org/wiki/Wikidata:Notability
- F15: official website (P856) (Wikidata): https://www.wikidata.org/wiki/Property:P856
- F16: AI features and your website (Google Search Central): https://developers.google.com/search/docs/appearance/ai-features
- F17: Article schema markup (Google Search Central): https://developers.google.com/search/docs/appearance/structured-data/article
- F18: Review snippet structured data (Google Search Central): https://developers.google.com/search/docs/appearance/structured-data/review-snippet
- F19: How HTTP status codes affect Google's crawlers (Google Crawling Infrastructure): https://developers.google.com/crawling/docs/troubleshooting/http-status-codes
- F20: Block Search indexing with noindex (Google Search Central): https://developers.google.com/search/docs/crawling-indexing/block-indexing
- F21: Learn about sitemaps (Google Search Central): https://developers.google.com/search/docs/crawling-indexing/sitemaps/overview
- F22: Link best practices for Google (Google Search Central): https://developers.google.com/search/docs/crawling-indexing/links-crawlable
- F23: Redirects and Google Search (Google Search Central): https://developers.google.com/search/docs/crawling-indexing/301-redirects

Linha sem fonte na tabela não recebe fonte inventada no relatório. A justificativa vem do próprio modelo autoral, identificado como Arquitetura Cognitiva do SEO.

## 9. Fluxo de execução

1. Criar lista de tarefas: entrada, amostra, sintomas 07 e 02 (rastreabilidade e estrutura), 01 e 05, 04 e 06, rodada de IA, nota, entregáveis, conferência.
2. Coletar a entrada (seção 1) e fixar o perfil (seção 3).
3. Montar a amostra e rodar os scripts da seção 4 em cada página. Guardar os resultados em arquivo de trabalho.
4. Responder os itens, sempre com evidência. Para vídeo ou demonstração, escrever na conversa uma linha por item: código, o que checou, evidência.
5. Tratar a rodada de IA conforme a seção 5.
6. Montar o JSON da avaliação (seção 10) e gerar os entregáveis com os scripts da seção 11.
7. Conferir: cada cumpre e cada sintoma tem evidência; a nota do PDF bate com a da planilha recalculada; os 92 itens aparecem na planilha; nenhuma fonte foi inventada; o texto segue a seção 12.
8. Oferecer a medição recorrente (seção 5).

## 10. Entregáveis

Gerar os três a partir do mesmo JSON. Relatório em docx só quando quem audita pedir.

**JSON da avaliação** (`raio-x-DOMINIO-AAAA-MM-DD.json`), contrato 1.1.0:

```json
{"schema_version":"1.1.0","tool":"raio-x-marca-reconhecivel","methodology_version":"1.1.0","producer":"skill",
 "created_at":"2026-11-04T10:00:00-03:00",
 "profile":{"domain":"exemplo.com.br","brand":"Marca","site_profile":"loja_sem_conteudo","platform":"Nuvemshop","auditor":"Nome opcional","sample":["/","/sobre/"]},
 "ai_round":{"status":"done|not_run","prompts":[{"date":"","platform":"","prompt":"","type":"categoria|comparacao|decisao|marca","run":1,"brands":[],"position":"","sources":[],"note":""}]},
 "checks":[{"id":"RX01","group":"01","layer":4,"weight":2,"role":"nota","result":"pass","origin":"observed","evidence":"","collected_at":"","na_reason":"","missing":""}]}
```

O arquivo não carrega nome de pessoa auditada nem e-mail; `auditor` é opcional e só vai para a capa.

**Relatório em PDF**, nesta ordem e sem quebra forçada de página: painel da nota (total, faixa ou aviso de provisória, cobertura, confiabilidade, sintomas detectados, amostra, barras por sintoma, camadas, "Por onde começar"); prioridades; achados (um título por item com sintoma, com Evidência, Sinal de falha, O que fazer e Por que importa com link da fonte; print da área quando houver); comentários da auditoria, item a item; rodada de IA; não se aplica; pendências; método; assinatura discreta de Amanda Noronha como criadora do método e da ferramenta.

**Planilha .xlsx**: abas Painel (primeira, com fórmulas que recalculam ao mudar uma resposta), Verificações (resultado e origem com lista suspensa e cor por estado, observações na última coluna), Rodada de IA e Método. Depois de gerar, recalcular com o script da skill xlsx e conferir zero erro de fórmula.

**Identidade:** Fúcsia Amanda #EC1180, Azul Noturno #0C1438, Azul Elevado #142155, Azul Bic Profundo #003A8C, Ciano #22E8F0, Gelo Técnico #F4F7FF, Cinza Suave #E3E6EF. Chivo nos títulos, Inter no corpo, pelo npm `@fontsource/chivo@5.1.0` e `@fontsource/inter@5.1.0`. Logo: o arquivo `logo.png` que acompanha a skill, na mesma pasta deste SKILL.md, copiado para a pasta de trabalho. Sem esse arquivo, usar a runa do `iconbrandbook.png` do projeto Amanda Noronha® (ferramenta Projects, `project_read`), recortada em 360 px; sem nenhum dos dois, gerar sem logo.

Se quem audita pedir no Google Sheets e o conector estiver ativo, criar uma planilha nova a partir do xlsx, nunca editar a planilha de origem.

## 11. Código

Gravar os arquivos abaixo numa pasta de trabalho, instalar as fontes (`npm i @fontsource/chivo@5.1.0 @fontsource/inter@5.1.0`) e rodar:

```bash
python3 catalogo.py CAMINHO_DESTE_SKILL.md catalogo.json
python3 pdf.py catalogo.json avaliacao.json relatorio.html
node render.js "$PWD/relatorio.html" "$PWD/relatorio.pdf" dominio.com.br DD/MM/AAAA
python3 planilha.py catalogo.json avaliacao.json raio-x.xlsx
```

Playwright e Chromium vêm instalados no ambiente; não reinstalar.

**catalogo.py**

```python
"""Lê as tabelas da seção 7 e as fontes da seção 8 deste SKILL.md e grava catalogo.json."""
import re, json, sys
s=open(sys.argv[1],encoding='utf-8').read()
FONTES={m.group(1):{'titulo':m.group(2),'editor':m.group(3),'url':m.group(4)} for m in re.finditer(r'^- (F\d+): (.+?) \(([^)]+)\): (\S+)$',s,re.M)}
SYM={}; itens=[]; cur=None
for line in s.split('\n'):
    m=re.match(r'### Sintoma (\d\d) · (.+?) \(Camada (\d)',line)
    if m: cur=m.group(1); SYM[cur]={'nome':m.group(2),'camada':int(m.group(3))}; continue
    if line.startswith('## 8.'): cur=None
    if cur and re.match(r'\| RX\d+ \|',line):
        c=[x.strip() for x in line.strip().strip('|').split('|')]
        itens.append({'id':c[0],'n':int(c[0][2:]),'grupo':cur,'camada':SYM[cur]['camada'],'nivel':int(c[1]),'papel':c[2].replace(' ','_'),
          'checar':c[3],'falha':c[4],'fazer':c[5],'metodo':c[6],'fontes':[f.strip() for f in c[7].split(',') if f.strip()],'peso':2})
json.dump({'metodologia':'1.1.0','sintomas':SYM,'fontes':FONTES,'itens':itens},open(sys.argv[2],'w'),ensure_ascii=False,indent=1)
print(len(itens),'itens')
```

**motor.py**

```python
"""Motor de nota do raio-x, metodologia 1.1.0 (documento 01)."""
import json
RES_PT={'pass':'cumpre','fail':'sintoma detectado','na':'não se aplica','unassessed':'não avaliado'}
ORI_PT={'declared':'declarada','observed':'observada','validated':'validada'}
PERFIL_PT={'loja_sem_conteudo':'loja sem conteúdo editorial','loja_com_conteudo':'loja com blog','site_com_blog':'site com blog e autor','institucional':'institucional sem preço e sem blog'}
AMOSTRA_MIN={'loja_sem_conteudo':7,'loja_com_conteudo':9,'site_com_blog':8,'institucional':5}
SEM_PRECO=['RX15','RX16','RX19','RX20']
SEM_BLOG=['RX41','RX42','RX43','RX45','RX46','RX47','RX48','RX54','RX70','RX75']
NSA_PERFIL={'loja_sem_conteudo':SEM_BLOG,'loja_com_conteudo':[],'site_com_blog':SEM_PRECO,'institucional':SEM_PRECO+SEM_BLOG}
def faixa(t):
    return 'Ausente' if t<=14 else ('Citada' if t<26 else 'Reconhecida')
def calcular(av,cat):
    it={i['id']:i for i in cat['itens']}
    fixos=NSA_PERFIL.get(av['profile'].get('site_profile'),[])
    for c in av['checks']:
        if c['id'] in fixos and c['result']!='na':
            c.update(result='na',origin='declared',na_reason='Não se aplica pelo perfil do site ('+PERFIL_PT[av['profile']['site_profile']]+').')
    ck=[dict(c,**{k:it[c['id']][k] for k in ('grupo','nivel','checar','falha','fazer','fontes','camada','papel')}) for c in av['checks']]
    for c in ck: c['fonte_texto']=it[c['id']].get('fonte_texto','')
    nota={}; det={}
    req=[c for c in ck if c['papel']=='requisito']
    req_ok=all(c['result']=='pass' for c in req) if req else True
    for g in cat['sintomas']:
        xs=[c for c in ck if c['grupo']==g and c['papel']=='nota']
        P=sum(c['weight'] for c in xs if c['result']=='pass'); F=sum(c['weight'] for c in xs if c['result']=='fail')
        v=round(5*P/(P+F)+1e-9,2) if P+F else None
        if g=='03' and not req_ok: v=None
        nota[g]=v; det[g]={'pass':sum(c['result']=='pass' for c in xs),'fail':sum(c['result']=='fail' for c in xs),
            'na':sum(c['result']=='na' for c in xs),'unassessed':sum(c['result']=='unassessed' for c in xs),'total':len(xs)}
    camada={}
    for L in range(1,6):
        xs=[c for c in ck if c['camada']==L and c['papel']=='nota' and not (c['grupo']=='03' and nota['03'] is None)]
        P=sum(c['weight'] for c in xs if c['result']=='pass'); F=sum(c['weight'] for c in xs if c['result']=='fail')
        camada[L]=round(5*P/(P+F)+1e-9,2) if P+F else None
    comprometida_por=None
    for L in range(1,6):
        if camada[L] is not None and camada[L]<2.5: comprometida_por=L; break
    total=round(sum(v or 0 for v in nota.values()),2)
    sc=[c for c in ck if c['papel']=='nota']
    resp=[c for c in sc if c['result'] in('pass','fail')]
    na=sum(c['result']=='na' for c in sc)
    cobertura=len(resp)/(len(sc)-na) if len(sc)-na else 0
    conf=sum(c['origin'] in('observed','validated') for c in resp)/len(resp) if resp else 0
    perfil=av['profile'].get('site_profile')
    amostra=len(av['profile'].get('sample',[])); minimo=AMOSTRA_MIN.get(perfil,7)
    provisoria=cobertura<0.6 or amostra<minimo
    flags=[]
    if comprometida_por: flags.append('camada_comprometida')
    if not req_ok: flags.append('rodada_ia_incompleta')
    if amostra<minimo: flags.append('amostra_abaixo_do_minimo')
    # prioridade: camada comprometida mais baixa; depois menor nota; nível 1 antes
    falhas=[c for c in ck if c['result']=='fail' and c['papel']=='nota']
    def chave(c):
        bloq=0 if (comprometida_por and c['camada']==comprometida_por) else 1
        return (bloq, nota[c['grupo']] if nota[c['grupo']] is not None else 9, c['nivel'], c['camada'], c['id'])
    falhas.sort(key=chave)
    alvo=None
    if comprometida_por: alvo=('camada',comprometida_por)
    else:
        com=[(v,g) for g,v in nota.items() if v is not None]
        if com: alvo=('sintoma',min(com)[1])
    return {'checks':ck,'nota':nota,'det':det,'camada':camada,'total':total,'faixa':None if provisoria else faixa(total),
            'faixa_calculada':faixa(total),'provisoria':provisoria,'cobertura':cobertura,'respondidos':len(resp),'base':len(sc)-na,
            'confiabilidade':conf,'req_ok':req_ok,'requisitos':req,'flags':flags,'comprometida_por':comprometida_por,'prioridades':falhas,'alvo':alvo,
            'amostra':amostra,'amostra_min':minimo}
```

**pdf.py**

```python
import json, html, sys, os, datetime
import motor
FD=os.path.abspath('node_modules/@fontsource')
LOGO='logo.png'
def fmt(v,d=1): return '' if v is None else f"{v:.{d}f}".replace('.',',')
def pct(v): return f"{round(v*100):d}%"
def e(s): return html.escape(str(s or ''))
COR={'pass':'#1F7A4D','fail':'#EC1180','na':'#8A90A8','unassessed':'#5A0099'}
def barra_cor(v):
    if v is None: return None
    return '#EC1180' if v<2.5 else ('#003A8C' if v<4 else '#142155')
def fonte_html(c,cat):
    out=[]
    for f in c['fontes']:
        F=cat['fontes'].get(f)
        if F: out.append(f'<a href="{e(F["url"])}">{e(F["titulo"])}</a> ({e(F["editor"])})')
    if c.get('fonte_texto'): out.append(e(c['fonte_texto']))
    return '; '.join(out) or 'Arquitetura Cognitiva do SEO, modelo autoral'
def gerar(av,cat,saida_html):
    r=motor.calcular(av,cat); p=av['profile']; S=cat['sintomas']
    data=datetime.datetime.fromisoformat(av['created_at']).strftime('%d/%m/%Y')
    css=f"""
@font-face{{font-family:Chivo;font-weight:600;src:url(file://{FD}/chivo/files/chivo-latin-600-normal.woff2)}}
@font-face{{font-family:Chivo;font-weight:800;src:url(file://{FD}/chivo/files/chivo-latin-800-normal.woff2)}}
@font-face{{font-family:Chivo;font-weight:900;src:url(file://{FD}/chivo/files/chivo-latin-900-normal.woff2)}}
@font-face{{font-family:Inter;font-weight:300;src:url(file://{FD}/inter/files/inter-latin-300-normal.woff2)}}
@font-face{{font-family:Inter;font-weight:400;src:url(file://{FD}/inter/files/inter-latin-400-normal.woff2)}}
@font-face{{font-family:Inter;font-weight:600;src:url(file://{FD}/inter/files/inter-latin-600-normal.woff2)}}
@page{{size:A4;margin:16mm 15mm 16mm 15mm}}
*{{box-sizing:border-box}}
body{{font-family:Inter,sans-serif;font-weight:400;color:#0C1438;font-size:9.6pt;line-height:1.45;margin:0}}
h1,h2,h3,.big{{font-family:Chivo,sans-serif}}
h2{{font-weight:800;font-size:17pt;margin:0 0 3mm;color:#0C1438}}
h3{{font-weight:600;font-size:11pt;margin:0 0 1.5mm}}
.lead{{color:#3B4466;margin:0 0 5mm;max-width:165mm}}
.capa{{background:#0C1438;color:#F4F7FF;padding:7mm 8mm 6mm 9mm;border-left:2.2mm solid #EC1180;border-radius:1.5mm;display:flex;justify-content:space-between;align-items:center;gap:8mm}}
.capa img{{width:17mm;height:17mm}}
.capa h1 span{{color:#EC1180;white-space:nowrap}}
.capa .sub{{font-weight:600;color:#fff;font-size:9.5pt;margin-bottom:2.5mm}}
.rot{{font-family:Inter;font-weight:600;font-size:8pt;letter-spacing:.12em;text-transform:uppercase;color:#EC1180}}
.capa h1{{font-weight:800;font-size:21pt;line-height:1.12;margin:0 0 1.5mm;color:#fff}}
.meta{{color:#C9CFEA;font-size:9pt}}
.painel{{padding:6mm 0 0}}
.linha{{display:flex;gap:5mm}}
.score{{background:#142155;color:#fff;border-radius:4mm;padding:6mm 7mm;width:72mm}}
.score .big{{font-weight:900;font-size:46pt;line-height:1}}
.score .de{{font-size:14pt;font-weight:300;color:#C9CFEA}}
.chip{{display:inline-block;border-radius:10mm;padding:1mm 3.5mm;font-weight:600;font-size:8.5pt;margin-top:3mm}}
.prov{{font-size:8.2pt;color:#C9CFEA;margin-top:2.5mm}}
.kpis{{flex:1;display:grid;grid-template-columns:1fr 1fr;gap:4mm}}
.kpi{{background:#F4F7FF;border-radius:4mm;padding:4.5mm 5mm}}
.kpi .v{{font-family:Chivo;font-weight:800;font-size:20pt;color:#003A8C;line-height:1.1}}
.kpi .l{{font-size:8.2pt;color:#3B4466}}
.sec{{margin-top:9mm}}
h2{{page-break-after:avoid}}
tr{{page-break-inside:avoid}}
thead{{display:table-header-group}}
.junto{{page-break-inside:avoid}}
.sec>h2+.lead{{page-break-after:avoid}}
.bar{{display:grid;grid-template-columns:9mm 54mm 1fr 12mm;align-items:center;gap:2.5mm;margin:1.6mm 0}}
.cod{{font-weight:600;color:#EC1180}}
.nm{{font-size:9pt}} .nm small{{display:block;color:#6B7392;font-size:7.4pt}}
.trk{{height:4.2mm;background:#E3E6EF;border-radius:3mm;position:relative;overflow:hidden}}
.fill{{height:100%;border-radius:3mm}}
.trk.sem{{background:repeating-linear-gradient(45deg,#F4F7FF,#F4F7FF 2mm,#E3E6EF 2mm,#E3E6EF 4mm)}}
.val{{font-family:Chivo;font-weight:800;text-align:right}}
.cam{{display:flex;flex-direction:column;gap:1.4mm}}
.cm{{display:grid;grid-template-columns:7mm 1fr 14mm;align-items:center;background:#F4F7FF;border-radius:2.5mm;padding:1.8mm 3mm}}
.cm b{{font-family:Chivo;color:#EC1180}}
.alerta{{background:#FDE7F2;border-radius:3mm;padding:3.5mm 4.5mm;margin-top:5mm}}
.alerta b{{color:#C30E6B}}
.quebra{{page-break-before:always}}
table{{width:100%;border-collapse:collapse;font-size:8.6pt}}
th{{text-align:left;font-weight:600;background:#0C1438;color:#fff;padding:2mm 2.5mm}}
td{{padding:1.8mm 2.5mm;border-bottom:.3mm solid #E3E6EF;vertical-align:top}}
.tag{{display:inline-block;white-space:nowrap;border-radius:2mm;padding:.3mm 2mm;font-size:7.4pt;font-weight:600;color:#fff}}
.achado{{border:.3mm solid #E3E6EF;border-radius:3mm;padding:4mm 5mm;margin:0 0 3.5mm;page-break-inside:avoid}}
.achado p{{margin:1.2mm 0}}
.achado .t{{display:flex;justify-content:space-between;gap:4mm}}
.achado .id{{font-family:Chivo;font-weight:800;color:#EC1180;white-space:nowrap}}
.mini{{font-size:7.6pt;color:#6B7392}}
a{{color:#003A8C}}
.assin{{border-top:.3mm solid #0C1438;margin-top:9mm;padding-top:5mm;text-align:center;font-size:8.2pt;color:#3B4466;line-height:1.6;page-break-inside:avoid}}
.assin img{{width:10mm;height:10mm}}
.assin .nome{{font-family:Chivo;font-weight:800;font-size:10.5pt;color:#0C1438;margin-top:1mm}}
.assin .nota{{font-size:7pt;color:#6B7392;margin-top:1mm}}
"""
    # painel
    fx=r['faixa']; chip=('#EC1180','#fff') if fx=='Citada' else (('#22E8F0','#0C1438') if fx=='Reconhecida' else ('#F4F7FF','#0C1438'))
    if fx: banda=f'<span class="chip" style="background:{chip[0]};color:{chip[1]}">Faixa {e(fx)}</span>'
    else: banda=f'<span class="chip" style="background:#F4F7FF;color:#0C1438">Nota provisória</span><div class="prov">Cobertura abaixo de 60%: a faixa não é exibida. Pela nota, a faixa seria {e(r["faixa_calculada"])}.</div>'
    bars=''
    for g,v in r['nota'].items():
        d=r['det'][g]; sub=f"Camada {S[g]['camada']} · {d['pass']+d['fail']} de {d['total']-d['na']} respondidos"
        if v is None:
            motivo='rodada de IA incompleta' if g=='03' and not r['req_ok'] else 'sem item respondido'
            bars+=f'<div class="bar"><span class="cod">{g}</span><span class="nm">{e(S[g]["nome"])}<small>{sub}</small></span><div class="trk sem"></div><span class="val" style="font-size:7.4pt;font-family:Inter;font-weight:600;color:#6B7392">sem nota</span></div>'
        else:
            bars+=f'<div class="bar"><span class="cod">{g}</span><span class="nm">{e(S[g]["nome"])}<small>{sub}</small></span><div class="trk"><div class="fill" style="width:{v/5*100:.0f}%;background:{barra_cor(v)}"></div></div><span class="val">{fmt(v)}</span></div>'
    nomes={1:'Rastreabilidade',2:'Estrutura',3:'Interpretação',4:'Memória',5:'Ação'}
    cams=''
    for L in range(5,0,-1):
        v=r['camada'][L]; aviso=''
        if r['comprometida_por'] and L>r['comprometida_por']: aviso=' <span class="tag" style="background:#EC1180">depende da camada %d</span>'%r['comprometida_por']
        cams+=f'<div class="cm"><b>{L}</b><span>{nomes[L]}{aviso}</span><span class="val">{fmt(v) or "sem nota"}</span></div>'
    alvo=r['alvo']
    if alvo and alvo[0]=='camada': inicio=f"camada {alvo[1]}, {nomes[alvo[1]]}, com nota abaixo de 2,5. As camadas de cima dependem dela."
    elif alvo: inicio=f"sintoma {alvo[1]}, {S[alvo[1]]['nome']} ({fmt(r['nota'][alvo[1]])}), pelos itens de nível 1. Nenhuma camada ficou abaixo de 2,5."
    else: inicio='sem item respondido.'
    perfil=motor.PERFIL_PT.get(p.get('site_profile'),p.get('site_profile',''))
    H=f"""<!doctype html><html lang="pt-BR"><head><meta charset="utf-8"><style>{css}</style></head><body>
<div class="capa"><div><h1>Raio-x da marca reconhecível: <span>{e(p.get('brand',p['domain']))}</span></h1><div class="sub">Relatório de auditoria</div>
<div class="meta">{e(p['domain'])} · Coleta: {data} · Metodologia {e(av['methodology_version'])} · Perfil: {e(perfil)}{(' · '+e(p.get('platform'))) if p.get('platform') else ''}</div>
{('<div class="meta"><b style="color:#22E8F0">Auditoria:</b> '+e(p.get('auditor'))+'</div>') if p.get('auditor') else ''}</div>
{('<img src="file://'+os.path.abspath(LOGO)+'" alt="Amanda Noronha">') if os.path.exists(LOGO) else ''}</div>
<div class="painel">
<div class="linha"><div class="score"><div class="rot" style="color:#22E8F0">Pontuação total</div><div><span class="big">{fmt(r['total'])}</span> <span class="de">/ 35</span></div>{banda}</div>
<div class="kpis">
<div class="kpi"><div class="v">{pct(r['cobertura'])}</div><div class="l"><b>Cobertura:</b> {r['respondidos']} de {r['base']} itens com resposta</div></div>
<div class="kpi"><div class="v">{pct(r['confiabilidade'])}</div><div class="l"><b>Confiabilidade:</b> respostas com evidência coletada</div></div>
<div class="kpi"><div class="v">{len(r['prioridades'])}</div><div class="l"><b>Sintomas detectados:</b> itens com sinal de falha</div></div>
<div class="kpi"><div class="v">{r['amostra']}</div><div class="l"><b>Amostra:</b> páginas auditadas, mínimo {r['amostra_min']} para o perfil</div></div>
</div></div>
<div class="sec linha"><div style="flex:1.55"><h3>Nota por sintoma, de 0 a 5</h3>{bars}</div>
<div style="flex:1"><h3>Nota por camada</h3><div class="cam">{cams}</div></div></div>
<div class="alerta"><b>Por onde começar:</b> {e(inicio)}{' A rodada de IA não foi feita, por isso o sintoma 03 fica sem nota e entra com 0 no total.' if not r['req_ok'] else ''}</div>
</div>"""
    # prioridades
    rows=''.join(f"<tr><td>{i}</td><td><b>{c['id']}</b></td><td>{e(c['checar'])}</td><td>{c['grupo']} · camada {c['camada']}</td><td>{c['nivel']}</td></tr>" for i,c in enumerate(r['prioridades'][:12],1))
    H+=f"""<div class="sec junto"><h2>Prioridades</h2><p class="lead">Ordem de ataque pela regra da metodologia: primeiro a camada abaixo de 2,5, se houver; depois o sintoma de menor nota, começando pelo nível 1.</p>
<table><thead><tr><th>#</th><th>Item</th><th>O que checar</th><th>Sintoma</th><th>Nível</th></tr></thead><tbody>{rows}</tbody></table></div>"""
    # achados
    H+='<div class="sec"><h2>Achados</h2><p class="lead">Um item por achado, na mesma ordem das prioridades.</p>'
    for c in r['prioridades']:
        H+=f"""<div class="achado"><div class="t"><h3>{e(c['checar'])}</h3><span class="id">{c['id']}</span></div>
<div class="mini">Sintoma {c['grupo']}, {e(S[c['grupo']]['nome'])} · camada {c['camada']} · nível {c['nivel']} · coleta {data}</div>
<p><b>Evidência:</b> {e(c.get('evidence'))}</p><p><b>Sinal de falha:</b> {e(c['falha'])}</p><p><b>O que fazer:</b> {e(c['fazer'])}</p><p><b>Por que importa:</b> {fonte_html(c,cat)}</p></div>"""
    H+='</div>'
    # comentários, logo depois dos achados
    rows=''
    for c in r['checks']:
        obs=c.get('evidence') or c.get('na_reason') or c.get('missing') or ''
        rows+=f"<tr><td><b>{c['id']}</b></td><td>{c['grupo']}</td><td><span class='tag' style='background:{COR[c['result']]}'>{motor.RES_PT[c['result']]}</span></td><td>{motor.ORI_PT[c['origin']]}</td><td>{e(obs)}</td></tr>"
    H+=f"""<div class="sec"><h2>Comentários da auditoria</h2><p class="lead">Registro completo, item a item, com o resultado, a origem da evidência e a observação da coleta.</p>
<table><thead><tr><th style="width:14mm">Item</th><th style="width:12mm">Sint.</th><th style="width:30mm">Resultado</th><th style="width:20mm">Origem</th><th>Observação</th></tr></thead><tbody>{rows}</tbody></table></div>"""
    # rodada IA
    reqrows=''.join(f"<tr><td><b>{c['id']}</b></td><td>{e(c['checar'])}</td><td>{motor.RES_PT[c['result']]}</td></tr>" for c in r['requisitos'])
    H+=f"""<div class="sec"><h2>Rodada de IA</h2><p class="lead">O sintoma 03 só recebe nota quando a rodada cumpre os requisitos abaixo. Eles conferem o processo de medição e não entram na nota.</p>
<table><thead><tr><th style="width:14mm">Item</th><th>Requisito</th><th style="width:32mm">Situação</th></tr></thead><tbody>{reqrows}</tbody></table></div>"""
    # não se aplica
    nas=[c for c in r['checks'] if c['result']=='na']
    rows=''.join(f"<tr><td><b>{c['id']}</b></td><td>{e(c['checar'])}</td><td>{e(c.get('na_reason'))}</td></tr>" for c in nas)
    H+=f"""<div class="sec"><h2>Não se aplica</h2><table><thead><tr><th style="width:14mm">Item</th><th style="width:70mm">O que checar</th><th>Motivo</th></tr></thead><tbody>{rows}</tbody></table></div>"""
    # pendências
    pend=[c for c in r['checks'] if c['result']=='unassessed' and c['papel']=='nota']
    rows=''.join(f"<tr><td><b>{c['id']}</b></td><td>{e(c['checar'])}</td><td>{e(c.get('missing'))}</td></tr>" for c in pend)
    H+=f"""<div class="sec"><h2>Pendências</h2><p class="lead">{len(pend)} itens ficaram como não avaliado. Cada um diz o que falta para entrar na conta.</p>
<table><thead><tr><th style="width:14mm">Item</th><th style="width:70mm">O que checar</th><th>O que falta</th></tr></thead><tbody>{rows}</tbody></table></div>"""
    H+=f"""<div class="sec"><h2>Método</h2><p><b>Nota do sintoma:</b> 5 × P ÷ (P + F), em que P e F somam os pesos dos itens que cumprem e dos que têm sintoma. Não se aplica e não avaliado saem da conta. <b>Total:</b> soma das sete notas, de 0 a 35; sintoma sem nota entra com 0. <b>Faixas:</b> Ausente até 14, Citada acima de 14 e abaixo de 26, Reconhecida de 26 a 35. <b>Cobertura:</b> itens respondidos sobre o total de itens que se aplicam; abaixo de 60%, a nota é provisória e a faixa não aparece. <b>Confiabilidade:</b> parte das respostas com evidência observada ou validada. <b>Camadas:</b> camada com nota abaixo de 2,5 compromete a leitura das de cima.</p>
</div>
<div class="assin">{('<img src="file://'+os.path.abspath(LOGO)+'" alt="">') if os.path.exists(LOGO) else ''}<div class="nome">Amanda Noronha</div>
<div>Criadora do método e da ferramenta Raio-x da marca reconhecível</div>
<div>Head de SEO · Consultora · Professora de MBA · Pesquisadora · Semrush Ambassador</div>
<div><a href="https://amandanoronha.com.br">amandanoronha.com.br</a> · <a href="mailto:contato@amandanoronha.com.br">contato@amandanoronha.com.br</a> · <a href="https://www.linkedin.com/in/noronhamandy">linkedin.com/in/noronhamandy</a> · @noronhamandy</div>
<div class="nota">Auditoria feita com o Raio-x da marca reconhecível, metodologia {e(av['methodology_version'])}, em {data}. Método e ferramenta de Amanda Noronha®, construídos sobre a Arquitetura Cognitiva do SEO. Os resultados dependem das páginas e plataformas disponíveis na data da coleta.</div></div>
</body></html>"""
    open(saida_html,'w',encoding='utf-8').write(H)
    return r
if __name__=='__main__':
    cat=json.load(open(sys.argv[1])); av=json.load(open(sys.argv[2]))
    gerar(av,cat,sys.argv[3])
```

**planilha.py**

```python
import json, sys, datetime
from openpyxl import Workbook
from openpyxl.styles import Font, PatternFill, Alignment, Border, Side
from openpyxl.formatting.rule import CellIsRule, DataBarRule, FormulaRule
from openpyxl.worksheet.datavalidation import DataValidation
from openpyxl.utils import get_column_letter
import motor
from openpyxl.drawing.image import Image as XLImage
import os
NOT='0C1438'; ELEV='142155'; FUC='EC1180'; BIC='003A8C'; GELO='F4F7FF'; CINZA='E3E6EF'
TIT=Font(name='Chivo',bold=True,size=20,color='FFFFFF'); H=Font(name='Inter',bold=True,size=10,color='FFFFFF')
B=Font(name='Inter',size=10,color=NOT); BB=Font(name='Inter',size=10,bold=True,color=NOT); ROT=Font(name='Inter',size=8,bold=True,color=FUC)
fill=lambda c:PatternFill('solid',start_color=c,end_color=c)
fina=Border(bottom=Side(style='thin',color=CINZA))
WRAP=Alignment(wrap_text=True,vertical='top'); MID=Alignment(vertical='center')
def gerar(av,cat,saida):
    r=motor.calcular(av,cat); S=cat['sintomas']; p=av['profile']
    wb=Workbook(); pn=wb.active; pn.title='Painel'
    vf=wb.create_sheet('Verificações'); ia=wb.create_sheet('Rodada de IA'); me=wb.create_sheet('Método')
    # ---------- Verificações (dados) ----------
    cols=[('Item',8),('Sintoma',9),('Camada',8),('Nível',7),('Papel',12),('Peso',6),('O que checar',46),('Sinal de falha',34),('O que fazer',38),('Resultado',18),('Origem',12),('Data da coleta',13),('Fonte',34),('Observações',70)]
    for j,(n,w) in enumerate(cols,1):
        c=vf.cell(1,j,n); c.font=H; c.fill=fill(NOT); c.alignment=MID; vf.column_dimensions[get_column_letter(j)].width=w
    vf.row_dimensions[1].height=24; vf.freeze_panes='H2'
    papel_pt={'nota':'nota','requisito':'requisito','requisito_opcional':'requisito opcional'}
    data=datetime.datetime.fromisoformat(av['created_at']).strftime('%d/%m/%Y')
    for i,c in enumerate(r['checks'],2):
        fontes='; '.join(cat['fontes'][f]['titulo'] for f in c['fontes'] if f in cat['fontes']) or c.get('fonte_texto','')
        obs=c.get('evidence') or c.get('na_reason') or c.get('missing') or ''
        vals=[c['id'],c['grupo'],c['camada'],c['nivel'],papel_pt[c['papel']],c['weight'],c['checar'],c['falha'],c['fazer'],motor.RES_PT[c['result']],motor.ORI_PT[c['origin']],data if c['result'] in('pass','fail') else '',fontes,obs]
        for j,v in enumerate(vals,1):
            x=vf.cell(i,j,v); x.font=BB if j==1 else B; x.alignment=WRAP; x.border=fina
    n=len(r['checks'])+1
    dv=DataValidation(type='list',formula1='"cumpre,sintoma detectado,não se aplica,não avaliado"',allow_blank=False); vf.add_data_validation(dv); dv.add(f'J2:J{n}')
    dv2=DataValidation(type='list',formula1='"declarada,observada,validada"'); vf.add_data_validation(dv2); dv2.add(f'K2:K{n}')
    for txt,cor,fc in [('cumpre','E5F4E4','1F7A4D'),('sintoma detectado','FDE7F2','C30E6B'),('não se aplica','F0EFEC','5B6070'),('não avaliado','EFEDFF','5A0099')]:
        vf.conditional_formatting.add(f'J2:J{n}',CellIsRule(operator='equal',formula=[f'"{txt}"'],fill=fill(cor),font=Font(name='Inter',bold=True,color=fc)))
    vf.auto_filter.ref=f'A1:N{n}'
    D='$'  # nenhum cifrão seguido de dígito no texto da skill
    faixa=lambda c:f'Verificações!{D}{c}{D}2:{D}{c}{D}{n}'
    R=faixa('J'); G=faixa('B'); Pp=faixa('E'); W=faixa('F'); L=faixa('C'); O=faixa('K')
    # ---------- Painel ----------
    for col,w in zip('ABCDEFGH',[5,34,18,14,14,14,14,28]): pn.column_dimensions[col].width=w
    pn.sheet_view.showGridLines=False
    pn.sheet_properties.pageSetUpPr.fitToPage=True; pn.page_setup.fitToWidth=1; pn.page_setup.fitToHeight=0
    for rr in range(1,5):
        for cc in range(1,9): pn.cell(rr,cc).fill=fill(NOT)
    pn['B2']='RAIO-X DA MARCA RECONHECÍVEL'; pn['B2'].font=Font(name='Inter',bold=True,size=9,color=FUC)
    pn['B3']=p.get('brand',p['domain']); pn['B3'].font=TIT
    pn['B4']=f"{p['domain']} · Coleta: {data} · Metodologia {av['methodology_version']} · Perfil: {motor.PERFIL_PT.get(p.get('site_profile'),'')}"; pn['B4'].font=Font(name='Inter',size=9,color='C9CFEA')
    pn.row_dimensions[3].height=32
    if os.path.exists('logo.png'):
        lg=XLImage('logo.png'); lg.width=44; lg.height=44; pn.add_image(lg,'H1')
    pn['B5']=('Auditoria: '+p['auditor']+' · ' if p.get('auditor') else '')+'Método e ferramenta: Amanda Noronha® · amandanoronha.com.br'
    pn['B5'].font=Font(name='Inter',size=8,color='5B6380')
    for ws in (pn,vf,ia,me):
        ws.oddFooter.left.text=f"Raio-x da marca reconhecível · {p['domain']} · coleta {data}"; ws.oddFooter.left.size=7
        ws.oddFooter.right.text="&P / &N"; ws.oddFooter.right.size=7
    # KPIs (linha 6-7)
    kp=[('B','Pontuação total, de 0 a 35','=ROUND(SUM(E12:E18),1)'),('C','Faixa','=IF(D7<0.6,"provisória",IF(B7<=14,"Ausente",IF(B7<26,"Citada","Reconhecida")))'),
        ('D','Cobertura',f'=IFERROR(SUMPRODUCT(({Pp}="nota")*(({R}="cumpre")+({R}="sintoma detectado")))/(COUNTIF({Pp},"nota")-COUNTIFS({Pp},"nota",{R},"não se aplica")),0)'),
        ('F','Confiabilidade',f'=IFERROR(SUMPRODUCT(({Pp}="nota")*(({R}="cumpre")+({R}="sintoma detectado"))*(({O}="observada")+({O}="validada")))/SUMPRODUCT(({Pp}="nota")*(({R}="cumpre")+({R}="sintoma detectado"))),0)')]
    for col,lab,f in kp:
        pn[f'{col}6']=lab; pn[f'{col}6'].font=ROT
        pn[f'{col}7']=f; pn[f'{col}7'].font=Font(name='Chivo',bold=True,size=22,color=BIC); pn[f'{col}7'].alignment=Alignment(horizontal='left')
    pn['B7'].font=Font(name='Chivo',bold=True,size=26,color=NOT); pn['C7'].font=Font(name='Chivo',bold=True,size=15,color=FUC); pn['B7'].number_format='0.0'
    pn['D7'].number_format='0%'; pn['F7'].number_format='0%'
    pn.row_dimensions[7].height=36
    pn['B8']='Abaixo de 60% de cobertura, a nota é provisória e a faixa não aparece.'; pn['B8'].font=Font(name='Inter',size=8,italic=True,color='6B7392')
    # tabela de sintomas
    hdr=['','Sintoma','Camada','Pontos P','Pontos F','Nota (0 a 5)','Respondidos','Situação']
    for j,v in enumerate(hdr,1):
        c=pn.cell(11,j,v); c.font=H; c.fill=fill(NOT)
    req=f'COUNTIFS({Pp},"requisito",{R},"cumpre")=COUNTIF({Pp},"requisito")'
    for k,(g,info) in enumerate(S.items()):
        rr=12+k
        pn.cell(rr,1,g).font=Font(name='Chivo',bold=True,color=FUC)
        pn.cell(rr,2,info['nome']).font=BB; pn.cell(rr,3,info['camada']).font=B
        pn.cell(rr,4,f'=SUMIFS({W},{G},"{g}",{Pp},"nota",{R},"cumpre")').font=B
        pn.cell(rr,5,'').font=B
        pn.cell(rr,6,'').font=B
        pc=f'D{rr}'; fc=f'SUMIFS({W},{G},"{g}",{Pp},"nota",{R},"sintoma detectado")'
        cond=f'AND({pc}+{fc}>0{","+req if g=="03" else ""})'
        pn.cell(rr,5).value=f'={fc}'
        pn.cell(rr,6).value=f'=IF({cond},ROUND(5*{pc}/({pc}+E{rr}),2),"")'
        pn.cell(rr,6).font=Font(name='Chivo',bold=True,size=12,color=NOT); pn.cell(rr,6).number_format='0.0'
        pn.cell(rr,7,f'=COUNTIFS({G},"{g}",{Pp},"nota",{R},"cumpre")+COUNTIFS({G},"{g}",{Pp},"nota",{R},"sintoma detectado")&" de "&(COUNTIFS({G},"{g}",{Pp},"nota")-COUNTIFS({G},"{g}",{Pp},"nota",{R},"não se aplica"))').font=B
        sit='"rodada de IA incompleta"' if g=='03' else '"sem item respondido"'
        pn.cell(rr,8,f'=IF(F{rr}="",{sit},IF(F{rr}<2.5,"atenção",""))').font=Font(name='Inter',size=9,color=FUC)
        for cc in range(1,9): pn.cell(rr,cc).border=fina
    # coluna E da soma: usa F (nota) para total
    pn['B7'].value='=ROUND(SUM(F12:F18),1)'
    pn.conditional_formatting.add('F12:F18',DataBarRule(start_type='num',start_value=0,end_type='num',end_value=5,color=BIC))
    pn.conditional_formatting.add('F12:F18',CellIsRule(operator='lessThan',formula=['2.5'],font=Font(name='Chivo',bold=True,size=12,color=FUC)))
    # camadas
    pn['B21']='Nota por camada'; pn['B21'].font=Font(name='Chivo',bold=True,size=13,color=NOT)
    nomes={1:'Rastreabilidade',2:'Estrutura',3:'Interpretação',4:'Memória',5:'Ação'}
    for j,v in enumerate(['','Camada','','','','Nota (0 a 5)','',''],1):
        c=pn.cell(22,j,v); c.font=H; c.fill=fill(NOT)
    for k,Lr in enumerate(range(5,0,-1)):
        rr=23+k
        pn.cell(rr,1,Lr).font=Font(name='Chivo',bold=True,color=FUC); pn.cell(rr,2,nomes[Lr]).font=BB
        Pc=f'SUMIFS({W},{L},{Lr},{Pp},"nota",{R},"cumpre")'; Fc=f'SUMIFS({W},{L},{Lr},{Pp},"nota",{R},"sintoma detectado")'
        if Lr==4:  # exclui o 03 quando ele está sem nota
            Pc=f'({Pc}-IF({D}F{D}14="",SUMIFS({W},{G},"03",{Pp},"nota",{R},"cumpre"),0))'; Fc=f'({Fc}-IF({D}F{D}14="",SUMIFS({W},{G},"03",{Pp},"nota",{R},"sintoma detectado"),0))'
        pn.cell(rr,6,f'=IF({Pc}+{Fc}>0,ROUND(5*{Pc}/({Pc}+{Fc}),2),"")').font=Font(name='Chivo',bold=True,size=12,color=NOT); pn.cell(rr,6).number_format='0.0'
        pn.cell(rr,8,f'=IF(SUMPRODUCT((F{rr+1}:F{D}27<>"")*(F{rr+1}:F{D}27<2.5))>0,"depende de camada abaixo","")' if Lr>1 else '').font=Font(name='Inter',size=9,color=FUC)
        for cc in range(1,9): pn.cell(rr,cc).border=fina
    pn.conditional_formatting.add('F23:F27',DataBarRule(start_type='num',start_value=0,end_type='num',end_value=5,color=FUC))
    # prioridades
    pn['B30']='Prioridades'; pn['B30'].font=Font(name='Chivo',bold=True,size=13,color=NOT)
    pn['B31']='Gerada na coleta: camada abaixo de 2,5 primeiro; depois o sintoma de menor nota, pelo nível 1.'; pn['B31'].font=Font(name='Inter',size=8,italic=True,color='6B7392')
    for j,v in enumerate(['#','O que checar','Item','Sintoma','Nível','','',''],1):
        c=pn.cell(32,j,v); c.font=H; c.fill=fill(NOT)
    for k,c in enumerate(r['prioridades'][:15],1):
        rr=32+k
        for j,v in enumerate([k,c['checar'],c['id'],c['grupo'],c['nivel']],1):
            x=pn.cell(rr,j,v); x.font=B; x.border=fina; x.alignment=WRAP
    # ---------- Rodada de IA ----------
    hdr=[('Data',12),('Plataforma',16),('Pergunta',48),('Tipo',14),('Rodada',8),('Marcas citadas, em ordem',42),('Posição da marca',12),('Domínios citados como fonte',40),('Observações',40)]
    for j,(v,w) in enumerate(hdr,1):
        c=ia.cell(1,j,v); c.font=H; c.fill=fill(NOT); ia.column_dimensions[get_column_letter(j)].width=w
    ia.freeze_panes='A2'
    for k,pr in enumerate(av.get('ai_round',{}).get('prompts',[]),2):
        for j,key in enumerate(['date','platform','prompt','type','run','brands','position','sources','note'],1):
            v=pr.get(key,''); ia.cell(k,j,', '.join(v) if isinstance(v,list) else v).font=B
    ia.cell(1,11,'Regras: rodar sem login, registrar a data, três rodadas por pergunta na primeira coleta, mesmas perguntas e plataformas toda semana. Posição = ordem da primeira menção; marcas da mesma entidade contam uma vez.').font=Font(name='Inter',size=9,italic=True,color='6B7392')
    # ---------- Método ----------
    me.column_dimensions['A'].width=120; me.sheet_view.showGridLines=False
    linhas=[('Método',True),
      ('Nota do sintoma: 5 × P ÷ (P + F). P e F somam os pesos dos itens que cumprem e dos que têm sintoma. Não se aplica e não avaliado saem da conta.',False),
      ('Total: soma das sete notas, de 0 a 35. Sintoma sem nota entra com 0.',False),
      ('Faixas: Ausente até 14; Citada acima de 14 e abaixo de 26; Reconhecida de 26 a 35.',False),
      ('Cobertura: itens respondidos sobre o total de itens que se aplicam. Abaixo de 60%, nota provisória e sem faixa.',False),
      ('Confiabilidade: parte das respostas com evidência observada ou validada.',False),
      ('Sintoma 03: só recebe nota com todos os requisitos da rodada de IA cumpridos. Requisitos não entram na nota.',False),
      ('Camadas: camada com nota abaixo de 2,5 compromete a leitura das de cima.',False),
      ('Como usar: troque o Resultado na aba Verificações; Painel recalcula sozinho. A coluna Observações guarda a evidência.',False),
      (f"Metodologia {av['methodology_version']} · documento 01 · Knowledge Core & Governance · Amanda Noronha®",False)]
    for i,(t,b) in enumerate(linhas,1):
        c=me.cell(i,1,t); c.font=Font(name='Chivo',bold=True,size=14,color=NOT) if b else B; c.alignment=WRAP
    wb.save(saida); return r
if __name__=='__main__':
    cat=json.load(open(sys.argv[1])); av=json.load(open(sys.argv[2])); gerar(av,cat,sys.argv[3])
```

**render.js**

```js
const {chromium}=require('playwright'); const fs=require('fs');
const [,, html, pdf, dominio, data]=process.argv;
(async()=>{const b=await chromium.launch();const p=await b.newPage();
await p.goto('file://'+html,{waitUntil:'load'});await p.waitForTimeout(400);
await p.pdf({path:pdf,format:'A4',printBackground:true,preferCSSPageSize:true,displayHeaderFooter:true,headerTemplate:'<span></span>',
footerTemplate:`<div style="font-family:Inter,Arial,sans-serif;font-size:6.8pt;color:#6B7392;width:100%;padding:0 15mm;display:flex;justify-content:space-between"><span>Raio-x da marca reconhecível · ${dominio} · ${data}</span><span class="pageNumber"></span></div>`});
await b.close()})();
```

## 12. Regras de escrita

Todo texto do relatório segue o Brandbook Master de Amanda Noronha, em Tom 1:

- Sem travessão em qualquer posição; usar ponto, vírgula, dois-pontos ou reescrever.
- Sem construção contrastiva do tipo "não é X, é Y" e variações.
- Sem pergunta retórica respondida em seguida, sem frase de efeito isolada, sem linguagem motivacional, de venda ou buzzword sem explicação, sem superlativo sem dado.
- Sem emoji e sem exclamação. Caixa alta só em rótulo e título.
- Rótulo antes de dois-pontos em negrito.
- Dado de terceiro sempre com fonte; a leitura da auditoria fica em bloco próprio, separada do dado.
- Fonte incerta: a afirmação sai.