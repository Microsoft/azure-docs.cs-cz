---
title: Nasazení kontejnerů s nástrojem Helm. v Kubernetes v Azure
description: Nasazení kontejnerů v clusteru Azure Kubernetes Service (AKS) pomocí nástroje Helm balení
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/13/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: dd2deba25615373765dd3492d03c1ba547c8ba8c
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39055130"
---
# <a name="install-applications-with-helm-in-azure-kubernetes-service-aks"></a>Instalace aplikací s nástrojem Helm ve službě Azure Kubernetes Service (AKS)

[Příkaz Helm] [ helm] je balení open source nástroj, který vám pomůže nainstalovat a spravovat životní cyklus aplikací Kubernetes. Podobně jako správce balíčků Linux jako *APT* a *Yumu*, Helm slouží ke správě grafů Kubernetes, což jsou balíčky předem prostředky Kubernetesu.

Tento článek ukazuje, jak nakonfigurovat a používat Helm v clusteru Kubernetes v AKS.

## <a name="before-you-begin"></a>Než začnete

Kroky popsané v tomto dokumentu předpokládají, že jste vytvořili AKS cluster a navázali `kubectl` připojení ke clusteru. Pokud budete potřebovat tyto položky zobrazit, [AKS quickstart][aks-quickstart].

## <a name="install-helm-cli"></a>Nainstalovat Helm CLI

Helm CLI je klient, který běží ve vývojovém systému a umožňuje spuštění, zastavení a správu aplikací s nástrojem Helm.

Pokud používáte Azure Cloud Shell, rozhraní příkazového řádku Helm je již nainstalována. Chcete-li nainstalovat rozhraní příkazového řádku příkaz Helm na počítači Mac, použijte `brew`. Další instalační možnosti najdete v tématu [instalace Helm][helm-install-options].

```console
brew install kubernetes-helm
```

Výstup:

```
==> Downloading https://homebrew.bintray.com/bottles/kubernetes-helm-2.9.1.high_sierra.bottle.tar.gz
######################################################################## 100.0%
==> Pouring kubernetes-helm-2.9.1.high_sierra.bottle.tar.gz
==> Caveats
Bash completion has been installed to:
  /usr/local/etc/bash_completion.d
==> Summary
🍺  /usr/local/Cellar/kubernetes-helm/2.9.1: 50 files, 66.2MB
```

## <a name="create-a-service-account"></a>Vytvoření účtu služby

Před nasazením Helm v clusteru s podporou RBAC, potřebujete účet služby a role vazby pro službu Tiller. Další informace o zabezpečení Helm / Tiller v RBAC povolena clusteru, naleznete v tématu [Tiller, obory názvů a RBAC][tiller-rbac]. Pokud váš cluster není povoleno RBAC, tento krok přeskočte.

Vytvořte soubor s názvem `helm-rbac.yaml` a zkopírujte do následující kód YAML:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1beta1
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

Vytvořit účet služby a role vazba s `kubectl create` příkaz:

```console
kubectl create -f helm-rbac.yaml
```

## <a name="secure-tiller-and-helm"></a>Zabezpečení Tiller a Helm

Příkaz Helm klient a služba Tiller ověření a vzájemnou komunikaci pomocí protokolu TLS/SSL. Tato metoda ověřování pomáhá se zabezpečením Kubernetes cluster a jaké služby je možné nasadit. Pro zlepšení zabezpečení, můžete vygenerovat vlastní certifikáty podepsané držitelem. Každý uživatel Helm získá vlastní klientský certifikát a Tiller by inicializovat v clusteru Kubernetes s využitím certifikátů použít. Další informace najdete v tématu [pomocí protokolu TLS/SSL mezi Helm a Tiller][helm-ssl].

S podporou RBAC cluster Kubernetes ve službě můžete řídit úroveň přístupu, kterou má Tiller do clusteru. Můžete definovat obor názvů Kubernetes, která je nasazená Tiller a omezit které obory názvů Tiller pak můžete nasadit prostředky v. Tento přístup umožňuje vytvářet Tiller instance v různých oborech názvů a limit nasazení hranice a oboru uživatele Helm klienta k určité obory názvů. Další informace najdete v tématu [Helm řízení přístupu na základě rolí][helm-rbac].

## <a name="configure-helm"></a>Konfigurace Helm

Chcete-li nasadit základní Tiller do clusteru AKS, použijte [příkaz helm init] [ helm-init] příkazu. Pokud váš cluster není povoleno RBAC, odeberte `--service-account` argumentu a hodnotu. Pokud jste nakonfigurovali protokol TLS/SSL pro Tiller a Helm, přeskočte tento krok základní inicializace a místo toho zadejte požadovaný `--tiller-tls-` jak je znázorněno v následujícím příkladu.

```console
helm init --service-account tiller
```

Pokud jste nakonfigurovali protokol TLS/SSL mezi Helm a Tiller poskytují `--tiller-tls-` parametrů a názvů vlastních certifikátů, jak je znázorněno v následujícím příkladu:

```console
helm init \
    --tiller-tls \
    --tiller-tls-cert tiller.cert.pem \
    --tiller-tls-key tiller.key.pem \
    --tiller-tls-verify \
    --tls-ca-cert ca.cert.pem \
    --service-account tiller
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
stable/acs-engine-autoscaler   2.2.0            2.1.1        Scales worker nodes within agent pools
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

Hang tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

## <a name="run-helm-charts"></a>Spustit helmu

Chcete-li nainstalovat grafy s nástrojem Helm, použijte [helm install] [ helm-install] příkaz a zadejte název grafu tak, aby instalace. Tento údaj zobrazíte v akci, nainstalujme základní nasazení Wordpress pomocí grafu helmu. Pokud jste nakonfigurovali protokol TLS/SSL, přidejte `--tls` parametr, který se klientský certifikát Helm.

```console
helm install stable/wordpress
```

Následující výstup zhuštěnému příkladu zobrazuje stav nasazení Kubernetes prostředky vytvořené v grafu helmu:

```
$ helm install stable/wordpress

NAME:   wishful-mastiff
LAST DEPLOYED: Thu Jul 12 15:53:56 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1beta1/Deployment
NAME                       DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
wishful-mastiff-wordpress  1        1        1           0          1s

==> v1beta1/StatefulSet
NAME                     DESIRED  CURRENT  AGE
wishful-mastiff-mariadb  1        1        1s

==> v1/Pod(related)
NAME                                        READY  STATUS   RESTARTS  AGE
wishful-mastiff-wordpress-6f96f8fdf9-q84sz  0/1    Pending  0         1s
wishful-mastiff-mariadb-0                   0/1    Pending  0         1s

==> v1/Secret
NAME                       TYPE    DATA  AGE
wishful-mastiff-mariadb    Opaque  2     2s
wishful-mastiff-wordpress  Opaque  2     2s

==> v1/ConfigMap
NAME                           DATA  AGE
wishful-mastiff-mariadb        1     2s
wishful-mastiff-mariadb-tests  1     2s

==> v1/PersistentVolumeClaim
NAME                       STATUS   VOLUME   CAPACITY  ACCESS MODES  STORAGECLASS  AGE
wishful-mastiff-wordpress  Pending  default  2s

==> v1/Service
NAME                       TYPE          CLUSTER-IP   EXTERNAL-IP  PORT(S)                     AGE
wishful-mastiff-mariadb    ClusterIP     10.1.116.54  <none>       3306/TCP                    2s
wishful-mastiff-wordpress  LoadBalancer  10.1.217.64  <pending>    80:31751/TCP,443:31264/TCP  2s
...
```

Bude trvat minutu nebo dvě *EXTERNAL-IP* adresu službě Wordpress a vyplňovat a budou ji bylo možné přistupovat k ní pomocí webového prohlížeče.

## <a name="list-helm-releases"></a>Uvolní list Helm

Chcete-li zobrazit seznam verzí nainstalované ve vašem clusteru, použijte [příkaz helm list] [ helm-list] příkazu. Následující příklad ukazuje, verze Wordpress nasazené v předchozím kroku. Pokud jste nakonfigurovali protokol TLS/SSL, přidejte `--tls` parametr, který se klientský certifikát Helm.

```console
$ helm list

NAME             REVISION    UPDATED                     STATUS      CHART              NAMESPACE
wishful-mastiff  1           Thu Jul 12 15:53:56 2018    DEPLOYED    wordpress-2.1.3  default
```

## <a name="next-steps"></a>Další postup

Další informace o správě aplikací nasazení Kubernetes pomocí Helm najdete v dokumentaci Helm.

> [!div class="nextstepaction"]
> [Dokumentace ke službě Helm][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-documentation]: https://docs.helm.sh/
[helm-init]: https://docs.helm.sh/helm/#helm-init
[helm-install]: https://docs.helm.sh/helm/#helm-install
[helm-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm-list]: https://docs.helm.sh/helm/#helm-list
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[helm-repo-update]: https://docs.helm.sh/helm/#helm-repo-update
[helm-search]: https://docs.helm.sh/helm/#helm-search
[tiller-rbac]: https://docs.helm.sh/using_helm/#tiller-namespaces-and-rbac
[helm-ssl]: https://docs.helm.sh/using_helm/#using-ssl-between-helm-and-tiller

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
