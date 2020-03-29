---
title: Konfigurace ověřování odběratele webhooku – Azure Event Grid IoT Edge | Dokumenty společnosti Microsoft
description: Konfigurace ověřování odběratelů webhooků
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 101dcae5870322878cec48098f2efae32cc68c14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841726"
---
# <a name="configure-webhook-subscriber-authentication"></a>Konfigurace ověřování odběratelů webhooků

Tato příručka uvádí příklady možných konfigurací předplatitele webhooku pro modul Event Grid. Ve výchozím nastavení jsou pro předplatitele webhooku přijímány pouze koncové body HTTPS. Modul Event Grid odmítne, pokud odběratel předloží certifikát podepsaný svým držitelem.

## <a name="allow-only-https-subscriber"></a>Povolit pouze odběratele protokolu HTTPS

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>Povolit předplatiteli PROTOKOLU HTTPS s certifikátem podepsaným svým držitelem

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Nastavte vlastnost `outbound__webhook__allowUnknownCA` `true` pouze v testovacích prostředích, protože obvykle můžete používat certifikáty podepsané svým držitelem. Pro produkční úlohy doporučujeme, aby byly nastaveny na **false**.

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>Povolit odběratele PROTOKOLU HTTPS, ale přeskočit ověření certifikátu

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=true",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

>[!NOTE]
>Nastavte vlastnost `outbound__webhook__skipServerCertValidation` `true` pouze v testovacích prostředích, protože pravděpodobně nepředstavujete certifikát, který je třeba ověřit. Pro produkční úlohy doporučujeme, aby byly nastaveny na **false**

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>Povolit protokol HTTP i protokol HTTPS s certifikáty podepsanými svým držitelem

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=false",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Nastavte vlastnost `outbound__webhook__httpsOnly` `false` pouze v testovacích prostředích, jak můžete chtít vyvolat odběratelhttp jako první. Pro produkční úlohy doporučujeme, aby byly nastaveny na **hodnotu true**
