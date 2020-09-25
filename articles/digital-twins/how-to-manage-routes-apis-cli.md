---
title: Správa koncových bodů a tras (rozhraní API a rozhraní příkazového řádku)
titleSuffix: Azure Digital Twins
description: Přečtěte si, jak nastavit a spravovat koncové body a trasy událostí pro data digitálních vláken Azure.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 6/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 2cde352738c1aa545c77cbcf5d974030cd75b1e8
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91326520"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-apis-and-cli"></a>Správa koncových bodů a tras v Azure Digital revláken (rozhraní API a CLI)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

V digitálních prostředcích Azure můžete směrovat [oznámení událostí](how-to-interpret-event-data.md) na služby pro příjem dat nebo připojené výpočetní prostředky. To je prováděno napřed nastavením **koncových bodů** , které mohou přijímat události. Pak můžete vytvořit  [**trasy událostí**](concepts-route-events.md) , které určují, které události generované pomocí digitálních vláken Azure jsou doručeny do těch koncových bodů.

Koncové body a trasy je možné spravovat pomocí [rozhraní API EventRoutes](how-to-use-apis-sdks.md), [sady .NET (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)nebo z rozhraní příkazového [řádku Azure Digital revlákens](how-to-use-cli.md). Tento článek vás provede procesem vytváření koncových bodů a tras pomocí těchto mechanismů.

Dají se taky spravovat prostřednictvím [Azure Portal](https://portal.azure.com). Verzi tohoto článku, která místo toho používá portál, najdete v tématu [*How to: Manage Endpoints and Routes (portál)*](how-to-manage-routes-portal.md).

## <a name="prerequisites"></a>Požadavky

* Budete potřebovat **účet Azure** (můžete [si ho nastavit zdarma).](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* V předplatném Azure budete potřebovat **instanci digitálního vlákna Azure** . Pokud instanci již nemáte, můžete ji vytvořit pomocí kroků v tématu [*Postupy: nastavení instance a ověřování*](how-to-set-up-instance-portal.md). Použijte následující hodnoty z instalačního programu užitečné pro pozdější použití v tomto článku:
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

Následující příklad ukazuje, jak vytvořit koncový bod Event gridu pomocí rozhraní příkazového řádku Azure CLI. Můžete použít [Azure Cloud Shell](https://shell.azure.com)nebo [místně nainstalovat rozhraní](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)příkazového řádku.

Nejprve vytvořte téma Event Grid. Můžete použít následující příkaz nebo zobrazit kroky podrobněji pomocí postupu v [části *Vytvoření vlastního tématu* ](../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) v rychlém startu Event Grid *vlastních událostí* .

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

Teď je téma Event gridu dostupné jako koncový bod uvnitř digitálních vláken Azure pod názvem zadaným `--endpoint-name` argumentem. Tento název obvykle použijete jako cíl **trasy události**, kterou vytvoříte [později v tomto článku](#event-routes-with-apis-and-the-c-sdk) pomocí rozhraní API služby Azure Digital.

### <a name="create-an-event-hubs-or-service-bus-endpoint"></a>Vytvoření koncového bodu Event Hubs nebo Service Bus

Proces pro vytváření koncových bodů Event Hubs nebo Service Bus je podobný procesu Event Grid zobrazenému výše.

Nejdřív vytvořte prostředky, které budete používat jako koncový bod. Tady je to, co je potřeba:
* Service Bus: _obor názvů Service Bus_ _Service Bus téma_, _autorizační pravidlo_
* Event Hubs: _obor názvů Event Hubs_, _centrum událostí_, _autorizační pravidlo_

Pak použijte následující příkazy k vytvoření koncových bodů v digitálních prozdvojeních Azure: 

* Přidat Service Bus koncový bod tématu (vyžaduje předem vytvořený Service Bus prostředek)
```azurecli 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

* Přidat Event Hubs koncový bod (vyžaduje předem vytvořený prostředek Event Hubs)
```azurecli
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

## <a name="event-routes-with-apis-and-the-c-sdk"></a>Trasy událostí (s rozhraními API a sadou C# SDK)

Aby bylo možné ve skutečnosti odesílat data z digitálních vláken Azure do koncového bodu, bude nutné definovat **trasu události**. **Rozhraní API** pro digitální vlákna v Azure EventRoutes umožňují vývojářům nacházet v celém systému a na navazujících službách tok událostí. Přečtěte si další informace o trasách událostí v tématu [*Koncepty: směrování událostí digitálních vláken Azure*](concepts-route-events.md).

Ukázky v této části používají sadu C# SDK.

**Předpoklad**: před přechodem na vytvoření trasy je nutné vytvořit koncové body, jak je popsáno výše v tomto článku. Po dokončení nastavení koncových bodů můžete pokračovat v vytváření směrování událostí.

>[!NOTE]
>Pokud jste své koncové body v poslední době nasadili, ověřte, že se jejich nasazení dokončilo, **než** se pokusíte použít pro novou trasu události. Pokud se nasazení trasy nepovede, protože koncové body nejsou připravené, počkejte pár minut a zkuste to znovu.
>
> Pokud tento tok spouštíte, můžete si ho vymezit tak, že sestavíte za 2-3 minut čekání na dokončení nasazování služby koncového bodu a teprve potom přejdete na nastavení směrování.

### <a name="create-an-event-route"></a>Vytvoření trasy události

Trasy událostí jsou definované pomocí rozhraní API roviny dat. 

Definice trasy může obsahovat tyto prvky:
* Název trasy, kterou chcete použít
* Název koncového bodu, který chcete použít
* Filtr definující události odesílané do koncového bodu 

Pokud název trasy neexistuje, žádné zprávy se nesměrují mimo digitální vlákna Azure. Pokud je název trasy a filtr je `true` , všechny zprávy jsou směrovány do koncového bodu. Pokud je k dispozici název trasy a je přidán jiný filtr, budou zprávy na základě filtru filtrovány.

Jedna trasa by měla umožňovat výběr více oznámení a typů událostí. 

`CreateEventRoute` je volání sady SDK, které se používá k přidání trasy události. Tady je příklad jeho použití:

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

Odesílaným událostem můžete omezit přidáváním **filtru** pro koncový bod do trasy události.

Pokud chcete přidat filtr, můžete použít požadavek PUT na *https://{YourHost}/EventRoutes/myNewRoute? API-Version = 2020-05 -31-Preview* s následujícím textem:

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

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>Další kroky

Přečtěte si o různých typech zpráv o událostech, které můžete získat:
* [*Postupy: interpretace dat událostí*](how-to-interpret-event-data.md)
