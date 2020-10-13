---
title: Jak protokolovat události do Azure Event Hubs v Azure API Management | Microsoft Docs
description: Naučte se protokolovat události do Azure Event Hubs v Azure API Management. Event Hubs je vysoce škálovatelná služba příchozího přenosu dat.
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
ms.openlocfilehash: 4909dde8efd7125a60509bb86b28f069bf6dbb24
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87903387"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Jak protokolovat události do Azure Event Hubs v Azure API Management
Vysoce škálovatelná služba Azure Event Hubs slouží ke zpracování příchozích dat. Dokáže přijímat miliony událostí za sekundu a umožňuje zpracovávat a analyzovat masivní objemy dat vytvářených zařízeními a aplikacemi připojenými k vaší síti. Event Hubs slouží jako "přední dveře" pro kanál událostí a jakmile se data shromažďují do centra událostí, je možné je transformovat a ukládat pomocí libovolného zprostředkovatele analýz v reálném čase nebo adaptérů pro dávkování/ukládání. Event Hubs oddělí vytvoření proudu událostí od spotřeby těchto události, aby spotřebitelé událostí mohli k událostem přistupovat podle svého vlastního plánu.

V tomto článku se dozvíte, jak [integrovat API Management Azure s Event Hubsm](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) videem, a popisuje, jak protokolovat API Management události pomocí Event Hubs Azure.

## <a name="create-an-azure-event-hub"></a>Vytvoření centra událostí Azure

Podrobné informace o tom, jak vytvořit centrum událostí a jak získat připojovací řetězce, které potřebujete k odesílání a příjmu událostí do a z centra událostí, najdete v tématu [vytvoření Event Hubs oboru názvů a centra událostí pomocí Azure Portal](../event-hubs/event-hubs-create.md).

## <a name="create-an-api-management-logger"></a>Vytvoření protokolovacího nástroje API Management
Teď, když máte centrum událostí, je dalším krokem konfigurace [protokolovacího](/rest/api/apimanagement/2019-12-01/logger) nástroje ve službě API Management, aby mohla protokolovat události do centra událostí.

API Management protokolovacích nástrojů se konfigurují pomocí [REST API API Management](https://aka.ms/apimapi). Podrobné příklady požadavků najdete v tématu [Vytvoření protokolovacích](/rest/api/apimanagement/2019-12-01/logger/createorupdate)nástrojů.

## <a name="configure-log-to-eventhub-policies"></a>Konfigurace zásad přihlášení k protokolu eventhub

Po nakonfigurování protokolovacího nástroje v API Management můžete nakonfigurovat zásadu přihlášení k protokolu, aby protokoloval požadované události. Zásady protokolů k protokolu eventhub se dají použít v oddílu příchozí zásady nebo v části odchozí zásady.

1. Přejděte k vaší instanci APIM.
2. Vyberte kartu Rozhraní API.
3. Vyberte rozhraní API, ke kterému chcete zásadu přidat. V tomto příkladu přidáme do **neomezeného** produktu zásadu pro **rozhraní API echo** .
4. Vyberte **všechny operace**.
5. V horní části obrazovky vyberte kartu Návrh.
6. V okně příchozí nebo odchozí zpracování klikněte na trojúhelník (vedle tužky).
7. Vyberte Editor kódu. Další informace najdete v tématu [jak nastavit nebo upravit zásady](set-edit-policies.md).
8. Umístěte kurzor do `inbound` `outbound` části zásady nebo.
9. V okně na pravé straně vyberte Upřesnit protokol **zásad**  >  **do centra EventHub**. Tím se vloží `log-to-eventhub` Šablona prohlášení o zásadách.

```xml
<log-to-eventhub logger-id="logger-id">
    @{
        return new JObject(
            new JProperty("EventTime", DateTime.UtcNow.ToString()),
            new JProperty("ServiceName", context.Deployment.ServiceName),
            new JProperty("RequestId", context.RequestId),
            new JProperty("RequestIp", context.Request.IpAddress),
            new JProperty("OperationName", context.Operation.Name)
        ).ToString();
    }
</log-to-eventhub>
```
Nahraďte `logger-id` hodnotou, kterou jste použili pro `{loggerId}` v adrese URL požadavku k vytvoření protokolovacího nástroje v předchozím kroku.

Můžete použít libovolný výraz, který vrátí řetězec jako hodnotu `log-to-eventhub` prvku. V tomto příkladu se protokoluje řetězec ve formátu JSON, který obsahuje datum a čas, název služby, ID žádosti, IP adresu požadavku a název operace.

Kliknutím na **Uložit** uložte aktualizovanou konfiguraci zásad. Jakmile je zásada uložena, je zásada aktivní a události budou protokolovány do určeného centra událostí.

> [!NOTE]
> Maximální podporovaná velikost zprávy, kterou je možné odeslat do centra událostí z této zásady API Management, je 200 kilobajtů (KB). Pokud je zpráva odeslaná do centra událostí větší než 200 KB, automaticky se zkrátí a zkrácená zpráva se přenese do Center událostí.

## <a name="preview-the-log-in-event-hubs-by-using-azure-stream-analytics"></a>Náhled Event Hubs přihlášení pomocí Azure Stream Analytics

Můžete zobrazit náhled Event Hubs přihlášení pomocí [Azure Stream Analytics dotazů](../event-hubs/process-data-azure-stream-analytics.md). 

1. V Azure Portal přejděte do centra událostí, do kterého protokolovací nástroj odesílá události. 
2. V části **funkce**vyberte kartu **data procesu** .
3. Na kartě **Povolit přehledy v reálném čase z událostí** vyberte **prozkoumat**.
4. Měli byste být schopni zobrazit náhled protokolu na kartě **Náhled vstupu** . Pokud zobrazená data nejsou aktuální, vyberte **aktualizovat** , aby se zobrazily nejnovější události.

## <a name="next-steps"></a>Další kroky
* Další informace o Azure Event Hubs
  * [Začínáme s Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Přijímání zpráv pomocí třídy EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)
  * [Průvodce programováním pro službu Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* Další informace o integraci API Management a Event Hubs
  * [Reference k entitě protokolovacího nástroje](/rest/api/apimanagement/2019-12-01/logger)
  * [odkaz na zásady přihlášení k protokolu eventhub](./api-management-advanced-policies.md#log-to-eventhub)
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
