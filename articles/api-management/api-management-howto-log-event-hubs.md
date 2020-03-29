---
title: Jak protokolovat události do Azure Event Hubs ve správě Rozhraní API Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak protokolovat události do Azure Event Hubs ve správě rozhraní AZURE API.
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
ms.openlocfilehash: 2f07f6a27e78ee4df8c64a09918758d02c28c6d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76898785"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Jak protokolovat události do Azure Event Hubs ve správě Azure API
Vysoce škálovatelná služba Azure Event Hubs slouží ke zpracování příchozích dat. Dokáže přijímat miliony událostí za sekundu a umožňuje zpracovávat a analyzovat masivní objemy dat vytvářených zařízeními a aplikacemi připojenými k vaší síti. Event Hubs funguje jako "přední dveře" pro kanál událostí a jakmile jsou data shromážděna do centra událostí, lze je transformovat a uložit pomocí libovolného poskytovatele analýzy v reálném čase nebo adaptérů dávkového nebo úložného systému. Event Hubs oddělí vytvoření proudu událostí od spotřeby těchto události, aby spotřebitelé událostí mohli k událostem přistupovat podle svého vlastního plánu.

Tento článek je společníkem [pro integraci správy rozhraní Azure API s](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) video Centra událostí a popisuje, jak protokolovat události správy rozhraní API pomocí Centra událostí Azure.

## <a name="create-an-azure-event-hub"></a>Vytvoření centra událostí Azure

Podrobné kroky k vytvoření centra událostí a získání připojovacích řetězců, které potřebujete k odesílání a přijímání událostí do a z centra událostí, najdete [v tématu Vytvoření oboru názvů Event Hubs a centra událostí pomocí portálu Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-api-management-logger"></a>Vytvoření loggeru správy rozhraní API
Teď, když máte Centrum událostí, dalším krokem je konfigurace [protokolování](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/logger) ve službě api management tak, aby mohl protokolovat události do centra událostí.

Úhozy kláves API Management jsou konfigurovány pomocí [rozhraní API API PRO správu rozhraní API.](https://aka.ms/apimapi) Podrobné příklady požadavků naleznete v tématu [jak vytvořit úhozy kláves](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/logger/createorupdate).

## <a name="configure-log-to-eventhubs-policies"></a>Konfigurace zásad log-to-eventhubs

Jakmile je váš protokolovací nástroj nakonfigurován ve správě rozhraní API, můžete nakonfigurovat zásady log-to-eventhubs tak, aby zaznamenávaly požadované události. Zásady log-to eventhubs lze použít v části zásad y příchozí nebo odchozí zásady.

1. Přejděte k vaší instanci APIM.
2. Vyberte kartu Rozhraní API.
3. Vyberte rozhraní API, do kterého chcete přidat zásadu. V tomto příkladu přidáváme zásady do **rozhraní Echo API** v produktu **Neomezený.**
4. Vyberte **všechny operace**.
5. V horní části obrazovky vyberte kartu Návrh.
6. V okně Příchozí nebo Odchozí zpracování klikněte na trojúhelník (vedle tužky).
7. Vyberte editor kódu. Další informace naleznete v [tématu Jak nastavit nebo upravit zásady](set-edit-policies.md).
8. Umístěte kurzor `inbound` do `outbound` části nebo zásady.
9. V okně vpravo vyberte **Upřesnit zásady** > **Log to EventHub**. Tím vložíte `log-to-eventhub` šablonu příkazu zásady.

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```
Nahraďte `logger-id` hodnotou, `{new logger name}` kterou jste použili v adrese URL k vytvoření protokolování v předchozím kroku.

Můžete použít libovolný výraz, který vrací řetězec `log-to-eventhub` jako hodnotu prvku. V tomto příkladu je zaznamenán řetězec obsahující datum a čas, název služby, id požadavku, ip adresu požadavku a název operace.

Kliknutím na **Uložit** uložte aktualizovanou konfiguraci zásad. Jakmile je uložena zásada je aktivní a události jsou zaznamenány do určeného centra událostí.

## <a name="next-steps"></a>Další kroky
* Další informace o Azure Event Hubs
  * [Začínáme s Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Přijímání zpráv pomocí třídy EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Průvodce programováním pro službu Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* Další informace o integraci služby API Management a Event Hubs
  * [Odkaz na entitu loggeru](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/logger)
  * [odkaz na zásady log-to-eventhub](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
  * [Monitorování rozhraní API pomocí Azure API Management, Event Hubs a Moesif](api-management-log-to-eventhub-sample.md)  
* Další informace o [integraci s Azure Application Insights](api-management-howto-app-insights.md)

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
