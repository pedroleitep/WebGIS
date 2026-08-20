# Entregável 2 — Especificação textual dos casos de uso

**Grupo:** Pedro Leite Pereira, Bruno Máximo Machado
**Sistema:** GeoSaúde BH — WebGIS para localização de serviços de saúde próximos a hospitais de Belo Horizonte
**Site hospedado:** https://web-gis-two-eta.vercel.app/

Uma tabela por caso de uso do [diagrama](01-diagrama-casos-de-uso.md), no formato exigido pelo
enunciado: **Nome · Descrição · Fluxo principal · Fluxos alternativos · Pré-condições · Pós-condições**.

**Índice**

| | | | |
|---|---|---|---|
| [UC01](#uc01) Localizar serviços de saúde próximos | [UC05](#uc05) Definir raio de busca | [UC09](#uc09) Visualizar resultados no mapa | [UC13](#uc13) Exportar resultados |
| [UC02](#uc02) Localizar farmácias próximas | [UC06](#uc06) Filtrar por categoria e atributos | [UC10](#uc10) Carregar base cartográfica | [UC14](#uc14) Alternar camadas do mapa |
| [UC03](#uc03) Localizar laboratórios de radiografia | [UC07](#uc07) Executar consulta espacial | [UC11](#uc11) Ordenar alfabeticamente | [UC15](#uc15) Traçar rota |
| [UC04](#uc04) Selecionar hospital de referência | [UC08](#uc08) Listar resultados por proximidade | [UC12](#uc12) Ver detalhes do estabelecimento | [UC16](#uc16) Manter cadastro |

---

<a id="uc01"></a>
## UC01

| Campo | Conteúdo |
|---|---|
| **Nome** | Localizar serviços de saúde próximos *(caso de uso base, abstrato)* |
| **Ator primário** | Usuário do Sistema |
| **Descrição** | Permite ao Usuário do Sistema encontrar estabelecimentos de saúde situados dentro de um raio a partir de um hospital de referência, com o resultado apresentado em lista ordenada e no mapa. |
| **Fluxo principal** | 1. O usuário abre o sistema e o mapa da região central é carregado (`«include» UC10`). 2. O usuário seleciona o hospital de referência (`«include» UC04`). 3. O usuário define o raio de busca (`«include» UC05`). 4. O usuário escolhe a categoria e os filtros de atributo (`«include» UC06`). 5. O usuário aciona *Executar consulta*. 6. O sistema executa a consulta espacial sobre a área de busca (`«include» UC07`). 7. O sistema calcula a distância de cada estabelecimento retornado até o hospital e ordena de forma crescente (`«include» UC08`). 8. O sistema desenha o polígono do raio, os marcadores numerados e ajusta o enquadramento (`«include» UC09`). |
| **Fluxos alternativos** | **A1.** Nenhum estabelecimento no raio: o sistema informa a ausência de resultados e sugere ampliar o raio ou relaxar os filtros; retorna ao passo 3. **A2.** O usuário altera raio, categoria ou filtros: o sistema refaz a consulta a partir do passo 5. **A3.** O usuário troca o hospital de referência: o sistema descarta o resultado atual e reinicia do passo 2. **E1.** Base cartográfica indisponível: o sistema exibe apenas as geometrias locais e registra aviso na barra de estado. |
| **Pré-condições** | Base de hospitais e estabelecimentos carregada; navegador com suporte a SVG. |
| **Pós-condições** | Conjunto de resultados calculado, ordenado por distância e sincronizado entre lista e mapa. |

---

<a id="uc02"></a>
## UC02

| Campo | Conteúdo |
|---|---|
| **Nome** | Localizar farmácias próximas |
| **Ator primário** | Familiar do Paciente |
| **Descrição** | Especialização de UC01. Permite ao Familiar do Paciente localizar farmácias em torno do hospital selecionado, para encontrar rapidamente a opção mais conveniente *(Estória 1)*. |
| **Fluxo principal** | 1. O familiar seleciona o hospital em que o paciente está internado. 2. Define o raio de busca (por exemplo, 500 m). 3. Escolhe a categoria **Farmácia**; o sistema desmarca as demais categorias. 4. Opcionalmente aplica filtros de atributo: *24 horas*, *entrega*, *manipulação*, *aplicação de injetáveis*. 5. O sistema seleciona as farmácias contidas na área de busca e calcula a distância de cada uma até o hospital. 6. O sistema exibe a lista ordenada da mais próxima para a mais distante e os marcadores numerados no mapa. |
| **Fluxos alternativos** | **A1.** Nenhuma farmácia encontrada no raio: o sistema informa e oferece ampliar o raio. **A2.** O familiar altera o raio e refaz a busca. **A3.** O filtro *24 horas* zera o conjunto: o sistema mantém o resultado sem o filtro e sinaliza quantos itens foram removidos. **A4.** O familiar pede ordenação alfabética (`«extend» UC11`). |
| **Pré-condições** | Hospital disponível e selecionado no sistema; existir ao menos uma farmácia cadastrada. |
| **Pós-condições** | Lista de farmácias exibida em ordem de proximidade, com marcadores correspondentes no mapa. |

---

<a id="uc03"></a>
## UC03

| Campo | Conteúdo |
|---|---|
| **Nome** | Localizar laboratórios de radiografia próximos |
| **Ator primário** | Médico |
| **Descrição** | Especialização de UC01. Permite ao Médico localizar laboratórios que realizam radiografia próximos ao hospital, para encaminhar o paciente ao serviço mais adequado e próximo *(Estória 2)*. |
| **Fluxo principal** | 1. O médico seleciona o hospital de referência. 2. Define o raio de busca (por exemplo, 1 km). 3. Escolhe a categoria **Laboratório**. 4. Ativa o filtro de exame **Radiografia**; o sistema descarta laboratórios que não oferecem o exame. 5. Opcionalmente restringe por *atende SUS* ou *plantão 24 h*. 6. O sistema retorna os laboratórios contidos na área de busca, ordenados por distância. 7. O médico abre o detalhe de um laboratório e consulta exames, telefone e horário (`«extend» UC12`). |
| **Fluxos alternativos** | **A1.** Nenhum laboratório com radiografia no raio: o sistema informa e sugere ampliar para o próximo raio pré-definido. **A2.** O médico remove o filtro de radiografia para ver todos os laboratórios de análises. **A3.** O médico solicita a rota até o laboratório escolhido (`«extend» UC15`). |
| **Pré-condições** | Hospital selecionado; base de laboratórios com o atributo de exames preenchido. |
| **Pós-condições** | Lista de laboratórios de radiografia exibida em ordem de proximidade e plotada no mapa. |

---

<a id="uc04"></a>
## UC04

| Campo | Conteúdo |
|---|---|
| **Nome** | Selecionar hospital de referência |
| **Ator primário** | Usuário do Sistema |
| **Descrição** | Define o ponto (geometria `POINT`) que serve de origem para o raio de busca e para o cálculo de todas as distâncias. |
| **Fluxo principal** | 1. O sistema apresenta a lista de hospitais cadastrados, com bairro e endereço. 2. O usuário escolhe um hospital na lista ou clica no marcador correspondente no mapa. 3. O sistema centraliza o mapa no hospital, destaca o marcador e exibe suas coordenadas. 4. O sistema identifica o bairro que contém o hospital e o registra no painel de consulta espacial. |
| **Fluxos alternativos** | **A1.** O usuário clica em um marcador de hospital diretamente no mapa: equivale ao passo 2. **A2.** Já existe uma consulta ativa: o sistema recalcula os resultados para o novo hospital. **E1.** Hospital sem coordenada válida: o sistema mantém a seleção anterior e sinaliza o erro. |
| **Pré-condições** | Existir ao menos um hospital cadastrado com latitude e longitude. |
| **Pós-condições** | Hospital de referência definido; mapa centralizado; bairro contenedor identificado. |

---

<a id="uc05"></a>
## UC05

| Campo | Conteúdo |
|---|---|
| **Nome** | Definir raio de busca |
| **Ator primário** | Usuário do Sistema |
| **Descrição** | Estabelece a distância máxima, em metros, que delimita a área de busca. O sistema materializa esse raio como um polígono circular (*buffer*) em torno do hospital. |
| **Fluxo principal** | 1. O sistema exibe os raios pré-definidos de 200 m, 500 m, 1 km e 2 km, além de um controle contínuo. 2. O usuário escolhe um valor pré-definido ou arrasta o controle entre 100 m e 3.000 m. 3. O sistema atualiza o rótulo numérico do raio. 4. O sistema redesenha o polígono de busca, a marca de escala e o rótulo de raio no mapa. |
| **Fluxos alternativos** | **A1.** Valor fora do intervalo permitido: o sistema limita ao mínimo ou máximo e avisa o usuário. **A2.** Existe consulta ativa: o resultado é recalculado a cada mudança do raio. |
| **Pré-condições** | Hospital de referência selecionado (UC04). |
| **Pós-condições** | Raio definido e polígono de busca desenhado sobre o mapa. |

---

<a id="uc06"></a>
## UC06

| Campo | Conteúdo |
|---|---|
| **Nome** | Filtrar por categoria e atributos |
| **Ator primário** | Usuário do Sistema |
| **Descrição** | Restringe o conjunto de estabelecimentos por tipo (hospital, farmácia, laboratório, UBS) e por atributos não espaciais, antes ou depois da consulta espacial. |
| **Fluxo principal** | 1. O sistema apresenta as categorias com sua cor de simbologia e a contagem de registros de cada uma. 2. O usuário marca ou desmarca as categorias desejadas. 3. O sistema apresenta os filtros de atributo pertinentes às categorias marcadas. 4. O usuário ativa os filtros desejados (por exemplo, *24 horas* ou *radiografia*). 5. O sistema aplica os filtros e atualiza lista, mapa e contadores. |
| **Fluxos alternativos** | **A1.** Nenhuma categoria marcada: o sistema esvazia a lista e informa que ao menos uma categoria deve ser selecionada. **A2.** A combinação de filtros não retorna registros: o sistema informa quais filtros eliminaram todos os itens. **A3.** O usuário limpa os filtros de atributo e mantém apenas a categoria. |
| **Pré-condições** | Base de estabelecimentos carregada com os atributos correspondentes. |
| **Pós-condições** | Conjunto de candidatos restrito às categorias e atributos escolhidos. |

---

<a id="uc07"></a>
## UC07

| Campo | Conteúdo |
|---|---|
| **Nome** | Executar consulta espacial |
| **Ator primário** | Usuário do Sistema |
| **Descrição** | Aplica os operadores topológicos entre a área de busca e as camadas de pontos, linhas e polígonos: `ST_Contains`, `ST_Within` e `ST_Intersects`. |
| **Fluxo principal** | 1. O sistema monta o polígono de busca a partir do hospital e do raio. 2. **Está contido:** seleciona os pontos de estabelecimento cuja geometria está contida no polígono de busca. 3. **Contém:** identifica o polígono de bairro que contém o hospital e verifica quais bairros contêm integralmente a área de busca. 4. **Intercepta:** seleciona as vias (linhas) e os bairros (polígonos) que interceptam a área de busca. 5. O sistema calcula a distância de cada estabelecimento selecionado ao hospital pela fórmula de Haversine. 6. O sistema exibe a consulta em notação pseudo-SQL e o resumo de cada predicado no painel de consulta espacial. |
| **Fluxos alternativos** | **A1.** O predicado *está contido* retorna conjunto vazio: os demais predicados continuam sendo apresentados. **A2.** A área de busca ultrapassa a extensão da base carregada: o sistema avisa que o resultado pode estar incompleto. **E1.** Geometria inválida na base: o registro é ignorado e contabilizado como inconsistência. |
| **Pré-condições** | Hospital (UC04), raio (UC05) e filtros (UC06) definidos. |
| **Pós-condições** | Conjunto-resposta calculado com distâncias e resultado dos três predicados topológicos disponível. |

---

<a id="uc08"></a>
## UC08

| Campo | Conteúdo |
|---|---|
| **Nome** | Listar resultados por proximidade |
| **Ator primário** | Usuário do Sistema |
| **Descrição** | Apresenta os estabelecimentos retornados em lista ordenada pela menor distância até o hospital, com posição, distância e tempo estimado de caminhada. |
| **Fluxo principal** | 1. O sistema ordena o conjunto-resposta pela distância crescente. 2. O sistema atribui a cada item uma posição sequencial, que é a mesma exibida no marcador do mapa. 3. Para cada item exibe nome, endereço, distância em metros, tempo de caminhada estimado e os atributos em destaque. 4. O sistema informa o total de estabelecimentos encontrados. |
| **Fluxos alternativos** | **A1.** Conjunto vazio: o sistema exibe estado vazio com orientação para ampliar o raio ou revisar os filtros. **A2.** O usuário troca a ordenação para alfabética (`«extend» UC11`); as posições numéricas continuam refletindo a proximidade. **A3.** O usuário exporta a lista (`«extend» UC13`). **A4.** Empate de distância: o sistema desempata por ordem alfabética do nome. |
| **Pré-condições** | Consulta espacial executada (UC07). |
| **Pós-condições** | Lista ordenada exibida e sincronizada com os marcadores do mapa. |

---

<a id="uc09"></a>
## UC09

| Campo | Conteúdo |
|---|---|
| **Nome** | Visualizar resultados no mapa |
| **Ator primário** | Usuário do Sistema |
| **Descrição** | Desenha no mapa o hospital, o polígono do raio e os estabelecimentos encontrados, mantendo a simbologia por categoria e a sincronia com a lista. |
| **Fluxo principal** | 1. O sistema desenha as camadas de fundo: bairros (polígonos), áreas verdes (polígonos) e malha viária (linhas). 2. Desenha o polígono do raio com a marca e o rótulo da distância. 3. Plota o hospital com símbolo próprio e os estabelecimentos com a cor da sua categoria e a posição da lista. 4. Ajusta o enquadramento para que a área de busca fique inteiramente visível. 5. Ao passar o cursor sobre um item da lista, destaca o marcador correspondente, e vice-versa. |
| **Fluxos alternativos** | **A1.** O usuário navega no mapa com arrastar, roda do mouse ou botões de zoom. **A2.** O usuário altera as camadas visíveis (`«extend» UC14`). **A3.** O usuário clica em um marcador e abre o detalhe (`«extend» UC12`). **A4.** Muitos marcadores sobrepostos: os rótulos são omitidos em escalas menores, preservando os símbolos. |
| **Pré-condições** | Base cartográfica carregada (UC10) e consulta executada (UC07). |
| **Pós-condições** | Mapa renderizado com a área de busca e os resultados; lista e mapa sincronizados. |

---

<a id="uc10"></a>
## UC10

| Campo | Conteúdo |
|---|---|
| **Nome** | Carregar base cartográfica |
| **Ator primário** | Provedor de Geodados *(ator secundário; disparado pelo sistema)* |
| **Descrição** | Obtém junto ao Provedor de Geodados as camadas de referência — limites de bairro, malha viária e áreas verdes — e as prepara para renderização. |
| **Fluxo principal** | 1. O sistema solicita as camadas de referência da área central de Belo Horizonte. 2. O Provedor de Geodados devolve as geometrias em coordenadas geográficas (EPSG:4326). 3. O sistema projeta as geometrias para Web Mercator e monta o índice de desenho. 4. O sistema informa na barra de estado a quantidade de feições carregadas por camada. |
| **Fluxos alternativos** | **E1.** Provedor indisponível: o sistema usa a base local embarcada e sinaliza o modo degradado. **E2.** Geometria fora da área de interesse: é descartada no recorte. |
| **Pré-condições** | Aplicação iniciada no navegador. |
| **Pós-condições** | Camadas de referência disponíveis para desenho e para os operadores topológicos. |

---

<a id="uc11"></a>
## UC11

| Campo | Conteúdo |
|---|---|
| **Nome** | Ordenar resultados alfabeticamente |
| **Ator primário** | Usuário do Sistema |
| **Descrição** | Extensão de UC08. Reordena a lista pelo nome do estabelecimento, em ordem crescente ou decrescente, sem alterar o conjunto retornado pela consulta. |
| **Fluxo principal** | 1. No ponto de extensão *ordenação*, o usuário escolhe **A → Z** ou **Z → A**. 2. O sistema reordena a lista pelo nome, ignorando maiúsculas e acentuação. 3. O sistema mantém em cada item a posição de proximidade e a distância, para que a referência espacial não se perca. |
| **Fluxos alternativos** | **A1.** O usuário retorna à ordenação por proximidade. **A2.** Nomes idênticos: desempate pela distância crescente. |
| **Pré-condições** | Lista de resultados exibida (UC08) com ao menos dois itens. |
| **Pós-condições** | Lista reordenada alfabeticamente, com o mesmo conjunto de estabelecimentos. |

---

<a id="uc12"></a>
## UC12

| Campo | Conteúdo |
|---|---|
| **Nome** | Ver detalhes do estabelecimento |
| **Ator primário** | Usuário do Sistema |
| **Descrição** | Extensão de UC09. Exibe a ficha completa de um estabelecimento selecionado na lista ou no mapa. |
| **Fluxo principal** | 1. O usuário clica no marcador do mapa ou no item da lista. 2. O sistema abre o cartão de detalhe junto ao marcador e destaca o item correspondente na lista. 3. O cartão apresenta nome, categoria, endereço, bairro, telefone, horário, atributos, coordenadas e distância até o hospital. 4. O usuário fecha o cartão ou seleciona outro estabelecimento. |
| **Fluxos alternativos** | **A1.** O usuário solicita a rota até o estabelecimento (`«extend» UC15`). **A2.** Atributo ausente no cadastro: o campo é exibido como *não informado*. **A3.** O usuário pressiona `Esc`: o cartão é fechado e a seleção, mantida. |
| **Pré-condições** | Estabelecimento presente no conjunto-resposta e visível no mapa. |
| **Pós-condições** | Detalhes exibidos e estabelecimento marcado como selecionado em lista e mapa. |

---

<a id="uc13"></a>
## UC13

| Campo | Conteúdo |
|---|---|
| **Nome** | Exportar resultados |
| **Ator primário** | Usuário do Sistema |
| **Descrição** | Extensão de UC08. Permite levar o resultado da consulta para fora do sistema em CSV, área de transferência ou impressão. |
| **Fluxo principal** | 1. O usuário escolhe o formato: **CSV**, **copiar lista** ou **imprimir**. 2. O sistema monta o conteúdo com posição, nome, categoria, endereço, distância e coordenadas. 3. O sistema entrega o arquivo, copia o texto ou abre a caixa de impressão. 4. O sistema confirma a ação na barra de estado. |
| **Fluxos alternativos** | **A1.** Lista vazia: a exportação é bloqueada e o sistema avisa que não há resultados. **E1.** Navegador nega acesso à área de transferência: o sistema exibe o texto para cópia manual. |
| **Pré-condições** | Consulta executada com ao menos um resultado. |
| **Pós-condições** | Resultado entregue no formato escolhido; conjunto-resposta inalterado. |

---

<a id="uc14"></a>
## UC14

| Campo | Conteúdo |
|---|---|
| **Nome** | Alternar camadas do mapa |
| **Ator primário** | Usuário do Sistema |
| **Descrição** | Extensão de UC09. Controla a visibilidade das camadas temáticas — bairros (polígonos), vias principais e malha viária (linhas), áreas verdes (polígonos) e rótulos. |
| **Fluxo principal** | 1. O sistema lista as camadas disponíveis com o respectivo tipo de geometria e o estado atual. 2. O usuário liga ou desliga uma camada. 3. O sistema redesenha o mapa preservando o enquadramento, a seleção e o resultado da consulta. |
| **Fluxos alternativos** | **A1.** Todas as camadas de fundo desligadas: permanecem visíveis o raio, o hospital e os resultados. **A2.** Rótulos desligados: os símbolos continuam sendo desenhados. |
| **Pré-condições** | Mapa renderizado (UC09). |
| **Pós-condições** | Composição do mapa atualizada conforme a escolha do usuário. |

---

<a id="uc15"></a>
## UC15

| Campo | Conteúdo |
|---|---|
| **Nome** | Traçar rota até o estabelecimento |
| **Ator primário** | Usuário do Sistema |
| **Descrição** | Extensão de UC12. Desenha a linha de ligação entre o hospital e o estabelecimento escolhido e informa a distância e o tempo estimado de caminhada. |
| **Fluxo principal** | 1. No cartão de detalhe, o usuário aciona *Traçar rota*. 2. O sistema desenha a linha (geometria `LINESTRING`) do hospital ao estabelecimento. 3. O sistema exibe a distância em linha reta e o tempo estimado de caminhada a 5 km/h. 4. O sistema ajusta o enquadramento para conter os dois pontos. |
| **Fluxos alternativos** | **A1.** O usuário traça a rota para outro estabelecimento: a linha anterior é substituída. **A2.** O usuário limpa a rota, retornando à visualização anterior. **N1.** A rota é uma aproximação em linha reta; o protótipo não consulta serviço de roteamento por vias. |
| **Pré-condições** | Estabelecimento selecionado com detalhes abertos (UC12). |
| **Pós-condições** | Linha de rota desenhada e medida apresentada ao usuário. |

---

<a id="uc16"></a>
## UC16

| Campo | Conteúdo |
|---|---|
| **Nome** | Manter cadastro de estabelecimentos |
| **Ator primário** | Administrador de Dados |
| **Descrição** | Permite ao Administrador de Dados incluir, alterar e excluir estabelecimentos, garantindo que cada registro tenha categoria, atributos e coordenadas válidas. |
| **Fluxo principal** | 1. O administrador se autentica e abre a manutenção do cadastro. 2. Escolhe incluir, alterar ou excluir um registro. 3. Informa nome, categoria, endereço, bairro, telefone, horário, atributos e coordenadas. 4. O sistema valida os campos obrigatórios e verifica se a coordenada está dentro da área de cobertura. 5. O sistema grava o registro e atualiza os contadores por categoria. |
| **Fluxos alternativos** | **A1.** Coordenada fora da área de cobertura: o sistema recusa a gravação e destaca o campo. **A2.** Exclusão de registro em uso na consulta corrente: o sistema pede confirmação. **E1.** Falha na gravação: a operação é desfeita e o administrador é notificado. |
| **Pré-condições** | Administrador autenticado com perfil de manutenção. |
| **Pós-condições** | Base de estabelecimentos atualizada e disponível para as próximas consultas. |

---

## Cobertura

As 16 tabelas correspondem, uma a uma, aos 16 casos de uso do diagrama do Entregável 1.
UC02 e UC03 materializam as Estórias 1 e 2 do enunciado; UC07 concentra os operadores
*está contido*, *contém* e *intercepta*; UC08 e UC11 cobrem a ordenação por aproximação
e a alfabética.
