---
title: Přizpůsobení CoreDNS pro službu Azure Kubernetes (AKS)
description: Naučte se, jak přizpůsobit CoreDNS pro přidání subdomén nebo rozšiřování vlastních koncových bodů DNS pomocí služby Azure Kubernetes Service (AKS).
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 03/15/2019
ms.author: jenoller
ms.openlocfilehash: 5a69882f6bb38ac8e005ce5552fe57383a58cc63
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69542576"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>Přizpůsobení CoreDNS pomocí služby Azure Kubernetes

Služba Azure Kubernetes Service (AKS) používá projekt [CoreDNS][coredns] pro správu DNS clusteru a řešení u všech clusterů *1.12. x* a vyšších. Dříve se použil projekt Kube-DNS. Tento projekt Kube-DNS je nyní zastaralý. Další informace o přizpůsobení CoreDNS a Kubernetes najdete v [oficiální dokumentaci pro odesílání dat][corednsk8s].

Jako AKS je spravovaná služba, a proto nemůžete změnit hlavní konfiguraci pro CoreDNS ( *CoreFile*). Místo toho použijete Kubernetes *ConfigMap* k přepsání výchozích nastavení. Pokud chcete zobrazit výchozí AKS CoreDNS ConfigMaps, použijte `kubectl get configmaps -namespace=kube-system coredns -o yaml` příkaz.

V tomto článku se dozvíte, jak používat ConfigMaps pro základní možnosti přizpůsobení CoreDNS v AKS.

> [!NOTE]
> `kube-dns`k dispozici jsou různé [Možnosti přizpůsobení][kubednsblog] prostřednictvím mapy konfigurace Kubernetes. CoreDNS není zpětně kompatibilní s Kube-DNS. Všechna vlastní nastavení, která jste předtím použili, musíte aktualizovat pro použití s CoreDNS.

## <a name="before-you-begin"></a>Před zahájením

V tomto článku se předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, přečtěte si rychlý Start AKS a [použijte Azure CLI][aks-quickstart-cli] nebo [Azure Portal][aks-quickstart-portal].

## <a name="what-is-supportedunsupported"></a>Co je podporováno/nepodporované

Podporují se všechny integrované moduly plug-in CoreDNS. Nepodporují se žádné moduly plug-in nebo doplňky třetích stran. 

## <a name="rewrite-dns"></a>Přepsat DNS

Jedním z scénářů je provést přepisování názvů DNS průběžně. V následujícím příkladu nahraďte `<domain to be written>` svým vlastním plně kvalifikovaným názvem domény. Vytvořte soubor s názvem `corednsms.yaml` a vložte následující příklad konfigurace:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: |
    <domain to be rewritten>.com:53 {
        errors
        cache 30
        rewrite name substring <domain to be rewritten>.com default.svc.cluster.local
        proxy .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10
    }
```

Vytvořte ConfigMap pomocí příkazu [kubectl použít ConfigMap][kubectl-apply] a zadejte název vašeho manifestu YAML:

```console
kubectl apply -f corednsms.yaml
```

Chcete-li ověřit použití vlastních nastavení, použijte [kubectl Get configmaps][kubectl-get] a zadejte *coredns-Custom* ConfigMap:

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

Nyní vynuťte CoreDNS znovu načíst ConfigMap. Příkaz [kubectl Delete pod][kubectl delete] není destruktivní a nezpůsobuje čas. `kube-dns` Lusky se odstraní a Plánovač Kubernetes je pak znovu vytvoří. Tyto nové lusky obsahují změnu hodnoty TTL.

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> Výše uvedený příkaz je správný. I když se mění `coredns`, nasazení je pod názvem **Kube-DNS** .

## <a name="custom-proxy-server"></a>Vlastní proxy server

Pokud potřebujete zadat proxy server pro síťový provoz, můžete vytvořit ConfigMap pro přizpůsobení DNS. V následujícím příkladu aktualizujte `proxy` název a adresu na hodnoty vlastního prostředí. Vytvořte soubor s názvem `corednsms.yaml` a vložte následující příklad konfigurace:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        proxy foo.com 1.1.1.1
    }
```

Jako v předchozích příkladech vytvořte ConfigMap pomocí příkazu [kubectl použít ConfigMap][kubectl-apply] a zadejte název vašeho manifestu YAML. Potom vynuťte CoreDNS opětovného načtení ConfigMap pomocí [kubectl odstranit pod][kubectl delete] , aby ho mohl Plánovač Kubernetese znovu vytvořit:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>Použití vlastních domén

Možná budete chtít nakonfigurovat vlastní domény, které se dají vyřešit jenom interně. Můžete například chtít vyřešit vlastní doménu *puglife. Local*, což není platná doména nejvyšší úrovně. Bez vlastní domény ConfigMap cluster AKS nemůže adresu přeložit.

V následujícím příkladu aktualizujte vlastní doménu a IP adresu tak, aby se provoz směroval na hodnoty vlastního prostředí. Vytvořte soubor s názvem `corednsms.yaml` a vložte následující příklad konfigurace:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  puglife.server: |
    puglife.local:53 {
        errors
        cache 30
        proxy . 192.11.0.1  # this is my test/dev DNS server
    }
```

Jako v předchozích příkladech vytvořte ConfigMap pomocí příkazu [kubectl použít ConfigMap][kubectl-apply] a zadejte název vašeho manifestu YAML. Potom vynuťte CoreDNS opětovného načtení ConfigMap pomocí [kubectl odstranit pod][kubectl delete] , aby ho mohl Plánovač Kubernetese znovu vytvořit:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="stub-domains"></a>Domény se zástupnými procedurami

CoreDNS lze také použít ke konfiguraci domén se zástupnými procedurami. V následujícím příkladu aktualizujte vlastní domény a IP adresy s použitím hodnot pro vlastní prostředí. Vytvořte soubor s názvem `corednsms.yaml` a vložte následující příklad konfigurace:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: |
    abc.com:53 {
        errors
        cache 30
        proxy . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        proxy . 2.3.4.5
    }

```

Jako v předchozích příkladech vytvořte ConfigMap pomocí příkazu [kubectl použít ConfigMap][kubectl-apply] a zadejte název vašeho manifestu YAML. Potom vynuťte CoreDNS opětovného načtení ConfigMap pomocí [kubectl odstranit pod][kubectl delete] , aby ho mohl Plánovač Kubernetese znovu vytvořit:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>Modul plug-in hostitele

Vzhledem k tomu, že jsou všechny integrované moduly plug-in podporované, znamená to, že modul plug-in [hostitelů][coredns hosts] CoreDNS je k dispozici i pro přizpůsobení:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom # this is the name of the configmap you can overwrite with your changes
  namespace: kube-system
data:
    test.override: |
          hosts example.hosts example.org { # example.hosts must be a file
              10.0.0.1 example.org
              fallthrough
          }
```

## <a name="next-steps"></a>Další postup

Tento článek ukázal několik ukázkových scénářů pro přizpůsobení CoreDNS. Informace o projektu CoreDNS najdete [na stránce projektu CoreDNS pro odesílání dat][coredns].

Další informace o konceptech základní sítě najdete v tématu [Koncepty sítě pro aplikace v AKS][concepts-network].

<!-- LINKS - external -->
[kubednsblog]: https://www.danielstechblog.io/using-custom-dns-server-for-domain-specific-name-resolution-with-azure-kubernetes-service/
[coredns]: https://coredns.io/
[corednsk8s]: https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns
[dnscache]: https://coredns.io/plugins/cache/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[coredns hosts]: https://coredns.io/plugins/hosts/

<!-- LINKS - internal -->
[concepts-network]: concepts-network.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
