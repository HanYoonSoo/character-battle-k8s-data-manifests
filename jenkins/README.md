# Jenkins Harness

This directory keeps Jenkins configuration in code so the repository-level harness stays reviewable.

Contents:

- `jcasc.yaml`: controller baseline managed by Configuration as Code
- `kustomization.yaml`: local Kubernetes bootstrap entrypoint
- `plugins.txt`: minimal plugin set for the bootstrap controller

Expected pipeline entrypoint:

- root `Jenkinsfile`

Recommended environment variables for agent nodes:

- `PYTHONPATH=src`
- `HARNESS_REPAIR_COMMAND` for an optional external coding agent command

The `repair` job is intended for deterministic auto-fix loops.
The `hygiene` job is intended for scheduled repository drift scans.
The `promote` job is intended for turning repeated failures into candidate rules.

Bootstrap the local Jenkins controller with:

```bash
# Edit infra/jenkins/jenkins-secret.example.yaml first.
kubectl apply -k infra/jenkins
```

Access the UI locally with:

```bash
kubectl -n jenkins get svc jenkins
```

Bootstrap note:

- the controller starts with one built-in executor so the repository pipeline can run before Kubernetes agent pod templates are configured
- once dedicated agent pods are configured, lower `JENKINS_CONTROLLER_EXECUTORS` back to `0`
- the Jenkins UI is exposed through `NodePort 30081`
- keep the JNLP/agent service internal as `ClusterIP`
