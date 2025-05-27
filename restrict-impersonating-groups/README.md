# Restrict Impersonating Groups in RBAC

This policy monitors and restricts the ability to impersonate groups in Kubernetes through RBAC Roles and ClusterRoles.

## Policy Description

The policy consists of two parts:

1. **ClusterRole Check** (`restrict-impersonating-groups-clusterroles.yaml`):
   - Monitors ClusterRoles for permissions to impersonate groups
   - Identifies ClusterRoleBindings using these roles
   - Reports subjects (ServiceAccounts/Users/Groups) with impersonation permissions

2. **Role Check** (`restrict-impersonating-groups-role.yaml`):
   - Monitors namespace-scoped Roles for permissions to impersonate groups
   - Identifies RoleBindings using these roles
   - Reports subjects with impersonation permissions

## Why This Policy?

Group impersonation in Kubernetes can be a security risk as it allows users to act as different groups. This policy helps:

- Prevent unauthorized group impersonation
- Identify subjects with impersonation capabilities
- Maintain security best practices
- Comply with security standards

## Policy Details

### Validation Rules

The policy checks for:
- Permissions to impersonate groups
- Associated RoleBindings/ClusterRoleBindings
- Subjects with impersonation permissions

### Message Format

When violations are found, the policy reports:
- The Role/ClusterRole name
- Associated RoleBinding/ClusterRoleBinding
- Subject type (ServiceAccount/User/Group)
- Subject name

## Usage

1. Apply the policies to your cluster:
   ```bash
   kubectl apply -f restrict-impersonating-groups-clusterroles.yaml
   kubectl apply -f restrict-impersonating-groups-role.yaml
   ```

2. The policy runs in audit mode by default
3. Review the policy violations in your cluster

## Requirements

- Kubernetes version: 1.32 or higher
- Kyverno version: 1.12.0 or higher

## Auditing with NCTL

You can audit your cluster for this policy using NCTL with the following command:

```bash
nctl scan kubernetes --policies restrict-impersonating-groups --cluster --details
```

This command will:
- Scan your entire Kubernetes cluster
- Check specifically for roles and clusterroles with group impersonation permissions
- Provide detailed information about any violations found
- Show which subjects have permissions to impersonate groups

The scan results will help you identify:
- Which roles and clusterroles grant group impersonation permissions
- Which bindings grant access to these roles
- Which subjects (users, groups, service accounts) have these permissions
- The scope of the permissions (cluster-wide or namespace-specific)

You can also scan specific namespaces to narrow down the results:

```bash
nctl scan kubernetes --policies restrict-impersonating-groups --namespace <namespace-name> --details
```

## Remediation

To fix violations:
1. Review if group impersonation is necessary
2. Remove unnecessary impersonation permissions
3. Use more specific permissions if required
4. Update role bindings to follow least privilege principle

## Example

A violation would look like:
```
ClusterRoleBinding example-binding with ServiceAccount example-sa is using role example-role with group impersonation permissions
```

## Security Best Practices

1. **Minimize Impersonation**:
   - Only grant impersonation to trusted subjects
   - Use specific group names instead of wildcards
   - Document all impersonation permissions

2. **Regular Auditing**:
   - Review impersonation permissions regularly
   - Remove unnecessary permissions
   - Keep track of who can impersonate whom

3. **Alternative Approaches**:
   - Use direct group membership instead of impersonation
   - Implement proper authentication mechanisms
   - Consider using service accounts for automation

## Security Implications

1. **Risks of Group Impersonation**:
   - Potential privilege escalation
   - Bypass of access controls
   - Unauthorized access to resources

2. **Best Practices**:
   - Limit impersonation to specific use cases
   - Monitor impersonation activities
   - Implement proper logging and auditing

## Contributing

Feel free to submit issues and enhancement requests! 