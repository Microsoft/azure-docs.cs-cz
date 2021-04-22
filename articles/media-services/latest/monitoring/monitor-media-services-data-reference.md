---
title: Referenční informace o Media Services dat monitorování
description: Důležité referenční materiály, které jsou potřeba při monitorování Media Services
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femila
ms.topic: reference
ms.service: media-services
ms.custom: subject-monitoring
ms.date: 04/21/2021
ms.openlocfilehash: 3fd7b8013ec67d718f308ccd1b72a6f90012e02e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873050"
---
# <a name="monitoring-media-services-data-reference"></a>Referenční informace o Media Services dat monitorování

Tento článek se zabývá daty, která jsou užitečná pro monitorování Media Services. Další informace o všech metrikách platforem podporovaných v Azure Monitor najdete v [Azure monitor podporované metriky](../../../azure-monitor/essentials/metrics-supported.md).

## <a name="metrics"></a>Metriky

Metriky se shromažďují v pravidelných intervalech bez ohledu na to, jestli se hodnota mění. Jsou užitečné pro upozorňování, protože je možné je vzorkovat často a výstraha se dá rychle aktivovat s relativně jednoduchou logikou.


Media Services podporuje monitorování metrik pro následující prostředky:

|Typ metriky | Obor názvů poskytovatele prostředků/typu<br/> a propojit s jednotlivými metrikami |
|-------|-----|
| Media Services obecné | [Obecné](/azure/azure-monitor/essentials/metrics-supported#microsoftmediamediaservices) |
| Živé události | [Microsoft. Media/MediaServices/liveEvents](/azure/azure-monitor/essentials/metrics-supported#microsoftmediamediaservicesliveevents) 
| Koncové body streamování | [Microsoft. Media/MediaServices/starají](/azure/azure-monitor/essentials/metrics-supported#microsoftmediamediaservicesstreamingendpoints), které jsou relevantní pro [koncové body streamování REST API](/rest/api/media/streamingendpoints). 


Měli byste taky zkontrolovat [kvóty a omezení účtu](../limits-quotas-constraints-reference.md).


## <a name="metric-dimensions"></a>Dimenze metriky

Další informace o tom, jaké dimenze metriky jsou, najdete v tématu multidimenzionální [metriky](../../../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics).

Služba Media Services má následující dimenze metriky.  Jsou explantory na základě metrik, které podporují.  Další informace najdete v odkazech na výše uvedené [metriky](#metrics) .   
- OutputFormat
- HttpStatusCode 
- ErrorCode 
- Stop 

## <a name="resource-logs"></a>Protokoly prostředků

Protokoly prostředků poskytují bohatou a častou data o provozu prostředku Azure. Další informace najdete v tématu [Jak shromažďovat a využívat data protokolu z vašich prostředků Azure](../../../azure-monitor/essentials/platform-logs-overview.md).

Media Services podporuje následující protokoly zdrojů: [Microsoft. Media/MediaServices](/azure/azure-monitor/essentials/resource-logs-categories#microsoftmediamediaservices)

## <a name="schemas"></a>Schémata

Podrobný popis schématu diagnostických protokolů nejvyšší úrovně najdete v tématu [podporované služby, schémata a kategorie pro diagnostické protokoly Azure](../../../azure-monitor/essentials/resource-logs-schema.md).

## <a name="key-delivery-log-schema-properties"></a>Vlastnosti schématu protokolu doručení klíčů

Tyto vlastnosti jsou specifické pro schéma protokolu doručení klíčů.

|Název|Description|
|---|---|
|keyId|ID požadovaného klíče|
|keyType|Může to být jedna z následujících hodnot: "Clear" (bez šifrování), "FairPlay", "PlayReady" nebo "Widevine".|
|policyName|Azure Resource Manager název zásady.|
|Typ TokenType|Typ tokenu.|
|statusMessage|Stavová zpráva|

### <a name="example"></a>Příklad

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

>[!NOTE]
> Widevine je služba od společnosti Google Inc. v souladu s podmínkami služby a zásadami ochrany osobních údajů Google, Inc.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [monitoring-next-steps](../includes/monitoring-next-steps.md)]
