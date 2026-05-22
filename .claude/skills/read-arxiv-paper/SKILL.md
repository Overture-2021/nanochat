---
name: read-arxiv-paper
description: Use this skill when asked to read an arXiv paper given an arXiv URL or arXiv ID
---

You will be given an arXiv paper URL or arXiv ID, for example:

https://www.arxiv.org/abs/2601.07372

or:

2601.07372

## Part 1: Normalize the paper ID and source URL

Extract the arXiv ID from the input.

Examples:

- `https://www.arxiv.org/abs/2601.07372` → `2601.07372`
- `https://arxiv.org/pdf/2601.07372` → `2601.07372`
- `https://arxiv.org/src/2601.07372` → `2601.07372`
- `2601.07372v2` → `2601.07372v2`

The source URL should be:

```text
https://www.arxiv.org/src/{arxiv_id}
````

The goal is to fetch the TeX source of the paper, not the PDF.

## Part 2: Download the paper source

Download the source archive to a local Codex cache directory:

```text
~/.cache/codex/knowledge/{arxiv_id}.tar.gz
```

If the file already exists, do not re-download it.

Create the parent directory if needed:

```text
~/.cache/codex/knowledge
```

## Part 3: Unpack the source

Unpack the source into:

```text
~/.cache/codex/knowledge/{arxiv_id}
```

If the directory already exists and appears to contain the unpacked source, reuse it.

Be aware that arXiv source downloads are usually gzip-compressed archives, but they can vary. If normal tar extraction fails, inspect the file type and handle common cases such as:

* `.tar.gz`
* `.gz` containing a single `.tex` file
* source files without a conventional archive structure

## Part 4: Locate the LaTeX entrypoint

Find the main LaTeX entrypoint.

Common names include:

```text
main.tex
paper.tex
ms.tex
article.tex
root.tex
```

If no obvious filename exists, search for files containing:

```tex
\documentclass
\begin{document}
```

Prefer the file that contains both.

If multiple candidates exist, choose the one that appears to be the top-level paper file and briefly note the choice.

## Part 5: Read the paper source

Read the entrypoint and recursively follow relevant LaTeX includes, such as:

```tex
\input{...}
\include{...}
\subfile{...}
\bibliography{...}
\addbibresource{...}
```

Read enough of the source to understand:

* the abstract
* introduction and motivation
* main method or model
* experiments or evaluation
* key results
* limitations
* related work, when useful
* appendix material, when it affects the main technical claims

Also inspect figure captions, tables, algorithm blocks, theorem statements, and important equations.

Do not rely only on the abstract.


## Part 6: Write the summary

Write the final summary to a markdown file in the current working directory:

```text
./knowledge/summary_{tag}.md
```

Use the local `./knowledge` directory, not the cache directory.

Create `./knowledge` if needed.

Generate a short, descriptive tag based on the paper topic, for example:

```text
conditional_memory
diffusion_planning
test_time_training
retrieval_attention
```

Before writing, check whether the target filename already exists. If it does, choose a non-conflicting name such as:

```text
summary_{tag}_2.md
summary_{tag}_3.md
```

## Summary format

The markdown summary should include:

```markdown
# Paper Summary: {paper title}

- **arXiv ID:** {arxiv_id}
- **Source:** {source URL}
- **Local source path:** `{unpacked_source_path}`
- **Entrypoint:** `{entrypoint_tex_file}`

## 1. One-paragraph summary

Briefly explain the paper’s main idea and why it matters.

## 2. Core problem

What problem is the paper trying to solve?

## 3. Key ideas

Explain the main technical ideas in clear, direct, concise language.

## 4. Method details

Describe the model, algorithm, system, theory, or experimental setup.

## 5. Results

Summarize the most important empirical or theoretical results.

## 6. Limitations and caveats

List weaknesses, assumptions, missing experiments, scalability issues, or unclear claims.

## 7. Project relevance

Explain how the paper may or may not apply to the current local project.

Include concrete implementation ideas when possible.

## 8. Possible next steps

Suggest practical follow-up actions, such as:

- files to inspect next
- prototype ideas
- experiments to run
- benchmarks to reproduce
- parts of the paper to re-read carefully
```

## Operating notes

Prefer using local command-line tools such as:

```bash
mkdir -p
curl -L
tar
file
find
rg
sed
python
```

Use robust, inspectable steps.

Do not delete downloaded or unpacked sources unless explicitly asked.

Do not overwrite existing summaries.
