# Basic RAG

A lightweight, educational implementation of a Retrieval-Augmented Generation (RAG) pipeline using HotpotQA, dense retrieval, FAISS, and a locally stored Qwen2.5-7B-Instruct model.

The project is developed incrementally to provide a reproducible baseline and a foundation for exploring more advanced retrieval and reasoning methods.

## Project Status

The project currently includes:

- Corpus construction and dense retrieval.
- FAISS-based similarity search.
- A Simple RAG pipeline that generates answers using retrieved context.
- Prompt experiments comparing alternative answer-generation instructions.
- A targeted experiment investigating the effect of increasing the retrieval depth.

The experiments are intended for learning and error analysis. Results from the initial evaluation set are preliminary and should not be interpreted as definitive benchmark results.

## Pipeline

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
Retrieved Context
   ↓
Qwen2.5-7B-Instruct
   ↓
Generated Answer
   ↓
Evaluation and Error Analysis
```

## Dataset

The project uses the HotpotQA dataset loaded from Hugging Face.

The initial configuration includes:

- 100 selected samples for corpus construction.
- 10 questions for the initial evaluation.
- Context paragraphs split into retrieval chunks.
- Supporting facts retained for retrieval evaluation.

The retrieval component uses the constructed corpus rather than searching the entire HotpotQA dataset at inference time.

## Embedding Model

The project uses:

`sentence-transformers/all-MiniLM-L6-v2`

The model produces 384-dimensional embeddings. The embeddings are normalized before being indexed.

## Vector Retrieval

FAISS is used for dense similarity search with:

`IndexFlatIP`

Because the embeddings are normalized, inner-product similarity corresponds to cosine similarity.

The default retrieval configuration is:

```text
Top-k = 5
```

The retrieval experiments also investigate `top_k=10` for a targeted question.

## Language Model

The generation component uses Qwen2.5-7B-Instruct with 8-bit quantization.

The model is loaded locally and the model files are not included in this repository.

Model inference requires a compatible environment with the necessary dependencies and sufficient memory. The notebooks are designed for Google Colab, but runtime availability and resource limits may vary.

## Notebooks

### `01_build_corpus_and_index.ipynb`

Builds the initial dataset subset, constructs the retrieval corpus, generates embeddings, creates the FAISS index, and evaluates retrieval performance.

### `02_simple_rag.ipynb`

Loads the previously generated corpus and index, retrieves relevant chunks, generates answers with Qwen, and evaluates the initial Simple RAG baseline.

### `03_rag_experiments.ipynb`

Investigates alternative prompt designs, compares generated answers, inspects retrieved evidence for selected questions, and examines a targeted change in retrieval depth.

## Initial Results

The project has been evaluated on an initial set of 10 HotpotQA questions. The results are preliminary and are intended to support experimentation and error analysis rather than provide a definitive benchmark.

### Retrieval Evaluation

The initial retrieval evaluation achieved:

- Mean Recall@5: 0.80
- Retrieval Success Rate: 1.00

The retrieved context contained the reference answer for 9 out of 10 questions (90%). This indicates that the retrieval component often finds relevant information, although successful retrieval does not necessarily guarantee a correct generated answer.

### Generation and Prompt Experiments

Three alternative prompt designs were compared with the baseline Simple RAG system.

| Method | Exact Match (EM) | Mean Token F1 |
|---|---:|---:|
| Baseline Simple RAG | 0% | 12.31% |
| Concise RAG | 40% | 45% |
| Evidence-Focused RAG | 40% | 45% |
| Precise Answer RAG | 40% | 40% |

The prompt experiments improved answer quality on this initial evaluation set compared with the baseline. The Concise RAG and Evidence-Focused RAG configurations achieved the highest mean token F1, while the Precise Answer RAG prompt produced exact matches on some questions but also introduced errors on others.

The Concise RAG configuration is therefore retained as the current default for further experiments. However, the evaluation set is small, and the results do not establish that one prompt design will consistently outperform the others.

### Error Analysis and Retrieval Depth

Question-level analysis revealed several distinct error types, including answers that were present in the retrieved context but were not correctly generated, answers expressed in wording different from the reference, and answers missing from the top-5 retrieved chunks.

A targeted retrieval-depth experiment also showed that increasing the number of retrieved chunks from 5 to 10 enabled the system to find the reference answer for a question that the top-5 retrieval had missed. This suggests that retrieval depth can affect answer availability, although retrieving more context does not automatically guarantee better generation.

### Limitations

These findings are based on only 10 evaluation questions. The results are useful for identifying failure cases and guiding subsequent experiments, but larger-scale evaluation is required before drawing reliable conclusions about overall performance.
