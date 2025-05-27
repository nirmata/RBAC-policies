# RBAC Policies for Kubernetes Security

![RBAC Security](https://img.shields.io/badge/Kubernetes-RBAC%20Security-blue)
![Kyverno](https://img.shields.io/badge/Policy%20Engine-Kyverno-green)

A comprehensive collection of Kubernetes Role-Based Access Control (RBAC) policies designed to enhance cluster security, detect privilege escalation paths, and enforce security best practices.

## Overview

The RBAC Policies project provides a set of policy rules to monitor, audit, and restrict risky RBAC configurations in Kubernetes clusters. These policies help security teams and cluster administrators identify and mitigate security risks related to excessive permissions, privilege escalation vectors, and risky access patterns.

## Why RBAC Policies?

Kubernetes RBAC is powerful but complex. Misconfigurations can lead to:

- Privilege escalation
- Unauthorized access to sensitive resources
- Security breaches
- Compliance violations

This project helps you:

- **Detect:** Find overly permissive roles and bindings
- **Monitor:** Track who has sensitive permissions
- **Enforce:** Implement guardrails around RBAC
- **Audit:** Regularly review RBAC security posture

## Policies Included

### Critical Risk Policies

| Policy | Description | 
|--------|-------------|
| [restrict-wildcard-verbs](./restrict-wildcard-verbs/) | Detects and restricts roles with wildcard verbs (`*`) |
| [restrict-wildcard-resources](./restrict-wildcard-resources/) | Detects and restricts roles with wildcard resources (`*`) |
| [restrict-secrets-access](./restrict-secrets-access/) | Monitors roles with access to Kubernetes Secrets |
| [restrict-impersonating-groups](./restrict-impersonating-groups/) | Restricts permissions to impersonate groups |
| [restrict-privilege-escalation-for-node-proxy](./restrict-privilege-escalation-for-node-proxy/) | Prevents node proxy privilege escalation paths |

### High Risk Policies

| Policy | Description |
|--------|-------------|
| [restrict-rolebinding-mapping](./restrict-rolebinding-mapping/) | Monitors improper role binding mappings |
| [restrict-create-malicious-resource](./restrict-create-malicious-resource/) | Restricts permissions to create potentially malicious resources |
| [restrict-create-binding-privilege-role](./restrict-create-binding-privilege-role/) | Prevents creation of bindings to privileged roles |

### Container Access Policies

| Policy | Description |
|--------|-------------|
| [restrict-attach-pods](./restrict-attach-pods/) | Restricts permissions to attach to pods |
| [restrict-malicious-exec-pods](./restrict-malicious-exec-pods/) | Restricts permissions to execute commands in pods |

## Installation

### Prerequisites

- Kubernetes cluster (v1.28+)
- Kyverno (v1.12.0+)
- kubectl

### Deploying All Policies

```bash
# Clone this repository
git clone https://github.com/yourusername/RBAC-policies.git
cd RBAC-policies

# Apply all policies
kubectl apply -f .
```

### Deploying Individual Policies

```bash
# Apply a specific policy
kubectl apply -f restrict-wildcard-verbs/
```

## Usage

### Auditing RBAC with NCTL

The project includes built-in support for auditing using NCTL (Kubernetes RBAC Security Scanner).

To scan your entire cluster for all policies:

```bash
nctl scan kubernetes --policies all --cluster --details
```

To scan for a specific policy:

```bash
nctl scan kubernetes --policies restrict-wildcard-verbs --cluster --details
```

Narrow down to a specific namespace:

```bash
nctl scan kubernetes --policies restrict-secrets-access --namespace production --details
```

### Execution Time Tools

The repository includes utility scripts to help with scanning:

- **nctl-execution-time-per-ns.sh**: Measures NCTL scan execution time per namespace
- **nctl-scan-namespace.sh**: Interactive script to scan selected namespaces

## Policy Development Approach

Each policy in this project follows a consistent pattern:

1. **Detection**: Identifies risky RBAC configurations
2. **Validation**: Checks against security best practices
3. **Reporting**: Provides clear, actionable information about violations
4. **Remediation**: Offers guidance on fixing issues

## Security Best Practices

This project enforces several RBAC security best practices:

- **Principle of Least Privilege**: Ensure roles have only necessary permissions
- **No Wildcard Permissions**: Avoid wildcard verbs and resources
- **Restrict Sensitive Operations**: Limit who can perform sensitive actions
- **Regular Auditing**: Continuously scan for violations

## Example Violations

### Wildcard Verbs

```yaml
# Violation: Role with wildcard verb
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: example-wildcard-role
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["*"]  # VIOLATION: Wildcard verb
```

### Secrets Access

```yaml
# Violation: Excessive secrets access
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: secrets-reader
rules:
- apiGroups: [""]
  resources: ["secrets"]
  verbs: ["get", "list", "watch"]  # VIOLATION: Broad secrets access
```

## Integration with Security Tools

These policies work well with:

- **[KubiScan](https://github.com/cyberark/KubiScan)**: For deeper RBAC privilege escalation analysis
- **[Kyverno](https://kyverno.io/)**: As the policy enforcement engine
- **[OPA/Gatekeeper](https://github.com/open-policy-agent/gatekeeper)**: For additional policy enforcement
- **Kubernetes Admission Controllers**: For blocking problematic configurations

## Contributing

We welcome contributions! Please:

1. Fork the repository
2. Create a feature branch
3. Submit a pull request

For new policies, please follow the existing structure and include:
- Policy YAML files
- Comprehensive README
- Examples of violations
- Remediation guidelines

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Acknowledgments

- Inspired by the [CyberArk KubiScan](https://github.com/cyberark/KubiScan) project
- Kubernetes SIG Security for best practices
- Cloud Native Security community

## References

- [Kubernetes RBAC Documentation](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)
- [CIS Kubernetes Benchmark](https://www.cisecurity.org/benchmark/kubernetes)
- [MITRE ATT&CK for Containers](https://attack.mitre.org/matrices/enterprise/containers/)
- [KubiScan by CyberArk](https://github.com/cyberark/KubiScan)
