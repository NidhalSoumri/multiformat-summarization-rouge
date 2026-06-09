# multiformat-summarization-rouge
Multi-strategy text summarization tool comparing zero-shot, few-shot, and chain-of-thought prompting via ROUGE metrics
# 📚 Multiformat Summarization Tool — Prompt Strategy Comparison

A text summarization tool that compares **3 prompt engineering strategies** (zero-shot, few-shot, chain-of-thought) and evaluates them objectively using **ROUGE metrics**. Built as a portfolio project to consolidate concepts of prompt engineering, LLM evaluation, and metric-driven model assessment.

## 🎯 Project Overview

Generative LLMs are non-deterministic and sensitive to prompt formulation. The same task can yield wildly different results depending on how the prompt is structured. This project benchmarks 3 classic prompting strategies on a summarization task, with rigorous evaluation via ROUGE — moving from subjective comparison to objective measurement.

## 🧪 The 3 Strategies Compared

| Strategy | Description | When it shines |
|---|---|---|
| **Zero-shot** | Direct task instruction, no example | Simple, low-token cost, baseline |
| **Few-shot** | 1+ example shown before the actual task | Imposing a specific output format |
| **Chain-of-Thought** | Force step-by-step reasoning before answering | Complex texts requiring hierarchy of ideas |

## 📊 ROUGE Evaluation

Each generated summary is compared against a human-written reference summary using:

- **ROUGE-1** — unigram overlap
- **ROUGE-2** — bigram overlap
- **ROUGE-L** — longest common subsequence

Each metric returns **Precision / Recall / F1** scores between 0 and 1 (higher = better).

### Known Limitations of ROUGE

ROUGE is purely lexical — it doesn't capture semantic equivalence. A semantically perfect paraphrase will score low if the wording differs. For production-grade evaluation, modern alternatives include:

- **BERTScore** — compares in embedding space, captures semantics
- **LLM-as-judge** — uses a strong LLM to evaluate generated outputs against criteria
- **Multiple reference summaries** — averages scores across several human-written references

## 🏗️ Architecture

```
Input Text
    │
    ▼
┌───────────────────────────────────────────┐
│  3 PARALLEL CHAINS (LangChain LCEL)       │
├───────────────────────────────────────────┤
│  [Zero-shot Prompt] | LLM | StrOutputParser  │
│  [Few-shot Prompt]  | LLM | StrOutputParser  │
│  [CoT Prompt]       | LLM | StrOutputParser  │
└───────────────────────────────────────────┘
    │
    ▼
[3 Generated Summaries]
    │
    ▼
[ROUGE Scorer] ←── Reference Summary
    │
    ▼
Comparison Table + Best Strategy Identified
```

## 🔧 Tech Stack

| Component | Choice | Rationale |
|---|---|---|
| Framework | **LangChain (LCEL)** | Modular pipeline, easy strategy swap |
| LLM | **Gemini 2.0 Flash** | Generous free tier, low latency |
| Evaluation | **rouge-score** | Standard implementation of ROUGE-1/2/L |
| UI | **Gradio** | Rapid prototyping, accessible to non-devs |
| Environment | **Google Colab** | Zero-setup development |

## 🎓 Key Design Decisions

- **Temperature = 0.3** : Higher than RAG (where I use 0) — summaries benefit from slight formulation variety while remaining faithful
- **One-shot example for few-shot** : Single example to demonstrate format without bloating token cost
- **Explicit step instruction in CoT** : The model is asked to identify main ideas, filter details, then synthesize — but to return ONLY the final summary
- **Reference summary written manually** : Quality of evaluation depends on quality of reference; production would use multiple human annotators

## 🔬 Observed Behaviors

During testing on Wikipedia-style technical text:

- **Few-shot tends to imitate the example structure** — including format markers, which can leak into output if not explicitly suppressed
- **Chain-of-Thought produces more concise summaries** when the prompt instructs to filter secondary details
- **Zero-shot remains competitive** on simple factual texts where structure isn't critical
- **ROUGE differences are often modest** on short factual texts, validating the need for semantic metrics on real production cases

## 🖥️ Interactive Interface

A Gradio interface is included to enable non-developers (e.g., product managers, content reviewers) to:
- Paste any input text
- Optionally provide a reference summary
- Compare all 3 strategies in parallel
- View ROUGE metrics in a clean comparison table

## 🔮 Production Considerations

For a real production deployment, the following improvements would be needed:

- **Semantic metrics** (BERTScore, LLM-judge) alongside ROUGE
- **Multiple reference summaries** averaged for robust evaluation
- **Larger benchmark dataset** beyond a single text (e.g., CNN/DailyMail, XSum)
- **Strategy auto-selection** based on input characteristics (length, complexity, domain)
- **Structured output parsing** to avoid format leaks observed in few-shot
- **Cost tracking per strategy** since few-shot and CoT consume more tokens than zero-shot

## 📂 Repository Structure

```
.
├── multiformat_summarization_rouge.ipynb   # Main notebook
├── README.md                                # This file
└── .gitignore                               # Python defaults
```

## 🛠️ Run It Yourself

1. Open `multiformat_summarization_rouge.ipynb` in Google Colab
2. Add your Gemini API key in Colab Secrets as `GOOGLE_API_KEY`
3. Run all cells
4. Use the Gradio interface to test custom texts

## 🔗 Related Project

This is part of a series on LLM ecosystem exploration. See also:
- [AI Data Assistant — RAG Pipeline](#) (TON_LIEN_DU_REPO_1) — Retrieval-Augmented Generation over PDFs

## 👤 Author

**Nidhal Soumri**
Engineering student at Polytech Nice Sophia
[GitHub](https://github.com/NidhalSoumri)
