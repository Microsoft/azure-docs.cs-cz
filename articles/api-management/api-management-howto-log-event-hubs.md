---
title: Jak protokolovat události do Azure Event Hubs v Azure API Management | Microsoft Docs
description: Naučte se protokolovat události do Azure Event Hubs v Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/29/2018
ms.author: apimpm
ms.openlocfilehash: 646d9206ec82d5f35ccab9365e76276ff779d225
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073483"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Jak protokolovat události do Azure Event Hubs v Azure API Management
Vysoce škálovatelná služba Azure Event Hubs slouží ke zpracování příchozích dat. Dokáže přijímat miliony událostí za sekundu a umožňuje zpracovávat a analyzovat masivní objemy dat vytvářených zařízeními a aplikacemi připojenými k vaší síti. Event Hubs slouží jako "přední dveře" pro kanál událostí a jakmile se data shromažďují do centra událostí, je možné je transformovat a ukládat pomocí libovolného zprostředkovatele analýz v reálném čase nebo adaptérů pro dávkování/ukládání. Event Hubs oddělí vytvoření proudu událostí od spotřeby těchto události, aby spotřebitelé událostí mohli k událostem přistupovat podle svého vlastního plánu.

V tomto článku se dozvíte, jak [integrovat API Management Azure s Event Hubsm](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) videem, a popisuje, jak protokolovat API Management události pomocí Event Hubs Azure.

## <a name="create-an-azure-event-hub"></a>Vytvoření centra událostí Azure

Podrobné informace o tom, jak vytvořit centrum událostí a jak získat připojovací řetězce, které potřebujete k odesílání a příjmu událostí do a z centra událostí, najdete v tématu [vytvoření Event Hubs oboru názvů a centra událostí pomocí Azure Portal](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-api-management-logger"></a>Vytvoření protokolovacího nástroje API Management
Teď, když máte centrum událostí, je dalším krokem konfigurace protokolovacího nástroje ve [](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) službě API Management, aby mohla protokolovat události do centra událostí.

API Management protokolovacích nástrojů se konfigurují pomocí [REST API API Management](https://aka.ms/smapi). Než poprvé použijete REST API, přečtěte si [požadavky](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest) a ujistěte se, že máte [povolený přístup k REST API](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#EnableRESTAPI).

Pokud chcete vytvořit protokolovací nástroj, vytvořte požadavek HTTP PUT pomocí následující šablony URL:

`https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2017-03-01`

* Nahraďte `{your service}` názvem vaší instance služby API Management.
* Nahraďte `{new logger name}` požadovaným názvem pro nový protokolovací nástroj. Tento název se odkazuje při konfiguraci zásad [protokolu na eventhub](/azure/api-management/api-management-advanced-policies#log-to-eventhub) .

Do žádosti přidejte následující hlavičky:

* Content-Type: Application/JSON
* Udělován SharedAccessSignature 58...
  * Pokyny k vygenerování `SharedAccessSignature` najdete v tématu [ověřování Azure API Management REST API](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication).

Text žádosti určete pomocí následující šablony:

```json
{
  "loggerType" : "AzureEventHub",
  "description" : "Sample logger description",
  "credentials" : {
    "name" : "Name of the Event Hub from the portal",
    "connectionString" : "Endpoint=Event Hub Sender connection string"
    }
}
```

* `loggerType`musí být nastaven na `AzureEventHub`hodnotu.
* `description`poskytuje volitelný popis protokolovacího nástroje a v případě potřeby může být řetězec s nulovou délkou.
* `credentials``name` obsahuje a `connectionString` v centru událostí Azure.

Při vytvoření tohoto požadavku se v případě, že je vytvořen protokolovací nástroj, vrátí stavový kód `201 Created` . Ukázková odpověď založená na výše uvedené žádosti o ukázku je uvedená níže.

```json
{
    "id": "/loggers/{new logger name}",
    "loggerType": "azureEventHub",
    "description": "Sample logger description",
    "credentials": {
        "name": "Name of the Event Hub from the Portal",
        "connectionString": "{{Logger-Credentials-xxxxxxxxxxxxxxx}}"
    },
    "isBuffered": true,
    "resourceId": null
}
```

> [!NOTE]
> Další možné návratové kódy a jejich důvody najdete v tématu [Vytvoření protokolovacího](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity#PUT)nástroje. Informace o tom, jak provádět jiné operace, jako je například seznam, aktualizace a odstranění, [](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) najdete v dokumentaci k entitě protokolovacího nástroje.
>
>

## <a name="configure-log-to-eventhubs-policies"></a>Konfigurace zásad přihlášení k eventhubs

Po nakonfigurování protokolovacího nástroje v API Management můžete nakonfigurovat zásady přihlášení k eventhubs, které budou protokolovat požadované události. Zásady log-to-eventhubs se dají použít v oddílu příchozí zásady nebo v části odchozí zásady.

1. Přejděte k vaší instanci APIM.
2. Vyberte kartu rozhraní API.
3. Vyberte rozhraní API, ke kterému chcete zásadu přidat. V tomto příkladu přidáme do neomezeného produktu zásadu pro **rozhraní API echo** .
4. Vyberte **Všechny operace**.
5. V horní části obrazovky vyberte kartu Návrh.
6. V okně příchozí nebo odchozí zpracování klikněte na trojúhelník (vedle tužky).
7. Vyberte Editor kódu. Další informace najdete v tématu [jak nastavit nebo upravit zásady](set-edit-policies.md).
8. Umístěte kurzor do `inbound` části zásady nebo `outbound` .
9. V okně na pravé straně vyberte Upřesnit protokol **zásad** > **do centra EventHub**. Tím se vloží `log-to-eventhub` šablona prohlášení o zásadách.

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```
Nahraďte `logger-id` hodnotou, kterou jste `{new logger name}` použili v adrese URL k vytvoření protokolovacího nástroje v předchozím kroku.

Můžete použít libovolný výraz, který vrátí řetězec jako hodnotu `log-to-eventhub` prvku. V tomto příkladu se protokoluje řetězec obsahující datum a čas, název služby, ID žádosti, IP adresu žádosti a název operace.

Kliknutím na **Uložit** uložte aktualizovanou konfiguraci zásad. Jakmile je zásada uložena, je zásada aktivní a události budou protokolovány do určeného centra událostí.

## <a name="next-steps"></a>Další postup
* Další informace o Azure Event Hubs
  * [Začínáme s Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Přijímání zpráv pomocí EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Průvodce programováním pro službu Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* Další informace o integraci API Management a Event Hubs
  * [Reference k entitě protokolovacího nástroje](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [odkaz na zásady přihlášení k protokolu eventhub](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
  * [Monitorování rozhraní API pomocí API Management Azure, Event Hubs a Moesif](api-management-log-to-eventhub-sample.md)  
* Další informace o [integraci s Azure Application Insights](api-management-howto-app-insights.md)

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
