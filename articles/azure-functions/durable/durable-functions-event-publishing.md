---
title: Durable Functions publikování do služby Azure Event Grid (preview)
description: Zjistěte, jak konfigurovat automatické publikování Azure Event Grid pro Durable Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: glenga
ms.openlocfilehash: 00735293d8fa8c6056f1ecf89fd312fe4b90bcac
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52642701"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Durable Functions publikování do služby Azure Event Grid (preview)

Tento článek popisuje, jak nastavit Azure Durable Functions k publikování Orchestrace životní cyklus (například, vytvoří se události, dokončenou a neúspěšné) pro vlastní [tématu Azure Event gridu](https://docs.microsoft.com/azure/event-grid/overview). 

Toto jsou některé scénáře, kdy se tato funkce je užitečná:

* **Scénáře DevOps, jako je nasazení modrá nebo zelená**: můžete chtít vědět, pokud jsou všechny úkoly spuštěné před implementací [strategii nasazení vedle sebe](https://docs.microsoft.com/azure/azure-functions/durable-functions-versioning#side-by-side-deployments).

* **Přidává rozšířenou podporu monitorování a diagnostiku**: vám může udržovat přehled o informace o stavu Orchestrace v externím úložišti optimalizované pro dotazy, jako je SQL database nebo cosmos DB.

* **Aktivita dlouho běžící na pozadí**: Pokud používáte Durable Functions pro dlouho běžící aktivitu na pozadí, tato funkce vám umožní zjistit aktuální stav.

## <a name="prerequisites"></a>Požadavky

* Nainstalujte [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) 1.3.0-rc nebo později v projektu Durable Functions.
* Nainstalujte [emulátoru úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-use-emulator).
* Nainstalujte [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) nebo použijte [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)

## <a name="create-a-custom-event-grid-topic"></a>Vytvoření vlastního tématu Event gridu

Vytvořte téma Event gridu pro odesílání událostí z Durable Functions. Následující pokyny ukazují, jak vytvořit téma s použitím rozhraní příkazového řádku Azure. Informace o tom, jak to udělat pomocí Powershellu nebo na webu Azure portal najdete v následujících článcích:

* [EventGrid šablon rychlý start: Vytvoření vlastní události – PowerShell](https://docs.microsoft.com/azure/event-grid/custom-event-quickstart-powershell)
* [EventGrid šablon rychlý start: Vytvoření vlastní události – Azure portal](https://docs.microsoft.com/azure/event-grid/custom-event-quickstart-portal)

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Pomocí příkazu `az group create` vytvořte skupinu prostředků. Event Grid v současné době nepodporuje všech oblastech. Informace o tom, které oblasti jsou podporovány, naleznete v tématu [Přehled služby Event Grid](https://docs.microsoft.com/azure/event-grid/overview). 

```bash
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Vytvoření vlastního tématu

Téma Event gridu poskytuje koncový bod definovaný uživatelem, do kterého odesíláte události do. Nahraďte `<topic_name>` jedinečným názvem vašeho tématu. Název tématu musí být jedinečný, protože bude záznam DNS.

```bash
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup 
```

## <a name="get-the-endpoint-and-key"></a>Získání koncového bodu a klíče

Získejte koncový bod tohoto tématu. Nahraďte `<topic_name>` s vámi zvolený název.

```bash
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

Získáte klíč tématu. Nahraďte `<topic_name>` s vámi zvolený název.

```bash
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Teď můžete odesílat události do tématu.

## <a name="configure-azure-event-grid-publishing"></a>Konfigurace publikování Azure Event Grid

V projektu Durable Functions najdete `host.json` souboru.

Přidat `EventGridTopicEndpoint` a `EventGridKeySettingName` v `durableTask` vlastnost.

```json
{
    "durableTask": {
        "EventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "EventGridKeySettingName": "EventGridKey"
    }
}
```

Je to možné vlastnosti konfigurace služby Azure Event Grid jsou následující:

* **EventGridTopicEndpoint** – koncový bod tématu Event gridu. *AppSettingName %* syntaxi můžete použít k vyřešení této hodnoty nastavení aplikace nebo proměnné prostředí.
* **EventGridKeySettingName** – klíč nastavení aplikace nastavte na vaši funkci Azure functions. Odolná služba Functions se získat klíč tématu Event gridu z hodnoty.
* **EventGridPublishRetryCount** – [volitelné] počet pokusů o zopakování Pokud publikování do tématu Event gridu se nezdaří.
* **EventGridPublishRetryInterval** -[Nepovinné] Event Grid publish interval opakování v *hh: mm:* formátu. Pokud není zadán, je výchozí interval opakování je 5 minut.

Jakmile nakonfigurujete `host.json` souborů, spuštění projektu si Durable Functions pro odeslání události životního cyklu do tématu Event gridu. Tento postup funguje při spuštění aplikace Function App a spouštíte místně.

Nastavte nastavení aplikace, které pro klíč tématu do aplikace Function App a `local.setting.json`. Následující kód JSON je ukázka `local.settings.json` pro místní ladění. Nahraďte `<topic_key>` s klíč tématu.  

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

Ujistěte se, že [emulátor úložiště](https://docs.microsoft.com/azure/storage/common/storage-use-emulator) funguje. Je vhodné spustit `AzureStorageEmulator.exe clear all` před provedením příkazu.

## <a name="create-functions-that-listen-for-events"></a>Vytvoření funkce, které naslouchat událostem

Vytvoření aplikace Function App. Je nejlepší umístit ve stejné oblasti jako téma Event gridu.

### <a name="create-an-event-grid-trigger-function"></a>Vytvoření funkce pro aktivaci služby Event Grid

Vytvoření funkce, která se zobrazí události životního cyklu. Vyberte **vlastní funkci**. 

![Vyberte možnost vytvořit vlastní funkci.](./media/durable-functions-event-publishing/functions-portal.png)

Zvolte Trigger služby Event Grid a vyberte `C#`.

![Vyberte Trigger služby Event Grid.](./media/durable-functions-event-publishing/eventgrid-trigger.png)

Zadejte název funkce a pak vyberte `Create`.

![Vytvoření triggeru služby Event Grid.](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

Vytvoří funkci s následujícím kódem: 

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

Vyberte `Add Event Grid Subscription`. Tato operace přidá odběr Event gridu pro téma Event gridu, kterou jste vytvořili. Další informace najdete v tématu [koncepty ve službě Azure Event Grid](https://docs.microsoft.com/azure/event-grid/concepts)

![Vyberte odkaz na Trigger služby Event Grid.](./media/durable-functions-event-publishing/eventgrid-trigger-link.png)

Vyberte `Event Grid Topics` pro **typ tématu**. Vyberte skupinu prostředků, kterou jste vytvořili pro téma Event gridu. Vyberte instanci téma Event gridu. Stisknutím klávesy `Create`.

![Vytvoří odběr Event Gridu.](./media/durable-functions-event-publishing/eventsubscription.png)

Nyní jste připraveni přijímat události životního cyklu. 

## <a name="create-durable-functions-to-send-the-events"></a>Vytvoření Durable Functions k odeslání události.

Ve vašem projektu Durable Functions zahájit ladění na místním počítači.  Následující kód je stejný jako kód šablony pro Durable Functions. Jste již nakonfigurovali `host.json` a `local.settings.json` na místním počítači. 

```csharp
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace LifeCycleEventSpike
{
    public static class Sample
    {
    {
        [FunctionName("Sample")]
        public static async Task<List<string>> RunOrchestrator(
            [OrchestrationTrigger] DurableOrchestrationContext context)
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
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
            [OrchestrationClient]DurableOrchestrationClient starter,
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

Při volání `Sample_HttpStart` Postman nebo prohlížeče, spustí odolné funkce pro odeslání události životního cyklu. Koncový bod se obvykle `http://localhost:7071/api/Sample_HttpStart` pro místní ladění.

Zobrazí protokoly z funkce, kterou jste vytvořili na webu Azure Portal.

```
2018-04-20T09:28:21.041 [Info] Function started (Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d)
2018-04-20T09:28:21.104 [Info] {
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
    "eventTime": "2018-04-20T09:28:19.6492068Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}

2018-04-20T09:28:21.104 [Info] Function completed (Success, Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d, Duration=65ms)
2018-04-20T09:28:37.098 [Info] Function started (Id=36fadea5-198b-4345-bb8e-2837febb89a2)
2018-04-20T09:28:37.098 [Info] {
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
    "eventTime": "2018-04-20T09:28:36.5061317Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}
2018-04-20T09:28:37.098 [Info] Function completed (Success, Id=36fadea5-198b-4345-bb8e-2837febb89a2, Duration=0ms)
```

## <a name="event-schema"></a>Schéma událostí

Následující seznam popisuje schéma události životního cyklu:

* **ID**: Jedinečný identifikátor události Event gridu.
* **Předmět**: cesta k předmětu událostí. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` bude `Running`, `Completed`, `Failed`, a `Terminated`.  
* **data**: trvalý specifických parametrů funkcí.
    * **hubName**: [TaskHub](https://docs.microsoft.com/azure/azure-functions/durable-functions-task-hubs) název.
    * **functionName**: název funkce nástroje Orchestrator.
    * **instanceId**: instanceId Durable Functions.
    * **z důvodu**: další data přidružená k události sledování. Další informace najdete v tématu [diagnostiky v Durable Functions (Azure Functions)](https://docs.microsoft.com/azure/azure-functions/durable-functions-diagnostics)
    * **runtimeStatus**: stav Runtime Orchestrace. Spuštěna, dokončena, se nezdařilo, protože bylo zrušeno. 
* **Typ eventType**: "orchestratorEvent"
* **čas události**: čas události (UTC).
* **dataVersion**: verze schématu událostí životního cyklu.
* **verze metadataVersion**: verze metadat.
* **téma**: EventGrid tématu prostředků.

## <a name="how-to-test-locally"></a>Místní testování

Chcete-li otestovat místně, použijte [ngrok](../functions-bindings-event-grid.md#local-testing-with-ngrok).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další Správa instancí v Durable Functions](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Další informace ve Durable Functions](durable-functions-versioning.md)
