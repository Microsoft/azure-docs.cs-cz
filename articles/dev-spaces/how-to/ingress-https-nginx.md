---
title: Použití vlastního kontroleru NGINX příchozího přenosu dat a konfigurace HTTPS
services: azure-dev-spaces
ms.date: 12/10/2019
ms.topic: conceptual
description: Naučte se nakonfigurovat Azure Dev Spaces k použití vlastního kontroleru NGINX příchozího přenosu dat a konfiguraci HTTPS pomocí tohoto kontroleru příchozího přenosu dat.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, síť pro služby, směrování sítě pro služby, kubectl, k8s
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 873057e88809c1aaa8047ac02129d83ca8e9a478
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608550"
---
# <a name="use-a-custom-nginx-ingress-controller-and-configure-https"></a>Použití vlastního kontroleru NGINX příchozího přenosu dat a konfigurace HTTPS

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

V tomto článku se dozvíte, jak nakonfigurovat Azure Dev Spaces, aby používaly vlastní kontroler příchozího přenosu dat (NGINX). Tento článek také ukazuje, jak nakonfigurovat tento vlastní kontroler příchozích dat na používání protokolu HTTPS.

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Pokud žádné nemáte, můžete si vytvořit [bezplatný účet][azure-account-create].
* [Nainstalované rozhraní Azure CLI][az-cli]
* Cluster služby Azure Kubernetes (AKS) s povoleným Azure Dev Spaces.
* [kubectl][kubectl] je nainstalovaný.
* Je [nainstalovaná Helm 3][helm-installed].
* [Vlastní doména][custom-domain] se [zónou DNS][dns-zone].  Tento článek předpokládá, že vlastní doména a zóna DNS jsou ve stejné skupině prostředků jako cluster AKS, ale je možné použít vlastní doménu a zónu DNS v jiné skupině prostředků.

## <a name="configure-a-custom-nginx-ingress-controller"></a>Konfigurace vlastního kontroleru NGINX příchozího přenosu dat

Připojte se ke clusteru pomocí [kubectl][kubectl]a klienta příkazového řádku Kubernetes. Pomocí příkazu [az aks get-credentials][az-aks-get-credentials] nakonfigurujte klienta `kubectl` pro připojení k vašemu clusteru Kubernetes. Tento příkaz stáhne pověření a nakonfiguruje rozhraní příkazového řádku Kubernetes pro jejich použití.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

Pokud chcete ověřit připojení ke clusteru, použijte příkaz [kubectl get][kubectl-get], který vrátí seznam uzlů clusteru.

```console
kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

Přidejte [oficiální stabilní úložiště Helm][helm-stable-repo], které obsahuje graf Helm řadiče Nginx pro příchozí přenosy.

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

Vytvořte Kubernetes obor názvů pro kontroler NGINX pro příchozí přenos dat a nainstalujte ho pomocí `helm` .

```console
kubectl create ns nginx
helm install nginx stable/nginx-ingress --namespace nginx --version 1.27.0
```

> [!NOTE]
> Výše uvedený příklad vytvoří veřejný koncový bod pro váš kontroler příchozího přenosu dat. Pokud pro svůj kontroler příchozího přenosu dat potřebujete místo toho použít privátní koncový bod, přidejte *--set Controller. Service. anotace. Service \\ . beta \\ . Kubernetes \\ . IO/Azure-Load-vyrovnávání zatížení – interní parametr "= true"* pro příkaz *Helm Install* . Příklad:
> ```console
> helm install nginx stable/nginx-ingress --namespace nginx --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-internal"=true --version 1.27.0
> ```
> Tento soukromý koncový bod se zveřejňuje v rámci virtuální sítě, ve které je nasazený cluster AKS.

Získejte IP adresu služby NGINX příchozího řadiče domény pomocí [kubectl Get][kubectl-get].

```console
kubectl get svc -n nginx --watch
```

Vzorový výstup zobrazuje IP adresy pro všechny služby v *nginxm* oboru názvů.

```console
NAME                                  TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                      AGE
nginx-nginx-ingress-controller        LoadBalancer   10.0.19.39     <pending>        80:31314/TCP,443:30521/TCP   10s
nginx-nginx-ingress-default-backend   ClusterIP      10.0.210.231   <none>           80/TCP                       10s
...
nginx-nginx-ingress-controller        LoadBalancer   10.0.19.39     MY_EXTERNAL_IP   80:31314/TCP,443:30521/TCP   26s
```

Přidejte do zóny DNS záznam *A* s externí IP adresou služby Nginx pomocí [AZ Network DNS Record-set a Add-Record][az-network-dns-record-set-a-add-record].

```azurecli
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.nginx \
    --ipv4-address MY_EXTERNAL_IP
```

Výše uvedený příklad přidá záznam *a* do zóny DNS *MY_CUSTOM_DOMAIN* .

V tomto článku se používá [ukázková aplikace Azure dev Spaces pro sdílení kol](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) k předvedení používání Azure dev Spaces. Naklonujte aplikaci z GitHubu a přejděte do jejího adresáře:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/charts
```

Otevřete [hodnoty. yaml][values-yaml] a proveďte následující aktualizace:
* Nahradí všechny výskyty *<REPLACE_ME_WITH_HOST_SUFFIX>* pomocí *Nginx. MY_CUSTOM_DOMAIN* používání vaší domény pro *MY_CUSTOM_DOMAIN*. 
* Nahraďte *Kubernetes.IO/Ingress.Class: traefik-azds # dev Spaces-Specific* to *Kubernetes.IO/Ingress.Class: Nginx # Custom* příchozí příchozí přenos dat. 

Níže je příklad aktualizovaného `values.yaml` souboru:

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: nginx  # Custom Ingress
    hosts:
      - dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: nginx  # Custom Ingress
    hosts:
      - dev.gateway.nginx.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
```

Uložte změny a zavřete soubor.

Vytvořte místo pro *vývoj* pomocí ukázkové aplikace `azds space select` .

```console
azds space select -n dev -y
```

Nasaďte ukázkovou aplikaci pomocí `helm install` .

```console
helm install bikesharingsampleapp . --dependency-update --namespace dev --atomic
```

Výše uvedený příklad nasadí ukázkovou aplikaci do oboru názvů pro *vývoj* .

Zobrazit adresy URL pro přístup k ukázkové aplikaci pomocí `azds list-uris` .

```console
azds list-uris
```

Níže uvedený výstup ukazuje příklady adres URL z `azds list-uris` .

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.nginx.MY_CUSTOM_DOMAIN/         Available
```

Přejděte do služby *bikesharingweb* otevřením veřejné adresy URL z `azds list-uris` příkazu. Ve výše uvedeném příkladu je veřejná adresa URL pro službu *bikesharingweb* `http://dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/` .

> [!NOTE]
> Pokud se místo služby *bikesharingweb* zobrazí chybová stránka, ověřte **, že jste aktualizovali** poznámku *Kubernetes.IO/Ingress.Class* a hostitele v souboru *Values. yaml* .

Pomocí `azds space select` příkazu vytvořte v oblasti *vývoj* podřízený prostor a seznam adres URL pro přístup k podřízenému místu pro vývoj.

```console
azds space select -n dev/azureuser1 -y
azds list-uris
```

Následující výstup zobrazuje ukázkové adresy URL z `azds list-uris` pro přístup k ukázkové aplikaci v podřízeném prostoru *azureuser1* .

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://azureuser1.s.dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/  Available
http://azureuser1.s.dev.gateway.nginx.MY_CUSTOM_DOMAIN/         Available
```

Přejděte ke službě *bikesharingweb* v podřízeném prostoru *azureuser1* pro vývoj otevřením veřejné adresy URL z `azds list-uris` příkazu. Ve výše uvedeném příkladu je veřejná adresa URL služby *bikesharingweb* v podřízeném prostoru *azureuser1* pro vývoj `http://azureuser1.s.dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/` .

## <a name="configure-the-nginx-ingress-controller-to-use-https"></a>Konfigurace kontroleru NGINX příchozího přenosu dat na používání protokolu HTTPS

Pomocí [Správce certifikátů][cert-manager] můžete automatizovat správu certifikátu TLS při konfiguraci kontroleru Nginx příchozího přenosu dat na používání protokolu HTTPS. Použijte `helm` k instalaci grafu *certmanager* .

```console
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.12/deploy/manifests/00-crds.yaml --namespace nginx
kubectl label namespace nginx certmanager.k8s.io/disable-validation=true
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm install cert-manager --namespace nginx --version v0.12.0 jetstack/cert-manager --set ingressShim.defaultIssuerName=letsencrypt --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Vytvořte `letsencrypt-clusterissuer.yaml` soubor a aktualizujte pole e-mail pomocí své e-mailové adresy.

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

> [!NOTE]
> Pro účely testování je k dispozici také [přípravný Server][letsencrypt-staging-issuer] , který můžete použít pro *ClusterIssuer*.

Použijte `kubectl` k použití `letsencrypt-clusterissuer.yaml` .

```console
kubectl apply -f letsencrypt-clusterissuer.yaml --namespace nginx
```

Aktualizujte [hodnoty. yaml][values-yaml] tak, aby obsahovaly podrobné informace o použití *certifikátů – Manager* a HTTPS. Níže je příklad aktualizovaného `values.yaml` souboru:

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: nginx  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN
      secretName: dev-bikesharingweb-secret

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: nginx  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.gateway.nginx.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.gateway.nginx.MY_CUSTOM_DOMAIN
      secretName: dev-gateway-secret
```

Upgradujte ukázkovou aplikaci pomocí `helm` :

```console
helm upgrade bikesharingsampleapp . --namespace dev --atomic
```

Přejděte do ukázkové aplikace v podřízeném prostoru pro *vývoj/azureuser1* a Všimněte si, že budete přesměrováni na používání protokolu HTTPS. Všimněte si také, že se stránka načítá, ale v prohlížeči se zobrazují nějaké chyby. Otevřením konzoly prohlížeče se zobrazí chyba vztahující se ke stránce HTTPS, která se pokouší načíst prostředky HTTP. Příklad:

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.nginx.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

Tuto chybu opravíte tak, že aktualizujete [BikeSharingWeb/azds. yaml][azds-yaml] podobný následujícímu:

```yaml
...
    ingress:
      annotations:
        kubernetes.io/ingress.class: nginx
        cert-manager.io/cluster-issuer: letsencrypt
      hosts:
      # This expands to [space.s.][rootSpace.]bikesharingweb.<random suffix>.<region>.azds.io
      - $(spacePrefix)$(rootSpacePrefix)bikesharingweb.nginx.MY_CUSTOM_DOMAIN
      tls:
      - hosts:
        - $(spacePrefix)$(rootSpacePrefix)bikesharingweb.nginx.MY_CUSTOM_DOMAIN
        secretName: dev-bikesharingweb-secret
...
```

Aktualizujte [BikeSharingWeb/package.jsna][package-json] se závislostí pro balíček *URL* .

```json
{
...
    "react-responsive": "^6.0.1",
    "universal-cookie": "^3.0.7",
    "url": "0.11.0"
  },
...
```

Aktualizujte metodu *getApiHostAsync* v [BikeSharingWeb/lib/helpers.js][helpers-js] tak, aby používala protokol https:

```javascript
...
    getApiHostAsync: async function() {
        const apiRequest = await fetch('/api/host');
        const data = await apiRequest.json();
        
        var urlapi = require('url');
        var url = urlapi.parse(data.apiHost);

        console.log('apiHost: ' + "https://"+url.host);
        return "https://"+url.host;
    },
...
```

Přejděte do `BikeSharingWeb` adresáře a použijte `azds up` ho ke spuštění aktualizované služby *BikeSharingWeb* .

```console
cd ../BikeSharingWeb/
azds up
```

Přejděte do ukázkové aplikace v podřízeném prostoru pro *vývoj/azureuser1* a Všimněte si, že budete přesměrováni na použití protokolu HTTPS bez jakýchkoli chyb.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak Azure Dev Spaces funguje.

> [!div class="nextstepaction"]
> [Jak Azure Dev Spaces funguje](../how-dev-spaces-works.md)


[az-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../../app-service/manage-custom-dns-buy-domain.md#buy-an-app-service-domain
[dns-zone]: ../../dns/dns-getstarted-cli.md
[azds-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/azds.yaml
[azure-account-create]: https://azure.microsoft.com/free
[cert-manager]: https://cert-manager.io/
[helm-installed]: https://helm.sh/docs/intro/install/
[helm-stable-repo]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helpers-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/lib/helpers.js#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[letsencrypt-staging-issuer]: https://cert-manager.io/docs/configuration/acme/#creating-a-basic-acme-issuer
[package-json]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/package.json
[values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml
