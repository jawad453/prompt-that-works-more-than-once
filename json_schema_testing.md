# JSON Schema Testing

## Purpose

This document records the JSON schema requirements and validation process used for the job-listing extraction harness.

The schema ensures that the model returns a structured JSON object with the required fields and that the `skills` field is always an array of strings.

## Required JSON Structure

```json
{
  "job_title": null,
  "company": null,
  "location": null,
  "employment_type": null,
  "work_mode": null,
  "salary": null,
  "experience_required": null,
  "skills": []
}
```

## JSON Schema

The API request uses OpenAI's JSON Schema response format:

```python
schema = {
    "type": "object",
    "properties": {
        "job_title": {"type": ["string", "null"]},
        "company": {"type": ["string", "null"]},
        "location": {"type": ["string", "null"]},
        "employment_type": {"type": ["string", "null"]},
        "work_mode": {"type": ["string", "null"]},
        "salary": {"type": ["string", "null"]},
        "experience_required": {"type": ["string", "null"]},
        "skills": {
            "type": "array",
            "items": {"type": "string"}
        }
    },
    "required": [
        "job_title",
        "company",
        "location",
        "employment_type",
        "work_mode",
        "salary",
        "experience_required",
        "skills"
    ],
    "additionalProperties": False
}
```

The request passes the schema through the API using:

```python
response_format={
    "type": "json_schema",
    "json_schema": {
        "name": "JobInfo",
        "schema": schema
    }
}
```

## Prompt Fallback Rule

The extraction prompt contains:

> If no skills are mentioned, output `"skills": []`.

The prompt also requires:

> NEVER invent, guess, infer, estimate, or add information.

and:

> Return ONLY valid JSON.

## Response Validation Process

Each model response is checked in this order:

1. Capture `response.choices[0].message.content`.
2. Trim leading and trailing whitespace.
3. Verify that the response starts with `{` and ends with `}`.
4. Parse the response using `json.loads`.
5. Validate the parsed object against the JSON schema.
6. Confirm that `skills` is an array and every item is a string.
7. Record the raw response, parse result, schema result, and notes in `test_results/test_log.md`.

Example preprocessing:

```python
raw = response.choices[0].message.content.strip()
json_str = raw.split("\n", 1)[0]
```

If the response does not have the expected JSON-object boundaries, the exact raw response is recorded and the failure is noted as:

```text
fallback to empty JSON
```

## Test Coverage

### Job #1 — Senior Cloud Engineer - AWS

Checks:
- JSON object returned
- Required fields present
- `skills` is an array of strings
- Schema validation performed
- Exact job title rule preserved

This was the original prompt-failure case because Version 1 rewrote the job title. Version 2 added the explicit exact-title rule.

### Job #2 — Software Engineer — Pakistan Stock Exchange

Checks:
- JSON object returned
- Required fields present
- Explicit salary information preserved
- `skills` is an array of strings
- Schema validation performed

The first JSON parsing test for Job #2 produced no parsing error.

### Job #3 — Company Secretary — Pakistan Digital Authority

Checks:
- JSON object returned
- Required fields present
- No skills invented when none are explicitly present in the supplied listing
- `skills` follows the empty-array rule when no skills are mentioned
- Schema validation performed

## Testing Log Format

The detailed testing log uses these columns:

| JobID | RawOutput | ParseResult | SchemaPass | Notes |
|---|---|---|---|---|
| Job #1 | Captured model response | PASS/FAIL | PASS/FAIL | Parsing, schema, and prompt-change notes |
| Job #2 | Captured model response | PASS/FAIL | PASS/FAIL | Parsing, schema, and prompt-change notes |
| Job #3 | Captured model response | PASS/FAIL | PASS/FAIL | Empty-skills and schema notes |

## Failure Handling

If `json.loads` raises a `JSONDecodeError`:

1. Preserve the exact raw model response.
2. Record the exact error message.
3. Record the listing ID.
4. Record the prompt or schema change made to address the failure.
5. Re-run the affected listing.
6. Record the corrected result.

If schema validation fails:

1. Preserve the validation error.
2. Identify the field that violated the schema.
3. Record the prompt or schema adjustment.
4. Re-run the affected listing.
5. Record the final validation result.

## Expected Result

A successful response must:

- Be valid JSON.
- Be a JSON object.
- Contain all required fields.
- Have `skills` as an array.
- Have only strings inside `skills`.
- Use `null` when a field is not explicitly stated.
- Contain no invented information.
- Pass strict JSON schema validation.
