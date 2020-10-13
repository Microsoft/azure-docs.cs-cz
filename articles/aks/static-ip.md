---
title: Použití statické IP adresy s nástrojem pro vyrovnávání zatížení
titleSuffix: Azure Kubernetes Service
description: Naučte se, jak vytvořit a používat statickou IP adresu pomocí nástroje pro vyrovnávání zatížení AKS (Azure Kubernetes Service).
services: container-service
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: 3055b5d32055d0ed0e3870f16f6af95407a68cd9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86243932"
---
# <a name="use-a-static-public-ip-address-and-dns-label-with-the-azure-kubernetes-service-aks-load-balancer"></a>Použití statické veřejné IP adresy a popisku DNS pomocí nástroje pro vyrovnávání zatížení AKS (Azure Kubernetes Service)

Ve výchozím nastavení je veřejná IP adresa přiřazená k prostředku nástroje pro vyrovnávání zatížení vytvořenému clusterem AKS platná jenom pro životnost tohoto prostředku. Při odstranění služby Kubernetes se odstraní také přidružená služba Vyrovnávání zatížení a IP adresa. Pokud chcete přiřadit konkrétní IP adresu nebo ponechat IP adresu pro znovu nasazené služby Kubernetes, můžete vytvořit a používat statickou veřejnou IP adresu.

V tomto článku se dozvíte, jak vytvořit statickou veřejnou IP adresu a přiřadit ji ke službě Kubernetes.

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, přečtěte si rychlý Start AKS a [použijte Azure CLI][aks-quickstart-cli] nebo [Azure Portal][aks-quickstart-portal].

Potřebujete také nainstalované a nakonfigurované rozhraní Azure CLI verze 2.0.59 nebo novější.  `az --version`Verzi zjistíte spuštěním. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI][install-azure-cli].

Tento článek se věnuje použití *standardní* IP adresy SKU s nástrojem pro vyrovnávání zatížení *Standard* SKU. Další informace najdete v tématu [typy IP adres a metody přidělování v Azure][ip-sku].

## <a name="create-a-static-ip-address"></a>Vytvořit statickou IP adresu

Pomocí příkazu [AZ Network Public IP Create][az-network-public-ip-create] vytvořte STATICKOU veřejnou IP adresu. V následujícím seznamu se vytvoří prostředek statických IP adres s názvem *myAKSPublicIP* ve skupině prostředků *myResourceGroup* :

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myAKSPublicIP \
    --sku Standard \
    --allocation-method static
```

> [!NOTE]
> Pokud používáte nástroj pro vyrovnávání zatížení *Basic* SKU v clusteru AKS, použijte při definování veřejné IP adresy hodnotu *Basic* pro parametr *SKU* . Pouze *základní* IP adresy SKU pracují s nástrojem pro vyrovnávání zatížení *Basic* SKU a pouze *standardní* IP adresa SKU fungují s nástroji pro vyrovnávání zatížení *standardních* SKU. 

Zobrazí se IP adresa, jak je znázorněno v následujícím zhuštěném příkladu výstupu:

```json
{
  "publicIp": {
    ...
    "ipAddress": "40.121.183.52",
    ...
  }
}
```

Veřejnou IP adresu můžete získat později pomocí příkazu [AZ Network Public-IP list][az-network-public-ip-list] . Zadejte název skupiny prostředků uzlu a veřejnou IP adresu, kterou jste vytvořili, a dotaz na adresu *ipAddress* , jak je znázorněno v následujícím příkladu:

```azurecli-interactive
$ az network public-ip show --resource-group myResourceGroup --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Vytvoření služby pomocí statické IP adresy

Než začnete vytvářet službu, ujistěte se, že instanční objekt používaný clusterem AKS má delegovaná oprávnění k jiné skupině prostředků. Například:

```azurecli-interactive
az role assignment create \
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

Případně můžete použít spravovanou identitu přiřazenou systémem pro oprávnění místo instančního objektu. Další informace najdete v tématu [použití spravovaných identit](use-managed-identity.md).

Chcete-li vytvořit službu *Vyrovnávání zatížení* se STATICKOU veřejnou IP adresou, přidejte do `loadBalancerIP` manifestu YAML vlastnost a hodnotu statické veřejné IP adresy. Vytvořte soubor s názvem `load-balancer-service.yaml` a zkopírujte ho na následující YAML. Zadejte vlastní veřejnou IP adresu vytvořenou v předchozím kroku. Následující příklad také nastaví anotaci na skupinu prostředků s názvem *myResourceGroup*. Zadejte název vlastní skupiny prostředků.

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-resource-group: myResourceGroup
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

Pomocí příkazu vytvořte službu a nasazení `kubectl apply` .

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="apply-a-dns-label-to-the-service"></a>Použití popisku DNS u služby

Pokud vaše služba používá dynamickou nebo statickou veřejnou IP adresu, můžete použít anotaci služby `service.beta.kubernetes.io/azure-dns-label-name` k nastavení popisku DNS s veřejným přístupem. Tím se publikuje plně kvalifikovaný název domény pro vaši službu pomocí veřejných serverů DNS Azure a domény nejvyšší úrovně. Hodnota anotace musí být jedinečná v rámci umístění Azure, proto se doporučuje použít dostatečně kvalifikovaný popisek.   

Azure pak automaticky připojí výchozí podsíť, například `<location>.cloudapp.azure.com` (kde umístění je oblast, kterou jste zvolili), k názvu, který zadáte, k vytvoření plně kvalifikovaného názvu DNS. Například:

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-dns-label-name: myserviceuniquelabel
  name: azure-load-balancer
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

> [!NOTE] 
> Pokud chcete publikovat službu ve vlastní doméně, přečtěte si téma [Azure DNS][azure-dns-zone] a Project [External-DNS][external-dns] .

## <a name="troubleshoot"></a>Řešení potíží

Pokud statická IP adresa definovaná ve vlastnosti *loadBalancerIP* manifestu služby Kubernetes neexistuje nebo se nevytvořila v rámci skupiny prostředků uzlu a nejsou nakonfigurované žádné další delegování, vytvoření služby Vyrovnávání zatížení se nepovede. Pokud chcete řešit potíže, Projděte si události vytvoření služby pomocí příkazu [kubectl popsat][kubectl-describe] . Zadejte název služby, jak je uvedeno v manifestu YAML, jak je znázorněno v následujícím příkladu:

```console
kubectl describe service azure-load-balancer
```

Zobrazí se informace o prostředku služby Kubernetes. *Události* na konci následujícího ukázkového výstupu označují, že se *nenašla IP adresa zadaná uživatelem*. V těchto scénářích ověřte, že jste ve skupině prostředků uzlu vytvořili statickou veřejnou IP adresu a že IP adresa zadaná v manifestu služby Kubernetes je správná.

```
Name:                     azure-load-balancer
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=azure-load-balancer
Type:                     LoadBalancer
IP:                       10.0.18.125
IP:                       40.121.183.52
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  32582/TCP
Endpoints:                <none>
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type     Reason                      Age               From                Message
  ----     ------                      ----              ----                -------
  Normal   CreatingLoadBalancer        7s (x2 over 22s)  service-controller  Creating load balancer
  Warning  CreatingLoadBalancerFailed  6s (x2 over 12s)  service-controller  Error creating load balancer (will retry): Failed to create load balancer for service default/azure-load-balancer: user supplied IP Address 40.121.183.52 was not found
```

## <a name="next-steps"></a>Další kroky

Pro lepší kontrolu nad síťovým přenosem do aplikací můžete místo toho [vytvořit kontroler příchozího][aks-ingress-basic]přenosu dat. Můžete také [vytvořit kontroler příchozího přenosu dat se statickou veřejnou IP adresou][aks-static-ingress].

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[azure-dns-zone]: https://azure.microsoft.com/services/dns/
[external-dns]: https://github.com/kubernetes-sigs/external-dns

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[aks-ingress-basic]: ingress-basic.md
[aks-static-ingress]: ingress-static-ip.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ip-sku]: ../virtual-network/public-ip-addresses.md#sku
