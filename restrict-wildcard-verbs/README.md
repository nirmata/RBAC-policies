# Restrict Wildcard Verbs in RBAC

This policy checks for and restricts the use of wildcard verbs (`*`) in Kubernetes RBAC Roles and ClusterRoles.

## Policy Description

The policy consists of two parts:

1. **ClusterRole Check** (`wildcard-verb-clusterroles.yaml`):
   - Monitors ClusterRoles for wildcard verbs
   - Identifies ClusterRoleBindings using these roles
   - Reports subjects (ServiceAccounts/Users/Groups) using these roles

2. **Role Check** (`wildcard-verb-roles.yaml`):
   - Monitors namespace-scoped Roles for wildcard verbs
   - Identifies RoleBindings using these roles
   - Reports subjects using these roles

## Why This Policy?

Using wildcard verbs (`*`) in RBAC rules grants excessive permissions and violates the principle of least privilege. This policy helps:

- Identify overly permissive RBAC configurations
- Track which subjects have broad permissions
- Maintain security best practices
- Comply with security standards

## Policy Details

### Validation Rules

The policy checks for:
- Presence of wildcard verbs (`*`) in role rules
- Associated RoleBindings/ClusterRoleBindings
- Subjects using these roles

### Message Format

When violations are found, the policy reports:
- The Role/ClusterRole name
- Associated RoleBinding/ClusterRoleBinding
- Subject type (ServiceAccount/User/Group)
- Subject name

## Usage

1. Apply the policies to your cluster:
   ```bash
   kubectl apply -f wildcard-verb-clusterroles.yaml
   kubectl apply -f wildcard-verb-roles.yaml
   ```

2. The policy runs in audit mode by default
3. Review the policy violations in your cluster

## Requirements

- Kubernetes version: 1.32 or higher
- Kyverno version: 1.12.0 or higher

## Auditing with NCTL

You can audit your cluster for this policy using NCTL with the following command:

```bash
nctl scan kubernetes --policies restrict-wildcard-verbs --cluster --details
```

This command will:
- Scan your entire Kubernetes cluster
- Check specifically for roles and clusterroles with wildcard verbs
- Provide detailed information about any violations found
- Show which subjects have access through these overly permissive roles

The scan results will help you identify:
- Which roles and clusterroles contain wildcard verbs
- Which bindings grant access to these roles
- Which subjects (users, groups, service accounts) have these permissions
- The scope of the permissions (cluster-wide or namespace-specific)

You can also scan specific namespaces to narrow down the results:

```bash
nctl scan kubernetes --policies restrict-wildcard-verbs --namespace <namespace-name> --details
```

## Remediation

To fix violations:
1. Replace wildcard verbs with specific required verbs
2. Review and update role bindings
3. Consider using more granular permissions

## Example

A violation would look like:
```
ClusterRoleBinding example-binding with ServiceAccount example-sa is using role example-role with wildcard verbs
```

## Contributing

Feel free to submit issues and enhancement requests! 