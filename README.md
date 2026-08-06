# Semantic Book Recommender

Sistema de recomendação de livros que combina busca semântica, classificação de categorias e análise de emoções. O usuário descreve o tipo de livro que procura e pode filtrar os resultados por categoria e tom emocional em uma interface construída com Gradio.

## Como funciona

O projeto processa o catálogo em quatro etapas:

1. Limpa e explora os dados dos livros.
2. Classifica cada livro como ficção ou não ficção com zero-shot classification.
3. Identifica as emoções presentes nas descrições.
4. Gera embeddings, armazena-os no Chroma e recupera os livros semanticamente mais próximos da consulta.

Principais tecnologias:

- Pandas, NumPy, Matplotlib e Seaborn para preparação e exploração dos dados;
- Hugging Face Transformers e PyTorch para classificação e análise de emoções;
- Ollama com `nomic-embed-text` para gerar embeddings localmente;
- Chroma para armazenar e consultar os vetores;
- LangChain para integrar embeddings e banco vetorial;
- Gradio para a interface web.

## Estrutura do projeto

```text
.
├── books.csv                       # Dataset original
├── books_cleaned.csv               # Dataset limpo
├── books_with_categories.csv       # Livros classificados por categoria
├── books_with_emotions.csv         # Livros com pontuações de emoções
├── data-exploration.ipynb           # Limpeza e análise exploratória
├── text-classification.ipynb        # Classificação fiction/nonfiction
├── sentiment-analysis.ipynb         # Análise das emoções
├── vector-search.ipynb              # Criação do índice vetorial
├── gradio-dashboard.py              # Aplicação web
├── cover-not-found.jpg              # Capa usada como fallback
└── requirements.txt                 # Dependências Python
```

O diretório `chroma_db/` é criado localmente pelo notebook de busca vetorial e não é versionado.

## Pré-requisitos

- Python 3.13;
- Ollama instalado e em execução;
- espaço livre para as dependências, modelos e cache;
- macOS com Apple Silicon para utilizar `device="mps"` nos notebooks, ou ajuste para `device="cpu"` em outros ambientes.

## Instalação

Clone o repositório, acesse a pasta do projeto e crie um ambiente virtual:

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
pip install -r requirements.txt
```

Baixe o modelo de embeddings usado pelo projeto:

```bash
ollama pull nomic-embed-text
```

O Ollama precisa estar ativo durante a criação do índice e durante as buscas do dashboard. Se necessário, inicie-o com:

```bash
ollama serve
```

## Preparação dos dados

Os arquivos processados já estão incluídos no repositório. Para refazer todo o pipeline, inicie o JupyterLab:

```bash
jupyter lab
```

Execute os notebooks nesta ordem:

1. `data-exploration.ipynb`
2. `text-classification.ipynb`
3. `sentiment-analysis.ipynb`
4. `vector-search.ipynb`

Os notebooks do Hugging Face baixam estes modelos na primeira execução:

- `facebook/bart-large-mnli`, usado na classificação de ficção e não ficção;
- `j-hartmann/emotion-english-distilroberta-base`, usado na análise de emoções.

Os modelos ficam no cache local do Hugging Face e não são baixados novamente enquanto permanecerem armazenados.

## Executando o dashboard

Confirme que o diretório `chroma_db/` foi criado pelo notebook `vector-search.ipynb`. Depois execute:

```bash
python gradio-dashboard.py
```

O terminal exibirá o endereço local da aplicação, normalmente:

```text
http://127.0.0.1:7860
```

Na interface, descreva um livro, selecione opcionalmente uma categoria e escolha um tom:

- Happy;
- Surprising;
- Angry;
- Suspenseful;
- Sad.

## Gerenciamento dos modelos locais

Para visualizar os modelos armazenados pelo Hugging Face:

```bash
hf cache ls
```

Para visualizar os modelos instalados no Ollama:

```bash
ollama list
```

## Observações

- O dashboard consulta o Ollama localmente; nenhuma chave de API é necessária para os embeddings.
- O primeiro processamento pode demorar devido ao download dos modelos e à criação do índice vetorial.
- Excluir `chroma_db/` exige executar novamente o notebook `vector-search.ipynb` antes de iniciar o dashboard.
