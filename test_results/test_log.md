# Test Log

The reusable prompt was tested on 15 real job listings.

| # | Job / Organization | Result |
|---|---|---|
| 1 | Senior Cloud Engineer - AWS — Smart Working | Initial FAIL → Retest PASS |
| 2 | Software Engineer — Pakistan Stock Exchange | PASS |
| 3 | Company Secretary — Pakistan Digital Authority | PASS |
| 4 | Digital Marketing Specialist — Wing | PASS |
| 5 | Senior Network Administrator — PRAL | PASS |
| 6 | Senior Manager, Corporate Communications — Visa | PASS |
| 7 | Assistant Director - Lead Talent Consultant — EY | PASS |
| 8 | Customer Service Representative (Sales) — ZDIGITIZING | PASS |
| 9 | Geography Sales Manager - Lahore — Unilever | PASS |
| 10 | Company Secretary — PECCEF Company | PASS |
| 11 | Data Entry Operator — Inter Market Knit Pvt Limited | PASS |
| 12 | Customer Service Representative — BA Communication Services | PASS |
| 13 | Assistant Key Account Manager - Islamabad — Unilever | PASS |
| 14 | Executive Director – Legal and Regulatory Affairs — Pakistan Digital Authority | PASS |
| 15 | Key Accounts Manager — Acgile | PASS |

## Genuine Prompt Failure

Job #1 initially failed because the model changed the exact job title instead of copying it exactly.

### Prompt Fix

The prompt was changed to explicitly require:

"job_title MUST be copied exactly from the job listing's stated job title."

It also instructs the model not to rewrite, summarize, improve, or add information from the job description.

### Retest

Job #1 was retested using the revised prompt and passed.

## Final Result

15 jobs tested  
15 final PASS  
1 genuine prompt failure identified and fixed  
0 unresolved failures