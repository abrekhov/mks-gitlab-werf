# mKS Gitlab Werf setup

## GitLab & mKS

### Enable kubernetes agent service (kas) in Gitlab config

```yaml
    environment:
        GITLAB_OMNIBUS_CONFIG: |
            ...
            gitlab_kas['enable'] = true
            ...
```

### Define Gitlab Agent config

```yaml
# cat .gitlab/agents/mks-agent
ci_access:
  groups:
  - id: <group_id>
  projects:
  - id: <project_id>
```

### Connect cluster

Inside Gitlab Project with .gitlab/agent folder go to
Infrastructure > Kubernetes Clusters > Connect a cluster.

Choose your agent.

Execute commands proposed by Gitlab.

[More info here](https://docs.gitlab.com/ee/user/clusters/agent/install/index.html)

### Setting up Werf

```bash
cd werf
kubectl -n kube-system apply -f werf-fuse-device-plugin-ds.yaml
kubectl create namespace gitlab-ci
kubectl apply -f enable-fuse-pod-limit-range.yaml
kubectl apply -f sa-runner.yaml
cd ..
```

[More info here](https://werf.io/documentation/v1.2/advanced/ci_cd/run_in_container/use_gitlab_ci_cd_with_kubernetes_executor.html)

### Install Gitlab Runner

```bash
cd gitlab-runner
helm repo add gitlab https://charts.gitlab.io 
vim values.yaml # set your domain and registry token
helm install --namespace gitlab-ci gitlab-runner -f values.yaml gitlab/gitlab-runner
```

[More info here](https://werf.io/documentation/v1.2/advanced/ci_cd/run_in_container/use_gitlab_ci_cd_with_kubernetes_executor.html)