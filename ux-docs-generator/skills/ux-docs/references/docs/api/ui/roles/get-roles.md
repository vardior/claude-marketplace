# GET /api/roles

**Handler**: `RolesController.GetRoles`  
**Type**: UI API  
**Features**: [[rbac]]

## Purpose

Retrieve all roles in the current workspace, including default roles and custom roles. Returns role metadata and member counts, but not full permission details (use GET /api/roles/{id} for that).

## Authentication

Required. Uses session cookie or Bearer token.

## Authorization

- Requires `ViewRoles` permission, OR
- User has `Admin` role, OR
- User has `ManageRoles` permission

Users without these permissions receive a 403 Forbidden.

## Request

### Parameters

| Name | In | Type | Required | Description |
|------|-----|------|----------|-------------|
| `include_members` | query | boolean | No | If true, includes list of member IDs for each role. Default: false |
| `type` | query | string | No | Filter by role type: `default`, `custom`, or `all`. Default: `all` |
| `sort` | query | string | No | Sort field: `name`, `member_count`, `created_at`. Default: `name` |
| `order` | query | string | No | Sort order: `asc` or `desc`. Default: `asc` |

### Example Request

```bash
curl -X GET "https://api.example.com/api/roles?type=custom&sort=member_count&order=desc" \
  -H "Authorization: Bearer {token}"
```

## Response

### Success (200 OK)

```json
{
  "roles": [
    {
      "id": "role_abc123",
      "name": "Admin",
      "description": "Full access to all workspace features",
      "type": "default",
      "member_count": 3,
      "permission_categories": ["workspace", "team", "projects", "pipelines", "deployments", "integrations", "analytics", "audit"],
      "created_at": "2024-01-01T00:00:00Z",
      "updated_at": "2024-01-01T00:00:00Z",
      "is_deletable": false,
      "is_editable": false
    },
    {
      "id": "role_def456",
      "name": "Developer",
      "description": "Can manage pipelines and deployments",
      "type": "custom",
      "member_count": 12,
      "permission_categories": ["projects", "pipelines", "deployments"],
      "created_at": "2024-03-15T10:30:00Z",
      "updated_at": "2024-06-20T14:45:00Z",
      "is_deletable": true,
      "is_editable": true
    }
  ],
  "total_count": 5,
  "default_role_id": "role_ghi789"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `roles` | array | List of role objects |
| `roles[].id` | string | Unique role identifier |
| `roles[].name` | string | Display name |
| `roles[].description` | string | Optional description |
| `roles[].type` | string | `default` or `custom` |
| `roles[].member_count` | integer | Number of users with this role |
| `roles[].permission_categories` | array | Categories with at least one permission granted |
| `roles[].created_at` | datetime | ISO 8601 creation timestamp |
| `roles[].updated_at` | datetime | ISO 8601 last update timestamp |
| `roles[].is_deletable` | boolean | Whether role can be deleted |
| `roles[].is_editable` | boolean | Whether role permissions can be modified |
| `total_count` | integer | Total number of roles |
| `default_role_id` | string | ID of the workspace's default role for new members |

### With `include_members=true`

Each role object includes an additional field:

```json
{
  "id": "role_def456",
  "name": "Developer",
  "members": ["user_111", "user_222", "user_333"]
}
```

### Errors

| Status | Code | Description |
|--------|------|-------------|
| 401 | `unauthorized` | Missing or invalid authentication |
| 403 | `forbidden` | User lacks ViewRoles permission |
| 500 | `internal_error` | Server error, retry later |

### Error Response Format

```json
{
  "error": {
    "code": "forbidden",
    "message": "You do not have permission to view roles",
    "details": {
      "required_permission": "ViewRoles"
    }
  }
}
```

## Side Effects

None. This is a read-only endpoint.

## Rate Limiting

- 100 requests per minute per user
- 1000 requests per minute per workspace

## Caching

Response is cached for 30 seconds. Member counts may be slightly stale.

## Related

- **Endpoints**: [[GET /api/roles/{id}]], [[POST /api/roles]], [[GET /api/permissions]]
- **Pages**: [[settings-permissions]]
- **Features**: [[rbac]]

## Usage Notes

- For displaying a roles table, this endpoint provides everything needed
- For editing a role, fetch full details with GET /api/roles/{id}
- Member counts update in near-real-time but may lag by a few seconds
- The `permission_categories` array is a summary; full permissions require the detail endpoint
