---
title: Použití statické IP adresy s vyvažovačem zatížení
titleSuffix: Azure Kubernetes Service
description: Zjistěte, jak vytvořit a používat statickou IP adresu pomocí služby Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: 5051232f29ad51d9fee893a4a660fc81f6e60d77
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886734"
---
# <a name="use-a-static-public-ip-address-and-dns-label-with-the-azure-kubernetes-service-aks-load-balancer"></a>Použití statické veřejné IP adresy a popisku DNS s balancerem služby Azure Kubernetes Service (AKS)

Ve výchozím nastavení je veřejná IP adresa přiřazená prostředku vykladače zatížení vytvořenému clusterem AKS platná pouze po dobu životnosti tohoto prostředku. Pokud odstraníte službu Kubernetes, odstraní se také přidružený systém vyrovnávání zatížení a adresa IP. Pokud chcete přiřadit určitou IP adresu nebo zachovat IP adresu pro přesazené služby Kubernetes, můžete vytvořit a použít statickou veřejnou IP adresu.

Tento článek ukazuje, jak vytvořit statickou veřejnou IP adresu a přiřadit ji k vaší službě Kubernetes.

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, podívejte se na aks rychlý start [pomocí Azure CLI][aks-quickstart-cli] nebo [pomocí portálu Azure][aks-quickstart-portal].

Potřebujete také nainstalované a nakonfigurované verze Azure CLI verze 2.0.59 nebo novější. Spuštěním `az --version` najděte verzi. Pokud potřebujete nainstalovat nebo upgradovat, přečtěte si informace [o instalaci příkazového příkazového příkazu k webu Azure][install-azure-cli].

Tento článek popisuje použití *standardní* sku IP se *standardním* vyrovnáváním zatížení skladové položky. Další informace najdete v tématu [typy IP adres a metody přidělení v Azure][ip-sku].

## <a name="create-a-static-ip-address"></a>Vytvoření statické adresy IP

Vytvořte statickou veřejnou IP adresu pomocí příkazu [az network public ip create.][az-network-public-ip-create] Následující vytvoří statický prostředek IP s názvem *myAKSPublicIP* ve skupině prostředků *myResourceGroup:*

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myAKSPublicIP \
    --sku Standard \
    --allocation-method static
```

> [!NOTE]
> Pokud používáte *základní* vyrovnávání zatížení skladové položky v clusteru AKS, použijte *basic* pro *sku* parametr při definování veřejné IP. Pouze *základní* ip adresy skladových sku pracují se *základním* účetním vytížením skladových položk a pouze *standardní* ip adresy skladových položk pracují se *standardními* výčitek zatížení skladových položk. 

Zobrazí se IP adresa, jak je znázorněno na následujícím kondenzovaném příkladu výstupu:

```json
{
  "publicIp": {
    ...
    "ipAddress": "40.121.183.52",
    ...
  }
}
```

Později můžete získat veřejnou IP adresu pomocí příkazu [az network public-ip list.][az-network-public-ip-list] Zadejte název skupiny prostředků uzlu a veřejnou IP adresu, kterou jste vytvořili, a dotaz na *adresu IPAddress,* jak je znázorněno v následujícím příkladu:

```azurecli-interactive
$ az network public-ip show --resource-group myResourceGroup --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Vytvoření služby pomocí statické adresy IP

Před vytvořením služby zajistěte, aby instanční objekt používaný clusterem AKS delegoval oprávnění k jiné skupině prostředků. Příklad:

```azurecli-interactive
az role assignment create \
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

Případně můžete použít systém přiřazenou spravovanou identitu pro oprávnění namísto instančního objektu. Další informace naleznete v tématu [Použití spravovaných identit](use-managed-identity.md).

Chcete-li vytvořit službu *LoadBalancer* se statickou veřejnou IP adresou, přidejte `loadBalancerIP` vlastnost a hodnotu statické veřejné IP adresy do manifestu YAML. Vytvořte soubor `load-balancer-service.yaml` s názvem a zkopírujte v následujícím yaml. Zadejte vlastní veřejnou IP adresu vytvořenou v předchozím kroku. Následující příklad také nastaví poznámku ke skupině prostředků s názvem *myResourceGroup*. Zadejte svůj vlastní název skupiny prostředků.

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

Vytvořte službu a `kubectl apply` nasazení pomocí příkazu.

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="apply-a-dns-label-to-the-service"></a>Použití popisku DNS u služby

Pokud vaše služba používá dynamickou nebo statickou veřejnou IP `service.beta.kubernetes.io/azure-dns-label-name` adresu, můžete pomocí anotace služby nastavit veřejný popisek DNS. Tím se publikuje plně kvalifikovaný název domény pro vaši službu pomocí veřejných serverů DNS Azure a domény nejvyšší úrovně. Hodnota poznámky musí být v umístění Azure jedinečná, takže doporučujeme použít dostatečně kvalifikovaný popisek.   

Azure pak automaticky připojí výchozí podsíť, `<location>.cloudapp.azure.com` například (kde umístění je oblast, kterou jste vybrali), k názvu, který zadáte, k vytvoření plně kvalifikovaného názvu DNS. Příklad:

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
> Pokud chcete publikovat službu ve vlastní doméně, přečtěte si informace o [Azure DNS][azure-dns-zone] a projektu [externího DNS.][external-dns]

## <a name="troubleshoot"></a>Řešení potíží

Pokud statická adresa IP definovaná ve vlastnosti *loadBalancerIP* manifestu služby Kubernetes neexistuje nebo nebyla vytvořena ve skupině prostředků uzlu a nejsou nakonfigurována žádná další delegace, vytvoření služby vyrovnávání zatížení se nezdaří. Chcete-li vyřešit potíže, zkontrolujte události vytvoření služby pomocí [příkazu kubectl describe.][kubectl-describe] Zadejte název služby, jak je uvedeno v manifestu YAML, jak je znázorněno v následujícím příkladu:

```console
kubectl describe service azure-load-balancer
```

Zobrazí se informace o prostředku služby Kubernetes. *Události* na konci následujícího příkladu výstupu označují, že *uživatel zadaný IP adresa nebyla nalezena*. V těchto scénářích ověřte, zda jste vytvořili statickou veřejnou IP adresu ve skupině prostředků uzlu a zda je adresa IP zadaná v manifestu služby Kubernetes správná.

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

Chcete-li získat další kontrolu nad síťovým provozem aplikací, můžete místo toho [vytvořit řadič příchozího přenosu dat][aks-ingress-basic]. Můžete také [vytvořit řadič příchozího přenosu dat se statickou veřejnou IP adresou][aks-static-ingress].

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
[ip-sku]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md#sku
