# Restrict Secrets Access in RBAC

This policy monitors and restricts access to Kubernetes Secrets through RBAC Roles and ClusterRoles.

## Policy Description

The policy consists of two parts:

1. **ClusterRole Check** (`access-secrets-clusterroles.yaml`):
   - Monitors ClusterRoles for permissions to access Secrets
   - Identifies ClusterRoleBindings using these roles
   - Reports subjects (ServiceAccounts/Users/Groups) with Secrets access

2. **Role Check** (`access-secrets-roles.yaml`):
   - Monitors namespace-scoped Roles for permissions to access Secrets
   - Identifies RoleBindings using these roles
   - Reports subjects with Secrets access

## Why This Policy?

Secrets in Kubernetes contain sensitive information like credentials, tokens, and keys. This policy helps:

- Identify who has access to Secrets
- Prevent unauthorized access to sensitive data
- Maintain security best practices
- Comply with security standards and regulations

## Policy Details

### Validation Rules

The policy checks for:
- Permissions to access Secrets resources
- Associated RoleBindings/ClusterRoleBindings
- Subjects with Secrets access permissions

### Message Format

When violations are found, the policy reports:
- The Role/ClusterRole name
- Associated RoleBinding/ClusterRoleBinding
- Subject type (ServiceAccount/User/Group)
- Subject name

## Usage

1. Apply the policies to your cluster:
   ```bash
   kubectl apply -f access-secrets-clusterroles.yaml
   kubectl apply -f access-secrets-roles.yaml
   ```

2. The policy runs in audit mode by default
3. Review the policy violations in your cluster

## Requirements

- Kubernetes version: 1.32 or higher
- Kyverno version: 1.12.0 or higher

## Auditing with NCTL

You can audit your cluster for this policy using NCTL with the following command:

```bash
nctl scan kubernetes --policies restrict-secrets-access --cluster --details
```

This command will:
- Scan your entire Kubernetes cluster
- Check specifically for roles and clusterroles with access to Secrets
- Provide detailed information about any violations found
- Show which subjects have permissions to access Secrets

The scan results will help you identify:
- Which roles and clusterroles grant Secrets access permissions
- Which bindings grant access to these roles
- Which subjects (users, groups, service accounts) have these permissions
- The scope of the permissions (cluster-wide or namespace-specific)

You can also scan specific namespaces to narrow down the results:

```bash
nctl scan kubernetes --policies restrict-secrets-access --namespace <namespace-name> --details
```

## Remediation

To fix violations:
1. Review if Secrets access is necessary
2. Use more granular permissions if access is required
3. Consider using alternative solutions like:
   - External Secrets Operator
   - HashiCorp Vault
   - Sealed Secrets
4. Update role bindings to follow least privilege principle

## Example

A violation would look like:
```
ClusterRoleBinding example-binding with ServiceAccount example-sa is using role example-role with Secrets access
```

## Security Best Practices

1. **Minimize Secrets Access**:
   - Only grant access to necessary subjects
   - Use specific verbs instead of wildcards
   - Limit to specific namespaces when possible

2. **Regular Auditing**:
   - Review Secrets access regularly
   - Remove unnecessary permissions
   - Document access requirements

3. **Alternative Solutions**:
   - Consider using external secret management
   - Implement encryption at rest
   - Use RBAC with specific resource names

## Contributing

Feel free to submit issues and enhancement requests! 