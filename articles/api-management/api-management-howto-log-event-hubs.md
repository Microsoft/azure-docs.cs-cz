---
title: Jak protokolování událostí ve službě Azure Event Hubs ve službě Azure API Management | Dokumentace Microsoftu
description: Zjistěte, jak protokolování událostí ve službě Azure Event Hubs ve službě Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: apimpm
ms.openlocfilehash: 496928697af069f773e47974129bb7d3de3e1cbc
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37886967"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Jak protokolování událostí ve službě Azure Event Hubs ve službě Azure API Management
Vysoce škálovatelná služba Azure Event Hubs slouží ke zpracování příchozích dat. Dokáže přijímat miliony událostí za sekundu a umožňuje zpracovávat a analyzovat masivní objemy dat vytvářených zařízeními a aplikacemi připojenými k vaší síti. Služba Event Hubs slouží jako "předních dveří" pro kanál událostí, a jakmile jsou data shromážděna do centra událostí, je možné transformovat a uložit pomocí libovolného zprostředkovatele datové analýzy v reálném čase nebo adaptérů dávkování/úložišť. Event Hubs oddělí vytvoření proudu událostí od spotřeby těchto události, aby spotřebitelé událostí mohli k událostem přistupovat podle svého vlastního plánu.

Tento článek, který je k [integrovat Azure API managementu s využitím služby Event Hubs](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) videa a popisuje, jak protokolovat události správy rozhraní API pomocí služby Azure Event Hubs.

## <a name="create-an-azure-event-hub"></a>Vytvoření centra událostí Azure

Podrobné pokyny o tom, jak vytvořit Centrum událostí a připojovací řetězce, které potřebujete k odesílání a příjmu událostí z centra událostí a získání najdete v tématu [vytvořit obor názvů služby Event Hubs a centra událostí pomocí webu Azure portal](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-api-management-logger"></a>Vytvoření API Management protokolovací nástroj
Teď, když máte Centrum událostí, dalším krokem je konfigurace [protokolovací nástroj](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) ve službě API Management service tak, aby mohl zaprotokolovat události do centra událostí.

Protokolovací nástroje API Management se konfiguruje pomocí [REST API služby API Management](http://aka.ms/smapi). Před prvním použitím rozhraní REST API, projděte si [požadavky](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#Prerequisites) a ujistěte se, že máte [povolený přístup k rozhraní REST API](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#EnableRESTAPI).

Pokud chcete vytvořit protokolovací nástroj, ujistěte se, požadavek HTTP PUT pomocí následující šablony adresy URL:

`https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2017-03-01`

* Nahraďte `{your service}` s názvem vaší instance služby API Management.
* Nahraďte `{new logger name}` s požadovaný název pro nový protokolovací nástroj. Při konfiguraci se odkazovat tento název [protokolu eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub) zásad

Přidejte následující hlavičky požadavku:

* Typ obsahu: application/json
* Autorizace: SharedAccessSignature 58...
  * Pokyny pro generování `SharedAccessSignature` naleznete v tématu [Azure API Management REST API ověřování](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication).

Určení těla požadavku pomocí následující šablony:

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

* `loggerType` musí být nastaveno na `AzureEventHub`.
* `description` poskytuje volitelný popis protokolovacího nástroje a může být řetězec nulové délky v případě potřeby.
* `credentials` obsahuje `name` a `connectionString` vaše Centrum událostí Azure.

Když nastavíte požadavku, pokud protokolovací nástroj se vytvoří, stavový kód `201 Created` je vrácena. Ukázková odpověď na základě výše uvedené ukázkové žádosti je uveden níže.

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
> Další možné návratové kódy a důvodech najdete v tématu [vytvořit protokolovací nástroj](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity#PUT). Jak provádět další operace, jako je například seznam, aktualizace a odstranění najdete v tématu [protokolovací nástroj](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) dokumentace entity.
>
>

## <a name="configure-log-to-eventhubs-policies"></a>Konfigurace zásad protokolu eventhubs

Jakmile vaše protokolovacího nástroje je nakonfigurovaná ve službě API Management, můžete nakonfigurovat zásady protokolu eventhubs protokolovat požadované události. Zásady protokolu eventhubs je možné v části příchozí zásady nebo části outbound zásady.

1. Přejděte k vaší instanci APIM.
2. Vyberte kartu rozhraní API.
3. Vyberte rozhraní API, ke kterému chcete přidat zásady. V tomto příkladu přidáváme zásadu, která **rozhraní Echo API** v **Unlimited** produktu.
4. Vyberte **Všechny operace**.
5. Horní části obrazovky vyberte kartu Návrh.
6. V okně zpracování příchozí nebo odchozí klikněte na trojúhelník (vedle tužky).
7. Vyberte editor kódu. Další informace najdete v tématu [postupy nastavení nebo úprava zásad](set-edit-policies.md).
8. Umístěte kurzor do `inbound` nebo `outbound` části zásady.
9. V okně na pravé straně vyberte **pokročilé zásady** > **protokolu k centru EventHub**. Vloží `log-to-eventhub` příkaz šablonu zásad.

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```
Nahraďte `logger-id` s hodnotou, které jste použili pro `{new logger name}` v adrese URL pro vytváření protokolovacího nástroje v předchozím kroku.

Můžete použít libovolný výraz, který vrátí řetězec jako hodnotu `log-to-eventhub` elementu. V tomto příkladu je zaznamenána řetězec obsahující datum a čas, název služby, id požadavku, požadavek ip adresu a název operace.

Klikněte na tlačítko **Uložit** uložíte aktualizované zásady. Jakmile je uložen je zásada aktivní a události se protokolují do určeným centra událostí.

## <a name="next-steps"></a>Další postup
* Další informace o Azure Event Hubs
  * [Začínáme s Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Příjem zpráv pomocí třídy EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Průvodce programováním pro službu Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* Další informace o integraci API Management a služby Event Hubs
  * [Odkaz na entitu protokolovací nástroj](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [Referenční příručce o zásadách protokolu do centra událostí](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
  * [Sledování vašich rozhraní API pomocí Azure API Management, Event Hubs a Runscope](api-management-log-to-eventhub-sample.md)  
* Další informace o [integrace s Azure Application Insights](api-management-howto-app-insights.md)

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
