---
title: Přizpůsobení CoreDNS pro Azure Kubernetes Service (AKS)
description: Zjistěte, jak přizpůsobit CoreDNS, přidání subdomén nebo rozšířit vlastní koncové body DNS pomocí Azure Kubernetes Service (AKS)
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 03/15/2019
ms.author: jnoller
ms.openlocfilehash: 9f3a62c5782724f14f10b5875fc8db31dbffe67c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66693386"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>Přizpůsobení CoreDNS pomocí služby Azure Kubernetes

Využívá Azure Kubernetes Service (AKS) [CoreDNS] [ coredns] projektu pro správu DNS clusteru a řešení se všemi *1.12.x* a větší clustery. Dříve byl použit projekt kube-dns. Tento projekt kube-dns je nyní zastaralá. Další informace o přizpůsobení CoreDNS a Kubernetes najdete v článku [oficiální dokumentaci nadřazeného][corednsk8s].

AKS je spravovaná služba, nelze změnit hlavní konfiguraci CoreDNS ( *CoreFile*). Místo toho použít Kubernetes *ConfigMap* chcete přepsat výchozí nastavení. Pokud chcete zobrazit výchozí AKS CoreDNS ConfigMaps, použijte `kubectl get configmaps coredns -o yaml` příkazu.

Tento článek ukazuje, jak používat ConfigMaps pro základní přizpůsobení možností CoreDNS ve službě AKS.

> [!NOTE]
> `kube-dns` nabízí různé [možnosti přizpůsobení] [ kubednsblog] přes mapu config Kubernetes. Je CoreDNS **není** zpětně kompatibilní s kube-dns. Pro použití s CoreDNS musí aktualizovat všechna vlastní nastavení, které jste použili dříve.

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte existující cluster AKS. Pokud potřebujete AKS cluster, najdete v tomto rychlém startu AKS [pomocí Azure CLI] [ aks-quickstart-cli] nebo [pomocí webu Azure portal][aks-quickstart-portal].

## <a name="what-is-supportedunsupported"></a>Co je podporované/nepodporované

Všechny integrované moduly plug-in CoreDNS jsou podporovány. Žádné moduly plug-in na add, třetích stran nejsou podporovány.

## <a name="rewrite-dns"></a>Rewrite DNS

Jeden scénář, ke kterým máte je provést na průběžné přepisů název DNS. V následujícím příkladu nahraďte `<domain to be written>` s vlastní plně kvalifikovaný název domény. Vytvořte soubor s názvem `corednsms.json` a vložte následující ukázková konfigurace:

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

Vytvoření s použitím ConfigMap [kubectl použít configmap] [ kubectl-apply] příkaz a zadejte název vašeho YAML manifestu:

```console
kubectl apply -f corednsms.json
```

K ověření se provedly úpravy, použijte [kubectl get configmaps] [ kubectl-get] a určete vaše *coredns vlastní* ConfigMap:

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

Nyní vynuťte CoreDNS znovu načte ConfigMap. [Kubectl odstranit pod] [ kubectl delete] příkazu není destruktivní a nezpůsobí časové prodlevy. `kube-dns` Podů se odstraní a Plánovač Kubernetes potom znovu vytvoří je. Tyto nové podů obsahovat změnu v hodnotě hodnotu TTL.

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> Výše uvedeného příkazu je správná. Zatímco měníme `coredns`, Probíhá nasazení **kube-dns** název.

## <a name="custom-proxy-server"></a>Vlastní proxy server

Pokud je třeba zadat proxy server pro síťový provoz, můžete vytvořit ConfigMap přizpůsobení DNS. V následujícím příkladu, aktualizujte `proxy` název a adresu hodnotami pro vlastní prostředí. Vytvořte soubor s názvem `corednsms.json` a vložte následující ukázková konfigurace:

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

Stejně jako v předchozích příkladech, vytvořte pomocí ConfigMap [kubectl použít configmap] [ kubectl-apply] příkaz a zadejte název manifestu YAML. Pak vynutit CoreDNS k opětovnému načtení pomocí ConfigMap [kubectl odstranit pod] [ kubectl delete] pro Plánovač Kubernetes je znovu vytvořit:

```console
kubectl apply -f corednsms.json
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>Použijte vlastní domény

Můžete chtít nakonfigurovat vlastní domény, které lze vyřešit pouze interně. Například můžete chtít vyřešit vlastní doménu *puglife.local*, která není platnou doménu nejvyšší úrovně. Bez vlastní doménu ConfigMap clusteru AKS nejde přeložit adresu.

V následujícím příkladu aktualizace vlastní domény a IP adresu směrovat přenos dat na hodnoty pro konkrétní prostředí. Vytvořte soubor s názvem `corednsms.json` a vložte následující ukázková konfigurace:

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

Stejně jako v předchozích příkladech, vytvořte pomocí ConfigMap [kubectl použít configmap] [ kubectl-apply] příkaz a zadejte název manifestu YAML. Pak vynutit CoreDNS k opětovnému načtení pomocí ConfigMap [kubectl odstranit pod] [ kubectl delete] pro Plánovač Kubernetes je znovu vytvořit:

```console
kubectl apply -f corednsms.json
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="stub-domains"></a>Zástupné domény

CoreDNS lze také nakonfigurovat zástupné domény. V následujícím příkladu aktualizace vlastní domény a IP adresy hodnotami pro vlastní prostředí. Vytvořte soubor s názvem `corednsms.json` a vložte následující ukázková konfigurace:

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

Stejně jako v předchozích příkladech, vytvořte pomocí ConfigMap [kubectl použít configmap] [ kubectl-apply] příkaz a zadejte název manifestu YAML. Pak vynutit CoreDNS k opětovnému načtení pomocí ConfigMap [kubectl odstranit pod] [ kubectl delete] pro Plánovač Kubernetes je znovu vytvořit:

```console
kubectl apply -f corednsms.json
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>Modul plug-in hostitele

Jako jsou podporovány všechny integrované moduly plug-in to znamená, že CoreDNS [hostitele] [ coredns hosts] modul plug-in je k dispozici pro přizpůsobení stejně:

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

V tomto článku jsme si ukázali některé ukázkové scénáře pro přizpůsobení CoreDNS. Informace o projektu CoreDNS, naleznete v tématu [stránce nadřazený projekt CoreDNS][coredns].

Další informace o klíčových konceptech sítě najdete v tématu [sítě koncepty pro aplikace ve službě AKS][concepts-network].

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
