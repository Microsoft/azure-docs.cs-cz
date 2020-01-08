---
title: Durable Functions publikování do Azure Event Grid (Preview)
description: Naučte se konfigurovat automatické publikování Azure Event Grid pro Durable Functions.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 5d1960f0e8d249ac77f3c64e18b332a3d55d5180
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2020
ms.locfileid: "75613121"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Durable Functions publikování do Azure Event Grid (Preview)

Tento článek popisuje, jak nastavit Durable Functions pro publikování událostí životního cyklu orchestrace (například vytvoření, dokončení a selhání) do vlastního [tématu Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).

Tato funkce je užitečná v následujících situacích:

* **DevOps scénáře jako modrá/zelená nasazení**: možná budete chtít zjistit, jestli nějaké úlohy běží před implementací [Souběžné strategie nasazení](durable-functions-versioning.md#side-by-side-deployments).

* **Rozšířená podpora monitorování a diagnostiky**: informace o stavu orchestrace můžete sledovat v externím úložišti optimalizovaném pro dotazy, jako je SQL Database nebo CosmosDB.

* **Dlouhodobě běžící aktivita na pozadí**: Pokud použijete Durable Functions pro dlouhou běžící aktivitu na pozadí, tato funkce vám pomůže seznámit se s aktuálním stavem.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Do projektu Durable Functions nainstalujte [Microsoft. Azure. WebJobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) .
* Nainstalujte [emulátor Azure Storage](../../storage/common/storage-use-emulator.md).
* Instalace rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) nebo použití [Azure Cloud Shell](../../cloud-shell/overview.md)

## <a name="create-a-custom-event-grid-topic"></a>Vytvoření vlastního tématu Event gridu

Vytvořte téma Event Grid pro odesílání událostí z Durable Functions. Následující pokyny ukazují, jak vytvořit téma pomocí Azure CLI. Informace o tom, jak to udělat pomocí PowerShellu nebo Azure Portal, najdete v následujících článcích:

* [EventGrid rychlý Start: Vytvoření vlastní události – PowerShell](../../event-grid/custom-event-quickstart-powershell.md)
* [EventGrid rychlý Start: Vytvoření vlastní události – Azure Portal](../../event-grid/custom-event-quickstart-portal.md)

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Pomocí příkazu `az group create` vytvořte skupinu prostředků. V současné době Azure Event Grid nepodporuje všechny oblasti. Informace o podporovaných oblastech najdete v tématu [přehled Azure Event Grid](../../event-grid/overview.md).

```bash
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Vytvoření vlastního tématu

Téma Event Grid poskytuje uživatelsky definovaný koncový bod, do kterého odesíláte událost. Nahraďte `<topic_name>` jedinečným názvem vašeho tématu. Název tématu musí být jedinečný, protože se jedná o položku DNS.

```bash
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>Získání koncového bodu a klíče

Získejte koncový bod tématu. Nahraďte `<topic_name>` názvem, který jste zvolili.

```bash
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

Získejte klíč tématu. Nahraďte `<topic_name>` názvem, který jste zvolili.

```bash
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Nyní můžete odesílat události do tématu.

## <a name="configure-azure-event-grid-publishing"></a>Konfigurace publikování Azure Event Grid

V projektu Durable Functions vyhledejte `host.json` soubor.

Do vlastnosti `durableTask` přidejte `eventGridTopicEndpoint` a `eventGridKeySettingName`.

```json
{
    "durableTask": {
        "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "eventGridKeySettingName": "EventGridKey"
    }
}
```

Možné konfigurační vlastnosti Azure Event Grid najdete v [dokumentaci Host. JSON](../functions-host-json.md#durabletask). Po nakonfigurování `host.json` souboru odešle Function App události životního cyklu do tématu Event Grid. To funguje, když aplikaci Function App spustíte místně i v Azure.

Nastavte nastavení aplikace pro klíč tématu v Function App a `local.settings.json`. Následující JSON je ukázka `local.settings.json` pro místní ladění. Nahraďte `<topic_key>` klíčem tématu.  

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "UseDevelopmentStorage=true",
        "AzureWebJobsDashboard": "UseDevelopmentStorage=true",
        "EventGridKey": "<topic_key>"
    }
}
```

Ujistěte se, že [emulátor úložiště](../../storage/common/storage-use-emulator.md) pracuje. Před spuštěním příkazu je vhodné spustit `AzureStorageEmulator.exe clear all`.

## <a name="create-functions-that-listen-for-events"></a>Vytvořit funkce, které naslouchají událostem

Vytvořte Function App. Je nejvhodnější ho vyhledat ve stejné oblasti jako téma Event Grid.

### <a name="create-an-event-grid-trigger-function"></a>Vytvoření funkce triggeru Event gridu

Vytvořte funkci pro příjem událostí životního cyklu. Vyberte možnost **vlastní funkce**.

![Vyberte vytvořit vlastní funkci.](./media/durable-functions-event-publishing/functions-portal.png)

Zvolte aktivační událost Event Grid a vyberte `C#`.

![Vyberte aktivační událost Event Grid.](./media/durable-functions-event-publishing/eventgrid-trigger.png)

Zadejte název funkce a pak vyberte `Create`.

![Vytvořte aktivační událost Event Grid.](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

Vytvoří se funkce s následujícím kódem:

#### <a name="precompiled-c"></a>PředkompilovanéC#
```csharp
public static void Run([HttpTrigger] JObject eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
}
```

#### <a name="c-script"></a>C#Pravidel

```csharp
#r "Newtonsoft.Json"
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Microsoft.Extensions.Logging;

public static void Run(JObject eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
}
```

Vyberte `Add Event Grid Subscription`. Tato operace přidá odběr služby Event Grid pro téma Event Grid, které jste vytvořili. Další informace najdete v tématu [Koncepty v Azure Event Grid](https://docs.microsoft.com/azure/event-grid/concepts) .

![Vyberte odkaz Event Grid aktivační události.](./media/durable-functions-event-publishing/eventgrid-trigger-link.png)

Jako **typ tématu**vyberte `Event Grid Topics`. Vyberte skupinu prostředků, kterou jste vytvořili pro téma Event Grid. Pak vyberte instanci tématu Event Grid. Stiskněte `Create`.

![Vytvoří odběr Event Gridu.](./media/durable-functions-event-publishing/eventsubscription.png)

Teď jste připraveni přijímat události životního cyklu.

## <a name="create-durable-functions-to-send-the-events"></a>Vytvoření Durable Functions k odeslání událostí

V projektu Durable Functions spusťte ladění na místním počítači.  Následující kód je stejný jako kód šablony pro Durable Functions. Na místním počítači jste už nakonfigurovali `host.json` a `local.settings.json`.

### <a name="precompiled-c"></a>PředkompilovanéC#

```csharp
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.DurableTask;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace LifeCycleEventSpike
{
    public static class Sample
    {
        [FunctionName("Sample")]
        public static async Task<List<string>> RunOrchestrator(
            [OrchestrationTrigger] IDurableOrchestrationContext context)
        {
            var outputs = new List<string>();

            // Replace "hello" with the name of your Durable Activity Function.
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "Tokyo"));
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "Seattle"));
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "London"));

            // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
            return outputs;
        }

        [FunctionName("Sample_Hello")]
        public static string SayHello([ActivityTrigger] string name, ILogger log)
        {
            log.LogInformation($"Saying hello to {name}.");
            return $"Hello {name}!";
        }

        [FunctionName("Sample_HttpStart")]
        public static async Task<HttpResponseMessage> HttpStart(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequestMessage req,
            [DurableClient] IDurableOrchestrationClient starter,
            ILogger log)
        {
            // Function input comes from the request content.
            string instanceId = await starter.StartNewAsync("Sample", null);
            log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
            return starter.CreateCheckStatusResponse(req, instanceId);
        }
    }
}
```

> [!NOTE]
> Předchozí kód je pro Durable Functions 2. x. Pro Durable Functions 1. x je nutné použít `DurableOrchestrationContext` namísto `IDurableOrchestrationContext`atribut `OrchestrationClient` namísto atributu `DurableClient` a místo `DurableOrchestrationClient` musíte použít parametr `IDurableOrchestrationClient`. Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

Pokud zavoláte `Sample_HttpStart` s využitím post nebo prohlížeče, spustí se trvalá funkce pro odeslání událostí životního cyklu. Koncový bod je obvykle `http://localhost:7071/api/Sample_HttpStart` pro místní ladění.

Podívejte se na protokoly ze funkce, kterou jste vytvořili v Azure Portal.

```
2019-04-20T09:28:21.041 [Info] Function started (Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d)
2019-04-20T09:28:21.104 [Info] {
    "id": "054fe385-c017-4ce3-b38a-052ac970c39d",
    "subject": "durable/orchestrator/Running",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Running"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2019-04-20T09:28:19.6492068Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}

2019-04-20T09:28:21.104 [Info] Function completed (Success, Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d, Duration=65ms)
2019-04-20T09:28:37.098 [Info] Function started (Id=36fadea5-198b-4345-bb8e-2837febb89a2)
2019-04-20T09:28:37.098 [Info] {
    "id": "8cf17246-fa9c-4dad-b32a-5a868104f17b",
    "subject": "durable/orchestrator/Completed",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Completed"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2019-04-20T09:28:36.5061317Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}
2019-04-20T09:28:37.098 [Info] Function completed (Success, Id=36fadea5-198b-4345-bb8e-2837febb89a2, Duration=0ms)
```

## <a name="event-schema"></a>Schéma událostí

Následující seznam vysvětluje schéma událostí životního cyklu:

* **`id`** : jedinečný identifikátor události Event Grid.
* **`subject`** : cesta k předmětu události. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` budou `Running`, `Completed`, `Failed`a `Terminated`.  
* **`data`** : Durable Functions specifické parametry.
  * **`hubName`** : [TaskHub](durable-functions-task-hubs.md) název.
  * **`functionName`** : název funkce nástroje Orchestrator.
  * **`instanceId`** : Durable Functions InstanceId.
  * **`reason`** : další data přidružená k události sledování. Další informace najdete v tématu [Diagnostika v Durable Functions (Azure Functions)](durable-functions-diagnostics.md) .
  * **`runtimeStatus`** : stav modulu runtime orchestrace. Spuštění, dokončení, selhání, zrušeno.
* **`eventType`** : "orchestratorEvent"
* **`eventTime`** : čas události (UTC).
* **`dataVersion`** : verze schématu událostí životního cyklu.
* **`metadataVersion`** : verze metadat.
* **`topic`** : prostředek tématu Event Grid.

## <a name="how-to-test-locally"></a>Jak místně testovat

K otestování lokálně použijte [ngrok](../functions-bindings-event-grid.md#local-testing-with-ngrok).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Naučte se správu instancí v Durable Functions](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Seznámení se správou verzí v Durable Functions](durable-functions-versioning.md)
