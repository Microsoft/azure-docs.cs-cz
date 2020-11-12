---
title: Správa koncových bodů a tras (rozhraní API a rozhraní příkazového řádku)
titleSuffix: Azure Digital Twins
description: Přečtěte si, jak nastavit a spravovat koncové body a trasy událostí pro data digitálních vláken Azure.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 10/12/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 0b8bd9006482daf7c9218f0f3dbb16d2e08359bf
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94533748"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-apis-and-cli"></a>Správa koncových bodů a tras v Azure Digital revláken (rozhraní API a CLI)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

V digitálních prostředcích Azure můžete směrovat [oznámení událostí](how-to-interpret-event-data.md) na služby pro příjem dat nebo připojené výpočetní prostředky. To je prováděno napřed nastavením **koncových bodů** , které mohou přijímat události. Pak můžete vytvořit  [**trasy událostí**](concepts-route-events.md) , které určují, které události generované pomocí digitálních vláken Azure jsou doručeny do těch koncových bodů.

Tento článek vás provede procesem vytvoření koncových bodů a tras s [rozhraními API pro směrování událostí](/rest/api/digital-twins/dataplane/eventroutes), [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)a [Azure Digital revlákens CLI](how-to-use-cli.md).

Alternativně můžete také spravovat koncové body a trasy pomocí [Azure Portal](https://portal.azure.com). Verzi tohoto článku, která místo toho používá portál, najdete v tématu [*How to: Manage Endpoints and Routes (portál)*](how-to-manage-routes-portal.md).

## <a name="prerequisites"></a>Požadavky

* Budete potřebovat **účet Azure** (můžete [si ho nastavit zdarma).](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* V předplatném Azure budete potřebovat **instanci digitálního vlákna Azure** . Pokud instanci již nemáte, můžete ji vytvořit pomocí kroků v tématu [*Postupy: nastavení instance a ověřování*](how-to-set-up-instance-cli.md). Použijte následující hodnoty z instalačního programu užitečné pro pozdější použití v tomto článku:
    - Název instance
    - Skupina prostředků
    
## <a name="create-an-endpoint-for-azure-digital-twins"></a>Vytvoření koncového bodu pro digitální vlákna Azure

Toto jsou podporované typy koncových bodů, které můžete vytvořit pro instanci:
* [Event Grid](../event-grid/overview.md)
* [Event Hubs](../event-hubs/event-hubs-about.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Další informace o různých typech koncových bodů najdete v tématu [*Výběr mezi službou zasílání zpráv Azure*](../event-grid/compare-messaging-services.md).

Aby bylo možné propojit koncový bod s digitálními podsítěmi Azure, musí již existovat téma Event gridu, centrum událostí nebo Service Bus, které používáte pro koncový bod. 

### <a name="create-an-event-grid-endpoint"></a>Vytvoření koncového bodu Event Grid

Následující příklad ukazuje, jak vytvořit koncový bod Event gridu pomocí rozhraní příkazového řádku Azure CLI. Můžete použít [Azure Cloud Shell](https://shell.azure.com)nebo [místně nainstalovat rozhraní](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)příkazového řádku.

Nejprve vytvořte téma Event Grid. Můžete použít následující příkaz nebo zobrazit kroky podrobněji pomocí postupu v [části *Vytvoření vlastního tématu*](../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) v rychlém startu Event Grid *vlastních událostí* .

```azurecli-interactive
az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
```

> [!TIP]
> Pokud chcete vypsat seznam názvů oblastí Azure, které se dají předávat do příkazů v Azure CLI, spusťte tento příkaz:
> ```azurecli-interactive
> az account list-locations -o table
> ```

Jakmile téma vytvoříte, můžete ho propojit s digitálními akcemi Azure pomocí následujícího [příkazu rozhraní příkazového řádku Azure Digital](how-to-use-cli.md)propojování:

```azurecli-interactive
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

Teď je téma Event gridu dostupné jako koncový bod uvnitř digitálních vláken Azure pod názvem zadaným `--endpoint-name` argumentem. Tento název obvykle použijete jako cíl **trasy události** , kterou vytvoříte [později v tomto článku](#create-an-event-route) pomocí rozhraní API služby Azure Digital.

### <a name="create-an-event-hubs-or-service-bus-endpoint"></a>Vytvoření koncového bodu Event Hubs nebo Service Bus

Proces pro vytváření koncových bodů Event Hubs nebo Service Bus je podobný procesu Event Grid zobrazenému výše.

Nejdřív vytvořte prostředky, které budete používat jako koncový bod. Tady je to, co je potřeba:
* Service Bus: _obor názvů Service Bus_ _Service Bus téma_ , _autorizační pravidlo_
* Event Hubs: _obor názvů Event Hubs_ , _centrum událostí_ , _autorizační pravidlo_

Pak použijte následující příkazy k vytvoření koncových bodů v digitálních prozdvojeních Azure: 

* Přidat Service Bus koncový bod tématu (vyžaduje předem vytvořený Service Bus prostředek)
```azurecli-interactive 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

* Přidat Event Hubs koncový bod (vyžaduje předem vytvořený prostředek Event Hubs)
```azurecli-interactive
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

### <a name="create-an-endpoint-with-dead-lettering"></a>Vytvoření koncového bodu s nedoručenými písmeny

Když koncový bod nemůže doručovat událost v určitém časovém období nebo po pokusu o doručení události v určitém počtu opakování, může odeslat nedoručenou událost do účtu úložiště. Tento proces se označuje jako **nedoručené**.

Aby bylo možné vytvořit koncový bod s povoleným nemrtvým dopisem, je nutné použít [rozhraní API ARM](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) k vytvoření koncového bodu. 

Před nastavením umístění nedoručených zpráv musíte mít účet úložiště s kontejnerem. Při vytváření koncového bodu zadejte adresu URL tohoto kontejneru. Nedoručené písmeno je k dispozici jako adresa URL kontejneru s tokenem SAS. Tento token potřebuje `write` oprávnění pouze pro cílový kontejner v rámci účtu úložiště. Plně vytvořená adresa URL bude ve formátu: `https://<storageAccountname>.blob.core.windows.net/<containerName>?<SASToken>`

Další informace o tokenech SAS najdete v tématu: [udělení omezeného přístupu k Azure Storage prostředkům pomocí sdílených přístupových podpisů (SAS)](/azure/storage/common/storage-sas-overview)

Další informace o nedoručených písmenech najdete v tématu [*Koncepty: směrování událostí*](concepts-route-events.md#dead-letter-events).

#### <a name="configuring-the-endpoint"></a>Konfigurace koncového bodu

Při vytváření koncového bodu přidejte `deadLetterSecret` do `properties` objektu text v těle žádosti, která obsahuje adresu URL kontejneru a token SAS pro váš účet úložiště.

```json
{
  "properties": {
    "endpointType": "EventGrid",
    "TopicEndpoint": "https://contosoGrid.westus2-1.eventgrid.azure.net/api/events",
    "accessKey1": "xxxxxxxxxxx",
    "accessKey2": "xxxxxxxxxxx",
    "deadLetterSecret":"https://<storageAccountname>.blob.core.windows.net/<containerName>?<SASToken>"
  }
}
```

Další informace najdete v dokumentaci k digitálním výsledkům Azure REST API dokumentaci: [koncové body – DigitalTwinsEndpoint CreateOrUpdate](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate).

### <a name="message-storage-schema"></a>Schéma úložiště zpráv

Nedoručené zprávy budou v účtu úložiště uloženy v následujícím formátu:

`{container}/{endpointName}/{year}/{month}/{day}/{hour}/{eventId}.json`

Nedoručené zprávy budou odpovídat schématu původní události, která byla určena k doručení do původního koncového bodu.

Tady je příklad zprávy nedoručených zpráv pro [dvojitou dobu vytvoření oznámení](how-to-interpret-event-data.md#digital-twin-life-cycle-notifications):

```json
{
  "specversion": "1.0",
  "id": "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "<yourInstance>.api.<yourregion>.da.azuredigitaltwins-test.net",
  "data": {
    "$dtId": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "$etag": "W/\"xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
    "TwinData": "some sample",
    "$metadata": {
      "$model": "dtmi:test:deadlettermodel;1",
      "room": {
        "lastUpdateTime": "2020-10-14T01:11:49.3576659Z"
      }
    }
  },
  "subject": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "time": "2020-10-14T01:11:49.3667224Z",
  "datacontenttype": "application/json",
  "traceparent": "00-889a9094ba22b9419dd9d8b3bfe1a301-f6564945cb20e94a-01"
}
```

## <a name="create-an-event-route"></a>Vytvoření trasy události

Aby bylo možné ve skutečnosti odesílat data z digitálních vláken Azure do koncového bodu, bude nutné definovat **trasu události**. **Rozhraní API** pro digitální vlákna v Azure EventRoutes umožňují vývojářům nacházet v celém systému a na navazujících službách tok událostí. Přečtěte si další informace o trasách událostí v tématu [*Koncepty: směrování událostí digitálních vláken Azure*](concepts-route-events.md).

Ukázky v této části používají [sadu .NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true).

**Předpoklad** : před přechodem na vytvoření trasy je nutné vytvořit koncové body, jak je popsáno výše v tomto článku. Po dokončení nastavení koncových bodů můžete pokračovat v vytváření směrování událostí.

>[!NOTE]
>Pokud jste své koncové body v poslední době nasadili, ověřte, že se jejich nasazení dokončilo, **než** se pokusíte použít pro novou trasu události. Pokud se nasazení trasy nepovede, protože koncové body nejsou připravené, počkejte pár minut a zkuste to znovu.
>
> Pokud tento tok spouštíte, můžete si ho vymezit tak, že sestavíte za 2-3 minut čekání na dokončení nasazování služby koncového bodu a teprve potom přejdete na nastavení směrování.

### <a name="creation-code-with-apis-and-the-c-sdk"></a>Vytváření kódu pomocí rozhraní API a sady C# SDK

Trasy událostí jsou definované pomocí [rozhraní API roviny dat](how-to-use-apis-sdks.md#overview-data-plane-apis). 

Definice trasy může obsahovat tyto prvky:
* Název trasy, kterou chcete použít
* Název koncového bodu, který chcete použít
* Filtr definující události odesílané do koncového bodu 

Pokud název trasy neexistuje, žádné zprávy se nesměrují mimo digitální vlákna Azure. Pokud je název trasy a filtr je `true` , všechny zprávy jsou směrovány do koncového bodu. Pokud je k dispozici název trasy a je přidán jiný filtr, budou zprávy na základě filtru filtrovány.

Jedna trasa by měla umožňovat výběr více oznámení a typů událostí. 

`CreateOrReplaceEventRouteAsync` je volání sady SDK, které se používá k přidání trasy události. Tady je příklad jeho použití:

```csharp
string eventFilter = "$eventType = 'DigitalTwinTelemetryMessages' or $eventType = 'DigitalTwinLifecycleNotification'";
var er = new DigitalTwinsEventRoute("<your-endpointName>", eventFilter);
await CreateOrReplaceEventRouteAsync(client, "routeName", er);
```
    
> [!TIP]
> Všechny funkce sady SDK přicházejí v synchronních a asynchronních verzích.

### <a name="event-route-sample-code"></a>Vzorový kód trasy události

Následující ukázková metoda ukazuje, jak vytvořit, vypsat a odstranit trasu události:
```csharp
private async static Task CreateEventRoute(DigitalTwinsClient client, String routeName, DigitalTwinsEventRoute er)
{
  try
  {
    Console.WriteLine("Create a route: testRoute1");
            
    // Make a filter that passes everything
    er.Filter = "true";
    await client.CreateOrReplaceEventRouteAsync(routeName, er);
    Console.WriteLine("Create route succeeded. Now listing routes:");
    Pageable<DigitalTwinsEventRoute> result = client.GetEventRoutes();
    foreach (DigitalTwinsEventRoute r in result)
    {
        Console.WriteLine($"Route {r.Id} to endpoint {r.EndpointName} with filter {r.Filter} ");
    }
    Console.WriteLine("Deleting routes:");
    foreach (DigitalTwinsEventRoute r in result)
    {
        Console.WriteLine($"Deleting route {r.Id}:");
        client.DeleteEventRoute(r.Id);
    }
  }
    catch (RequestFailedException e)
    {
        Console.WriteLine($"*** Error in event route processing ({e.ErrorCode}):\n${e.Message}");
    }
  }
```

## <a name="filter-events"></a>Události filtru

Bez filtrování se koncovým bodům dostanou nejrůznější události z digitálních vláken Azure:
* Telemetrie aktivovaná pomocí [digitálních vláken](concepts-twins-graph.md) pomocí rozhraní API služby Azure Digital Service
* Dvojitá oznámení o změně vlastností, která se aktivují při změnách vlastností u všech vláken v instanci digitálních vláken Azure
* Události životního cyklu aktivované při vytváření nebo odstraňování vazeb

Odesílaným událostem můžete omezit přidáváním **filtru** pro koncový bod do trasy události.

Pokud chcete přidat filtr, můžete použít požadavek PUT na *https://{YourHost}/EventRoutes/myNewRoute? API-Version = 2020-10-31* s následujícím textem:

```json  
{
    "endpointName": "<endpoint-name>",
    "filter": "<filter-text>"
}
``` 

Tady jsou podporované filtry tras. Pomocí podrobností ve sloupci *schéma textu filtru* nahraďte `<filter-text>` zástupný text v těle žádosti výše.

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

## <a name="manage-endpoints-and-routes-with-cli"></a>Správa koncových bodů a tras pomocí rozhraní příkazového řádku

Koncové body a trasy je taky možné spravovat pomocí rozhraní příkazového řádku Azure Digital revlákens CLI. Další informace o použití rozhraní příkazového řádku a příkazů, které jsou k dispozici, najdete v tématu [*Postup: použití rozhraní příkazového řádku Azure Digital zdvojené*](how-to-use-cli.md).

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>Další kroky

Přečtěte si o různých typech zpráv o událostech, které můžete získat:
* [*Postupy: interpretace dat událostí*](how-to-interpret-event-data.md)