---
title: Vystavení služby AKS přes HTTP nebo HTTPS pomocí Application Gateway
description: Tento článek poskytuje informace o tom, jak vystavit službu AKS přes protokol HTTP nebo HTTPS pomocí Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 10/22/2019
ms.author: caya
ms.openlocfilehash: b04c381cbe5ad9199f182f39e7311ae01fe2b8ba
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513611"
---
# <a name="expose-an-aks-service-over-http-or-https-using-application-gateway"></a>Vystavení služby AKS přes HTTP nebo HTTPS pomocí Application Gateway 

Tyto kurzy vám pomůžou znázornit využití [prostředků Kubernetes](https://kubernetes.io/docs/concepts/services-networking/ingress/) příchozího přenosu dat a vystavit tak ukázkovou službu Kubernetes prostřednictvím [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) přes HTTP nebo HTTPS.

## <a name="prerequisites"></a>Předpoklady

- Nainstalovaná `ingress-azure` Helm graf.
  - [**Nasazení bezserverová**](ingress-controller-install-new.md): Pokud začínáte od začátku, přečtěte si tyto pokyny k instalaci, které popisují postup nasazení clusteru AKS pomocí Application Gateway a instalace řadiče pro příchozí bránu Application Gateway do clusteru AKS.
  - [**Nasazení brownfield**](ingress-controller-install-existing.md): Pokud máte existující cluster AKS a Application Gateway, přečtěte si tyto pokyny, jak nainstalovat řadič pro příchozí bránu Application Gateway do clusteru AKS.
- Pokud chcete v této aplikaci používat protokol HTTPS, budete potřebovat certifikát x509 a jeho privátní klíč.

## <a name="deploy-guestbook-application"></a>Nasazení aplikace `guestbook`

Aplikace kniha návštěv je kanonická Kubernetes aplikace, která vytváří front-end webové uživatelské rozhraní, back-end a databázi Redis. Ve výchozím nastavení `guestbook` zveřejňuje svou aplikaci prostřednictvím služby s názvem `frontend` na `80`portu. Bez Kubernetes prostředků příchozího přenosu dat není služba přístupná z vnějšku clusteru AKS. Pro přístup k aplikaci prostřednictvím protokolu HTTP a protokolu HTTPS použijeme prostředky příchozího přenosu dat a nastavení.

Pomocí níže uvedených pokynů Nasaďte aplikaci pro knihu návštěv.

1. Stáhnout [`guestbook-all-in-one.yaml` odsud](https://raw.githubusercontent.com/kubernetes/examples/master/guestbook/all-in-one/guestbook-all-in-one.yaml)
1. Nasaďte `guestbook-all-in-one.yaml` do clusteru AKS spuštěním

  ```bash
  kubectl apply -f guestbook-all-in-one.yaml
  ```

Nyní byla aplikace `guestbook` nasazena.

## <a name="expose-services-over-http"></a>Vystavení služeb přes protokol HTTP

K zpřístupnění aplikace pro knihu návštěv budeme používat následující prostředek příchozího přenosu dat:

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

Tato příchozí akce zpřístupní službu `frontend` nasazení `guestbook-all-in-one` jako výchozí back-end Application Gateway.

Uložte výše uvedený prostředek příchozího přenosu dat jako `ing-guestbook.yaml`.

1. Nasaďte `ing-guestbook.yaml` spuštěním:

    ```bash
    kubectl apply -f ing-guestbook.yaml
    ```

1. Podívejte se do protokolu pro stav nasazení na řadiči příchozího přenosu dat.

Nyní by měla být aplikace `guestbook` k dispozici. Můžete to zjistit tak, že navštívíte veřejnou adresu Application Gateway.

## <a name="expose-services-over-https"></a>Vystavení služeb přes HTTPS

### <a name="without-specified-hostname"></a>Bez zadaného názvu hostitele

V případě, že není zadán název hostitele, bude služba knihy návštěv dostupná na všech názvech hostitelů odkazujících na službu Application Gateway.

1. Před nasazením příchozího přenosu dat je potřeba vytvořit tajný kód Kubernetes pro hostování certifikátu a privátního klíče. Tajný klíč Kubernetes můžete vytvořit spuštěním

    ```bash
    kubectl create secret tls <guestbook-secret-name> --key <path-to-key> --cert <path-to-cert>
    ```

1. Zadejte následující příchozí přenosy. Do pole příchozí přenos dat zadejte název tajného kódu v části `secretName`.

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
    > Ve výše uvedeném prostředku příchozího přenosu dat nahraďte `<guestbook-secret-name>` názvem svého tajného klíče. Uložte výše uvedený prostředek příchozího přenosu do souboru s názvem `ing-guestbook-tls.yaml`.

1. Nasaďte-kniha-knihy – YAML spuštěním

    ```bash
    kubectl apply -f ing-guestbook-tls.yaml
    ```

1. Podívejte se do protokolu pro stav nasazení na řadiči příchozího přenosu dat.

Nyní bude aplikace `guestbook` k dispozici na protokolech HTTP i HTTPS.

### <a name="with-specified-hostname"></a>Se zadaným názvem hostitele

Můžete taky zadat název hostitele na vstupu pro multiplexování konfigurací a služeb TLS.
Zadáte-li název hostitele, bude služba v knize pro přístup k dispozici pouze na zadaném hostiteli.

1. Zadejte následující příchozí přenosy.
    V poli příchozího přenosu zadejte název tajného kódu do oddílu `secretName` a podle toho v `hosts` oddílu nahraďte název hostitele.

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

1. Podívejte se do protokolu pro stav nasazení na řadiči příchozího přenosu dat.

Nyní bude aplikace `guestbook` k dispozici pouze na HTTP i HTTPS v zadaném hostiteli (`<guestbook.contoso.com>` v tomto příkladu).

## <a name="integrate-with-other-services"></a>Integrace s ostatními službami

Následující příchozí přenos dat vám umožní přidat do tohoto příchozího přenosu další cesty a přesměrovat tyto cesty na jiné služby:

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
