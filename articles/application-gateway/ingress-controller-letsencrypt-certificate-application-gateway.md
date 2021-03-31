---
title: Použití LetsEncrypt.org certifikátů s Application Gateway
description: Tento článek poskytuje informace o tom, jak získat certifikát z LetsEncrypt.org a použít ho na Application Gateway pro clustery AKS.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: df8722e8160538daa1535711092790dbb2405097
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "84807038"
---
# <a name="use-certificates-with-letsencryptorg-on-application-gateway-for-aks-clusters"></a>Použití certifikátů s LetsEncrypt.org na Application Gateway pro clustery AKS

V této části se nakonfigurují vaše AKSy pro využití [LetsEncrypt.org](https://letsencrypt.org/) a k automatickému získání certifikátu TLS/SSL pro vaši doménu. Certifikát se nainstaluje na Application Gateway, což provede ukončení protokolu SSL/TLS pro váš cluster AKS. Zde popsaná instalace používá doplněk [CERT Manager](https://github.com/jetstack/cert-manager) Kubernetes, který automatizuje vytváření a správu certifikátů.

Použijte následující postup a nainstalujte si [Správce certifikátů](https://docs.cert-manager.io) v existujícím clusteru AKS.

1. Graf Helm

    Spusťte následující skript pro instalaci `cert-manager` grafu Helm. Tato akce:

    - Vytvoření nového `cert-manager` oboru názvů na AKS
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

    Vytvořte `ClusterIssuer` prostředek. To vyžaduje, `cert-manager` aby reprezentuje `Lets Encrypt` certifikační autoritu, ve které se budou nacházet podepsané certifikáty.

    Pomocí prostředku certifikátů bez oboru názvů `ClusterIssuer` vystaví správce certifikátů certifikáty, které lze spotřebovat z více oborů názvů. `Let’s Encrypt` pomocí ACME protokolu ověří, zda máte pod kontrolou daný název domény a vystavení certifikátu. Další podrobnosti o konfiguraci `ClusterIssuer` vlastností [najdete tady](https://docs.cert-manager.io/en/latest/tasks/issuers/index.html). `ClusterIssuer` vyzve `cert-manager` k vystavování certifikátů pomocí `Lets Encrypt` přípravného prostředí, které se používá pro testování (kořenový certifikát, který není přítomen v úložištích prohlížeče nebo klientů).

    Výchozí typ výzvy v YAML níže je `http01` . Další výzvy jsou zdokumentovány na [typech letsencrypt.org-Challenge](https://letsencrypt.org/docs/challenge-types/)

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

    Vytvořte prostředek příchozího přenosu dat, který vystaví `guestbook` aplikaci pomocí Application Gateway s nástrojem umožňuje šifrovat certifikát.

    Ujistěte se, že Application Gateway má veřejnou front-end IP adresu s názvem DNS (buď pomocí výchozí `azure.com` domény, nebo zřídí `Azure DNS Zone` službu a přiřadíte vlastní doménu).
    Poznamenejte si anotaci `certmanager.k8s.io/cluster-issuer: letsencrypt-staging` , která instruuje správce certifikátů, aby zpracoval tento označený prostředek příchozího přenosu dat.

    > [!IMPORTANT] 
    > Aktualizujte `<PLACEHOLDERS.COM>` v YAML níže s vaší vlastní doménou (nebo Application Gateway One, například "KH-AKS-Ingress.westeurope.cloudapp.Azure.com").

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

    Po několika sekundách získáte přístup ke `guestbook` službě prostřednictvím adresy url Application Gateway HTTPS pomocí automaticky vydaného **přípravného** `Lets Encrypt` certifikátu.
    Prohlížeč vás může upozorňovat na neplatnou certifikační autoritu. Pracovní certifikát vystavil `CN=Fake LE Intermediate X1` . Označuje, že systém fungoval podle očekávání a že jste připravení na produkční certifikát.

4. Provozní certifikát

    Po úspěšném dokončení instalace můžete přepnout na produkční server, který je k instalaci:
    1. Nahraďte pracovní poznámku u svého prostředku příchozího přenosu pomocí: `certmanager.k8s.io/cluster-issuer: letsencrypt-prod`
    1. Odstraňte stávající pracovní `ClusterIssuer` postup, který jste vytvořili v předchozím kroku, a vytvořte nový tak, že nahradíte Acme Server z CLUSTERISSUER YAML výše. `https://acme-v02.api.letsencrypt.org/directory`

5. Vypršení platnosti a obnovení certifikátu

    Před `Lets Encrypt` vypršením platnosti certifikátu `cert-manager` bude certifikát automaticky aktualizovat v úložišti tajného kódu Kubernetes. V tomto okamžiku Application Gateway řadič příchozího přenosu dat použije aktualizovaný tajný klíč odkazovaný v prostředcích příchozího přenosu dat, který používá ke konfiguraci Application Gateway.
