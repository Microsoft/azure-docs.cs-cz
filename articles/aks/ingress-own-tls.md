---
title: Použít certifikáty protokolu TLS pro příchozí přenos dat se cluster Azure Kubernetes Service (AKS)
description: Zjistěte, jak nainstalovat a nakonfigurovat kontroler příchozího přenosu dat serveru NGINX, která používá vlastní certifikáty v clusteru služby Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 10/23/2018
ms.author: iainfou
ms.openlocfilehash: c109febc90c9dd8d9b17489c9e612f677695bd25
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55727088"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>Vytvoření řadiče příchozího přenosu dat protokolu HTTPS a používat vlastní certifikáty TLS ve službě Azure Kubernetes Service (AKS)

Řadič služby příchozího přenosu dat je část softwaru, která poskytuje reverzní proxy server, směrování provozu konfigurovatelné a ukončení protokolu TLS pro služby Kubernetes. Prostředky Kubernetesu příchozího přenosu dat se používají ke konfiguraci pravidla příchozího přenosu dat a trasy pro jednotlivé služby Kubernetes. Použití kontroler příchozího přenosu dat a pravidla příchozího přenosu dat, jednu IP adresu je možné směrovat provoz do více služeb v clusteru Kubernetes.

V tomto článku se dozvíte, jak nasadit [kontroler příchozího přenosu dat NGINX] [ nginx-ingress] v clusteru služby Azure Kubernetes Service (AKS). Generovat vlastní certifikáty a vytvoření tajného kódu pro použití Kubernetes s příchozího přenosu dat trasy. Nakonec jsou dvě aplikace běží v clusteru AKS, z nichž každý je přístupný přes jednu IP adresu.

Můžete také:

- [Vytvoření základního příchozího přenosu dat řadiče s externí síťové připojení.][aks-ingress-basic]
- [Povolit doplněk směrování aplikace HTTP][aks-http-app-routing]
- [Vytvoření řadiče příchozího přenosu dat, který se používá soukromé, interní sítě a IP adresy][aks-ingress-internal]
- Vytvoření řadiče příchozího přenosu dat, která používá umožňuje šifrovat automaticky generovat certifikáty TLS [s dynamické veřejné IP adresy] [ aks-ingress-tls] nebo [se statickou veřejnou IP adresu][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Před zahájením

Tento článek používá Helm k instalaci serveru NGINX kontroler příchozího přenosu dat a ukázkovou webovou aplikaci. Musíte mít Helm inicializován v rámci clusteru AKS a pomocí účtu služby pro Tiller. Ujistěte se, že používáte nejnovější verzi nástroje Helm. Pokyny k upgradu, najdete v článku [Helm instalace dokumentace][helm-install]. Další informace o konfiguraci a použití Helm, naleznete v tématu [instalace aplikací s nástrojem Helm ve službě Azure Kubernetes Service (AKS)][use-helm].

Tento článek také vyžaduje, že používáte Azure CLI verze 2.0.41 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Vytvoření řadiče příchozího přenosu dat

Chcete-li vytvořit kontroler příchozího přenosu dat, použijte `Helm` instalace *nginx příchozího přenosu dat*. Pro přidání redundance dvě repliky řadiče příchozího přenosu dat NGINX nasazení se používají `--set controller.replicaCount` parametru. Pokud chcete naplno využívat s replikami kontroler příchozího přenosu dat, ujistěte se, že existuje více než jeden uzel v clusteru AKS.

> [!TIP]
> Následující příklad nainstaluje kontroler příchozího přenosu dat v `kube-system` oboru názvů. V případě potřeby, můžete určit jiný obor názvů pro vlastní prostředí. Pokud váš cluster AKS není povoleno RBAC, přidejte `--set rbac.create=false` příkazy.

```console
helm install stable/nginx-ingress --namespace kube-system --set controller.replicaCount=2
```

Pro kontroler příchozího přenosu dat se vytvoří během instalace Azure veřejnou IP adresu. Tato veřejná IP adresa je statická dobu životnosti kontroleru příchozího přenosu. Pokud odstraníte kontroler příchozího přenosu dat, dojde ke ztrátě přiřazení veřejné IP adresy. Pokud potom vytvoříte řadič další příchozí přenos dat, se přiřadí novou veřejnou IP adresu. Pokud chcete zachovat použijte veřejnou IP adresu, můžete místo toho [vytvořit řadič příchozího přenosu dat se statickou veřejnou IP adresu][aks-ingress-static-tls].

Chcete-li získat veřejnou IP adresu, použijte `kubectl get service` příkazu. Trvá několik minut, než IP adresu, která přiřazené příslušné službě.

```
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                          TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
virulent-seal-nginx-ingress-controller        LoadBalancer   10.0.48.240   40.87.46.190   80:31159/TCP,443:30657/TCP   7m
virulent-seal-nginx-ingress-default-backend   ClusterIP      10.0.50.5     <none>         80/TCP                       7m
```

Poznamenejte si tuto veřejnou IP adresu, ujistěte se, jak ji v posledním kroku slouží k otestování nasazení.

Žádná pravidla příchozího přenosu dat dosud nebyly vytvořeny. Pokud přejdete na veřejnou IP adresu, zobrazí se stránka 404 výchozí kontroler příchozího přenosu dat serveru NGINX.

## <a name="generate-tls-certificates"></a>Vygenerovat certifikáty TLS

Pro účely tohoto článku, umožňuje vygenerovat certifikát podepsaný svým držitelem s `openssl`. Pro použití v produkčním prostředí by měl požádat o certifikát důvěryhodný, podepsaný prostřednictvím zprostředkovatele nebo svou vlastní certifikační autoritu (CA). V dalším kroku vygenerujete Kubernetes *tajný kód* pomocí TLS certifikát a privátní klíč vygenerovaný OpenSSL.

Následující příklad generuje 2048bitového algoritmu RSA X 509 certifikát platný pro 365 dnů s názvem *aks – příchozí přenos dat – tls.crt*. Název souboru privátního klíče *aks – příchozí přenos dat – tls.key*. Tajného kódu Kubernetes TLS vyžaduje oba soubory zkompilovány.

V tomto článku funguje s *demo.azure.com* běžný název subjektu a není potřeba změnit. Pro použití v produkčním prostředí, zadat svoje vlastní organizace pro `-subj` parametr:

```console
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out aks-ingress-tls.crt \
    -keyout aks-ingress-tls.key \
    -subj "/CN=demo.azure.com/O=aks-ingress-tls"
```

## <a name="create-kubernetes-secret-for-the-tls-certificate"></a>Vytvoření tajného klíče pro certifikát TLS Kubernetes

Pokud chcete povolit Kubernetes pro kontroler příchozího přenosu dat používat certifikát TLS a privátní klíč, vytvořte a používejte tajný klíč. Tajný kód je definován jednou a pomocí certifikátu a klíče soubor vytvořený v předchozím kroku. Je tento tajný kód pak odkazovat při definování příchozího přenosu dat trasy.

Následující příklad vytvoří název tajného kódu *aks – příchozí přenos dat – protokol tls*:

```console
kubectl create secret tls aks-ingress-tls \
    --key aks-ingress-tls.key \
    --cert aks-ingress-tls.crt
```

## <a name="run-demo-applications"></a>Spuštění ukázkové aplikace

Byly nakonfigurovány kontroler příchozího přenosu dat a tajný klíč vaším certifikátem. Teď Pojďme spuštění dvě ukázkové aplikace ve vašem clusteru AKS. V tomto příkladu Helm umožňuje nasadit dvě instance aplikace jednoduchý "Hello world".

Před instalací ukázkové grafy Helm, přidejte úložiště ukázky v Azure následujícím způsobem pro vaše prostředí helmu:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Vytvoření první ukázkové aplikace z grafu helmu pomocí následujícího příkazu:

```console
helm install azure-samples/aks-helloworld
```

Nyní instalaci druhé instance ukázkové aplikace. Pro druhou instanci zadejte nový název tak, že dvě aplikace jsou vizuálně distinct. Můžete také zadat jedinečný název služby:

```console
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Vytvoření trasy příchozího přenosu dat

Obě aplikace běží v clusteru Kubernetes, ale jsou nakonfigurované s využitím služby typu `ClusterIP`. Aplikace v důsledku toho nejsou přístupné z Internetu. Aby se daly veřejně k dispozici, vytvořte prostředek příchozího přenosu dat Kubernetes. Prostředek příchozího přenosu dat nakonfiguruje pravidla, která směrovat provoz mezi těmito dvěma aplikacemi.

V následujícím příkladu, provoz na adresu `https://demo.azure.com/` se směruje na službu s názvem `aks-helloworld`. Provoz na adresu `https://demo.azure.com/hello-world-two` směrován `ingress-demo` služby. Pro účely tohoto článku nemusíte změnit názvy hostitelů ukázku. Pro použití v produkčním prostředí zadejte názvy zadané jako část procesu žádosti a generování certifikátu.

> [!TIP]
> Pokud zadaný během procesu žádosti o certifikát, CN název, název hostitele není souhlasit s hostitelem definované v trase vaše příchozího přenosu dat, můžete příchozího přenosu dat kontroleru zobrazí *Kubernetes příchozího přenosu dat Kontroleru falešné certifikát*. Ujistěte se, že certifikát a příchozího přenosu dat trasy shoda názvy hostitelů.

*Tls* části říká příchozího přenosu dat trasy, která má použijte tajný klíč s názvem *aks – příchozí přenos dat – protokol tls* hostitele *demo.azure.com*. Znovu pro použití v produkčním prostředí, zadejte adresu hostitele.

Vytvořte soubor s názvem `hello-world-ingress.yaml` a kopie v následujícím příkladu YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  tls:
  - hosts:
    - demo.azure.com
    secretName: aks-ingress-tls
  rules:
  - host: demo.azure.com
    http:
      paths:
      - path: /
        backend:
          serviceName: aks-helloworld
          servicePort: 80
      - path: /hello-world-two
        backend:
          serviceName: ingress-demo
          servicePort: 80
```

Vytvoření prostředků pomocí příchozího přenosu dat `kubectl apply -f hello-world-ingress.yaml` příkazu.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>Otestujte konfiguraci příchozího přenosu dat

K otestování certifikátů pomocí našich phishing *demo.azure.com* hostovat, použijte `curl` a zadejte *– vyřešit* parametr. Tento parametr umožňuje mapovat *demo.azure.com* názvem pro veřejnou IP adresu příchozího přenosu dat řadiče. Zadejte veřejnou IP adresu z vaší vlastní kontroler příchozího přenosu dat, jak je znázorněno v následujícím příkladu:

```
curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com
```

Žádné další cesty byla zadaná s adresou, takže kontroler příchozího přenosu dat výchozí hodnota */* trasy. Vrátí první ukázkové aplikace, jak je znázorněno v následujícím výstupu zhuštěnému příkladu:

```
$ curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

*- V* parametr v našich `curl` příkaz vypíše podrobné informace, včetně certifikát TLS přijata. Poloviny pomocí curl výstupu, můžete ověřit, že byl použit vlastní certifikát TLS. *-K* parametru pokračuje v načítání stránky, i když používáme certifikát podepsaný svým držitelem. Následující příklad ukazuje, že *vystavitele: CN=Demo.Azure.com; O = aks – příchozí přenos dat – protokol tls* certifikát se používá:

```
[...]
* Server certificate:
*  subject: CN=demo.azure.com; O=aks-ingress-tls
*  start date: Oct 22 22:13:54 2018 GMT
*  expire date: Oct 22 22:13:54 2019 GMT
*  issuer: CN=demo.azure.com; O=aks-ingress-tls
*  SSL certificate verify result: self signed certificate (18), continuing anyway.
[...]
```

Nyní přidejte */hello-world-two* cestě na adresu, jako například `https://demo.azure.com/hello-world-two`. Vrátí druhou ukázkové aplikace s vlastní název, jak je znázorněno v následujícím výstupu zhuštěnému příkladu:

```
$ curl -v -k --resolve demo.azure.com:443:137.117.36.18 https://demo.azure.com/hello-world-two

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tento článek používá Helm k instalaci součásti příchozího přenosu dat a ukázkové aplikace. Při nasazování grafu helmu vytvoří řada prostředky Kubernetesu. Tyto prostředky zahrnují podů, nasazení a služby. Vyčistit, nejprve seznamu Helm vydané verze s `helm list` příkazu. Vyhledejte grafy s názvem *nginx příchozího přenosu dat* a *aks-helloworld*, jak je znázorněno v následujícím příkladu výstupu:

```
$ helm list

NAME            REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
virulent-seal   1           Tue Oct 23 16:37:24 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
billowing-guppy 1           Tue Oct 23 16:41:38 2018    DEPLOYED    aks-helloworld-0.1.0                default
listless-quokka 1           Tue Oct 23 16:41:30 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Odstranit vydané verze s `helm delete` příkazu. Následující příklad odstraní nasazení serveru NGINX příchozího přenosu dat a dvě ukázkové AKS hello world aplikace.

```
$ helm delete virulent-seal billowing-guppy listless-quokka

release "virulent-seal" deleted
release "billowing-guppy" deleted
release "listless-quokka" deleted
```

V dalším kroku odeberte Helm úložiště pro aplikace hello world AKS:

```console
helm repo remove azure-samples
```

Odeberte trasu příchozího přenosu dat, která přesměruje přenosy do ukázkové aplikace:

```console
kubectl delete -f hello-world-ingress.yaml
```

Nakonec odeberte certifikát tajný kód:

```console
kubectl delete secret aks-ingress-tls
```

## <a name="next-steps"></a>Další postup

Tento článek zahrnuty některé externí součásti pro AKS. Další informace o těchto komponent, naleznete na následujících stránkách projektu:

- [Helm CLI][helm-cli]
- [Kontroler příchozího přenosu dat serveru NGINX][nginx-ingress]

Můžete také:

- [Vytvoření základního příchozího přenosu dat řadiče s externí síťové připojení.][aks-ingress-basic]
- [Povolit doplněk směrování aplikace HTTP][aks-http-app-routing]
- [Vytvoření řadiče příchozího přenosu dat, který se používá soukromé, interní sítě a IP adresy][aks-ingress-internal]
- Vytvoření řadiče příchozího přenosu dat, která používá umožňuje šifrovat automaticky generovat certifikáty TLS [s dynamické veřejné IP adresy] [ aks-ingress-tls] nebo [se statickou veřejnou IP adresu][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-tls]: ingress-tls.md
