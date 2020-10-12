---
title: Instalace existujících aplikací pomocí Helm v AKS
description: Naučte se používat nástroj pro vytváření balíčků Helm k nasazení kontejnerů v clusteru Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.topic: article
ms.date: 06/24/2020
ms.author: zarhoads
ms.openlocfilehash: d05d0166724e586fa79e58e2e74fb583b45d0cc6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88852876"
---
# <a name="install-existing-applications-with-helm-in-azure-kubernetes-service-aks"></a>Instalace stávajících aplikací pomocí Helm ve službě Azure Kubernetes (AKS)

[Helm][helm] je open source nástroj pro balení, který vám pomůže s instalací a správou životního cyklu aplikací Kubernetes. Podobně jako správci balíčků pro Linux, jako je *apt* a *Yumu*, se Helm používá ke správě Kubernetes grafů, což jsou balíčky předkonfigurovaných prostředků Kubernetes.

V tomto článku se dozvíte, jak nakonfigurovat a používat Helm v clusteru Kubernetes v AKS.

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, přečtěte si rychlý Start AKS a [použijte Azure CLI][aks-quickstart-cli] nebo [Azure Portal][aks-quickstart-portal].

Je také potřeba nainstalovat rozhraní příkazového řádku Helm, což je klient, který běží ve vašem vývojovém systému. Umožňuje spouštět, zastavovat a spravovat aplikace pomocí Helm. Pokud použijete Azure Cloud Shell, rozhraní příkazového řádku Helm je již nainstalováno. Pokyny k instalaci na místní platformě najdete v tématu [instalace Helm][helm-install].

> [!IMPORTANT]
> Helm je určený ke spuštění na uzlech systému Linux. Pokud máte v clusteru uzly Windows serveru, musíte zajistit, aby Helm lusky běžely jenom na uzlech se systémem Linux. Také je potřeba zajistit, aby všechny Helm grafy, které nainstalujete, běžely taky na správných uzlech. Příkazy v tomto článku používají [k8s-Node-Selector] k tomu, aby byly lusky naplánovány na správné uzly, ale ne všechny Helm grafy mohou vystavit selektor uzlů. Můžete také zvážit použití dalších možností v clusteru, například [chuti][taints].

## <a name="verify-your-version-of-helm"></a>Ověření verze Helm

Pomocí `helm version` příkazu ověřte, že máte nainstalovanou Helm 3:

```console
helm version
```

Následující příklad ukazuje nainstalovanou verzi Helm 3.0.0:

```console
$ helm version

version.BuildInfo{Version:"v3.0.0", GitCommit:"e29ce2a54e96cd02ccfce88bee4f58bb6e2a28b6", GitTreeState:"clean", GoVersion:"go1.13.4"}
```

## <a name="install-an-application-with-helm-v3"></a>Instalace aplikace pomocí Helm V3

### <a name="add-helm-repositories"></a>Přidat úložiště Helm

Pomocí příkazu [Helm úložiště][helm-repo-add] přidejte oficiální helmové grafy a úložiště *Nginx* pro příjem dat.

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
```

### <a name="find-helm-charts"></a>Hledání Helm grafů

Grafy Helm slouží k nasazování aplikací do clusteru Kubernetes. K vyhledání předem vytvořených grafů Helm použijte příkaz [Helm Search][helm-search] :

```console
helm search repo stable
```

Následující zhuštěný příklad výstupu ukazuje některé z Helm grafů dostupných pro použití:

```console
$ helm search repo stable

NAME                                    CHART VERSION   APP VERSION                     DESCRIPTION                                       
stable/acs-engine-autoscaler            2.2.2           2.1.1                           DEPRECATED Scales worker nodes within agent pools 
stable/aerospike                        0.3.1           v4.5.0.5                        A Helm chart for Aerospike in Kubernetes          
stable/airflow                          4.10.0          1.10.4                          Airflow is a platform to programmatically autho...
stable/ambassador                       4.4.7           0.85.0                          A Helm chart for Datawire Ambassador              
stable/anchore-engine                   1.3.7           0.5.2                           Anchore container analysis and policy evaluatio...
stable/apm-server                       2.1.5           7.0.0                           The server receives data from the Elastic APM a...
stable/ark                              4.2.2           0.10.2                          DEPRECATED A Helm chart for ark                   
stable/artifactory                      7.3.1           6.1.0                           DEPRECATED Universal Repository Manager support...
stable/artifactory-ha                   0.4.1           6.2.0                           DEPRECATED Universal Repository Manager support...
stable/atlantis                         3.8.4           v0.8.2                          A Helm chart for Atlantis https://www.runatlant...
stable/auditbeat                        1.1.0           6.7.0                           A lightweight shipper to audit the activities o...
stable/aws-cluster-autoscaler           0.3.3                                           Scales worker nodes within autoscaling groups.    
stable/aws-iam-authenticator            0.1.1           1.0                             A Helm chart for aws-iam-authenticator            
stable/bitcoind                         0.2.2           0.17.1                          Bitcoin is an innovative payment network and a ...
stable/bookstack                        1.1.2           0.27.4-1                        BookStack is a simple, self-hosted, easy-to-use...
stable/buildkite                        0.2.4           3                               DEPRECATED Agent for Buildkite                    
stable/burrow                           1.5.2           0.29.0                          Burrow is a permissionable smart contract machine 
stable/centrifugo                       3.1.0           2.1.0                           Centrifugo is a real-time messaging server.       
stable/cerebro                          1.3.1           0.8.5                           A Helm chart for Cerebro - a web admin tool tha...
stable/cert-manager                     v0.6.7          v0.6.2                          A Helm chart for cert-manager                     
stable/chaoskube                        3.1.2           0.14.0                          Chaoskube periodically kills random pods in you...
stable/chartmuseum                      2.4.0           0.8.2                           Host your own Helm Chart Repository               
stable/chronograf                       1.1.0           1.7.12                          Open-source web application written in Go and R...
stable/clamav                           1.0.4           1.6                             An Open-Source antivirus engine for detecting t...
stable/cloudserver                      1.0.3           8.1.5                           An open-source Node.js implementation of the Am...
stable/cluster-autoscaler               6.2.0           1.14.6                          Scales worker nodes within autoscaling groups.    
stable/cluster-overprovisioner          0.2.6           1.0                             Installs the a deployment that overprovisions t...
stable/cockroachdb                      2.1.16          19.1.5                          CockroachDB is a scalable, survivable, strongly...
stable/collabora-code                   1.0.5           4.0.3.1                         A Helm chart for Collabora Office - CODE-Edition  
stable/concourse                        8.2.7           5.6.0                           Concourse is a simple and scalable CI system.     
stable/consul                           3.9.2           1.5.3                           Highly available and distributed service discov...
stable/contour                          0.1.0           v0.15.0                         Contour Ingress controller for Kubernetes         
stable/coredns                          1.7.4           1.6.4                           CoreDNS is a DNS server that chains plugins and...
stable/cosbench                         1.0.1           0.0.6                           A benchmark tool for cloud object storage services
stable/coscale                          1.0.0           3.16.0                          CoScale Agent                                     
stable/couchbase-operator               1.0.1           1.2.1                           A Helm chart to deploy the Couchbase Autonomous...
stable/couchdb                          2.3.0           2.3.1                           DEPRECATED A database featuring seamless multi-...
stable/dask                             3.1.0           1.1.5                           Distributed computation in Python with task sch...
stable/dask-distributed                 2.0.2                                           DEPRECATED: Distributed computation in Python     
stable/datadog                          1.38.3          6.14.0                          DataDog Agent 
...
```

Chcete-li aktualizovat seznam grafů, použijte příkaz [Helm úložiště Update][helm-repo-update] .

```console
helm repo update
```

Následující příklad ukazuje úspěšnou aktualizaci úložiště:

```console
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "ingress-nginx" chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

### <a name="run-helm-charts"></a>Spuštění Helm grafů

Pokud chcete grafy nainstalovat pomocí Helm, použijte příkaz pro [instalaci Helm][helm-install-command] a zadejte název verze a název grafu, který chcete nainstalovat. Pokud chcete vidět, jak se v akci nainstaluje graf Helm, nainstalujte základní nasazení Nginx pomocí grafu Helm.

```console
helm install my-nginx-ingress ingress-nginx/ingress-nginx \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Následující zhuštěný příklad výstupu ukazuje stav nasazení prostředků Kubernetes vytvořených pomocí grafu Helm:

```console
$ helm install my-nginx-ingress ingress-nginx/ingress-nginx \
>     --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
>     --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux

NAME: my-nginx-ingress
LAST DEPLOYED: Fri Nov 22 10:08:06 2019
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The nginx-ingress controller has been installed.
It may take a few minutes for the LoadBalancer IP to be available.
You can watch the status by running 'kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller'
...
```

Pomocí `kubectl get services` příkazu Získejte *externí IP adresu* vaší služby.

```console
kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller
```

Například následující příkaz zobrazuje *externí IP adresu* pro službu *My-Nginx-příchozí-Nginx-Controller* :

```console
$ kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller

NAME                                        TYPE           CLUSTER-IP   EXTERNAL-IP      PORT(S)                      AGE   SELECTOR
my-nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.2.237   <EXTERNAL-IP>    80:31380/TCP,443:32239/TCP   72s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=my-nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

### <a name="list-releases"></a>Vypsat vydané verze

Pokud chcete zobrazit seznam verzí nainstalovaných v clusteru, použijte `helm list` příkaz.

```console
helm list
```

Následující příklad ukazuje verzi *My-Nginx-* nasazenou v předchozím kroku:

```console
$ helm list

NAME                NAMESPACE   REVISION    UPDATED                                 STATUS      CHART                   APP VERSION
my-nginx-ingress    default     1           2019-11-22 10:08:06.048477 -0600 CST    deployed    nginx-ingress-1.25.0    0.26.1 
```

### <a name="clean-up-resources"></a>Vyčištění prostředků

Když nasadíte graf Helm, vytvoří se několik prostředků Kubernetes. Mezi tyto prostředky patří lusky, nasazení a služby. K vyčištění těchto prostředků použijte příkaz pro [odinstalaci Helm][helm-cleanup] a zadejte název vydané verze, jak je uvedeno v předchozím `helm list` příkazu.

```console
helm uninstall my-nginx-ingress
```

Následující příklad ukazuje verzi s názvem *My-Nginx-* příchozí byla odinstalována:

```console
$ helm uninstall my-nginx-ingress

release "my-nginx-ingress" uninstalled
```

## <a name="next-steps"></a>Další kroky

Další informace o správě nasazení aplikací Kubernetes pomocí Helm najdete v dokumentaci k Helm.

> [!div class="nextstepaction"]
> [Dokumentace k nástroji Helm][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-cleanup]: https://helm.sh/docs/intro/using_helm/#helm-uninstall-uninstalling-a-release
[helm-documentation]: https://helm.sh/docs/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-command]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-repo-add]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helm-search]: https://helm.sh/docs/intro/using_helm/#helm-search-finding-charts
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
            
<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[taints]: operator-best-practices-advanced-scheduler.md
