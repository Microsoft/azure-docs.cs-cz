---
title: Použití certifikátů TLS pro příchozí přenosy
titleSuffix: Azure Kubernetes Service
description: Naučte se, jak nainstalovat a nakonfigurovat řadič NGINX příchozího přenosu dat, který používá vaše vlastní certifikáty v clusteru Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: ee196bd749ad5821a1855d4549b22698c724b3f5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100104922"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>Vytvoření kontroleru příchozího přenosu dat protokolu HTTPS a použití vlastních certifikátů TLS ve službě Azure Kubernetes Service (AKS)

Kontroler příchozího přenosu dat je softwarový software, který poskytuje reverzní proxy, konfigurovatelné směrování provozu a ukončení protokolu TLS pro služby Kubernetes Services. Kubernetes příchozí prostředky se používají ke konfiguraci pravidel příchozího přenosu dat a tras pro jednotlivé služby Kubernetes. Pomocí řadiče příchozího přenosu dat a pravidel příchozího přenosu dat se dá jedna IP adresa používat ke směrování provozu do několika služeb v clusteru Kubernetes.

V tomto článku se dozvíte, jak nasadit [řadič Nginx][nginx-ingress] příchozího přenosu do clusteru Azure Kubernetes Service (AKS). Vygenerujete vlastní certifikáty a vytvoříte Kubernetes tajný klíč pro použití s trasou příchozího přenosu dat. Nakonec se dvě aplikace spouští v clusteru AKS, z nichž každá je přístupná přes jednu IP adresu.

Můžete také:

- [Vytvoření základního kontroleru příchozího přenosu dat s připojením k externí síti][aks-ingress-basic]
- [Povolit doplněk směrování aplikace HTTP][aks-http-app-routing]
- [Vytvoření kontroleru příchozího přenosu dat, který používá interní privátní síť a IP adresu][aks-ingress-internal]
- Vytvořte kontroler příchozího přenosu dat, který pomocí šifry umožňuje automatické generování certifikátů TLS [s dynamickou veřejnou IP adresou][aks-ingress-tls] nebo [statickou veřejnou IP adresou][aks-ingress-static-tls] .

## <a name="before-you-begin"></a>Než začnete

Tento článek používá [Helm 3][helm] k instalaci kontroleru Nginx pro příchozí přenosy. Ujistěte se, že používáte nejnovější verzi Helm a máte přístup k úložišti Helm příchozího přenosu *Nginx* . Pokyny k upgradu najdete v [dokumentaci k instalaci Helm][helm-install]. Další informace o konfiguraci a použití Helm najdete v tématu [install Applications with Helm in Azure Kubernetes Service (AKS)][use-helm].

Tento článek také vyžaduje, abyste spustili Azure CLI verze 2.0.64 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Vytvoření kontroleru příchozího přenosu dat

Pokud chcete vytvořit kontroler příchozího přenosu dat, použijte `Helm` k instalaci *Nginx-* příchozí. Pro přidání redundance se nasadí dvě repliky kontrolerů příchozího přenosu dat NGINX s parametrem `--set controller.replicaCount`. Pokud chcete mít v clusteru AKS k dispozici více než jeden uzel, zajistěte, aby bylo možné plně využít více uzlů.

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
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Během instalace se vytvoří veřejná IP adresa Azure pro kontroler příchozího přenosu dat. Tato veřejná IP adresa je statická pro životní cyklus řadiče pro příchozí přenos dat. Pokud adaptér příchozího přenosu odstraníte, bude přiřazení veřejné IP adresy ztraceno. Pokud pak vytvoříte další kontroler příchozího přenosu dat, přiřadí se nová veřejná IP adresa. Pokud chcete zachovat použití veřejné IP adresy, můžete místo toho [vytvořit kontroler příchozího přenosu se statickou veřejnou IP adresou][aks-ingress-static-tls].

K získání veřejné IP adresy použijte `kubectl get service` příkaz.

```console
kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller
```

Přiřazení IP adresy ke službě trvá několik minut.

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Poznamenejte si tuto veřejnou IP adresu, jak je použit v posledním kroku k otestování nasazení.

Zatím se nevytvořila žádná pravidla pro příchozí přenosy. Pokud přejdete na veřejnou IP adresu, zobrazí se výchozí stránka 404 adaptéru NGINX pro příchozí přenosy.

## <a name="generate-tls-certificates"></a>Generování certifikátů TLS

V tomto článku vygenerujeme certifikát podepsaný svým držitelem `openssl` . V případě produkčního použití byste měli požádat o důvěryhodný, podepsaný certifikát prostřednictvím poskytovatele nebo vlastní certifikační autority (CA). V dalším kroku vygenerujete *tajný klíč* Kubernetes pomocí certifikátu TLS a privátního klíče vygenerovaného pomocí OpenSSL.

Následující příklad vygeneruje 2048 certifikát RSA x509 platný pro 365 dní s názvem *AKS-Ingress-TLS. CRT*. Soubor privátního klíče má název *AKS-Ingress-TLS. Key*. Tajný klíč Kubernetes TLS vyžaduje oba tyto soubory.

Tento článek spolupracuje s běžným názvem subjektu *demo.Azure.com* a nemusí se měnit. Pro produkční použití zadejte vlastní hodnoty organizace pro `-subj` parametr:

```console
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out aks-ingress-tls.crt \
    -keyout aks-ingress-tls.key \
    -subj "/CN=demo.azure.com/O=aks-ingress-tls"
```

## <a name="create-kubernetes-secret-for-the-tls-certificate"></a>Vytvoření tajného klíče Kubernetes pro certifikát TLS

Pokud chcete, aby Kubernetes mohl používat certifikát TLS a privátní klíč pro řadič příchozího přenosu dat, vytvoříte a použijete tajný klíč. Tajný kód je definován jednou a používá certifikát a soubor klíčů vytvořený v předchozím kroku. Pak se na tento tajný klíč odkazuje při definování směrování příchozího přenosu dat.

Následující příklad vytvoří tajný název *AKS-příchozí-TLS*:

```console
kubectl create secret tls aks-ingress-tls \
    --namespace ingress-basic \
    --key aks-ingress-tls.key \
    --cert aks-ingress-tls.crt
```

## <a name="run-demo-applications"></a>Spustit ukázkové aplikace

Byl nakonfigurován kontroler příchozího přenosu dat a tajný kód s vaším certifikátem. Teď spustíme dvě ukázkové aplikace v clusteru AKS. V tomto příkladu se Helm používá k nasazení dvou instancí jednoduché aplikace Hello World.

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

Obě aplikace jsou teď spuštěné v clusteru Kubernetes, ale nakonfigurují se se službou typu `ClusterIP` . Aplikace proto nejsou přístupné z Internetu. Pokud je chcete zpřístupnit veřejnosti, vytvořte Kubernetes prostředek příchozího přenosu dat. Prostředek příchozího přenosu dat konfiguruje pravidla, která směrují provoz do jedné z těchto dvou aplikací.

V následujícím příkladu je přenos do adresy `https://demo.azure.com/` směrován do služby s názvem `aks-helloworld` . Provoz na adresu `https://demo.azure.com/hello-world-two` je směrován do `ingress-demo` služby. V tomto článku nemusíte tyto názvy ukázkových hostitelů měnit. V části použití v produkčním prostředí zadejte názvy zadané jako součást procesu žádosti o certifikát a procesu generování.

> [!TIP]
> Pokud se název hostitele zadaný během procesu žádosti o certifikát, název CN, neshoduje s hostitelem definovaným v trase příchozího přenosu dat, zobrazí se na řadiči příchozího upozornění *Kubernetes příchozí certifikát* . Zajistěte, aby se názvy hostitelů a certifikátů příchozího směrování shodovaly.

Část *TLS* oznamuje trase příchozího přenosu dat s názvem *AKS-Inuse-tls* pro hostitele *demo.Azure.com*. Pro produkční použití zadejte svou vlastní adresu hostitele.

Vytvořte soubor s názvem `hello-world-ingress.yaml` a zkopírujte ho do následujícího příkladu YAML.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /$2
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

Ukázkový výstup ukazuje, že je vytvořen prostředek příchozího přenosu dat.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>Test konfigurace příchozího přenosu dat

K otestování certifikátů pomocí našeho falešného hostitele *demo.Azure.com* použijte `curl` a zadejte parametr *--Resolve* . Tento parametr umožňuje mapovat *demo.Azure.com* název na veřejnou IP adresu vašeho kontroleru příchozího přenosu dat. Zadejte veřejnou IP adresu vlastního kontroleru příchozího přenosu dat, jak je znázorněno v následujícím příkladu:

```
curl -v -k --resolve demo.azure.com:443:EXTERNAL_IP https://demo.azure.com
```

S adresou se nezadala žádná další cesta, takže kontroler příchozího přenosu je výchozí pro */* trasu. Vrátí se první ukázková aplikace, jak je znázorněno v následujícím zhuštěném příkladu výstupu:

```
$ curl -v -k --resolve demo.azure.com:443:EXTERNAL_IP https://demo.azure.com

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

Parametr *-v* v našem `curl` příkazu obsahuje podrobné informace, včetně přijatého certifikátu protokolu TLS. Poloduplexní výstup můžete ověřit, že se použil vlastní certifikát TLS. Parametr *-k* pokračuje v načítání stránky i v případě, že používáme certifikát podepsaný svým držitelem. Následující příklad ukazuje, že *Vystavitel: CN = demo. Azure. com; O = AKS-* příchozí certifikát TLS byl použit:

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

Nyní přidejte cestu */Hello-World-Two* k adrese, například `https://demo.azure.com/hello-world-two` . Vrátí se druhá ukázková aplikace s vlastním názvem, jak je znázorněno v následujícím zhuštěném příkladu výstupu:

```
$ curl -v -k --resolve demo.azure.com:443:EXTERNAL_IP https://demo.azure.com/hello-world-two

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tento článek používá Helm k instalaci komponent příchozího přenosu dat a ukázkových aplikací. Když nasadíte graf Helm, vytvoří se několik prostředků Kubernetes. Mezi tyto prostředky patří lusky, nasazení a služby. Chcete-li vyčistit tyto prostředky, můžete buď odstranit celý vzorový obor názvů, nebo jednotlivé prostředky.

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

Vyhledejte graf s názvem *Nginx-* příchozí, jak je znázorněno v následujícím příkladu výstupu:

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

Odstraňte tajný klíč certifikátu:

```console
kubectl delete secret aks-ingress-tls --namespace ingress-basic
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
- [Vytvoření kontroleru příchozího přenosu dat, který používá interní privátní síť a IP adresu][aks-ingress-internal]
- Vytvořte kontroler příchozího přenosu dat, který pomocí šifry umožňuje automatické generování certifikátů TLS [s dynamickou veřejnou IP adresou][aks-ingress-tls] nebo [statickou veřejnou IP adresou][aks-ingress-static-tls] .

<!-- LINKS - external -->
[helm-cli]: ./kubernetes-helm.md
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm]: https://helm.sh/
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
