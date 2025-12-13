---
title: University Search System using Elasticsearch
categories: [Software Development, Small Application]
---

# University Search with Elasticsearch (CSV → Logstash → Index → Web UI)

A small information retrieval project that indexes a CSV of university data into **Elasticsearch** and provides a browser-based UI for searching across **Name**, **Description**, and **Website**. The UI supports:

- multi-field keyword search
- simple boolean logic (AND / OR)
- optional partial (wildcard) search mode
- relevance score display per result

---

## Dataset format

The dataset is a CSV with three fields:

- `Name`
- `Description`
- `Website`

Example row:

```csv
Name,Description,Website
Massachusetts Institute of Technology (MIT),"Massachusetts Institute of Technology (MIT), located in Cambridge, Massachusetts...",https://engineering.mit.edu
```

---

## System architecture

1. **Logstash** reads the CSV and parses fields
2. Documents are indexed into Elasticsearch under the `university` index
3. A **static web page** sends queries to Elasticsearch and renders results

---

## Indexing pipeline (Logstash)

This Logstash config loads a CSV file into Elasticsearch. I used a file input (full path via env var), a CSV filter to map columns, and an Elasticsearch output to write into the `university` index.

```conf
input {
  file {
    path => "${CSV_PATH}"        # full path required
    start_position => "beginning"
    sincedb_path => "/dev/null"
  }
}

filter {
  csv {
    separator => ","
    columns => ["Name", "Description", "Website"]
  }
}

output {
  elasticsearch {
    hosts => "localhost:9200"
    index => "university"
  }
  stdout {}
}
```

What this demonstrates:

- ingestion workflow (ETL-style)
- structuring data into fields for search
- repeatable indexing for local testing

---

## Search UI behavior

The UI provides two search modes:

### 1) Completed search (keyword + AND/OR)

- User types a query such as: `Texas AND engineering` or `MIT OR Stanford`
- Each term searches across multiple fields using `multi_match`
- AND/OR logic is expressed using boolean query composition

Key idea (query building):

- default operator is OR
- when `AND` is present, terms are combined into a `bool.must`

Example of the request body pattern:

```js
const requestBody = {
  query: {
    bool: {
      should: shouldClauses,
    },
  },
};
```

### 2) Partial query mode (wildcards)

Partial query is useful when the user wants results while typing (e.g., `Tex` → Texas).

In partial mode:

- the main search bar is disabled
- a new input appears for wildcard search
- the query uses `wildcard` across multiple fields

Example pattern:

```js
const requestBody = {
  query: {
    bool: {
      should: [
        { wildcard: { Name: `*${partial}*` } },
        { wildcard: { Description: `*${partial}*` } },
        { wildcard: { Website: `*${partial}*` } },
      ],
    },
  },
};
```

---

## Result rendering

Each hit is displayed as a “card” with:

- university name
- description
- link to website (if available)
- Elasticsearch `_score` to show relative relevance

This made it easier to verify whether query changes improved ranking.

---

## Challenges and what I learned

### Query logic is harder than it looks

Supporting AND/OR in a single input box means the frontend must:

- tokenize input
- track an operator state
- build nested bool queries correctly (must/should)

### Wildcards are convenient but can be expensive

Wildcard queries are great for “search as you type,” but they can be heavy at scale. This project highlighted the tradeoff between:

- usability (partial matching)
- performance (query cost)

---

## Improvements I would make next

### 1) Use analyzers instead of wildcard for partial matching

For partial search, a better approach is typically:

- `match_phrase_prefix`, or
- edge n-gram analyzers, or
- `search_as_you_type` mapping

This improves speed and relevance compared to wildcard scans.

### 2) Use `simple_query_string` for boolean text input

Elasticsearch can parse `AND` / `OR` safely without manual query assembly by using `simple_query_string` over multiple fields. That would simplify the frontend and reduce edge cases.

### 3) Add mappings and normalization

Define explicit mappings:

- keyword vs text fields
- lowercase normalizers for consistent matching
- optional boosting (e.g., Name matches score higher than Description matches)

### 4) Add a backend proxy (security + CORS)

For real deployment, the browser should not call Elasticsearch directly. A minimal backend would:

- protect the cluster from public access
- sanitize queries
- handle pagination and rate limiting

---
