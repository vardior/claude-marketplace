# Permissions Settings

**Route**: `/settings/permissions`  
**Component**: `src/pages/Settings/Permissions/index.tsx`  
**Features**: [[rbac]], [[teams]]

## Purpose

Manage role-based access control for your workspace. Define custom roles with specific permission sets and assign them to team members.

## Access

- **Authentication**: Required
- **Permissions**: `ManageRoles` or `Admin` role
- **Conditions**: Available on Pro and Enterprise plans

## Layout

### Header Section

Page title "Roles & Permissions" with description text explaining the purpose.

**User Actions**:
- **New Role** button: Opens the role creation modal (requires `ManageRoles` permission)

### Roles Table

Lists all roles in the workspace, both default and custom.

| Column | Description |
|--------|-------------|
| Role Name | Name with badge showing member count |
| Type | "Default" or "Custom" indicator |
| Permissions | Comma-separated list of permission categories |
| Members | Number of users assigned this role |
| Actions | Dropdown menu (Edit, Duplicate, Delete) |

- **Sorting**: By name (A-Z, Z-A), by member count, by creation date
- **Filtering**: By type (Default/Custom), by permission category
- **Row Click**: Opens role detail panel on the right
- **Row Actions**:
  - Edit: Opens role detail panel in edit mode
  - Duplicate: Creates a copy with "Copy of" prefix
  - Delete: Opens confirmation modal (disabled for default roles)

### Role Detail Panel

Slides in from right when a role is selected. Shows full role configuration.

**Sections**:
- **Header**: Role name (editable for custom roles), type badge
- **Description**: Optional description field
- **Permissions**: Grouped checkboxes by category
- **Members**: List of assigned users with "Manage Members" link

**User Actions**:
- Save Changes: Saves edits (validation required)
- Cancel: Discards changes, closes panel
- Manage Members: Navigates to Team page filtered by this role

## Forms

### Role Edit Form

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| Name | Text input | Yes | 3-50 characters, unique within workspace |
| Description | Textarea | No | Max 500 characters |
| Permissions | Checkbox groups | Yes | At least one permission required |

**Validation**:
- Name must be unique (shows inline error if duplicate)
- Cannot remove all permissions from a role with members

**Submit**: PUT to `/api/roles/{id}`, shows success toast, updates table

## Modals

### New Role Modal

- **Trigger**: "New Role" button in header
- **Purpose**: Create a new custom role
- **Fields**:
  - Name (required)
  - Description (optional)
  - Copy permissions from (dropdown of existing roles, optional)
- **Actions**:
  - Create: POST to `/api/roles`, closes modal, selects new role in table
  - Cancel: Closes modal without action

### Delete Role Confirmation

- **Trigger**: Delete action in row dropdown
- **Purpose**: Confirm role deletion
- **Content**:
  - Warning icon and message
  - Shows number of affected members
  - If members exist: "These members will be reassigned to the default Member role"
- **Actions**:
  - Delete: DELETE to `/api/roles/{id}`, closes modal, removes from table
  - Cancel: Closes modal

### Bulk Reassign Modal

- **Trigger**: Attempting to delete a role with 10+ members
- **Purpose**: Choose reassignment target
- **Fields**:
  - Target role dropdown (excludes the role being deleted)
- **Actions**:
  - Reassign & Delete: Moves members, then deletes role
  - Cancel: Closes modal

## API Calls

- `GET /api/roles` - List all roles with member counts
- `GET /api/roles/{id}` - Get role details with full permission set
- `POST /api/roles` - Create new role
- `PUT /api/roles/{id}` - Update role
- `DELETE /api/roles/{id}` - Delete role
- `GET /api/permissions` - List available permissions grouped by category

## Conditional Behavior

### Plan Restrictions
- **Free plan**: Table shows default roles only, "New Role" button shows upgrade prompt
- **Pro plan**: Up to 10 custom roles, "New Role" disabled at limit with tooltip
- **Enterprise**: Unlimited custom roles

### Permission-Based UI
- Users without `ManageRoles`: See read-only view, no action buttons
- Users with `ViewRoles` only: Can see roles but not member lists

## Related

- **Pages**: [[settings-team]], [[settings-workspace]]
- **Features**: [[rbac]], [[teams]], [[audit-logging]]
- **APIs**: [[api-roles]], [[api-permissions]]
