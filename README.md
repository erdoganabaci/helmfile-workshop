# helmfile-workshop
This is a repository used for a helmfile workshop

# Required knowledge

* Basic Kubernetes knowledge (CKA)
* Basic container knowledge (docker)
* Basic CI/CD knowledge
* Basic git knowledge

# Workshop

## Preparation

Install minikube

https://kubernetes.io/docs/tasks/tools/install-minikube/

Install helm (version 3)

https://helm.sh/docs/intro/install/

Install helmfile

https://github.com/roboll/helmfile#installation

Install kubectl

https://kubernetes.io/docs/tasks/tools/install-kubectl/

## Engineer

### Part 1: Helm

Install wordpress with defaults.

First add the bitnami helm repo:

`helm repo add bitnami https://charts.bitnami.com/bitnami`

Then create the namespace for the workshop:

`kubectl create ns workshop`

Finally install wordpress:

`helm install workshop bitnami/wordpress -n workshop`

Fetch the wordpress default login credentials with:

`kubectl get secret --namespace workshop workshop-wordpress -o jsonpath="{.data.wordpress-password}" | base64 --decode`

Try to connect to wordpress via `kubectl port-forward svc/workshop-wordpress 1337:80 -n workshop` with username `user` and the password you just fetched from the secret.

Congrats! You have installed Wordpress on Kubernetes!

With `kubectl get pods -n workshop` observe which components have been installed.

Uninstall wordpress again using `helm ls` and `helm uninstall` and delete all created PVC's with `kubectl delete pvc --all -n workshop`.

### Part 2: Helmfile

Install the `helm diff` plugin: `helm plugin install https://github.com/databus23/helm-diff`

So we mature the way we work, we want to run wordpress on a separate `mysql` instance (because corporate told us to) instead of the built-in `mariadb` instance and we need to develop a "production grade" deployment workflow.

Explore https://github.com/roboll/helmfile and see if you can setup this up with `helmfile` as a tool and a `test`, `staging` and `production` equivalant with different versions installed of wordpress.

Use the [values.yaml](https://github.com/bitnami/charts/blob/master/bitnami/wordpress/values.yaml) from the helm chart source as reference.

You can use `helmfile.yaml` in this repository as a starting point and example.

### Extra: CI/CD your deployments

Think of an outline what your git strategy would be how you would apply changes to the configuration automatically with a CI/CD pipeline.

* What would happen if a branch is created from `master` and code being pushed to that branch?
* When would you apply to `test`?
* When to `staging`?
* And when and how to `production`?

You could try to implement this with [git hooks](https://githooks.com/#:~:text=Git%20hooks%20are%20scripts%20that,Git%20hooks%20are%20run%20locally) although the important part is to know how to conceptually solve this.

### Helm & Helmfile hero assignment: Implement Helm Secrets for managing secrets inside value files.

https://github.com/zendesk/helm-secrets

### Extra: commands
```

wget https://github.com/roboll/helmfile/releases/download/v0.131.0/helmfile_linux_amd64

mv helmfile_linux_amd64 helmfile

chmod +x helmfile

./helmfile diff

```
