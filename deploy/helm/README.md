# Helm deployment variants

This repository keeps two cloud-specific Helm charts because the original deployments use different
Kubernetes ingress and database patterns:

- `aws/` — EKS + AWS Load Balancer Controller/ALB; application database is AWS RDS MariaDB.
- `azure/` — AKS + Azure application routing; the supplied deployment uses MariaDB inside Kubernetes.

The application containers are the same codebase. Cloud-specific values/manifests are isolated so the
same release can be promoted to either cloud without duplicating application source code.
