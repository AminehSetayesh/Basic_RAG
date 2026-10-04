# Basic RAG

A lightweight and reproducible implementation of a Retrieval-Augmented Generation (RAG) pipeline using HotpotQA, dense retrieval, FAISS, and a local Qwen2.5-7B-Instruct model.

The project is developed incrementally, starting from a simple RAG baseline and providing a foundation for experimenting with more advanced retrieval and reasoning methods.

## Current Pipeline

The current implementation focuses on the retrieval component:

```text
HotpotQA
   ↓
Sample Selection
   ↓
Corpus Construction
   ↓
Text Embeddings
   ↓
FAISS Index
   ↓
Dense Retrieval
   ↓
Retrieval Evaluation
```

The next stage of the project will extend this retrieval pipeline into a complete Simple RAG system by connecting the retrieved context to the Qwen language model.

## Dataset

The current implementation uses the **HotpotQA** dataset loaded directly from Hugging Face.

For the initial experiment:

- 100 samples are selected from the validation split.
- 10 samples are used for the initial evaluation.
- Each HotpotQA context paragraph is treated as one retrieval chunk.
- Supporting facts are retained for evaluation but are not provided to the retriever.

## Embedding Model

The project uses:

`sentence-transformers/all-MiniLM-L6-v2`

Each corpus chunk is converted into a 384-dimensional normalized embedding.

## Vector Retrieval

FAISS is used for dense vector retrieval with:

`IndexFlatIP`

Because the embeddings are normalized, inner product is equivalent to cosine similarity for retrieval.

The current default retrieval setting is:

```text
Top-k = 5
```

## Retrieval Evaluation

The initial retrieval evaluation uses:

- Recall@5
- Retrieval Success Rate

A retrieved gold evidence chunk is identified using the HotpotQA supporting facts associated with each sample.

### Initial Result

On the initial 10-question evaluation set:

```text
Mean Recall@5        : 0.80
Retrieval Success Rate : 1.00
```

These results are an initial development baseline on a small evaluation set and should not be interpreted as a final benchmark result.

## Project Structure

```text
Basic_RAG/
├── notebooks/
│   └── 01_build_corpus_and_index.ipynb
├── .gitignore
├── requirements.txt
└── README.md
```

The following directories are generated during execution and are excluded from version control:

```text
data/
corpus/
indexes/
results/
```

## Running the Project

The project is designed to run in Google Colab.

The notebook downloads HotpotQA directly from Hugging Face and generates the required corpus, embeddings, FAISS index, and retrieval evaluation results.

## Reproducibility

The initial sample selection uses a fixed random seed:

```text
Random seed = 42
```

The selected samples and evaluation samples are therefore reproducible when the notebook is executed with the same configuration.
