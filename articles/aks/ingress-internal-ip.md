---
title: Řadič příchozího přenosu dat v interní síti
titleSuffix: Azure Kubernetes Service
description: Naučte se, jak nainstalovat a nakonfigurovat NGINX příchozího řadiče pro interní privátní síť v clusteru Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 38b08775158a9e5742f1df013ba52a2176af390d
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82100035"
---
# <a name="create-an-ingress-controller-to-an-internal-virtual-network-in-azure-kubernetes-service-aks"></a>Vytvoření kontroleru příchozího přenosu dat do interní virtuální sítě ve službě Azure Kubernetes (AKS)

Kontroler příchozího přenosu dat je softwarový software, který poskytuje reverzní proxy, konfigurovatelné směrování provozu a ukončení protokolu TLS pro služby Kubernetes Services. Kubernetes příchozí prostředky se používají ke konfiguraci pravidel příchozího přenosu dat a tras pro jednotlivé služby Kubernetes. Pomocí řadiče příchozího přenosu dat a pravidel příchozího přenosu dat se dá jedna IP adresa používat ke směrování provozu do několika služeb v clusteru Kubernetes.

V tomto článku se dozvíte, jak nasadit [řadič Nginx][nginx-ingress] příchozího přenosu do clusteru Azure Kubernetes Service (AKS). Kontroler příchozího přenosu dat je nakonfigurovaný v interní privátní virtuální síti a IP adrese. Není povolený žádný externí přístup. Dvě aplikace se pak spustí v clusteru AKS, z nichž každá je přístupná přes jednu IP adresu.

Můžete také:

- [Vytvoření základního kontroleru příchozího přenosu dat s připojením k externí síti][aks-ingress-basic]
- [Povolit doplněk směrování aplikace HTTP][aks-http-app-routing]
- [Vytvoření kontroleru příchozího přenosu dat, který používá vaše vlastní certifikáty TLS][aks-ingress-own-tls]
- Vytvořte kontroler příchozího přenosu dat, který pomocí šifry umožňuje automatické generování certifikátů TLS [s dynamickou veřejnou IP adresou][aks-ingress-tls] nebo [statickou veřejnou IP adresou][aks-ingress-static-tls] .

## <a name="before-you-begin"></a>Před zahájením

Tento článek používá [Helm 3][helm] k instalaci kontroleru Nginx příchozího přenosu dat, správce certifikátů a ukázkové webové aplikace. Musíte mít Helm inicializovaný v rámci vašeho clusteru AKS a používat účet služby pro pokladnu. Další informace o konfiguraci a použití Helm najdete v tématu [install Applications with Helm in Azure Kubernetes Service (AKS)][use-helm].

Tento článek také vyžaduje, abyste spustili Azure CLI verze 2.0.64 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Vytvoření kontroleru příchozího přenosu dat

Ve výchozím nastavení se kontroler NGINX příchozího přenosu dat vytvoří pomocí dynamického přiřazení veřejné IP adresy. Běžným požadavkem na konfiguraci je použití interní privátní sítě a IP adresy. Tento přístup umožňuje omezit přístup k vašim službám na interní uživatele bez přístupu k externímu přístupu.

Pomocí následujícího ukázkového souboru manifestu vytvořte soubor s názvem *internal-YAML.* Tento příklad přiřadí *10.240.0.42* prostředku *loadBalancerIP* . Zadejte vlastní interní IP adresu pro použití s řadičem příchozího přenosu dat. Ujistěte se, že se tato IP adresa už ve vaší virtuální síti nepoužívá.

```yaml
controller:
  service:
    loadBalancerIP: 10.240.0.42
    annotations:
      service.beta.kubernetes.io/azure-load-balancer-internal: "true"
```

Teď nasaďte *Nginx a vstupní* graf s Helm. Chcete-li použít soubor manifestu vytvořený v předchozím kroku, přidejte `-f internal-ingress.yaml` parametr. Pro přidání redundance se nasadí dvě repliky kontrolerů příchozího přenosu dat NGINX s parametrem `--set controller.replicaCount`. Pokud chcete mít v clusteru AKS k dispozici více než jeden uzel, zajistěte, aby bylo možné plně využít více uzlů.

Kontroler příchozího přenosu dat je potřeba naplánovat také v uzlu Linuxu. Uzly Windows serveru (v současné době ve verzi Preview v AKS) by neměli spustit kontroler příchozího přenosu dat. Selektor uzlů se specifikuje pomocí parametru `--set nodeSelector`, aby plánovači Kubernetes oznámil, že má spustit kontroler příchozího přenosu dat NGINX v uzlu Linuxu.

> [!TIP]
> Následující příklad vytvoří obor názvů Kubernetes pro prostředky příchozího přenosu dat s názvem příchozí *– Basic*. Podle potřeby zadejte obor názvů pro vlastní prostředí. Pokud váš cluster AKS není RBAC povolený, přidejte `--set rbac.create=false` do příkazů Helm.

> [!TIP]
> Pokud chcete povolit [zachování IP adresy zdrojového klienta][client-source-ip] pro požadavky na kontejnery v clusteru, přidejte `--set controller.service.externalTrafficPolicy=Local` do příkazu Helm Install. Zdrojová IP adresa klienta je uložená v hlavičce žádosti v části *předané X-pro*. Při použití kontroleru příchozího přenosu dat s povoleným zachováním IP adresy klienta nebude předávat SSL fungovat.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install stable/nginx-ingress \
    --namespace ingress-basic \
    -f internal-ingress.yaml \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Když se pro kontroler příchozího přenosu NGINX vytvoří služba Vyrovnávání zatížení Kubernetes, vaše interní IP adresa se přiřadí, jak je znázorněno v následujícím příkladu výstupu:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                              TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
alternating-coral-nginx-ingress-controller        LoadBalancer   10.0.97.109   10.240.0.42   80:31507/TCP,443:30707/TCP   1m
alternating-coral-nginx-ingress-default-backend   ClusterIP      10.0.134.66   <none>        80/TCP                       1m
```

Zatím se nevytvořila žádná pravidla příchozího přenosu dat, takže pokud přejdete na interní IP adresu, zobrazí se 404 výchozí stránka NGINX adaptéru pro příjem dat. Pravidla příchozího přenosu dat jsou nakonfigurovaná v následujících krocích.

## <a name="run-demo-applications"></a>Spustit ukázkové aplikace

Pokud chcete zobrazit kontroler příchozího přenosu v akci, spusťte v clusteru AKS dvě ukázkové aplikace. V tomto příkladu se Helm používá k nasazení dvou instancí jednoduché aplikace Hello World.

Než budete moct nainstalovat ukázkové grafy Helm, přidejte do prostředí Helm úložiště ukázek Azure následujícím způsobem:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Vytvořte první ukázkovou aplikaci z grafu Helm pomocí následujícího příkazu:

```console
helm install azure-samples/aks-helloworld --namespace ingress-basic
```

Nyní nainstalujte druhou instanci ukázkové aplikace. Pro druhou instanci zadáte nový název, aby byly tyto dvě aplikace vizuálně jedinečné. Zadejte také jedinečný název služby:

```console
helm install azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Vytvoření trasy příchozího přenosu dat

Obě aplikace jsou teď spuštěné v clusteru Kubernetes. Pokud chcete směrovat provoz do každé aplikace, vytvořte Kubernetes prostředek příchozího přenosu dat. Prostředek příchozího přenosu dat konfiguruje pravidla, která směrují provoz do jedné z těchto dvou aplikací.

V následujícím příkladu je přenos do adresy `http://10.240.0.42/` směrován do služby s názvem. `aks-helloworld` Provoz na adresu `http://10.240.0.42/hello-world-two` je směrován do `ingress-demo` služby.

Vytvořte soubor s názvem `hello-world-ingress.yaml` a zkopírujte ho do následujícího příkladu YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
```

Pomocí `kubectl apply -f hello-world-ingress.yaml` příkazu vytvořte prostředek příchozího přenosu dat.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>Test řadiče pro příchozí přenosy

Pokud chcete testovat trasy pro kontroler příchozího přenosu dat, vyhledejte tyto dvě aplikace pomocí webového klienta. V případě potřeby můžete tuto interní funkci jenom rychle otestovat z části pod v clusteru AKS. Vytvořte test pod a připojte k němu relaci terminálu:

```console
kubectl run -it --rm aks-ingress-test --image=debian --namespace ingress-basic
```

Nainstalujte `curl` v části pod pomocí `apt-get`:

```console
apt-get update && apt-get install -y curl
```

Teď máte přístup k adrese vašeho kontroleru Kubernetes příchozího přenosu `curl`dat pomocí, *http://10.240.0.42*jako je například. Zadejte svou vlastní interní IP adresu, kterou jste nasadili při nasazení kontroleru příchozího přenosu dat v prvním kroku tohoto článku.

```console
curl -L http://10.240.0.42
```

S adresou se nezadala žádná další cesta, takže kontroler příchozího přenosu je */* výchozí pro trasu. Vrátí se první ukázková aplikace, jak je znázorněno v následujícím zhuštěném příkladu výstupu:

```
$ curl -L 10.240.0.42

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

Nyní přidejte cestu */Hello-World-Two* k adrese, například *http://10.240.0.42/hello-world-two*. Vrátí se druhá ukázková aplikace s vlastním názvem, jak je znázorněno v následujícím zhuštěném příkladu výstupu:

```
$ curl -L -k http://10.240.0.42/hello-world-two

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tento článek používá Helm k instalaci komponent příchozího přenosu dat a ukázkových aplikací. Když nasadíte graf Helm, vytvoří se několik prostředků Kubernetes. Tyto prostředky zahrnují lusky, nasazení a služby. Chcete-li vyčistit tyto prostředky, můžete buď odstranit celý vzorový obor názvů, nebo jednotlivé prostředky.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Odstranění ukázkového oboru názvů a všech prostředků

Chcete-li odstranit celý vzorový obor názvů, `kubectl delete` použijte příkaz a zadejte název oboru názvů. Všechny prostředky v oboru názvů jsou odstraněny.

```console
kubectl delete namespace ingress-basic
```

Pak odeberte úložiště Helm pro aplikaci AKS Hello World:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Odstranit prostředky jednotlivě

Další možností je podrobnější přístup k odstranění jednotlivých vytvořených prostředků. Seznam vydaných verzí Helm `helm list` pomocí příkazu. Vyhledejte grafy s názvem *Nginx-* příchozí a *AKS-HelloWorld*, jak je znázorněno v následujícím příkladu výstupu:

```
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
kissing-ferret      1           Tue Oct 16 17:13:39 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
intended-lemur      1           Tue Oct 16 17:20:59 2018    DEPLOYED    aks-helloworld-0.1.0                default
pioneering-wombat   1           Tue Oct 16 17:21:05 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Odstraňte vydané verze `helm delete` příkazem. Následující příklad odstraní nasazení NGINX příchozího přenosu dat a dvě ukázkové aplikace Hello World AKS.

```
$ helm delete kissing-ferret intended-lemur pioneering-wombat

release "kissing-ferret" deleted
release "intended-lemur" deleted
release "pioneering-wombat" deleted
```

Pak odeberte úložiště Helm pro aplikaci AKS Hello World:

```console
helm repo remove azure-samples
```

Odebrat trasu příchozího přenosu dat směrovaného do ukázkových aplikací:

```console
kubectl delete -f hello-world-ingress.yaml
```

Nakonec můžete odstranit samotný obor názvů. Použijte `kubectl delete` příkaz a zadejte název vašeho oboru názvů:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Další kroky

Tento článek obsahuje některé externí komponenty, které se AKS. Další informace o těchto komponentách naleznete na následujících stránkách projektu:

- [Helm CLI][helm-cli]
- [Kontroler NGINX pro příchozí přenosy][nginx-ingress]

Můžete také:

- [Vytvoření základního kontroleru příchozího přenosu dat s připojením k externí síti][aks-ingress-basic]
- [Povolit doplněk směrování aplikace HTTP][aks-http-app-routing]
- [Vytvoření kontroleru příchozího přenosu dat s dynamickou veřejnou IP adresou a konfigurací šifry pro automatické generování certifikátů TLS][aks-ingress-tls]
- [Vytvoření kontroleru příchozího přenosu dat se statickou veřejnou IP adresou a nastavení šifrování, které umožňuje automatické generování certifikátů TLS][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers