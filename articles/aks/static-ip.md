---
title: Statické IP adresy pomocí nástroje pro vyrovnávání zatížení Azure Kubernetes Service (AKS)
description: Zjistěte, jak vytvořit a používat statickou IP adresu nástroje pro vyrovnávání zatížení Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 09/26/2018
ms.author: iainfou
ms.openlocfilehash: a7e592e9911c596f2cf74724e73c469ed616e5f0
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47391333"
---
# <a name="use-a-static-public-ip-address-with-the-azure-kubernetes-service-aks-load-balancer"></a>Statické veřejné IP adresy pomocí nástroje pro vyrovnávání zatížení Azure Kubernetes Service (AKS)

Ve výchozím nastavení veřejná IP adresa přidělená k prostředku nástroje pro vyrovnávání zatížení vytvořil AKS cluster platí pouze pro životnosti prostředku. Při odstranění služby Kubernetes, se také odstraní přidružené vyrovnávání zátěže a IP adresu. Pokud chcete přiřadit konkrétní IP adresu nebo zachovat IP adresu pro opakovaně nasazeném služby Kubernetes, můžete vytvořit a používat statickou veřejnou IP adresu.

V tomto článku se dozvíte, jak vytvořit statickou veřejnou IP adresu a přiřaďte ho do služby Kubernetes.

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte existující cluster AKS. Pokud potřebujete AKS cluster, najdete v tomto rychlém startu AKS [pomocí Azure CLI] [ aks-quickstart-cli] nebo [pomocí webu Azure portal][aks-quickstart-portal].

Také nutné mít Azure CLI verze 2.0.46 nebo později nainstalované a nakonfigurované. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI] [install-azure-cli].

## <a name="create-a-static-ip-address"></a>Vytvoření statické IP adresy

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
````

Později můžete získat na veřejných IP adres pomocí [az network public-ip list] [ az-network-public-ip-list] příkazu. Zadejte název skupiny prostředků uzel a potom dotázat *ipAddress* jak je znázorněno v následujícím příkladu:

```azurecli
$ az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Vytvoření služby pomocí statické IP adresy

Chcete-li vytvořit službu s statickou IP adresu, přidejte `loadBalancerIP` vlastnosti a hodnoty statických IP adres do manifestu YAML, jak je znázorněno v následujícím příkladu:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

## <a name="troubleshoot"></a>Řešení potíží

Pokud podle statickou IP adresu *loadBalancerIP* vlastnost service manifest Kubernetesu neexistuje nebo není vytvořená ve skupině prostředků uzlu, vytvoření služby nástroje pro vyrovnávání zatížení se nepovedlo. Řešení potíží, najdete v tématu události vytvoření služby s [kubectl popisují] [ kubectl-describe] příkazu. Zadejte název služby, jak je uvedeno v manifestu YAML, jak je znázorněno v následujícím příkladu:

```console
kubectl describe service azure-vote-front
```

Zobrazí se informace o prostředku služby Kubernetes. *Události* následující příklad výstupu na konci označuje, že *uživatele nebyla nalezena zadaná IP adresa*. V těchto scénářích platí ověřte, že jste vytvořili statickou veřejnou IP adresu v uzlu skupiny prostředků a správnost IP adresy uvedené v manifestu služby Kubernetes.

```
Name:                     azure-vote-front
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=azure-vote-front
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
  Warning  CreatingLoadBalancerFailed  6s (x2 over 12s)  service-controller  Error creating load balancer (will retry): Failed to create load balancer for service default/azure-vote-front: user supplied IP Address 40.121.183.52 was not found
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
