# Restrict RoleBinding Mapping in RBAC

This policy monitors and restricts the mapping of RoleBindings to ensure proper access control and prevent potential privilege escalation in Kubernetes.

## Policy Description

The policy consists of two parts:

1. **ClusterRoleBinding Check** (`restrict-rolebinding-mapping.yaml`):
   - Monitors ClusterRoleBindings for proper role mapping
   - Identifies subjects (ServiceAccounts/Users/Groups) with potentially excessive permissions
   - Reports any violations of role mapping restrictions

2. **RoleBinding Check** (`restrict-rolebinding-mapping.yaml`):
   - Monitors namespace-scoped RoleBindings for proper role mapping
   - Identifies subjects with potentially excessive permissions
   - Reports any violations of role mapping restrictions

## Why This Policy?

Improper role binding mappings can lead to security risks and privilege escalation. This policy helps:

- Prevent unauthorized role assignments
- Identify subjects with excessive permissions
- Maintain proper access control
- Comply with security standards

## Policy Details

### Validation Rules

The policy checks for:
- Proper mapping between RoleBindings and Roles/ClusterRoles
- Associated subjects and their permissions
- Compliance with role mapping restrictions

### Message Format

When violations are found, the policy reports:
- The RoleBinding/ClusterRoleBinding name
- Associated Role/ClusterRole
- Subject type (ServiceAccount/User/Group)
- Subject name

## Usage

1. Apply the policy to your cluster:
   ```bash
   kubectl apply -f restrict-rolebinding-mapping.yaml
   ```

2. The policy runs in audit mode by default
3. Review the policy violations in your cluster

## Requirements

- Kubernetes version: 1.32 or higher
- Kyverno version: 1.12.0 or higher

## Auditing with NCTL

You can audit your cluster for this policy using NCTL with the following command:

```bash
nctl scan kubernetes --policies restrict-rolebinding-mapping --cluster --details
```

This command will:
- Scan your entire Kubernetes cluster
- Check specifically for improper RoleBinding/ClusterRoleBinding mappings
- Provide detailed information about any violations found
- Show which subjects have potentially excessive permissions through these mappings

The scan results will help you identify:
- Which role bindings grant excessive permissions
- Which roles are being improperly mapped
- Which subjects (users, groups, service accounts) have these permissions
- The scope of the permissions (cluster-wide or namespace-specific)

You can also scan specific namespaces to narrow down the results:

```bash
nctl scan kubernetes --policies restrict-rolebinding-mapping --namespace <namespace-name> --details
```

## Remediation

To fix violations:
1. Review role binding mappings
2. Remove unnecessary role assignments
3. Use more granular permissions if required
4. Update role bindings to follow least privilege principle

## Example

A violation would look like:
```
RoleBinding example-binding with ServiceAccount example-sa has improper role mapping to role example-role
```

## Security Best Practices

1. **Proper Role Mapping**:
   - Map roles to appropriate subjects
   - Use specific role names instead of wildcards
   - Document all role mappings

2. **Regular Auditing**:
   - Review role mappings regularly
   - Remove unnecessary mappings
   - Keep track of who has what roles

3. **Alternative Approaches**:
   - Use role-based access control (RBAC)
   - Implement proper authentication mechanisms
   - Consider using service accounts for automation

## Security Implications

1. **Risks of Improper Mapping**:
   - Potential privilege escalation
   - Unauthorized access to resources
   - Violation of least privilege principle

2. **Best Practices**:
   - Limit role mappings to specific use cases
   - Monitor role assignment activities
   - Implement proper logging and auditing

## Common Role Mapping Patterns to Monitor

1. **Sensitive Roles**:
   - Cluster-admin
   - Admin
   - Edit
   - View

2. **Control Plane Roles**:
   - System roles
   - Custom roles with broad permissions
   - Roles with sensitive resource access

## Contributing

Feel free to submit issues and enhancement requests! 