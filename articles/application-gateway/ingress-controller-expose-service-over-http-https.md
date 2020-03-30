---
title: Vystavit službu AKS přes HTTP nebo HTTPS pomocí aplikační brány
description: Tento článek obsahuje informace o tom, jak vystavit službu AKS přes HTTP nebo HTTPS pomocí aplikační brány.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: c664141a8c89ccbdf37bd3f9a19cfa659982a47d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795573"
---
# <a name="expose-an-aks-service-over-http-or-https-using-application-gateway"></a>Vystavit službu AKS přes HTTP nebo HTTPS pomocí aplikační brány 

Tyto kurzy pomáhají ilustrovat využití [prostředků Příchozí přenos dat Kubernetes](https://kubernetes.io/docs/concepts/services-networking/ingress/) k vystavení příkladu služby Kubernetes prostřednictvím brány aplikace [Azure](https://azure.microsoft.com/services/application-gateway/) přes HTTP nebo HTTPS.

## <a name="prerequisites"></a>Požadavky

- Nainstalovaný `ingress-azure` kormidelní kormidelník.
  - [**Nasazení zeleného pole**](ingress-controller-install-new.md): Pokud začínáte od začátku, přečtěte si tyto pokyny k instalaci, které popisují kroky k nasazení clusteru AKS s aplikační bránou a instalaci řadiče příchozího přenosu dat aplikační brány v clusteru AKS.
  - [**Brownfield Nasazení**](ingress-controller-install-existing.md): Pokud máte existující cluster AKS a aplikační bránu, naleznete v těchto pokynech k instalaci řadiče příchozího přenosu dat brány aplikace v clusteru AKS.
- Pokud chcete v této aplikaci používat protokol HTTPS, budete potřebovat certifikát x509 a jeho soukromý klíč.

## <a name="deploy-guestbook-application"></a>Nasazení `guestbook` aplikace

Aplikace knihy návštěv je kanonický Kubernetes aplikace, která skládá z front-endu webového uživatelského nastavení, back-endu a databáze Redis. Ve výchozím `guestbook` nastavení zveřejňuje jeho aplikace `frontend` prostřednictvím `80`služby s názvem na portu . Bez prostředku příchozího přenosu dat Kubernetes není služba přístupná mimo cluster AKS. Použijeme aplikaci a nastavení Ingress resources pro přístup k aplikaci prostřednictvím PROTOKOLU HTTP a HTTPS.

Podle následujících pokynů nasaďte aplikaci knihy návštěv.

1. Stáhnout `guestbook-all-in-one.yaml` [zde](https://raw.githubusercontent.com/kubernetes/examples/master/guestbook/all-in-one/guestbook-all-in-one.yaml)
1. Nasazení `guestbook-all-in-one.yaml` do clusteru AKS spuštěním

  ```bash
  kubectl apply -f guestbook-all-in-one.yaml
  ```

Nyní byla `guestbook` aplikace nasazena.

## <a name="expose-services-over-http"></a>Vystavit služby přes protokol HTTP

Aby bylo možné vystavit aplikace knihy návštěv, budeme používat následující ingress prostředek:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```

Tento příchozí přenos `frontend` dat zpřístupní službu `guestbook-all-in-one` nasazení jako výchozí back-end aplikační brány.

Uložte výše uvedený `ing-guestbook.yaml`prostředek příchozího přenosu dat jako .

1. Nasazení `ing-guestbook.yaml` spuštěním:

    ```bash
    kubectl apply -f ing-guestbook.yaml
    ```

1. Zkontrolujte, zda protokol řadiče příchozího přenosu dat pro stav nasazení.

Nyní `guestbook` by měla být k dispozici aplikace. Můžete to zkontrolovat na veřejné adrese aplikační brány.

## <a name="expose-services-over-https"></a>Vystavit služby přes protokol HTTPS

### <a name="without-specified-hostname"></a>Bez zadaného názvu hostitele

Bez zadání názvu hostitele bude služba knihy návštěv k dispozici na všech názvech hostitelů, které odkazují na aplikační bránu.

1. Před nasazením příchozího přenosu dat je třeba vytvořit tajný klíč kubernetes pro hostování certifikátu a soukromého klíče. Můžete vytvořit tajemství kubernetes spuštěním

    ```bash
    kubectl create secret tls <guestbook-secret-name> --key <path-to-key> --cert <path-to-cert>
    ```

1. Definujte následující příchozí přenos dat. V příchozím přenosu dat zadejte název `secretName` tajného klíče v části.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - secretName: <guestbook-secret-name>
      rules:
      - http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

    > [!NOTE] 
    > Nahraďte `<guestbook-secret-name>` ve výše uvedeném prostředku příchozího přenosu dat názvem tajného klíče. Uložte výše uvedený prostředek příchozího přenosu dat do názvu `ing-guestbook-tls.yaml`souboru .

1. Nasazení ing-guestbook-tls.yaml spuštěním

    ```bash
    kubectl apply -f ing-guestbook-tls.yaml
    ```

1. Zkontrolujte, zda protokol řadiče příchozího přenosu dat pro stav nasazení.

Nyní `guestbook` bude aplikace k dispozici jak na HTTP, tak na PROTOKOLU HTTPS.

### <a name="with-specified-hostname"></a>Se zadaným názvem hostitele

Můžete také zadat název hostitele na příchozím přenosu dat, aby multiplextní TLS konfigurace a služby.
Zadáním názvu hostitele bude služba knihy návštěv k dispozici pouze na určeném hostiteli.

1. Definujte následující příchozí přenos dat.
    V příchozím přenosu dat zadejte název `secretName` tajného klíče v `hosts` části a odpovídajícím způsobem nahraďte název hostitele v oddílu.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - hosts:
          - <guestbook.contoso.com>
          secretName: <guestbook-secret-name>
      rules:
      - host: <guestbook.contoso.com>
        http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

1. Nasazení `ing-guestbook-tls-sni.yaml` spuštěním

    ```bash
    kubectl apply -f ing-guestbook-tls-sni.yaml
    ```

1. Zkontrolujte, zda protokol řadiče příchozího přenosu dat pro stav nasazení.

Nyní `guestbook` bude aplikace k dispozici na HTTP i HTTPS`<guestbook.contoso.com>` pouze na určeném hostiteli ( v tomto příkladu).

## <a name="integrate-with-other-services"></a>Integrace s ostatními službami

Následující příchozí přenos dat vám umožní přidat další cesty do tohoto příchozího přenosu dat a přesměrovat tyto cesty na jiné služby:

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      rules:
      - http:
          paths:
          - path: </other/*>
            backend:
              serviceName: <other-service>
              servicePort: 80
          - backend:
              serviceName: frontend
              servicePort: 80
    ```
