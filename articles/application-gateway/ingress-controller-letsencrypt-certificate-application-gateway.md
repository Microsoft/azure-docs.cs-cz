---
title: Použití LetsEncrypt.org certifikátů s aplikační bránou
description: Tento článek obsahuje informace o tom, jak získat certifikát od LetsEncrypt.org a použít jej v aplikační bráně pro clustery AKS.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 92e9747865f1a0910c8bae4001cc597ae9ea3da6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73957974"
---
# <a name="use-certificates-with-letsencryptorg-on-application-gateway-for-aks-clusters"></a>Použití certifikátů s LetsEncrypt.org v aplikační bráně pro clustery AKS

Tato část konfiguruje akvatenční systém tak, aby využíval [LetsEncrypt.org](https://letsencrypt.org/) a automaticky získal certifikát TLS/SSL pro vaši doménu. Certifikát bude nainstalován v application gateway, která bude provádět ukončení SSL/TLS pro váš cluster AKS. Zde popsané nastavení používá doplněk [cert-manager](https://github.com/jetstack/cert-manager) Kubernetes, který automatizuje vytváření a správu certifikátů.

Podle následujících kroků nainstalujte [správce certifikátů](https://docs.cert-manager.io) do stávajícího clusteru AKS.

1. Graf skory

    Chcete-li nainstalovat `cert-manager` kormidelní kormidelní kormidelní kormidelní kormidelní kormi Tímto:

    - vytvoření nového `cert-manager` oboru názvů ve službě AKS
    - vytvořit následující crds: certifikát, výzva, clusterissuer, vydavatel, pořadí
    - instalace grafu správce certifikátu (od [docs.cert-manager.io)](https://docs.cert-manager.io/en/latest/getting-started/install/kubernetes.html#steps)

    ```bash
    #!/bin/bash

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

2. Prostředek clusteru

    Vytvořte `ClusterIssuer` zdroj. Vyžaduje `cert-manager` to, aby `Lets Encrypt` certifikační úřad zastupoval, kde budou podepsané certifikáty získány.

    Pomocí prostředku bez oboru `ClusterIssuer` názvů bude správce certifikátů vydávat certifikáty, které lze spotřebovávat z více oborů názvů. `Let’s Encrypt`Používá protokol ACME k ověření, zda ovládáte daný název domény, a k vydání certifikátu. Další podrobnosti `ClusterIssuer` o konfiguraci vlastností [zde](https://docs.cert-manager.io/en/latest/tasks/issuers/index.html). `ClusterIssuer`vydá `cert-manager` certifikáty pomocí pracovního prostředí používaného `Lets Encrypt` pro testování (kořenový certifikát není k dispozici v úložištích důvěryhodnosti prohlížeče/klienta).

    Výchozí typ výzvy v níže `http01`uvedeném yaml je . Další výzvy jsou popsány na [letsencrypt.org - Typy výzev](https://letsencrypt.org/docs/challenge-types/)

    > [!IMPORTANT] 
    > Aktualizace `<YOUR.EMAIL@ADDRESS>` v YAML níže

    ```bash
    #!/bin/bash
    kubectl apply -f - <<EOF
    apiVersion: certmanager.k8s.io/v1alpha1
    kind: ClusterIssuer
    metadata:
    name: letsencrypt-staging
    spec:
    acme:
        # You must replace this email address with your own.
        # Let's Encrypt will use this to contact you about expiring
        # certificates, and issues related to your account.
        email: <YOUR.EMAIL@ADDRESS>
        # ACME server URL for Let’s Encrypt’s staging environment.
        # The staging environment will not issue trusted certificates but is
        # used to ensure that the verification process is working properly
        # before moving to production
        server: https://acme-staging-v02.api.letsencrypt.org/directory
        privateKeySecretRef:
        # Secret resource used to store the account's private key.
        name: example-issuer-account-key
        # Enable the HTTP-01 challenge provider
        # you prove ownership of a domain by ensuring that a particular
        # file is present at the domain
        http01: {}
    EOF
    ```

3. Nasazení aplikace

    Vytvořte prostředek příchozího `guestbook` přenosu dat k vystavení aplikace pomocí aplikační brány s certifikátem Umožňuje šifrovat.

    Ujistěte se, že aplikační brána má veřejnou front-endovou konfiguraci IP s názvem DNS (buď pomocí výchozí `azure.com` domény, nebo zřizování `Azure DNS Zone` služby a přiřaďte vlastní doménu).
    Všimněte si `certmanager.k8s.io/cluster-issuer: letsencrypt-staging`poznámky , která říká správci certifikátů, aby zpracoval prostředek označeného příchozího přenosu dat.

    > [!IMPORTANT] 
    > Aktualizujte `<PLACEHOLDERS.COM>` v yaml níže s vlastní doménou (nebo aplikační bránou, například "kh-aks-ingress.westeurope.cloudapp.azure.com")

    ```bash
    kubectl apply -f - <<EOF
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
    name: guestbook-letsencrypt-staging
    annotations:
        kubernetes.io/ingress.class: azure/application-gateway
        certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    spec:
    tls:
    - hosts:
        - <PLACEHOLDERS.COM>
        secretName: guestbook-secret-name
    rules:
    - host: <PLACEHOLDERS.COM>
        http:
        paths:
        - backend:
            serviceName: frontend
            servicePort: 80
    EOF
    ```

    Po několika sekundách můžete `guestbook` ke službě přistupovat prostřednictvím adresy URL HTTPS aplikační brány pomocí automaticky vydaného **pracovního** `Lets Encrypt` certifikátu.
    Váš prohlížeč vás může varovat před neplatným orgánem certifikátu. Pracovní certifikát je vydán `CN=Fake LE Intermediate X1`společností . To to znamená, že systém fungoval podle očekávání a jste připraveni na produkční certifikát.

4. Výrobní certifikát

    Po úspěšném nastavení pracovního certifikátu můžete přepnout na produkční server ACME:
    1. Nahraďte pracovní poznámku v prostředku Příchozí přenos dat:`certmanager.k8s.io/cluster-issuer: letsencrypt-prod`
    1. Odstraňte existující `ClusterIssuer` pracovní vytvořenou v předchozím kroku a vytvořte nový nahrazením serveru ACME z výše uvedeného serveru YAML clusterissueru`https://acme-v02.api.letsencrypt.org/directory`

5. Vypršení platnosti a obnovení certifikátu

    Před `Lets Encrypt` vypršením platnosti certifikátu `cert-manager` se certifikát automaticky aktualizuje v tajném úložišti Kubernetes. V tomto okamžiku ingress řadič aplikační brány použije aktualizovaný tajný klíč odkazovaný v ingress prostředky, které používá ke konfiguraci aplikační brány.
