# Regex & LangChain Reference

---

## 1. Python `re` Module — Functions

| Function | Syntax | What it does |
|---|---|---|
| `re.match()` | `re.match(pattern, string, flags=0)` | Matches **only at the beginning** of the string |
| `re.search()` | `re.search(pattern, string, flags=0)` | Scans through the string, returns **first match** anywhere |
| `re.findall()` | `re.findall(pattern, string, flags=0)` | Returns a **list of all matches** |
| `re.finditer()` | `re.finditer(pattern, string, flags=0)` | Returns an **iterator of match objects** |
| `re.sub()` | `re.sub(pattern, repl, string, count=0)` | **Replaces** matches with `repl` |
| `re.subn()` | `re.subn(pattern, repl, string, count=0)` | Same as `sub()` but also returns **replacement count** |
| `re.split()` | `re.split(pattern, string, maxsplit=0)` | **Splits** string at each match |
| `re.compile()` | `re.compile(pattern, flags=0)` | **Compiles** pattern into a reusable regex object |
| `re.fullmatch()` | `re.fullmatch(pattern, string, flags=0)` | Matches pattern against the **entire string** |
| `re.escape()` | `re.escape(string)` | **Escapes** all non-alphanumeric characters in a string |

---

## 2. Regex Syntax Cheatsheet

### Special Characters

| Pattern | Meaning | Example |
|---|---|---|
| `.` | Any character except newline | `a.c` → `abc`, `axc` |
| `^` | Start of string | `^Hello` |
| `$` | End of string | `world$` |
| `*` | 0 or more (greedy) | `ab*` → `a`, `ab`, `abbb` |
| `+` | 1 or more (greedy) | `ab+` → `ab`, `abbb` |
| `?` | 0 or 1 (makes quantifier lazy when appended) | `ab?` → `a`, `ab` |
| `{n}` | Exactly n times | `a{3}` → `aaa` |
| `{n,m}` | Between n and m times | `a{2,4}` → `aa`, `aaa`, `aaaa` |
| `[]` | Character class | `[aeiou]`, `[a-z]`, `[^0-9]` |
| `\|` | Alternation (OR) | `cat\|dog` |
| `()` | Capturing group | `(ab)+` |
| `(?:...)` | Non-capturing group | `(?:ab)+` |
| `\` | Escape character | `\.` matches literal `.` |

### Shorthand Character Classes

| Pattern | Meaning |
|---|---|
| `\d` | Digit `[0-9]` |
| `\D` | Non-digit |
| `\w` | Word char `[a-zA-Z0-9_]` |
| `\W` | Non-word char |
| `\s` | Whitespace `[ \t\n\r\f\v]` |
| `\S` | Non-whitespace |
| `\b` | Word boundary |
| `\B` | Non-word boundary |

### Flags

| Flag | Short | Meaning |
|---|---|---|
| `re.IGNORECASE` | `re.I` | Case-insensitive matching |
| `re.MULTILINE` | `re.M` | `^` and `$` match per line |
| `re.DOTALL` | `re.S` | `.` matches newline too |
| `re.VERBOSE` | `re.X` | Allows whitespace/comments in pattern |

---

## 3. Greedy vs Lazy — The `?` Modifier

This is one of the most important distinctions in regex.

```python
import re

html = "<b>bold</b> and <i>italic</i>"

# Greedy — matches as MUCH as possible
re.findall(r'<.*>', html)
# → ['<b>bold</b> and <i>italic</i>']   ← gobbles everything

# Lazy — matches as LITTLE as possible
re.findall(r'<.*?>', html)
# → ['<b>', '</b>', '<i>', '</i>']       ← stops at first >
```

> Rule of thumb: append `?` to any quantifier (`*?`, `+?`, `{n,m}?`) to make it **lazy (non-greedy)**.

### More Greedy vs Lazy Examples

```python
text = "start MATCH_ONE end start MATCH_TWO end"

# Greedy
re.search(r'start.*end', text).group()
# → 'start MATCH_ONE end start MATCH_TWO end'

# Lazy
re.search(r'start.*?end', text).group()
# → 'start MATCH_ONE end'
```

---

## 4. Groups & Named Groups

```python
# Capturing group
m = re.search(r'(\d{4})-(\d{2})-(\d{2})', '2026-05-28')
m.group(0)  # → '2026-05-28'  (full match)
m.group(1)  # → '2026'
m.group(2)  # → '05'

# Named group: (?P<name>...)
m = re.search(r'(?P<year>\d{4})-(?P<month>\d{2})-(?P<day>\d{2})', '2026-05-28')
m.group('year')   # → '2026'
m.group('month')  # → '05'
```

---

## 5. Lookahead & Lookbehind (Zero-width Assertions)

```python
# Positive lookahead: foo(?=bar)  — 'foo' only if followed by 'bar'
re.findall(r'\d+(?= dollars)', '100 dollars and 50 euros')
# → ['100']

# Negative lookahead: foo(?!bar)
re.findall(r'\d+(?! dollars)', '100 dollars and 50 euros')
# → ['50']

# Positive lookbehind: (?<=foo)bar  — 'bar' only if preceded by 'foo'
re.findall(r'(?<=@)\w+', 'user@gmail and admin@yahoo')
# → ['gmail', 'yahoo']

# Negative lookbehind: (?<!foo)bar
```

---

## 6. Practical Examples

```python
import re

# Extract all HTML tags
re.findall(r'<.*?>', '<div class="x"><p>Hello</p></div>')
# → ['<div class="x">', '<p>', '</p>', '</div>']

# Validate email (basic)
re.fullmatch(r'[\w.+-]+@[\w-]+\.[a-zA-Z]{2,}', 'harsh@iitmandi.ac.in')

# Extract URLs
re.findall(r'https?://\S+', 'Visit https://iitmandi.ac.in for info')

# Remove extra whitespace
re.sub(r'\s+', ' ', 'too   many    spaces').strip()
# → 'too many spaces'

# Find all floats/ints in a string
re.findall(r'-?\d+\.?\d*', 'x=-3.14, y=42, z=0.001')
# → ['-3.14', '42', '0.001']

# Split on multiple delimiters
re.split(r'[,;|\s]+', 'a, b;c|d  e')
# → ['a', 'b', 'c', 'd', 'e']
```

---

---

## 7. LangChain — Tokenizers vs Text Splitters

These are **two completely different concepts** often confused with each other.

---

### Tokenizers

A **tokenizer** converts raw text into **tokens** — the atomic units that a language model reads. This is a **pre-processing step for the model**, not for chunking documents.

- Tokens can be words, subwords, or characters depending on the algorithm
- Used to **count tokens**, **stay within context limits**, or **encode text** for model input
- Examples: `tiktoken` (OpenAI), HuggingFace tokenizers

```python
import tiktoken

enc = tiktoken.encoding_for_model("gpt-4")
tokens = enc.encode("Hello, IIT Mandi!")
print(len(tokens))   # → 6  (token count)
```

In LangChain, tokenizers surface via `TokenTextSplitter` and length functions:

```python
from langchain.text_splitter import TokenTextSplitter

splitter = TokenTextSplitter(
    encoding_name="cl100k_base",   # tiktoken encoding
    chunk_size=500,                 # max tokens per chunk
    chunk_overlap=50
)
```

---

### Text Splitters

A **Text Splitter** divides a **long document** into **smaller chunks** suitable for embedding + retrieval (RAG). This is a **document processing step**, not a model input step.

- Operates on character/token/semantic boundaries
- The goal is to keep **semantically related content together** within a size limit
- Output is a list of `Document` objects passed to a vector store

```python
from langchain.text_splitter import RecursiveCharacterTextSplitter

splitter = RecursiveCharacterTextSplitter(
    chunk_size=1000,       # characters per chunk
    chunk_overlap=200,     # overlap to preserve context across chunks
    separators=["\n\n", "\n", " ", ""]
)
docs = splitter.split_text(long_text)
```

---

### Side-by-Side Comparison

| Aspect | Tokenizer | Text Splitter |
|---|---|---|
| **Purpose** | Encode text → model tokens | Split document → retrievable chunks |
| **Output** | Integer token IDs (or count) | List of text strings / `Document` objects |
| **Used by** | The LLM itself | The RAG / embedding pipeline |
| **Unit** | Subword tokens (BPE, WordPiece) | Characters, tokens, sentences, or semantic units |
| **Stage** | Model input encoding | Pre-retrieval document processing |
| **LangChain class** | Used inside `TokenTextSplitter` | `RecursiveCharacterTextSplitter`, `CharacterTextSplitter`, `MarkdownHeaderTextSplitter`, etc. |
| **Affects context window?** | Directly (token count = context usage) | Indirectly (chunk size ≈ token count per call) |

---

### LangChain Text Splitter Types

| Splitter | Splits on | Best for |
|---|---|---|
| `CharacterTextSplitter` | Single separator (default `\n\n`) | Simple plain text |
| `RecursiveCharacterTextSplitter` | `["\n\n", "\n", " ", ""]` hierarchy | General purpose, most commonly used |
| `TokenTextSplitter` | Token count via tiktoken | When you need exact token-level control |
| `MarkdownHeaderTextSplitter` | Markdown headers (`#`, `##`, etc.) | Markdown docs, structured notes |
| `HTMLHeaderTextSplitter` | HTML heading tags | Web-scraped content |
| `SentenceTransformersTokenTextSplitter` | HuggingFace tokenizer tokens | Open-source embedding models |
| `SemanticChunker` | Embedding similarity breakpoints | High-quality semantic chunking |

---

### One-Line Summary

> **Tokenizer** = how the model *reads* text.  
> **Text Splitter** = how *you* break documents into chunks before feeding them to the model.

---

*Generated: 2026-05-28*