---
title: Použití statické IP adresy pro odchozí přenosy
titleSuffix: Azure Kubernetes Service
description: Zjistěte, jak vytvořit a používat statickou veřejnou IP adresu pro odchozí provoz v clusteru Služby Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 03/04/2019
ms.openlocfilehash: 08a9682434605fffde73c835e7a9e9d6971d7ff0
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803378"
---
# <a name="use-a-static-public-ip-address-for-egress-traffic-in-azure-kubernetes-service-aks"></a>Použití statické veřejné IP adresy pro odchozí provoz ve službě Azure Kubernetes Service (AKS)

Ve výchozím nastavení je náhodně přiřazena e-výstupní IP adresa z clusteru služby Azure Kubernetes Service (AKS). Tato konfigurace není ideální, pokud potřebujete například identifikovat IP adresu pro přístup k externím službám. Místo toho může být nutné přiřadit statickou adresu IP, která může být uvedena na seznamu povolených pro přístup ke službě.

Tento článek ukazuje, jak vytvořit a použít statickou veřejnou IP adresu pro použití s odchozími přenosy v clusteru AKS.

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, podívejte se na aks rychlý start [pomocí Azure CLI][aks-quickstart-cli] nebo [pomocí portálu Azure][aks-quickstart-portal].

Potřebujete také nainstalované a nakonfigurované verze Azure CLI verze 2.0.59 nebo novější. Spuštěním `az --version` najděte verzi. Pokud potřebujete nainstalovat nebo upgradovat, přečtěte si informace [o instalaci příkazového příkazového příkazu k webu Azure][install-azure-cli].

## <a name="egress-traffic-overview"></a>Přehled odchozího provozu

Odchozí provoz z clusteru AKS se řídí [konvencemi Azure Load Balancer][outbound-connections]. Před vytvořením první služby Kubernetes typu `LoadBalancer` nejsou uzly agenta v clusteru AKS součástí žádného fondu Azure Load Balancer. V této konfiguraci uzly nemají žádnou úroveň instance veřejné IP adresy. Azure převede odchozí tok na veřejnou zdrojovou IP adresu, která není konfigurovatelná nebo deterministická.

Po vytvoření služby Kubernetes typu `LoadBalancer` jsou uzly agentů přidány do fondu Azure Load Balancer. Pro odchozí tok Azure překládá na první veřejnou IP adresu nakonfigurovanou v zařízení pro vyrovnávání zatížení. Tato veřejná IP adresa je platná pouze po dobu životnosti tohoto prostředku. Pokud odstraníte službu LoadBalancer Kubernetes, odstraní se také přidružený systém vyrovnávání zatížení a adresa IP. Pokud chcete přiřadit určitou IP adresu nebo zachovat IP adresu pro přesazené služby Kubernetes, můžete vytvořit a použít statickou veřejnou IP adresu.

## <a name="create-a-static-public-ip"></a>Vytvoření statické veřejné IP adresy

Získejte název skupiny prostředků pomocí příkazu [az aks show][az-aks-show] a přidejte parametr dotazu. `--query nodeResourceGroup` Následující příklad získá skupinu prostředků uzlu pro název clusteru AKS *myAKSCluster* v názvu skupiny prostředků *myResourceGroup*:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Nyní vytvořte statickou veřejnou IP adresu s příkazem [az sítě public ip create.][az-network-public-ip-create] Zadejte název skupiny prostředků uzlu získaný v předchozím příkazu a potom název prostředku adresy IP, například *myAKSPublicIP*:

```azurecli-interactive
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

Ip adresa je zobrazena tak, jak je znázorněno na následujícím kondenzovaném příkladu výstupu:

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

Později můžete získat veřejnou IP adresu pomocí příkazu [az network public-ip list.][az-network-public-ip-list] Zadejte název skupiny prostředků uzlu a potom zadejte dotaz na *adresu IPAddress,* jak je znázorněno v následujícím příkladu:

```azurecli-interactive
$ az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-with-the-static-ip"></a>Vytvoření služby se statickou IP adresou

Chcete-li vytvořit službu se statickou `loadBalancerIP` veřejnou IP adresou, přidejte vlastnost a hodnotu statické veřejné IP adresy do manifestu YAML. Vytvořte soubor `egress-service.yaml` s názvem a zkopírujte v následujícím yaml. Zadejte vlastní veřejnou IP adresu vytvořenou v předchozím kroku.

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

Vytvořte službu a `kubectl apply` nasazení pomocí příkazu.

```console
kubectl apply -f egress-service.yaml
```

Tato služba konfiguruje novou front-endovou IP adresu v Azure Load Balancer. Pokud nemáte žádné jiné IP adresy nakonfigurované, pak **všechny** odchozí přenosy by nyní měly používat tuto adresu. Když více adres jsou nakonfigurovány na Azure Load Balancer, odchozí použití používá první IP na tento vyrovnávání zatížení.

## <a name="verify-egress-address"></a>Ověřit adresu výstupu

Chcete-li ověřit, zda je používána statická veřejná adresa `checkip.dyndns.org`IP, můžete použít službu vyhledávání DNS, například .

Spuštění a připojení k základnímu *modulu Debian:*

```console
kubectl run -it --rm aks-ip --image=debian --generator=run-pod/v1
```

Chcete-li získat přístup k webu `apt-get` z `curl` kontejneru, použijte k instalaci do kontejneru.

```console
apt-get update && apt-get install curl -y
```

Nyní použijte curl pro přístup k *webu checkip.dyndns.org.* Adresa IP odchozího přenosu je zobrazena tak, jak je zobrazena v následujícím příkladu výstupu. Tato adresa IP odpovídá statické veřejné IP adrese vytvořené a definované pro službu loadBalancer:

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 40.121.183.52</body></html>
```

## <a name="next-steps"></a>Další kroky

Chcete-li se vyhnout udržování více veřejných IP adres v Azure Load Balancer, můžete místo toho použít řadič příchozího přenosu dat. Řadiče příchozího přenosu dat poskytují další výhody, jako je například ukončení SSL/TLS, podpora přepsání identifikátoru URI a šifrování ssl/tls proti proudu. Další informace naleznete [v tématu Vytvoření základního řadiče příchozího přenosu dat v AKS][ingress-aks-cluster].

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
