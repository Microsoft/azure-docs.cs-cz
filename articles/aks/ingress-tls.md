---
title: Vytvoření příchozího přenosu dat pomocí automatického TLS
titleSuffix: Azure Kubernetes Service
description: Naučte se, jak nainstalovat a nakonfigurovat řadič příchozího přenosu NGINX, který používá šifrování pro automatické generování certifikátů TLS v clusteru Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 5032880ddc5d23f824adec28aee85c652bad29d2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993154"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>Vytvoření kontroleru příchozího přenosu HTTPS ve službě Azure Kubernetes (AKS)

Kontroler příchozího přenosu dat je softwarový software, který poskytuje reverzní proxy, konfigurovatelné směrování provozu a ukončení protokolu TLS pro služby Kubernetes Services. Kubernetes příchozí prostředky se používají ke konfiguraci pravidel příchozího přenosu dat a tras pro jednotlivé služby Kubernetes. Pomocí řadiče příchozího přenosu dat a pravidel příchozího přenosu dat se dá jedna IP adresa používat ke směrování provozu do několika služeb v clusteru Kubernetes.

V tomto článku se dozvíte, jak nasadit [řadič Nginx][nginx-ingress] příchozího přenosu do clusteru Azure Kubernetes Service (AKS). Projekt [Správce certifikátů][cert-manager] se používá k automatickému generování a konfiguraci certifikátů, které [umožňují šifrování][lets-encrypt] . Nakonec se dvě aplikace spouští v clusteru AKS, z nichž každá je přístupná přes jednu IP adresu.

Můžete také:

- [Vytvoření základního kontroleru příchozího přenosu dat s připojením k externí síti][aks-ingress-basic]
- [Povolit doplněk směrování aplikace HTTP][aks-http-app-routing]
- [Vytvoření kontroleru příchozího přenosu dat, který používá interní privátní síť a IP adresu][aks-ingress-internal]
- [Vytvoření kontroleru příchozího přenosu dat, který používá vaše vlastní certifikáty TLS][aks-ingress-own-tls]
- [Vytvořte kontroler příchozího přenosu dat, který pomocí šifrovaného šifrování automaticky generuje certifikáty TLS se statickou veřejnou IP adresou.][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, přečtěte si rychlý Start AKS a [použijte Azure CLI][aks-quickstart-cli] nebo [Azure Portal][aks-quickstart-portal].

Tento článek také předpokládá, že máte [vlastní doménu][custom-domain] s [zónou DNS][dns-zone] ve stejné skupině prostředků jako cluster AKS.

Tento článek používá [Helm 3][helm] k instalaci kontroleru Nginx příchozího přenosu dat a správce certifikátů. Ujistěte se, že používáte nejnovější verzi Helm a máte přístup k úložištím *Nginx* a *jetstack* Helm. Pokyny k upgradu najdete v [dokumentaci k instalaci Helm][helm-install]. Další informace o konfiguraci a použití Helm najdete v tématu [install Applications with Helm in Azure Kubernetes Service (AKS)][use-helm].

Tento článek také vyžaduje, abyste spustili Azure CLI verze 2.0.64 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Vytvoření kontroleru příchozího přenosu dat

Pokud chcete vytvořit kontroler příchozích dat, pomocí `helm` příkazu nainstalujte *Nginx-* příchozí přenos dat. Pro přidání redundance se nasadí dvě repliky kontrolerů příchozího přenosu dat NGINX s parametrem `--set controller.replicaCount`. Pokud chcete mít v clusteru AKS k dispozici více než jeden uzel, zajistěte, aby bylo možné plně využít více uzlů.

Kontroler příchozího přenosu dat je potřeba naplánovat také v uzlu Linuxu. V uzlech Windows Serveru by se kontroler příchozího přenosu dat neměl spouštět. Selektor uzlů se specifikuje pomocí parametru `--set nodeSelector`, aby plánovači Kubernetes oznámil, že má spustit kontroler příchozího přenosu dat NGINX v uzlu Linuxu.

> [!TIP]
> Následující příklad vytvoří obor názvů Kubernetes pro prostředky příchozího přenosu dat s názvem příchozí *– Basic*. Podle potřeby zadejte obor názvů pro vlastní prostředí.

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
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Během instalace se vytvoří veřejná IP adresa Azure pro kontroler příchozího přenosu dat. Tato veřejná IP adresa je statická pro životní cyklus řadiče pro příchozí přenos dat. Pokud adaptér příchozího přenosu odstraníte, bude přiřazení veřejné IP adresy ztraceno. Pokud pak vytvoříte další kontroler příchozího přenosu dat, přiřadí se nová veřejná IP adresa. Pokud chcete zachovat použití veřejné IP adresy, můžete místo toho [vytvořit kontroler příchozího přenosu se statickou veřejnou IP adresou][aks-ingress-static-tls].

K získání veřejné IP adresy použijte `kubectl get service` příkaz. Přiřazení IP adresy ke službě trvá několik minut.

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Zatím se nevytvořila žádná pravidla pro příchozí přenosy. Pokud přejdete na veřejnou IP adresu, zobrazí se výchozí stránka 404 adaptéru NGINX pro příchozí přenosy.

## <a name="add-an-a-record-to-your-dns-zone"></a>Přidání záznamu A do zóny DNS

Přidejte do zóny DNS záznam *A* s externí IP adresou služby Nginx pomocí [AZ Network DNS Record-set a Add-Record][az-network-dns-record-set-a-add-record].

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name * \
    --ipv4-address MY_EXTERNAL_IP
```

> [!NOTE]
> Volitelně můžete místo vlastní domény nakonfigurovat plně kvalifikovaný název domény pro IP adresu kontroleru příchozího přenosu dat. Všimněte si, že tato ukázka je určena pro prostředí bash.
> 
> ```azurecli-interactive
> # Public IP address of your ingress controller
> IP="MY_EXTERNAL_IP"
> 
> # Name to associate with public IP address
> DNSNAME="demo-aks-ingress"
> 
> # Get the resource-id of the public ip
> PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)
> 
> # Update public ip address with DNS name
> az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
> 
> # Display the FQDN
> az network public-ip show --ids $PUBLICIPID --query "[dnsSettings.fqdn]" --output tsv
> ```

## <a name="install-cert-manager"></a>Instalace nástroje cert-manager

Kontroler příchozího přenosu dat NGINX podporuje ukončení protokolu TLS. Existuje několik způsobů, jak načíst a nakonfigurovat certifikáty pro protokol HTTPS. V tomto článku se dozvíte, jak používat [Správce certifikátů][cert-manager], který umožňuje automatické [šifrování][lets-encrypt] generování certifikátů a funkcí správy.

Instalace kontroleru správce certifikátů:

```console
# Label the ingress-basic namespace to disable resource validation
kubectl label namespace ingress-basic cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  cert-manager \
  --namespace ingress-basic \
  --version v0.16.1 \
  --set installCRDs=true \
  --set nodeSelector."beta\.kubernetes\.io/os"=linux \
  jetstack/cert-manager
```

Další informace o konfiguraci Správce certifikátů najdete v [projektu správce certifikátů][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Vytvoření vystavitele clusteru certifikační autority

Než bude možné certifikáty vystavit, správce certifikátů vyžaduje prostředek [vystavitele][cert-manager-issuer] nebo [ClusterIssuer][cert-manager-cluster-issuer] . Tyto Kubernetes prostředky jsou identické ve fungování, ale `Issuer` fungují v jednom oboru názvů a `ClusterIssuer` fungují napříč všemi obory názvů. Další informace najdete v dokumentaci [vystavitele správce certifikátů][cert-manager-issuer] .

Vytvořte Vystavitel clusteru, například `cluster-issuer.yaml` , pomocí následujícího ukázkového manifestu. Aktualizujte e-mailovou adresu platnou adresou z vaší organizace:

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: MY_EMAIL_ADDRESS
    privateKeySecretRef:
      name: letsencrypt
    solvers:
    - http01:
        ingress:
          class: nginx
          podTemplate:
            spec:
              nodeSelector:
                "kubernetes.io/os": linux
```

K vytvoření vystavitele použijte `kubectl apply` příkaz.

```console
kubectl apply -f cluster-issuer.yaml
```

## <a name="run-demo-applications"></a>Spustit ukázkové aplikace

Je nakonfigurovaný kontroler příchozího přenosu dat a řešení správy certifikátů. Teď spustíme dvě ukázkové aplikace v clusteru AKS. V tomto příkladu se Helm používá k nasazení dvou instancí jednoduché aplikace *Hello World* .

Pokud chcete zobrazit kontroler příchozího přenosu v akci, spusťte dvě ukázkové aplikace v clusteru AKS. V tomto příkladu můžete použít `kubectl apply` k nasazení dvou instancí jednoduché aplikace *Hello World* .

Vytvořte soubor *AKS-HelloWorld-One. yaml* a zkopírujte do něj následující příklad YAML:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld-one
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld-one
  template:
    metadata:
      labels:
        app: aks-helloworld-one
    spec:
      containers:
      - name: aks-helloworld-one
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
  name: aks-helloworld-one
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld-one
```

Vytvořte soubor *AKS-HelloWorld-dva soubory. yaml* a zkopírujte následující příklad YAML:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld-two
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld-two
  template:
    metadata:
      labels:
        app: aks-helloworld-two
    spec:
      containers:
      - name: aks-helloworld-two
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
  name: aks-helloworld-two
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld-two
```

Spusťte dvě ukázkové aplikace pomocí `kubectl apply` :

```console
kubectl apply -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl apply -f aks-helloworld-two.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Vytvoření trasy příchozího přenosu dat

Obě aplikace jsou teď spuštěné v clusteru Kubernetes. Jsou však konfigurovány se službou typu `ClusterIP` a nejsou přístupné z Internetu. Pokud je chcete zpřístupnit veřejnosti, vytvořte Kubernetes prostředek příchozího přenosu dat. Prostředek příchozího přenosu dat konfiguruje pravidla, která směrují provoz do jedné z těchto dvou aplikací.

V následujícím příkladu provoz na adresu *Hello-World – příchozí. MY_CUSTOM_DOMAIN* je směrován do služby *AKS-HelloWorld* . Provoz na adresu *Hello-World – příchozí. Služba MY_CUSTOM_DOMAIN/Hello-World-Two* je směrována do služby *AKS-HelloWorld-dvě* . Provoz do *Hello-World – příchozí. MY_CUSTOM_DOMAIN/static* je směrován do služby s názvem *AKS-HelloWorld* pro statické prostředky.

> [!NOTE]
> Pokud jste nakonfigurovali plně kvalifikovaný název domény pro IP adresu kontroleru příchozího přenosu dat místo vlastní domény, použijte místo *Hello-World-to plně kvalifikovaný název domény. MY_CUSTOM_DOMAIN*. Pokud je například plně kvalifikovaný název domény *demo-AKS-Ingress.eastus.cloudapp.Azure.com*, nahraďte *Hello-World-příchozí. MY_CUSTOM_DOMAIN* s *demo-AKS-Ingress.eastus.cloudapp.Azure.com* v `hello-world-ingress.yaml` .

`hello-world-ingress.yaml`Pomocí níže uvedeného příkladu YAML vytvořte soubor s názvem. Aktualizujte *hostitele* a *hostitele* na název DNS, který jste vytvořili v předchozím kroku.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$1
    nginx.ingress.kubernetes.io/use-regex: "true"
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - backend:
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /hello-world-one(/|$)(.*)
      - backend:
          serviceName: aks-helloworld-two
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
      - backend:
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /(.*)
---
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress-static
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /static/$2
    nginx.ingress.kubernetes.io/use-regex: "true"
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - backend:
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /static(/|$)(.*)
```

Pomocí příkazu vytvořte prostředek příchozího přenosu dat `kubectl apply` .

```console
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

## <a name="verify-a-certificate-object-has-been-created"></a>Ověření, že byl vytvořen objekt certifikátu

V dalším kroku se musí vytvořit prostředek certifikátu. Prostředek certifikátu definuje požadovaný certifikát X. 509. Další informace najdete v tématu [certifikáty pro správce][cert-manager-certificates]certifikátů. Správce certifikátů vytvořil pro vás automaticky objekt certifikátu pomocí překrytí příchozího přenosu dat, které se automaticky nasadí pomocí nástroje CERT Manager od verze v 0.2.2. Další informace najdete v dokumentaci ke [vstupnímu překrytí][ingress-shim].

Chcete-li ověřit, zda byl certifikát vytvořen úspěšně, použijte `kubectl get certificate --namespace ingress-basic` příkaz a ověřte, zda je hodnota *připravena* nastavena na *hodnotu true*, což může trvat několik minut.

```
$ kubectl get certificate --namespace ingress-basic

NAME         READY   SECRET       AGE
tls-secret   True    tls-secret   11m
```

## <a name="test-the-ingress-configuration"></a>Test konfigurace příchozího přenosu dat

Otevřete webový prohlížeč a *zahello World-to. MY_CUSTOM_DOMAIN* adaptéru Kubernetes pro příchozí přenos dat. Všimněte si, že přesměrujete na používání protokolu HTTPS a certifikát je důvěryhodný a ukázková aplikace se zobrazí ve webovém prohlížeči. Přidejte cestu */Hello-World-Two* a Všimněte si, že se zobrazí druhá ukázková aplikace s vlastním názvem.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tento článek používá Helm k instalaci komponent příchozího přenosu dat, certifikátů a ukázkových aplikací. Když nasadíte graf Helm, vytvoří se několik prostředků Kubernetes. Tyto prostředky zahrnují lusky, nasazení a služby. Chcete-li vyčistit tyto prostředky, můžete buď odstranit celý vzorový obor názvů, nebo jednotlivé prostředky.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Odstranění ukázkového oboru názvů a všech prostředků

Chcete-li odstranit celý vzorový obor názvů, použijte `kubectl delete` příkaz a zadejte název oboru názvů. Všechny prostředky v oboru názvů jsou odstraněny.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Odstranit prostředky jednotlivě

Další možností je podrobnější přístup k odstranění jednotlivých vytvořených prostředků. Nejdřív odeberte prostředky vystavitele clusteru:

```console
kubectl delete -f cluster-issuer.yaml --namespace ingress-basic
```

Seznam vydaných verzí Helm pomocí `helm list` příkazu. Vyhledejte grafy s názvem *Nginx* a *CERT-Manager*, jak je znázorněno v následujícím příkladu výstupu:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
cert-manager            ingress-basic   1               2020-01-15 10:23:36.515514 -0600 CST    deployed        cert-manager-v0.13.0    v0.13.0    
nginx                   ingress-basic   1               2020-01-15 10:09:45.982693 -0600 CST    deployed        nginx-ingress-1.29.1    0.27.0  
```

Odinstalujte verze pomocí `helm uninstall` příkazu. Následující příklad odinstaluje nasazení NGINX příchozího přenosu dat a správce certifikátů.

```
$ helm uninstall cert-manager nginx --namespace ingress-basic

release "cert-manager" uninstalled
release "nginx" uninstalled
```

Dále odeberte dvě ukázkové aplikace:

```console
kubectl delete -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl delete -f aks-helloworld-two.yaml --namespace ingress-basic
```

Odebrat trasu příchozího přenosu dat směrovaného do ukázkových aplikací:

```console
kubectl delete -f hello-world-ingress.yaml --namespace ingress-basic
```

Nakonec můžete odstranit samotný obor názvů. Použijte `kubectl delete` příkaz a zadejte název vašeho oboru názvů:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Další kroky

Tento článek obsahuje některé externí komponenty, které se AKS. Další informace o těchto komponentách naleznete na následujících stránkách projektu:

- [Helm CLI][helm-cli]
- [Kontroler NGINX pro příchozí přenosy][nginx-ingress]
- [cert-manager][cert-manager]

Můžete také:

- [Vytvoření základního kontroleru příchozího přenosu dat s připojením k externí síti][aks-ingress-basic]
- [Povolit doplněk směrování aplikace HTTP][aks-http-app-routing]
- [Vytvoření kontroleru příchozího přenosu dat, který používá interní privátní síť a IP adresu][aks-ingress-internal]
- [Vytvoření kontroleru příchozího přenosu dat, který používá vaše vlastní certifikáty TLS][aks-ingress-own-tls]
- [Vytvořte kontroler příchozího přenosu dat, který pomocí šifrovaného šifrování automaticky generuje certifikáty TLS se statickou veřejnou IP adresou.][aks-ingress-static-tls]

<!-- LINKS - external -->
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../dns/dns-getstarted-cli.md
[helm]: https://helm.sh/
[helm-cli]: ./kubernetes-helm.md
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
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
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
