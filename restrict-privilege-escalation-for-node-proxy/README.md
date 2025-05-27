# Restrict Privilege Escalation for Node Proxy

This policy monitors and restricts access to the Kubernetes Node Proxy API to prevent potential privilege escalation attacks.

## Policy Description

The policy consists of two parts:

1. **ClusterRole Check** (`restrict-privilege-escalation-for-node-proxy-clusterrole.yaml`):
   - Monitors ClusterRoles for permissions to access the Node Proxy API
   - Identifies ClusterRoleBindings using these roles
   - Reports subjects (ServiceAccounts/Users/Groups) with potential privilege escalation paths

2. **Role Check** (`restrict-privilege-escalation-for-node-proxy-role.yaml`):
   - Monitors namespace-scoped Roles for permissions to access the Node Proxy API
   - Identifies RoleBindings using these roles
   - Reports subjects with potential privilege escalation paths

## Why This Policy?

The Kubernetes Node Proxy API can be used for privilege escalation if not properly restricted. This policy helps:

- Prevent potential privilege escalation paths
- Identify subjects with Node Proxy access
- Maintain security best practices
- Comply with security standards

## Security Risk

Access to the Node Proxy API (`/api/v1/nodes/{name}/proxy`) can allow attackers to:
- Execute commands on nodes
- Access the kubelet API
- Potentially gain cluster-admin privileges
- Access sensitive information on nodes

## Policy Details

### Validation Rules

The policy checks for:
- Permissions to access the Node Proxy API
- Associated RoleBindings/ClusterRoleBindings
- Subjects with Node Proxy access permissions

### Message Format

When violations are found, the policy reports:
- The Role/ClusterRole name
- Associated RoleBinding/ClusterRoleBinding
- Subject type (ServiceAccount/User/Group)
- Subject name

## Usage

1. Apply the policies to your cluster:
   ```bash
   kubectl apply -f restrict-privilege-escalation-for-node-proxy-clusterrole.yaml
   kubectl apply -f restrict-privilege-escalation-for-node-proxy-role.yaml
   ```

2. The policy runs in audit mode by default
3. Review the policy violations in your cluster

## Requirements

- Kubernetes version: 1.28 or higher
- Kyverno version: 1.12.0 or higher

## Auditing with NCTL

You can audit your cluster for this policy using NCTL with the following command:

```bash
nctl scan kubernetes --policies restrict-privilege-escalation-for-node-proxy --cluster --details
```

This command will:
- Scan your entire Kubernetes cluster
- Check specifically for roles and clusterroles with Node Proxy API access
- Provide detailed information about any violations found
- Show which subjects have permissions that could lead to privilege escalation

The scan results will help you identify:
- Which roles and clusterroles grant Node Proxy access permissions
- Which bindings grant access to these roles
- Which subjects (users, groups, service accounts) have these permissions
- The scope of the permissions (cluster-wide or namespace-specific)

You can also scan specific namespaces to narrow down the results:

```bash
nctl scan kubernetes --policies restrict-privilege-escalation-for-node-proxy --namespace <namespace-name> --details
```

## Remediation

To fix violations:
1. Remove unnecessary Node Proxy access permissions
2. Use more specific permissions if required
3. Consider using the Kubernetes API server as a proxy instead
4. Implement proper node security controls

## Example

A violation would look like:
```
ClusterRoleBinding example-binding with ServiceAccount example-sa is using role example-role with Node Proxy access permissions
```

## Security Best Practices

1. **Minimize Node Access**:
   - Restrict direct node access
   - Use controlled deployment mechanisms
   - Implement proper node security

2. **Regular Auditing**:
   - Review node access permissions regularly
   - Remove unnecessary permissions
   - Monitor for suspicious node access patterns

3. **Alternative Approaches**:
   - Use the Kubernetes API for required operations
   - Implement proper pod security policies
   - Consider using admission controllers for additional restrictions

## Related CVEs

This policy helps protect against vulnerabilities similar to:
- CVE-2018-1002105 (Kubernetes privilege escalation via node/proxy subresources)
- CVE-2020-8559 (Privilege escalation from compromised node to cluster)

## Contributing

Feel free to submit issues and enhancement requests! 