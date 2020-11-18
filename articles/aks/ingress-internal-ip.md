---
title: Řadič příchozího přenosu dat v interní síti
titleSuffix: Azure Kubernetes Service
description: Naučte se, jak nainstalovat a nakonfigurovat NGINX příchozího řadiče pro interní privátní síť v clusteru Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 6c848160afc6a6a755e967dd8517e48240bc113e
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94685881"
---
# <a name="create-an-ingress-controller-to-an-internal-virtual-network-in-azure-kubernetes-service-aks"></a>Vytvoření kontroleru příchozího přenosu dat do interní virtuální sítě ve službě Azure Kubernetes (AKS)

Kontroler příchozího přenosu dat je softwarový software, který poskytuje reverzní proxy, konfigurovatelné směrování provozu a ukončení protokolu TLS pro služby Kubernetes Services. Kubernetes příchozí prostředky se používají ke konfiguraci pravidel příchozího přenosu dat a tras pro jednotlivé služby Kubernetes. Pomocí řadiče příchozího přenosu dat a pravidel příchozího přenosu dat se dá jedna IP adresa používat ke směrování provozu do několika služeb v clusteru Kubernetes.

V tomto článku se dozvíte, jak nasadit [řadič Nginx][nginx-ingress] příchozího přenosu do clusteru Azure Kubernetes Service (AKS). Kontroler příchozího přenosu dat je nakonfigurovaný v interní privátní virtuální síti a IP adrese. Není povolený žádný externí přístup. Dvě aplikace se pak spustí v clusteru AKS, z nichž každá je přístupná přes jednu IP adresu.

Můžete také:

- [Vytvoření základního kontroleru příchozího přenosu dat s připojením k externí síti][aks-ingress-basic]
- [Povolit doplněk směrování aplikace HTTP][aks-http-app-routing]
- [Vytvoření kontroleru příchozího přenosu dat, který používá vaše vlastní certifikáty TLS][aks-ingress-own-tls]
- Vytvořte kontroler příchozího přenosu dat, který pomocí šifry umožňuje automatické generování certifikátů TLS [s dynamickou veřejnou IP adresou][aks-ingress-tls] nebo [statickou veřejnou IP adresou][aks-ingress-static-tls] .

## <a name="before-you-begin"></a>Než začnete

Tento článek používá [Helm 3][helm] k instalaci kontroleru Nginx pro příchozí přenosy. Ujistěte se, že používáte nejnovější verzi Helm a máte přístup k úložišti Helm příchozího přenosu *Nginx* . Další informace o konfiguraci a použití Helm najdete v tématu [install Applications with Helm in Azure Kubernetes Service (AKS)][use-helm].

Tento článek také vyžaduje, abyste spustili Azure CLI verze 2.0.64 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Vytvoření kontroleru příchozího přenosu dat

Ve výchozím nastavení se kontroler NGINX příchozího přenosu dat vytvoří pomocí dynamického přiřazení veřejné IP adresy. Běžným požadavkem na konfiguraci je použití interní privátní sítě a IP adresy. Tento přístup umožňuje omezit přístup k vašim službám na interní uživatele bez přístupu k externímu přístupu.

Pomocí následujícího ukázkového souboru manifestu vytvořte soubor s názvem *internal-YAML.* Tento příklad přiřadí *10.240.0.42* prostředku *loadBalancerIP* . Zadejte vlastní interní IP adresu pro použití s řadičem příchozího přenosu dat. Ujistěte se, že se tato IP adresa už ve vaší virtuální síti nepoužívá. Pokud používáte existující virtuální síť a podsíť, musíte nakonfigurovat cluster AKS se správnými oprávněními ke správě virtuální sítě a podsítě. Další informace najdete v tématu [použití sítě kubenet s vlastními rozsahy IP adres ve službě Azure Kubernetes Service (AKS)][aks-configure-kubenet-networking] nebo [Konfigurace sítě Azure CNI v Azure KUBERNETES Service (AKS)][aks-configure-advanced-networking] .

```yaml
controller:
  service:
    loadBalancerIP: 10.240.0.42
    annotations:
      service.beta.kubernetes.io/azure-load-balancer-internal: "true"
```

Teď nasaďte *Nginx a vstupní* graf s Helm. Chcete-li použít soubor manifestu vytvořený v předchozím kroku, přidejte `-f internal-ingress.yaml` parametr. Pro přidání redundance se nasadí dvě repliky kontrolerů příchozího přenosu dat NGINX s parametrem `--set controller.replicaCount`. Pokud chcete mít v clusteru AKS k dispozici více než jeden uzel, zajistěte, aby bylo možné plně využít více uzlů.

Kontroler příchozího přenosu dat je potřeba naplánovat také v uzlu Linuxu. V uzlech Windows Serveru by se kontroler příchozího přenosu dat neměl spouštět. Selektor uzlů se specifikuje pomocí parametru `--set nodeSelector`, aby plánovači Kubernetes oznámil, že má spustit kontroler příchozího přenosu dat NGINX v uzlu Linuxu.

> [!TIP]
> Následující příklad vytvoří obor názvů Kubernetes pro prostředky příchozího přenosu dat s názvem příchozí *– Basic*. Podle potřeby zadejte obor názvů pro vlastní prostředí. Pokud váš cluster AKS není Kubernetes RBAC povolený, přidejte `--set rbac.create=false` do příkazů Helm.

> [!TIP]
> Pokud chcete povolit [zachování IP adresy zdrojového klienta][client-source-ip] pro požadavky na kontejnery v clusteru, přidejte `--set controller.service.externalTrafficPolicy=Local` do příkazu Helm Install. Zdrojová IP adresa klienta je uložená v hlavičce žádosti v části *předané X-pro*. Při použití kontroleru příchozího přenosu dat s povoleným zachováním IP adresy klienta nebude předávat protokol TLS fungovat.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    -f internal-ingress.yaml \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Když se pro kontroler příchozího přenosu NGINX vytvoří služba Vyrovnávání zatížení Kubernetes, vaše interní IP adresa se přiřadí. K získání veřejné IP adresy použijte `kubectl get service` příkaz.

```console
kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller
```

Přiřazení IP adresy ke službě trvá několik minut, jak je znázorněno v následujícím příkladu výstupu:

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Zatím se nevytvořila žádná pravidla příchozího přenosu dat, takže pokud přejdete na interní IP adresu, zobrazí se 404 výchozí stránka NGINX adaptéru pro příjem dat. Pravidla příchozího přenosu dat jsou nakonfigurovaná v následujících krocích.

## <a name="run-demo-applications"></a>Spustit ukázkové aplikace

Pokud chcete zobrazit kontroler příchozího přenosu v akci, spusťte dvě ukázkové aplikace v clusteru AKS. V tomto příkladu můžete použít `kubectl apply` k nasazení dvou instancí jednoduché aplikace *Hello World* .

Vytvořte soubor *AKS-Hello. yaml* a zkopírujte ho do následujícího příkladu YAML:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld
  template:
    metadata:
      labels:
        app: aks-helloworld
    spec:
      containers:
      - name: aks-helloworld
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "Welcome to Azure Kubernetes Service (AKS)"
---
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld
```

Vytvořte soubor *. yaml* s příchozím demo a zkopírujte do něj následující příklad YAML:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ingress-demo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ingress-demo
  template:
    metadata:
      labels:
        app: ingress-demo
    spec:
      containers:
      - name: ingress-demo
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "AKS Ingress Demo"
---
apiVersion: v1
kind: Service
metadata:
  name: ingress-demo
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: ingress-demo
```

Spusťte dvě ukázkové aplikace pomocí `kubectl apply` :

```console
kubectl apply -f aks-helloworld.yaml --namespace ingress-basic
kubectl apply -f ingress-demo.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Vytvoření trasy příchozího přenosu dat

Obě aplikace jsou teď spuštěné v clusteru Kubernetes. Pokud chcete směrovat provoz do každé aplikace, vytvořte Kubernetes prostředek příchozího přenosu dat. Prostředek příchozího přenosu dat konfiguruje pravidla, která směrují provoz do jedné z těchto dvou aplikací.

V následujícím příkladu je přenos do adresy `http://10.240.0.42/` směrován do služby s názvem `aks-helloworld` . Provoz na adresu `http://10.240.0.42/hello-world-two` je směrován do `ingress-demo` služby.

Vytvořte soubor s názvem `hello-world-ingress.yaml` a zkopírujte ho do následujícího příkladu YAML.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /hello-world-one(/|$)(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
```

Pomocí příkazu vytvořte prostředek příchozího přenosu dat `kubectl apply -f hello-world-ingress.yaml` .

```console
kubectl apply -f hello-world-ingress.yaml
```

Následující příklad výstupu ukazuje vytvoření prostředku příchozího přenosu dat.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>Test řadiče pro příchozí přenosy

Pokud chcete testovat trasy pro kontroler příchozího přenosu dat, vyhledejte tyto dvě aplikace pomocí webového klienta. V případě potřeby můžete tuto interní funkci jenom rychle otestovat z části pod v clusteru AKS. Vytvořte test pod a připojte k němu relaci terminálu:

```console
kubectl run -it --rm aks-ingress-test --image=debian --namespace ingress-basic
```

Nainstalujte `curl` v části pod pomocí `apt-get` :

```console
apt-get update && apt-get install -y curl
```

Teď máte přístup k adrese vašeho kontroleru Kubernetes příchozího přenosu dat pomocí `curl` , jako je například *http://10.240.0.42* . Zadejte svou vlastní interní IP adresu, kterou jste nasadili při nasazení kontroleru příchozího přenosu dat v prvním kroku tohoto článku.

```console
curl -L http://10.240.0.42
```

S adresou se nezadala žádná další cesta, takže kontroler příchozího přenosu je výchozí pro */* trasu. Vrátí se první ukázková aplikace, jak je znázorněno v následujícím zhuštěném příkladu výstupu:

```
$ curl -L http://10.240.0.42

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

Nyní přidejte cestu */Hello-World-Two* k adrese, například *http://10.240.0.42/hello-world-two* . Vrátí se druhá ukázková aplikace s vlastním názvem, jak je znázorněno v následujícím zhuštěném příkladu výstupu:

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

Tento článek používá Helm k instalaci komponent příchozího přenosu dat. Když nasadíte graf Helm, vytvoří se několik prostředků Kubernetes. Tyto prostředky zahrnují lusky, nasazení a služby. Chcete-li vyčistit tyto prostředky, můžete buď odstranit celý vzorový obor názvů, nebo jednotlivé prostředky.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Odstranění ukázkového oboru názvů a všech prostředků

Chcete-li odstranit celý vzorový obor názvů, použijte `kubectl delete` příkaz a zadejte název oboru názvů. Všechny prostředky v oboru názvů jsou odstraněny.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Odstranit prostředky jednotlivě

Další možností je podrobnější přístup k odstranění jednotlivých vytvořených prostředků. Seznam vydaných verzí Helm pomocí `helm list` příkazu. 

```console
helm list --namespace ingress-basic
```

Vyhledejte grafy s názvem *Nginx-* příchozí a *AKS-HelloWorld*, jak je znázorněno v následujícím příkladu výstupu:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1  
```

Odinstalujte verze pomocí `helm uninstall` příkazu.

```console
helm uninstall nginx-ingress --namespace ingress-basic
```

Následující příklad odinstaluje nasazení NGINX příchozího přenosu dat.

```
$ helm uninstall nginx-ingress --namespace ingress-basic

release "nginx-ingress" uninstalled
```

Dále odeberte dvě ukázkové aplikace:

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
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
[helm-cli]: ./kubernetes-helm.md
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
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md