---
title: Instalace existujících aplikací pomocí Helm v AKS
description: Naučte se používat nástroj pro vytváření balíčků Helm k nasazení kontejnerů v clusteru Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.topic: article
ms.date: 12/07/2020
ms.author: zarhoads
ms.openlocfilehash: f12dffe0b538738a8f6dd00cd3d87d44da828f21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96779163"
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

Pomocí příkazu [Helm úložiště][helm-repo-add] přidejte úložiště příchozího přenosu dat *(Nginx* ).

```console
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
```

### <a name="find-helm-charts"></a>Hledání Helm grafů

Grafy Helm slouží k nasazování aplikací do clusteru Kubernetes. K vyhledání předem vytvořených grafů Helm použijte příkaz [Helm Search][helm-search] :

```console
helm search repo ingress-nginx
```

Následující zhuštěný příklad výstupu ukazuje některé z Helm grafů dostupných pro použití:

```console
$ helm search repo ingress-nginx

NAME                            CHART VERSION   APP VERSION     DESCRIPTION                                       
ingress-nginx/ingress-nginx     2.12.0          0.34.1          Ingress controller for Kubernetes using NGINX a...
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
