---
title: Použijte statickou IP adresu s nástrojem pro vyrovnávání zatížení Azure Kubernetes služby (AKS)
description: Použijte statickou IP adresu s nástrojem pro vyrovnávání zatížení Azure Kubernetes služby (AKS).
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/21/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 9f6c34bd09d022b2453869c048f5f3cda7580b91
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34596657"
---
# <a name="use-a-static-ip-address-with-the-azure-kubernetes-service-aks-load-balancer"></a>Použijte statickou IP adresu s nástrojem pro vyrovnávání zatížení Azure Kubernetes služby (AKS)

V některých případech může například při spouštění služby Kubernetes Azure (AKS) se znovu vytvoří vyrovnávání nebo se znovu vytvoří Kubernetes služby s typem nástroj pro vyrovnávání zatížení změnit veřejnou IP adresu služby Kubernetes. Tento dokument podrobnosti konfigurace statickou IP adresu pro vaše Kubernetes služby.

## <a name="create-static-ip-address"></a>Vytvořte statické IP adresy

Vytvořte statickou veřejnou IP adresu pro službu Kubernetes. IP adresa musí být vytvořen v AKS **uzlu** skupinu prostředků. Získat název skupiny prostředků s [az prostředků zobrazit] [ az-resource-show] příkaz.

```azurecli-interactive
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Použití [vytvoření veřejné IP adresy sítě az] [ az-network-public-ip-create] příkaz pro vytvoření IP adresy.

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static
```

Poznamenejte si adresu IP.

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    "ipConfiguration": null,
    "ipTags": [],
    "location": "eastus",
    "name": "myAKSPublicIP",
    "provisioningState": "Succeeded",
    "publicIpAddressVersion": "IPv4",
    "publicIpAllocationMethod": "Static",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "56ec8760-a3b8-4aeb-a89d-42e68d2cbc8c",
    "sku": {
      "name": "Basic"
    },
    "tags": null,
    "type": "Microsoft.Network/publicIPAddresses",
    "zones": null
  }
````

 V případě potřeby adresu můžete načíst pomocí [seznam veřejné ip sítě az] [ az-network-public-ip-list] příkaz.

```azurecli-interactive
az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv
```

```console
40.121.183.52
```

## <a name="create-service-with-ip-address"></a>Vytvoření služby pomocí IP adresy

Jakmile zřídil statickou IP adresu služby Kubernetes lze vytvořit pomocí `loadBalancerIP` vlastností a hodnotou statické IP adresy.

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

## <a name="troubleshooting"></a>Řešení potíží

Pokud statickou IP adresu nebyl vytvořen nebo byl vytvořen ve skupině prostředků nesprávný, vytvoření služby se nezdaří. K řešení potíží, vraťte se události vytvoření služby pomocí [kubectl popisují] [ kubectl-describe] příkaz.

```azurecli-interactive
kubectl describe service azure-vote-front
```

```console
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

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_describe/

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az_network_public_ip_list
[az-resource-show]: /cli/azure/resource#az-resource-show
