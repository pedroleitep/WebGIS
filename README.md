# GeoSaúde BH — protótipo WebGIS

Sistema para localizar serviços de saúde próximos a um hospital de Belo Horizonte.
Trabalho de Engenharia de Software II — especificação e prototipação de um WebGIS.

Abra **`index.html`** em qualquer navegador. Não há build, servidor ou dependência
externa: um arquivo, funciona offline, inclusive com duplo clique.

---

## Os três entregáveis

| Entregável | Documento para entregar | Fontes |
|---|---|---|
| **1 — Diagrama de Casos de Uso** | [`docs/entregavel-1-diagrama.pdf`](docs/entregavel-1-diagrama.pdf) · 5 páginas, A4 | [`docs/01-diagrama-casos-de-uso.md`](docs/01-diagrama-casos-de-uso.md) · figura isolada em [`docs/diagrama-casos-de-uso.svg`](docs/diagrama-casos-de-uso.svg) |
| **2 — Especificação textual** | [`docs/entregavel-2-especificacao.pdf`](docs/entregavel-2-especificacao.pdf) · 12 páginas, A4 | [`docs/02-especificacao-casos-de-uso.md`](docs/02-especificacao-casos-de-uso.md) |
| **3 — Protótipo funcional** | [`index.html`](index.html) — abra no navegador | — |

Os entregáveis 1 e 2 são documentos separados, em PDF. O protótipo é só o mapa:
a documentação não fica embutida na aplicação.

O diagrama tem 5 atores e 16 casos de uso, com 7 relações `include`, 5 `extend`,
generalização de atores (Familiar do Paciente e Médico → Usuário do Sistema) e
generalização de casos de uso (UC02 e UC03 → UC01). Há uma tabela de especificação
para cada um dos 16 casos.

---

## Como o protótipo atende às estórias

**Estória 1 — familiar procurando farmácias**
Selecione *Hospital das Clínicas da UFMG*, raio **500 m**, categoria **Farmácia**.
A lista aparece à direita em ordem de proximidade, com os marcadores numerados no mapa.
Ative *24 horas* se a busca for de madrugada.

**Estória 2 — médico procurando radiografia**
Mesmo hospital, raio **1 km**, categoria **Laboratório**, filtro **Radiografia**.
Laboratórios sem o exame são descartados. Clique num resultado e use *Traçar rota*
para ver a distância até o serviço escolhido.

---

## Elementos de SIG implementados

| Elemento pedido no enunciado | Como aparece |
|---|---|
| **Filtros** | Categoria (hospital, farmácia, laboratório, UBS) e atributos: 24 h, entrega, manipulação, injetáveis, radiografia, tomografia, ultrassonografia, atende SUS |
| **Query** | Painel *Consulta espacial*, que reescreve a consulta em pseudo-SQL a cada mudança de hospital, raio ou filtro |
| **Pontos** | Hospitais e estabelecimentos (`POINT`) |
| **Linhas** | 15 vias principais nomeadas e 51 segmentos da malha viária (`LINESTRING`); a rota hospital → estabelecimento também é uma linha |
| **Polígonos** | 8 bairros, 5 áreas verdes e o *buffer* do raio de busca (`POLYGON`) |
| **Está contido** | `ST_Within` — estabelecimentos contidos no polígono do raio |
| **Contém** | `ST_Contains` — qual bairro contém o hospital; quais bairros contêm integralmente a área de busca |
| **Intercepta** | `ST_Intersects` — vias, bairros e áreas verdes que cruzam a área de busca |
| **Listagem por aproximação** | Ordenação padrão por distância crescente (Haversine), com posição espelhada no marcador do mapa |
| **Raio de busca** | Atalhos de 200 m, 500 m, 1 km e 2 km, mais controle contínuo de 100 m a 3 km |

Ordenação alternativa **A → Z** e **Z → A** fica ao lado da ordenação por proximidade;
as posições numéricas continuam refletindo a distância, para a referência espacial não se perder.

---

## Como funciona por dentro

Sem biblioteca de mapas. O arquivo implementa:

- **Projeção Web Mercator** sobre coordenadas EPSG:4326, com *pan*, *zoom* e barra de escala.
- **Referencial local em metros** girado −49°, o mesmo ângulo da malha do centro de BH.
  Isso torna os predicados topológicos cálculos planos exatos e faz o mapa parecer BH.
- **Distância geodésica** pela fórmula de Haversine.
- **Predicados topológicos**: ponto-em-polígono, distância ponto-segmento e
  distância ponto-retângulo, usados para `ST_Within`, `ST_Contains` e `ST_Intersects`.
- **Renderização SVG** reconstruída a cada quadro, com descarte do que está fora da tela.

---

## Sobre os dados

> **As coordenadas dos 6 hospitais são aproximações de unidades reais de Belo Horizonte.**
> **Todo o restante — farmácias, laboratórios, UBS, limites de bairro, malha viária e
> áreas verdes — é sintético**, gerado de forma determinística (semente fixa) apenas
> para exercitar o protótipo. Nomes, endereços, telefones e horários são fictícios e
> **não devem ser usados para decisão real de atendimento.**

Trocar por dados reais significa substituir os arrays `HOSPITAIS`, `BAIRROS`, `VIAS`
e a função `buildBase()` por uma leitura de GeoJSON — o restante do código não muda,
porque toda a lógica já opera sobre `{lat, lon}`.

---

## Estrutura

```
webgis-saude-bh/
├── index.html                            protótipo WebGIS (entregável 3)
├── README.md
└── docs/
    ├── entregavel-1-diagrama.pdf         ENTREGÁVEL 1 — documento final
    ├── entregavel-2-especificacao.pdf    ENTREGÁVEL 2 — documento final
    ├── 01-diagrama-casos-de-uso.md       fonte do entregável 1 (PlantUML e Mermaid)
    ├── 02-especificacao-casos-de-uso.md  fonte do entregável 2 (16 tabelas)
    ├── diagrama-casos-de-uso.svg         figura isolada, para colar no relatório
    └── print/                            HTML de onde os PDFs são impressos
```

### Regerar os PDFs

Os PDFs saem do HTML em `docs/print/` via Chrome headless. O layout de impressão
está no próprio arquivo: A4 retrato para o texto e uma folha em paisagem dedicada
ao diagrama, para o UML sair legível.

```bash
chrome --headless=new --no-pdf-header-footer \
  --print-to-pdf=docs/entregavel-1-diagrama.pdf \
  docs/print/entregavel-1-diagrama.html
```

---

## Uso de IA

O protótipo foi construído com apoio de IA (Claude). A modelagem — atores, recorte dos
casos de uso, escolha dos operadores topológicos e dos fluxos alternativos — foi decidida
a partir do enunciado e das estórias, e cada decisão está registrada nas justificativas
da seção 3 do Entregável 1.
