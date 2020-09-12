---
title: Použít řadič příchozího přenosu se statickou IP adresou
titleSuffix: Azure Kubernetes Service
description: Naučte se, jak nainstalovat a nakonfigurovat řadič příchozího přenosu NGINX se statickou veřejnou IP adresou v clusteru Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: be4856beac69d11de12ec764f313fa59f3b24e9f
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89290544"
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

Tento článek používá [Helm 3][helm] k instalaci kontroleru Nginx příchozího přenosu dat a správce certifikátů. Ujistěte se, že používáte nejnovější verzi Helm a máte přístup k úložištím *Nginx* a *jetstack* Helm. Pokyny k upgradu najdete v [dokumentaci k instalaci Helm][helm-install]. Další informace o konfiguraci a použití Helm najdete v tématu [install Applications with Helm in Azure Kubernetes Service (AKS)][use-helm].

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

> [!NOTE]
> Výše uvedené příkazy vytvoří IP adresu, která se odstraní, když odstraníte cluster AKS. Případně můžete vytvořit IP adresu v jiné skupině prostředků, kterou je možné spravovat odděleně od clusteru AKS. Pokud vytvoříte IP adresu v jiné skupině prostředků, ujistěte se, že instanční objekt používaný clusterem AKS má delegovaná oprávnění k jiné skupině prostředků, jako je například *Přispěvatel sítě*. Další informace najdete v tématu [použití statické veřejné IP adresy a popisku DNS s nástrojem AKS Load Balancer][aks-static-ip].

Teď nasaďte *Nginx a vstupní* graf s Helm. Pro přidání redundance se nasadí dvě repliky kontrolerů příchozího přenosu dat NGINX s parametrem `--set controller.replicaCount`. Pokud chcete mít v clusteru AKS k dispozici více než jeden uzel, zajistěte, aby bylo možné plně využít více uzlů.

Do vydaných verzí musíte předat dva další parametry, takže kontroler příchozího přenosu dat (Helm) bude mít na paměti, že obě statické IP adresy nástroje pro vyrovnávání zatížení budou přiděleny službě příchozího řadiče domény a popisek názvu DNS, který se použije u prostředku veřejné IP adresy. Aby certifikáty HTTPS správně fungovaly, použije se k nakonfigurování plně kvalifikovaného názvu domény pro IP adresu kontroleru příchozího přenosu názvů DNS.

1. Přidejte `--set controller.service.loadBalancerIP` parametr. Zadejte vlastní veřejnou IP adresu vytvořenou v předchozím kroku.
1. Přidejte `--set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"` parametr. Zadejte popisek názvu DNS, který se použije pro veřejnou IP adresu vytvořenou v předchozím kroku.

Kontroler příchozího přenosu dat je potřeba naplánovat také v uzlu Linuxu. V uzlech Windows Serveru by se kontroler příchozího přenosu dat neměl spouštět. Selektor uzlů se specifikuje pomocí parametru `--set nodeSelector`, aby plánovači Kubernetes oznámil, že má spustit kontroler příchozího přenosu dat NGINX v uzlu Linuxu.

> [!TIP]
> Následující příklad vytvoří obor názvů Kubernetes pro prostředky příchozího přenosu dat s názvem příchozí *– Basic*. Podle potřeby zadejte obor názvů pro vlastní prostředí. Pokud váš cluster AKS není RBAC povolený, přidejte `--set rbac.create=false` do příkazů Helm.

> [!TIP]
> Pokud chcete povolit [zachování IP adresy zdrojového klienta][client-source-ip] pro požadavky na kontejnery v clusteru, přidejte `--set controller.service.externalTrafficPolicy=Local` do příkazu Helm Install. Zdrojová IP adresa klienta je uložená v hlavičce žádosti v části *předané X-pro*. Při použití kontroleru příchozího přenosu dat s povoleným zachováním IP adresy klienta nebude předávat protokol TLS fungovat.

Aktualizujte následující skript s použitím **IP adresy** vašeho kontroleru příchozího přenosu dat a **jedinečného názvu** , který byste chtěli použít pro předponu plně kvalifikovaného názvu domény.

> [!IMPORTANT]
> Při spuštění příkazu je třeba aktualizovat položku nahradit *STATIC_IP* a *DNS_LABEL* s vlastní IP adresou a jedinečným názvem.

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
    --set controller.service.loadBalancerIP="STATIC_IP" \
    --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"="DNS_LABEL"
```

Když se pro kontroler příchozího přenosu NGINX vytvoří služba Vyrovnávání zatížení Kubernetes, vaše statická IP adresa se přiřadí, jak je znázorněno v následujícím příkladu výstupu:

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Zatím se nevytvořila žádná pravidla příchozího přenosu dat, takže pokud přejdete na veřejnou IP adresu, zobrazí se 404 výchozí stránka NGINX adaptéru pro příjem dat. Pravidla příchozího přenosu dat jsou nakonfigurovaná v následujících krocích.

Pomocí dotazu na plně kvalifikovaný název domény na veřejné IP adrese můžete ověřit, že je popisek názvu DNS použit následujícím způsobem:

```azurecli-interactive
az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query "[?name=='myAKSPublicIP'].[dnsSettings.fqdn]" -o tsv
```

Kontroler příchozího přenosu dat je teď přístupný prostřednictvím IP adresy nebo plně kvalifikovaného názvu domény.

## <a name="install-cert-manager"></a>Instalace nástroje cert-manager

Kontroler příchozího přenosu dat NGINX podporuje ukončení protokolu TLS. Existuje několik způsobů, jak načíst a nakonfigurovat certifikáty pro protokol HTTPS. V tomto článku se dozvíte, jak používat [Správce certifikátů][cert-manager], který umožňuje automatické [šifrování][lets-encrypt] generování certifikátů a funkcí správy.

> [!NOTE]
> V tomto článku se používá prostředí, které `staging` umožňuje šifrování. V produkčních nasazeních použijte `letsencrypt-prod` a `https://acme-v02.api.letsencrypt.org/directory` v definicích prostředků a při instalaci grafu Helm.

Pokud chcete nainstalovat kontrolér správce certifikátů do clusteru s podporou RBAC, použijte následující `helm install` příkaz:

```console
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
          podTemplate:
            spec:
              nodeSelector:
                "kubernetes.io/os": linux
```

K vytvoření vystavitele použijte `kubectl apply` příkaz.

```
$ kubectl apply -f cluster-issuer.yaml --namespace ingress-basic

clusterissuer.cert-manager.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>Spustit ukázkové aplikace

Je nakonfigurovaný kontroler příchozího přenosu dat a řešení správy certifikátů. Teď spustíme dvě ukázkové aplikace v clusteru AKS. V tomto příkladu se Helm používá k nasazení dvou instancí jednoduché aplikace Hello World.

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
        image: neilpeterson/aks-helloworld:v1
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
        image: neilpeterson/aks-helloworld:v1
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

V následujícím příkladu je přenos do adresy `https://demo-aks-ingress.eastus.cloudapp.azure.com/` směrován do služby s názvem `aks-helloworld` . Provoz na adresu `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` je směrován do `ingress-demo` služby. Aktualizujte *hostitele* a *hostitele* na název DNS, který jste vytvořili v předchozím kroku.

Vytvořte soubor s názvem `hello-world-ingress.yaml` a zkopírujte ho do následujícího příkladu YAML.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    cert-manager.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /$1
    nginx.ingress.kubernetes.io/use-regex: "true"
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

Pomocí příkazu vytvořte prostředek příchozího přenosu dat `kubectl apply` .

```
$ kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic

ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>Vytvoření objektu certifikátu

V dalším kroku se musí vytvořit prostředek certifikátu. Prostředek certifikátu definuje požadovaný certifikát X. 509. Další informace najdete v tématu [certifikáty pro správce][cert-manager-certificates]certifikátů.

Správce certifikátů pravděpodobně pro vás automaticky vytvořil objekt certifikátu pomocí příchozího překrytí, které se automaticky nasazuje pomocí nástroje CERT Manager od verze v 0.2.2. Další informace najdete v dokumentaci ke [vstupnímu překrytí][ingress-shim].

Chcete-li ověřit, zda byl certifikát vytvořen úspěšně, použijte `kubectl describe certificate tls-secret --namespace ingress-basic` příkaz.

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

Prostředek certifikátu vytvoříte pomocí `kubectl apply` příkazu.

```
$ kubectl apply -f certificates.yaml

certificate.cert-manager.io/tls-secret created
```

## <a name="test-the-ingress-configuration"></a>Test konfigurace příchozího přenosu dat

Otevřete webový prohlížeč s plně kvalifikovaným názvem domény vašeho kontroleru Kubernetes příchozího přenosu dat, například *`https://demo-aks-ingress.eastus.cloudapp.azure.com`* .

Jak tyto příklady používají `letsencrypt-staging` , vydaný certifikát TLS/SSL není v prohlížeči důvěryhodný. Přijměte výzvu k zadání upozornění, abyste mohli pokračovat v používání aplikace. Informace o certifikátu zobrazí tento *falešný certifikát x1 pro Intermediate* , který je vydaný pomocí zašifrování. Tento falešný certifikát indikuje `cert-manager` správné zpracování žádosti a obdržel certifikát od poskytovatele:

![Pojďme si dešifrovat přípravný certifikát](media/ingress/staging-certificate.png)

Když změníte šifrování tak, aby se `prod` místo toho používal `staging` důvěryhodný certifikát vydaný pomocí šifry, jak je znázorněno v následujícím příkladu:

![Pojďme šifrovat certifikát](media/ingress/certificate.png)

Ukázková aplikace se zobrazí ve webovém prohlížeči:

![Příklad aplikace jedna](media/ingress/app-one.png)

Nyní přidejte cestu */Hello-World-Two* k plně kvalifikovanému názvu domény, například *`https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two`* . Zobrazí se druhá ukázková aplikace s vlastním názvem:

![Příklad aplikace – dvě](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tento článek používá Helm k instalaci komponent příchozího přenosu dat, certifikátů a ukázkových aplikací. Když nasadíte graf Helm, vytvoří se několik prostředků Kubernetes. Tyto prostředky zahrnují lusky, nasazení a služby. Chcete-li vyčistit tyto prostředky, můžete buď odstranit celý vzorový obor názvů, nebo jednotlivé prostředky.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Odstranění ukázkového oboru názvů a všech prostředků

Chcete-li odstranit celý vzorový obor názvů, použijte `kubectl delete` příkaz a zadejte název oboru názvů. Všechny prostředky v oboru názvů jsou odstraněny.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Odstranit prostředky jednotlivě

Další možností je podrobnější přístup k odstranění jednotlivých vytvořených prostředků. Nejdřív odeberte prostředky certifikátu:

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Teď seznam vydaných verzí Helm pomocí `helm list` příkazu. Vyhledejte grafy s názvem *Nginx-* příchozí a *CERT-Manager* , jak je znázorněno v následujícím příkladu výstupu:

```
$ helm list --all-namespaces

NAME                    NAMESPACE       REVISION        UPDATED                        STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-11 14:51:03.454165006  deployed        nginx-ingress-1.28.2    0.26.2
cert-manager            ingress-basic   1               2020-01-06 21:19:03.866212286  deployed        cert-manager-v0.13.0    v0.13.0
```

Odinstalujte verze pomocí `helm uninstall` příkazu. Následující příklad odinstaluje nasazení NGINX příchozího přenosu a nasazení Správce certifikátů.

```
$ helm uninstall nginx-ingress cert-manager -n ingress-basic

release "nginx-ingress" deleted
release "cert-manager" deleted
```

Dále odeberte dvě ukázkové aplikace:

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
```

Odstraňte vlastní obor názvů. Použijte `kubectl delete` příkaz a zadejte název vašeho oboru názvů:

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
[helm-cli]: ./kubernetes-helm.md
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm]: https://helm.sh/
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
[aks-static-ip]: static-ip.md
