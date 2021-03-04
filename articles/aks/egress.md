---
title: Použití statické IP adresy pro odchozí přenosy
titleSuffix: Azure Kubernetes Service
description: Naučte se vytvářet a používat statickou veřejnou IP adresu pro odchozí přenosy v clusteru Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/04/2019
ms.openlocfilehash: 2eefeecfa550683dafcf66d936837e2a891c4c84
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101726542"
---
# <a name="use-a-static-public-ip-address-for-egress-traffic-with-a-basic-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Použití statické veřejné IP adresy pro odchozí přenosy se *základní* službou pro vyrovnávání zatížení ve službě Azure Kubernetes Service (AKS)

Ve výchozím nastavení je odchozí IP adresa z clusteru Azure Kubernetes Service (AKS) náhodně přiřazená. Tato konfigurace není ideální, pokud potřebujete identifikovat IP adresu pro přístup k externím službám, například. Místo toho možná budete muset přiřadit statickou IP adresu, kterou chcete přidat do seznamu povolených adres pro přístup k službě.

V tomto článku se dozvíte, jak vytvořit a používat statickou veřejnou IP adresu pro použití s odchozími přenosy v clusteru AKS.

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že používáte Azure Basic Load Balancer.  Doporučujeme používat [Standard Load Balancer Azure](../load-balancer/load-balancer-overview.md)a můžete využít pokročilejší funkce pro [řízení odchozího provozu AKS](./limit-egress-traffic.md).

V tomto článku se předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, přečtěte si rychlý Start AKS a [použijte Azure CLI][aks-quickstart-cli] nebo [Azure Portal][aks-quickstart-portal].

Potřebujete také nainstalované a nakonfigurované rozhraní Azure CLI verze 2.0.59 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

> [!IMPORTANT]
> Tento článek používá nástroj pro vyrovnávání zatížení *Basic* SKU s jedním fondem uzlů. Tato konfigurace není k dispozici pro více fondů uzlů, protože nástroj pro vyrovnávání zatížení *Basic* SKU není u více fondů uzlů podporován. Další informace o používání nástroje pro vyrovnávání zatížení *Standard* SKU najdete v tématu [použití veřejné Standard Load Balancer ve službě Azure KUBERNETES Service (AKS)][slb] .

## <a name="egress-traffic-overview"></a>Přehled odchozího provozu

Odchozí provoz z clusteru AKS se řídí [konvencemi Azure Load Balancer][outbound-connections]. Před vytvořením první služby Kubernetes typu `LoadBalancer` nebude uzel agenta v clusteru AKS součástí žádného fondu Azure Load Balancer. V této konfiguraci uzly nemají veřejnou IP adresu na úrovni instance. Azure překládá odchozí tok do veřejné zdrojové IP adresy, která není konfigurovatelná ani deterministické.

Jakmile se vytvoří služba Kubernetes typu `LoadBalancer` , přidají se uzly agentů do fondu Azure Load Balancer. Load Balancer Basic zvolí jeden front-end, který se má použít pro odchozí toky, pokud jsou kandidáti na odchozí toky více (veřejných) front-endu IP. Tento výběr není možné konfigurovat a měli byste zvážit, že je algoritmus výběru náhodný. Tato veřejná IP adresa je platná jenom pro životnost tohoto prostředku. Odstraníte-li službu Kubernetes vyrovnávání zatížení sítě, bude odstraněna také přidružená služba Vyrovnávání zatížení a IP adresa. Pokud chcete přiřadit konkrétní IP adresu nebo ponechat IP adresu pro znovu nasazené služby Kubernetes, můžete vytvořit a používat statickou veřejnou IP adresu.

## <a name="create-a-static-public-ip"></a>Vytvoření statické veřejné IP adresy

Název skupiny prostředků získáte pomocí příkazu [AZ AKS show][az-aks-show] a přidejte `--query nodeResourceGroup` parametr dotazu. Následující příklad načte skupinu prostředků uzlu pro název clusteru AKS *myAKSCluster* v názvu skupiny prostředků *myResourceGroup*:

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
    [..]
  }
```

Veřejnou IP adresu můžete získat později pomocí příkazu [AZ Network Public-IP list][az-network-public-ip-list] . Zadejte název skupiny prostředků uzlu a potom zadejte dotaz na *adresu ipAddress* , jak je znázorněno v následujícím příkladu:

```azurecli-interactive
$ az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-with-the-static-ip"></a>Vytvoření služby se statickou IP adresou

Chcete-li vytvořit službu se statickou veřejnou IP adresou, přidejte do `loadBalancerIP` MANIFESTU YAML vlastnost a hodnotu statické veřejné IP adresy. Vytvořte soubor s názvem `egress-service.yaml` a zkopírujte ho na následující YAML. Zadejte vlastní veřejnou IP adresu vytvořenou v předchozím kroku.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-egress
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
```

Pomocí příkazu vytvořte službu a nasazení `kubectl apply` .

```console
kubectl apply -f egress-service.yaml
```

Tato služba konfiguruje novou front-end IP adresu na Azure Load Balancer. Pokud nemáte nakonfigurovanou žádnou jinou IP adresu, měla by nyní **všechny** přenosy dat používat. Pokud je v Azure Load Balancer nakonfigurovaných víc adres, každá z těchto veřejných IP adres je kandidátem na odchozí toky a jedna se vybere náhodně.

## <a name="verify-egress-address"></a>Ověřit výstupní adresu

Pokud chcete ověřit, že se používá statická veřejná IP adresa, můžete použít službu vyhledávání DNS, jako je například `checkip.dyndns.org` .

Začněte a připojte se k základnímu *Debian* pod:

```console
kubectl run -it --rm aks-ip --image=debian
```

Chcete-li získat přístup k webu z kontejneru, použijte příkaz `apt-get` pro instalaci `curl` do kontejneru.

```console
apt-get update && apt-get install curl -y
```

Teď k přístupu k webu *checkip.dyndns.org* použijte kudrlinkou. Zobrazí se výstupní IP adresa, jak je znázorněno v následujícím příkladu výstupu. Tato IP adresa se shoduje se statickou veřejnou IP adresou vytvořenou a definovanou pro službu Vyrovnávání zatížení:

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 40.121.183.52</body></html>
```

## <a name="next-steps"></a>Další kroky

Abyste se vyhnuli udržování více veřejných IP adres na Azure Load Balancer, můžete místo toho použít kontroler příchozího přenosu dat. Řadiče příchozího přenosu dat poskytují další výhody, jako je ukončení protokolu SSL/TLS, podpora přepsání identifikátoru URI a nadřazeného šifrování SSL/TLS. Další informace najdete v tématu [Vytvoření základního kontroleru][ingress-aks-cluster]příchozího přenosu v AKS.

<!-- LINKS - internal -->
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[azure-cli-install]: /cli/azure/install-azure-cli
[ingress-aks-cluster]: ./ingress-basic.md
[outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#scenarios
[public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[slb]: load-balancer-standard.md
