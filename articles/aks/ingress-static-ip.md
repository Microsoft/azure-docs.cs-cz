---
title: Vytvoření kontroleru HTTP příchozího přenosu se statickou IP adresou ve službě Azure Kubernetes Service (AKS)
description: Naučte se, jak nainstalovat a nakonfigurovat řadič příchozího přenosu NGINX se statickou veřejnou IP adresou v clusteru Azure Kubernetes Service (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: mlearned
ms.openlocfilehash: 7e390ed1151c45ca9a325b1795a8fbcad74cdfdb
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74194729"
---
# <a name="create-an-ingress-controller-with-a-static-public-ip-address-in-azure-kubernetes-service-aks"></a>Vytvoření kontroleru příchozího přenosu dat se statickou veřejnou IP adresou ve službě Azure Kubernetes Service (AKS)

Kontroler příchozího přenosu dat je softwarový software, který poskytuje reverzní proxy, konfigurovatelné směrování provozu a ukončení protokolu TLS pro služby Kubernetes Services. Kubernetes příchozí prostředky se používají ke konfiguraci pravidel příchozího přenosu dat a tras pro jednotlivé služby Kubernetes. Pomocí řadiče příchozího přenosu dat a pravidel příchozího přenosu dat se dá jedna IP adresa používat ke směrování provozu do několika služeb v clusteru Kubernetes.

V tomto článku se dozvíte, jak nasadit [řadič Nginx][nginx-ingress] příchozího přenosu do clusteru Azure Kubernetes Service (AKS). Kontroler příchozího přenosu dat je nakonfigurovaný se statickou veřejnou IP adresou. Projekt [Správce certifikátů][cert-manager] se používá k automatickému generování a konfiguraci certifikátů, které [umožňují šifrování][lets-encrypt] . Nakonec se dvě aplikace spouští v clusteru AKS, z nichž každá je přístupná přes jednu IP adresu.

Můžete také:

- [Vytvoření základního kontroleru příchozího přenosu dat s připojením k externí síti][aks-ingress-basic]
- [Povolit doplněk směrování aplikace HTTP][aks-http-app-routing]
- [Vytvoření kontroleru příchozího přenosu dat, který používá vaše vlastní certifikáty TLS][aks-ingress-own-tls]
- [Vytvořte kontroler příchozího přenosu dat, který pomocí šifry umožňuje automaticky generovat certifikáty TLS s dynamickou veřejnou IP adresou.][aks-ingress-tls]

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, přečtěte si rychlý Start AKS a [použijte Azure CLI][aks-quickstart-cli] nebo [Azure Portal][aks-quickstart-portal].

Tento článek používá Helm k instalaci řadiče NGINX příchozího přenosu dat, správce certifikátů a ukázkovou webovou aplikaci. Musíte mít Helm inicializovaný v rámci vašeho clusteru AKS a používat účet služby pro pokladnu. Ujistěte se, že používáte nejnovější verzi Helm. Pokyny k upgradu najdete v [dokumentaci k instalaci Helm][helm-install]. Další informace o konfiguraci a použití Helm najdete v tématu [install Applications with Helm in Azure Kubernetes Service (AKS)][use-helm].

Tento článek také vyžaduje, abyste spustili Azure CLI verze 2.0.64 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Vytvoření kontroleru příchozího přenosu dat

Ve výchozím nastavení se kontroler NGINX příchozího přenosu dat vytvoří pomocí nového přiřazení veřejné IP adresy. Tato veřejná IP adresa je statická jenom pro životní cyklus řadiče příchozího přenosu dat a při odstranění a opětovném vytvoření kontroleru se ztratí. Běžným požadavkem na konfiguraci je poskytnout řadiči příchozí sítě NGINX stávající statickou veřejnou IP adresu. Pokud se kontroler příchozího přenosu odstraní, statická veřejná IP adresa zůstane. Tento přístup vám umožní v rámci životního cyklu vašich aplikací používat existující záznamy DNS a konfigurace sítě konzistentním způsobem.

Pokud potřebujete vytvořit statickou veřejnou IP adresu, nejprve pomocí příkazu [AZ AKS show][az-aks-show] načtěte název skupiny prostředků clusteru AKS.

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

V dalším kroku vytvořte veřejnou IP adresu s metodou *statického* přidělení pomocí příkazu [AZ Network Public-IP Create][az-network-public-ip-create] . Následující příklad vytvoří veřejnou IP adresu s názvem *myAKSPublicIP* ve skupině prostředků clusteru AKS, kterou jste získali v předchozím kroku:

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --sku Standard --allocation-method static --query publicIp.ipAddress -o tsv
```

Teď nasaďte *Nginx a vstupní* graf s Helm. Přidejte parametr `--set controller.service.loadBalancerIP` a zadejte vlastní veřejnou IP adresu vytvořenou v předchozím kroku. Pro přidání redundance se nasadí dvě repliky řadičů příchozího přenosu NGINX s parametrem `--set controller.replicaCount`. Pokud chcete mít v clusteru AKS k dispozici více než jeden uzel, zajistěte, aby bylo možné plně využít více uzlů.

Řadič příchozího přenosu dat musí být také naplánován na uzel Linux. Uzly Windows serveru (v současné době ve verzi Preview v AKS) by neměli spustit kontroler příchozího přenosu dat. Selektor uzlů je určený pomocí parametru `--set nodeSelector`, který umožňuje, aby Plánovač Kubernetes spouštěl NGINX řadič příchozího přenosu v uzlu založeném na systému Linux.

> [!TIP]
> Následující příklad vytvoří obor názvů Kubernetes pro prostředky příchozího přenosu dat s názvem příchozí *– Basic*. Podle potřeby zadejte obor názvů pro vlastní prostředí. Pokud váš cluster AKS není RBAC povolený, přidejte `--set rbac.create=false` do příkazů Helm.

> [!TIP]
> Pokud chcete povolit [zachování IP adresy zdrojového klienta][client-source-ip] pro požadavky na kontejnery v clusteru, přidejte `--set controller.service.externalTrafficPolicy=Local` do příkazu pro instalaci Helm. Zdrojová IP adresa klienta je uložená v hlavičce žádosti v části *předané X-pro*. Při použití kontroleru příchozího přenosu dat s povoleným zachováním IP adresy klienta nebude předávat SSL fungovat.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.service.loadBalancerIP="40.121.63.72"
```

Když se pro kontroler příchozího přenosu NGINX vytvoří služba Vyrovnávání zatížení Kubernetes, vaše statická IP adresa se přiřadí, jak je znázorněno v následujícím příkladu výstupu:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                        TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
dinky-panda-nginx-ingress-controller        LoadBalancer   10.0.232.56   40.121.63.72   80:31978/TCP,443:32037/TCP   3m
dinky-panda-nginx-ingress-default-backend   ClusterIP      10.0.95.248   <none>         80/TCP                       3m
```

Zatím se nevytvořila žádná pravidla příchozího přenosu dat, takže pokud přejdete na veřejnou IP adresu, zobrazí se 404 výchozí stránka NGINX adaptéru pro příjem dat. Pravidla příchozího přenosu dat jsou nakonfigurovaná v následujících krocích.

## <a name="configure-a-dns-name"></a>Konfigurace názvu DNS

Aby certifikáty HTTPS správně fungovaly, nakonfigurujte plně kvalifikovaný název domény pro IP adresu kontroleru příchozího přenosu dat. Aktualizujte následující skript s použitím IP adresy vašeho kontroleru příchozího přenosu dat a jedinečného názvu, který byste chtěli použít pro plně kvalifikovaný název domény:

```azurecli-interactive
#!/bin/bash

# Public IP address of your ingress controller
IP="40.121.63.72"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with DNS name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
```

Kontroler příchozího přenosu dat je teď přístupný prostřednictvím plně kvalifikovaného názvu domény.

## <a name="install-cert-manager"></a>Instalace správce certifikátů

Kontroler příchozího přenosu dat NGINX podporuje ukončení protokolu TLS. Existuje několik způsobů, jak načíst a nakonfigurovat certifikáty pro protokol HTTPS. V tomto článku se dozvíte, jak používat [Správce certifikátů][cert-manager], který umožňuje automatické [šifrování][lets-encrypt] generování certifikátů a funkcí správy.

> [!NOTE]
> V tomto článku se používá `staging` prostředí pro šifrování. V produkčních nasazeních použijte `letsencrypt-prod` a `https://acme-v02.api.letsencrypt.org/directory` v definicích prostředků a při instalaci grafu Helm.

Pokud chcete nainstalovat kontrolér správce certifikátů v clusteru s povolenou RBAC, použijte následující příkaz `helm install`:

```console
# Install the CustomResourceDefinition resources separately
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.11/deploy/manifests/00-crds.yaml

# Create the namespace for cert-manager
kubectl create namespace cert-manager

# Label the cert-manager namespace to disable resource validation
kubectl label namespace cert-manager cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  --name cert-manager \
  --namespace cert-manager \
  --version v0.11.0 \
  jetstack/cert-manager
```

Další informace o konfiguraci Správce certifikátů najdete v [projektu správce certifikátů][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Vytvoření vystavitele clusteru certifikační autority

Než bude možné certifikáty vystavit, správce certifikátů vyžaduje prostředek [vystavitele][cert-manager-issuer] nebo [ClusterIssuer][cert-manager-cluster-issuer] . Tyto Kubernetes prostředky jsou identické ve funkcích, ale `Issuer` fungují v jednom oboru názvů a `ClusterIssuer` fungují napříč všemi obory názvů. Další informace najdete v dokumentaci [vystavitele správce certifikátů][cert-manager-issuer] .

Vytvořte Vystavitel clusteru, například `cluster-issuer.yaml`, pomocí následujícího ukázkového manifestu. Aktualizujte e-mailovou adresu platnou adresou z vaší organizace:

```yaml
apiVersion: cert-manager.io/v1alpha2
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

K vytvoření vystavitele použijte příkaz `kubectl apply -f cluster-issuer.yaml`.

```
$ kubectl apply -f cluster-issuer.yaml

clusterissuer.cert-manager.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>Spustit ukázkové aplikace

Je nakonfigurovaný kontroler příchozího přenosu dat a řešení správy certifikátů. Teď spustíme dvě ukázkové aplikace v clusteru AKS. V tomto příkladu se Helm používá k nasazení dvou instancí jednoduché aplikace Hello World.

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

Obě aplikace jsou teď spuštěné v clusteru Kubernetes, ale nakonfigurují se se službou typu `ClusterIP`. Aplikace proto nejsou přístupné z Internetu. Pokud je chcete zpřístupnit veřejnosti, vytvořte Kubernetes prostředek příchozího přenosu dat. Prostředek příchozího přenosu dat konfiguruje pravidla, která směrují provoz do jedné z těchto dvou aplikací.

V následujícím příkladu je provoz na adresu `https://demo-aks-ingress.eastus.cloudapp.azure.com/` směrován do služby s názvem `aks-helloworld`. Provoz na adresu `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` je směrován do služby `ingress-demo`. Aktualizujte *hostitele* a *hostitele* na název DNS, který jste vytvořili v předchozím kroku.

Vytvořte soubor s názvem `hello-world-ingress.yaml` a zkopírujte následující příklad YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    cert-manager.io/cluster-issuer: letsencrypt-staging
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

Pomocí příkazu `kubectl apply -f hello-world-ingress.yaml` vytvořte prostředek příchozího přenosu dat.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>Vytvoření objektu certifikátu

V dalším kroku se musí vytvořit prostředek certifikátu. Prostředek certifikátu definuje požadovaný certifikát X. 509. Další informace najdete v tématu [certifikáty pro správce][cert-manager-certificates]certifikátů.

Správce certifikátů pravděpodobně pro vás automaticky vytvořil objekt certifikátu pomocí příchozího překrytí, které se automaticky nasazuje pomocí nástroje CERT Manager od verze v 0.2.2. Další informace najdete v dokumentaci ke [vstupnímu překrytí][ingress-shim].

Chcete-li ověřit, zda byl certifikát vytvořen úspěšně, použijte příkaz `kubectl describe certificate tls-secret --namespace ingress-basic`.

Pokud byl certifikát vystavený, zobrazí se výstup podobný následujícímu:
```
Type    Reason          Age   From          Message
----    ------          ----  ----          -------
  Normal  CreateOrder     11m   cert-manager  Created new ACME order, attempting validation...
  Normal  DomainVerified  10m   cert-manager  Domain "demo-aks-ingress.eastus.cloudapp.azure.com" verified with "http-01" validation
  Normal  IssueCert       10m   cert-manager  Issuing certificate...
  Normal  CertObtained    10m   cert-manager  Obtained certificate from ACME server
  Normal  CertIssued      10m   cert-manager  Certificate issued successfully
```

Pokud potřebujete vytvořit další prostředek certifikátu, můžete tak učinit pomocí následujícího ukázkového manifestu. Aktualizujte *dnsNames* a *domény* na název DNS, který jste vytvořili v předchozím kroku. Pokud používáte jenom interní kontroler příchozího přenosu dat, zadejte interní název DNS pro vaši službu.

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: Certificate
metadata:
  name: tls-secret
  namespace: ingress-basic
spec:
  secretName: tls-secret
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

K vytvoření prostředku certifikátu použijte příkaz `kubectl apply -f certificates.yaml`.

```
$ kubectl apply -f certificates.yaml

certificate.cert-manager.io/tls-secret created
```

## <a name="test-the-ingress-configuration"></a>Test konfigurace příchozího přenosu dat

Otevřete webový prohlížeč na plně kvalifikovaný název domény řadiče Kubernetes příchozího přenosu dat, například *https://demo-aks-ingress.eastus.cloudapp.azure.com* .

Jak tyto příklady používají `letsencrypt-staging`, vydaný certifikát SSL není důvěryhodný pro prohlížeč. Přijměte výzvu k zadání upozornění, abyste mohli pokračovat v používání aplikace. Informace o certifikátu zobrazí tento *falešný certifikát x1 pro Intermediate* , který je vydaný pomocí zašifrování. Tento falešný certifikát indikuje, `cert-manager` zpracoval požadavek správně a obdržel certifikát od poskytovatele:

![Pojďme si dešifrovat přípravný certifikát](media/ingress/staging-certificate.png)

Když změníte šifrování, aby se místo `staging`používalo `prod`, bude použit důvěryhodný certifikát vydaný pomocí šifry, jak je znázorněno v následujícím příkladu:

![Pojďme šifrovat certifikát](media/ingress/certificate.png)

Ukázková aplikace se zobrazí ve webovém prohlížeči:

![Příklad aplikace jedna](media/ingress/app-one.png)

Nyní přidejte cestu */Hello-World-Two* k plně kvalifikovanému názvu domény, například *https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two* . Zobrazí se druhá ukázková aplikace s vlastním názvem:

![Příklad aplikace – dvě](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tento článek používá Helm k instalaci komponent příchozího přenosu dat, certifikátů a ukázkových aplikací. Když nasadíte graf Helm, vytvoří se několik prostředků Kubernetes. Tyto prostředky zahrnují lusky, nasazení a služby. Chcete-li vyčistit tyto prostředky, můžete buď odstranit celý vzorový obor názvů, nebo jednotlivé prostředky.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Odstranění ukázkového oboru názvů a všech prostředků

Chcete-li odstranit celý ukázkový obor názvů, použijte příkaz `kubectl delete` a zadejte název oboru názvů. Všechny prostředky v oboru názvů jsou odstraněny.

```console
kubectl delete namespace ingress-basic
```

Pak odeberte úložiště Helm pro aplikaci AKS Hello World:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Odstranit prostředky jednotlivě

Další možností je podrobnější přístup k odstranění jednotlivých vytvořených prostředků. Nejdřív odeberte prostředky certifikátu:

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Nyní můžete zobrazit seznam vydaných verzí Helm pomocí příkazu `helm list`. Vyhledejte grafy s názvem *Nginx-* příchozí, *CERT-Manager*a *AKS-HelloWorld*, jak je znázorněno v následujícím příkladu výstupu:

```
$ helm list

NAME                    REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
waxen-hamster           1           Wed Mar  6 23:16:00 2019    DEPLOYED    nginx-ingress-1.3.1   0.22.0        kube-system
alliterating-peacock    1           Wed Mar  6 23:17:37 2019    DEPLOYED    cert-manager-v0.6.6     v0.6.2      kube-system
mollified-armadillo     1           Wed Mar  6 23:26:04 2019    DEPLOYED    aks-helloworld-0.1.0                default
wondering-clam          1           Wed Mar  6 23:26:07 2019    DEPLOYED    aks-helloworld-0.1.0                default
```

Odstraňte vydané verze pomocí příkazu `helm delete`. Následující příklad odstraní nasazení NGINX příchozího přenosu dat, správce certifikátů a dvě ukázkové AKS aplikace Hello World.

```
$ helm delete waxen-hamster alliterating-peacock mollified-armadillo wondering-clam

release "billowing-kitten" deleted
release "loitering-waterbuffalo" deleted
release "flabby-deer" deleted
release "linting-echidna" deleted
```

Pak odeberte úložiště Helm pro aplikaci AKS Hello World:

```console
helm repo remove azure-samples
```

Odebrat trasu příchozího přenosu dat směrovaného do ukázkových aplikací:

```console
kubectl delete -f hello-world-ingress.yaml
```

Odstraňte vlastní obor názvů. Použijte příkaz `kubectl delete` a zadejte název vašeho oboru názvů:

```console
kubectl delete namespace ingress-basic
```

Nakonec Odeberte statickou veřejnou IP adresu vytvořenou pro kontroler příchozího přenosu dat. Zadejte název skupiny prostředků clusteru *MC_* získané v prvním kroku tohoto článku, například *MC_myResourceGroup_myAKSCluster_eastus*:

```azurecli-interactive
az network public-ip delete --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP
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
- [Vytvoření kontroleru příchozího přenosu dat s dynamickou veřejnou IP adresou a konfigurací šifry pro automatické generování certifikátů TLS][aks-ingress-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
