---
title: Statické IP adresy pomocí nástroje pro vyrovnávání zatížení Azure Kubernetes Service (AKS)
description: Zjistěte, jak vytvořit a používat statickou IP adresu nástroje pro vyrovnávání zatížení Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 09/26/2018
ms.author: iainfou
ms.openlocfilehash: 5ac64f61d1c7879c578a1b6994a6918a7aa28a2e
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53017044"
---
# <a name="use-a-static-public-ip-address-with-the-azure-kubernetes-service-aks-load-balancer"></a>Statické veřejné IP adresy pomocí nástroje pro vyrovnávání zatížení Azure Kubernetes Service (AKS)

Ve výchozím nastavení veřejná IP adresa přidělená k prostředku nástroje pro vyrovnávání zatížení vytvořil AKS cluster platí pouze pro životnosti prostředku. Při odstranění služby Kubernetes, se také odstraní přidružené vyrovnávání zátěže a IP adresu. Pokud chcete přiřadit konkrétní IP adresu nebo zachovat IP adresu pro opakovaně nasazeném služby Kubernetes, můžete vytvořit a používat statickou veřejnou IP adresu.

V tomto článku se dozvíte, jak vytvořit statickou veřejnou IP adresu a přiřaďte ho do služby Kubernetes.

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte existující cluster AKS. Pokud potřebujete AKS cluster, najdete v tomto rychlém startu AKS [pomocí Azure CLI] [ aks-quickstart-cli] nebo [pomocí webu Azure portal][aks-quickstart-portal].

Také musíte mít nainstalované a nakonfigurované rozhraní Azure CLI verze 2.0.46 nebo novější. Spustit `az --version` k vyhledání verze. Pokud potřebujete instalaci nebo upgrade, naleznete v tématu [instalace Azure CLI][install-azure-cli].

## <a name="create-a-static-ip-address"></a>Vytvoření statické IP adresy

Při vytváření statickou veřejnou IP adresu pro použití službou AKS prostředek IP adresy mají být vytvořeny v **uzel** skupinu prostředků. Pokud chcete samostatné prostředky, najdete v článku [použijte statickou IP adresu mimo skupinu prostředků uzel](#use-a-static-ip-address-outside-of-the-node-resource-group).

Získání názvu uzlu skupiny prostředků s [az aks zobrazit] [ az-aks-show] příkaz a přidejte `--query nodeResourceGroup` parametr dotazu. Následující příklad získá uzlu skupiny prostředků pro AKS název clusteru *myAKSCluster* v názvu skupiny prostředků *myResourceGroup*:

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
    [...]
  }
```

Později můžete získat na veřejných IP adres pomocí [az network public-ip list] [ az-network-public-ip-list] příkazu. Zadejte název uzlu skupiny prostředků a veřejné IP adresy, které jste vytvořili a dotaz *ipAddress* jak je znázorněno v následujícím příkladu:

```azurecli
$ az network public-ip show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Vytvoření služby pomocí statické IP adresy

Chcete-li vytvořit službu se statickou veřejnou IP adresu, přidejte `loadBalancerIP` vlastnosti a hodnotu statickou veřejnou IP Adresou adres do manifestu YAML. Vytvořte soubor s názvem `load-balancer-service.yaml` a zkopírujte do následující kód YAML. Zadejte vlastní veřejnou IP adresu vytvořenou v předchozím kroku.

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

Vytvoření služby a nasazení se `kubectl apply` příkazu.

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="use-a-static-ip-address-outside-of-the-node-resource-group"></a>Použijte statickou IP adresu mimo uzel skupiny prostředků

S Kubernetes 1.10 nebo novější můžete použít statické IP adresy, který je vytvořen mimo uzel skupiny prostředků. Instanční objekt používané clusterem AKS musí mít delegovaná oprávnění k jiné skupině prostředků, jak je znázorněno v následujícím příkladu:

```azurecli
az role assignment create\
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

Pokud chcete použít IP adresu mimo uzel skupiny prostředků, přidáte poznámku k její definici. Následující příklad nastaví anotaci do skupiny prostředků s názvem *myResourceGroup*. Zadejte vlastní název skupiny prostředků:

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

Pokud podle statickou IP adresu *loadBalancerIP* vlastnost service manifest Kubernetesu neexistuje nebo není vytvořená ve skupině prostředků uzlu, vytvoření služby nástroje pro vyrovnávání zatížení se nepovedlo. Řešení potíží, najdete v tématu události vytvoření služby s [kubectl popisují] [ kubectl-describe] příkazu. Zadejte název služby, jak je uvedeno v manifestu YAML, jak je znázorněno v následujícím příkladu:

```console
kubectl describe service azure-load-balancer
```

Zobrazí se informace o prostředku služby Kubernetes. *Události* následující příklad výstupu na konci označuje, že *uživatele nebyla nalezena zadaná IP adresa*. V těchto scénářích platí ověřte, že jste vytvořili statickou veřejnou IP adresu v uzlu skupiny prostředků a správnost IP adresy uvedené v manifestu služby Kubernetes.

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

Pro další kontroly nad síťovými přenosy do aplikací, můžete místo toho [vytvořit řadič příchozího přenosu dat][aks-ingress-basic]. Můžete také [vytvořit řadič příchozího přenosu dat se statickou veřejnou IP adresu][aks-static-ingress].

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
