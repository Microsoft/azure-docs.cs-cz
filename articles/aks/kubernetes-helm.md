---
title: Nasazení kontejnerů s nástrojem Helm. v Kubernetes v Azure
description: Další informace o použití nástroje pro balení Helm k nasazení kontejnerů v clusteru Azure Kubernetes Service (AKS)
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 05/23/2019
ms.author: zarhoads
ms.openlocfilehash: 76a5391cbe142851d9b1f60ea9346af2e7a35d6a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66392142"
---
# <a name="install-applications-with-helm-in-azure-kubernetes-service-aks"></a>Instalace aplikací s nástrojem Helm ve službě Azure Kubernetes Service (AKS)

[Příkaz Helm] [ helm] je balení open source nástroj, který vám pomůže nainstalovat a spravovat životní cyklus aplikací Kubernetes. Podobně jako správce balíčků Linux jako *APT* a *Yumu*, Helm slouží ke správě grafů Kubernetes, což jsou balíčky předem prostředky Kubernetesu.

Tento článek ukazuje, jak nakonfigurovat a používat Helm v clusteru Kubernetes v AKS.

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte existující cluster AKS. Pokud potřebujete AKS cluster, najdete v tomto rychlém startu AKS [pomocí Azure CLI] [ aks-quickstart-cli] nebo [pomocí webu Azure portal][aks-quickstart-portal].

Budete také potřebovat Helm nainstalované rozhraní příkazového řádku, které je klient, na kterém běží ve vývojovém systému. Umožňuje spuštění, zastavení a správu aplikací s nástrojem Helm. Pokud používáte Azure Cloud Shell, rozhraní příkazového řádku Helm je již nainstalována. Pokyny k instalaci na místní platformě najdete [instalace Helm][helm-install].

> [!IMPORTANT]
> Helm je určena pro spuštění na uzlech systému Linux. Pokud máte systém Windows Server uzlů v clusteru, ujistěte se, že jsou Helm podů pouze naplánovány ke spuštění na uzly s Linuxem. Je také potřeba zajistit, že jsou všechny helmu, který můžete nainstalovat také naplánovány ke spuštění na správné uzly. Příkazy v tomto článku používají [uzel selektory] [ k8s-node-selector] zajistit podů jsou naplánovány ke správné uzly, ale ne všechny grafy Helm může vystavit uzlu selektoru. Můžete také zvážit použití jiné možnosti v clusteru, jako například [taints][taints].

## <a name="create-a-service-account"></a>Vytvoření účtu služby

Před nasazením Helm v clusteru AKS povolené RBAC, potřebujete účet služby a role vazby pro službu Tiller. Další informace o zabezpečení Helm / Tiller v RBAC povolena clusteru, naleznete v tématu [Tiller, obory názvů a RBAC][tiller-rbac]. Pokud váš cluster AKS není povoleno RBAC, tento krok přeskočte.

Vytvořte soubor s názvem `helm-rbac.yaml` a zkopírujte do následující kód YAML:

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

Vytvořit účet služby a role vazba s `kubectl apply` příkaz:

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="secure-tiller-and-helm"></a>Zabezpečení Tiller a Helm

Příkaz Helm klient a služba Tiller ověření a vzájemnou komunikaci pomocí protokolu TLS/SSL. Tato metoda ověřování pomáhá se zabezpečením Kubernetes cluster a jaké služby je možné nasadit. Pro zlepšení zabezpečení, můžete vygenerovat vlastní certifikáty podepsané držitelem. Každý uživatel Helm získá vlastní klientský certifikát a Tiller by inicializovat v clusteru Kubernetes s využitím certifikátů použít. Další informace najdete v tématu [pomocí protokolu TLS/SSL mezi Helm a Tiller][helm-ssl].

S podporou RBAC cluster Kubernetes ve službě můžete řídit úroveň přístupu, kterou má Tiller do clusteru. Můžete definovat obor názvů Kubernetes, která je nasazená Tiller a omezit které obory názvů Tiller pak můžete nasadit prostředky v. Tento přístup umožňuje vytvářet Tiller instance v různých oborech názvů a limit nasazení hranice a oboru uživatele Helm klienta k určité obory názvů. Další informace najdete v tématu [Helm řízení přístupu na základě rolí][helm-rbac].

## <a name="configure-helm"></a>Konfigurace Helm

Chcete-li nasadit základní Tiller do clusteru AKS, použijte [příkaz helm init] [ helm-init] příkazu. Pokud váš cluster není povoleno RBAC, odeberte `--service-account` argumentu a hodnotu. Pokud jste nakonfigurovali protokol TLS/SSL pro Tiller a Helm, přeskočte tento krok základní inicializace a místo toho zadejte požadovaný `--tiller-tls-` jak je znázorněno v následujícím příkladu.

```console
helm init --service-account tiller --node-selectors "beta.kubernetes.io/os"="linux"
```

Pokud jste nakonfigurovali protokol TLS/SSL mezi Helm a Tiller poskytují `--tiller-tls-*` parametrů a názvů vlastních certifikátů, jak je znázorněno v následujícím příkladu:

```console
helm init \
    --tiller-tls \
    --tiller-tls-cert tiller.cert.pem \
    --tiller-tls-key tiller.key.pem \
    --tiller-tls-verify \
    --tls-ca-cert ca.cert.pem \
    --service-account tiller \
    --node-selectors "beta.kubernetes.io/os"="linux"
```

## <a name="find-helm-charts"></a>Vyhledat Helm grafy

Helmu slouží k nasazení aplikací do clusteru Kubernetes. Chcete-li vyhledat helmu předem vytvořené, použijte [helm search] [ helm-search] příkaz:

```console
helm search
```

Následující výstup zhuštěnému příkladu jsou uvedeny některé k dispozici pro použití helmu:

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

Chcete-li aktualizovat seznam grafy, použijte [aktualizace úložiště helmu] [ helm-repo-update] příkazu. Následující příklad ukazuje úspěšné úložiště aktualizací:

```console
$ helm repo update

Hold tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

## <a name="run-helm-charts"></a>Spustit helmu

Chcete-li nainstalovat grafy s nástrojem Helm, použijte [helm install] [ helm-install] příkaz a zadejte název grafu tak, aby instalace. Nainstalovat Helm chart v akci najdete nainstalujme nasazení základní nginx pomocí grafu helmu. Pokud jste nakonfigurovali protokol TLS/SSL, přidejte `--tls` parametr, který se klientský certifikát Helm.

```console
helm install stable/nginx-ingress \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Následující výstup zhuštěnému příkladu zobrazuje stav nasazení Kubernetes prostředky vytvořené v grafu helmu:

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

Bude trvat minutu nebo dvě *EXTERNAL-IP* adresu službě nginx kontroler příchozího přenosu dat a vyplňovat a budou ji bylo možné přistupovat k ní pomocí webového prohlížeče.

## <a name="list-helm-releases"></a>Uvolní list Helm

Chcete-li zobrazit seznam verzí nainstalované ve vašem clusteru, použijte [příkaz helm list] [ helm-list] příkazu. Následující příklad ukazuje verzi serveru nginx příchozího přenosu dat nasazené v předchozím kroku. Pokud jste nakonfigurovali protokol TLS/SSL, přidejte `--tls` parametr, který se klientský certifikát Helm.

```console
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                 APP VERSION   NAMESPACE
flailing-alpaca   1         Thu May 23 12:55:21 2019    DEPLOYED    nginx-ingress-1.6.13    0.24.1      default
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Při nasazování grafu helmu vytvoří řada prostředky Kubernetesu. Tyto prostředky zahrnují podů, nasazení a služby. K uvolnění těchto prostředků, použijte `helm delete` příkaz a zadejte název vaší verze a jak se nachází v předchozím `helm list` příkazu. Následující příklad odstraní vydání s názvem *flailing alpakové*:

```console
$ helm delete flailing-alpaca

release "flailing-alpaca" deleted
```

## <a name="next-steps"></a>Další postup

Další informace o správě aplikací nasazení Kubernetes pomocí Helm najdete v dokumentaci Helm.

> [!div class="nextstepaction"]
> [Dokumentace ke službě Helm][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-documentation]: https://docs.helm.sh/
[helm-init]: https://docs.helm.sh/helm/#helm-init
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[helm-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm-list]: https://docs.helm.sh/helm/#helm-list
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