---
title: Vytvořte interní nástroj pro vyrovnávání zatížení.
titleSuffix: Azure Kubernetes Service
description: Naučte se vytvářet a používat interní nástroj pro vyrovnávání zatížení k vystavování služeb pomocí Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/04/2019
ms.openlocfilehash: 4c2c0866aa9a721a73e1eb8fa230f0022cf6b8ca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102505626"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Použití interního nástroje pro vyrovnávání zatížení se službou Azure Kubernetes Service (AKS)

Pokud chcete omezit přístup k vašim aplikacím ve službě Azure Kubernetes Service (AKS), můžete vytvořit a používat interní nástroj pro vyrovnávání zatížení. Interní nástroj pro vyrovnávání zatížení zpřístupňuje službu Kubernetes jenom aplikacím běžícím ve stejné virtuální síti jako cluster Kubernetes. V tomto článku se dozvíte, jak vytvořit a používat interní nástroj pro vyrovnávání zatížení se službou Azure Kubernetes Service (AKS).

> [!NOTE]
> Azure Load Balancer je k dispozici ve dvou SKU – *Basic* a *Standard*. Ve výchozím nastavení se standardní SKU používá při vytváření clusteru AKS.  Při vytváření služby s typem jako nástroj pro vyrovnávání zatížení se při zřizování clusteru zobrazí stejný typ. Další informace najdete v tématu [porovnání SKU nástroje pro vyrovnávání zatížení Azure][azure-lb-comparison].

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, přečtěte si rychlý Start AKS a [použijte Azure CLI][aks-quickstart-cli] nebo [Azure Portal][aks-quickstart-portal].

Potřebujete také nainstalované a nakonfigurované rozhraní Azure CLI verze 2.0.59 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

Identita clusteru clusteru AKS potřebuje oprávnění ke správě síťových prostředků, pokud použijete existující podsíť nebo skupinu prostředků. Informace najdete v tématu [použití sítě kubenet s vlastními rozsahy IP adres ve službě Azure Kubernetes Service (AKS)][use-kubenet] nebo [Konfigurace sítě Azure CNI ve službě Azure KUBERNETES Service (AKS)][advanced-networking]. Pokud konfigurujete Nástroj pro vyrovnávání zatížení tak, aby používal [IP adresu v jiné podsíti][different-subnet], ujistěte se, že má identita clusteru AKS také oprávnění ke čtení této podsítě.

Další informace o oprávněních najdete v tématu [delegování přístupu AKS k ostatním prostředkům Azure][aks-sp].

## <a name="create-an-internal-load-balancer"></a>Vytvořte interní nástroj pro vyrovnávání zatížení.

Pokud chcete vytvořit interní nástroj pro vyrovnávání zatížení, vytvořte v něm manifest služby s názvem `internal-lb.yaml` s typem služby a službou *Azure-Load Balancer – interní* anotaci, jak je znázorněno v následujícím příkladu: 

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

Po zobrazení podrobností služby se IP adresa interního nástroje pro vyrovnávání zatížení zobrazí ve sloupci *externí-IP* . V tomto kontextu je *externí* ve vztahu k externímu rozhraní nástroje pro vyrovnávání zatížení, a ne k tomu, že obdrží veřejnou externí IP adresu. Změna IP adresy z na skutečnou interní IP adresu může trvat minutu nebo dvě *\<pending\>* , jak je znázorněno v následujícím příkladu:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>Zadat IP adresu

Pokud chcete použít konkrétní IP adresu s interním nástrojem pro vyrovnávání zatížení, přidejte do manifestu YAML nástroje pro vyrovnávání zatížení vlastnost *loadBalancerIP* . V tomto scénáři se zadaná IP adresa musí nacházet ve stejné podsíti jako cluster AKS a nesmí být přiřazená k prostředku. Nepoužívejte například IP adresu v rozsahu určeném pro Kubernetes podsíť.

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

Další informace o konfiguraci nástroje pro vyrovnávání zatížení v jiné podsíti najdete v tématu [Určení jiné podsítě][different-subnet] .

## <a name="use-private-networks"></a>Použití privátních sítí

Při vytváření clusteru AKS můžete zadat Pokročilá nastavení sítě. Tento přístup umožňuje nasadit cluster do existující virtuální sítě Azure a podsítí. Jedním z scénářů je nasadit cluster AKS do privátní sítě připojené k místnímu prostředí a spouštět služby přístupné jenom interně. Další informace najdete v tématu Konfigurace vlastních podsítí virtuální sítě pomocí [Kubenet][use-kubenet] nebo [Azure CNI][advanced-networking].

Pro nasazení interního nástroje pro vyrovnávání zatížení v clusteru AKS, který používá privátní síť, není nutné provádět žádné změny v předchozích krocích. Nástroj pro vyrovnávání zatížení se vytvoří ve stejné skupině prostředků jako cluster AKS, ale připojil se k vaší privátní virtuální síti a podsíti, jak je znázorněno v následujícím příkladu:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Pro cluster AKS možná budete muset udělit identitu clusteru pro skupinu prostředků, ve *které se* nasazují vaše prostředky virtuální sítě Azure. Zobrazte identitu clusteru pomocí [AZ AKS show][az-aks-show], například `az aks show --resource-group myResourceGroup --name myAKSCluster --query "identity"` . Přiřazení role vytvoříte pomocí příkazu [AZ role Assignment Create][az-role-assignment-create] .

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

Můžete také přímo odstranit službu jako u libovolného prostředku Kubernetes, například `kubectl delete service internal-app` , který pak odstraní základní nástroj pro vyrovnávání zatížení Azure.

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
[azure-lb-comparison]: ../load-balancer/skus.md
[use-kubenet]: configure-kubenet.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[different-subnet]: #specify-a-different-subnet