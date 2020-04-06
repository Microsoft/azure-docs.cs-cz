---
title: Vytvořte interní nástroj pro vyrovnávání zatížení.
titleSuffix: Azure Kubernetes Service
description: Zjistěte, jak vytvořit a použít interní systém vyrovnávání zatížení k vystavení vašich služeb pomocí služby Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/04/2019
ms.openlocfilehash: 4decd66a558b031f1aaaf9c64556dae545ed05d3
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668415"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Použití interního zařízení pro vyrovnávání zatížení se službou Azure Kubernetes Service (AKS)

Chcete-li omezit přístup k vašim aplikacím ve službě Azure Kubernetes Service (AKS), můžete vytvořit a použít interní vyrovnávání zatížení. Interní nástroj pro vyrovnávání zatížení zpřístupňuje službu Kubernetes pouze aplikacím spuštěným ve stejné virtuální síti jako cluster Kubernetes. Tento článek ukazuje, jak vytvořit a používat interní vyrovnávání zatížení se službou Azure Kubernetes Service (AKS).

> [!NOTE]
> Azure Load Balancer je k dispozici ve dvou sku - *základní* a *standardní*. Ve výchozím nastavení se standardní skladová položka používá při vytváření clusteru AKS.  Při vytváření služby s typem jako LoadBalancer získáte stejný typ LB jako při zřizování clusteru. Další informace naleznete v [tématu Azure vyrovnávání zatížení SKU porovnání][azure-lb-comparison].

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, podívejte se na aks rychlý start [pomocí Azure CLI][aks-quickstart-cli] nebo [pomocí portálu Azure][aks-quickstart-portal].

Potřebujete také nainstalované a nakonfigurované verze Azure CLI verze 2.0.59 nebo novější. Spuštěním `az --version` najděte verzi. Pokud potřebujete nainstalovat nebo upgradovat, přečtěte si informace [o instalaci příkazového příkazového příkazu k webu Azure][install-azure-cli].

Pokud používáte existující podsíť nebo skupinu prostředků, potřebuje objekt zabezpečení clusterové služby AKS oprávnění ke správě síťových prostředků. Obecně přiřaďte roli *přispěvatele sítě* k instančnímu objektu v delegovaných prostředcích. Další informace o oprávněních najdete [v tématu Delegate AKS přístup k jiným prostředkům Azure][aks-sp].

## <a name="create-an-internal-load-balancer"></a>Vytvořte interní nástroj pro vyrovnávání zatížení.

Chcete-li vytvořit interní nástroj pro vyrovnávání zatížení, vytvořte manifest služby s názvem `internal-lb.yaml` s typem služby *LoadBalancer* a interní poznámkou *azure-load balancer,* jak je znázorněno v následujícím příkladu:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

Nasazení interního nástrojpro vyrovnávání zatížení pomocí [kubectl použít][kubectl-apply] a zadejte název manifestu YAML:

```console
kubectl apply -f internal-lb.yaml
```

Nástroj pro vyrovnávání zatížení Azure se vytvoří ve skupině prostředků uzlu a připojí se ke stejné virtuální síti jako cluster AKS.

Při zobrazení podrobností o službě se ve sloupci *EXTERNAL-IP* zobrazí adresa IP interního systému vyrovnávání zatížení. V tomto kontextu *Externí* je ve vztahu k externí rozhraní vyrovnávání zatížení, nikoli, že obdrží veřejnou, externí IP adresu. Může trvat minutu nebo dvě, než * \<se\> * ip adresa změní z čekající na skutečnou interní IP adresu, jak je znázorněno v následujícím příkladu:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>Zadání adresy IP

Pokud chcete použít konkrétní IP adresu s interním nástrojem pro vyrovnávání zatížení, přidejte vlastnost *loadBalancerIP* do manifestu YAML nástroj pro vyrovnávání zatížení. Zadaná adresa IP musí být umístěna ve stejné podsíti jako cluster AKS a nesmí být již přiřazena k prostředku.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: internal-app
```

Při nasazení a zobrazení podrobností o službě odráží ip adresa ve sloupci *EXTERNAL-IP* zadanou adresu IP:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="use-private-networks"></a>Používání privátních sítí

Při vytváření clusteru AKS můžete zadat upřesňující nastavení sítě. Tento přístup umožňuje nasadit cluster do existující virtuální sítě Azure a podsítí. Jedním scénářem je nasazení clusteru AKS do privátní sítě připojené k místnímu prostředí a spuštění služeb přístupné pouze interně. Další informace najdete v tématu konfigurace vlastních podsítí virtuální sítě s [Kubenet][use-kubenet] nebo [Azure CNI][advanced-networking].

K nasazení interního systému vyrovnávání zatížení v clusteru AKS, který používá privátní síť, nejsou nutné žádné změny předchozích kroků. Nástroj pro vyrovnávání zatížení se vytvoří ve stejné skupině prostředků jako cluster AKS, ale je připojen k privátní virtuální síti a podsíti, jak je znázorněno v následujícím příkladu:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Možná budete muset udělit instanční objekt pro váš cluster AKS roli *síťového přispěvatele* do skupiny prostředků, kde se nasazují vaše prostředky virtuální sítě Azure. Zobrazení instančního objektu s [az aks show][az-aks-show], například `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"`. Chcete-li vytvořit přiřazení role, použijte příkaz [vytvořit přiřazení role az.][az-role-assignment-create]

## <a name="specify-a-different-subnet"></a>Určení jiné podsítě

Chcete-li určit podsíť pro vyrovnávání zatížení, přidejte do služby anotaci *azure-load balancer-internal-subnet.* Zadaná podsíť musí být ve stejné virtuální síti jako cluster AKS. Při nasazení je adresa *EXTERNAL-IP* vykladače zatížení součástí zadané podsítě.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "apps-subnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

## <a name="delete-the-load-balancer"></a>Odstranění rovnováhy zatížení

Při odstranění všech služeb, které používají interní vyrovnávání zatížení, je odstraněn také samotný systém vyrovnávání zatížení.

Můžete také přímo odstranit službu jako u všech prostředků `kubectl delete service internal-app`Kubernetes, jako je například , který také odstraní základní nástroj pro vyrovnávání zatížení Azure.

## <a name="next-steps"></a>Další kroky

Další informace o službách Kubernetes naleznete v [dokumentaci ke službám Kubernetes][kubernetes-services].

<!-- LINKS - External -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb-comparison]: ../load-balancer/concepts-limitations.md#skus
[use-kubenet]: configure-kubenet.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
