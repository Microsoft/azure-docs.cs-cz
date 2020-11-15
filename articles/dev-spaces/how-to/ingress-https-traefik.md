---
title: Použití vlastního kontroleru traefik příchozího přenosu dat a konfigurace HTTPS
services: azure-dev-spaces
ms.date: 12/10/2019
ms.topic: conceptual
description: Naučte se nakonfigurovat Azure Dev Spaces k použití vlastního kontroleru traefik příchozího přenosu dat a konfiguraci HTTPS pomocí tohoto kontroleru příchozího přenosu dat.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, síť pro služby, směrování sítě pro služby, kubectl, k8s
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 2dcb549078f1f0f5f7168960864d564fd0c169fc
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2020
ms.locfileid: "94636822"
---
# <a name="use-a-custom-traefik-ingress-controller-and-configure-https"></a>Použití vlastního kontroleru traefik příchozího přenosu dat a konfigurace HTTPS

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

V tomto článku se dozvíte, jak nakonfigurovat Azure Dev Spaces, aby používaly vlastní kontroler příchozího přenosu dat (traefik). Tento článek také ukazuje, jak nakonfigurovat tento vlastní kontroler příchozích dat na používání protokolu HTTPS.

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Pokud žádné nemáte, můžete si vytvořit [bezplatný účet][azure-account-create].
* [Nainstalované rozhraní Azure CLI][az-cli]
* [Cluster Azure Kubernetes Service (AKS) s povoleným Azure Dev Spaces] [QS-CLI].
* [kubectl][kubectl] je nainstalovaný.
* Je [nainstalovaná Helm 3][helm-installed].
* [Vlastní doména][custom-domain] se [zónou DNS][dns-zone]. Tento článek předpokládá, že vlastní doména a zóna DNS jsou ve stejné skupině prostředků jako cluster AKS, ale je možné použít vlastní doménu a zónu DNS v jiné skupině prostředků.

## <a name="configure-a-custom-traefik-ingress-controller"></a>Konfigurace vlastního kontroleru traefik příchozího přenosu dat

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

Přidejte [oficiální stabilní úložiště Helm][helm-stable-repo], které obsahuje graf Helm řadiče traefik pro příchozí přenosy.

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

Vytvořte Kubernetes obor názvů pro kontroler traefik pro příchozí přenos dat a nainstalujte ho pomocí `helm` .

> [!NOTE]
> Pokud v clusteru AKS není povolená Kubernetes RBAC, odeberte parametr *--set RBAC. Enabled = true* .

```console
kubectl create ns traefik
helm install traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set fullnameOverride=customtraefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --version 1.85.0
```

> [!NOTE]
> Výše uvedený příklad vytvoří veřejný koncový bod pro váš kontroler příchozího přenosu dat. Pokud pro svůj kontroler příchozího přenosu dat potřebujete místo toho použít privátní koncový bod, přidejte *--set Service. anotace. Service \\ . beta \\ . Kubernetes \\ . IO/Azure-Load-vyrovnávání zatížení – interní parametr "= true"* pro příkaz *Helm Install* .
> ```console
> helm install traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set fullnameOverride=customtraefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --set service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-internal"=true --version 1.85.0
> ```
> Tento soukromý koncový bod se zveřejňuje v rámci virtuální sítě, ve které je nasazený cluster AKS.

Získejte IP adresu služby traefik příchozího řadiče domény pomocí [kubectl Get][kubectl-get].

```console
kubectl get svc -n traefik --watch
```

Vzorový výstup zobrazuje IP adresy pro všechny služby v *traefikm* oboru názvů.

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>     80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

Přidejte do zóny DNS záznam *A* s externí IP adresou služby traefik pomocí [AZ Network DNS Record-set a Add-Record][az-network-dns-record-set-a-add-record].

```azurecli
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_EXTERNAL_IP
```

Výše uvedený příklad přidá záznam *a* do zóny DNS *MY_CUSTOM_DOMAIN* .

V tomto článku se používá [ukázková aplikace Azure dev Spaces pro sdílení kol](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) k předvedení používání Azure dev Spaces. Naklonujte aplikaci z GitHubu a přejděte do jejího adresáře:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/charts
```

Otevřete [hodnoty. yaml][values-yaml] a proveďte následující aktualizace:
* Nahradí všechny výskyty *<REPLACE_ME_WITH_HOST_SUFFIX>* pomocí *traefik. MY_CUSTOM_DOMAIN* používání vaší domény pro *MY_CUSTOM_DOMAIN*. 
* Nahraďte *Kubernetes.IO/Ingress.Class: traefik-azds # dev Spaces-Specific* to *Kubernetes.IO/Ingress.Class: traefik # Custom* příchozí příchozí přenos dat. 

Níže je příklad aktualizovaného `values.yaml` souboru:

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
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
http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Přejděte do služby *bikesharingweb* otevřením veřejné adresy URL z `azds list-uris` příkazu. Ve výše uvedeném příkladu je veřejná adresa URL pro službu *bikesharingweb* `http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/` .

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
http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Přejděte ke službě *bikesharingweb* v podřízeném prostoru *azureuser1* pro vývoj otevřením veřejné adresy URL z `azds list-uris` příkazu. Ve výše uvedeném příkladu je veřejná adresa URL služby *bikesharingweb* v podřízeném prostoru *azureuser1* pro vývoj `http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/` .

## <a name="configure-the-traefik-ingress-controller-to-use-https"></a>Konfigurace kontroleru traefik příchozího přenosu dat na používání protokolu HTTPS

Pomocí [Správce certifikátů][cert-manager] můžete automatizovat správu certifikátu TLS při konfiguraci kontroleru traefik příchozího přenosu dat na používání protokolu HTTPS. Použijte `helm` k instalaci grafu *certmanager* .

```console
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.12/deploy/manifests/00-crds.yaml --namespace traefik
kubectl label namespace traefik certmanager.k8s.io/disable-validation=true
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm install cert-manager --namespace traefik --version v0.12.0 jetstack/cert-manager --set ingressShim.defaultIssuerName=letsencrypt --set ingressShim.defaultIssuerKind=ClusterIssuer
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
            class: traefik
```

> [!NOTE]
> Pro účely testování je k dispozici také [přípravný Server][letsencrypt-staging-issuer] , který můžete použít pro *ClusterIssuer*.

Použijte `kubectl` k použití `letsencrypt-clusterissuer.yaml` .

```console
kubectl apply -f letsencrypt-clusterissuer.yaml --namespace traefik
```

Odeberte předchozí *traefik* *ClusterRole* a *ClusterRoleBinding* a potom upgradujte TRAEFIK, aby bylo možné používat protokol HTTPS pomocí `helm` .

> [!NOTE]
> Pokud v clusteru AKS není povolená Kubernetes RBAC, odeberte parametr *--set RBAC. Enabled = true* .

```console
kubectl delete ClusterRole traefik
kubectl delete ClusterRoleBinding traefik
helm upgrade traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --version 1.85.0 --set ssl.enabled=true --set ssl.enforced=true --set ssl.permanentRedirect=true
```

Získejte aktualizovanou IP adresu služby traefik příchozího řadiče domény pomocí [kubectl Get][kubectl-get].

```console
kubectl get svc -n traefik --watch
```

Vzorový výstup zobrazuje IP adresy pro všechny služby v *traefikm* oboru názvů.

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP          PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>            80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_NEW_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

Přidejte do zóny DNS záznam *A* s novou externí IP adresou služby traefik pomocí [AZ Network DNS Record-set a Add-Record][az-network-dns-record-set-a-add-record] a *odeberte předchozí záznam pomocí* [AZ Network DNS Record-set a Remove-Record][az-network-dns-record-set-a-remove-record].

```azurecli
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_NEW_EXTERNAL_IP

az network dns record-set a remove-record \
    --resource-group myResourceGroup \
    --zone-name  MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address PREVIOUS_EXTERNAL_IP
```

Výše uvedený příklad aktualizuje záznam *A* v zóně DNS *MY_CUSTOM_DOMAIN* pro použití *PREVIOUS_EXTERNAL_IP*.

Aktualizujte [hodnoty. yaml][values-yaml] tak, aby obsahovaly podrobné informace o použití *certifikátů – Manager* a HTTPS. Níže je příklad aktualizovaného `values.yaml` souboru:

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN
      secretName: dev-bikesharingweb-secret

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN
      secretName: dev-gateway-secret
```

Upgradujte ukázkovou aplikaci pomocí `helm` :

```console
helm upgrade bikesharingsampleapp . --namespace dev --atomic
```

Přejděte do ukázkové aplikace v podřízeném prostoru pro *vývoj/azureuser1* a Všimněte si, že budete přesměrováni na používání protokolu HTTPS.

> [!IMPORTANT]
> Může trvat 30 minut nebo déle, než se změny DNS dokončí a vaše ukázková aplikace bude dostupná.

Všimněte si také, že se stránka načítá, ale v prohlížeči se zobrazují nějaké chyby. Otevřením konzoly prohlížeče se zobrazí chyba vztahující se ke stránce HTTPS, která se pokouší načíst prostředky HTTP. Příklad:

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

Pokud chcete tuto chybu opravit, aktualizujte [BikeSharingWeb/azds. yaml][azds-yaml] tak, aby používaly *traefik* pro *Kubernetes.IO/Ingress.Class* a vlastní doménu pro *$ (hostSuffix)*. Příklad:

```yaml
...
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik
      hosts:
      # This expands to [space.s.][rootSpace.]bikesharingweb.<random suffix>.<region>.azds.io
      - $(spacePrefix)$(rootSpacePrefix)bikesharingweb.traefik.MY_CUSTOM_DOMAIN
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

Přejděte do `BikeSharingWeb` adresáře a použijte `azds up` ho ke spuštění aktualizované služby BikeSharingWeb.

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
[az-network-dns-record-set-a-remove-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-remove-record
[custom-domain]: ../../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
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
