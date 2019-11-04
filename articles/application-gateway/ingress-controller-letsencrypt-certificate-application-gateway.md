---
title: Použití LetsEncrypt.org certifikátů s Application Gateway
description: Tento článek poskytuje informace o tom, jak získat certifikát z LetsEncrypt.org a použít ho na Application Gateway pro clustery AKS.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 10/23/2019
ms.author: caya
ms.openlocfilehash: f582a9ae2bfcec90b35e0827d2d75e00bb7cae9c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513468"
---
# <a name="use-certificates-with-letsencryptorg-on-application-gateway-for-aks-clusters"></a>Použití certifikátů s LetsEncrypt.org na Application Gateway pro clustery AKS

V této části se nakonfigurují vaše AKSy pro využití [LetsEncrypt.org](https://letsencrypt.org/) a k automatickému získání certifikátu TLS/SSL pro vaši doménu. Certifikát se nainstaluje na Application Gateway, což provede ukončení protokolu SSL/TLS pro váš cluster AKS. Zde popsaná instalace používá doplněk [CERT Manager](https://github.com/jetstack/cert-manager) Kubernetes, který automatizuje vytváření a správu certifikátů.

Použijte následující postup a nainstalujte si [Správce certifikátů](https://docs.cert-manager.io) v existujícím clusteru AKS.

1. Graf Helm

    Spuštěním následujícího skriptu nainstalujte graf `cert-manager` Helm. Tato akce:

    - vytvořit nový obor názvů `cert-manager` v AKS
    - Vytvořte následující CRDs: Certificate, Challenge, ClusterIssuer, Issuer, Order.
    - instalace grafu CERT Manageru (z [docs.CERT-Manager.IO)](https://docs.cert-manager.io/en/latest/getting-started/install/kubernetes.html#steps)

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

2. Prostředek ClusterIssuer

    Vytvořte prostředek `ClusterIssuer`. `cert-manager` se vyžaduje pro reprezentaci `Lets Encrypt` certifikační autority, kde se budou podepsané certifikáty získat.

    Pomocí `ClusterIssuer` prostředku, který není oborem názvů, vygeneruje správce certifikátů certifikáty, které je možné spotřebovat z více oborů názvů. `Let’s Encrypt` používá ACME protokol k ověření, že ovládáte daný název domény a chcete vydat certifikát. Další podrobnosti o konfiguraci `ClusterIssuer` vlastností [najdete tady](https://docs.cert-manager.io/en/latest/tasks/issuers/index.html). `ClusterIssuer` vydá pokyn `cert-manager` k vystavování certifikátů pomocí `Lets Encrypt` přípravného prostředí, které se používá pro testování (kořenový certifikát, který není přítomen v úložištích prohlížeče/klientů).

    Výchozí typ výzvy v YAML níže je `http01`. Další výzvy jsou zdokumentovány na [typech letsencrypt.org-Challenge](https://letsencrypt.org/docs/challenge-types/)

    > [!IMPORTANT] 
    > Aktualizovat `<YOUR.EMAIL@ADDRESS>` v YAML níže

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

    Vytvořte prostředek příchozího přenosu dat, který vystaví `guestbook` aplikaci pomocí Application Gateway s nástrojem umožňuje šifrovat certifikát.

    Ujistěte se, že Application Gateway má veřejnou front-end IP adresu s názvem DNS (buď pomocí výchozí `azure.com` domény, nebo zřídí službu `Azure DNS Zone` a přiřadíte vlastní doménu).
    Poznamenejte si `certmanager.k8s.io/cluster-issuer: letsencrypt-staging`anotace, která instruuje správce certifikátů, aby zpracoval tento označený prostředek příchozího přenosu dat.

    > [!IMPORTANT] 
    > Aktualizujte `<PLACEHOLDERS.COM>` v YAML níže s vaší vlastní doménou (nebo Application Gateway One, například "kh-aks-ingress.westeurope.cloudapp.azure.com").

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

    Po několika sekundách můžete ke službě `guestbook` přistupovat přes Application Gateway adresu URL HTTPS pomocí automatického vystavení `Lets Encrypt`ho certifikátu pro **přípravu** .
    Prohlížeč vás může upozorňovat na neplatnou certifikační autoritu. Pracovní certifikát je vydaný pomocí `CN=Fake LE Intermediate X1`. Označuje, že systém fungoval podle očekávání a že jste připravení na produkční certifikát.

4. Provozní certifikát: po úspěšném dokončení instalace se můžete přepnout na produkční server, který je k disfázi:
    1. Nahraďte pracovní anotaci prostředku příchozího přenosu pomocí: `certmanager.k8s.io/cluster-issuer: letsencrypt-prod`
    1. Odstraňte stávající pracovní `ClusterIssuer`, které jste vytvořili v předchozím kroku, a vytvořte nový, a to tak, že nahradíte ACME Server z YAML ClusterIssuer výše pomocí `https://acme-v02.api.letsencrypt.org/directory`

5. Vypršení platnosti certifikátu a obnovení před vypršením platnosti certifikátu `Lets Encrypt` `cert-manager` automaticky aktualizuje certifikát v úložišti tajného kódu Kubernetes. V tomto okamžiku Application Gateway řadič příchozího přenosu dat použije aktualizovaný tajný klíč odkazovaný v prostředcích příchozího přenosu dat, který používá ke konfiguraci Application Gateway.
