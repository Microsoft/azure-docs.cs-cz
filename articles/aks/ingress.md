---
title: Nakonfigurujte příchozí s clusterem Azure Kubernetes služby (AKS)
description: Nainstalujte a nakonfigurujte řadič NGINX příjem příchozích dat v clusteru služby Azure Kubernetes služby (AKS).
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/25/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: fcf0b6f3b7f6d75006d8c10aab041c25fc0d8c39
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751281"
---
# <a name="https-ingress-on-azure-kubernetes-service-aks"></a>Příchozí přenos HTTPS pro službu Azure Kubernetes (AKS)

Řadič příjem příchozích dat je softwarového produktu, které poskytuje reverzní proxy server, směrování provozu konfigurovatelné a ukončení TLS pro Kubernetes služby. Prostředky Kubernetes příjem příchozích dat se používají ke konfiguraci směrování pro jednotlivé služby Kubernetes a příchozího pravidla. Pomocí řadič příjem příchozích dat a příchozího pravidla, jedna externí adresa slouží ke směrování provozu na více služeb v clusteru s podporou Kubernetes.

Tento dokument vás provede ukázkové nasazení [NGINX příjem příchozích dat řadič] [ nginx-ingress] v clusteru služby Azure Kubernetes služby (AKS). Kromě toho [cert manager] [ cert-manager] projektu se používá k automatickému generování a konfigurace [umožňuje šifrování] [ lets-encrypt] certifikáty. Nakonec několik aplikací se spouštějí v clusteru AKS, z nichž každý je přístupný prostřednictvím jednu adresu.

## <a name="install-an-ingress-controller"></a>Nainstalovat řadič příjem příchozích dat

K instalaci řadičem NGINX příjem příchozích dat použijte Helm. Viz řadič příjem příchozích dat NGINX [dokumentace] [ nginx-ingress] nasazení podrobné informace.

Tento příklad nainstaluje řadič v `kube-system` obor názvů, lze upravit k oboru názvů podle svého výběru. Pokud váš cluster AKS není RBAC povoleno, přidejte `--set rbac.create=false` k příkazu. Další informace najdete v tématu [nginx příjem příchozích dat grafu][nginx-ingress].

```bash
helm install stable/nginx-ingress --namespace kube-system
```

Během instalace se vytvoří Azure veřejnou IP adresu pro příjem příchozích dat řadič. Chcete-li získat veřejnou IP adresu, použijte příkaz kubectl get service. Může trvat nějakou dobu IP adresu pro přiřazení ke službě.

```console
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                       TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
eager-crab-nginx-ingress-controller        LoadBalancer   10.0.182.160   51.145.155.210  80:30920/TCP,443:30426/TCP   20m
eager-crab-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

Protože žádná pravidla příjem příchozích dat byly vytvořeny, pokud přejdete na veřejnou IP adresu, jsou směrovány na NGINX příjem příchozích dat řadiče výchozí 404 stránku.

![Výchozí NGINX back-end](media/ingress/default-back-end.png)

## <a name="configure-dns-name"></a>Konfigurace názvu DNS

Protože se používají certifikáty protokolu HTTPS, musíte nakonfigurovat název plně kvalifikovaný název domény pro příjem příchozích dat řadiče IP adresu. V tomto příkladu se vytvoří plně kvalifikovaný název domény Azure pomocí Azure CLI. Aktualizujte skript s IP adresou řadiče pro příjem příchozích dat a název, který chcete používat ve plně kvalifikovaný název domény.

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

Příjem příchozích dat řadiče by teď měly být přístupné prostřednictvím plně kvalifikovaný název domény.

## <a name="install-cert-manager"></a>Instalace certifikátu Správce

Příjem příchozích dat řadičem NGINX podporuje TLS ukončení. Existuje několik způsobů, jak načíst a nakonfigurovat certifikáty pro protokol HTTPS, tento dokument ukazuje, jak pomocí [cert manager][cert-manager], který poskytuje automatické [umožňuje šifrování] [ lets-encrypt] funkce generování a správu certifikátů.

Instalace certifikátu správce řadiče, následujícím příkazem Helm instalace.

```bash
helm install stable/cert-manager --set ingressShim.defaultIssuerName=letsencrypt-prod --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Pokud váš cluster není RBAC povolit, použijte tento příkaz.

```bash
helm install stable/cert-manager \
  --set ingressShim.defaultIssuerName=letsencrypt-prod \
  --set ingressShim.defaultIssuerKind=ClusterIssuer \
  --set rbac.create=false \
  --set serviceAccount.create=false
```

Další informace o konfiguraci správce certifikátu, najdete v článku [cert manager projektu][cert-manager].

## <a name="create-ca-cluster-issuer"></a>Vytvoření clusteru vystavitele certifikační Autority

Před vydáním certifikátů, správce certifikátů vyžaduje [vystavitele] [ cert-manager-issuer] nebo [ClusterIssuer] [ cert-manager-cluster-issuer] prostředků. Prostředky jsou identické ve funkcích ale `Issuer` funguje v jeden obor názvů kde `ClusterIssuer` funguje napříč všech oborů názvů. Další informace najdete v tématu [vystavitele certifikátu manager] [ cert-manager-issuer] dokumentaci.

Vytvořte pomocí následujících manifestu clusteru vystavitele. Aktualizujte e-mailová adresa platnou adresou z vaší organizace.

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

Dále musí být vytvořený prostředek certifikátu. Prostředek certifikátu definuje požadovaný certifikát X.509. Další informace najdete v tématu, [cert správce certifikátů][cert-manager-certificates].

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

V tomto okamžiku byly nakonfigurovány řadič příjem příchozích dat a řešení pro správu certifikát. Nyní můžete spusťte několik aplikací v clusteru AKS.

V tomto příkladu Helm slouží ke spouštění více instancí aplikace jednoduché hello world.

Než spustíte aplikaci, přidejte úložiště Helm ukázek Azure ve vývojovém systému.

```bash
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Spusťte grafu AKS hello world pomocí následujícího příkazu:

```bash
helm install azure-samples/aks-helloworld
```

Nyní instalaci druhé instance aplikace hello world.

Pro druhou instanci zadejte nový název tak, aby dvě možná využití jsou vizuálně jedinečné. Budete taky muset zadat jedinečný název služby. Tyto konfigurace můžete vidět v následujícím příkazu.

```bash
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-ingress-route"></a>Vytvoření příchozího směrování

Obě aplikace je nyní spuštěna v clusteru Kubernetes, ale jsou nakonfigurované s službu typu `ClusterIP`. Aplikace jako takový nejsou přístupné z Internetu. Aby bylo možné k dispozici, vytvořte prostředek příjem příchozích dat Kubernetes. Vstupní prostředek nakonfiguruje pravidla, která směrovat provoz mezi těmito dvěma aplikacemi.

Vytvořte název souboru `hello-world-ingress.yaml` a zkopírujte následující YAML.

Všimněte si, že provoz na adresu `https://demo-aks-ingress.eastus.cloudapp.azure.com/` se směruje na služby s názvem `aks-helloworld`. Provoz na adresu `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` se směruje na `ingress-demo` služby.

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

Vytvoření vstupní zdroj s `kubectl apply` příkaz.

```console
kubectl apply -f hello-world-ingress.yaml
```

## <a name="test-the-ingress-configuration"></a>Otestujte konfiguraci příjem příchozích dat

Přejděte do plně kvalifikovaný název domény řadiči Kubernetes příjem příchozích dat, měli byste vidět aplikace hello world.

![Aplikace – příklad jednoho](media/ingress/app-one.png)

Nyní přejděte na plně kvalifikovaný název domény řadiče příjem příchozích dat s `/hello-world-two` cestu, měli byste vidět aplikace hello world pomocí vlastní název.

![Aplikace – příklad dvou](media/ingress/app-two.png)

Všimněte si také, že připojení je šifrovaná a že se používá certifikát vydaný umožňuje šifrování.

![Umožňuje šifrování certifikátu](media/ingress/certificate.png)

## <a name="next-steps"></a>Další postup

Další informace o softwaru ukázáno v tomto dokumentu.

- [Helm rozhraní příkazového řádku][helm-cli]
- [Řadič NGINX příjem příchozích dat][nginx-ingress]
- [Správce certifikátů][cert-manager]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm#install-helm-cli
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
