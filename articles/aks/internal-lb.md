---
title: Vytvoření interního nástroje pro vyrovnávání zatížení ve službě Azure Kubernetes Service (AKS)
description: Naučte se vytvářet a používat interní nástroj pro vyrovnávání zatížení k vystavování služeb pomocí Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/04/2019
ms.openlocfilehash: ff102ebe50dd4d2169090718ced9e550701b1b09
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78374686"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Použití interního nástroje pro vyrovnávání zatížení se službou Azure Kubernetes Service (AKS)

Pokud chcete omezit přístup k vašim aplikacím ve službě Azure Kubernetes Service (AKS), můžete vytvořit a používat interní nástroj pro vyrovnávání zatížení. Interní nástroj pro vyrovnávání zatížení zpřístupňuje službu Kubernetes jenom aplikacím běžícím ve stejné virtuální síti jako cluster Kubernetes. V tomto článku se dozvíte, jak vytvořit a používat interní nástroj pro vyrovnávání zatížení se službou Azure Kubernetes Service (AKS).

> [!NOTE]
> Azure Load Balancer je k dispozici ve dvou SKU – *Basic* a *Standard*. Ve výchozím nastavení se standardní SKU používá při vytváření clusteru AKS.  Při vytváření služby s typem jako nástroj pro vyrovnávání zatížení se při zřizování clusteru zobrazí stejný typ. Další informace najdete v tématu [porovnání SKU nástroje pro vyrovnávání zatížení Azure][azure-lb-comparison].

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, přečtěte si rychlý Start AKS a [použijte Azure CLI][aks-quickstart-cli] nebo [Azure Portal][aks-quickstart-portal].

Potřebujete také nainstalované a nakonfigurované rozhraní Azure CLI verze 2.0.59 nebo novější. Pro nalezení verze spusťte `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI][install-azure-cli].

Pokud použijete existující podsíť nebo skupinu prostředků, instanční objekt služby AKS potřebuje oprávnění ke správě síťových prostředků. Obecně přiřaďte roli *Přispěvatel sítě* k instančnímu objektu u delegovaných prostředků. Další informace o oprávněních najdete v tématu [delegování přístupu AKS k ostatním prostředkům Azure][aks-sp].

## <a name="create-an-internal-load-balancer"></a>Vytvořte interní nástroj pro vyrovnávání zatížení.

Pokud chcete vytvořit interní nástroj pro vyrovnávání zatížení, vytvořte v něm manifest služby s názvem `internal-lb.yaml` *s typem služby* a službou *Azure-Load Balancer – interní* anotaci, jak je znázorněno v následujícím příkladu:

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

Nasaďte interní nástroj pro vyrovnávání zatížení pomocí [kubectl použít][kubectl-apply] a zadejte název manifestu YAML:

```console
kubectl apply -f internal-lb.yaml
```

V rámci skupiny prostředků uzlu se vytvoří nástroj pro vyrovnávání zatížení Azure, který se připojí ke stejné virtuální síti jako cluster AKS.

Po zobrazení podrobností služby se IP adresa interního nástroje pro vyrovnávání zatížení zobrazí ve sloupci *externí-IP* . V tomto kontextu je *externí* ve vztahu k externímu rozhraní nástroje pro vyrovnávání zatížení, a ne k tomu, že obdrží veřejnou externí IP adresu. Může trvat několik minut, než se IP adresa změní z *\<čeká na\>* na skutečnou interní IP adresu, jak je znázorněno v následujícím příkladu:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>Zadat IP adresu

Pokud chcete použít konkrétní IP adresu s interním nástrojem pro vyrovnávání zatížení, přidejte do manifestu YAML nástroje pro vyrovnávání zatížení vlastnost *loadBalancerIP* . Zadaná IP adresa se musí nacházet ve stejné podsíti jako cluster AKS a nesmí se k prostředku přiřazovat.

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

Když nasadíte a zobrazíte podrobnosti služby, IP adresa ve sloupci *External-IP* odráží zadanou IP adresu:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="use-private-networks"></a>Použití privátních sítí

Při vytváření clusteru AKS můžete zadat Pokročilá nastavení sítě. Tento přístup umožňuje nasadit cluster do existující virtuální sítě Azure a podsítí. Jedním z scénářů je nasadit cluster AKS do privátní sítě připojené k místnímu prostředí a spouštět služby přístupné jenom interně. Další informace najdete v tématu Konfigurace vlastních podsítí virtuální sítě pomocí [Kubenet][use-kubenet] nebo [Azure CNI][advanced-networking].

Pro nasazení interního nástroje pro vyrovnávání zatížení v clusteru AKS, který používá privátní síť, není nutné provádět žádné změny v předchozích krocích. Nástroj pro vyrovnávání zatížení se vytvoří ve stejné skupině prostředků jako cluster AKS, ale připojil se k vaší privátní virtuální síti a podsíti, jak je znázorněno v následujícím příkladu:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Je možné, že bude nutné instančnímu objektu pro cluster AKS udělit roli *Přispěvatel sítě* do skupiny prostředků, ve které jsou nasazené prostředky virtuální sítě Azure. Zobrazte instanční objekt pomocí [AZ AKS show][az-aks-show], například `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"`. Přiřazení role vytvoříte pomocí příkazu [AZ role Assignment Create][az-role-assignment-create] .

## <a name="specify-a-different-subnet"></a>Zadejte jinou podsíť.

Pokud chcete zadat podsíť pro nástroj pro vyrovnávání zatížení, přidejte do služby anotaci *Azure-Load Balancer – interní podsíť* . Zadaná podsíť musí být ve stejné virtuální síti jako cluster AKS. Při nasazení je *externí IP* adresa nástroje pro vyrovnávání zatížení součástí zadané podsítě.

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

## <a name="delete-the-load-balancer"></a>Odstranit Nástroj pro vyrovnávání zatížení

Když se odstraní všechny služby, které používají interní nástroj pro vyrovnávání zatížení, odstraní se taky samotný nástroj pro vyrovnávání zatížení.

Můžete také přímo odstranit službu jako u libovolného prostředku Kubernetes, jako je například `kubectl delete service internal-app`, a následně odstranit základní nástroj pro vyrovnávání zatížení Azure.

## <a name="next-steps"></a>Další kroky

Další informace o službách Kubernetes Services najdete v [dokumentaci ke službám Kubernetes][kubernetes-services].

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
