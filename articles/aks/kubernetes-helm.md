---
title: Nasazení kontejnerů s Helm v Kubernetes v Azure
description: Pomocí nástroje balení Helm nasazení kontejnerů v clusteru s podporou Kubernetes v AKS
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 531e6d9368b2bf91c48fd41b1e9330879b0df49a
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102153"
---
# <a name="use-helm-with-azure-kubernetes-service-aks"></a>Helm pomocí služby Azure Kubernetes (AKS)

[Helm] [ helm] je nástroj balení open source, který vám pomůže nainstalovat a spravovat životní cyklus aplikace Kubernetes. Podobně jako správce balíčku Linux jako *byt č* a *Yum*, Helm slouží ke správě Kubernetes grafy, které jsou balíčky předkonfigurované Kubernetes prostředků.

Tento dokument postup prostřednictvím konfiguraci a použití Helm v clusteru s podporou Kubernetes na AKS.

## <a name="before-you-begin"></a>Než začnete

Podrobně popsané kroky v tomto dokumentu předpokládají, že jste vytvořili cluster AKS a navázali s ním připojení přes kubectl. Pokud budete potřebovat tyto položky zobrazit, [rychlý start AKS][aks-quickstart].

## <a name="install-helm-cli"></a>Nainstalujte Helm rozhraní příkazového řádku

Rozhraní příkazového řádku Helm je klient, který běží ve vývojovém systému a umožňuje spuštění, zastavení a správu aplikací s Helm.

Pokud používáte Azure CloudShell, rozhraní příkazového řádku Helm je již nainstalován. Instalace rozhraní příkazového řádku Helm na použití Mac `brew`. Instalace dalších možností najdete v tématu [instalace Helm][helm-install-options].

```console
brew install kubernetes-helm
```

Výstup:

```
==> Downloading https://homebrew.bintray.com/bottles/kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
######################################################################## 100.0%
==> Pouring kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
==> Caveats
Bash completion has been installed to:
  /usr/local/etc/bash_completion.d
==> Summary
🍺  /usr/local/Cellar/kubernetes-helm/2.6.2: 50 files, 132.4MB
```

## <a name="create-service-account"></a>Vytvoření účtu služby

Než konfigurace Helm v RBAC povoleno clusteru, musíte účet služby a vazby pro službu výšce kormidelní páky role. Další informace o zabezpečení Helm / výšce kormidelní páky v RBAC povoleno clusteru, najdete v části [výšce kormidelní páky, obory názvů a RBAC][tiller-rbac]. Poznámka: Pokud váš cluster není RBAC povolena, tento krok přeskočit.

Vytvořte soubor s názvem `helm-rbac.yaml` a zkopírujte následující YAML.

```
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

Vytvořit účet služby a vazbu role pomocí `kubectl create` příkaz.

```
kubectl create -f helm-rbac.yaml
```

Když pomocí RBAC povoleno clusteru, zobrazí se možnosti na úrovni přístupu, kterou má výšce kormidelní páky do clusteru. V tématu [Helm: řízení přístupu na základě role] [ helm-rbac] Další informace o možnostech konfigurace.

## <a name="configure-helm"></a>Konfigurace Helm

Nyní nainstalovat pomocí výšce kormidelní páky [helm init] [ helm-init] příkaz. Pokud váš cluster není RBAC povolena, odeberte `--service-account` argumentu a hodnotu.

```
helm init --service-account tiller
```

## <a name="find-helm-charts"></a>Najít Helm grafy

Helm grafy se používají k nasazení aplikací do clusteru s podporou Kubernetes. Chcete-li vyhledat předem vytvořené Helm grafy, použijte [helm vyhledávání] [ helm-search] příkaz.

```azurecli-interactive
helm search
```

Vypadá výstup podobný následujícímu, ale s mnoha další grafy.

```
NAME                            VERSION DESCRIPTION
stable/acs-engine-autoscaler    2.0.0   Scales worker nodes within agent pools
stable/artifactory              6.1.0   Universal Repository Manager supporting all maj...
stable/aws-cluster-autoscaler   0.3.1   Scales worker nodes within autoscaling groups.
stable/buildkite                0.2.0   Agent for Buildkite
stable/centrifugo               2.0.0   Centrifugo is a real-time messaging server.
stable/chaoskube                0.5.0   Chaoskube periodically kills random pods in you...
stable/chronograf               0.3.0   Open-source web application written in Go and R...
stable/cluster-autoscaler       0.2.0   Scales worker nodes within autoscaling groups.
stable/cockroachdb              0.5.0   CockroachDB is a scalable, survivable, strongly...
stable/concourse                0.7.0   Concourse is a simple and scalable CI system.
stable/consul                   0.4.1   Highly available and distributed service discov...
stable/coredns                  0.5.0   CoreDNS is a DNS server that chains middleware ...
stable/coscale                  0.2.0   CoScale Agent
stable/dask-distributed         2.0.0   Distributed computation in Python
stable/datadog                  0.8.0   DataDog Agent
...
```

Chcete-li aktualizovat seznam grafy, použijte [helm úložišti aktualizace] [ helm-repo-update] příkaz.

```azurecli-interactive
helm repo update
```

Výstup:

```
Hang tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

## <a name="run-helm-charts"></a>Spustit Helm grafy

Chcete-li nasadit pomocí graf Helm Wordpress, použijte [helm instalace] [ helm-install] příkaz.

```azurecli-interactive
helm install stable/wordpress
```

Výstup bude vypadat podobně jako následující, ale obsahuje další informace, jako je například pokyny o tom, jak použít Kubernetes nasazení.

```
NAME:   bilging-ibex
LAST DEPLOYED: Tue Jun  5 14:31:49 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                                     READY  STATUS   RESTARTS  AGE
bilging-ibex-mariadb-7557b5474-dmdxn     0/1    Pending  0         1s
bilging-ibex-wordpress-7494c545fb-tskhz  0/1    Pending  0         1s

==> v1/Secret
NAME                    TYPE    DATA  AGE
bilging-ibex-mariadb    Opaque  2     1s
bilging-ibex-wordpress  Opaque  2     1s

==> v1/ConfigMap
NAME                        DATA  AGE
bilging-ibex-mariadb        1     1s
bilging-ibex-mariadb-tests  1     1s

==> v1/PersistentVolumeClaim
NAME                    STATUS   VOLUME   CAPACITY  ACCESS MODES  STORAGECLASS  AGE
bilging-ibex-mariadb    Pending  default  1s
bilging-ibex-wordpress  Pending  default  1s

==> v1/Service
NAME                    TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)                     AGE
bilging-ibex-mariadb    ClusterIP     10.0.76.164   <none>       3306/TCP                    1s
bilging-ibex-wordpress  LoadBalancer  10.0.215.250  <pending>    80:30934/TCP,443:31134/TCP  1s

==> v1beta1/Deployment
NAME                    DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
bilging-ibex-mariadb    1        1        1           0          1s
bilging-ibex-wordpress  1        1        1           0          1s
...
```

## <a name="list-helm-releases"></a>Uvolní Helm seznamu

Chcete-li zobrazit seznam verze nainstalované v clusteru, použijte [helm seznamu] [ helm-list] příkaz.

```azurecli-interactive
helm list
```

Výstup:

```
NAME            REVISION    UPDATED                     STATUS      CHART           NAMESPACE
bilging-ibex    1           Tue Jun  5 14:31:49 2018    DEPLOYED    wordpress-1.0.9 default
```

## <a name="next-steps"></a>Další postup

Další informace o správě Kubernetes grafy naleznete v dokumentaci k Helm.

> [!div class="nextstepaction"]
> [Helm dokumentace][helm-documentation]

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

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
