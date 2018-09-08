---
title: Konfigurace příchozí přenos dat pomocí clusteru Azure Kubernetes Service (AKS)
description: Zjistěte, jak nainstalovat a nakonfigurovat kontrolér příchozího přenosu dat serveru NGINX, která používá umožňuje šifrování pro automatické generování certifikátu SSL v clusteru služby Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 08/17/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 8ea63f7eee961d72ce2343fa92329d938a6fcd73
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44094871"
---
# <a name="deploy-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>Nasazení adaptéru HTTPS příchozího přenosu dat ve službě Azure Kubernetes Service (AKS)

Řadič služby příchozího přenosu dat je část softwaru, která poskytuje reverzní proxy server, směrování provozu konfigurovatelné a ukončení protokolu TLS pro služby Kubernetes. Prostředky Kubernetesu příchozího přenosu dat se používají ke konfiguraci pravidla příchozího přenosu dat a trasy pro jednotlivé služby Kubernetes. Použití kontroler příchozího přenosu dat a pravidla příchozího přenosu dat, jedna externí adresa je možné směrovat provoz do více služeb v clusteru Kubernetes.

V tomto článku se dozvíte, jak nasadit [kontroler příchozího přenosu dat NGINX] [ nginx-ingress] v clusteru služby Azure Kubernetes Service (AKS). [Cert správce] [ cert-manager] projektu slouží k automatickému generování a konfigurace [umožňuje šifrovat] [ lets-encrypt] certifikáty. Nakonec několik aplikací, které jsou spuštěny v clusteru AKS, z nichž každý je přístupný přes jednu adresu.

## <a name="before-you-begin"></a>Než začnete

Tento článek používá Helm k instalaci serveru NGINX kontroler příchozího přenosu dat, správce certifikátů a ukázkovou webovou aplikaci. Musíte mít Helm inicializován v rámci clusteru AKS a pomocí účtu služby pro Tiller. Ujistěte se, že používáte nejnovější verzi nástroje Helm. Upgrade instuctions, najdete v článku [Helm instalace dokumentace][helm-install]. Další informace o konfiguraci a použití Helm, naleznete v tématu [instalace aplikací s nástrojem Helm ve službě Azure Kubernetes Service (AKS)][use-helm].

Tento článek také vyžaduje, že používáte Azure CLI verze 2.0.41 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="install-an-ingress-controller"></a>Nainstalovat řadič příchozího přenosu dat

Pomocí helmu k instalaci serveru NGINX kontroleru příchozího přenosu. Podrobné informace o nasazení, najdete v článku [dokumentaci kontroleru příchozího přenosu dat NGINX][nginx-ingress].

Následující příklad nainstaluje řadič v `kube-system` oboru názvů. Můžete určit jiný obor názvů pro konkrétní prostředí. Pokud váš cluster AKS není povoleno RBAC, přidejte `--set rbac.create=false` k příkazu.

```console
helm install stable/nginx-ingress --namespace kube-system
```

Pro kontroler příchozího přenosu dat se vytvoří během instalace Azure veřejnou IP adresu. Chcete-li získat veřejnou IP adresu, použijte `kubectl get service` příkazu. Trvá několik minut, než IP adresu, která přiřazené příslušné službě.

```
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                       TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
eager-crab-nginx-ingress-controller        LoadBalancer   10.0.182.160   51.145.155.210  80:30920/TCP,443:30426/TCP   20m
eager-crab-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

Žádná pravidla příchozího přenosu dat dosud nebyly vytvořeny. Pokud přejdete na veřejnou IP adresu, kontroler příchozího přenosu dat NGINX výchozí 404 – Stránka se zobrazí, jak je znázorněno v následujícím příkladu:

![Výchozí server NGINX back-endu](media/ingress/default-back-end.png)

### <a name="use-an-existing-static-public-ip-address"></a>Použít existující statické veřejné IP adresy

V předchozím `helm install` krok, kontroler příchozího přenosu dat NGINX byla vytvořena s novou, dynamické veřejné IP adresy přiřazením. Běžné požadavky konfigurace je poskytnout existující *statické* veřejnou IP adresu. Tento přístup umožňuje použít existující záznamy DNS a konfigurace sítě konzistentním způsobem. Následující volitelné kroky je možné použít místo předchozí `helm install` příkaz, kde je dynamické veřejné IP adresy přiřazené vám.

Pokud je potřeba vytvořit statickou veřejnou IP adresu, nejdřív získejte název skupiny prostředků clusteru AKS pomocí [az aks zobrazit] [ az-aks-show] příkaz:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

Dále vytvořte veřejnou IP adresu pomocí *statické* pomocí metody přidělování [vytvořit az network public-ip] [ az-network-public-ip-create] příkazu. Následující příklad vytvoří veřejnou IP adresu s názvem *myAKSPublicIP* v AKS clusteru skupinu prostředků, kterou jste získali v předchozím kroku:

```azurecli
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static
```

Nyní nasaďte *nginx příchozího přenosu dat* grafu s nástrojem Helm. Přidat `--set controller.service.loadBalancerIP` parametr a zadejte vlastní veřejnou IP adresu vytvořenou v předchozím kroku:

```console
helm install stable/nginx-ingress --namespace kube-system --set controller.service.loadBalancerIP="40.121.63.72"
```

Když se pro kontroler příchozího přenosu dat NGINX Kubernetes služby Vyrovnávání zatížení, je přidělit statickou IP adresu, jak je znázorněno v následujícím příkladu výstupu:

```
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                        TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
dinky-panda-nginx-ingress-controller        LoadBalancer   10.0.232.56   40.121.63.72   80:31978/TCP,443:32037/TCP   3m
dinky-panda-nginx-ingress-default-backend   ClusterIP      10.0.95.248   <none>         80/TCP                       3m
```

Znovu žádná pravidla příchozího přenosu dat dosud nebyly vytvořeny, takže pokud přejdete na veřejnou IP adresu, zobrazí se stránka 404 výchozí kontroler příchozího přenosu dat serveru NGINX. Pravidla příchozího přenosu dat se konfigurují v následujících krocích.

## <a name="configure-a-dns-name"></a>Konfigurace názvu DNS

HTTPS certifikáty, které budou fungovat správně nakonfigurujte plně kvalifikovaný název domény pro IP adresu příchozího přenosu dat kontroleru. Tento skript aktualizace s IP adresou vašeho kontroler příchozího přenosu dat a jedinečný název, který chcete použít pro plně kvalifikovaný název:

```console
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
helm install stable/cert-manager --set ingressShim.defaultIssuerName=letsencrypt-staging --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Pokud váš cluster není povoleno RBAC, místo toho použijte následující příkaz:

```console
helm install stable/cert-manager \
  --set ingressShim.defaultIssuerName=letsencrypt-staging \
  --set ingressShim.defaultIssuerKind=ClusterIssuer \
  --set rbac.create=false \
  --set serviceAccount.create=false
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

## <a name="create-a-certificate-object"></a>Vytvořte objekt certifikátu

V dalším kroku musí být vytvořen prostředek certifikátu. Prostředek certifikátu definuje požadovaný certifikát X.509. Další informace najdete v tématu [cert správce certifikátů][cert-manager-certificates].

Vytvořit prostředek certifikátu, jako například `certificates.yaml`, s manifestem následující příklad. Aktualizace *dnsNames* a *domén* jako název DNS, kterou jste vytvořili v předchozím kroku.

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: tls-secret
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

Chcete-li vytvořit prostředek certifikátu, použijte `kubectl apply -f certificates.yaml` příkazu.

```
$ kubectl apply -f certificates.yaml

certificate.certmanager.k8s.io/tls-secret created
```

## <a name="run-demo-applications"></a>Spuštění ukázkové aplikace

Byly nakonfigurovány kontroler příchozího přenosu dat a řešení pro správu certifikátů. Teď Pojďme spuštění dvě ukázkové aplikace ve vašem clusteru AKS. V tomto příkladu Helm umožňuje nasadit dvě instance aplikace jednoduchý "Hello world".

Před instalací ukázkové grafy Helm, přidejte úložiště ukázky v Azure následujícím způsobem pro vaše prostředí helmu:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Vytvoření první ukázkové aplikace z grafu helmu pomocí následujícího příkazu:

```console
helm install azure-samples/aks-helloworld
```

Nyní instalaci druhé instance ukázkové aplikace. Pro druhou instanci zadejte nový název tak, že dvě aplikace jsou vizuálně distinct. Můžete také zadat jedinečný název služby:

```console
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Vytvoření trasy příchozího přenosu dat

Obě aplikace běží v clusteru Kubernetes, ale jsou nakonfigurované s využitím služby typu `ClusterIP`. Aplikace v důsledku toho nejsou přístupné z Internetu. Aby se daly veřejně k dispozici, vytvořte prostředek příchozího přenosu dat Kubernetes. Prostředek příchozího přenosu dat nakonfiguruje pravidla, která směrovat provoz mezi těmito dvěma aplikacemi.

V následujícím příkladu, provoz na adresu `https://demo-aks-ingress.eastus.cloudapp.azure.com/` se směruje na službu s názvem `aks-helloworld`. Provoz na adresu `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` směrován `ingress-demo` služby. Aktualizace *hostitele* a *hostitele* jako název DNS, kterou jste vytvořili v předchozím kroku.

Vytvořte soubor s názvem `hello-world-ingress.yaml` a kopie v následujícím příkladu YAML:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
    http:
      paths:
      - path: /
        backend:
          serviceName: aks-helloworld
          servicePort: 80
      - path: /hello-world-two
        backend:
          serviceName: ingress-demo
          servicePort: 80
```

Vytvoření prostředků pomocí příchozího přenosu dat `kubectl apply -f hello-world-ingress.yaml` příkazu.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>Otestujte konfiguraci příchozího přenosu dat

Otevřete webový prohlížeč na plně kvalifikovaný název domény řadiče příchozího přenosu dat Kubernetes, jako například *https://demo-aks-ingress.eastus.cloudapp.azure.com*.

Jak používat tyto příklady `letsencrypt-staging`, vystavený certifikát SSL není důvěryhodný v prohlížeči. Potvrďte výzvu s upozorněním a pokračujte v aplikaci. Informace o certifikátu se zobrazí *phishing LE zprostředkující X1* certifikát vystavil umožňuje šifrovat. Určuje tento falešný certifikát `cert-manager` požadavek zpracoval správně a přijetí certifikátu od poskytovatele:

![Umožňuje šifrovat pracovní certifikátu](media/ingress/staging-certificate.png)

Pokud změníte, můžeme použít šifrování `prod` spíše než `staging`, důvěryhodný certifikát vydaný umožňuje šifrování se používá, jak je znázorněno v následujícím příkladu:

![Pojďme certifikátu šifrování](media/ingress/certificate.png)

Ukázkové aplikace se zobrazí ve webovém prohlížeči:

![Aplikace – příklad jednoho](media/ingress/app-one.png)

Nyní přidejte */hello-world-two* cesty na plně kvalifikovaný název domény, jako například *https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two*. Druhý ukázkové aplikace s vlastní název se zobrazí:

![Aplikace – příklad 2](media/ingress/app-two.png)

## <a name="next-steps"></a>Další postup

Tento článek zahrnuty některé externí součásti pro AKS. Další informace o těchto komponent, naleznete na následujících stránkách projektu:

- [Helm CLI][helm-cli]
- [Kontroler příchozího přenosu dat serveru NGINX][nginx-ingress]
- [Správce certifikátů][cert-manager]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm#install-helm-cli
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
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
