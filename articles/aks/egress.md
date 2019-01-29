---
title: Statická IP adresa pro výchozí přenos ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak vytvořit a používat statickou veřejnou IP adresu pro výchozí přenos v clusteru služby Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 09/26/2018
ms.author: iainfou
ms.openlocfilehash: aeffe172fd422f18e2828c5274e9a2ed13cc546a
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55103356"
---
# <a name="use-a-static-public-ip-address-for-egress-traffic-in-azure-kubernetes-service-aks"></a>Používat statickou veřejnou IP adresu pro výchozí přenos ve službě Azure Kubernetes Service (AKS)

Ve výchozím nastavení se náhodně přiřadí IP adresu výchozí přenos dat z clusteru služby Azure Kubernetes Service (AKS). Tato konfigurace není ideální, pokud je potřeba identifikovat IP adresu pro přístup k externím službám, třeba. Místo toho budete muset přiřadit statickou IP adresu, kterou lze přidat na seznam povolených pro přístup k službě.

V tomto článku se dozvíte, jak vytvořit a používat statickou veřejnou IP adresu pro použití s výchozí přenos v clusteru AKS.

## <a name="before-you-begin"></a>Před zahájením

Tento článek předpokládá, že máte existující cluster AKS. Pokud potřebujete AKS cluster, najdete v tomto rychlém startu AKS [pomocí Azure CLI] [ aks-quickstart-cli] nebo [pomocí webu Azure portal][aks-quickstart-portal].

Také musíte mít nainstalované a nakonfigurované rozhraní Azure CLI verze 2.0.46 nebo novější. Spustit `az --version` k vyhledání verze. Pokud potřebujete instalaci nebo upgrade, naleznete v tématu [instalace Azure CLI][install-azure-cli].

## <a name="egress-traffic-overview"></a>Přehled provozu odchozího přenosu dat

Následuje odchozího provozu z clusteru AKS [Azure Load Balancer konvence][outbound-connections]. Před první služby Kubernetes typu `LoadBalancer` vytvoření agenta uzly v clusteru AKS, které nejsou součástí žádné fondu nástroje pro vyrovnávání zatížení Azure. V této konfiguraci uzlů úroveň žádné instance veřejnou IP adresu. Azure se přeloží odchozí tok veřejné Zdrojová IP adresa, která není konfigurovatelné nebo deterministický.

Jakmile služba Kubernetes typu `LoadBalancer` se vytvoří, uzly jsou přidány do fondu služby Azure Load Balancer agenta. Pro odchozí tok Azure převede do první veřejnou IP adresou nakonfigurovanou na nástroj pro vyrovnávání zatížení. Tato veřejná IP adresa platí pouze pro životnosti prostředku. Při odstranění služby Kubernetes nástroj pro vyrovnávání zatížení, se také odstraní přidružené vyrovnávání zátěže a IP adresu. Pokud chcete přiřadit konkrétní IP adresu nebo zachovat IP adresu pro opakovaně nasazeném služby Kubernetes, můžete vytvořit a používat statickou veřejnou IP adresu.

## <a name="create-a-static-public-ip"></a>Vytvoření statické veřejné IP adresy

Při vytváření statickou veřejnou IP adresu pro použití službou AKS prostředek IP adresy musí být vytvořeny ve **uzel** skupinu prostředků. Získání názvu skupiny prostředků s [az aks zobrazit] [ az-aks-show] příkaz a přidejte `--query nodeResourceGroup` parametr dotazu. Následující příklad získá uzlu skupiny prostředků pro AKS název clusteru *myAKSCluster* v názvu skupiny prostředků *myResourceGroup*:

```azurecli
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Teď Vytvořte statickou veřejnou IP adresu s [vytvoření veřejné IP adresy sítě az] [ az-network-public-ip-create] příkazu. Zadejte název uzlu skupiny prostředků získané v předchozím příkazu, a potom název pro IP adresu vztahují prostředků, jako *myAKSPublicIP*:

```azurecli
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

IP adresa se zobrazí, jak je znázorněno v následujícím výstupu zhuštěnému příkladu:

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

Později můžete získat na veřejných IP adres pomocí [az network public-ip list] [ az-network-public-ip-list] příkazu. Zadejte název skupiny prostředků uzel a potom dotázat *ipAddress* jak je znázorněno v následujícím příkladu:

```azurecli
$ az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-with-the-static-ip"></a>Vytvoření služby pomocí statické IP adresy

Chcete-li vytvořit službu se statickou veřejnou IP adresu, přidejte `loadBalancerIP` vlastnosti a hodnotu statickou veřejnou IP Adresou adres do manifestu YAML. Vytvořte soubor s názvem `egress-service.yaml` a zkopírujte do následující kód YAML. Zadejte vlastní veřejnou IP adresu vytvořenou v předchozím kroku.

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

Vytvoření služby a nasazení se `kubectl apply` příkazu.

```console
kubectl apply -f egress-service.yaml
```

Tato služba v Azure Load Balancer nakonfiguruje novou IP adresu front-endu. Pokud nemáte jiné IP adresy nakonfigurován, pak **všechny** výchozí přenos byste teď měli použít tuto adresu. Při více adres jsou nakonfigurované na Azure Load Balancer, výchozí přenos dat používá první IP adresa na tento nástroj pro vyrovnávání zatížení.

## <a name="verify-egress-address"></a>Zkontrolujte výstupní adresou

Chcete-li ověřit, že se používá statickou veřejnou IP adresu, vyhledávací služba DNS můžete použít například `checkip.dyndns.org`.

Spuštění a připojení k základní *Debian* podu:

```console
kubectl run -it --rm aks-ip --image=debian
```

Chcete-li získat přístup k webu z v rámci kontejneru, použijte `apt-get` instalace `curl` do kontejneru.

```console
apt-get update && apt-get install curl -y
```

Teď používáme nástroj curl k přístupu *checkip.dyndns.org* lokality. Odchozí IP adresa se zobrazí, jak se zobrazuje v následujícím příkladu výstupu. Tato IP adresa shoduje statickou veřejnou IP adresu vytvoří a definované pro nástroj pro vyrovnávání zatížení služby:

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 23.101.128.81</body></html>
```

## <a name="next-steps"></a>Další postup

Pokud chcete vyhnout, udržování víc veřejných IP adres v nástroji pro vyrovnávání zatížení Azure, můžete místo toho používat kontroler příchozího přenosu dat. Řadiče příchozího přenosu dat poskytuje další výhody, jako je například ukončení protokolu SSL/TLS, podporu pro identifikátor URI přepisů a nadřazeného šifrování SSL/TLS. Další informace najdete v tématu [vytvoříte řadič základního příchozího přenosu dat ve službě AKS][ingress-aks-cluster].

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