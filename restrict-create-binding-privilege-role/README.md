# Restrict Creation of Bindings to Privileged Roles

This policy monitors and restricts permissions to create RoleBindings and ClusterRoleBindings that reference privileged roles, which could be used for privilege escalation in Kubernetes clusters.

## Policy Description

The policy (`restrict-create-binding-privilege-role.yaml`):
- Monitors permissions to create RoleBindings and ClusterRoleBindings
- Identifies subjects with the ability to bind privileged roles
- Reports potential privilege escalation paths via role binding creation

## Why This Policy?

The ability to create bindings to privileged roles is a critical security concern. This policy helps:

- Prevent privilege escalation by unauthorized binding creation
- Identify subjects with excessive binding permissions
- Maintain security best practices
- Comply with security standards

## Security Risk

Unrestricted binding creation permissions can allow attackers to:
- Create bindings to privileged roles like `cluster-admin`
- Grant themselves or others elevated permissions
- Bypass existing permission restrictions
- Gain full control of the Kubernetes cluster
- Establish persistent admin access

## Policy Details

### Validation Rules

The policy checks for:
- Permissions to create RoleBindings and ClusterRoleBindings
- Associated existing RoleBindings/ClusterRoleBindings
- Subjects with permissions to create these bindings

### Message Format

When violations are found, the policy reports:
- The Role/ClusterRole name
- Associated RoleBinding/ClusterRoleBinding
- Subject type (ServiceAccount/User/Group)
- Subject name

## Usage

1. Apply the policy to your cluster:
   ```bash
   kubectl apply -f restrict-create-binding-privilege-role.yaml
   ```

2. The policy runs in audit mode by default
3. Review the policy violations in your cluster

## Requirements

- Kubernetes version: 1.28 or higher
- Kyverno version: 1.12.0 or higher

## Auditing with NCTL

You can audit your cluster for this policy using NCTL with the following command:

```bash
nctl scan kubernetes --policies restrict-create-binding-privilege-role --cluster --details
```

This command will:
- Scan your entire Kubernetes cluster
- Check specifically for this policy
- Provide detailed information about any violations found
- Show which subjects have permissions to create bindings to privileged roles

The scan results will help you identify:
- Which roles grant binding creation permissions
- Which subjects (users, groups, service accounts) have these permissions
- The scope of the permissions (cluster-wide or namespace-specific)
- Potential remediation steps

## Remediation

To fix violations:
1. Review if binding creation permissions are necessary
2. Limit binding creation permissions to specific non-privileged roles
3. Implement additional admission controls for role binding creation
4. Consider implementing Kyverno or OPA/Gatekeeper policies to prevent privileged bindings

## Example

A violation would look like:
```
ClusterRoleBinding example-binding with ServiceAccount example-sa is using role example-role with permissions to create bindings to privileged roles
```

## Security Best Practices

1. **Restrict Binding Creation**:
   - Limit binding creation permissions to trusted admins only
   - Use specific role references instead of wildcards
   - Implement time-bound access through temporary credentials

2. **Layer Your Defenses**:
   - Use RBAC restrictions as the first line of defense
   - Implement admission controllers as a second layer
   - Regularly audit role bindings and cluster role bindings

3. **High-Risk Roles to Monitor**:
   - cluster-admin
   - admin
   - edit
   - Custom roles with elevated privileges
   - Roles with permissions to sensitive resources

## Relationship to Other Security Controls

This policy works best when combined with:
- Other RBAC security policies
- Runtime security monitoring
- Kubernetes audit logging
- Admission controllers
- Regular review of ClusterRoleBindings

## Contributing

Feel free to submit issues and enhancement requests! 