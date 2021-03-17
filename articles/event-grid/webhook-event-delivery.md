---
title: Doručení události Webhooku
description: Tento článek popisuje doručování událostí Webhooku a ověření koncového bodu při použití webhooků.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: e9a52d0cb3e4e880d91e1b748d97ef3041298930
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87461234"
---
# <a name="webhook-event-delivery"></a>Doručení události Webhooku
Webhooky jsou jedním z mnoha způsobů, jak přijímat události z Azure Event Grid. Když je nová událost připravena, Služba Event Grid odešle požadavek HTTP do nakonfigurovaného koncového bodu s událostí v textu požadavku.

Stejně jako mnoho dalších služeb, které podporují Webhooky, Event Grid vyžaduje, abyste prokáže vlastnictví koncového bodu Webhooku předtím, než začne doručovat události do tohoto koncového bodu. Tento požadavek brání uživateli se zlými úmysly v přeplavení koncového bodu s událostmi. Když použijete některou ze tří služeb Azure uvedených níže, infrastruktura Azure toto ověření automaticky zpracuje:

- Azure Logic Apps s [konektorem Event Grid](/connectors/azureeventgrid/)
- Azure Automation prostřednictvím [Webhooku](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
- Azure Functions s [triggerem Event Grid](../azure-functions/functions-bindings-event-grid.md)

## <a name="endpoint-validation-with-event-grid-events"></a>Ověřování koncových bodů s Event Gridmi událostmi
Pokud používáte jiný typ koncového bodu, jako je Azure Functions založený na triggeru HTTP, váš kód koncového bodu musí být součástí ověřovací metody handshake s Event Grid. Event Grid podporuje dva způsoby ověření předplatného.

1. **Synchronní metoda handshake**: v době vytváření odběru událostí Event Grid odesílá do vašeho koncového bodu událost ověření předplatného. Schéma této události je podobné jako jakákoli jiná událost Event Grid. Datová část této události obsahuje `validationCode` vlastnost. Vaše aplikace ověřuje, zda je žádost o ověření určena pro očekávané předplatné události, a v případě synchronního vrátí ověřovací kód v odpovědi. Tento mechanismus handshake je podporován ve všech Event Grid verzích.

2. **Asynchronní metoda handshake**: v některých případech nemůžete v případě synchronních odpovědí vracet ValidationCode. Například pokud používáte službu třetí strany (například [`Zapier`](https://zapier.com) nebo [IFTTT](https://ifttt.com/)), nemůžete programově reagovat pomocí ověřovacího kódu.

   Počínaje verzí 2018-05-01-Preview Event Grid podporuje manuální ověřování metodou handshake. Pokud vytváříte odběr událostí pomocí sady SDK nebo nástroje, který používá rozhraní API verze 2018-05-01-Preview nebo novější, Event Grid odešle `validationUrl` vlastnost v datové části události ověření předplatného. K dokončení metody handshake Najděte tuto adresu URL v datech události a proveďte do ní požadavek GET. Můžete použít buď klienta REST, nebo webový prohlížeč.

   Zadaná adresa URL je platná **5 minut**. Během této doby je stav zřizování odběru události `AwaitingManualAction` . Pokud nedokončíte ruční ověření během 5 minut, stav zřizování je nastaven na `Failed` . Než začnete s ručním ověřováním, budete muset vytvořit odběr událostí znovu.

   Tento mechanismus ověřování také vyžaduje, aby koncový bod Webhooku vrátil stavový kód HTTP 200, aby věděl, že příspěvek pro událost ověření byl přijat předtím, než mohl být vložen do režimu ručního ověření. Jinými slovy, pokud koncový bod vrátí 200, ale nevrátí zpět odpověď ověření synchronně, režim se převede do režimu ručního ověření. Pokud se v ověřovací adrese URL objeví během 5 minut, považuje se ověřovací metoda handshake za úspěšnou.

> [!NOTE]
> Použití certifikátů podepsaných svým držitelem k ověření se nepodporuje. Místo toho použijte podepsaný certifikát od Komerční certifikační autority (CA).

### <a name="validation-details"></a>Podrobnosti ověření

- V okamžiku vytvoření nebo aktualizace odběru události Event Grid účtuje událost ověření předplatného do cílového koncového bodu.
- Událost obsahuje hodnotu hlavičky "AEG-Event-Type: SubscriptionValidation".
- Tělo události má stejné schéma jako jiné události Event Grid.
- Vlastnost eventType události je `Microsoft.EventGrid.SubscriptionValidationEvent` .
- Vlastnost dat události obsahuje `validationCode` vlastnost s náhodně generovaným řetězcem. Například "validationCode: acb13...".
- Data události také obsahují `validationUrl` vlastnost s adresou URL pro ruční ověření předplatného.
- Pole obsahuje pouze událost ověření. Další události se odesílají v samostatném požadavku po návratu zpět ověřovacího kódu.
- Sady SDK pro EventGrid dataplan mají třídy odpovídající datům události ověření předplatného a odpovědi na ověření předplatného.

V následujícím příkladu je uveden příklad SubscriptionValidationEvent:

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
      "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=512d38b6-c7b8-40c8-89fe-f46f9e9622b6&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1A1A1A1A"
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2018-01-25T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

Chcete-li prokázat vlastnictví koncového bodu, vraťte kód ověření do vlastnosti validationResponse, jak je znázorněno v následujícím příkladu:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Je nutné vrátit stavový kód odpovědi HTTP 200 OK. Přijatý protokol HTTP 202 nebyl rozpoznán jako platná odpověď ověření předplatného Event Grid. Požadavek HTTP musí být dokončen do 30 sekund. Pokud operace nekončí do 30 sekund, operace se zruší a může se pokusit o její opakování po 5 sekundách. Pokud všechny pokusy selžou, bude zpracována jako chyba handshake ověření.

Nebo můžete předplatné ověřit ručně odesláním požadavku GET na adresu URL pro ověření. Předplatné události zůstane v nedokončeném stavu, dokud se neověří. Ověřovací adresa URL používá port 553. Pokud vaše pravidla brány firewall blokují port 553, může být potřeba aktualizovat pravidla pro úspěšnou ruční handshaki.

Příklad manipulace s metodou handshake ověření předplatného najdete v tématu [ukázka jazyka C#](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

## <a name="endpoint-validation-with-cloudevents-v10"></a>Ověření koncového bodu pomocí CloudEvents v 1.0
Pokud už jste obeznámeni s Event Grid, možná jste si všimli, že je Event Grid ověření koncového bodu handshake pro předcházení zneužití. CloudEvents v 1.0 implementuje svou vlastní [sémantiku ochrany proti zneužívání](webhook-event-delivery.md) pomocí metody HTTP. Další informace o této službě si můžete přečíst [tady](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). Při použití schématu CloudEvents pro výstup Event Grid použití s ochranou zneužití CloudEvents v 1.0 místo mechanismu události ověřování Event Grid.

## <a name="next-steps"></a>Další kroky
V následujícím článku se dozvíte, jak řešit ověřování odběrů událostí: 

[Řešení potíží s ověřováním odběru událostí](troubleshoot-subscription-validation.md)
