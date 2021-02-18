---
title: Azure Media Services schémat diagnostických protokolů – Azure
description: Tento článek ukazuje schémata Azure Media Servicesch diagnostických protokolů.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 8015f610dfd9404810a1fa19c951078ebb45ac4f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588491"
---
# <a name="diagnostic-logs-schemas"></a>Schémata diagnostických protokolů

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

[Azure monitor](../../azure-monitor/overview.md) vám umožní monitorovat metriky a diagnostické protokoly, které vám pomůžou pochopit, jak vaše aplikace provádí. Můžete monitorovat protokoly diagnostiky Media Services a vytvářet výstrahy a oznámení pro shromážděné metriky a protokoly. Můžete odeslat protokoly do [Azure Storage](https://azure.microsoft.com/services/storage/), streamovat je do [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)a exportovat je do [Log Analytics](https://azure.microsoft.com/services/log-analytics/)nebo použít služby třetích stran.

Podrobné informace najdete v tématu [Azure monitor metriky](../../azure-monitor/data-platform.md) a [Azure monitor diagnostických protokolů](../../azure-monitor/essentials/platform-logs-overview.md).

Tento článek popisuje Media Services schémat diagnostických protokolů.

## <a name="top-level-diagnostic-logs-schema"></a>Schéma diagnostických protokolů nejvyšší úrovně

Podrobný popis schématu diagnostických protokolů nejvyšší úrovně najdete v tématu [podporované služby, schémata a kategorie pro diagnostické protokoly Azure](../../azure-monitor/essentials/resource-logs-schema.md).

## <a name="key-delivery-log-schema"></a>Schéma protokolu doručení klíčů

### <a name="properties"></a>Vlastnosti

Tyto vlastnosti jsou specifické pro schéma protokolu doručení klíčů.

|Název|Description|
|---|---|
|keyId|ID požadovaného klíče|
|keyType|Může to být jedna z následujících hodnot: "Clear" (bez šifrování), "FairPlay", "PlayReady" nebo "Widevine".|
|policyName|Azure Resource Manager název zásady.|
|Typ TokenType|Typ tokenu.|
|statusMessage|Stavová zpráva|

### <a name="examples"></a>Příklady

Vlastnosti schématu žádostí o doručení klíčů

```json
{
    "time": "2019-01-11T17:59:10.4908614Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Succeeded",
    "resultSignature": "OK",
    "durationMs": 315,
    "identity": {
        "authorization": {
            "issuer": "http://testacs",
            "audience": "urn:test"
        },
        "claims": {
            "urn:microsoft:azure:mediaservices:contentkeyidentifier": "3321e646-78d0-4896-84ec-c7b98eddfca5",
            "iss": "http://testacs",
            "aud": "urn:test",
            "exp": "1547233138"
        }
    },
    "level": "Informational",
    "location": "uswestcentral",
    "properties": {
        "requestId": "b0243468-d8e5-4edf-a48b-d408e1661050",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "JWT",
        "statusMessage": "OK"
    }
} 
```

```json
 {
    "time": "2019-01-11T17:59:33.4676382Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Failed",
    "resultSignature": "Unauthorized",
    "durationMs": 2,
    "level": "Error",
    "location": "uswestcentral",
    "properties": {
        "requestId": "875af030-b77c-416b-b7e1-58f23ebec182",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "None",
        "statusMessage": "No token present in authorization header or URL."
    }
} 
```

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba od společnosti Google Inc. v souladu s podmínkami služby a zásadami ochrany osobních údajů Google, Inc.

## <a name="next-steps"></a>Další kroky

[Monitorovat Media Services metriky a diagnostické protokoly](media-services-metrics-diagnostic-logs.md)
