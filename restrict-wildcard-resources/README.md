# Restrict Wildcard Resources in RBAC

This policy monitors and restricts the use of wildcard resources (`*`) in Kubernetes RBAC Roles and ClusterRoles.

## Policy Description

The policy consists of two parts:

1. **ClusterRole Check** (`restrict-wildcard-resources-clusterrole.yaml`):
   - Monitors ClusterRoles for wildcard resource permissions
   - Identifies ClusterRoleBindings using these roles
   - Reports subjects (ServiceAccounts/Users/Groups) with broad resource access

2. **Role Check** (`restrict-wildcard-resources-role.yaml`):
   - Monitors namespace-scoped Roles for wildcard resource permissions
   - Identifies RoleBindings using these roles
   - Reports subjects with broad resource access

## Why This Policy?

Using wildcard resources (`*`) in RBAC rules grants excessive permissions across all resource types. This policy helps:

- Prevent overly permissive resource access
- Identify subjects with broad resource permissions
- Maintain security best practices
- Comply with security standards

## Policy Details

### Validation Rules

The policy checks for:
- Presence of wildcard resources (`*`) in role rules
- Associated RoleBindings/ClusterRoleBindings
- Subjects with broad resource access

### Message Format

When violations are found, the policy reports:
- The Role/ClusterRole name
- Associated RoleBinding/ClusterRoleBinding
- Subject type (ServiceAccount/User/Group)
- Subject name

## Usage

1. Apply the policies to your cluster:
   ```bash
   kubectl apply -f restrict-wildcard-resources-clusterrole.yaml
   kubectl apply -f restrict-wildcard-resources-role.yaml
   ```

2. The policy runs in audit mode by default
3. Review the policy violations in your cluster

## Requirements

- Kubernetes version: 1.32 or higher
- Kyverno version: 1.12.0 or higher

## Auditing with NCTL

You can audit your cluster for this policy using NCTL with the following command:

```bash
nctl scan kubernetes --policies restrict-wildcard-resources --cluster --details
```

This command will:
- Scan your entire Kubernetes cluster
- Check specifically for roles and clusterroles with wildcard resources
- Provide detailed information about any violations found
- Show which subjects have broad access to all resource types

The scan results will help you identify:
- Which roles and clusterroles contain wildcard resources
- Which bindings grant access to these roles
- Which subjects (users, groups, service accounts) have these permissions
- The scope of the permissions (cluster-wide or namespace-specific)

You can also scan specific namespaces to narrow down the results:

```bash
nctl scan kubernetes --policies restrict-wildcard-resources --namespace <namespace-name> --details
```

## Remediation

To fix violations:
1. Review if broad resource access is necessary
2. Replace wildcard resources with specific resource types
3. Use more granular permissions if required
4. Update role bindings to follow least privilege principle

## Example

A violation would look like:
```
ClusterRoleBinding example-binding with ServiceAccount example-sa is using role example-role with wildcard resource permissions
```

## Security Best Practices

1. **Minimize Resource Access**:
   - Only grant access to necessary resource types
   - Use specific resource names instead of wildcards
   - Document all resource permissions

2. **Regular Auditing**:
   - Review resource permissions regularly
   - Remove unnecessary permissions
   - Keep track of who can access what resources

3. **Alternative Approaches**:
   - Use specific resource types
   - Implement proper resource isolation
   - Consider using namespace-level restrictions

## Security Implications

1. **Risks of Wildcard Resources**:
   - Potential privilege escalation
   - Unauthorized access to sensitive resources
   - Violation of least privilege principle

2. **Best Practices**:
   - Limit resource access to specific types
   - Monitor resource access patterns
   - Implement proper logging and auditing

## Common Resource Types to Monitor

1. **Sensitive Resources**:
   - Secrets
   - ConfigMaps
   - PersistentVolumes
   - ServiceAccounts

2. **Control Plane Resources**:
   - Nodes
   - Namespaces
   - ClusterRoles
   - ClusterRoleBindings

## Contributing

Feel free to submit issues and enhancement requests! 