---
title: Konfigurace příchozí přenos dat pomocí clusteru Azure Kubernetes Service (AKS)
description: Nainstalujte a nakonfigurujte kontroler příchozího přenosu dat serveru NGINX v clusteru služby Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/25/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: bd223e9eebac495d7336c618b831528505c30959
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2018
ms.locfileid: "37857391"
---
# <a name="https-ingress-on-azure-kubernetes-service-aks"></a>HTTPS příchozího přenosu dat ve službě Azure Kubernetes Service (AKS)

Řadič služby příchozího přenosu dat je část softwaru, která poskytuje reverzní proxy server, směrování provozu konfigurovatelné a ukončení protokolu TLS pro služby Kubernetes. Prostředky Kubernetesu příchozího přenosu dat se používají ke konfiguraci pravidla příchozího přenosu dat a trasy pro jednotlivé služby Kubernetes. Použití kontroler příchozího přenosu dat a pravidla příchozího přenosu dat, jedna externí adresa je možné směrovat provoz do více služeb v clusteru Kubernetes.

Tento dokument vás provede nasazení ukázky [kontroler příchozího přenosu dat NGINX] [ nginx-ingress] v clusteru služby Azure Kubernetes Service (AKS). Kromě toho [cert správce] [ cert-manager] projektu slouží k automatickému generování a konfigurace [umožňuje šifrovat] [ lets-encrypt] certifikáty. Nakonec několik aplikací, které jsou spuštěny v clusteru AKS, z nichž každý je přístupný přes jednu adresu.

## <a name="install-an-ingress-controller"></a>Nainstalovat řadič příchozího přenosu dat

Pomocí helmu k instalaci serveru NGINX kontroleru příchozího přenosu. Zobrazit kontroler příchozího přenosu dat NGINX [dokumentaci] [ nginx-ingress] nasazení podrobné informace.

Tento příklad nainstaluje řadič v `kube-system` obor názvů, to lze upravit na obor názvů podle vašeho výběru. Pokud váš cluster AKS není povoleno RBAC, přidejte `--set rbac.create=false` k příkazu. Další informace najdete v tématu [nginx příchozího přenosu dat grafu][nginx-ingress].

```bash
helm install stable/nginx-ingress --namespace kube-system
```

Pro kontroler příchozího přenosu dat se vytvoří během instalace Azure veřejnou IP adresu. K získání veřejné IP adresy, pomocí příkazu kubectl get service. Může trvat nějakou dobu IP adresu, která přiřazené příslušné službě.

```console
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                       TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
eager-crab-nginx-ingress-controller        LoadBalancer   10.0.182.160   51.145.155.210  80:30920/TCP,443:30426/TCP   20m
eager-crab-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

Vzhledem k tomu, že žádná pravidla příchozího přenosu dat byly vytvořeny, pokud přejdete na veřejnou IP adresu, jsou směrovány na stránku serveru NGINX příchozího přenosu dat řadiče výchozí 404.

![Výchozí server NGINX back-endu](media/ingress/default-back-end.png)

## <a name="configure-dns-name"></a>Konfigurace názvu DNS

Protože se používají certifikáty protokolu HTTPS, musíte nakonfigurovat plně kvalifikovaný název domény pro IP adresu příchozího přenosu dat řadiče. V tomto příkladu je plně kvalifikovaný název domény Azure vytvořené pomocí Azure CLI. Aktualizujte skript s IP adresou, kontroler příchozího přenosu dat a název, který chcete použít v plně kvalifikovaný název.

```bash
#!/bin/bash

# Public IP address
IP="51.145.155.210"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with dns name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
```

Kontroler příchozího přenosu dat by měl nyní být přístupné přes plně kvalifikovaný název.

## <a name="install-cert-manager"></a>Instalace správce certifikátů

Kontroler příchozího přenosu dat NGINX podporuje ukončení protokolu TLS. Přestože existuje několik způsobů, jak načíst a nakonfigurovat certifikáty pro protokol HTTPS, tento dokument ukazuje, jak pomocí [cert správce][cert-manager], která nabízí automatické [umožňuje šifrovat] [ lets-encrypt] certifikátu generování a funkcí pro správu.

Instalace certifikátu správce řadiče, použijte následující příkaz Helm install.

```bash
helm install stable/cert-manager --set ingressShim.defaultIssuerName=letsencrypt-prod --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Pokud váš cluster není povoleno RBAC, použijte tento příkaz.

```bash
helm install stable/cert-manager \
  --set ingressShim.defaultIssuerName=letsencrypt-prod \
  --set ingressShim.defaultIssuerKind=ClusterIssuer \
  --set rbac.create=false \
  --set serviceAccount.create=false
```

Další informace o konfiguraci správce certifikátů, najdete v článku [cert správce projektu][cert-manager].

## <a name="create-ca-cluster-issuer"></a>Vytvoření clusteru vystavitele certifikační Autority

Před vydáním certifikátů, vyžaduje správce certifikátů [vystavitele] [ cert-manager-issuer] nebo [ClusterIssuer] [ cert-manager-cluster-issuer] prostředků. Prostředky jsou ale stejné funkcí `Issuer` funguje v jednoho oboru názvů kde `ClusterIssuer` funguje ve všech oborů názvů. Další informace najdete v tématu [vystavitele certifikátu správce] [ cert-manager-issuer] dokumentaci.

Vytvoření clusteru vystavitele pomocí následující manifestu. Aktualizujte e-mailovou adresu platnou adresu z vaší organizace.

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-prod
    http01: {}
```

## <a name="create-certificate-object"></a>Vytvořit objekt certifikátu

V dalším kroku musí být vytvořen prostředek certifikátu. Prostředek certifikátu definuje požadovaný certifikát X.509. Další informace najdete v tématu, [cert správce certifikátů][cert-manager-certificates].

Vytvořte prostředek certifikátu s následující manifestu.

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
    name: letsencrypt-prod
    kind: ClusterIssuer
```

## <a name="run-application"></a>Spuštění aplikace

V tomto okamžiku byly nakonfigurovány kontroler příchozího přenosu dat a řešení pro správu certifikátů. Nyní můžete spusťte několik aplikací ve vašem clusteru AKS.

V tomto příkladu Helm slouží ke spouštění více instancí aplikace jednoduchý hello world.

Před spuštěním aplikace, přidejte do úložiště helmu ukázky v Azure pro váš vývojový systém.

```bash
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Spuštění grafu AKS hello world pomocí následujícího příkazu:

```bash
helm install azure-samples/aks-helloworld
```

Nyní instalaci druhé instance aplikace hello world.

Pro druhou instanci zadejte nový název tak, že dvě aplikace jsou vizuálně distinct. Také musíte zadat jedinečný název služby. Tyto konfigurace můžete vidět v následujícím příkazu.

```bash
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-ingress-route"></a>Vytvoření směrování příchozího přenosu dat

Obě aplikace jsou teď běží na clusteru Kubernetes, ale nepřidáte službu typu `ClusterIP`. Aplikace v důsledku toho nejsou přístupné z Internetu. Aby bylo možné je k dispozici, vytvořte prostředek příchozího přenosu dat Kubernetes. Prostředek příchozího přenosu dat nakonfiguruje pravidla, která směrovat provoz mezi těmito dvěma aplikacemi.

Vytvořte soubor `hello-world-ingress.yaml` a zkopírujte do následující kód YAML.

Všimněte si, že provoz na adresu `https://demo-aks-ingress.eastus.cloudapp.azure.com/` se směruje na službu s názvem `aks-helloworld`. Provoz na adresu `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` směrován `ingress-demo` služby.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-prod
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

Vytvořit prostředek příchozího přenosu dat se `kubectl apply` příkazu.

```console
kubectl apply -f hello-world-ingress.yaml
```

## <a name="test-the-ingress-configuration"></a>Otestujte konfiguraci příchozího přenosu dat

Přejděte na plně kvalifikovaný název domény řadiče příchozího přenosu dat Kubernetes, měli byste vidět aplikace hello world.

![Aplikace – příklad jednoho](media/ingress/app-one.png)

Teď přejděte na plně kvalifikovaný název domény kontroler příchozího přenosu dat se `/hello-world-two` cestu, měli byste vidět aplikace hello world pomocí vlastní název.

![Aplikace – příklad 2](media/ingress/app-two.png)

Všimněte si také, že připojení je šifrovaný a použití certifikátu vydaného umožňuje šifrovat.

![Umožňuje zašifrovat certifikátu](media/ingress/certificate.png)

## <a name="next-steps"></a>Další postup

Další informace o softwaru v tomto dokumentu jsme vám ukázali.

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
