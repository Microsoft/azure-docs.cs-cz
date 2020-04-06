---
title: Řadič příchozího přenosu dat v interní síti
titleSuffix: Azure Kubernetes Service
description: Zjistěte, jak nainstalovat a nakonfigurovat řadič příchozího přenosu dat NGINX pro interní privátní síť v clusteru služby Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 4a123a02ed26a5257d3b8e3ee69fb14d96cde550
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668481"
---
# <a name="create-an-ingress-controller-to-an-internal-virtual-network-in-azure-kubernetes-service-aks"></a>Vytvoření řadiče příchozího přenosu dat do interní virtuální sítě ve službě Azure Kubernetes Service (AKS)

Řadič příchozího přenosu dat je software, který poskytuje reverzní proxy server, konfigurovatelné směrování provozu a ukončení TLS pro služby Kubernetes. Prostředky příchozího přenosu dat kubernetes se používají ke konfiguraci pravidel příchozího přenosu dat a tras pro jednotlivé služby Kubernetes. Pomocí řadiče příchozího přenosu dat a pravidel příchozího přenosu dat lze jednu adresu IP použít k směrování provozu do více služeb v clusteru Kubernetes.

Tento článek ukazuje, jak nasadit [řadič příchozího přenosu dat NGINX][nginx-ingress] v clusteru služby Azure Kubernetes Service (AKS). Řadič příchozího přenosu dat je nakonfigurován na interní, privátní virtuální síti a ip adrese. Není povolen žádný externí přístup. Dvě aplikace jsou pak spuštěny v clusteru AKS, z nichž každá je přístupná přes jednu IP adresu.

Můžete také:

- [Vytvoření základního řadiče příchozího přenosu dat s externím připojením k síti][aks-ingress-basic]
- [Povolení doplňku směrování aplikací HTTP][aks-http-app-routing]
- [Vytvoření řadiče příchozího přenosu dat, který používá vlastní certifikáty TLS][aks-ingress-own-tls]
- Vytvoření řadiče příchozího přenosu dat, který používá let's Encrypt k automatickému generování certifikátů TLS [s dynamickou veřejnou IP adresou][aks-ingress-tls] nebo [statickou veřejnou IP adresou][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Než začnete

Tento článek používá Helm k instalaci řadiče příchozího přenosu dat NGINX, správce certifikátů a ukázkové webové aplikace. Musíte mít Helm inicializovány v rámci clusteru AKS a pomocí účtu služby tiller. Další informace o konfiguraci a používání helmu najdete v [tématu Instalace aplikací pomocí helmu ve službě Azure Kubernetes Service (AKS).][use-helm]

Tento článek také vyžaduje, abyste spouštěli Azure CLI verze 2.0.64 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Vytvoření řadiče příchozího přenosu dat

Ve výchozím nastavení je řadič příchozího přenosu dat NGINX vytvořen s dynamickým přiřazením veřejné IP adresy. Běžným požadavkem na konfiguraci je použití interní, privátní sítě a IP adresy. Tento přístup umožňuje omezit přístup ke službám interním uživatelům bez externího přístupu.

Vytvořte soubor s názvem *internal-ingress.yaml* pomocí následujícího příkladu souboru manifestu. Tento příklad přiřadí prostředek *loadBalancerIP* *10.240.0.42.* Zadejte vlastní interní IP adresu pro použití s řadičem příchozího přenosu dat. Ujistěte se, že tato adresa IP ještě není v rámci vaší virtuální sítě používána.

```yaml
controller:
  service:
    loadBalancerIP: 10.240.0.42
    annotations:
      service.beta.kubernetes.io/azure-load-balancer-internal: "true"
```

Nyní nasaďte graf *nginx-ingress* s helmem. Chcete-li použít soubor manifestu vytvořený `-f internal-ingress.yaml` v předchozím kroku, přidejte parametr. Pro přidání redundance se nasadí dvě repliky kontrolerů příchozího přenosu dat NGINX s parametrem `--set controller.replicaCount`. Chcete-li plně využít spuštění replik řadiče příchozího přenosu dat, ujistěte se, že je více než jeden uzel v clusteru AKS.

Kontroler příchozího přenosu dat je potřeba naplánovat také v uzlu Linuxu. Uzly systému Windows Server (aktuálně ve verzi preview v AKS) by neměly spouštět řadič příchozího přenosu dat. Selektor uzlů se specifikuje pomocí parametru `--set nodeSelector`, aby plánovači Kubernetes oznámil, že má spustit kontroler příchozího přenosu dat NGINX v uzlu Linuxu.

> [!TIP]
> Následující příklad vytvoří obor názvů Kubernetes pro prostředky příchozího přenosu dat s názvem *ingress-basic*. Podle potřeby zadejte obor názvů pro vlastní prostředí. Pokud váš cluster AKS není povolen `--set rbac.create=false` RBAC, přidejte do příkazů Helm.

> [!TIP]
> Pokud chcete povolit [uchování IP zdrojů klienta][client-source-ip] pro požadavky `--set controller.service.externalTrafficPolicy=Local` na kontejnery v clusteru, přidejte do příkazu Helm install. Zdrojová ADRESA klienta je uložena v hlavičce požadavku v části *X-Forwarded-For*. Při použití řadiče příchozího přenosu dat s povoleným uchováním IP zdroje klienta nebude předávací přenos SSL fungovat.

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

Při vytvoření služby Vyrovnávání zatížení Kubernetes pro řadič příchozího přenosu dat NGINX je přiřazena vaše interní IP adresa, jak je znázorněno v následujícím příkladu výstupu:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                              TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
alternating-coral-nginx-ingress-controller        LoadBalancer   10.0.97.109   10.240.0.42   80:31507/TCP,443:30707/TCP   1m
alternating-coral-nginx-ingress-default-backend   ClusterIP      10.0.134.66   <none>        80/TCP                       1m
```

Zatím nebyla vytvořena žádná pravidla příchozího přenosu dat, takže při procházení interní IP adresy se zobrazí výchozí stránka řadiče příchozího přenosu dat NGINX 404. Pravidla příchozího přenosu dat jsou konfigurována v následujících krocích.

## <a name="run-demo-applications"></a>Spuštění ukázkových aplikací

Chcete-li zobrazit řadič příchozího přenosu dat v akci, spusťme dvě ukázkové aplikace v clusteru AKS. V tomto příkladu Helm se používá k nasazení dvou instancí jednoduché aplikace "Hello world".

Před instalací ukázkových grafů Helm přidejte úložiště ukázek Azure do prostředí Helm následujícím způsobem:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Vytvořte první ukázkovou aplikaci z grafu Helm pomocí následujícího příkazu:

```console
helm install azure-samples/aks-helloworld --namespace ingress-basic
```

Nyní nainstalujte druhou instanci ukázkové aplikace. Pro druhou instanci zadáte nový název tak, aby obě aplikace jsou vizuálně odlišné. Zadáte také jedinečný název služby:

```console
helm install azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Vytvoření postupu příchozího přenosu dat

Obě aplikace jsou nyní spuštěny v clusteru Kubernetes. Chcete-li směrovat provoz do každé aplikace, vytvořte prostředek příchozího přenosu dat Kubernetes. Prostředek příchozího přenosu dat konfiguruje pravidla, která směrují provoz do jedné ze dvou aplikací.

V následujícím příkladu je `http://10.240.0.42/` provoz na adresu směrován `aks-helloworld`na službu s názvem . Provoz na `http://10.240.0.42/hello-world-two` adresu je směrován `ingress-demo` do služby.

Vytvořte soubor `hello-world-ingress.yaml` s názvem a zkopírujte v následujícím příkladu YAML.

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

Vytvořte prostředek příchozího `kubectl apply -f hello-world-ingress.yaml` přenosu dat pomocí příkazu.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>Otestujte řadič příchozího přenosu dat

Chcete-li otestovat trasy pro řadič příchozího přenosu dat, přejděte na dvě aplikace s webovým klientem. V případě potřeby můžete tuto funkci pouze pro interní rychle otestovat z podu v clusteru AKS. Vytvořte testovací modul a připojte k němu terminálovou relaci:

```console
kubectl run -it --rm aks-ingress-test --image=debian --namespace ingress-basic
```

Nainstalujte `curl` do `apt-get`modulu pomocí :

```console
apt-get update && apt-get install -y curl
```

Nyní přístup k adrese řadiče příchozího přenosu `curl`dat *http://10.240.0.42*Kubernetes pomocí , například . Zadejte vlastní interní IP adresu určenou při nasazení řadiče příchozího přenosu dat v prvním kroku tohoto článku.

```console
curl -L http://10.240.0.42
```

S adresou nebyla poskytnuta žádná další cesta, takže */* řadič příchozího přenosu dat je výchozí pro trasu. První ukázková aplikace je vrácena, jak je znázorněno na následujícím výstupu zkráceného příkladu:

```
$ curl -L 10.240.0.42

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

Nyní přidejte */hello-world-two* cestu k *http://10.240.0.42/hello-world-two*adrese, například . Druhá ukázková aplikace s vlastním názvem je vrácena, jak je znázorněno na následujícím výstupu zkráceného příkladu:

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

Tento článek používá Helm k instalaci součástí příchozího přenosu dat a ukázkové aplikace. Při nasazení grafu Helm se vytvoří několik prostředků Kubernetes. Tyto prostředky zahrnují pody, nasazení a služby. Chcete-li vyčistit tyto prostředky, můžete odstranit celý ukázkový obor názvů nebo jednotlivé prostředky.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Odstranění ukázkového oboru názvů a všech prostředků

Chcete-li odstranit celý ukázkový `kubectl delete` obor názvů, použijte příkaz a zadejte název oboru názvů. Všechny prostředky v oboru názvů budou odstraněny.

```console
kubectl delete namespace ingress-basic
```

Potom odstraňte repo helmu pro aplikaci AKS hello world:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Odstranění prostředků jednotlivě

Alternativně podrobnější přístup je odstranit jednotlivé vytvořené prostředky. Seznam vydání helmu `helm list` pomocí příkazu. Podívejte se na grafy s názvem *nginx-ingress* a *aks-helloworld*, jak je znázorněno v následujícím příkladu výstupu:

```
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
kissing-ferret      1           Tue Oct 16 17:13:39 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
intended-lemur      1           Tue Oct 16 17:20:59 2018    DEPLOYED    aks-helloworld-0.1.0                default
pioneering-wombat   1           Tue Oct 16 17:21:05 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Odstraňte verze pomocí `helm delete` příkazu. Následující příklad odstraní nasazení příchozího přenosu dat NGINX a dvě ukázkové aplikace AKS hello world.

```
$ helm delete kissing-ferret intended-lemur pioneering-wombat

release "kissing-ferret" deleted
release "intended-lemur" deleted
release "pioneering-wombat" deleted
```

Dále odeberte repo helmu pro aplikaci AKS hello world:

```console
helm repo remove azure-samples
```

Odeberte trasu příchozího přenosu dat, která směrovala provoz do ukázkových aplikací:

```console
kubectl delete -f hello-world-ingress.yaml
```

Nakonec můžete odstranit samotný obor názvů. Použijte `kubectl delete` příkaz a zadejte název oboru názvů:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Další kroky

Tento článek zahrnoval některé externí součásti AKS. Další informace o těchto součástech naleznete na následujících stránkách projektu:

- [Helm CLI][helm-cli]
- [Řadič příchozího přenosu dat NGINX][nginx-ingress]

Můžete také:

- [Vytvoření základního řadiče příchozího přenosu dat s externím připojením k síti][aks-ingress-basic]
- [Povolení doplňku směrování aplikací HTTP][aks-http-app-routing]
- [Vytvoření řadiče příchozího přenosu dat s dynamickou veřejnou IP adresou a konfigurace funkce Let's Encrypt tak, aby automaticky generovala certifikáty TLS][aks-ingress-tls]
- [Vytvoření řadiče příchozího přenosu dat se statickou veřejnou IP adresou a konfigurace funkce Let's Encrypt pro automatické generování certifikátů TLS][aks-ingress-static-tls]

<!-- LINKS - external -->
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