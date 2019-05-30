---
title: Vytvoření HTTPS příchozí přenos dat pomocí clusteru Azure Kubernetes Service (AKS)
description: Zjistěte, jak nainstalovat a nakonfigurovat kontrolér příchozího přenosu dat serveru NGINX, která používá umožňuje šifrování pro automatické generování certifikátu TLS v clusteru služby Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: iainfou
ms.openlocfilehash: ad73b9d84a041f42cfdc3c7f5513bd0d32adf2a0
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66392183"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>Vytvoření řadiče HTTPS příchozího přenosu dat ve službě Azure Kubernetes Service (AKS)

Řadič služby příchozího přenosu dat je část softwaru, která poskytuje reverzní proxy server, směrování provozu konfigurovatelné a ukončení protokolu TLS pro služby Kubernetes. Prostředky Kubernetesu příchozího přenosu dat se používají ke konfiguraci pravidla příchozího přenosu dat a trasy pro jednotlivé služby Kubernetes. Použití kontroler příchozího přenosu dat a pravidla příchozího přenosu dat, jednu IP adresu je možné směrovat provoz do více služeb v clusteru Kubernetes.

V tomto článku se dozvíte, jak nasadit [kontroler příchozího přenosu dat NGINX] [ nginx-ingress] v clusteru služby Azure Kubernetes Service (AKS). [Cert správce] [ cert-manager] projektu slouží k automatickému generování a konfigurace [umožňuje šifrovat] [ lets-encrypt] certifikáty. Nakonec jsou dvě aplikace běží v clusteru AKS, z nichž každý je přístupný přes jednu IP adresu.

Můžete také:

- [Vytvoření základního příchozího přenosu dat řadiče s externí síťové připojení.][aks-ingress-basic]
- [Povolit doplněk směrování aplikace HTTP][aks-http-app-routing]
- [Vytvoření řadiče příchozího přenosu dat, který se používá soukromé, interní sítě a IP adresy][aks-ingress-internal]
- [Vytvoření řadiče příchozího přenosu dat, která používá vlastní certifikáty TLS][aks-ingress-own-tls]
- [Vytvoření řadiče příchozího přenosu dat, která používá umožňuje šifrovat automaticky vygenerovat certifikáty protokolu TLS se statickou veřejnou IP adresu][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte existující cluster AKS. Pokud potřebujete AKS cluster, najdete v tomto rychlém startu AKS [pomocí Azure CLI] [ aks-quickstart-cli] nebo [pomocí webu Azure portal][aks-quickstart-portal].

Tento článek používá Helm k instalaci serveru NGINX kontroler příchozího přenosu dat, správce certifikátů a ukázkovou webovou aplikaci. Musíte mít Helm inicializován v rámci clusteru AKS a pomocí účtu služby pro Tiller. Ujistěte se, že používáte nejnovější verzi nástroje Helm. Pokyny k upgradu, najdete v článku [Helm instalace dokumentace][helm-install]. Další informace o konfiguraci a použití Helm, naleznete v tématu [instalace aplikací s nástrojem Helm ve službě Azure Kubernetes Service (AKS)][use-helm].

Tento článek také vyžaduje, že používáte Azure CLI verze 2.0.64 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Vytvoření řadiče příchozího přenosu dat

Chcete-li vytvořit kontroler příchozího přenosu dat, použijte `Helm` instalace *nginx příchozího přenosu dat*. Pro přidání redundance dvě repliky řadiče příchozího přenosu dat NGINX nasazení se používají `--set controller.replicaCount` parametru. Pokud chcete naplno využívat s replikami kontroler příchozího přenosu dat, ujistěte se, že existuje více než jeden uzel v clusteru AKS.

Kontroler příchozího přenosu dat musí také naplánovat na uzlu systému Linux. Kontroler příchozího přenosu dat se nemůže spouštět uzly Windows serveru (aktuálně ve verzi preview ve službě AKS). Výběr uzlu je zadán pomocí `--set nodeSelector` parametr říct Kubernetes plánovač spustit kontroler příchozího přenosu dat NGINX na uzlech založených na Linuxu.

> [!TIP]
> Následující příklad vytvoří Kubernetes obor názvů pro prostředky příchozího přenosu dat s názvem *příchozího přenosu dat basic*. Podle potřeby zadejte obor názvů pro konkrétní prostředí. Pokud váš cluster AKS není povoleno RBAC, přidejte `--set rbac.create=false` k příkazům Helm.

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

Pro kontroler příchozího přenosu dat se vytvoří během instalace Azure veřejnou IP adresu. Tato veřejná IP adresa je statická dobu životnosti kontroleru příchozího přenosu. Pokud odstraníte kontroler příchozího přenosu dat, dojde ke ztrátě přiřazení veřejné IP adresy. Pokud potom vytvoříte řadič další příchozí přenos dat, se přiřadí novou veřejnou IP adresu. Pokud chcete zachovat použijte veřejnou IP adresu, můžete místo toho [vytvořit řadič příchozího přenosu dat se statickou veřejnou IP adresu][aks-ingress-static-tls].

Chcete-li získat veřejnou IP adresu, použijte `kubectl get service` příkazu. Trvá několik minut, než IP adresu, která přiřazené příslušné službě.

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                             TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
billowing-kitten-nginx-ingress-controller        LoadBalancer   10.0.182.160   51.145.155.210  80:30920/TCP,443:30426/TCP   20m
billowing-kitten-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

Žádná pravidla příchozího přenosu dat dosud nebyly vytvořeny. Pokud přejdete na veřejnou IP adresu, zobrazí se stránka 404 výchozí kontroler příchozího přenosu dat serveru NGINX.

## <a name="configure-a-dns-name"></a>Konfigurace názvu DNS

HTTPS certifikáty, které budou fungovat správně nakonfigurujte plně kvalifikovaný název domény pro IP adresu příchozího přenosu dat kontroleru. Tento skript aktualizace s IP adresou vašeho kontroler příchozího přenosu dat a jedinečný název, který chcete použít pro plně kvalifikovaný název:

```azurecli-interactive
#!/bin/bash

# Public IP address of your ingress controller
IP="51.145.155.210"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with DNS name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
```

Kontroler příchozího přenosu dat je teď dostupná přes plně kvalifikovaný název.

## <a name="install-cert-manager"></a>Instalace správce certifikátů

Kontroler příchozího přenosu dat NGINX podporuje ukončení protokolu TLS. Existuje několik způsobů, jak načíst a nakonfigurovat certifikáty pro protokol HTTPS. Tento článek ukazuje, jak pomocí [cert správce][cert-manager], která nabízí automatické [umožňuje šifrovat] [ lets-encrypt] generování certifikátů a funkce správy.

> [!NOTE]
> Tento článek používá `staging` prostředí umožňuje šifrovat. V nasazení v produkčním prostředí, použijte `letsencrypt-prod` a `https://acme-v02.api.letsencrypt.org/directory` v definicích prostředků a při instalaci grafu helmu.

Chcete-li nainstalovat řadič správce certifikátů v clusteru s podporou RBAC, použijte tuto `helm install` příkaz:

```console
# Install the CustomResourceDefinition resources separately
kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.8/deploy/manifests/00-crds.yaml

# Create the namespace for cert-manager
kubectl create namespace cert-manager

# Label the cert-manager namespace to disable resource validation
kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  --name cert-manager \
  --namespace cert-manager \
  --version v0.8.0 \
  jetstack/cert-manager
```

Další informace o konfiguraci správce certifikátů, najdete v článku [cert správce projektu][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Vytvoření clusteru vystavitele certifikační Autority

Před vydáním certifikátů, vyžaduje správce certifikátů [vystavitele] [ cert-manager-issuer] nebo [ClusterIssuer] [ cert-manager-cluster-issuer] prostředků. Tyto prostředky Kubernetesu je stejných ve funkci, ale `Issuer` funguje v jednoho oboru názvů, a `ClusterIssuer` funguje ve všech oborů názvů. Další informace najdete v tématu [vystavitele certifikátu správce] [ cert-manager-issuer] dokumentaci.

Vytvoření clusteru vystavitele, jako například `cluster-issuer.yaml`, pomocí následujícího příkladu manifest. Aktualizujte e-mailovou adresu platnou adresu z vaší organizace:

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
  namespace: ingress-basic
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    http01: {}
```

Chcete-li vytvořit vydavatele, použijte `kubectl apply -f cluster-issuer.yaml` příkazu.

```
$ kubectl apply -f cluster-issuer.yaml

clusterissuer.certmanager.k8s.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>Spuštění ukázkové aplikace

Byly nakonfigurovány kontroler příchozího přenosu dat a řešení pro správu certifikátů. Teď Pojďme spuštění dvě ukázkové aplikace ve vašem clusteru AKS. V tomto příkladu Helm umožňuje nasadit dvě instance aplikace jednoduchý "Hello world".

Před instalací ukázkové grafy Helm, přidejte úložiště ukázky v Azure následujícím způsobem pro vaše prostředí helmu:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Vytvoření první ukázkové aplikace z grafu helmu pomocí následujícího příkazu:

```console
helm install azure-samples/aks-helloworld --namespace ingress-basic
```

Nyní instalaci druhé instance ukázkové aplikace. Pro druhou instanci zadejte nový název tak, že dvě aplikace jsou vizuálně distinct. Můžete také zadat jedinečný název služby:

```console
helm install azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Vytvoření trasy příchozího přenosu dat

Obě aplikace běží v clusteru Kubernetes, ale jsou nakonfigurované s využitím služby typu `ClusterIP`. Aplikace v důsledku toho nejsou přístupné z Internetu. Aby se daly veřejně k dispozici, vytvořte prostředek příchozího přenosu dat Kubernetes. Prostředek příchozího přenosu dat nakonfiguruje pravidla, která směrovat provoz mezi těmito dvěma aplikacemi.

V následujícím příkladu, provoz na adresu `https://demo-aks-ingress.eastus.cloudapp.azure.com/` se směruje na službu s názvem `aks-helloworld`. Provoz na adresu `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` směrován `ingress-demo` služby. Aktualizace *hostitele* a *hostitele* jako název DNS, kterou jste vytvořili v předchozím kroku.

Vytvořte soubor s názvem `hello-world-ingress.yaml` a kopie v následujícím příkladu YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
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

Vytvoření prostředků pomocí příchozího přenosu dat `kubectl apply -f hello-world-ingress.yaml` příkazu.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>Vytvořte objekt certifikátu

V dalším kroku musí být vytvořen prostředek certifikátu. Prostředek certifikátu definuje požadovaný certifikát X.509. Další informace najdete v tématu [cert správce certifikátů][cert-manager-certificates].

Správce certifikátů pravděpodobně automaticky vytvořila objekt certifikátu pomocí příchozího přenosu dat překrytí, který je nasazen automaticky pomocí Správce certifikátů od v0.2.2. Další informace najdete v tématu [příchozího přenosu dat překrytí dokumentaci][ingress-shim].

Chcete-li ověřit, že certifikát byl úspěšně vytvořen, použijte `kubectl describe certificate tls-secret --namespace ingress-basic` příkazu.

Pokud certifikát vydala, zobrazí se výstup podobný následujícímu:
```
Type    Reason          Age   From          Message
----    ------          ----  ----          -------
  Normal  CreateOrder     11m   cert-manager  Created new ACME order, attempting validation...
  Normal  DomainVerified  10m   cert-manager  Domain "demo-aks-ingress.eastus.cloudapp.azure.com" verified with "http-01" validation
  Normal  IssueCert       10m   cert-manager  Issuing certificate...
  Normal  CertObtained    10m   cert-manager  Obtained certificate from ACME server
  Normal  CertIssued      10m   cert-manager  Certificate issued successfully
```

Pokud potřebujete k vytvoření prostředku další certifikáty, udělat s manifestem následující příklad. Aktualizace *dnsNames* a *domén* jako název DNS, kterou jste vytvořili v předchozím kroku. Pokud používáte řadič jen vnitřní příchozího přenosu dat, zadejte interní název DNS pro vaši službu.

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: tls-secret
  namespace: ingress-basic
spec:
  secretName: tls-secret-staging
  dnsNames:
  - demo-aks-ingress.eastus.cloudapp.azure.com
  acme:
    config:
    - http01:
        ingressClass: nginx
      domains:
      - demo-aks-ingress.eastus.cloudapp.azure.com
  issuerRef:
    name: letsencrypt-staging
    kind: ClusterIssuer
```

Chcete-li vytvořit prostředek certifikátu, použijte `kubectl apply -f certificates.yaml` příkazu.

```
$ kubectl apply -f certificates.yaml

certificate.certmanager.k8s.io/tls-secret-staging created
```

## <a name="test-the-ingress-configuration"></a>Otestujte konfiguraci příchozího přenosu dat

Otevřete webový prohlížeč na plně kvalifikovaný název domény řadiče příchozího přenosu dat Kubernetes, jako například *https://demo-aks-ingress.eastus.cloudapp.azure.com* .

Jak používat tyto příklady `letsencrypt-staging`, vystavený certifikát SSL není důvěryhodný v prohlížeči. Potvrďte výzvu s upozorněním a pokračujte v aplikaci. Informace o certifikátu se zobrazí *phishing LE zprostředkující X1* certifikát vystavil umožňuje šifrovat. Určuje tento falešný certifikát `cert-manager` požadavek zpracoval správně a přijetí certifikátu od poskytovatele:

![Umožňuje šifrovat pracovní certifikátu](media/ingress/staging-certificate.png)

Pokud změníte, můžeme použít šifrování `prod` spíše než `staging`, důvěryhodný certifikát vydaný umožňuje šifrování se používá, jak je znázorněno v následujícím příkladu:

![Pojďme certifikátu šifrování](media/ingress/certificate.png)

Ukázkové aplikace se zobrazí ve webovém prohlížeči:

![Aplikace – příklad jednoho](media/ingress/app-one.png)

Nyní přidejte */hello-world-two* cesty na plně kvalifikovaný název domény, jako například *https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two* . Druhý ukázkové aplikace s vlastní název se zobrazí:

![Aplikace – příklad 2](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tento článek používá Helm k instalaci součásti příchozího přenosu dat, certifikáty a ukázkové aplikace. Při nasazování grafu helmu vytvoří řada prostředky Kubernetesu. Tyto prostředky zahrnují podů, nasazení a služby. K uvolnění těchto prostředků, můžete buď odstranit obor názvů celé ukázkové nebo jednotlivé prostředky.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Odstranit obor názvů vzorku a všechny prostředky

Chcete-li odstranit obor názvů celého vzorku, použijte `kubectl delete` příkaz a zadejte název vašeho oboru názvů. Se odstraní všechny prostředky v oboru názvů.

```console
kubectl delete namespace ingress-basic
```

Potom odeberte Helm úložiště pro aplikace hello world AKS:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Odstranit prostředky jednotlivě

Podrobnější přístupem je můžete také odstranit jednotlivé prostředky vytvoří. Nejdřív odeberte certifikát prostředky:

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Nyní seznamu Helm vydané verze s `helm list` příkazu. Vyhledejte grafy s názvem *nginx příchozího přenosu dat*, *cert správce*, a *aks-helloworld*, jak je znázorněno v následujícím příkladu výstupu:

```
$ helm list

NAME                    REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
billowing-kitten        1           Wed Mar  6 19:37:43 2019    DEPLOYED    nginx-ingress-1.3.1     0.22.0      kube-system
loitering-waterbuffalo  1           Wed Mar  6 20:25:01 2019    DEPLOYED    cert-manager-v0.6.6     v0.6.2      kube-system
flabby-deer             1           Wed Mar  6 20:27:54 2019    DEPLOYED    aks-helloworld-0.1.0                default
linting-echidna         1           Wed Mar  6 20:27:59 2019    DEPLOYED    aks-helloworld-0.1.0                default
```

Odstranit vydané verze s `helm delete` příkazu. Následující příklad odstraní nasazení serveru NGINX příchozího přenosu dat, správce certifikátů a dvě ukázkové AKS hello world aplikace.

```
$ helm delete billowing-kitten loitering-waterbuffalo flabby-deer linting-echidna

release "billowing-kitten" deleted
release "loitering-waterbuffalo" deleted
release "flabby-deer" deleted
release "linting-echidna" deleted
```

V dalším kroku odeberte Helm úložiště pro aplikace hello world AKS:

```console
helm repo remove azure-samples
```

Odstraní obor názvů. Použití `kubectl delete` příkaz a zadejte název vašeho oboru názvů:

```console
kubectl delete namespace ingress-basic
```

Nakonec odeberte trasu příchozího přenosu dat, která přesměruje přenosy do ukázkové aplikace:

```console
kubectl delete -f hello-world-ingress.yaml
```

## <a name="next-steps"></a>Další postup

Tento článek zahrnuty některé externí součásti pro AKS. Další informace o těchto komponent, naleznete na následujících stránkách projektu:

- [Helm CLI][helm-cli]
- [Kontroler příchozího přenosu dat serveru NGINX][nginx-ingress]
- [cert-manager][cert-manager]

Můžete také:

- [Vytvoření základního příchozího přenosu dat řadiče s externí síťové připojení.][aks-ingress-basic]
- [Povolit doplněk směrování aplikace HTTP][aks-http-app-routing]
- [Vytvoření řadiče příchozího přenosu dat, který se používá soukromé, interní sítě a IP adresy][aks-ingress-internal]
- [Vytvoření řadiče příchozího přenosu dat, která používá vlastní certifikáty TLS][aks-ingress-own-tls]
- [Vytvoření řadiče příchozího přenosu dat, která používá umožňuje šifrovat automaticky vygenerovat certifikáty protokolu TLS se statickou veřejnou IP adresu][aks-ingress-static-tls]

<!-- LINKS - external -->
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
[install-azure-cli]: /cli/azure/install-azure-cli
