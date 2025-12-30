# Role-Based Access Control (RBAC)

## What It Is

Control who can see and do what within your workspace. Define custom roles with specific permission sets, then assign those roles to team members. Permissions are additive—users get the combined permissions of all their assigned roles.

## Why You'd Use It

- **Security**: Limit access to sensitive operations like billing, deployments, or user management
- **Compliance**: Enforce least-privilege access for SOC 2, HIPAA, or other requirements
- **Organization**: Different teams need different capabilities (developers vs. finance vs. support)
- **Onboarding**: Automatically give new members appropriate access via default roles

## Availability

| Plan | What You Get |
|------|--------------|
| **Free** | 3 default roles (Admin, Member, Viewer). Cannot create custom roles. |
| **Pro** | Custom roles (up to 10), granular permissions, role duplication, multiple roles per user. |
| **Enterprise** | Unlimited roles, permission inheritance across workspaces, IP restrictions, audit logging of permission changes. |

## Key Capabilities

- **Create custom roles**: Define roles with exactly the permissions you need
- **Granular permissions**: 40+ individual permissions across 8 categories
- **Multiple roles per user**: Assign several roles; permissions combine additively
- **Role duplication**: Copy existing roles as starting templates
- **Default role assignment**: Automatically assign a role to new workspace members

## Getting Started

1. Go to **Settings → Permissions**
2. Review the three default roles to understand the permission structure
3. Click **New Role** to create a custom role (Pro/Enterprise)
4. Select the permissions you want to grant
5. Go to **Settings → Team** to assign roles to members

## Permission Categories

Permissions are grouped into these categories:

| Category | Controls |
|----------|----------|
| **Workspace** | Workspace settings, billing, API keys |
| **Team** | Inviting members, managing roles |
| **Projects** | Creating, editing, deleting projects |
| **Pipelines** | Pipeline configuration and execution |
| **Deployments** | Deployment targets and history |
| **Integrations** | Third-party connections |
| **Analytics** | Dashboards and reports |
| **Audit** | Viewing audit logs |

## Common Workflows

### Setting Up Access for a New Team

**Goal**: Configure appropriate access levels when starting fresh

1. Review default roles in **Settings → Permissions**
   - **Admin**: Full access to everything
   - **Member**: Can work with most features, can't manage workspace
   - **Viewer**: Read-only access
2. Identify if you need custom roles (e.g., "Developer" who can deploy but not manage billing)
3. Create custom roles with specific permission combinations
4. Assign roles to each team member in **Settings → Team**
5. Set the default role for new members in **Workspace Settings → Defaults**

**Result**: Team members have appropriate access from day one.

### Adding a Contractor with Limited Access

**Goal**: Give an external person access to only what they need

1. Go to **Settings → Permissions → New Role**
2. Name it "Contractor" or similar
3. Select only necessary permissions (e.g., `ViewPipelines`, `ViewDeployments`)
4. Save the role
5. Invite the contractor from **Settings → Team**
6. Assign them the Contractor role during the invite

**Result**: Contractor can see their relevant work but nothing else.

### Temporarily Elevating Access

**Goal**: Give someone extra permissions for a specific task

1. Go to **Settings → Team**
2. Find the user and click to open their detail panel
3. Click **Edit Roles**
4. Add the additional role (their existing role stays)
5. After they complete the task, remove the elevated role

**Result**: User temporarily has expanded access, easily reverted.

## Configuration Options

| Setting | Location | Default | Description |
|---------|----------|---------|-------------|
| Default role | Workspace Settings → Defaults | Member | Role auto-assigned to new members |
| Require 2FA for admins | Workspace Settings → Security | Off | Admins must have 2FA enabled |
| Permission inheritance | Workspace Settings → Security | On | Child workspaces inherit parent permissions (Enterprise) |

## Limitations

### What You Can't Do

- **Individual resource restrictions**: Permissions apply to resource *types* (all pipelines), not specific resources (Pipeline X). Use separate projects or workspaces for isolation.

- **Custom permissions**: You combine built-in permissions into roles, but can't create entirely new permissions.

- **Delete default roles**: Admin, Member, and Viewer are permanent. Create custom roles if you need different names.

- **Instant propagation**: Role changes may take up to 5 minutes to apply everywhere. Users can log out and back in to force refresh.

### Plan Limits

- **Free**: No custom roles, stuck with Admin/Member/Viewer
- **Pro**: Maximum 10 custom roles
- **Enterprise**: Unlimited

## Gotchas & Tips

**Permissions are additive**: If a user has Role A (can view) and Role B (can edit), they can edit. You can't use one role to restrict what another grants.

**Can't delete roles with members**: You must reassign all members before deleting a role. The UI will prompt you to choose a new role for affected users.

**Default roles can't be modified**: You can't change permissions on Admin, Member, or Viewer. Create custom roles instead.

**API keys inherit creator's permissions**: API keys can only do what the user who created them can do.

## Related Features

- **[[teams]]**: Where you assign roles to members
- **[[audit-logging]]**: Track who changed permissions (Enterprise)
- **[[sso]]**: Automatically assign roles based on identity provider groups
- **[[workspaces]]**: Permissions are scoped to workspace boundaries

## Frequently Asked Questions

**Q: Can a user have multiple roles?**  
Yes, on Pro and Enterprise plans. Permissions combine additively.

**Q: What happens when I delete a role?**  
You must reassign all members first. The UI will prompt you to choose a target role.

**Q: Can I see who has a specific permission?**  
Go to Settings → Permissions, click a role, and you'll see the member count. Click "View Members" to see the list.

**Q: Do role changes apply immediately?**  
Usually within seconds, but can take up to 5 minutes for all systems to sync. Logging out and back in forces immediate refresh.

**Q: Can I restrict access to specific projects?**  
Not directly with RBAC. Create separate workspaces for true isolation, or use project-level settings where available.

**Q: What's the difference between Admin and a custom role with all permissions?**  
Functionally identical, but Admin role cannot be deleted or modified, and is recognized by support for account recovery.
