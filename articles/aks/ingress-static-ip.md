---
title: Použití řadiče příchozího přenosu dat se statickou IP adresou
titleSuffix: Azure Kubernetes Service
description: Zjistěte, jak nainstalovat a nakonfigurovat řadič příchozího přenosu dat NGINX se statickou veřejnou IP adresou v clusteru služby Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: fe7f1070ce233c204d9658d4a75c5e1c7a189f12
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668509"
---
# <a name="create-an-ingress-controller-with-a-static-public-ip-address-in-azure-kubernetes-service-aks"></a>Vytvoření řadiče příchozího přenosu dat se statickou veřejnou IP adresou ve službě Azure Kubernetes Service (AKS)

Řadič příchozího přenosu dat je software, který poskytuje reverzní proxy server, konfigurovatelné směrování provozu a ukončení TLS pro služby Kubernetes. Prostředky příchozího přenosu dat kubernetes se používají ke konfiguraci pravidel příchozího přenosu dat a tras pro jednotlivé služby Kubernetes. Pomocí řadiče příchozího přenosu dat a pravidel příchozího přenosu dat lze jednu adresu IP použít k směrování provozu do více služeb v clusteru Kubernetes.

Tento článek ukazuje, jak nasadit [řadič příchozího přenosu dat NGINX][nginx-ingress] v clusteru služby Azure Kubernetes Service (AKS). Řadič příchozího přenosu dat je nakonfigurován se statickou veřejnou IP adresou. Projekt [správce certifikátů][cert-manager] se používá k automatickému generování a konfiguraci certifikátů [Let's Encrypt.][lets-encrypt] Nakonec jsou v clusteru AKS spuštěny dvě aplikace, z nichž každá je přístupná přes jednu adresu IP.

Můžete také:

- [Vytvoření základního řadiče příchozího přenosu dat s externím připojením k síti][aks-ingress-basic]
- [Povolení doplňku směrování aplikací HTTP][aks-http-app-routing]
- [Vytvoření řadiče příchozího přenosu dat, který používá vlastní certifikáty TLS][aks-ingress-own-tls]
- [Vytvoření řadiče příchozího přenosu dat, který používá let's Encrypt k automatickému generování certifikátů TLS s dynamickou veřejnou IP adresou][aks-ingress-tls]

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, podívejte se na aks rychlý start [pomocí Azure CLI][aks-quickstart-cli] nebo [pomocí portálu Azure][aks-quickstart-portal].

Tento článek používá Helm k instalaci řadiče příchozího přenosu dat NGINX, správce certifikátů a ukázkové webové aplikace. Ujistěte se, že používáte nejnovější verzi Helmu. Pokyny k upgradu naleznete v [dokumentech k instalaci helmy][helm-install]. Další informace o konfiguraci a používání helmu najdete v [tématu Instalace aplikací pomocí helmu ve službě Azure Kubernetes Service (AKS).][use-helm]

Tento článek také vyžaduje, abyste spouštěli Azure CLI verze 2.0.64 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Vytvoření řadiče příchozího přenosu dat

Ve výchozím nastavení je řadič příchozího přenosu dat NGINX vytvořen s novým přiřazením veřejné IP adresy. Tato veřejná IP adresa je pouze statická pro životnost řadiče příchozího přenosu dat a je ztracena, pokud je řadič odstraněn a znovu vytvořen. Běžným požadavkem na konfiguraci je poskytnout řadiči příchozího přenosu dat NGINX existující statickou veřejnou IP adresu. Statická veřejná IP adresa zůstane, pokud je odstraněn řadič příchozího přenosu dat. Tento přístup umožňuje používat existující záznamy DNS a konfigurace sítě konzistentním způsobem po celou dobu životnosti aplikací.

Pokud potřebujete vytvořit statickou veřejnou IP adresu, nejprve získejte název skupiny prostředků clusteru AKS pomocí příkazu [az aks show:][az-aks-show]

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

Dále vytvořte veřejnou IP adresu s *metodou statického* přidělení pomocí příkazu [az network public-ip create.][az-network-public-ip-create] Následující příklad vytvoří veřejnou IP adresu s názvem *myAKSPublicIP* ve skupině prostředků clusteru AKS získané v předchozím kroku:

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --sku Standard --allocation-method static --query publicIp.ipAddress -o tsv
```

Nyní nasaďte graf *nginx-ingress* s helmem. Pro přidání redundance se nasadí dvě repliky kontrolerů příchozího přenosu dat NGINX s parametrem `--set controller.replicaCount`. Chcete-li plně využít spuštění replik řadiče příchozího přenosu dat, ujistěte se, že je více než jeden uzel v clusteru AKS.

Musíte předat dva další parametry vydání helmu, aby byl řadič příchozího přenosu dat informován o statické IP adrese systému vyrovnávání zatížení, která má být přidělena službě řadiče příchozího přenosu dat, a popisku názvu DNS, který je použit pro prostředek veřejné IP adresy. Aby certifikáty HTTPS fungovaly správně, popisek názvu DNS se používá ke konfiguraci hlavního názvu domény pro ip adresu řadiče příchozího přenosu dat.

1. Přidejte `--set controller.service.loadBalancerIP` parametr. Zadejte vlastní veřejnou IP adresu, která byla vytvořena v předchozím kroku.
1. Přidejte `--set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"` parametr. Zadejte popisek názvu DNS, který se použije na veřejnou ADRESU IP, která byla vytvořena v předchozím kroku.

Kontroler příchozího přenosu dat je potřeba naplánovat také v uzlu Linuxu. Uzly systému Windows Server (aktuálně ve verzi preview v AKS) by neměly spouštět řadič příchozího přenosu dat. Selektor uzlů se specifikuje pomocí parametru `--set nodeSelector`, aby plánovači Kubernetes oznámil, že má spustit kontroler příchozího přenosu dat NGINX v uzlu Linuxu.

> [!TIP]
> Následující příklad vytvoří obor názvů Kubernetes pro prostředky příchozího přenosu dat s názvem *ingress-basic*. Podle potřeby zadejte obor názvů pro vlastní prostředí. Pokud váš cluster AKS není povolen `--set rbac.create=false` RBAC, přidejte do příkazů Helm.

> [!TIP]
> Pokud chcete povolit [uchování IP zdrojů klienta][client-source-ip] pro požadavky `--set controller.service.externalTrafficPolicy=Local` na kontejnery v clusteru, přidejte do příkazu Helm install. Zdrojová ADRESA klienta je uložena v hlavičce požadavku v části *X-Forwarded-For*. Při použití řadiče příchozího přenosu dat s povoleným uchováním IP zdroje klienta nebude předávací přenos SSL fungovat.

Aktualizujte následující skript s **IP adresou** řadiče příchozího přenosu dat a **jedinečným názvem,** který chcete použít pro předponu FQDN:

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.service.loadBalancerIP="40.121.63.72"
    --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"="demo-aks-ingress"
```

Při vytvoření služby Vyrovnávání zatížení Kubernetes pro řadič příchozího přenosu dat NGINX je přiřazena vaše statická IP adresa, jak je znázorněno na následujícím příkladu výstupu:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                        TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
nginx-ingress-controller                    LoadBalancer   10.0.232.56   40.121.63.72   80:31978/TCP,443:32037/TCP   3m
nginx-ingress-default-backend               ClusterIP      10.0.95.248   <none>         80/TCP                       3m
```

Zatím nebyla vytvořena žádná pravidla příchozího přenosu dat, takže při procházení veřejné IP adresy se zobrazí výchozí stránka řadiče příchozího přenosu dat NGINX 404. Pravidla příchozího přenosu dat jsou konfigurována v následujících krocích.

Můžete ověřit, zda byl použit popisek názvu DNS, takto dotazem na hlavní název domény na veřejné ADRESE IP následujícím způsobem:

```azurecli-interactive
#!/bin/bash
az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query $("[?name=='myAKSPublicIP'].[dnsSettings.fqdn]") -o tsv
```

The ingress controller is now accessible through the IP address or the FQDN.

## <a name="install-cert-manager"></a>Instalace nástroje cert-manager

Kontroler příchozího přenosu dat NGINX podporuje ukončení protokolu TLS. Existuje několik způsobů, jak načíst a nakonfigurovat certifikáty pro protokol HTTPS. Tento článek ukazuje použití [správce certifikátu][cert-manager], který poskytuje automatické [umožňuje šifrovat][lets-encrypt] generování certifikátů a funkce správy.

> [!NOTE]
> Tento článek `staging` používá prostředí pro Let's Encrypt. V produkčních nasazeních použijte `letsencrypt-prod` a `https://acme-v02.api.letsencrypt.org/directory` v definicích prostředků a při instalaci grafu Helm.

Chcete-li nainstalovat řadič správce certifikátu do clusteru s `helm install` podporou RBAC, použijte následující příkaz:

```console
# Install the CustomResourceDefinition resources separately
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.13/deploy/manifests/00-crds.yaml

# Label the cert-manager namespace to disable resource validation
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
  name: letsencrypt-staging
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    solvers:
    - http01:
        ingress:
          class: nginx
```

Chcete-li vytvořit vystavitel, použijte `kubectl apply -f cluster-issuer.yaml` příkaz.

```
$ kubectl apply -f cluster-issuer.yaml --namespace ingress-basic

clusterissuer.cert-manager.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>Spuštění ukázkových aplikací

Byl nakonfigurován řadič příchozího přenosu dat a řešení správy certifikátů. Nyní spustíme dvě ukázkové aplikace v clusteru AKS. V tomto příkladu Helm se používá k nasazení dvou instancí jednoduché aplikace "Hello world".

Před instalací ukázkových grafů Helm přidejte úložiště ukázek Azure do prostředí Helm následujícím způsobem:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Vytvořte první ukázkovou aplikaci z grafu Helm pomocí následujícího příkazu:

```console
helm install aks-helloworld azure-samples/aks-helloworld --namespace ingress-basic
```

Nyní nainstalujte druhou instanci ukázkové aplikace. Pro druhou instanci zadáte nový název tak, aby obě aplikace jsou vizuálně odlišné. Zadáte také jedinečný název služby:

```console
helm install aks-helloworld-2 azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Vytvoření postupu příchozího přenosu dat

Obě aplikace jsou nyní spuštěny v clusteru Kubernetes, ale `ClusterIP`jsou nakonfigurovány se službou typu . Jako takové aplikace nejsou přístupné z internetu. Chcete-li je zpřístupnit veřejně, vytvořte prostředek příchozího přenosu dat Kubernetes. Prostředek příchozího přenosu dat konfiguruje pravidla, která směrují provoz do jedné ze dvou aplikací.

V následujícím příkladu je `https://demo-aks-ingress.eastus.cloudapp.azure.com/` provoz na adresu směrován `aks-helloworld`na službu s názvem . Provoz na `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` adresu je směrován `ingress-demo` do služby. Aktualizujte *hostitele* a *hostitele* na název DNS, který jste vytvořili v předchozím kroku.

Vytvořte soubor `hello-world-ingress.yaml` s názvem a zkopírujte v následujícím příkladu YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
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

Vytvořte prostředek příchozího `kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic` přenosu dat pomocí příkazu.

```
$ kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic

ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>Vytvoření objektu certifikátu

Dále musí být vytvořen prostředek certifikátu. Prostředek certifikátu definuje požadovaný certifikát X.509. Další informace naleznete v [tématu certifikáty správce certifikátů][cert-manager-certificates].

Správce certifikátů pravděpodobně automaticky vytvořil objekt certifikátu pomocí příchozího posuvu, který je automaticky nasazen se správcem certifikátů od v0.2.2. Další informace naleznete [v dokumentaci ingress-shim][ingress-shim].

Chcete-li ověřit, zda byl certifikát `kubectl describe certificate tls-secret --namespace ingress-basic` úspěšně vytvořen, použijte příkaz.

Pokud byl certifikát vydán, zobrazí se výstup podobný následujícímu:
```
Type    Reason          Age   From          Message
----    ------          ----  ----          -------
  Normal  CreateOrder     11m   cert-manager  Created new ACME order, attempting validation...
  Normal  DomainVerified  10m   cert-manager  Domain "demo-aks-ingress.eastus.cloudapp.azure.com" verified with "http-01" validation
  Normal  IssueCert       10m   cert-manager  Issuing certificate...
  Normal  CertObtained    10m   cert-manager  Obtained certificate from ACME server
  Normal  CertIssued      10m   cert-manager  Certificate issued successfully
```

Pokud potřebujete vytvořit další prostředek certifikátu, můžete tak učinit s následujícím příkladem manifestu. Aktualizujte *dnsNames* a *domény* na název DNS, který jste vytvořili v předchozím kroku. Pokud používáte interní pouze ingress řadič, zadejte interní název DNS pro vaši službu.

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

Chcete-li vytvořit prostředek `kubectl apply -f certificates.yaml` certifikátu, použijte příkaz.

```
$ kubectl apply -f certificates.yaml

certificate.cert-manager.io/tls-secret created
```

## <a name="test-the-ingress-configuration"></a>Otestovat konfiguraci příchozího přenosu dat

Otevřete webový prohlížeč do plně přístupného domény řadiče příchozího přenosu *https://demo-aks-ingress.eastus.cloudapp.azure.com*dat Kubernetes, například .

Jak tyto příklady používají `letsencrypt-staging`, vydaný certifikát SSL není prohlížečem důvěryhodný. Přijměte výzvu upozornění, abyste pokračovali v aplikaci. Informace o certifikátu ukazují, že tento certifikát *Fake LE Intermediate X1* vydává společnost Let's Encrypt. Tento falešný `cert-manager` certifikát označuje, že žádost byl zpracován správně a obdržel certifikát od poskytovatele:

![Zašifrovat pracovní certifikát](media/ingress/staging-certificate.png)

Pokud změníte let's `prod` Encrypt `staging`použít spíše než , důvěryhodný certifikát vydaný Let's Encrypt se používá, jak je znázorněno v následujícím příkladu:

![Zašifrujte certifikát](media/ingress/certificate.png)

Demo aplikace je zobrazena ve webovém prohlížeči:

![Příklad aplikace jeden](media/ingress/app-one.png)

Nyní přidejte */hello-world-two* cestu k fQDN, například *https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two*. Druhá demo aplikace s vlastním názvem je zobrazena:

![Příklad aplikace dva](media/ingress/app-two.png)

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

Alternativně podrobnější přístup je odstranit jednotlivé vytvořené prostředky. Nejprve odeberte prostředky certifikátu:

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Nyní seznam Helm zprávy `helm list` s příkazem. Vyhledejte grafy s názvem *nginx-ingress*, *cert-manager*a *aks-helloworld*, jak je znázorněno v následujícím příkladu výstupu:

```
$ helm list --all-namespaces

NAME                    NAMESPACE       REVISION        UPDATED                        STATUS          CHART                   APP VERSION
aks-helloworld          ingress-basic   1               2020-01-11 15:02:21.51172346   deployed        aks-helloworld-0.1.0
aks-helloworld-2        ingress-basic   1               2020-01-11 15:03:10.533465598  deployed        aks-helloworld-0.1.0
nginx-ingress           ingress-basic   1               2020-01-11 14:51:03.454165006  deployed        nginx-ingress-1.28.2    0.26.2
cert-manager            ingress-basic    1               2020-01-06 21:19:03.866212286  deployed        cert-manager-v0.13.0    v0.13.0
```

Odstraňte verze pomocí `helm uninstall` příkazu. Následující příklad odstraní nasazení příchozího přenosu dat NGINX, správce certifikátů a dvě ukázkové aplikace AKS hello world.

```
$ helm uninstall aks-helloworld aks-helloworld-2 nginx-ingress cert-manager -n ingress-basic

release "aks-helloworld" deleted
release "aks-helloworld-2" deleted
release "nginx-ingress" deleted
release "cert-manager" deleted
```

Dále odeberte repo helmu pro aplikaci AKS hello world:

```console
helm repo remove azure-samples
```

Odstraňte samotný obor názvů. Použijte `kubectl delete` příkaz a zadejte název oboru názvů:

```console
kubectl delete namespace ingress-basic
```

Nakonec odeberte statickou veřejnou IP adresu vytvořenou pro řadič příchozího přenosu dat. Zadejte název skupiny prostředků *MC_* clusteru získaný v prvním kroku tohoto článku, například *MC_myResourceGroup_myAKSCluster_eastus*:

```azurecli-interactive
az network public-ip delete --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP
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
- [Vytvoření řadiče příchozího přenosu dat s dynamickou veřejnou IP adresou a konfigurace funkce Let's Encrypt tak, aby automaticky generovala certifikáty TLS][aks-ingress-tls]

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
