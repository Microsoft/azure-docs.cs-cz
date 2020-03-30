---
title: Použití vlastního řadiče příchozího přenosu dat traefik a konfigurace protokolu HTTPS
services: azure-dev-spaces
ms.date: 12/10/2019
ms.topic: conceptual
description: Zjistěte, jak nakonfigurovat Azure Dev Spaces tak, aby používal vlastní řadič příchozího přenosu dat traefik, a nakonfigurovat protokol HTTPS pomocí tohoto řadiče příchozího přenosu dat.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery, Helm, síť služeb, směrování sítě služeb, kubectl, k8s
ms.openlocfilehash: fd11b3bbd3f90b75203084ff0753c1485d57a35b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80155425"
---
# <a name="use-a-custom-traefik-ingress-controller-and-configure-https"></a>Použití vlastního řadiče příchozího přenosu dat traefik a konfigurace protokolu HTTPS

Tento článek ukazuje, jak nakonfigurovat Azure Dev Spaces pro použití vlastního řadiče příchozího přenosu dat traefik. Tento článek také ukazuje, jak nakonfigurovat tento vlastní řadič příchozího přenosu dat pro použití protokolu HTTPS.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud žádné nemáte, můžete si vytvořit [bezplatný účet][azure-account-create].
* [Nainstalované rozhraní Azure CLI][az-cli]
* [Cluster Azure Kubernetes Service (AKS) s povoleným Azure Dev Spaces][qs-cli].
* [kubectl][kubectl] nainstalován.
* [Helm 3 nainstalován][helm-installed].
* [Vlastní doména][custom-domain] se [zónou DNS][dns-zone]. Tento článek předpokládá, že vlastní doména a zóna DNS jsou ve stejné skupině prostředků jako cluster AKS, ale je možné použít vlastní doménu a zónu DNS v jiné skupině prostředků.

## <a name="configure-a-custom-traefik-ingress-controller"></a>Konfigurace vlastního řadiče příchozího přenosu dat traefik

Připojte se ke clusteru pomocí [kubectl][kubectl], klienta příkazového řádku Kubernetes. Pomocí příkazu [az aks get-credentials][az-aks-get-credentials] nakonfigurujte klienta `kubectl` pro připojení k vašemu clusteru Kubernetes. Tento příkaz stáhne pověření a nakonfiguruje rozhraní příkazového příkazu Kubernetes tak, aby je používalo.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

Pokud chcete ověřit připojení ke clusteru, použijte příkaz [kubectl get][kubectl-get], který vrátí seznam uzlů clusteru.

```console
kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

Přidejte [oficiální stabilní úložiště Helm][helm-stable-repo], které obsahuje graf Helm řadiče traefik.

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

Vytvořte obor názvů Kubernetes pro řadič přenosu dat traefik a nainstalujte jej pomocí `helm`.

> [!NOTE]
> Pokud váš cluster AKS nemá povolenou funkci RBAC, odeberte parametr *--set rbac.enabled=true.*

```console
kubectl create ns traefik
helm install traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set fullnameOverride=customtraefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --version 1.85.0
```

> [!NOTE]
> Výše uvedený příklad vytvoří veřejný koncový bod pro řadič příchozího přenosu dat. Pokud potřebujete místo toho použít soukromý koncový bod pro řadič příchozího přenosu dat, přidejte *--set service.anotations." service\\.beta\\\\.kubernetes .io/azure-load-balancer-internal"=true* parametr do příkazu helm *install.*
> ```console
> helm install traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set fullnameOverride=customtraefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --set service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-internal"=true --version 1.85.0
> ```
> Tento soukromý koncový bod je vystaven v rámci virtuální sítě, kde se nasadí cluster AKS.

Získejte IP adresu služby řadiče příchozího přenosu dat traefik pomocí [kubectl get][kubectl-get].

```console
kubectl get svc -n traefik --watch
```

Ukázkový výstup zobrazuje IP adresy pro všechny služby v překladu *názvů traefik.*

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>     80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

Přidejte záznam *A* do zóny DNS s externí IP adresou služby traefik pomocí [sady záznamů DNS az network a add-record][az-network-dns-record-set-a-add-record].

```azurecli
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_EXTERNAL_IP
```

Výše uvedený příklad přidá záznam *A* do zóny *DNS MY_CUSTOM_DOMAIN.*

V tomto článku použijete [ukázkovou aplikaci Azure Dev Spaces Bike Sharing](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) k předvedení pomocí Azure Dev Spaces. Klonujte aplikaci z GitHubu a přejděte do jejího adresáře:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/charts
```

Otevřete [soubor values.yaml][values-yaml] a proveďte následující aktualizace:
* Nahraďte všechny výskyty *<REPLACE_ME_WITH_HOST_SUFFIX>* *traefikem. MY_CUSTOM_DOMAIN* pro *MY_CUSTOM_DOMAIN*používat doménu . 
* Nahradit *kubernetes.io/ingress.class: traefik-azds # Dev Spaces-specifické* s *kubernetes.io/ingress.class: traefik # Custom Ingress*. 

Níže je uveden příklad `values.yaml` aktualizovaného souboru:

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

Vytvořte *dev* prostor s `azds space select`ukázkovou aplikací pomocí aplikace .

```console
azds space select -n dev -y
```

Nasazení ukázkové `helm install`aplikace pomocí .

```console
helm install bikesharingsampleapp . --dependency-update --namespace dev --atomic
```

Výše uvedený příklad nasazuje ukázkovou aplikaci do oboru názvů *dev.*

Zobrazení adres URL pro přístup k `azds list-uris`ukázkové aplikaci pomocí aplikace .

```console
azds list-uris
```

Níže uvedený výstup ukazuje příklad `azds list-uris`adres URL z .

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Přejděte na službu *bikesharingweb* otevřením `azds list-uris` veřejné adresy URL z příkazu. Ve výše uvedeném příkladu je `http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`veřejná adresa URL služby *bikesharingweb* .

> [!NOTE]
> Pokud se místo služby *bikesharingweb* zobrazí chybová stránka, ověřte, zda jste v souboru *values.yaml* **aktualizovali** *kubernetes.io/ingress.class* anotaci i hostitele.

Pomocí `azds space select` příkazu vytvořte podřízený prostor pod *dev* a uveďte adresy URL pro přístup k podřízenému dev prostoru.

```console
azds space select -n dev/azureuser1 -y
azds list-uris
```

Níže uvedený výstup ukazuje příklad `azds list-uris` adresy URL z pro přístup k ukázkové aplikace v *azureuser1* podřízené dev prostoru.

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Přejděte na *bikesharingweb* služby v *azureuser1* podřízené dev `azds list-uris` prostoru otevřením veřejné URL z příkazu. Ve výše uvedeném příkladu je `http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`veřejná adresa URL pro službu *bikesharingweb* v podřízeném dev prostoru *azureuser1* .

## <a name="configure-the-traefik-ingress-controller-to-use-https"></a>Konfigurace řadiče příchozího přenosu dat traefik pro použití protokolu HTTPS

Pomocí [správce cert-manageru][cert-manager] můžete automatizovat správu certifikátu TLS při konfiguraci řadiče příchozího přenosu dat traefik pro použití protokolu HTTPS. Slouží `helm` k instalaci grafu *certmanager.*

```console
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.12/deploy/manifests/00-crds.yaml --namespace traefik
kubectl label namespace traefik certmanager.k8s.io/disable-validation=true
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm install cert-manager --namespace traefik --version v0.12.0 jetstack/cert-manager --set ingressShim.defaultIssuerName=letsencrypt --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Vytvořte `letsencrypt-clusterissuer.yaml` soubor a aktualizujte pole e-mailu pomocí e-mailové adresy.

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
> Pro testování je také [pracovní server,][letsencrypt-staging-issuer] který můžete použít pro *clusterissuer*.

Slouží `kubectl` k `letsencrypt-clusterissuer.yaml`aplikaci .

```console
kubectl apply -f letsencrypt-clusterissuer.yaml --namespace traefik
```

Odeberte předchozí *funkce traefik* *ClusterRole* a *ClusterRoleBinding*a `helm`potom upgradujte traefik tak, aby používal protokol HTTPS pomocí .

> [!NOTE]
> Pokud váš cluster AKS nemá povolenou funkci RBAC, odeberte parametr *--set rbac.enabled=true.*

```console
kubectl delete ClusterRole traefik
kubectl delete ClusterRoleBinding traefik
helm upgrade traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --version 1.85.0 --set ssl.enabled=true --set ssl.enforced=true --set ssl.permanentRedirect=true
```

Získejte aktualizovanou IP adresu služby řadiče příchozího přenosu dat traefik pomocí [kubectl get][kubectl-get].

```console
kubectl get svc -n traefik --watch
```

Ukázkový výstup zobrazuje IP adresy pro všechny služby v překladu *názvů traefik.*

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP          PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>            80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_NEW_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

Přidejte záznam *A* do zóny DNS s novou externí IP adresou služby traefik pomocí [sady záznamů az network DNS a][az-network-dns-record-set-a-add-record] odeberte předchozí záznam *A* pomocí sady záznamů dns sítě AZ a [odeberte záznam][az-network-dns-record-set-a-remove-record].

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

Výše uvedený příklad aktualizuje záznam *A* v zóně *MY_CUSTOM_DOMAIN* DNS tak, aby používal *PREVIOUS_EXTERNAL_IP*.

Aktualizujte [soubor values.yaml][values-yaml] tak, aby obsahoval podrobnosti pro použití *správce certifikátů* a protokolu HTTPS. Níže je uveden příklad `values.yaml` aktualizovaného souboru:

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

Inovujte `helm`ukázkovou aplikaci pomocí :

```console
helm upgrade bikesharingsampleapp . --namespace dev --atomic
```

Přejděte na ukázkovou aplikaci v podřízeném prostoru *dev/azureuser1* a všimněte si, že jste přesměrováni na použití protokolu HTTPS.

> [!IMPORTANT]
> Dokončení změn DNS a přístupná ukázkové aplikaci může trvat 30 minut nebo déle.

Všimněte si také, že se stránka načte, ale prohlížeč zobrazuje některé chyby. Otevření konzoly prohlížeče ukazuje, že chyba se týká stránky HTTPS, která se pokouší načíst prostředky HTTP. Například:

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

Chcete-li tuto chybu opravit, aktualizujte [BikeSharingWeb/azds.yaml][azds-yaml] použít *traefik* pro *kubernetes.io/ingress.class* a vlastní doménu pro *$(hostSuffix)*. Například:

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

Aktualizujte [soubor BikeSharingWeb/package.json][package-json] závislostí na balíčku *adres URL.*

```json
{
...
    "react-responsive": "^6.0.1",
    "universal-cookie": "^3.0.7",
    "url": "0.11.0"
  },
...
```

Aktualizujte metodu *getApiHostAsync* v [souboru BikeSharingWeb/lib/helpers.js][helpers-js] tak, aby používala protokol HTTPS:

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

Přejděte `BikeSharingWeb` do adresáře a spusťte `azds up` aktualizovanou službu BikeSharingWeb.

```console
cd ../BikeSharingWeb/
azds up
```

Přejděte na ukázkovou aplikaci v podřízeném prostoru *dev/azureuser1* a všimněte si, že jste přesměrováni na použití protokolu HTTPS bez chyb.

## <a name="next-steps"></a>Další kroky

Zjistěte, jak Azure Dev Spaces pomáhá vyvíjet složitější aplikace napříč více kontejnery a jak můžete zjednodušit vývoj spolupráce pomocí práce s různými verzemi nebo větvemi kódu v různých prostorech.

> [!div class="nextstepaction"]
> [Vývoj týmu v Azure Dev Spaces][team-development-qs]


[az-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[az-network-dns-record-set-a-remove-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-remove-record
[custom-domain]: ../../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../../dns/dns-getstarted-cli.md
[qs-cli]: ../quickstart-cli.md
[team-development-qs]: ../quickstart-team-development.md

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