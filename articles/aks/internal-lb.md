---
title: Vytvoření interního load balanceru úrovně Azure Kubernetes Service (AKS)
description: Informace o vytváření a používání interního nástroje pro vystavit svoje služby Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/12/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 123fc08995416e0ff9c7e12a526deadc34b3a4a2
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001391"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Použití interního nástroje Azure Kubernetes Service (AKS)

Interní Vyrovnávání zatížení zpřístupňuje služby Kubernetes aplikace spuštěné ve stejné virtuální síti jako Kubernetes cluster. Tento článek popisuje, jak vytváření a používání interního nástroje Azure Kubernetes Service (AKS). Nástroj Azure Load Balancer je k dispozici ve dvou skladových položkách: Basic a Standard. AKS používá základní SKU.

## <a name="create-an-internal-load-balancer"></a>Vytvořte interní nástroj pro vyrovnávání zatížení.

Vytvoření interního nástroje, vytvoření manifestu služby s typem služby *nástroj pro vyrovnávání zatížení* a *azure zatížení – nástroje pro vyrovnávání – interní* poznámky, jak je znázorněno v následujícím příkladu:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Po nasazení s `kubetctl apply`, služby Azure load balancer je vytvořen a k dispozici ve stejné virtuální síti jako clusteru AKS.

![Obrázek AKS interního nástroje load balancer](media/internal-lb/internal-lb.png)

Když zobrazujete podrobnosti služby, IP adresa v *EXTERNAL-IP* sloupec je IP adresa interní služby load balancer, jak je znázorněno v následujícím příkladu:

```
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   10s
```

## <a name="specify-an-ip-address"></a>Zadejte IP adresu

Pokud chcete použít konkrétní IP adresu pomocí interní služby load balancer, přidejte *loadBalancerIP* vlastnost specifikace nástroje pro vyrovnávání zatížení. Zadaná IP adresa se musí nacházet ve stejné podsíti jako AKS cluster a nesmí být přiřazeny k prostředku.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Když zobrazujete podrobnosti služby, IP adresa na *EXTERNAL-IP* odráží zadaná IP adresa:

```
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="use-private-networks"></a>Použití privátních sítí

Při vytváření clusteru AKS, můžete zadat rozšířené nastavení sítě. Tento přístup vám umožní nasadit cluster do existující virtuální síť Azure a podsítě. Jeden scénář je k nasazení clusteru AKS do privátní síť připojená k v místním prostředí a spuštění služeb dostupná jenom interně. Další informace najdete v tématu [konfiguraci rozšířeného sítě ve službě AKS][advanced-networking].

Žádné změny k předchozí kroky jsou nutné k nasazení interního nástroje v clusteru AKS, který používá privátní sítě. Nástroje pro vyrovnávání zatížení je vytvořen ve stejné skupině prostředků jako AKS cluster, ale připojení k vaší privátní virtuální síť a podsíť, jak je znázorněno v následujícím příkladu:

```
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Možná budete muset udělit instančním objektu pro AKS cluster *Přispěvatel sítě* role do skupiny prostředků, ve které jsou nasazené prostředky virtuální sítě Azure. Zobrazit instanční objekt s [az aks zobrazit][az-aks-show], jako například `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"`. Chcete-li vytvořit přiřazení role, použijte [vytvořit přiřazení role az] [ az-role-assignment-create] příkazu.

## <a name="specify-a-different-subnet"></a>Zadejte jinou podsíť

Zadejte podsíť pro nástroj pro vyrovnávání zatížení, přidat *azure zatížení – nástroje pro vyrovnávání – interní podsítě* poznámky ke službě. Zadaná podsíť musí být ve stejné virtuální síti jako clusteru AKS. Po nasazení nástroje pro vyrovnávání zatížení *EXTERNAL-IP* adresa je součástí zadané podsíti.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "apps-subnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

## <a name="delete-the-load-balancer"></a>Odstranit nástroj pro vyrovnávání zatížení

Při odstranění všech služeb, které používají interní služby load balancer, se odstraní také nástroj pro vyrovnávání zatížení, samotného.

## <a name="next-steps"></a>Další postup

Další informace o službách Kubernetes na [dokumentace ke službě services Kubernetes][kubernetes-services].

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[advanced-networking]: networking-overview.md
[deploy-advanced-networking]: networking-overview.md#configure-networking---cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create