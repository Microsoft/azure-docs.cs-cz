---
title: Nasazení kontejnerů pomocí Helm v Kubernetes v Azure
description: Naučte se používat nástroj pro vytváření balíčků Helm k nasazení kontejnerů v clusteru Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 05/23/2019
ms.author: zarhoads
ms.openlocfilehash: bc74ac660c5bba0624416d0a1724d959a4c385a7
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305276"
---
# <a name="install-applications-with-helm-in-azure-kubernetes-service-aks"></a>Instalace aplikací pomocí Helm ve službě Azure Kubernetes (AKS)

[Helm][helm] je open source nástroj pro balení, který vám pomůže s instalací a správou životního cyklu aplikací Kubernetes. Podobně jako správci balíčků pro Linux, jako je *apt* a *Yumu*, se Helm používá ke správě Kubernetes grafů, což jsou balíčky předkonfigurovaných prostředků Kubernetes.

V tomto článku se dozvíte, jak nakonfigurovat a používat Helm v clusteru Kubernetes v AKS.

## <a name="before-you-begin"></a>Před zahájením

V tomto článku se předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, přečtěte si rychlý Start AKS a [použijte Azure CLI][aks-quickstart-cli] nebo [Azure Portal][aks-quickstart-portal].

Je také potřeba nainstalovat rozhraní příkazového řádku Helm, což je klient, který běží ve vašem vývojovém systému. Umožňuje spouštět, zastavovat a spravovat aplikace pomocí Helm. Pokud použijete Azure Cloud Shell, rozhraní příkazového řádku Helm je již nainstalováno. Pokyny k instalaci na místní platformě najdete v tématu [instalace Helm][helm-install].

> [!IMPORTANT]
> Helm je určený ke spuštění na uzlech systému Linux. Pokud máte v clusteru uzly Windows serveru, musíte zajistit, aby Helm lusky běžely jenom na uzlech se systémem Linux. Také je potřeba zajistit, aby všechny Helm grafy, které nainstalujete, běžely taky na správných uzlech. V příkazech v tomto článku se používají selektory [uzlů][k8s-node-selector] k tomu, aby byly lusky naplánovány na správné uzly, ale ne všechny Helm grafy mohou vystavit selektor uzlů. Můžete také zvážit použití dalších možností v clusteru, například [chuti][taints].

## <a name="create-a-service-account"></a>Vytvoření účtu služby

Než budete moct nasadit Helm v clusteru AKS s povoleným RBAC, budete potřebovat účet služby a vazbu role pro službu do služby. Další informace o zabezpečení Helm/v případě, že je v clusteru s povoleným RBAC, najdete v tématech předané [, obory názvů a RBAC][tiller-rbac]. Pokud váš cluster AKS není RBAC povolený, přeskočte tento krok.

Vytvořte soubor s názvem `helm-rbac.yaml` a zkopírujte ho do následujícího YAML:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

Vytvořte účet služby a vazbu role pomocí `kubectl apply` příkazu:

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="secure-tiller-and-helm"></a>Zabezpečení a Helm

Klient Helm a služba přenáší služby a navzájem komunikují pomocí protokolu TLS/SSL. Tato metoda ověřování pomáhá zabezpečit cluster Kubernetes a které služby lze nasadit. Pro zvýšení zabezpečení můžete vygenerovat vlastní podepsané certifikáty. Každý Helm uživatel obdrží svůj vlastní certifikát klienta a v clusteru Kubernetes by se inicializoval, že se použily certifikáty. Další informace najdete v tématu [používání protokolu TLS/SSL mezi Helm a pokladnou][helm-ssl].

S clusterem Kubernetes s povolenou RBAC můžete řídit úroveň přístupu, který má cluster k dispozici. Můžete definovat obor názvů Kubernetes, v němž je nasazený, a omezit, které obory názvů můžou nasadit prostředky v. Tento přístup umožňuje vytvořit instance v různých oborech názvů a omezit hranice nasazení a určit obor uživatelů klienta Helm na určité obory názvů. Další informace najdete v tématu [Helm řízení přístupu na základě rolí][helm-rbac].

## <a name="configure-helm"></a>Konfigurace Helm

K nasazení základní pokladny do clusteru AKS použijte příkaz [Helm init][helm-init] . Pokud váš cluster nemá povolené RBAC, odeberte `--service-account` argument a hodnotu. V následujících příkladech je také možné nastavit [historii-Max][helm-history-max] na 200.

Pokud jste nakonfigurovali protokol TLS/SSL pro předané a Helm, přeskočte tento krok základní inicializace a místo `--tiller-tls-` toho zadejte požadované, jak je znázorněno v následujícím příkladu.

```console
helm init --history-max 200 --service-account tiller --node-selectors "beta.kubernetes.io/os=linux"
```

Pokud jste nakonfigurovali protokol TLS/SSL mezi Helm a do `--tiller-tls-*` pokladny, zadejte parametry a názvy vašich vlastních certifikátů, jak je znázorněno v následujícím příkladu:

```console
helm init \
    --tiller-tls \
    --tiller-tls-cert tiller.cert.pem \
    --tiller-tls-key tiller.key.pem \
    --tiller-tls-verify \
    --tls-ca-cert ca.cert.pem \
    --history-max 200 \
    --service-account tiller \
    --node-selectors "beta.kubernetes.io/os=linux"
```

## <a name="find-helm-charts"></a>Hledání Helm grafů

Grafy Helm slouží k nasazování aplikací do clusteru Kubernetes. K vyhledání předem vytvořených grafů Helm použijte příkaz [Helm Search][helm-search] :

```console
helm search
```

Následující zhuštěný příklad výstupu ukazuje některé z Helm grafů dostupných pro použití:

```
$ helm search

NAME                           CHART VERSION    APP VERSION  DESCRIPTION
stable/aerospike               0.1.7            v3.14.1.2    A Helm chart for Aerospike in Kubernetes
stable/anchore-engine          0.1.7            0.1.10       Anchore container analysis and policy evaluatio...
stable/apm-server              0.1.0            6.2.4        The server receives data from the Elastic APM a...
stable/ark                     1.0.1            0.8.2        A Helm chart for ark
stable/artifactory             7.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/artifactory-ha          0.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/auditbeat               0.1.0            6.2.4        A lightweight shipper to audit the activities o...
stable/aws-cluster-autoscaler  0.3.3                         Scales worker nodes within autoscaling groups.
stable/bitcoind                0.1.3            0.15.1       Bitcoin is an innovative payment network and a ...
stable/buildkite               0.2.3            3            Agent for Buildkite
stable/burrow                  0.4.4            0.17.1       Burrow is a permissionable smart contract machine
stable/centrifugo              2.0.1            1.7.3        Centrifugo is a real-time messaging server.
stable/cerebro                 0.1.0            0.7.3        A Helm chart for Cerebro - a web admin tool tha...
stable/cert-manager            v0.3.3           v0.3.1       A Helm chart for cert-manager
stable/chaoskube               0.7.0            0.8.0        Chaoskube periodically kills random pods in you...
stable/chartmuseum             1.5.0            0.7.0        Helm Chart Repository with support for Amazon S...
stable/chronograf              0.4.5            1.3          Open-source web application written in Go and R...
stable/cluster-autoscaler      0.6.4            1.2.2        Scales worker nodes within autoscaling groups.
stable/cockroachdb             1.1.1            2.0.0        CockroachDB is a scalable, survivable, strongly...
stable/concourse               1.10.1           3.14.1       Concourse is a simple and scalable CI system.
stable/consul                  3.2.0            1.0.0        Highly available and distributed service discov...
stable/coredns                 0.9.0            1.0.6        CoreDNS is a DNS server that chains plugins and...
stable/coscale                 0.2.1            3.9.1        CoScale Agent
stable/dask                    1.0.4            0.17.4       Distributed computation in Python with task sch...
stable/dask-distributed        2.0.2                         DEPRECATED: Distributed computation in Python
stable/datadog                 0.18.0           6.3.0        DataDog Agent
...
```

Chcete-li aktualizovat seznam grafů, použijte příkaz [Helm úložiště Update][helm-repo-update] . Následující příklad ukazuje úspěšnou aktualizaci úložiště:

```console
$ helm repo update

Hold tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete.
```

## <a name="run-helm-charts"></a>Spuštění Helm grafů

Pokud chcete grafy nainstalovat pomocí Helm, použijte příkaz pro [instalaci Helm][helm-install] a zadejte název grafu, který chcete nainstalovat. Pokud chcete vidět, jak se v akci nainstaluje graf Helm, nainstalujte základní nasazení Nginx pomocí grafu Helm. Pokud jste nakonfigurovali protokol TLS/SSL, `--tls` přidejte parametr pro použití certifikátu klienta Helm.

```console
helm install stable/nginx-ingress \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Následující zhuštěný příklad výstupu ukazuje stav nasazení prostředků Kubernetes vytvořených pomocí grafu Helm:

```
$ helm install stable/nginx-ingress --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux

NAME:   flailing-alpaca
LAST DEPLOYED: Thu May 23 12:55:21 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                                      DATA  AGE
flailing-alpaca-nginx-ingress-controller  1     0s

==> v1/Pod(related)
NAME                                                            READY  STATUS             RESTARTS  AGE
flailing-alpaca-nginx-ingress-controller-56666dfd9f-bq4cl       0/1    ContainerCreating  0         0s
flailing-alpaca-nginx-ingress-default-backend-66bc89dc44-m87bp  0/1    ContainerCreating  0         0s

==> v1/Service
NAME                                           TYPE          CLUSTER-IP  EXTERNAL-IP  PORT(S)                     AGE
flailing-alpaca-nginx-ingress-controller       LoadBalancer  10.0.109.7  <pending>    80:31219/TCP,443:32421/TCP  0s
flailing-alpaca-nginx-ingress-default-backend  ClusterIP     10.0.44.97  <none>       80/TCP                      0s
...
```

Vyplní jednu minutu nebo dvě adresu *externí IP* adresy služby Nginx-The-Controller a umožní vám k ní přístup pomocí webového prohlížeče.

## <a name="list-helm-releases"></a>Výpis verzí Helm

Pokud chcete zobrazit seznam verzí nainstalovaných v clusteru, použijte příkaz [Helm list][helm-list] . Následující příklad ukazuje Nginx verzi nasazenou v předchozím kroku. Pokud jste nakonfigurovali protokol TLS/SSL, `--tls` přidejte parametr pro použití certifikátu klienta Helm.

```console
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                 APP VERSION   NAMESPACE
flailing-alpaca   1         Thu May 23 12:55:21 2019    DEPLOYED    nginx-ingress-1.6.13    0.24.1      default
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když nasadíte graf Helm, vytvoří se několik prostředků Kubernetes. Mezi tyto prostředky patří lusky, nasazení a služby. K vyčištění těchto prostředků použijte `helm delete` příkaz a zadejte název vydané verze, jak je uvedeno v předchozím `helm list` příkazu. Následující příklad odstraní verzi s názvem *flailing-Alpaca*:

```console
$ helm delete flailing-alpaca

release "flailing-alpaca" deleted
```

## <a name="next-steps"></a>Další postup

Další informace o správě nasazení aplikací Kubernetes pomocí Helm najdete v dokumentaci k Helm.

> [!div class="nextstepaction"]
> [Dokumentace k Helm][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-documentation]: https://docs.helm.sh/
[helm-init]: https://docs.helm.sh/helm/#helm-init
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[helm-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm-list]: https://docs.helm.sh/helm/#helm-list
[helm-history-max]: https://helm.sh/docs/using_helm/#initialize-helm-and-install-tiller
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[helm-repo-update]: https://docs.helm.sh/helm/#helm-repo-update
[helm-search]: https://docs.helm.sh/helm/#helm-search
[tiller-rbac]: https://docs.helm.sh/using_helm/#tiller-namespaces-and-rbac
[helm-ssl]: https://docs.helm.sh/using_helm/#using-ssl-between-helm-and-tiller

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
[taints]: operator-best-practices-advanced-scheduler.md
