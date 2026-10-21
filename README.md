 # prim-campus-unifor

Algoritmo de Prim aplicado ao campus da UNIFOR: os prédios viram vértices de um grafo, e a
árvore geradora mínima (MST) indica como ligar todos eles com o menor comprimento total de
cabo.

Seminário AV2 — Resolução de Problemas com Grafos (UNIFOR), Prof. Matheus Linhares.

**Equipe:** João Victor Rodrigues (2422976) · Eduardo Fontenele (2214659) · Breno Sampaio (2422974)

## Resultado

- **27 vértices** marcados sobre o mapa oficial do campus (`data/map.png`).
- **Grafo completo** K27, com 351 arestas; o peso é a distância euclidiana entre os pontos, em
  pixels do mapa.
- **MST** com 26 arestas a partir do vértice `A`: custo total de **2 095,7 px**.

## Estrutura

```
prim-campus-unifor/
├── pyproject.toml           # dependências (gerenciadas com uv)
├── uv.lock
├── .python-version          # Python 3.14
└── src/prim_campus_unifor/
    ├── map-nodes.ipynb      # marca os vértices clicando no mapa e salva vertices.json
    ├── prim-case.ipynb      # monta o grafo, executa o Prim e desenha a MST
    └── data/
        ├── map.png          # mapa oficial do campus
        └── vertices.json    # 27 vértices: rótulo -> [x, y] em pixels do mapa
```

## Requisitos

- Python 3.14 (definido em `.python-version`)
- [uv](https://docs.astral.sh/uv/) para instalar as dependências
- Dependências (em `pyproject.toml`): `networkx`, `numpy`, `pandas`, `matplotlib`, `ipympl`;
  `jupyter` e `ipykernel` no grupo `dev`

## Como executar

```bash
uv sync                 # cria o ambiente e instala as dependências (inclui o grupo dev)
uv run jupyter lab      # abre o Jupyter
```

No Jupyter, abra `src/prim_campus_unifor/prim-case.ipynb` e execute todas as células
(**Run → Run All Cells**). A saída esperada é:

```
Custo total da MST: 2095.7
```

seguida do mapa com a MST desenhada em vermelho.

Sem uv, dá para usar um ambiente virtual comum:

```bash
python -m venv .venv
source .venv/bin/activate            # Windows: .venv\Scripts\activate
pip install networkx numpy pandas matplotlib ipympl jupyter
jupyter lab
```

## Marcar novos vértices (opcional)

`map-nodes.ipynb` abre o mapa em modo interativo (`%matplotlib widget`):

- **clique esquerdo** adiciona um vértice (o nome vem da caixa de texto, ou `v0`, `v1`, ...);
- **clique direito** remove o vértice mais próximo;
- **Salvar** grava `vertices.json` na pasta do notebook.

Para usar os novos pontos, mova o arquivo para `data/vertices.json`.

## Como funciona

1. **Distâncias:** `prim-case.ipynb` lê `data/vertices.json` e calcula a matriz de distâncias
   euclidianas entre todos os pares com NumPy, arredondada para 0,1 px.
2. **Grafo:** `nx.from_pandas_adjacency` transforma a matriz em um grafo completo; a diagonal
   (distância 0) não vira laço.
3. **Prim:** `MSTPrim(graph, s)` segue o MST-PRIM do CLRS com uma `queue.PriorityQueue` (heap
   binário). Como ela não tem DECREASE-KEY, cada redução de chave insere uma nova entrada, e
   entradas de vértices já visitados são descartadas ao sair da fila. Complexidade: O(E log V).
4. **Desenho:** a MST é desenhada em vermelho sobre o mapa com `networkx` e `matplotlib`.
