# Get Detections API reference

| About this reference | |
|---|---|
| **Article type** | API reference |
| **Audience** | Developers and security engineers |
| **Product** | CrowdStrike Falcon Detections API |

Use this endpoint to retrieve a list of detection IDs that match your query. You can then pass the returned IDs to the [Get Detection Details](#related-endpoints) endpoint to retrieve full detection records.

---

## Endpoint

```http
GET /detects/queries/detects/v1
```

---

## Authentication

This endpoint uses OAuth 2.0 bearer token authentication. Include your access token in the `Authorization` header of every request.

```http
Authorization: Bearer <access_token>
```

**Required scope:** `detects:read`

To generate an access token, see [Authentication: OAuth 2.0 Token Request](https://falcon.crowdstrike.com/documentation/46/crowdstrike-oauth2-based-apis).

!!! note
    Access tokens expire after 30 minutes. If you receive a `401 Unauthorized` response, generate a new token and retry the request.

---

## Request parameters

### Required headers

| Header | Value | Description |
|---|---|---|
| `Authorization` | `Bearer <access_token>` | OAuth 2.0 bearer token. See [Authentication: OAuth 2.0 Token Request](https://falcon.crowdstrike.com/documentation/46/crowdstrike-oauth2-based-apis). |
| `Content-Type` | `application/json` | Must be set on all requests. |

### Query parameters

At least one of `filter` or `q` is required. All other query parameters are optional.

| Parameter | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter` | string | Conditionally required | None | FQL (Falcon Query Language) expression to scope results. Required if `q` is not provided. For example: `status:'new'+severity_name:'High'` |
| `q` | string | Conditionally required | None | Free-text search string across all detection fields. Required if `filter` is not provided. |
| `limit` | integer | Required if `offset` is specified | 100 | Maximum number of detection IDs to return. Accepted range: 1 to 9999. |
| `offset` | integer | Optional | 0 | Starting position for pagination. Use with `limit` to page through results. |
| `sort` | string | Optional | None | Field to sort results by. Append `.asc` or `.desc` to specify sort direction. For example: `last_behavior.desc` |

!!! note
    Requests without `filter` or `q` return a `400 Bad Request` response. Always scope your query to avoid retrieving unbounded result sets.

---

## Example requests

### Minimal request

Retrieve the first 100 detection IDs with no filters applied.

```bash
curl -X GET "https://api.crowdstrike.com/detects/queries/detects/v1" \
  -H "Authorization: Bearer <access_token>" \
  -H "Content-Type: application/json"
```

### Filtered request

Retrieve up to 10 new high-severity detections, sorted by most recent behavior.

```bash
curl -X GET "https://api.crowdstrike.com/detects/queries/detects/v1\
?limit=10\
&filter=status%3A%27new%27%2Bseverity_name%3A%27High%27\
&sort=last_behavior.desc\
&offset=0" \
  -H "Authorization: Bearer <access_token>"
```

Decoded filter for readability:

```
filter=status:'new'+severity_name:'High'
```

### Python example

```python
import requests

url = "https://api.crowdstrike.com/detects/queries/detects/v1"

headers = {
    "Authorization": "Bearer <access_token>",
    "Content-Type": "application/json"
}

params = {
    "limit": 10,
    "filter": "status:'new'+severity_name:'High'",
    "sort": "last_behavior.desc",
    "offset": 0
}

response = requests.get(url, headers=headers, params=params)
print(response.json())
```

---

## Response

### 200 OK

A successful request returns a `meta` object with pagination details and a `resources` array containing matching detection IDs.

```json
{
  "meta": {
    "query_time": 0.003,
    "pagination": {
      "offset": 0,
      "limit": 10,
      "total": 243
    }
  },
  "resources": [
    "ldt:a1b2c3d4e5f6789:0001",
    "ldt:a1b2c3d4e5f6789:0002",
    "ldt:a1b2c3d4e5f6789:0003"
  ],
  "errors": []
}
```

### Response fields

| Field | Type | Description |
|---|---|---|
| `meta.query_time` | float | Time taken to process the query, in seconds. |
| `meta.pagination.offset` | integer | Starting position of the current result set. |
| `meta.pagination.limit` | integer | Number of IDs returned in this response. |
| `meta.pagination.total` | integer | Total number of detections matching the query. Use this value to determine when to stop paginating. |
| `resources` | array of strings | Detection IDs matching your query. Pass these to [Get Detection Details](#related-endpoints) to retrieve full records. |
| `errors` | array | Error objects. Empty on success. |

---

## Pagination

This endpoint returns results in pages. Use `limit` and `offset` together to retrieve all results across multiple requests.

**How it works:**

- `limit` controls how many IDs are returned per request.
- `offset` controls where the result set starts.
- To get the next page, increment `offset` by the value of `limit`.
- Stop paginating when `offset` exceeds `meta.pagination.total`.

**Example: Paginating through 243 detections in pages of 100**

```bash
# Page 1
GET /detects/queries/detects/v1?limit=100&offset=0

# Page 2
GET /detects/queries/detects/v1?limit=100&offset=100

# Page 3 (returns remaining 43 results)
GET /detects/queries/detects/v1?limit=100&offset=200
```

**Python pagination example**

```python
import requests

url = "https://api.crowdstrike.com/detects/queries/detects/v1"
headers = {"Authorization": "Bearer <access_token>"}

offset = 0
limit = 100
all_ids = []

while True:
    params = {"limit": limit, "offset": offset}
    response = requests.get(url, headers=headers, params=params).json()

    ids = response["resources"]
    all_ids.extend(ids)

    total = response["meta"]["pagination"]["total"]
    offset += limit

    if offset >= total:
        break

print(f"Retrieved {len(all_ids)} detection IDs")
```

---

## Rate limits

| Limit type | Value |
|---|---|
| Requests per minute | 100 |
| Requests per hour | 1,500 |
| Max results per request (`limit`) | 9,999 |

If you exceed the rate limit, the API returns a `429 Too Many Requests` response. Check the `Retry-After` header for the number of seconds to wait before retrying.

```bash
# Check the Retry-After header in a 429 response
HTTP/1.1 429 Too Many Requests
Retry-After: 30
```

!!! tip
    Use the maximum `limit` value of 9,999 to minimize the number of requests needed when retrieving large result sets.

---

## Error codes

| HTTP code | Error | Cause | Resolution |
|---|---|---|---|
| `400` | Bad Request | The `filter` parameter contains an invalid FQL expression. | Check your FQL syntax. See [FQL Reference](#) for supported operators and fields. |
| `401` | Unauthorized | The access token is missing, expired, or invalid. | Generate a new token and retry. See [Authentication: OAuth 2.0 Token Request](#). |
| `403` | Forbidden | The API client does not have the `detects:read` scope. | Update the client permissions in the Falcon console under **Support and Resources > API Clients and Keys**. |
| `429` | Too Many Requests | You have exceeded the rate limit. | Wait for the duration specified in the `Retry-After` response header, then retry. |
| `500` | Internal Server Error | An unexpected server error occurred. | Retry the request. If the issue continues, contact [CrowdStrike Support](#). |

---

## Best practices

**Use filters to narrow results before paginating.**
Applying a `filter` reduces the total result set, which means fewer pagination requests and faster retrieval. Avoid fetching all detections and filtering client-side.

**Always check `meta.pagination.total` before paginating.**
The total count tells you exactly how many pages to expect. Use it to build efficient pagination loops rather than relying on empty responses as a stop condition.

**Cache access tokens for their full 30-minute lifetime.**
Generating a new token for every request adds unnecessary latency and counts against your rate limit. Store the token and refresh it only when it expires or returns a `401`.

**Use the maximum `limit` of 9,999 for bulk retrieval.**
When you need all detection IDs, a single request with `limit=9999` is more efficient than multiple smaller requests.

**Handle `429` responses gracefully.**
Build retry logic that respects the `Retry-After` header. Avoid fixed sleep intervals, which can be too short or unnecessarily long.

---

## Related endpoints

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/detects/entities/summaries/GET/v1` | Get full detection details for a list of detection IDs. |
| `PATCH` | `/detects/entities/detects/v2` | Update the status or assignee of one or more detections. |
| `GET` | `/detects/queries/detects/v1` | List detection IDs (this endpoint). |
