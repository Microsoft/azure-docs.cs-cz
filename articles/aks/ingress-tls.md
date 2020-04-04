---
title: Vytvoření příchozího přenosu dat https pomocí clusteru Služby Azure Kubernetes (AKS)
description: Zjistěte, jak nainstalovat a nakonfigurovat řadič příchozího přenosu dat NGINX, který používá Let's Encrypt pro automatické generování certifikátů TLS v clusteru Služby Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 01/29/2020
ms.openlocfilehash: ece117d60ae3d7fd70c0972bb463340c5d38c9e1
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637263"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>Vytvoření řadiče příchozího přenosu dat HTTPS ve službě Azure Kubernetes Service (AKS)

Řadič příchozího přenosu dat je software, který poskytuje reverzní proxy server, konfigurovatelné směrování provozu a ukončení TLS pro služby Kubernetes. Prostředky příchozího přenosu dat kubernetes se používají ke konfiguraci pravidel příchozího přenosu dat a tras pro jednotlivé služby Kubernetes. Pomocí řadiče příchozího přenosu dat a pravidel příchozího přenosu dat lze jednu adresu IP použít k směrování provozu do více služeb v clusteru Kubernetes.

Tento článek ukazuje, jak nasadit [řadič příchozího přenosu dat NGINX][nginx-ingress] v clusteru služby Azure Kubernetes Service (AKS). Projekt [správce certifikátů][cert-manager] se používá k automatickému generování a konfiguraci certifikátů [Let's Encrypt.][lets-encrypt] Nakonec jsou v clusteru AKS spuštěny dvě aplikace, z nichž každá je přístupná přes jednu adresu IP.

Můžete také:

- [Vytvoření základního řadiče příchozího přenosu dat s externím připojením k síti][aks-ingress-basic]
- [Povolení doplňku směrování aplikací HTTP][aks-http-app-routing]
- [Vytvoření řadiče příchozího přenosu dat, který používá interní, privátní síť a adresu IP][aks-ingress-internal]
- [Vytvoření řadiče příchozího přenosu dat, který používá vlastní certifikáty TLS][aks-ingress-own-tls]
- [Vytvoření řadiče příchozího přenosu dat, který používá let's Encrypt k automatickému generování certifikátů TLS se statickou veřejnou IP adresou][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, podívejte se na aks rychlý start [pomocí Azure CLI][aks-quickstart-cli] nebo [pomocí portálu Azure][aks-quickstart-portal].

Tento článek také předpokládá, že máte [vlastní doménu][custom-domain] se [zónou DNS][dns-zone] ve stejné skupině prostředků jako cluster AKS.

Tento článek používá Helm k instalaci řadiče příchozího přenosu dat NGINX, správce certifikátů a ukázkové webové aplikace. Ujistěte se, že používáte nejnovější verzi Helmu. Pokyny k upgradu naleznete v [dokumentech k instalaci helmy][helm-install]. Další informace o konfiguraci a používání helmu najdete v [tématu Instalace aplikací pomocí helmu ve službě Azure Kubernetes Service (AKS).][use-helm]

Tento článek také vyžaduje, abyste spouštěli Azure CLI verze 2.0.64 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Vytvoření řadiče příchozího přenosu dat

Chcete-li vytvořit řadič příchozího přenosu dat, použijte `helm` příkaz k instalaci *nginx-ingress*. Pro přidání redundance se nasadí dvě repliky kontrolerů příchozího přenosu dat NGINX s parametrem `--set controller.replicaCount`. Chcete-li plně využít spuštění replik řadiče příchozího přenosu dat, ujistěte se, že je více než jeden uzel v clusteru AKS.

Kontroler příchozího přenosu dat je potřeba naplánovat také v uzlu Linuxu. Uzly systému Windows Server (aktuálně ve verzi preview v AKS) by neměly spouštět řadič příchozího přenosu dat. Selektor uzlů se specifikuje pomocí parametru `--set nodeSelector`, aby plánovači Kubernetes oznámil, že má spustit kontroler příchozího přenosu dat NGINX v uzlu Linuxu.

> [!TIP]
> Následující příklad vytvoří obor názvů Kubernetes pro prostředky příchozího přenosu dat s názvem *ingress-basic*. Podle potřeby zadejte obor názvů pro vlastní prostředí.

> [!TIP]
> Pokud chcete povolit [uchování IP zdrojů klienta][client-source-ip] pro požadavky `--set controller.service.externalTrafficPolicy=Local` na kontejnery v clusteru, přidejte do příkazu Helm install. Zdrojová ADRESA klienta je uložena v hlavičce požadavku v části *X-Forwarded-For*. Při použití řadiče příchozího přenosu dat s povoleným uchováním IP zdroje klienta nebude předávací přenos SSL fungovat.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the official stable repo
helm repo add stable https://kubernetes-charts.storage.googleapis.com/

# Use Helm to deploy an NGINX ingress controller
helm install nginx stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Během instalace se vytvoří veřejná IP adresa Azure pro řadič příchozího přenosu dat. Tato veřejná IP adresa je statická pro životnost řadiče příchozího přenosu dat. Pokud odstraníte řadič příchozího přenosu dat, dojde ke ztrátě přiřazení veřejné IP adresy. Pokud pak vytvoříte další řadič příchozího přenosu dat, bude přiřazena nová veřejná IP adresa. Pokud chcete zachovat použití veřejné IP adresy, můžete místo toho [vytvořit řadič příchozího přenosu dat se statickou veřejnou IP adresou][aks-ingress-static-tls].

Chcete-li získat veřejnou `kubectl get service` IP adresu, použijte příkaz. Trvá několik minut, než bude ip adresa přiřazena ke službě.

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                             TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
nginx-ingress-controller                         LoadBalancer   10.0.182.160   MY_EXTERNAL_IP  80:30920/TCP,443:30426/TCP   20m
nginx-ingress-default-backend                    ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

Dosud nebyla vytvořena žádná pravidla příchozího přenosu dat. Pokud přejdete na veřejnou IP adresu, zobrazí se výchozí stránka 404 řadiče příchozího přenosu dat NGINX.

## <a name="add-an-a-record-to-your-dns-zone"></a>Přidání záznamu A do zóny DNS

Přidejte záznam *A* do zóny DNS s externí IP adresou služby NGINX pomocí [sady záznamů DNS az network .][az-network-dns-record-set-a-add-record]

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name '*' \
    --ipv4-address MY_EXTERNAL_IP
```

> [!NOTE]
> Volitelně můžete nakonfigurovat hlavní název domény pro ip adresu příchozího řadiče namísto vlastní domény. Všimněte si, že tato ukázka je pro shell Bash.
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

Kontroler příchozího přenosu dat NGINX podporuje ukončení protokolu TLS. Existuje několik způsobů, jak načíst a nakonfigurovat certifikáty pro protokol HTTPS. Tento článek ukazuje použití [správce certifikátu][cert-manager], který poskytuje automatické [umožňuje šifrovat][lets-encrypt] generování certifikátů a funkce správy.

Instalace řadiče správce certifikátu:

```console
# Install the CustomResourceDefinition resources separately
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.13/deploy/manifests/00-crds.yaml

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
  --version v0.13.0 \
  jetstack/cert-manager
```

Další informace o konfiguraci nástroje cert-manager naleznete v [projektu správce certifikátů][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Vytvoření vystavittele clusteru certifikační autority

Před vydáním certifikátů vyžaduje správce certifikátů prostředek [vystavitra][cert-manager-issuer] nebo [clusterissueru.][cert-manager-cluster-issuer] Tyto kubernetes prostředky jsou identické `Issuer` funkce, ale funguje v `ClusterIssuer` jednom oboru názvů a funguje ve všech oborech názvů. Další informace naleznete v dokumentaci [vydavatele správce certifikátů.][cert-manager-issuer]

Vytvořte vystavitele `cluster-issuer.yaml`clusteru, například , pomocí následujícího příkladu manifestu. Aktualizace e-mailové adresy platnou adresou z vaší organizace:

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
```

Chcete-li vytvořit vystavitel, použijte `kubectl apply` příkaz.

```console
kubectl apply -f cluster-issuer.yaml
```

## <a name="run-demo-applications"></a>Spuštění ukázkových aplikací

Byl nakonfigurován řadič příchozího přenosu dat a řešení správy certifikátů. Nyní spustíme dvě ukázkové aplikace v clusteru AKS. V tomto příkladu Helm se používá k nasazení dvou instancí jednoduché aplikace *Hello svět.*

Před instalací ukázkových grafů Helm přidejte úložiště ukázek Azure do prostředí Helm.

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Vytvořte demo aplikaci s názvem *aks-helloworld* pomocí *grafu azure-samples/aks-helloworld* Helm.

```console
helm install aks-helloworld azure-samples/aks-helloworld --namespace ingress-basic
```

Vytvořte druhou instanci ukázkové aplikace s názvem *aks-helloworld-two*. Zadejte nový název a jedinečný název služby tak, aby obě aplikace byly vizuálně odlišné pomocí *--set*.

```console
helm install aks-helloworld-two azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="aks-helloworld-two"
```

## <a name="create-an-ingress-route"></a>Vytvoření postupu příchozího přenosu dat

Obě aplikace jsou nyní spuštěny v clusteru Kubernetes. Jsou však konfigurovány se `ClusterIP` službou typu a nejsou přístupné z internetu. Chcete-li je zpřístupnit veřejně, vytvořte prostředek příchozího přenosu dat Kubernetes. Prostředek příchozího přenosu dat konfiguruje pravidla, která směrují provoz do jedné ze dvou aplikací.

V následujícím příkladu provoz na adresu *hello-world-ingress. MY_CUSTOM_DOMAIN* je směrována na službu *AKS-Helloworld.* Provoz na adresu *hello-world-ingress. MY_CUSTOM_DOMAIN/hello-world-two* je směrována na službu *aks-helloworld-two.* Provoz na *hello-world-ingress. MY_CUSTOM_DOMAIN/static* je směrována do služby s názvem *aks-helloworld* pro statické prostředky.

Vytvořte soubor `hello-world-ingress.yaml` pojmenovaný podle příkladu YAML. Aktualizujte *hostitele* a *hostitele* na název DNS, který jste vytvořili v předchozím kroku.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$2
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
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: aks-helloworld-two
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress-static
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /static/$2
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
          serviceName: aks-helloworld
          servicePort: 80
        path: /static(/|$)(.*)
```

Vytvořte prostředek příchozího `kubectl apply` přenosu dat pomocí příkazu.

```console
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

## <a name="verify-a-certificate-object-has-been-created"></a>Ověření vytvoření objektu certifikátu

Dále musí být vytvořen prostředek certifikátu. Prostředek certifikátu definuje požadovaný certifikát X.509. Další informace naleznete v [tématu certifikáty správce certifikátů][cert-manager-certificates]. Správce certifikátů pro vás automaticky vytvořil objekt certifikátu pomocí funkce Příchozí přemísťování dat, který je automaticky nasazen pomocí správce certifikátů od v0.2.2. Další informace naleznete [v dokumentaci ingress-shim][ingress-shim].

Chcete-li ověřit, zda byl certifikát `kubectl get certificate --namespace ingress-basic` úspěšně vytvořen, použijte příkaz a ověřte, zda je *hodnota READY* *true*, což může trvat několik minut.

```
$ kubectl get certificate --namespace ingress-basic

NAME         READY   SECRET       AGE
tls-secret   True    tls-secret   11m
```

## <a name="test-the-ingress-configuration"></a>Otestovat konfiguraci příchozího přenosu dat

Otevřete webový prohlížeč pro *příchozí hod hello-world. MY_CUSTOM_DOMAIN* řadiče příchozího přenosu dat Kubernetes. Všimněte si, že jste přesměrování používat HTTPS a certifikát je důvěryhodný a ukázková aplikace se zobrazí ve webovém prohlížeči. Přidejte cestu */hello-world-two* a všimněte si, že se zobrazí druhá demo aplikace s vlastním názvem.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tento článek používá Helm k instalaci příchozích součástí, certifikátů a ukázkových aplikací. Při nasazení grafu Helm se vytvoří několik prostředků Kubernetes. Tyto prostředky zahrnují pody, nasazení a služby. Chcete-li vyčistit tyto prostředky, můžete odstranit celý ukázkový obor názvů nebo jednotlivé prostředky.

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

Alternativně podrobnější přístup je odstranit jednotlivé vytvořené prostředky. Nejprve odeberte prostředky vystavittele clusteru:

```console
kubectl delete -f cluster-issuer.yaml --namespace ingress-basic
```

Seznam vydání helmu `helm list` pomocí příkazu. Podívejte se na grafy s názvem *nginx-ingress* a *aks-helloworld*, jak je znázorněno v následujícím příkladu výstupu:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
aks-helloworld          ingress-basic   1               2020-01-15 10:24:32.054871 -0600 CST    deployed        aks-helloworld-0.1.0               
aks-helloworld-two      ingress-basic   1               2020-01-15 10:24:37.671667 -0600 CST    deployed        aks-helloworld-0.1.0               
cert-manager            ingress-basic   1               2020-01-15 10:23:36.515514 -0600 CST    deployed        cert-manager-v0.13.0    v0.13.0    
nginx                   ingress-basic   1               2020-01-15 10:09:45.982693 -0600 CST    deployed        nginx-ingress-1.29.1    0.27.0  
```

Odstraňte verze pomocí `helm delete` příkazu. Následující příklad odstraní nasazení příchozího přenosu dat NGINX a dvě ukázkové aplikace AKS hello world.

```
$ helm uninstall aks-helloworld aks-helloworld-two cert-manager nginx --namespace ingress-basic

release "aks-helloworld" uninstalled
release "aks-helloworld-two" uninstalled
release "cert-manager" uninstalled
release "nginx" uninstalled
```

Dále odeberte repo helmu pro aplikaci AKS hello world:

```console
helm repo remove azure-samples
```

Odeberte trasu příchozího přenosu dat, která směrovala provoz do ukázkových aplikací:

```console
kubectl delete -f hello-world-ingress.yaml --namespace ingress-basic
```

Nakonec můžete odstranit samotný obor názvů. Použijte `kubectl delete` příkaz a zadejte název oboru názvů:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Další kroky

Tento článek zahrnoval některé externí součásti AKS. Další informace o těchto součástech naleznete na následujících stránkách projektu:

- [Helm CLI][helm-cli]
- [Řadič příchozího přenosu dat NGINX][nginx-ingress]
- [správce certifikátů][cert-manager]

Můžete také:

- [Vytvoření základního řadiče příchozího přenosu dat s externím připojením k síti][aks-ingress-basic]
- [Povolení doplňku směrování aplikací HTTP][aks-http-app-routing]
- [Vytvoření řadiče příchozího přenosu dat, který používá interní, privátní síť a adresu IP][aks-ingress-internal]
- [Vytvoření řadiče příchozího přenosu dat, který používá vlastní certifikáty TLS][aks-ingress-own-tls]
- [Vytvoření řadiče příchozího přenosu dat, který používá let's Encrypt k automatickému generování certifikátů TLS se statickou veřejnou IP adresou][aks-ingress-static-tls]

<!-- LINKS - external -->
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../dns/dns-getstarted-cli.md
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
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
