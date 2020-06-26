---
title: Správa koncových bodů a tras
titleSuffix: Azure Digital Twins
description: Přečtěte si, jak nastavit a spravovat koncové body a trasy událostí pro data digitálních vláken Azure.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 6/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5049e875c3b8047b99e3256c7bbac60d0af822bb
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85392263"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins"></a>Správa koncových bodů a tras v digitálních prozdvojeních Azure

V případě digitálních vláken Azure můžete směrovat [oznámení událostí](how-to-interpret-event-data.md) do služeb pro příjem dat nebo se připojit k výpočetním prostředkům. K tomu je potřeba nejdřív nastavit **koncové body** , které můžou přijímat události, a potom [**trasy událostí**](concepts-route-events.md) , které určují, které události generované pomocí digitálních vláken Azure se doručí do těch koncových bodů.

Mezi podporované typy koncových bodů patří:
* [Centrum událostí](../event-hubs/event-hubs-about.md)
* [Event Grid](../event-grid/overview.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Další informace o různých koncových bodech najdete v tématu [Volba mezi službou zasílání zpráv Azure](https://docs.microsoft.com/azure/event-grid/compare-messaging-services).

Koncové body a trasy se spravují pomocí [**rozhraní API EventRoutes**](how-to-use-apis-sdks.md), [sady .NET (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)nebo rozhraní příkazového [řádku Azure Digital revlákens](how-to-use-cli.md).

## <a name="create-an-endpoint-for-azure-digital-twins"></a>Vytvoření koncového bodu pro digitální vlákna Azure

Aby bylo možné propojit koncový bod s digitálními podsítěmi Azure, musí již existovat centrum událostí, téma Event Grid nebo Service Bus, které používáte pro koncový bod. 

Následující příklad ukazuje, jak vytvořit téma Event gridu pomocí rozhraní příkazového řádku Azure CLI:

```azurecli
az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
```

> [!TIP]
> Pokud chcete vypsat seznam názvů oblastí Azure, které se dají předávat do příkazů v Azure CLI, spusťte tento příkaz:
> ```azurecli
> az account list-locations -o table
> ```

Jakmile téma vytvoříte, můžete ho propojit s digitálními akcemi Azure pomocí následujícího příkazu:

```azurecli
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

Tím se v rámci názvu zadaného s argumentem zpřístupní téma Event gridu v rámci digitálních vláken Azure `--endpoint-name` . Tento název obvykle použijete jako cíl **trasy události**, kterou vytvoříte v další části pomocí rozhraní API služby Azure Digital Service.

Pro centrum událostí a koncové body Service Bus existují ekvivalentní příkazy:

* Přidat Service Bus koncový bod tématu (vyžaduje předem vytvořený Service Bus prostředek)
```azurecli 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

* Přidat koncový bod centra událostí (vyžaduje předem vytvořený prostředek centra událostí)
```azurecli
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

## <a name="manage-event-routes-with-apis"></a>Správa směrování událostí pomocí rozhraní API

Aby bylo možné skutečně odesílat data z digitálních vláken Azure do koncového bodu, je nutné definovat trasu události. **Rozhraní API** pro digitální vlákna v Azure EventRoutes umožňují vývojářům nacházet v celém systému a na navazujících službách tok událostí. Přečtěte si další informace o trasách událostí v tématu [Koncepty: směrování událostí digitálních vláken Azure](concepts-route-events.md).

Ukázky v tomto článku používají sadu C# SDK.

Trasy událostí jsou definované pomocí rozhraní API roviny dat. Definice trasy může obsahovat tyto prvky:
* ID trasy, kterou chcete použít
* Název koncového bodu, který chcete použít
* Filtr definující události odesílané do koncového bodu 

Pokud není k dispozici žádné ID trasy, nejsou směrovány žádné zprávy mimo digitální vlákna Azure. Pokud je k dispozici ID trasy a filtr je `true` , všechny zprávy budou směrovány do koncového bodu. Pokud je k dispozici ID trasy a je přidán jiný filtr, budou zprávy na základě filtru filtrovány.

Jedna trasa by měla umožňovat výběr více oznámení a typů událostí. 

`CreateEventRoute`je volání sady SDK, které se používá k přidání trasy události. Tady je příklad jeho použití:

```csharp
EventRoute er = new EventRoute("endpointName");
er.Filter("true"); //Filter allows all messages
await client.CreateEventRoute("routeName", er);
```

> [!TIP]
> Všechny funkce sady SDK přicházejí v synchronních a asynchronních verzích.

### <a name="event-route-sample-code"></a>Vzorový kód trasy události

Následující ukázka kódu ukazuje, jak vytvořit, vypsat a odstranit trasu události:
```csharp
try
{
    Console.WriteLine("Create a route: testRoute1");
    EventRoute er = new EventRoute("< your - endpoint - name >");
    // Make a filter that passes everything
    er.Filter = "true";
    client.CreateEventRoute("< your - route - name >", er);
    Console.WriteLine("Create route succeeded. Now listing routes:");
    Pageable <EventRoute> result = client.GetEventRoutes();
    foreach (EventRoute r in result)
    {
        Console.WriteLine($"Route {r.Id} to endpoint {r.EndpointId} with filter {r.Filter} ");
    }
    Console.WriteLine("Deleting routes:");
    foreach (EventRoute r in result)
    {
        Console.WriteLine($"Deleting route {r.Id}:");
        client.DeleteEventRoute(r.Id);
    }
}
catch (RequestFailedException e)
{
    Console.WriteLine($"*** Error in event route processing ({e.ErrorCode}):\n${e.Message}");
}
```

### <a name="filter-events"></a>Události filtru

Bez filtrování se koncovým bodům dostanou nejrůznější události z digitálních vláken Azure:
* Telemetrie aktivovaná pomocí [digitálních vláken](concepts-twins-graph.md) pomocí rozhraní API služby Azure Digital Service
* Dvojitá oznámení o změně vlastností, která se aktivují při změnách vlastností u všech vláken v instanci digitálních vláken Azure
* Události životního cyklu aktivované při vytváření nebo odstraňování vazeb
* Přidané nebo odstraněné události změny modelu aktivované při přidání nebo odstranění [modelů](concepts-models.md) nakonfigurovaných v instanci digitálních vláken Azure

Odesílaných událostí můžete omezit přidáváním filtru do koncového bodu.

Pokud chcete přidat filtr, můžete použít požadavek PUT na *https://{YourHost}/EventRoutes/myNewRoute? API-Version = 2020-05 -31-Preview* s následujícím textem:

```json  
{
    "endpointName": "<endpoint-name>",
    "filter": "<filter-text>"
}
``` 

Tady jsou podporované filtry tras.

| Název filtru | Description | Filtrovat schéma | Podporované hodnoty | 
| --- | --- | --- | --- |
| Typ | [Typ toku událostí](./concepts-route-events.md#types-of-event-messages) prostřednictvím vaší digitální instance | `"filter" : "type = '<eventType>'"` | `Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| Zdroj | Název instance digitálního vlákna Azure | `"filter" : "source = '<hostname>'"`|  **Pro oznámení**:`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net` <br> **Pro telemetrii**:`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net/digitaltwins/<twinId>`|
| Subjekt | Popis události v kontextu výše uvedeného zdroje událostí | `"filter": " subject = '<subject>'"` | **Pro oznámení**: subjekt je`<twinid>` <br> nebo formát identifikátoru URI pro předměty, které jsou jednoznačně identifikované více částmi nebo identifikátory:<br>`<twinid>/relationships/<relationshipid>`<br> **Pro telemetrii**: subjekt je cesta k komponentě (Pokud se telemetrie generuje z vlákna), jako je například `comp1.comp2` . Pokud telemetrie není vygenerována ze součásti, je pole předmětu prázdné. |
| Schéma dat | ID modelu DTDL | `"filter": "dataschema = 'dtmi:example:com:floor4;2'"` | **Pro telemetrii**: schéma dat je ID modelu vlákna nebo komponenty, která tuto telemetrii emituje. <br>**Pro oznámení**: schéma dat se nepodporuje.|
| Typ obsahu | Typ obsahu hodnoty dat | `"filter": "datacontenttype = '<contentType>'"` | `application/json` |
| Specifikace verze | Verze schématu události, kterou používáte | `"filter": "specversion = '<version>'"` | Musí být `1.0` . To označuje schéma CloudEvents verze 1,0. |
| True nebo false | Umožňuje vytvoření trasy bez filtrování nebo zakázání trasy. | `"filter" : "<true/false>"` | `true`= trasa je povolená bez filtrování. <br> `false`= trasa je zakázána. |
<!--
| ID | *Not implemented for public preview* | "filter": "id = '…'" | |
| Schema | *Not implemented for public preview*  | "filter": "dataschema = '…'" | |
-->

Filtry je také možné kombinovat pomocí následujících operací:

| Název filtru | Filtrovat schéma | Příklad | 
| --- | --- | --- |
| A/NEBO | `"filter": "<filter1> AND <filter2>"` | `"filter": "type != 'microsoft.iot.telemetry' AND datacontenttype = 'application/json'"` |
| A/NEBO | `"filter": "<filter1> OR <filter2>"` | `"filter": "type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json'"` |
| Vnořené operace | `"filter": "(<Comparison1>) AND (<Comparison2>)"` | `"filter": "(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')"` |

> [!NOTE]
> Během období Preview je podporována pouze řetězcová rovnost (=,! =).

Když implementujete nebo aktualizujete filtr, může tato změna trvat několik minut, než se v datovém kanálu projeví.

## <a name="manage-endpoints-and-routes-with-cli"></a>Správa koncových bodů a tras pomocí rozhraní příkazového řádku

Koncové body a trasy je taky možné spravovat pomocí rozhraní příkazového řádku Azure Digital revlákens CLI. Příkazy najdete v tématu [Postupy: použití rozhraní příkazového řádku Azure Digital zdvojené](how-to-use-cli.md).

## <a name="monitor-event-routes"></a>Monitorování tras událostí

Metriky směrování, jako je počet, latence a frekvence selhání, se dají zobrazit v [Azure Portal](https://portal.azure.com/). 

Na domovské stránce portálu vyhledejte vaši instanci digitálních vláken Azure, abyste si vyčetli její podrobnosti. Výběrem možnosti **metriky** v nabídce instance digitálních vláken Azure zobrazíte stránku *metriky* .

:::image type="content" source="media/how-to-manage-routes/metrics.png" alt-text="Stránka metriky instance digitálních vláken Azure v Azure Portal":::

Tady můžete zobrazit metriky pro vaši instanci a vytvořit vlastní zobrazení.

## <a name="next-steps"></a>Další kroky

Přečtěte si o různých typech zpráv o událostech, které můžete získat:
* [Postupy: interpretace dat událostí](how-to-interpret-event-data.md)
