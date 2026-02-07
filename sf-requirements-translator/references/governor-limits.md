# Key Governor Limits for Architecture Decisions

## Per-Transaction Limits (Synchronous)
| Resource | Limit | Impact |
|---|---|---|
| SOQL queries | 100 | Avoid queries in loops |
| DML statements | 150 | Bulkify operations |
| Records retrieved (SOQL) | 50,000 | Use selective queries, pagination |
| Records processed (DML) | 10,000 | Split into batches if needed |
| Callouts | 100 | Consolidate external calls |
| Callout timeout | 120s (total) | Use async for long operations |
| Heap size | 6 MB (sync), 12 MB (async) | Watch large collections |
| CPU time | 10s (sync), 60s (async) | Move heavy logic to async |

## Flow-Specific
- Max 2,000 executed elements per flow interview
- Max 25,000 executed elements per transaction (all flows)
- Autolaunched flows run in system context by default
- Screen flows run in user context

## Agentforce-Specific
- Max 10 actions per topic (recommended, not hard limit)
- Agent conversation context window varies by model
- Knowledge grounding: retrieves top chunks per query
- Trust Layer adds latency per LLM call

## Data Volume Thresholds
| Volume | Approach |
|---|---|
| <10k records | Standard SOQL, no special handling |
| 10k-100k | Ensure indexes on filter fields, consider skinny tables |
| 100k-1M | Batch Apex, Async SOQL, Data Cloud for analytics |
| >1M | Big Objects, Data Cloud, external storage |

## Architecture Rules of Thumb
- **Prefer declarative** until hitting Flow element limits or complex logic
- **Batch Apex** for >50k records or long-running processes
- **Platform Events** for decoupling and async processing
- **Custom Metadata Types** for configurable thresholds (deployable, no data migration)
- **Never** query/DML in a loop — always bulkify
