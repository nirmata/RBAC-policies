# Restrict Creation of Malicious Resources

This policy monitors and restricts permissions to create potentially malicious resources in Kubernetes clusters, which could be used for privilege escalation or attack path creation.

## Policy Description

The policy consists of two parts:

1. **ClusterRole Check** (`restrict-create-malicious-resource-clusterrole.yaml`):
   - Monitors ClusterRoles for permissions to create potentially dangerous resources
   - Identifies ClusterRoleBindings using these roles
   - Reports subjects (ServiceAccounts/Users/Groups) with permissions to create malicious resources

2. **Role Check** (`restrict-create-malicious-resource-role.yaml`):
   - Monitors namespace-scoped Roles for permissions to create potentially dangerous resources
   - Identifies RoleBindings using these roles
   - Reports subjects with permissions to create malicious resources

## Why This Policy?

The ability to create certain Kubernetes resources can be misused for privilege escalation and security compromises. This policy helps:

- Prevent unauthorized creation of high-risk resources
- Identify subjects with excessive creation permissions
- Maintain security best practices
- Comply with security standards

## Security Risk

Unrestricted resource creation permissions can allow attackers to:
- Create pods with dangerous configurations (hostPath volumes, privileged containers)
- Create roles or clusterroles that grant excessive permissions
- Deploy resources that can access node components
- Establish persistence in the cluster
- Create network policies or services that expose internal systems

## Policy Details

### Validation Rules

The policy checks for:
- Permissions to create sensitive resources (pods, deployments, daemonsets, etc.)
- Associated RoleBindings/ClusterRoleBindings
- Subjects with permissions to create these resources

### Message Format

When violations are found, the policy reports:
- The Role/ClusterRole name
- Associated RoleBinding/ClusterRoleBinding
- Subject type (ServiceAccount/User/Group)
- Subject name

## Usage

1. Apply the policies to your cluster:
   ```bash
   kubectl apply -f restrict-create-malicious-resource-clusterrole.yaml
   kubectl apply -f restrict-create-malicious-resource-role.yaml
   ```

2. The policy runs in audit mode by default
3. Review the policy violations in your cluster

## Requirements

- Kubernetes version: 1.28 or higher
- Kyverno version: 1.12.0 or higher

## Remediation

To fix violations:
1. Review if resource creation permissions are necessary
2. Limit creation permissions to specific resources and namespaces
3. Implement Pod Security Standards or Admission Controllers
4. Use OPA/Gatekeeper or Kyverno to enforce security policies at admission time

## Auditing with NCTL

You can audit your cluster for this policy using NCTL with the following command:

```bash
nctl scan kubernetes --policies restrict-create-malicious-resource --cluster --details
```

This command will:
- Scan your entire Kubernetes cluster
- Check specifically for this policy
- Provide detailed information about any violations found
- Show which subjects have permissions to create potentially malicious resources

The scan results will help you identify:
- Which roles and bindings grant dangerous permissions
- Which subjects (users, groups, service accounts) have these permissions
- The scope of the permissions (cluster-wide or namespace-specific)
- Potential remediation steps

## Example

A violation would look like:
```
ClusterRoleBinding example-binding with ServiceAccount example-sa is using role example-role with permissions to create potentially malicious resources
```

## Security Best Practices

1. **Minimize Creation Permissions**:
   - Grant resource creation permissions only when necessary
   - Use specific resource types instead of wildcards
   - Implement time-bound access through temporary credentials

2. **Layer Your Defenses**:
   - Use RBAC restrictions as the first line of defense
   - Implement admission controllers as a second layer
   - Deploy runtime security monitoring as a third layer

3. **High-Risk Resources to Monitor**:
   - Pods and workloads (Deployments, DaemonSets, StatefulSets)
   - RBAC resources (Roles, ClusterRoles, Bindings)
   - Privileged resources (PodSecurityPolicies, SecurityContexts)
   - Networking resources (Services, NetworkPolicies)
   - Storage resources with host access (PersistentVolumes with hostPath)

## Relationship to Other Security Controls

This policy works best when combined with:
- Pod Security Standards/Policies
- Network Policies
- Admission Controllers
- OPA/Gatekeeper constraints
- Runtime security monitoring

## Contributing

Feel free to submit issues and enhancement requests! 