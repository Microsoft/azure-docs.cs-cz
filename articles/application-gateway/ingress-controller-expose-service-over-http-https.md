---
title: Vystavení služby AKS přes HTTP nebo HTTPS pomocí Application Gateway
description: Tento článek poskytuje informace o tom, jak vystavit službu AKS přes protokol HTTP nebo HTTPS pomocí Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 3b816ddc0eccf8c406cfed37d6bfc594e27d3629
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85850351"
---
# <a name="expose-an-aks-service-over-http-or-https-using-application-gateway"></a>Vystavení služby AKS přes HTTP nebo HTTPS pomocí Application Gateway 

Tyto kurzy vám pomůžou znázornit využití [prostředků Kubernetes](https://kubernetes.io/docs/concepts/services-networking/ingress/) příchozího přenosu dat a vystavit tak ukázkovou službu Kubernetes prostřednictvím [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) přes HTTP nebo HTTPS.

## <a name="prerequisites"></a>Požadavky

- Byl nainstalován `ingress-azure` Helm graf.
  - [**Nasazení bezserverová**](ingress-controller-install-new.md): Pokud začínáte od začátku, přečtěte si tyto pokyny k instalaci, které popisují postup nasazení clusteru AKS pomocí Application Gateway a instalace řadiče pro příchozí bránu Application Gateway do clusteru AKS.
  - [**Nasazení brownfield**](ingress-controller-install-existing.md): Pokud máte existující cluster AKS a Application Gateway, přečtěte si tyto pokyny, jak nainstalovat řadič pro příchozí bránu Application Gateway do clusteru AKS.
- Pokud chcete v této aplikaci používat protokol HTTPS, budete potřebovat certifikát x509 a jeho privátní klíč.

## <a name="deploy-guestbook-application"></a>Nasadit `guestbook` aplikaci

Aplikace kniha návštěv je kanonická Kubernetes aplikace, která vytváří front-end webové uživatelské rozhraní, back-end a databázi Redis. Ve výchozím nastavení `guestbook` zpřístupňuje svou aplikaci prostřednictvím služby s názvem `frontend` na portu `80` . Bez Kubernetes prostředků příchozího přenosu dat není služba přístupná z vnějšku clusteru AKS. Pro přístup k aplikaci prostřednictvím protokolu HTTP a protokolu HTTPS použijeme prostředky příchozího přenosu dat a nastavení.

Pomocí níže uvedených pokynů Nasaďte aplikaci pro knihu návštěv.

1. Stáhnout `guestbook-all-in-one.yaml` odsud [here](https://raw.githubusercontent.com/kubernetes/examples/master/guestbook/all-in-one/guestbook-all-in-one.yaml)
1. Nasaďte `guestbook-all-in-one.yaml` do clusteru AKS spuštěním

  ```bash
  kubectl apply -f guestbook-all-in-one.yaml
  ```

Nyní byla `guestbook` aplikace nasazena.

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

Tato příchozí `frontend` služba zpřístupní službu `guestbook-all-in-one` nasazení jako výchozí back-end Application Gateway.

Uložte výše uvedený prostředek příchozího přenosu dat jako `ing-guestbook.yaml` .

1. Nasadit spuštěním `ing-guestbook.yaml` :

    ```bash
    kubectl apply -f ing-guestbook.yaml
    ```

1. Podívejte se do protokolu pro stav nasazení na řadiči příchozího přenosu dat.

Nyní `guestbook` by měla být aplikace k dispozici. Můžete to zjistit tak, že navštívíte veřejnou adresu Application Gateway.

## <a name="expose-services-over-https"></a>Vystavení služeb přes HTTPS

### <a name="without-specified-hostname"></a>Bez zadaného názvu hostitele

V případě, že není zadán název hostitele, bude služba knihy návštěv dostupná na všech názvech hostitelů odkazujících na službu Application Gateway.

1. Před nasazením příchozího přenosu dat je potřeba vytvořit tajný kód Kubernetes pro hostování certifikátu a privátního klíče. Tajný klíč Kubernetes můžete vytvořit spuštěním

    ```bash
    kubectl create secret tls <guestbook-secret-name> --key <path-to-key> --cert <path-to-cert>
    ```

1. Zadejte následující příchozí přenosy. V poli příchozího přenosu zadejte název tajného kódu v `secretName` části.

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
    > Nahraďte `<guestbook-secret-name>` ve výše uvedeném prostředku příchozího přenosu názvem vašeho tajného kódu. Uložte výše uvedený prostředek příchozího přenosu do názvu souboru `ing-guestbook-tls.yaml` .

1. Nasaďte-kniha-knihy – YAML spuštěním

    ```bash
    kubectl apply -f ing-guestbook-tls.yaml
    ```

1. Podívejte se do protokolu pro stav nasazení na řadiči příchozího přenosu dat.

`guestbook`Aplikace bude nyní k dispozici na protokolech HTTP i HTTPS.

### <a name="with-specified-hostname"></a>Se zadaným názvem hostitele

Můžete taky zadat název hostitele na vstupu pro multiplexování konfigurací a služeb TLS.
Zadáte-li název hostitele, bude služba v knize pro přístup k dispozici pouze na zadaném hostiteli.

1. Zadejte následující příchozí přenosy.
    V poli příchozího přenosu zadejte název tajného kódu v `secretName` oddílu a odpovídajícím způsobem nahraďte název hostitele v `hosts` oddílu.

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

1. Nasadit `ing-guestbook-tls-sni.yaml` spuštěním

    ```bash
    kubectl apply -f ing-guestbook-tls-sni.yaml
    ```

1. Podívejte se do protokolu pro stav nasazení na řadiči příchozího přenosu dat.

`guestbook`Aplikace bude teď k dispozici na http i HTTPS jenom na zadaném hostiteli ( `<guestbook.contoso.com>` v tomto příkladu).

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
