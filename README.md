# Deploy Helm Charts

This repository contains actions for deploying Helm charts in AWS EKS using Bitovi's commons GitHub Actions (GHA) and BitOps framework. The main action utilized in this process is `github-actions-deploy-eks`, which is located in the action repository.

## Workflow Overview

This GitHub Action workflow first creates a Kubernetes cluster by setting the input `aws_eks_create` to `true`. The output from this step is then used as input for subsequent actions. Finally, if Helm is specified, it deploys the specified Helm chart.

## Helm Chart Specifications

Users have three locations where Helm chart specifications can be provided:

1. **Deployment Level**:
    - Users can add their own Helm charts in the `operations/deployment/helm` directory. If adding to a custom directory, the path of the directory should be added to the GHA input `input_helm_charts`, and changes should be committed.
    - For default charts like Grafana, Prometheus, Loki, Ingress Nginx, charts already exist in the action repository. Users can customize the values by adding a `values.yaml` file under the respective chart directory. For example, to customize Grafana configuration, users can add `values.yaml` under `operations/deployment/helm/grafana`.

2. **Action Level** (using BitOps):
    - The action repository `github-actions-deploy-eks` contains toggles for Grafana, Prometheus, and Loki via boolean input parameters (`grafana_enable`, `prometheus_enable`, `loki_enable`). Users can enable or disable these charts as needed. Additional charts can be added at this level.

## Destroy Stack

To destroy the entire stack, the GitHub Action can be run with the input `tf_stack_destroy` set to `true`.

## Example Usage

```yaml
name: Basic deploy
on:
  push:
    branches: [ bitops-conditional-helm ]

jobs:
  Deploy-Cluster:
    runs-on: ubuntu-latest

    steps:
    - name: Create EKS Cluster
      uses: bitovi/github-actions-deploy-eks@bitops-conditional-helm
      with:
        aws_access_key_id: ${{ secrets.AWS_ACCESS_KEY_ID_SANDBOX}}
        aws_secret_access_key: ${{ secrets.AWS_SECRET_ACCESS_KEY_SANDBOX}}
        aws_default_region: us-east-1
        aws_eks_cluster_admin_role_arn: arn:aws:iam::755521597925:role/AWSReservedSSO_AdministratorAccess_402f22a297379e03
        aws_eks_create: true
        # input_helm_charts:
        # tf_stack_destroy: true
        tf_state_bucket_destroy: true
        # prometheus_enable: true
        grafana_enable: true
        # loki_enable:
```

This workflow sets up a basic deployment where an EKS cluster is created, and Grafana is enabled for monitoring. Adjust the input parameters as needed for your specific deployment requirements.