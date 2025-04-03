# OpenShift. Install and configure Node Health Check Operator

## 📖 Description

OpenShift nodes may become unhealthy at any given time, the reasons leading to a node failure may vary.  
When a node fails, the workloads running on affected node must be rescheduled.  
These workloads might require at-most-one semantics which means no split-brain scenatio is allowed in the cluster.  
  
This repository provides an Ansible playbook and OpenShift manifests for deploying `Node Health Check` Operator with `Self Node Remediation` Operator on an OpenShift cluster.  
`Self Node Remediation` Operator is recommended for nodes that do not have a management interface (like iLO or iDRAC), or the interface might be unreachable, since SNR does not require one to work.  
SNR Operator can be used in any environment (physical or virtual nodes).  

---

## 📁 Repo Structure

```
.
├── configure-nhc.yaml # Ansible playbook to apply the manifests
├── nhc-template.yml.j2 # NodeHealthCheck CR definition
├── node-health-check-namespace.yml.j2 # Namespace for Node Health Check Operator
├── node-health-check-subscription.yml.j2 # Subscription for Node Health Check Operator
├── Readme.md # Readme file
├── snr-subscription.yml.j2 # Subscription for Self Node Remediation Operator
├── vars.yaml # File with variables
└── workload-availability-operator-group.yml.j2 # Operator Group for NHC and SNR Operators
```

---

## 🚀 How to Apply

You can install and configure NHC using Ansible

```bash
# Login to the cluster
oc login --token=sha256~XXXXXXXXXXXXXXXXXXX --server=https://api.xxx.yyy.zzz.com:6443

# Clone the repository
git clone https://github.com/ishushkin/ocp-virt-conf-nhc
cd ocp-virt-conf-nhc

# Apply to cluster using Ansible
ansible-playbook configure-nhc.yaml
```
---

## 👀 How to Verify Installation

1. Log in to the OpenShift with oc login command
2. Verify that the installation succeeded by inspecting the CSV resource:
   ```bash
   oc get csv -n openshift-workload-availability
   ```
3. Verify that the Node Health Check Operator is up and running:
   ```bash
   oc get deployment -n openshift-workload-availability
   ```

---

## 👀 How to verify operation

⚠️**Attention: This step can disrupt running applications and/or make them unavailable, use with caution.**  
In order to verify correct operation of the Operator, you need to make one of the worker nodes not reachable for other nodes in the cluster.
For example, it can be achieved by disabling network interface on a node.
After the timeout defined in the vars file (condition_duration var) the NHC operator will perform remediation and workloads from the faulty node should be rescheduled on the surviving nodes.

---

## Ansible Variables

The following table outlines the configurable variables used in the playbook's variables file "vars.yml":

| Variable         | Description                                 | Default Value       |
|------------------|---------------------------------------------|---------------------|
| `node_health_check_namespace`  | Namespace where to install Operators              | `openshift-workload-availability`         |
| `workload_availability_operator_group`          | Operator Group name                    | `workload-availability-operator-group`         |

These values can be changed by editing `vars.yml` file.

---

## ⚠️ Limitations

None.

---

## 🛠️ TODO

Add Option to use Fence Agents Remediation (FAR) Operator

---

## 📦 Prerequisites

- OpenShift 4.x with access to the OpenShift Console
- Ansible

---

## ✍️ Contribution Guidelines

Contributions, issues, and feature requests are welcome. Please fork the repository and submit a pull request following our branch naming conventions and review process.

---

## 📄 License

This repository is licensed under the [MIT License](LICENSE), unless otherwise noted.

---

## 👥 Maintainers

- Platform Team, Li9 DevOps Group
- Contacts: igor.shushkin@li9.com
