---
title: Použití statické IP adresy se službou Azure Kubernetes Service (AKS) pro vyrovnávání zatížení
description: Naučte se, jak vytvořit a používat statickou IP adresu pomocí nástroje pro vyrovnávání zatížení AKS (Azure Kubernetes Service).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/04/2019
ms.author: mlearned
ms.openlocfilehash: 9e32715766734bcbb150d70aeed2dc5b06a4bcbb
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "67614468"
---
# <a name="use-a-static-public-ip-address-with-the-azure-kubernetes-service-aks-load-balancer"></a>Použití statické veřejné IP adresy se službou Azure Kubernetes Service (AKS) pro vyrovnávání zatížení

Ve výchozím nastavení je veřejná IP adresa přiřazená k prostředku nástroje pro vyrovnávání zatížení vytvořenému clusterem AKS platná jenom pro životnost tohoto prostředku. Při odstranění služby Kubernetes se odstraní také přidružená služba Vyrovnávání zatížení a IP adresa. Pokud chcete přiřadit konkrétní IP adresu nebo ponechat IP adresu pro znovu nasazené služby Kubernetes, můžete vytvořit a používat statickou veřejnou IP adresu.

V tomto článku se dozvíte, jak vytvořit statickou veřejnou IP adresu a přiřadit ji ke službě Kubernetes.

## <a name="before-you-begin"></a>Před zahájením

V tomto článku se předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, přečtěte si rychlý Start AKS a [použijte Azure CLI][aks-quickstart-cli] nebo [Azure Portal][aks-quickstart-portal].

Potřebujete také nainstalované a nakonfigurované rozhraní Azure CLI verze 2.0.59 nebo novější. Verzi `az --version` zjistíte spuštěním. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI][install-azure-cli].

V současné době je podporovaná jenom *základní SKU IP*. V práci probíhá podpora standardní SKU prostředku *IP* . Další informace najdete v tématu [typy IP adres a metody přidělování v Azure][ip-sku].

## <a name="create-a-static-ip-address"></a>Vytvořit statickou IP adresu

Při vytváření statické veřejné IP adresy pro použití s AKS by se měl prostředek IP adresy vytvořit v rámci skupiny prostředků **uzlu** . Pokud chcete prostředky oddělit, v následující části [použijte STATICKOU IP adresu mimo skupinu prostředků uzlu](#use-a-static-ip-address-outside-of-the-node-resource-group).

Nejprve Získejte název skupiny prostředků uzlu pomocí příkazu [AZ AKS show][az-aks-show] a přidejte `--query nodeResourceGroup` parametr dotazu. Následující příklad načte skupinu prostředků uzlu pro název clusteru AKS *myAKSCluster* v názvu skupiny prostředků *myResourceGroup*:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Teď Vytvořte statickou veřejnou IP adresu pomocí příkazu [AZ Network Public IP Create][az-network-public-ip-create] . Zadejte název skupiny prostředků uzlu získaný v předchozím příkazu a potom název prostředku IP adresy, například *myAKSPublicIP*:

```azurecli-interactive
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

Zobrazí se IP adresa, jak je znázorněno v následujícím zhuštěném příkladu výstupu:

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    [...]
  }
}
```

Veřejnou IP adresu můžete získat později pomocí příkazu [AZ Network Public-IP list][az-network-public-ip-list] . Zadejte název skupiny prostředků uzlu a veřejnou IP adresu, kterou jste vytvořili, a dotaz na adresu *ipAddress* , jak je znázorněno v následujícím příkladu:

```azurecli-interactive
$ az network public-ip show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Vytvoření služby pomocí statické IP adresy

Chcete-li vytvořit službu se statickou veřejnou IP adresou, přidejte `loadBalancerIP` do manifestu YAML vlastnost a hodnotu statické veřejné IP adresy. Vytvořte soubor s názvem `load-balancer-service.yaml` a zkopírujte ho na následující YAML. Zadejte vlastní veřejnou IP adresu vytvořenou v předchozím kroku.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

Pomocí `kubectl apply` příkazu vytvořte službu a nasazení.

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="use-a-static-ip-address-outside-of-the-node-resource-group"></a>Použít statickou IP adresu mimo skupinu prostředků uzlu

V Kubernetes 1,10 nebo novějším můžete použít statickou IP adresu, která je vytvořená mimo skupinu prostředků uzlu. Instanční objekt používaný clusterem AKS musí mít delegovaná oprávnění k druhé skupině prostředků, jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az role assignment create\
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

Pokud chcete použít IP adresu mimo skupinu prostředků uzlu, přidejte anotaci do definice služby. Následující příklad nastaví anotaci na skupinu prostředků s názvem *myResourceGroup*. Zadejte název vlastní skupiny prostředků:

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

## <a name="next-steps"></a>Další postup

Pro lepší kontrolu nad síťovým přenosem do aplikací můžete místo toho [vytvořit kontroler příchozího][aks-ingress-basic]přenosu dat. Můžete také vytvořit kontroler příchozího přenosu dat [se statickou veřejnou IP adresou][aks-static-ingress].

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

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
[ip-sku]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md#sku
