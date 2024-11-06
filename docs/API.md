# KG-RAG API Documentation

KG-RAG provides two REST API endpoints for accessing SPOKE's biomedical knowledge. This documentation details the endpoints, their parameters, and usage.

## Table of Contents
- [Base URI](#base-uri)
- [Endpoints](#endpoints)
  - [Disease-Centric Endpoint](#disease-centric-endpoint)
  - [Extended Endpoint](#extended-endpoint)
- [Response Format](#response-format)
- [Code Examples](#code-examples)

## Base URI
`https://spoke.rbvi.ucsf.edu/api/`

## Endpoints

### Disease-Centric Endpoint
**Endpoint**: `v1/kg_rag_context`

This endpoint is optimized for disease-related queries and is used by the Versa integration.

#### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| query | string | Yes | N/A | Query that the user enters |
| nodes | list | Yes | N/A | List of SPOKE disease nodes. Must be represented exactly as in SPOKE |
| search_depth | integer | No | 1 | Number of hops to search in SPOKE graph. Higher values increase response time exponentially |
| edge_evidence | boolean | No | True | Include statistical evidence (p-values, z-scores, etc.) from SPOKE |

### Extended Endpoint
**Endpoint**: `v1/kg_rag_context_extended`

This endpoint supports broader biomedical queries without requiring specific disease nodes.

#### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| query | string | Yes | N/A | Biomedical query in English language |
| search_depth | integer | No | 1 | Number of hops to search in SPOKE graph. Higher values increase response time exponentially |
| edge_evidence | boolean | No | True | Include statistical evidence (p-values, z-scores, etc.) from SPOKE |


## Response Format

Both endpoints return a JSON object with the following structure:

| Name | Type | Description |
|------|------|-------------|
| context | string | Context from SPOKE associated with the user query. This context can be used for RAG |
| query | string | Original user query passed to API |
| nodes | list | List of nodes and their associated nodetypes extracted from the user query |
| search_depth | integer | Search depth passed to API |
| edge_evidence | boolean | Flag that was passed to API |

## Code Examples

### Disease-Centric Endpoint

#### Python
```python
import requests

base_uri = "https://spoke.rbvi.ucsf.edu/api/"
endpoint = "v1/kg_rag_context"
uri = base_uri + endpoint

query = "What are the genes associated with alstrom syndrome and hypochondrogenesis?"
nodes = ["Alstrom syndrome", "hypochondrogenesis"]
search_depth = 1
edge_evidence = True

params = {
    "query": query,
    "nodes": nodes,
    "search_depth": search_depth,
    "edge_evidence": edge_evidence
}

response = requests.get(uri, params=params)
print(response.json())
```

#### cURL
```
curl "https://spoke.rbvi.ucsf.edu/api/v1/kg_rag_context?query=What%20are%20the%20genes%20associated%20with%20alstrom%20syndrome%20and%20hypochondrogenesis%3F&nodes=Alstrom%20syndrome&nodes=hypochondrogenesis&search_depth=1&edge_evidence=True"
```


### Extended Endpoint

#### Python
```
import requests

base_uri = "https://spoke.rbvi.ucsf.edu/api/"
endpoint = "v1/kg_rag_context_extended"
uri = base_uri + endpoint

query = "What are the sideffects of metformin?"
search_depth = 1
edge_evidence = False

params = {
    "query": query,
    "search_depth": search_depth,
    "edge_evidence": edge_evidence
}

response = requests.get(uri, params=params)
print(response.json())
```

#### cURL
```
curl -G "https://spoke.rbvi.ucsf.edu/api/v1/kg_rag_context_extended" \
--data-urlencode "query=What are the sideffects of metformin?" \
--data-urlencode "search_depth=1" \
--data-urlencode "edge_evidence=false"
```

