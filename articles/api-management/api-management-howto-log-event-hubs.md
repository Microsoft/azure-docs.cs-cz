---
title: "Jak zapisovat do protokolu událostí Azure Event Hubs ve službě Azure API Management | Microsoft Docs"
description: "Zjistěte, jak do protokolu událostí Azure Event Hubs ve službě Azure API Management."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: apimpm
ms.openlocfilehash: 3f4da70d94d28496f5b08035ead0ef7acf1ca3bc
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Jak zapisovat do protokolu událostí Azure Event Hubs ve službě Azure API Management
Vysoce škálovatelná služba Azure Event Hubs slouží ke zpracování příchozích dat. Dokáže přijímat miliony událostí za sekundu a umožňuje zpracovávat a analyzovat masivní objemy dat vytvářených zařízeními a aplikacemi připojenými k vaší síti. Služba Event Hubs slouží jako "přední dveře" pro kanál událostí, a jakmile jsou data shromážděna do centra událostí, lze je transformovat a uložené pomocí kteréhokoli poskytovatele služeb, analýzu v reálném čase nebo adaptérů pro dávkování či ukládání. Event Hubs oddělí vytvoření proudu událostí od spotřeby těchto události, aby spotřebitelé událostí mohli k událostem přistupovat podle svého vlastního plánu.

Tento článek je Pomocníka pro [integrovat Azure API Management službou Event Hubs](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) video a popisuje, jak do protokolu událostí správy rozhraní API pomocí Azure Event Hubs.

## <a name="create-an-azure-event-hub"></a>Vytvoření centra událostí Azure

Podrobné pokyny o tom, jak vytváření centra událostí a získat připojovací řetězce, které potřebujete k odesílání a příjmu událostí do a z centra událostí najdete v tématu [vytvořit obor názvů Event Hubs a centra událostí pomocí portálu Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-api-management-logger"></a>Vytvoření protokolovač API Management
Teď, když máte centra událostí, dalším krokem je konfigurace [Protokolovač](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) ve službě API Management služby tak, aby mohl zaprotokolovat události do centra událostí.

Protokolovací nástroje API Management jsou konfigurováni pomocí [rozhraní API REST API správy](http://aka.ms/smapi). Před použitím rozhraní REST API poprvé, přečtěte si [požadavky](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#Prerequisites) a ujistěte se, že máte [povolen přístup k rozhraní REST API](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#EnableRESTAPI).

Pokud chcete vytvořit protokoly, ujistěte se, požadavek HTTP PUT pomocí následující šablony adresy URL:

`https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2017-03-01`

* Nahraďte `{your service}` s názvem vaší instance služby API Management.
* Nahraďte `{new logger name}` s požadovaným názvem pro vaše nové protokolovacího nástroje. Při konfiguraci odkazujete tento název [protokolu eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub) zásad

Přidejte následující hlavičky žádosti:

* Content-Type: application/json
* Autorizace: SharedAccessSignature 58...
  * Pokyny pro generování `SharedAccessSignature` najdete v části [Azure API Management REST API ověřování](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication).

Zadejte text žádosti pomocí následující šablony:

```json
{
  "loggerType" : "AzureEventHub",
  "description" : "Sample logger description",
  "credentials" : {
    "name" : "Name of the Event Hub from the Azure Classic Portal",
    "connectionString" : "Endpoint=Event Hub Sender connection string"
    }
}
```

* `loggerType` musí být nastavena na `AzureEventHub`.
* `description` poskytuje volitelný popis protokolovacího nástroje a v případě potřeby může být řetězec nulové délky.
* `credentials` obsahuje `name` a `connectionString` centra událostí Azure.

Pokud vytvoříte požadavek, je-li protokolovač vytvořena stavový kód `201 Created` je vrácen.

> [!NOTE]
> Ostatní možné návratové kódy a jejich důvodů najdete v tématu [vytvořit protokoly](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity#PUT). Jak provádět další operace, jako je například seznam, aktualizace a odstranění, najdete v sekci [Protokolovač](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) dokumentace entity.
>
>

## <a name="configure-log-to-eventhubs-policies"></a>Konfigurace zásad protokolu eventhubs

Jakmile vaše protokoly nakonfigurovaný ve službě API Management, můžete nakonfigurovat zásad protokolu eventhubs k požadované událostem protokolu. Zásady protokolu eventhubs lze použít v části zásady příchozí nebo odchozí zásad.

1. Přejděte k vaší instanci APIM.
2. Vyberte kartu rozhraní API.
3. Vyberte rozhraní API, do které chcete přidat zásadu. V tomto příkladu je právě přidávána zásadu, která **Echo API** v **neomezený** produktu.
4. Vyberte **všechny operace**.
5. Nahoře na obrazovce vyberte kartu Návrh.
6. V okně zpracování příchozí nebo odchozí klepněte na trojúhelník (vedle tužky).
7. Vyberte editoru kódu. Další informace najdete v tématu [jak nastavit nebo upravit zásady](set-edit-policies.md).
8. Umístěte kurzor v `inbound` nebo `outbound` zásad.
9. V okně na pravé straně, vyberte **pokročilé zásady** > **protokolu k centru EventHub**. Vloží `log-to-eventhub` příkaz šablony zásad.

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```
Nahraďte `logger-id` s názvem protokolovacího nástroje správy rozhraní API, které jste nakonfigurovali v předchozím kroku.

Můžete použít libovolný výraz, který vrací řetězec jako hodnotu `log-to-eventhub` elementu. V tomto příkladu se zaznamená řetězec obsahující datum a čas, název služby, id požadavku, požadavek ip adresu a název operace.

Klikněte na tlačítko **Uložit** aktualizované zásady konfiguraci uložíte. Jakmile je uložen zásady nejsou aktivní a do určené centra událostí jsou zaznamenány události.

## <a name="next-steps"></a>Další postup
* Další informace o Azure Event Hubs
  * [Začínáme s Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Přijímat zprávy pomocí třídy EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Průvodce programováním pro službu Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* Další informace o integraci API Management a služby Event Hubs
  * [Odkaz na entitu protokolovacího nástroje](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [referenční informace o protokolu eventhub zásad](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
  * [Sledovat vaše rozhraní API s Azure API Management, Event Hubs a Runscope](api-management-log-to-eventhub-sample.md)    

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
