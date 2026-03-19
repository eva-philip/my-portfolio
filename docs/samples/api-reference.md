# Get Detections — API Reference

| | |
|---|---|
| **Article type** | API reference documentation |
| **Audience** | Developers and security engineers |
| **Product** | CrowdStrike Falcon Detections API |

## Endpoint
```http
GET /detects/queries/detects/v1
```

Returns a list of detection IDs matching the specified query parameters. Use the returned IDs with the Get Detection Details endpoint to retrieve full detection data.

## Authentication

This endpoint requires OAuth 2.0 bearer token authentication. Include your access token in the Authorization header:
```http
Authorization: Bearer <access_token>
```

Required OAuth scope: `detects:read`

!!! note
    To generate an access token, see Authentication — OAuth 2.0 Token Request.

## Request Parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `limit` | integer | No | Maximum number of detection IDs to return. Default: 100. Maximum: 9999. |
| `offset` | integer | No | Starting position for pagination. Use with `limit` to page through results. |
| `filter` | string | No | FQL query to filter results. For example: `status:'new'+severity_name:'High'` |
| `sort` | string | No | Field to sort results by. For example: `last_behavior.desc` or `created_timestamp.asc` |
| `q` | string | No | Full-text search across detection fields. |

## Example Request
```http
GET /detects/queries/detects/v1
  ?limit=10
  &filter=status%3A%27new%27%2Bseverity_name%3A%27High%27
  &sort=last_behavior.desc
  &offset=0
Authorization: Bearer eyJhbGciOiJSUzI1NiIsI...
```

## Response

### 200 OK
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

### Response Fields

| Field | Type | Description |
|---|---|---|
| `meta.query_time` | float | Time taken to process the query, in seconds |
| `meta.pagination.offset` | integer | Starting position of the current result set |
| `meta.pagination.limit` | integer | Number of results returned in this response |
| `meta.pagination.total` | integer | Total number of detections matching the query |
| `resources` | array of strings | List of detection IDs. Pass these to Get Detection Details to retrieve full records |
| `errors` | array | Array of error objects. Empty on success. |

## Error Codes

| HTTP Code | Error | Description |
|---|---|---|
| `400` | Bad Request | The query contains an invalid FQL expression. Check your `filter` parameter syntax. |
| `401` | Unauthorized | The access token is missing, expired, or invalid. Generate a new token and retry. |
| `403` | Forbidden | The API client does not have the `detects:read` scope. Update client permissions in the Falcon console. |
| `429` | Too Many Requests | Rate limit exceeded. Wait before retrying. See API Rate Limits for details. |
| `500` | Internal Server Error | An unexpected error occurred. Retry the request. If the issue persists, contact CrowdStrike Support. |

## Pagination

This endpoint supports pagination using `limit` and `offset`. To retrieve the next page, increment the offset by the limit value:
```http
# First page
GET /detects/queries/detects/v1?limit=100&offset=0

# Second page
GET /detects/queries/detects/v1?limit=100&offset=100
```

Use `meta.pagination.total` to determine the total number of results and stop paginating when offset exceeds total.

## Related Endpoints

- `GET /detects/entities/summaries/GET/v1` — Get Detection Details
- `PATCH /detects/entities/detects/v2` — Update Detection Status
- `GET /detects/queries/detects/v1` — List Detection IDs (this endpoint)