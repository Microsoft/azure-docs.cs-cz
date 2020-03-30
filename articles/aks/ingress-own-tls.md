---
title: Použití vlastních certifikátů TLS pro příchozí přenos dat pomocí clusteru Služby Azure Kubernetes Service (AKS)
description: Zjistěte, jak nainstalovat a nakonfigurovat řadič příchozího přenosu dat NGINX, který používá vaše vlastní certifikáty v clusteru služby Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: e567f5384cdd1e40ea67284713a29a92ee87af7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595497"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>Vytvoření kontroleru příchozího přenosu dat protokolu HTTPS a použití vlastních certifikátů TLS ve službě Azure Kubernetes Service (AKS)

Řadič příchozího přenosu dat je software, který poskytuje reverzní proxy server, konfigurovatelné směrování provozu a ukončení TLS pro služby Kubernetes. Prostředky příchozího přenosu dat kubernetes se používají ke konfiguraci pravidel příchozího přenosu dat a tras pro jednotlivé služby Kubernetes. Pomocí řadiče příchozího přenosu dat a pravidel příchozího přenosu dat lze jednu adresu IP použít k směrování provozu do více služeb v clusteru Kubernetes.

Tento článek ukazuje, jak nasadit [řadič příchozího přenosu dat NGINX][nginx-ingress] v clusteru služby Azure Kubernetes Service (AKS). Vygenerujete vlastní certifikáty a vytvoříte tajný klíč Kubernetes pro použití s trasou příchozího přenosu dat. Nakonec jsou v clusteru AKS spuštěny dvě aplikace, z nichž každá je přístupná přes jednu adresu IP.

Můžete také:

- [Vytvoření základního řadiče příchozího přenosu dat s externím připojením k síti][aks-ingress-basic]
- [Povolení doplňku směrování aplikací HTTP][aks-http-app-routing]
- [Vytvoření řadiče příchozího přenosu dat, který používá interní, privátní síť a adresu IP][aks-ingress-internal]
- Vytvoření řadiče příchozího přenosu dat, který používá let's Encrypt k automatickému generování certifikátů TLS [s dynamickou veřejnou IP adresou][aks-ingress-tls] nebo [statickou veřejnou IP adresou][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Než začnete

Tento článek používá Helm k instalaci řadiče příchozího přenosu dat NGINX a ukázkové webové aplikace. Musíte mít Helm inicializovány v rámci clusteru AKS a pomocí účtu služby tiller. Ujistěte se, že používáte nejnovější verzi Helmu. Pokyny k upgradu naleznete v [dokumentech k instalaci helmy][helm-install]. Další informace o konfiguraci a používání helmu najdete v [tématu Instalace aplikací pomocí helmu ve službě Azure Kubernetes Service (AKS).][use-helm]

Tento článek také vyžaduje, abyste spouštěli Azure CLI verze 2.0.64 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Vytvoření řadiče příchozího přenosu dat

Chcete-li vytvořit řadič `Helm` příchozího přenosu dat, použijte k instalaci *nginx-ingress*. Pro přidání redundance se nasadí dvě repliky kontrolerů příchozího přenosu dat NGINX s parametrem `--set controller.replicaCount`. Chcete-li plně využít spuštění replik řadiče příchozího přenosu dat, ujistěte se, že je více než jeden uzel v clusteru AKS.

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
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Během instalace se vytvoří veřejná IP adresa Azure pro řadič příchozího přenosu dat. Tato veřejná IP adresa je statická pro životnost řadiče příchozího přenosu dat. Pokud odstraníte řadič příchozího přenosu dat, dojde ke ztrátě přiřazení veřejné IP adresy. Pokud pak vytvoříte další řadič příchozího přenosu dat, bude přiřazena nová veřejná IP adresa. Pokud chcete zachovat použití veřejné IP adresy, můžete místo toho [vytvořit řadič příchozího přenosu dat se statickou veřejnou IP adresou][aks-ingress-static-tls].

Chcete-li získat veřejnou `kubectl get service` IP adresu, použijte příkaz. Trvá několik minut, než bude ip adresa přiřazena ke službě.

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                          TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
virulent-seal-nginx-ingress-controller        LoadBalancer   10.0.48.240   40.87.46.190   80:31159/TCP,443:30657/TCP   7m
virulent-seal-nginx-ingress-default-backend   ClusterIP      10.0.50.5     <none>         80/TCP                       7m
```

Poznamenejte si tuto veřejnou IP adresu, jak se používá v posledním kroku k testování nasazení.

Dosud nebyla vytvořena žádná pravidla příchozího přenosu dat. Pokud přejdete na veřejnou IP adresu, zobrazí se výchozí stránka 404 řadiče příchozího přenosu dat NGINX.

## <a name="generate-tls-certificates"></a>Generovat certifikáty TLS

Pro tento článek vygenerujeme certifikát podepsaný `openssl`svým držitelem s . Pro použití v produkčním prostředí byste měli požádat o důvěryhodný podepsaný certifikát prostřednictvím poskytovatele nebo vlastní certifikační autority (CA). V dalším kroku vygenerujete Tajný *klíč* Kubernetes pomocí certifikátu TLS a soukromého klíče generovaného OpenSSL.

Následující příklad generuje certifikát RSA X509 o 2048 bitech platný po dobu 365 dnů s názvem *aks-ingress-tls.crt*. Soubor soukromého klíče má název *aks-ingress-tls.key*. Tajný klíč TLS Kubernetes vyžaduje oba tyto soubory.

Tento článek pracuje s *demo.azure.com* předmětu běžný název a není třeba měnit. Pro použití v produkčním prostředí zadejte vlastní hodnoty organizace pro `-subj` parametr:

```console
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out aks-ingress-tls.crt \
    -keyout aks-ingress-tls.key \
    -subj "/CN=demo.azure.com/O=aks-ingress-tls"
```

## <a name="create-kubernetes-secret-for-the-tls-certificate"></a>Vytvoření tajného klíče Kubernetes pro certifikát TLS

Chcete-li povolit Kubernetes používat certifikát TLS a soukromý klíč pro řadič příchozího přenosu dat, vytvořte a použijte tajný klíč. Tajný klíč je definován jednou a používá certifikát a soubor klíče vytvořený v předchozím kroku. Potom odkazovat na tento tajný klíč při definování příchozího přenosu dat trasy.

Následující příklad vytvoří tajný název *aks-ingress-tls*:

```console
kubectl create secret tls aks-ingress-tls \
    --namespace ingress-basic \
    --key aks-ingress-tls.key \
    --cert aks-ingress-tls.crt
```

## <a name="run-demo-applications"></a>Spuštění ukázkových aplikací

Byl nakonfigurován řadič příchozího přenosu dat a tajný klíč s certifikátem. Nyní spustíme dvě ukázkové aplikace v clusteru AKS. V tomto příkladu Helm se používá k nasazení dvou instancí jednoduché aplikace "Hello world".

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

Obě aplikace jsou nyní spuštěny v clusteru Kubernetes, ale `ClusterIP`jsou nakonfigurovány se službou typu . Jako takové aplikace nejsou přístupné z internetu. Chcete-li je zpřístupnit veřejně, vytvořte prostředek příchozího přenosu dat Kubernetes. Prostředek příchozího přenosu dat konfiguruje pravidla, která směrují provoz do jedné ze dvou aplikací.

V následujícím příkladu je `https://demo.azure.com/` provoz na adresu směrován `aks-helloworld`na službu s názvem . Provoz na `https://demo.azure.com/hello-world-two` adresu je směrován `ingress-demo` do služby. Pro tento článek není nutné měnit tyto názvy demo hostitelů. Pro použití v produkčním prostředí zadejte názvy zadané jako součást žádosti o certifikát a proces generování certifikátu.

> [!TIP]
> Pokud název hostitele zadaný během procesu žádosti o certifikát, název CN, neodpovídá hostiteli definovanému v příchozí mačkání cesta, zobrazí se řadič příchozího přenosu dat upozornění *řadiče příchozího přenosu dat.* Ujistěte se, že se shodují názvy hostitelů a hostitelů příchozí trasy.

Část *tls* říká příchozí mu trase, aby používala tajné *názvy aks-ingress-tls* pro hostitelskou *demo.azure.com*. Opět pro produkční použití zadejte vlastní adresu hostitele.

Vytvořte soubor `hello-world-ingress.yaml` s názvem a zkopírujte v následujícím příkladu YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  tls:
  - hosts:
    - demo.azure.com
    secretName: aks-ingress-tls
  rules:
  - host: demo.azure.com
    http:
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

## <a name="test-the-ingress-configuration"></a>Otestovat konfiguraci příchozího přenosu dat

Chcete-li otestovat certifikáty s `curl` naší mašlovou *demo.azure.com* hostitele, použijte a zadejte parametr *--resolve.* Tento parametr umožňuje mapovat *název demo.azure.com* na veřejnou IP adresu řadiče příchozího přenosu dat. Zadejte veřejnou IP adresu vlastního řadiče příchozího přenosu dat, jak je znázorněno v následujícím příkladu:

```
curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com
```

S adresou nebyla poskytnuta žádná další cesta, takže */* řadič příchozího přenosu dat je výchozí pro trasu. První ukázková aplikace je vrácena, jak je znázorněno na následujícím výstupu zkráceného příkladu:

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

Parametr *-v* našem `curl` příkazu vyslovuje podrobné informace, včetně přijatého certifikátu TLS. V polovině výstupu curl můžete ověřit, zda byl použit vlastní certifikát TLS. Parametr *-k* pokračuje v načítání stránky, i když používáme certifikát podepsaný svým držitelem. Následující příklad ukazuje, že *vystavitel: CN=demo.azure.com; Byl použit certifikát O=aks-ingress-tls:*

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

Nyní přidejte */hello-world-two* cestu k `https://demo.azure.com/hello-world-two`adrese, například . Druhá ukázková aplikace s vlastním názvem je vrácena, jak je znázorněno na následujícím výstupu zkráceného příkladu:

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

NAME            REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
virulent-seal   1           Tue Oct 23 16:37:24 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
billowing-guppy 1           Tue Oct 23 16:41:38 2018    DEPLOYED    aks-helloworld-0.1.0                default
listless-quokka 1           Tue Oct 23 16:41:30 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Odstraňte verze pomocí `helm delete` příkazu. Následující příklad odstraní nasazení příchozího přenosu dat NGINX a dvě ukázkové aplikace AKS hello world.

```
$ helm delete virulent-seal billowing-guppy listless-quokka

release "virulent-seal" deleted
release "billowing-guppy" deleted
release "listless-quokka" deleted
```

Dále odeberte repo helmu pro aplikaci AKS hello world:

```console
helm repo remove azure-samples
```

Odeberte trasu příchozího přenosu dat, která směrovala provoz do ukázkových aplikací:

```console
kubectl delete -f hello-world-ingress.yaml
```

Odstranit tajný klíč certifikátu:

```console
kubectl delete secret aks-ingress-tls
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
- [Vytvoření řadiče příchozího přenosu dat, který používá interní, privátní síť a adresu IP][aks-ingress-internal]
- Vytvoření řadiče příchozího přenosu dat, který používá let's Encrypt k automatickému generování certifikátů TLS [s dynamickou veřejnou IP adresou][aks-ingress-tls] nebo [statickou veřejnou IP adresou][aks-ingress-static-tls]

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
[client-source-ip]: concepts-network.md#ingress-controllers
