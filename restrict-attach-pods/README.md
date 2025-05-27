# Restrict Attach Access to Pods

This policy monitors and restricts permissions to attach to pods, which could be used for unauthorized access and potential security breaches in Kubernetes clusters.

## Policy Description

The policy consists of two parts:

1. **ClusterRole Check** (`restrict-attach-pods-clusterrole.yaml`):
   - Monitors ClusterRoles for permissions to attach to pods
   - Identifies ClusterRoleBindings using these roles
   - Reports subjects (ServiceAccounts/Users/Groups) with pod attach permissions

2. **Role Check** (`restrict-attach-pods-role.yaml`):
   - Monitors namespace-scoped Roles for permissions to attach to pods
   - Identifies RoleBindings using these roles
   - Reports subjects with pod attach permissions

## Why This Policy?

The ability to attach to pods can be misused for unauthorized access if not properly restricted. This policy helps:

- Prevent unauthorized attachment to pods
- Identify subjects with attach permissions
- Maintain security best practices
- Comply with security standards

## Security Risk

Unrestricted pod attach permissions can allow attackers to:
- Gain interactive access to running containers
- Access terminal sessions within pods
- Intercept container I/O streams
- Access sensitive data inside pods
- Establish persistent access to the cluster

## Policy Details

### Validation Rules

The policy checks for:
- Permissions to attach to pods (create subresource pods/attach)
- Associated RoleBindings/ClusterRoleBindings
- Subjects with pod attach permissions

### Message Format

When violations are found, the policy reports:
- The Role/ClusterRole name
- Associated RoleBinding/ClusterRoleBinding
- Subject type (ServiceAccount/User/Group)
- Subject name

## Usage

1. Apply the policies to your cluster:
   ```bash
   kubectl apply -f restrict-attach-pods-clusterrole.yaml
   kubectl apply -f restrict-attach-pods-role.yaml
   ```

2. The policy runs in audit mode by default
3. Review the policy violations in your cluster

## Requirements

- Kubernetes version: 1.28 or higher
- Kyverno version: 1.12.0 or higher

## Auditing with NCTL

You can audit your cluster for this policy using NCTL with the following command:

```bash
nctl scan kubernetes --policies restrict-attach-pods --cluster --details
```

This command will:
- Scan your entire Kubernetes cluster
- Check specifically for roles and clusterroles with pod attach permissions
- Provide detailed information about any violations found
- Show which subjects have permissions to attach to pods

The scan results will help you identify:
- Which roles and clusterroles grant pod attach permissions
- Which bindings grant access to these roles
- Which subjects (users, groups, service accounts) have these permissions
- The scope of the permissions (cluster-wide or namespace-specific)

You can also scan specific namespaces to narrow down the results:

```bash
nctl scan kubernetes --policies restrict-attach-pods --namespace <namespace-name> --details
```

## Remediation

To fix violations:
1. Review if pod attach permissions are necessary
2. Limit attach permissions to specific pods or namespaces
3. Consider implementing logging and auditing for attach actions
4. Use alternative approaches for debugging and container access

## Example

A violation would look like:
```
ClusterRoleBinding example-binding with ServiceAccount example-sa is using role example-role with pod attach permissions
```

## Security Best Practices

1. **Minimize Attach Permissions**:
   - Grant attach permissions only when necessary
   - Use specific pod selectors when possible
   - Implement time-bound access through temporary credentials

2. **Regular Auditing**:
   - Review attach permissions regularly
   - Monitor and log all attach activities
   - Remove unnecessary permissions

3. **Alternative Approaches**:
   - Use kubectl logs for debugging
   - Implement proper logging mechanisms
   - Use sidecars for monitoring and debugging
   - Implement robust logging infrastructure

## Relationship to Other Policies

This policy complements other pod access restriction policies:
- **restrict-malicious-exec-pods**: Restricts command execution in pods
- **restrict-pod-access**: Restricts overall pod access
- **restrict-container-access**: Restricts direct container access

Together, these policies provide comprehensive protection against unauthorized container access.

## Contributing

Feel free to submit issues and enhancement requests! 