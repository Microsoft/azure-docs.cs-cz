---
title: Přizpůsobení služby CoreDNS pro službu Azure Kubernetes (AKS)
description: Zjistěte, jak přizpůsobit CoreDNS pro přidání subdomén nebo rozšířit vlastní koncové body DNS pomocí služby Azure Kubernetes Service (AKS).
services: container-service
author: jnoller
ms.topic: article
ms.date: 03/15/2019
ms.author: jenoller
ms.openlocfilehash: 78132a53313f4a8ee5c10af340c8dab08c3e42c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595820"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>Přizpůsobení CoreDNS pomocí služby Azure Kubernetes Service

Azure Kubernetes Service (AKS) používá projekt [CoreDNS][coredns] pro správu a rozlišení CLUSTERU DNS se všemi clustery *1.12.x* a vyššími. Dříve byl použit projekt kube-dns. Tento projekt kube-dns je nyní zastaralé. Další informace o přizpůsobení služby CoreDNS a kubernetes naleznete v [oficiální předcházející dokumentaci][corednsk8s].

Jako AKS je spravovaná služba, nelze změnit hlavní konfiguraci pro CoreDNS *(CoreFile*). Místo toho použijete Kubernetes *ConfigMap* přepsat výchozí nastavení. Chcete-li zobrazit výchozí aks CoreDNS `kubectl get configmaps --namespace=kube-system coredns -o yaml` ConfigMaps, použijte příkaz.

Tento článek ukazuje, jak používat ConfigMaps pro základní možnosti přizpůsobení CoreDNS v AKS. Tento přístup se liší od konfigurace CoreDNS v jiných kontextech, jako je například použití CoreFile. Ověřte verzi coredns, kterou používáte, protože hodnoty konfigurace se mohou mezi verzemi měnit.

> [!NOTE]
> `kube-dns`nabízí různé [možnosti přizpůsobení][kubednsblog] prostřednictvím konfigurační mapy Kubernetes. CoreDNS **není** zpětně kompatibilní s kube-dns. Všechna vlastní nastavení, která jste dříve použili, musí být aktualizována pro použití s coredns.

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, podívejte se na aks rychlý start [pomocí Azure CLI][aks-quickstart-cli] nebo [pomocí portálu Azure][aks-quickstart-portal].

## <a name="what-is-supportedunsupported"></a>Co je podporováno/nepodporováno

Podporovány jsou všechny vestavěné pluginy CoreDNS. Nejsou podporovány žádné pluginy pro doplňky a třetí strany.

## <a name="rewrite-dns"></a>Přepsat DNS

Jeden scénář, který máte, je provádět přepsání názvů DNS za chodu. V následujícím příkladu `<domain to be written>` nahraďte vlastním plně kvalifikovaným názvem domény. Vytvořte soubor `corednsms.yaml` s názvem a vložte následující ukázkovou konfiguraci:

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
        forward .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10
    }
```

Vytvořte ConfigMap pomocí [příkazu kubectl apply configmap][kubectl-apply] a zadejte název manifestu YAML:

```console
kubectl apply -f corednsms.yaml
```

Chcete-li ověřit, zda byly použity vlastní nastavení, použijte [kubectl get configmaps][kubectl-get] a zadejte *coredns-custom* ConfigMap:

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

Nyní vynutit CoreDNS znovu načíst ConfigMap. Příkaz [kubectl delete pod][kubectl delete] není destruktivní a nezpůsobuje prostoje. Pody `kube-dns` jsou odstraněny a Plánovač Kubernetes je pak znovu vytvoří. Tyto nové pody obsahují změnu hodnoty TTL.

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> Výše uvedený příkaz je správný. Zatímco se měníme `coredns`, nasazení je pod názvem **kube-dns.**

## <a name="custom-forward-server"></a>Vlastní předsulaná server

Pokud potřebujete zadat předací server pro váš síťový provoz, můžete vytvořit ConfigMap pro přizpůsobení DNS. V následujícím příkladu `forward` aktualizujte název a adresu s hodnotami pro vlastní prostředí. Vytvořte soubor `corednsms.yaml` s názvem a vložte následující ukázkovou konfiguraci:

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

Stejně jako v předchozích příkladech vytvořte ConfigMap pomocí [příkazu kubectl apply configmap][kubectl-apply] a zadejte název manifestu YAML. Potom force CoreDNS znovu načíst ConfigMap pomocí [kubectl delete pod][kubectl delete] pro Plánovač Kubernetes znovu vytvořit:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>Použití vlastních domén

Můžete chtít nakonfigurovat vlastní domény, které lze vyřešit pouze interně. Můžete například vyřešit vlastní doménu *puglife.local*, která není platnou doménou nejvyšší úrovně. Bez vlastní domény ConfigMap cluster AKS nemůže přeložit adresu.

V následujícím příkladu aktualizujte vlastní doménu a adresu IP tak, aby nasměrovala provoz na hodnoty pro vlastní prostředí. Vytvořte soubor `corednsms.yaml` s názvem a vložte následující ukázkovou konfiguraci:

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
        forward . 192.11.0.1  # this is my test/dev DNS server
    }
```

Stejně jako v předchozích příkladech vytvořte ConfigMap pomocí [příkazu kubectl apply configmap][kubectl-apply] a zadejte název manifestu YAML. Potom force CoreDNS znovu načíst ConfigMap pomocí [kubectl delete pod][kubectl delete] pro Plánovač Kubernetes znovu vytvořit:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="stub-domains"></a>Domény se zakázaným inzerováním

CoreDNS lze také použít ke konfiguraci domén se zakázaným inzerováním. V následujícím příkladu aktualizujte vlastní domény a ADRESY IP hodnotami pro vlastní prostředí. Vytvořte soubor `corednsms.yaml` s názvem a vložte následující ukázkovou konfiguraci:

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
        forward . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        forward . 2.3.4.5
    }

```

Stejně jako v předchozích příkladech vytvořte ConfigMap pomocí [příkazu kubectl apply configmap][kubectl-apply] a zadejte název manifestu YAML. Potom force CoreDNS znovu načíst ConfigMap pomocí [kubectl delete pod][kubectl delete] pro Plánovač Kubernetes znovu vytvořit:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>Hostitelé plugin

Vzhledem k tomu, že jsou podporovány všechny vestavěné pluginy, znamená to, že plugin CoreDNS [Hosts][coredns hosts] je k dispozici také pro přizpůsobení:

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

## <a name="enable-logging-for-dns-query-debugging"></a>Povolení protokolování pro ladění dotazů DNS 

Chcete-li povolit protokolování dotazů DNS, použijte v konfigurační mapě coredns-custom následující konfiguraci:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  log.override: |
        log
```

## <a name="next-steps"></a>Další kroky

Tento článek ukázal některé příklady scénářů pro přizpůsobení CoreDNS. Informace o projektu CoreDNS naleznete na [stránce projektu CoreDNS upstream][coredns].

Další informace o konceptech hlavních sítí najdete [v tématu Síťové koncepty pro aplikace v AKS][concepts-network].

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
