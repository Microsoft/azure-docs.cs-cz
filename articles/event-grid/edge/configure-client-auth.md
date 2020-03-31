---
title: Konfigurace ověřování příchozích hovorů klientem – Azure Event Grid IoT Edge | Dokumenty společnosti Microsoft
description: Konfigurace protokolů rozhraní API vystavených službou Event Grid na IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 3363db4557dd19e8d72747ccd62bb535abb7b1e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841787"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>Konfigurace ověřování příchozích volání klientem

Tato příručka uvádí příklady možných konfigurací ověřování klienta pro modul Event Grid. Modul Event Grid podporuje dva typy ověřování klienta:

* Na základě klíče sdíleného přístupového podpisu (SAS)
* Na základě certifikátu

Všechny možné konfigurace naleznete v příručce [zabezpečení a ověřování.](security-authentication.md)

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>Povolit ověřování klientů na základě certifikátu, žádné certifikáty podepsané svým držitelem

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=false"
  ]
}
 ```

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>Povolit ověřování klientů na základě certifikátu, povolit certifikáty podepsané svým držitelem

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
```

>[!NOTE]
>Nastavte vlastnost **inbound__clientAuth__clientCert__allowUnknownCA** na **hodnotu true** pouze v testovacích prostředích, protože obvykle můžete používat certifikáty podepsané svým držitelem. Pro produkční úlohy doporučujeme nastavit tuto vlastnost na **false** a certifikáty od certifikační autority (CA).

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>Povolení ověřování klienta založeného na certifikátech a klíčích sas

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=true",
    "inbound__clientAuth__sasKeys__key1=<some-secret1-here>",
    "inbound__clientAuth__sasKeys__key2=<some-secret2-here>",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Ověřování klientů na základě klíče SAS umožňuje hraničnímu modulu bez IoT pro správu a operace runtime za předpokladu, že porty rozhraní API jsou samozřejmě přístupné mimo síť IoT Edge.
