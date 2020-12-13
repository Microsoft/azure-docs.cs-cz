---
title: Přizpůsobení CoreDNS pro službu Azure Kubernetes (AKS)
description: Naučte se, jak přizpůsobit CoreDNS pro přidání subdomén nebo rozšiřování vlastních koncových bodů DNS pomocí služby Azure Kubernetes Service (AKS).
services: container-service
author: palma21
ms.topic: article
ms.date: 03/15/2019
ms.author: jpalma
ms.openlocfilehash: 5b13931bc6a13d988c21f728b996c51270769e0c
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368677"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>Přizpůsobení CoreDNS pomocí služby Azure Kubernetes Service

Služba Azure Kubernetes Service (AKS) používá projekt [CoreDNS][coredns] pro správu DNS clusteru a řešení u všech clusterů *1.12. x* a vyšších. Dříve se použil projekt Kube-DNS. Tento projekt Kube-DNS je nyní zastaralý. Další informace o přizpůsobení CoreDNS a Kubernetes najdete v [oficiální dokumentaci pro odesílání dat][corednsk8s].

Jako AKS je spravovaná služba, a proto nemůžete změnit hlavní konfiguraci pro CoreDNS ( *CoreFile*). Místo toho použijete Kubernetes *ConfigMap* k přepsání výchozích nastavení. Pokud chcete zobrazit výchozí AKS CoreDNS ConfigMaps, použijte `kubectl get configmaps --namespace=kube-system coredns -o yaml` příkaz.

V tomto článku se dozvíte, jak používat ConfigMaps pro základní možnosti přizpůsobení CoreDNS v AKS. Tento přístup se liší od konfigurace CoreDNS v jiných kontextech, jako je například použití CoreFile. Ověřte, že je spuštěná verze CoreDNS, protože hodnoty konfigurace se můžou mezi verzemi měnit.

> [!NOTE]
> `kube-dns` k dispozici jsou různé [Možnosti přizpůsobení][kubednsblog] prostřednictvím mapy konfigurace Kubernetes. CoreDNS není **zpětně** kompatibilní s Kube-DNS. Všechna vlastní nastavení, která jste předtím použili, musíte aktualizovat pro použití s CoreDNS.

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, přečtěte si rychlý Start AKS a [použijte Azure CLI][aks-quickstart-cli] nebo [Azure Portal][aks-quickstart-portal].

Při vytváření konfigurace jako v níže uvedených příkladech musí vaše názvy v *datové* části končit buď *. Server* , nebo *. override*. Tato konvence vytváření názvů je definovaná ve výchozí AKS CoreDNS Configmap, kterou můžete zobrazit pomocí `kubectl get configmaps --namespace=kube-system coredns -o yaml` příkazu.

## <a name="what-is-supportedunsupported"></a>Co je podporováno/nepodporované

Podporují se všechny integrované moduly plug-in CoreDNS. Nepodporují se žádné moduly plug-in nebo doplňky třetích stran.

## <a name="rewrite-dns"></a>Přepsat DNS

Jedním z scénářů je provést přepisování názvů DNS průběžně. V následujícím příkladu nahraďte svým `<domain to be written>` vlastním plně kvalifikovaným názvem domény. Vytvořte soubor s názvem `corednsms.yaml` a vložte následující příklad konfigurace:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        errors
        cache 30
        rewrite name substring <domain to be rewritten>.com default.svc.cluster.local
        kubernetes cluster.local in-addr.arpa ip6.arpa {
          pods insecure
          upstream
          fallthrough in-addr.arpa ip6.arpa
        }
        forward .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10, but that server must be able to resolve the rewritten domain name
    }
```

> [!IMPORTANT]
> Pokud přesměrujete na server DNS, jako je například IP adresa služby CoreDNS, musí být server DNS schopný přeložit název přepsané domény.

Vytvořte ConfigMap pomocí příkazu [kubectl použít ConfigMap][kubectl-apply] a zadejte název vašeho manifestu YAML:

```console
kubectl apply -f corednsms.yaml
```

Chcete-li ověřit použití vlastních nastavení, použijte [kubectl Get configmaps][kubectl-get] a zadejte *coredns-Custom* ConfigMap:

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

Nyní vynuťte CoreDNS znovu načíst ConfigMap. Příkaz [kubectl Delete pod][kubectl delete] není destruktivní a nezpůsobuje čas. `kube-dns`Lusky se odstraní a Plánovač Kubernetes je pak znovu vytvoří. Tyto nové lusky obsahují změnu hodnoty TTL.

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> Výše uvedený příkaz je správný. I když se mění `coredns` , nasazení je pod názvem **Kube-DNS** .

## <a name="custom-forward-server"></a>Vlastní server pro přeposílání

Pokud pro síťový provoz potřebujete zadat server pro přeposílání, můžete vytvořit ConfigMap pro přizpůsobení DNS. V následujícím příkladu aktualizujte `forward` název a adresu na hodnoty vlastního prostředí. Vytvořte soubor s názvem `corednsms.yaml` a vložte následující příklad konfigurace:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        forward foo.com 1.1.1.1
    }
```

Jako v předchozích příkladech vytvořte ConfigMap pomocí příkazu [kubectl použít ConfigMap][kubectl-apply] a zadejte název vašeho manifestu YAML. Potom vynuťte CoreDNS opětovného načtení ConfigMap pomocí [kubectl odstranit pod][kubectl delete] , aby ho mohl Plánovač Kubernetese znovu vytvořit:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
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
  puglife.server: | # you may select any name here, but it must end with the .server file extension
    puglife.local:53 {
        errors
        cache 30
        forward . 192.11.0.1  # this is my test/dev DNS server
    }
```

Jako v předchozích příkladech vytvořte ConfigMap pomocí příkazu [kubectl použít ConfigMap][kubectl-apply] a zadejte název vašeho manifestu YAML. Potom vynuťte CoreDNS opětovného načtení ConfigMap pomocí [kubectl odstranit pod][kubectl delete] , aby ho mohl Plánovač Kubernetese znovu vytvořit:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
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
  test.server: | # you may select any name here, but it must end with the .server file extension
    abc.com:53 {
        errors
        cache 30
        forward . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        forward . 2.3.4.5
    }

```

Jako v předchozích příkladech vytvořte ConfigMap pomocí příkazu [kubectl použít ConfigMap][kubectl-apply] a zadejte název vašeho manifestu YAML. Potom vynuťte CoreDNS opětovného načtení ConfigMap pomocí [kubectl odstranit pod][kubectl delete] , aby ho mohl Plánovač Kubernetese znovu vytvořit:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
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
    test.override: | # you may select any name here, but it must end with the .override file extension
          hosts example.hosts example.org { # example.hosts must be a file
              10.0.0.1 example.org
              fallthrough
          }
```

## <a name="enable-logging-for-dns-query-debugging"></a>Povolit protokolování pro ladění dotazů DNS 

Pokud chcete povolit protokolování dotazů DNS, použijte v coredns vlastní ConfigMap následující konfiguraci:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  log.override: | # you may select any name here, but it must end with the .override file extension
        log
```

## <a name="next-steps"></a>Další kroky

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
