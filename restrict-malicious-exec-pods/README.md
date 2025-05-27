# Restrict Malicious Exec into Pods

This policy monitors and restricts permissions to execute commands in pods, which could be used for malicious activities in Kubernetes clusters.

## Policy Description

The policy consists of two parts:

1. **ClusterRole Check** (`restrict-malicious-exec-pods-clusterrole.yaml`):
   - Monitors ClusterRoles for permissions to exec into pods
   - Identifies ClusterRoleBindings using these roles
   - Reports subjects (ServiceAccounts/Users/Groups) with pod exec permissions

2. **Role Check** (`restrict-malicious-exec-pods-role.yaml`):
   - Monitors namespace-scoped Roles for permissions to exec into pods
   - Identifies RoleBindings using these roles
   - Reports subjects with pod exec permissions

## Why This Policy?

The ability to execute commands in pods can be misused for malicious purposes if not properly restricted. This policy helps:

- Prevent unauthorized command execution in pods
- Identify subjects with exec permissions
- Maintain security best practices
- Comply with security standards

## Security Risk

Unrestricted pod exec permissions can allow attackers to:
- Execute arbitrary commands within containers
- Access sensitive data inside pods
- Move laterally within the cluster
- Modify application behavior at runtime
- Extract secrets and credentials

## Policy Details

### Validation Rules

The policy checks for:
- Permissions to exec into pods (create subresource pods/exec)
- Associated RoleBindings/ClusterRoleBindings
- Subjects with pod exec permissions

### Message Format

When violations are found, the policy reports:
- The Role/ClusterRole name
- Associated RoleBinding/ClusterRoleBinding
- Subject type (ServiceAccount/User/Group)
- Subject name

## Usage

1. Apply the policies to your cluster:
   ```bash
   kubectl apply -f restrict-malicious-exec-pods-clusterrole.yaml
   kubectl apply -f restrict-malicious-exec-pods-role.yaml
   ```

2. The policy runs in audit mode by default
3. Review the policy violations in your cluster

## Requirements

- Kubernetes version: 1.28 or higher
- Kyverno version: 1.12.0 or higher

## Auditing with NCTL

You can audit your cluster for this policy using NCTL with the following command:

```bash
nctl scan kubernetes --policies restrict-malicious-exec-pods --cluster --details
```

This command will:
- Scan your entire Kubernetes cluster
- Check specifically for roles and clusterroles with pod exec permissions
- Provide detailed information about any violations found
- Show which subjects have permissions to execute commands in pods

The scan results will help you identify:
- Which roles and clusterroles grant pod exec permissions
- Which bindings grant access to these roles
- Which subjects (users, groups, service accounts) have these permissions
- The scope of the permissions (cluster-wide or namespace-specific)

You can also scan specific namespaces to narrow down the results:

```bash
nctl scan kubernetes --policies restrict-malicious-exec-pods --namespace <namespace-name> --details
```

## Remediation

To fix violations:
1. Review if pod exec permissions are necessary
2. Limit exec permissions to specific pods or namespaces
3. Consider implementing logging and auditing for exec actions
4. Use alternative approaches for container management

## Example

A violation would look like:
```
ClusterRoleBinding example-binding with ServiceAccount example-sa is using role example-role with pod exec permissions
```

## Security Best Practices

1. **Minimize Exec Permissions**:
   - Grant exec permissions only when necessary
   - Use specific pod selectors when possible
   - Implement time-bound access through temporary credentials

2. **Regular Auditing**:
   - Review exec permissions regularly
   - Monitor and log all exec activities
   - Remove unnecessary permissions

3. **Alternative Approaches**:
   - Use init containers for initialization tasks
   - Implement proper CI/CD pipelines for updates
   - Design applications to avoid the need for runtime execution
   - Use sidecars for monitoring and debugging

## Related Security Concerns

This policy helps protect against:
- Container escape vulnerabilities
- Credential theft from within pods
- Lateral movement within the cluster
- Data exfiltration from containers
- Runtime modification of applications

## Contributing

Feel free to submit issues and enhancement requests! 