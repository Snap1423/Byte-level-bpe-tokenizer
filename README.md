# Byte-Level BPE Tokenizer from Scratch

An educational Python implementation of a byte-level Byte Pair Encoding (BPE) tokenizer. The main artifact is an annotated Jupyter notebook that shows how UTF-8 text becomes bytes, how frequent byte pairs are merged during training, and how the learned rules are used to encode and decode text.

This is a learning project, not a production-ready tokenizer and not a reproduction of any particular model's tokenizer.

## Attribution

This project was developed while learning from Andrej Karpathy's [BPE tokenizer tutorial](https://www.youtube.com/watch?v=zduSFxRajkE). The core learning approach—starting from bytes, repeatedly merging frequent adjacent pairs, and reconstructing a vocabulary from merge rules—follows that tutorial. The notebook's organisation, comments, multilingual test cases, validation, and repository documentation are project-specific modifications.

## What is BPE tokenization?

BPE starts with small units and repeatedly combines common adjacent pairs into new tokens. In this notebook the starting units are UTF-8 bytes, so the initial vocabulary always contains IDs `0` through `255`.

For example, if a pair of byte IDs occurs frequently in the training text, BPE creates a new token ID for that pair. Later merges can combine learned tokens into longer byte sequences. The saved merge rules determine how new text is encoded.

## Objectives

- Learn the mechanics of byte-level BPE from first principles.
- Keep the training, encoding, and decoding steps easy to inspect.
- Verify exact UTF-8 round trips with multilingual text.

## Features

- Converts text to UTF-8 byte IDs.
- Counts adjacent token pairs and merges the most frequent pair during training.
- Stores learned pair-to-token merge rules.
- Builds a token vocabulary for decoding.
- Encodes new text by applying the learned merge rules in training order.
- Decodes token IDs back to strict UTF-8 text.
- Runs round-trip tests for English, Kannada, Hindi, Japanese, Chinese, Korean, Arabic, Russian, and previously unseen UTF-8 input.

## Technology

- Python 3
- Jupyter Notebook
- Python standard library for the tokenizer implementation

## Project structure

```text
.
├── main.ipynb        # The complete annotated implementation and tests
├── original_notebook/
│   └── main_original.ipynb  # Preserved notebook before the cleanup
├── README.md
├── requirements.txt  # Notebook environment dependency
└── .gitignore
```

## Installation and execution

1. Clone or download this repository.
2. Create and activate a virtual environment (recommended).

   **Windows PowerShell:**

   ```powershell
   python -m venv .venv
   .\.venv\Scripts\Activate.ps1
   ```

   **macOS or Linux:**

   ```bash
   python3 -m venv .venv
   source .venv/bin/activate
   ```

3. Install the notebook dependency:

   ```bash
   python -m pip install -r requirements.txt
   ```

4. Start Jupyter and open `main.ipynb`:

   ```bash
   jupyter notebook
   ```

5. In Jupyter, choose **Run All Cells**. The final test cell should print `PASS` for every case.

The tokenizer itself uses no third-party Python package; `jupyter` is included only to run the notebook.

## How it works

### Training

`train_bpe` converts the training text to UTF-8 bytes, counts adjacent token pairs, selects the most frequent pair, replaces its non-overlapping occurrences with a new token ID, and records that rule. It repeats until the requested vocabulary size is reached or no pair remains.

### Encoding

`encode` first converts the input text to UTF-8 bytes. It then repeatedly applies only pairs found in the learned merge rules. When multiple rules match, it uses the lowest learned token ID first, which corresponds to the earliest learned rule. Because all byte IDs are present initially, input bytes that never occurred in the training text still work without an unknown-byte token.

### Decoding

`decode` expands each token ID back to its byte sequence, joins the bytes, and strictly decodes them as UTF-8. Invalid token IDs raise a clear `ValueError`.

## Example

After running the notebook:

```python
example = "Hello world!"
token_ids = encode(example)
print(token_ids)
print(decode(token_ids))
```

The first line is a list of integer token IDs (the exact list depends on the training corpus and vocabulary size). The second line is:

```text
Hello world!
```

## Current limitations

- The training corpus and vocabulary are intentionally tiny.
- Pair statistics are recomputed after every merge, which is simple but inefficient for large corpora.
- There is no pre-tokenization, normalization, special-token support, persistence format, or command-line interface.
- Strict UTF-8 decoding intentionally rejects token sequences that do not form valid UTF-8 text.
- This notebook is for understanding BPE, not for matching GPT-2, GPT-4, or another model tokenizer.

## Possible future improvements

- Train on a larger and more representative corpus.
- Save and load merge rules and vocabularies.
- Add special tokens and explicit text-normalization policies.
- Improve training performance with incremental pair-frequency updates.
- Add automated tests outside the notebook and a small reusable Python package.
