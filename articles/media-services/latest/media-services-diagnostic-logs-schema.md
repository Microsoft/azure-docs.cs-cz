---
title: Azure Media Services diagnostické protokoly schémata – Azure
description: Tento článek popisuje schémata diagnostické protokoly Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/20/2019
ms.author: juliako
ms.openlocfilehash: 394370738bc7996a221300540e68404986d91310
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60322246"
---
# <a name="diagnostic-logs-schemas"></a>Schémata diagnostických protokolů

[Azure Monitor](../../azure-monitor/overview.md) umožňuje monitorování metrik a diagnostické protokoly, které vám pomohou pochopit, jaký výkon vašich aplikací. Můžete sledovat diagnostické protokoly služby Media Services a vytvořte si upozornění a oznámení o shromážděných metrik a protokolů. Můžete odeslat protokoly do [služby Azure Storage](https://azure.microsoft.com/services/storage/), Streamovat je do [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)a exportovat je do [Log Analytics](https://azure.microsoft.com/services/log-analytics/), nebo použít služby 3. stran.

Podrobné informace najdete v tématu [metrik Azure monitoru](../../azure-monitor/platform/data-platform.md) a [Azure Monitor diagnostické protokoly](../../azure-monitor/platform/diagnostic-logs-overview.md).

Tento článek popisuje schémata pro diagnostické protokoly služby Media Services.

## <a name="top-level-diagnostic-logs-schema"></a>Schéma nejvyšší úrovně diagnostické protokoly

Podrobný popis schématu nejvyšší úrovně diagnostické protokoly najdete v tématu [podporované služby, schémat a kategorie pro diagnostické protokoly Azure](../../azure-monitor/platform/tutorial-dashboards.md).

## <a name="key-delivery-log-schema"></a>Schéma protokolu doručení klíče

### <a name="properties"></a>Vlastnosti

Tyto vlastnosti jsou specifické pro schéma doručení klíče protokolu.

|Name|Popis|
|---|---|
|keyId|ID požadovaný klíč.|
|keyType|Můžou mít jednu z následujících hodnot: "Clear" (bez šifrování), "FairPlay", "PlayReady" nebo "Widevine".|
|policyName|Azure Resource Manageru název zásady.|
|tokenType|Typ tokenu.|
|statusMessage|Stavová zpráva.|

### <a name="examples"></a>Příklady

Vlastnosti schématu žádosti o doručení klíče.

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

## <a name="next-steps"></a>Další postup

[Monitorování metrik služby Media Services a diagnostické protokoly](media-services-metrics-diagnostic-logs.md)
