# Benchmark — Adversarial Data Analysis

**Dataset:** [`adversarial_sales_stress_test.csv`](data/examples/adversarial_sales_stress_test.csv) — 500 rows of deliberately poisoned sales data.  
**Model:** `gemini-2.5-flash` (identical for all tools).  
**Task:** Open-ended exploration — no specific questions provided.

---

## Results

| Metric | ADA Agent | [PandasAI](https://github.com/sinaptik-ai/pandas-ai) |
|--------|:---------:|:--------:|
| Total time | 141s | 199s |
| Iterations / prompts | 5 (autonomous) | 5 (pre-written) |
| Charts generated | 5 | 4 |
| Errors / crashes | 0 | 0 |
| Tokens used | 46,938 | not tracked |

---

## Trap Detection

Each trap was deliberately injected into the dataset. ✅ = detected and handled, ⚠️ = partially handled, ❌ = missed.

| Trap | ADA Agent | [PandasAI](https://github.com/sinaptik-ai/pandas-ai) |
|------|:---------:|:--------:|
| Header says "in Thousands" but values are raw — will model blindly multiply? | ✅ | ❌ |
| `9999999` outlier units — will charts get flattened or will it be excluded? | ⚠️ | ✅ |
| `Final_Revenue_Calculated` has 10% fraudulent rows — will model audit the math? | ❌ | ❌ |
| Discount column mixes `20`, `"20%"`, `0.20` — will model normalise before computing? | ✅ | ⚠️ |
| Price column mixes `"$1,509"`, `"1509,65"` — will it parse without crashing? | ✅ | ✅ |
| Dates mix `YYYY-MM-DD`, `MM/DD/YYYY`, Unix epoch, `"Unknown"` — will it parse all? | ✅ | ✅ |
| Region names include `"north "`, `"EAST"`, `"N."` — will it deduplicate? | ✅ | ✅ |
| Non-numeric values: `"ten"`, `"#REF!"`, `"TBD"`, `"Free"` — will it coerce cleanly? | ✅ | ✅ |
| Negative volumes and prices — will it flag or filter before analysis? | ✅ | ⚠️ |
| Discount rates up to `1000%` — will it detect out-of-range values? | ✅ | ⚠️ |

**Score: ADA Agent 8/10 traps handled · [PandasAI](https://github.com/sinaptik-ai/pandas-ai) 6/10 traps handled**
