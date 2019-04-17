# TK8 addon - GoCD

## What are TK8 addons?

- TK8 add-ons provide freedom of choice for the user to deploy tools and applications without being tied to any customized formats of deployment.
- Simplified deployment process via CLI (will also be available via TK8 web in future).
- With the TK8 add-ons platform, you can also build your own add-ons.

To get more support join us on [Slack](https://kubernauts-slack-join.herokuapp.com)

### Prerequisites

This addon was created for the tk8 cli you could find it here: https://github.com/kubernauts/tk8
Addon integration is supported on Version 0.5.0 and greater

Alternative you can apply the main.yml directly with kubectl

RBAC must be enabled on the Kubernetes Cluster.

## What is GoCD?

GoCD is an [open-source](https://en.wikipedia.org/wiki/Open-source_software) tool which is used in software development to help teams and organizations automate the [continuous delivery (CD)](https://en.wikipedia.org/wiki/Continuous_delivery) of software. It supports automating the entire build-test-release process from code check-in to deployment. It helps to keep producing valuable software in short cycles and ensure that the software can be reliably released at any time. It supports several [version control](https://en.wikipedia.org/wiki/Version_control) tools including [Git](https://en.wikipedia.org/wiki/Git_(software)), [Mercurial](https://en.wikipedia.org/wiki/Mercurial), [Subversion](https://en.wikipedia.org/wiki/Subversion_(software)), [Perforce](https://en.wikipedia.org/wiki/Perforce) and [Team Foundation Server](https://en.wikipedia.org/wiki/Team_Foundation_Server). Other version control software can be supported by installing additional plugins. GoCD is released under the [Apache 2 License](https://en.wikipedia.org/wiki/Apache_License).

## Get started

You can install GoCD on the Kubernetes cluster via TK8 addons functionality. What do you need:
- tk8 binary
- A Kubernetes cluster that supports Service objects of type: LoadBalancer

## Deploy GoCD on your Kubernetes Cluster

Run **tk8 addon install gocd**

    $ tk8 addon install gocd
    Search local for gocd
    Addon gocd already exist
    Found gocd local.
    Install gocd
    execute main.sh
    Creating main.yaml
    add  ./gocd-config/00-gocd-configmap.yaml
    add  ./gocd-config/01-gocd-server-pvc.yaml
    add  ./gocd-config/02-gocd-ea-serviceaccount.yaml
    add  ./gocd-config/03-gocd-ea-clusterrole.yaml
    add  ./gocd-config/04-gocd-ea-crb.yaml
    add  ./gocd-config/05-gocd-svc.yaml
    add  ./gocd-config/06-gocd-agent-deploy.yaml
    add  ./gocd-config/07-gocd-server-deploy.yaml
    add  ./gocd-config/08-gocd-ingress.yaml
    apply gocd/main.yml
    configmap/gocd-cm created
    persistentvolumeclaim/gocd-server-pvc created
    serviceaccount/gocd-sa created
    clusterrole.rbac.authorization.k8s.io/gocd-cr created
    clusterrolebinding.rbac.authorization.k8s.io/gocd-crb created
    service/gocd-server created
    deployment.apps/gocd-agent-deploy created
    deployment.apps/gocd-server-deploy created
    ingress.extensions/gocd-server-ing created
    gocd installation complete
    
This command clones the https://github.com/kubernauts/tk8-addon-gocd repository locally and setups GoCD. This command also creates:
- RBAC permissions necessary for GoCD
- Deploys a GoCD server and agent
- An ingress for accessing GoCD UI

Verify if everything is running with **kubectl get all -l app=gocd**
    
    $ kubectl get all -l app=gocd
    NAME                                      READY   STATUS    RESTARTS   AGE
    pod/gocd-server-deploy-5b7954dd7d-gslzq   1/1     Running   0          3m40s
     
    NAME                  TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)                         AGE
    service/gocd-server   NodePort   10.43.200.88   <none>        8153:30163/TCP,8154:31055/TCP   3m43s
     
    NAME                                 READY   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/gocd-agent-deploy    0/0     0            0           3m43s
    deployment.apps/gocd-server-deploy   1/1     1            1           3m42s
     
    NAME                                            DESIRED   CURRENT   READY   AGE
    replicaset.apps/gocd-agent-deploy-57b5c57f5d    0         0         0       3m43s
    replicaset.apps/gocd-server-deploy-5b7954dd7d   1         1         1       3m42s
     
    $ kubectl get ing gocd-server-ing
    NAME              HOSTS   ADDRESS                                                                      PORTS   AGE
    gocd-server-ing   *       84.200.100.197,84.200.100.199,84.200.100.201,84.200.100.203,84.200.100.205   80      5m9s

## Accessing GoCD

Access the GoCD UI by visiting the IP addresses/hostname mentioned in the gocd-server-ing ingress. Now, you can try running a sample hello_world pipeline. The results should look something like this:

![image](https://user-images.githubusercontent.com/38726510/56271113-fbd8f880-60f7-11e9-9284-36e9778285d9.png)

## Uninstall GoCD

For removing GoCD from your cluster, we can use TK8 addon's destroy functionality. Run **tk8 addon destroy gocd**
    
    $ tk8 addon destroy gocd
    Search local for gocd
    Addon gocd already exist
    Found gocd local.
    Destroying gocd
    execute main.sh
    Creating main.yaml
    add  ./gocd-config/00-gocd-configmap.yaml
    add  ./gocd-config/01-gocd-server-pvc.yaml
    add  ./gocd-config/02-gocd-ea-serviceaccount.yaml
    add  ./gocd-config/03-gocd-ea-clusterrole.yaml
    add  ./gocd-config/04-gocd-ea-crb.yaml
    add  ./gocd-config/05-gocd-svc.yaml
    add  ./gocd-config/06-gocd-agent-deploy.yaml
    add  ./gocd-config/07-gocd-server-deploy.yaml
    add  ./gocd-config/08-gocd-ingress.yaml
    delete gocd from cluster
    configmap "gocd-cm" deleted
    persistentvolumeclaim "gocd-server-pvc" deleted
    serviceaccount "gocd-sa" deleted
    clusterrole.rbac.authorization.k8s.io "gocd-cr" deleted
    clusterrolebinding.rbac.authorization.k8s.io "gocd-crb" deleted
    service "gocd-server" deleted
    deployment.apps "gocd-agent-deploy" deleted
    deployment.apps "gocd-server-deploy" deleted
    ingress.extensions "gocd-server-ing" deleted
    gocd destroy complete
