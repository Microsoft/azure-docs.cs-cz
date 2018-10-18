---
title: Vytvoření interního nástroje ve službě Azure Kubernetes Service (AKS)
description: Informace o vytváření a používání interního nástroje pro vystavit svoje služby Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 10/08/2018
ms.author: iainfou
ms.openlocfilehash: 042d2ee0f615ce5216fc11152f0f65518ff9bd5c
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49376375"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Použití interního nástroje Azure Kubernetes Service (AKS)

Pokud chcete omezit přístup k aplikacím ve službě Azure Kubernetes Service (AKS), můžete vytvořit a používat interní load balancer. Interní nástroj zpřístupňuje služby Kubernetes pouze pro aplikace spuštěné ve stejné virtuální síti jako Kubernetes cluster. Tento článek popisuje, jak vytváření a používání interního nástroje Azure Kubernetes Service (AKS).

> [!NOTE]
> Nástroj Azure Load Balancer je k dispozici ve dvou skladových jednotkách - *základní* a *standardní*. Další informace najdete v tématu [porovnání SKU nástroje pro vyrovnávání zatížení Azure][azure-lb-comparison]. V současné době podporuje AKS *základní* SKU. Pokud chcete použít *standardní* SKU, můžete použít nadřazený [acs-engine][acs-engine].

## <a name="create-an-internal-load-balancer"></a>Vytvořte interní nástroj pro vyrovnávání zatížení.

Vytvoření interního nástroje, vytvoření manifestu služby s názvem `internal-lb.yaml` s typem služby *nástroj pro vyrovnávání zatížení* a *azure zatížení – nástroje pro vyrovnávání – interní* poznámky, jak je znázorněno v následujícím Příklad:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

Po nasazení s `kubectl apply -f internal-lb.yaml`, služby Azure load balancer je vytvořen a k dispozici ve stejné virtuální síti jako clusteru AKS.

Když zobrazujete podrobnosti služby, IP adresa interní služby load balancer je zobrazena ve *EXTERNAL-IP* sloupce. Může trvat minutu nebo dvě IP adresy v *\<čekající\>* na skutečné vnitřní IP adresu, jak je znázorněno v následujícím příkladu:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>Zadejte IP adresu

Pokud chcete použít konkrétní IP adresu pomocí interní služby load balancer, přidejte *loadBalancerIP* vlastnosti manifestu YAML nástroje pro vyrovnávání zatížení. Zadaná IP adresa se musí nacházet ve stejné podsíti jako AKS cluster a nesmí být přiřazeny k prostředku.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: internal-app
```

Když zobrazujete podrobnosti služby, IP adresa v *EXTERNAL-IP* sloupce odráží zadaná IP adresa:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="use-private-networks"></a>Použití privátních sítí

Při vytváření clusteru AKS, můžete zadat rozšířené nastavení sítě. Tento přístup vám umožní nasadit cluster do existující virtuální síť Azure a podsítě. Jeden scénář je k nasazení clusteru AKS do privátní síť připojená k v místním prostředí a spuštění služeb dostupná jenom interně. Další informace najdete v tématu [konfiguraci rozšířeného sítě ve službě AKS][advanced-networking].

Žádné změny k předchozí kroky jsou nutné k nasazení interního nástroje v clusteru AKS, který používá privátní sítě. Nástroje pro vyrovnávání zatížení je vytvořen ve stejné skupině prostředků jako AKS cluster, ale připojení k vaší privátní virtuální síť a podsíť, jak je znázorněno v následujícím příkladu:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Možná budete muset udělit instančním objektu pro AKS cluster *Přispěvatel sítě* role do skupiny prostředků, ve které jsou nasazené prostředky virtuální sítě Azure. Zobrazit instanční objekt s [az aks zobrazit][az-aks-show], jako například `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"`. Chcete-li vytvořit přiřazení role, použijte [vytvořit přiřazení role az] [ az-role-assignment-create] příkazu.

## <a name="specify-a-different-subnet"></a>Zadejte jinou podsíť

Zadejte podsíť pro nástroj pro vyrovnávání zatížení, přidat *azure zatížení – nástroje pro vyrovnávání – interní podsítě* poznámky ke službě. Zadaná podsíť musí být ve stejné virtuální síti jako clusteru AKS. Po nasazení nástroje pro vyrovnávání zatížení *EXTERNAL-IP* adresa je součástí zadané podsíti.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "apps-subnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

## <a name="delete-the-load-balancer"></a>Odstranit nástroj pro vyrovnávání zatížení

Při odstranění všech služeb, které používají interní služby load balancer, se odstraní také nástroj pro vyrovnávání zatížení, samotného.

Můžete také přímo odstranit službu jako s jakýmikoli prostředky Kubernetes jako `kubectl delete service internal-app`, což také odstraní základní nástroje pro vyrovnávání zatížení Azure.

## <a name="next-steps"></a>Další postup

Další informace o službách Kubernetes na [dokumentace ke službě services Kubernetes][kubernetes-services].

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[acs-engine]: https://github.com/Azure/acs-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-advanced-networking.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus