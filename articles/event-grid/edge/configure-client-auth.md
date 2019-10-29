---
title: Konfigurovat ověřování klientů u příchozích volání – Azure Event Grid IoT Edge | Microsoft Docs
description: Nakonfigurujte protokoly rozhraní API, které jsou vystavené Event Grid v IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: b5456130e89bf77e2c2ba41880323e38f6b27f4c
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992506"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>Konfigurovat ověřování klientů u příchozích volání

Tato příručka obsahuje příklady možných konfigurací ověřování klientů pro modul Event Grid. Modul Event Grid podporuje dva typy ověřování klientů:-

* Založený na klíči SAS (Shared Access Signature)
* na základě certifikátu

V tématu Průvodce [zabezpečením a ověřováním](security-authentication.md) najdete všechny možné konfigurace.

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>Povolit ověřování klientů založených na certifikátech, žádné certifikáty podepsané svým držitelem

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=false"
  ]
}
 ```

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>Povolit ověřování klientů na základě certifikátů, povolit certifikáty podepsané svým držitelem

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true"
  ]
}
```

>[!NOTE]
>Nastavte vlastnost **příchozí: clientAuth: clientCert: allowUnknownCA** na **hodnotu true** pouze v testovacích prostředích, protože obvykle můžete používat certifikáty podepsané svým držitelem. Pro produkční úlohy doporučujeme tuto vlastnost nastavit na **hodnotu false** a certifikáty od certifikační autority (CA).

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>Povolit ověřování klientů založených na certifikátech a SAS – klíč

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=true",
    "inbound:clientAuth:sasKeys:key1=<some-secret1-here>",
    "inbound:clientAuth:sasKeys:key2=<some-secret2-here>",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Ověřování klienta na základě klíčů SAS umožňuje, aby modul pro správu a provoz v prostředí neiot Edge probíhají operace správy a běhu za předpokladu, že porty rozhraní API jsou přístupné mimo IoT Edge síť.
