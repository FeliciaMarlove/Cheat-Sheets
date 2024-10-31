# Kubernetes cheat sheet

<!-- TOC start (generated with https://github.com/derlin/bitdowntoc) -->

* [Kubectl](#kubectl)
    + [Context](#context)
        - [List all contexts](#list-all-contexts)
        - [Show the current context](#show-the-current-context)
        - [Set the current context to my-cluster-name ](#set-the-current-context-to-my-cluster-name)
        - [Rename a context](#rename-a-context)
    + [Namespace](#namespace)
        - [List all namespaces in cluster](#list-all-namespaces-in-cluster)
        - [Create a namespace in current context](#create-a-namespace-in-current-context)
        - [Change namespace](#change-namespace)
        - [List events in a namespace](#list-events-in-a-namespace)
        - [List all services in the namespace](#list-all-services-in-the-namespace)
        - [List all pods in all namespaces](#list-all-pods-in-all-namespaces)
        - [List all pods in the current namespace, with more details](#list-all-pods-in-the-current-namespace-with-more-details)
    + [Deployment](#deployment)
        - [Describe a deployment](#describe-a-deployment-name-namespace-labels-annotations-selector-replicas-strategy-pod-template-events)
        - [Show information about a deployment](#show-information-about-a-deployment-name-ready-state-up-to-date-state-available-state-age)
    + [Pod and node](#pod-and-node)
        - [List all pods in the current namespace](#list-all-pods-in-the-current-namespace)
        - [Get a pod's YAML definition](#get-a-pods-yaml-definition)
        - [Describe commands with verbose output](#describe-commands-with-verbose-output)
        - [Connect to the terminal of a running pod](#connect-to-the-terminal-of-a-running-pod)
        - [See pod logs](#see-pod-logs)
        - [Save pods logs to a file](#save-pods-logs-to-a-file)
* [helm](#helm)
    + [List all deployments](#list-all-deployments)
    + [Dry run (simulate installation process) with detailed debug output](#dry-run-simulate-installation-process-with-detailed-debug-output)
* [Relationship: Container (Docker) - Orchestrator (K8s) - Package manager (helm)](#relationship-container-docker-orchestrator-k8s-package-manager-helm)
* [Entry points](#entry-points)

<!-- TOC end -->

## <span style="background:darkcyan">Kubectl</span>

### <span style="background:blue">Context</span>

| 📝 | context = group of access parameters (cluster, user, namespace...) |
|----|:-------------------------------------------------------------------|

#### List all contexts

`kubectl config get-contexts`

#### Show the current context

`kubectl config current-context`

#### Set the current context to my-cluster-name 

`kubectl config use-context my-cluster-name`

#### Rename a context

`kubectl config rename-context old-name new-name`

### <span style="background:blue">Namespace</span>

#### List all namespaces in cluster

`kubectl get ns`

#### Create a namespace in current context

`kubectl create namespace my_namespace`

#### Change namespace

`kubectl config set-context --current --namespace my-namespace`

#### List events in a namespace

`kubectl get event –namespace my-namespace`

#### List all services in the namespace

`kubectl get services`

#### List all pods in all namespaces

`kubectl get pods --all-namespaces`

#### List all pods in the current namespace, with more details

`kubectl get pods -o wide`

### <span style="background:blue">Deployment</span>

#### Describe a deployment (name, namespace, labels, annotations, selector, replicas, strategy, pod template, events)

`kubectl describe deployment`

#### Show information about a deployment (name, ready state, up-to-date state, available state, age)
 
`kubectl get deployment my-deployment`

### <span style="background:blue">Pod and node</span>

#### List all pods in the current namespace

`kubectl get pods`

#### Get a pod's YAML definition

`kubectl get pod my-pod -o yaml`       

#### Describe commands with verbose output

`kubectl describe nodes my-node`

`kubectl describe pods my-pod`

#### Connect to the terminal of a running pod

`kubectl exec --stdin --tty pod_name -- /bin/bash`

#### See pod logs

`kubectl logs pod_name`

#### Save pods logs to a file

`kubectl logs pod_name > app.log`

| 📝 Open output logs and don't show lines with "abc" `grep -v "abc" app.log` |
|-----------------------------------------------------------------------------|
| 📝 Open output logs and only show lines with "abc" `grep "abc" app.log`     |
| 📝 Make it case insensitive with the parameter `-i`                         |

## <span style="background:darkcyan">helm</span>

### List all deployments

`helm list`

### Dry run (simulate installation process) with detailed debug output

`helm install release_name ./chart_path --dry-run --debug`

## <span style="background:darkcyan">Relationship</span>: Container (Docker) - Orchestrator (K8s) - Package manager (helm)

K8s Cluster (= Kubernetes environment)

---K8s namespace (= logical container for resources)

-------helm release (= instance of a helm chart running in a K8s namespace)

-----------K8s deployment (= set of identical pods)

----------------k8s pod (= smallest deployable unit in Kubernetes)
 
-------------------Docker container 

K8s node (= server that runs the Kubernetes runtime and hosts the pods)

## <span style="background:darkcyan">Entry points</span>

* Service: Provides a single IP for the cluster. The cluster IP is only visible inside the cluster.
* Load balancer: Exposes an external IP to outside the cluster and routes the traffic to the service. Lives outside the cluster (usually provided by a Cloud provider).
* Ingress controller (service mesh): Load balancer that can handle several services (=> several IP addresses available for the cluster.). Lives inside the cluster.

![image](./assets/kubernetes_service.png)