---
title: Trvale funkce publikování do mřížky událostí Azure (preview)
description: Naučte se konfigurovat automatické publikování mřížky událostí Azure pro odolná funkce.
services: functions
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/20/2018
ms.author: tdykstra
ms.openlocfilehash: 6e7fdd4faa4213681813733aa8afe81d56835862
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2018
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Trvale funkce publikování do mřížky událostí Azure (preview)

Tento článek ukazuje, jak nastavit trvanlivý Azure Functions k publikování událostí životního cyklu orchestration (například vytvoření dokončené a k selhání) do vlastní [téma mřížky události Azure](https://docs.microsoft.com/en-us/azure/event-grid/overview). 

Tady jsou některé scénáře, kde tato funkce je užitečná:

* **Jako DevOps scénáře nasazení modrá nebo zelená**: můžete chtít vědět, zda jsou všechny úlohy spuštěna před implementací [strategie nasazení vedle sebe](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-versioning#side-by-side-deployments).

* **Podpora monitorování a Diagnostika rozšířené**: můžete sledovat určité informace o stavu orchestration v externím obchodu optimalizované pro dotazy, jako je například SQL database nebo CosmosDB.

* **Aktivita dlouho běžící na pozadí**: Pokud používáte trvanlivý funkce pro aktivitu dlouho běžící pozadí, tato funkce vám pomůže vědět, aktuální stav.

## <a name="prerequisites"></a>Požadavky

* Nainstalujte [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) 1.3.0-rc nebo novější ve vašem projektu trvanlivý funkce.
* Nainstalujte [emulátoru úložiště Azure](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-emulator).
* Nainstalujte [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest) nebo použijte [prostředí cloudu Azure](https://docs.microsoft.com/en-us/azure/cloud-shell/overview)

## <a name="create-a-custom-event-grid-topic"></a>Vytvořit vlastní téma události mřížky

Vytvořte tématu událostí mřížky pro odeslání události z trvanlivý funkcí. Následující pokyny ukazují, jak vytvořit téma pomocí rozhraní příkazového řádku Azure. Informace o tom, jak to provést pomocí prostředí PowerShell nebo portálu Azure naleznete v následujících článcích:

* [EventGrid – elementy QuickStart: Vytvoření vlastní události – prostředí PowerShell](https://docs.microsoft.com/en-us/azure/event-grid/custom-event-quickstart-powershell)
* [EventGrid – elementy QuickStart: Vytvoření vlastní události – portál Azure](https://docs.microsoft.com/en-us/azure/event-grid/custom-event-quickstart-portal)

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků s `az group create` příkaz. V současné době nepodporuje mřížky událostí všech oblastech. Informace o tom, které jsou podporované oblasti najdete v tématu [Přehled událostí mřížky](https://docs.microsoft.com/en-us/azure/event-grid/overview). 

```bash
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Vytvoření vlastního tématu

Události mřížky téma obsahuje koncový bod definovaný uživatelem, který post vaše události. Nahraďte `<topic_name>` jedinečným názvem vašeho tématu. Název tématu musí být jedinečný, protože bude položka DNS.

```bash
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup 
```

## <a name="get-the-endpoint-and-key"></a>Získat koncový bod a klíč

Získáte koncový bod tohoto tématu. Nahraďte `<topic_name>` s vámi zvolený název.

```bash
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

Získáte klíč tématu. Nahraďte `<topic_name>` s vámi zvolený název.

```bash
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Teď může odesílat události do tématu.

## <a name="configure-azure-event-grid-publishing"></a>Konfigurace publikování Azure událostí mřížky

V projektu trvanlivý funkce Najít `host.json` souboru.

Přidat `EventGridTopicEndpoint` a `EventGridKeySettingName` v `durableTask` vlastnost.

```json
{
    "durableTask": {
        "EventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "EventGridKeySettingName": "EventGridKey"
    }
}
```

* **EventGridTopicEndpoint** -koncový bod téma události mřížky.
* **EventGridKeySettingName** -klíč nastavení aplikace v Azure funkce. Trvanlivý funkce získají klíč události tématu mřížky z hodnoty.

Jakmile nakonfigurujete `host.json` souboru projektu spustí vaše trvanlivý funkce odesílat události životního cyklu do tématu událostí mřížky. Toto funguje, když spustíte v aplikaci funkce a při spuštění místně.

Nastavení aplikace pro klíč tématu v aplikaci funkce a `local.setting.json`. Následujícím kódu JSON je ukázka `local.settings.json` pro místní ladění. Nahraďte `<topic_key>` s klíč tématu.  

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

Ujistěte se, že [emulátor úložiště](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-emulator) pracuje. Je vhodné spustit `AzureStorageEmulator.exe clear all` před provedením příkazu.

## <a name="create-functions-that-listen-for-events"></a>Vytvoření funkce, které naslouchání událostem

Vytvořte aplikaci funkce. Je nejlepší umístit ve stejné oblasti jako téma události mřížky.

### <a name="create-an-event-grid-trigger-function"></a>Vytvoření funkce aktivační událost mřížky

Vytvořte funkci pro příjem události životního cyklu. Vyberte **vlastní funkce**. 

![Vyberte možnost vytvořit vlastní funkce.](media/durable-functions-event-publishing/functions-portal.png)

Zvolte aktivační událost mřížky a vyberte `C#`.

![Vyberte mřížky aktivační události.](media/durable-functions-event-publishing/eventgrid-trigger.png)

Zadejte název funkce a potom vyberte `Create`.

![Vytvořte aktivační událost mřížky.](media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

Se vytvoří funkci s následujícím kódem: 

```csharp
#r "Newtonsoft.Json"
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

Vyberte `Add Event Grid Subscription`. Tato operace přidá předplatné mřížky událostí pro událost mřížky téma, které jste vytvořili. Další informace najdete v tématu [koncepty v mřížce událostí Azure](https://docs.microsoft.com/en-us/azure/event-grid/concepts)

![Vyberte odkaz aktivační událost mřížky.](media/durable-functions-event-publishing/eventgrid-trigger-link.png)

Vyberte `Event Grid Topics` pro **typu tématu**. Vyberte skupinu prostředků, kterou jste vytvořili pro téma události mřížky. Pak vyberte instanci mřížky události tématu. Stiskněte klávesu `Create`.

![Vytvoří odběr Event Gridu.](media/durable-functions-event-publishing/eventsubscription.png)

Teď můžete začít přijímat události životního cyklu. 

## <a name="create-durable-functions-to-send-the-events"></a>Vytvoření odolné funkce pro odeslání události.

V projektu trvanlivý funkce spusťte ladění na místním počítači.  Následující kód je stejný jako kód šablony pro odolná funkce. Jste již nakonfigurovali `host.json` a `local.settings.json` na místním počítači. 

```csharp
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;

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
        public static string SayHello([ActivityTrigger] string name, TraceWriter log)
        {
            log.Info($"Saying hello to {name}.");
            return $"Hello {name}!";
        }

        [FunctionName("Sample_HttpStart")]
        public static async Task<HttpResponseMessage> HttpStart(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
            [OrchestrationClient]DurableOrchestrationClient starter,
            TraceWriter log)
        {
            // Function input comes from the request content.
            string instanceId = await starter.StartNewAsync("Sample", null);
            log.Info($"Started orchestration with ID = '{instanceId}'.");
            return starter.CreateCheckStatusResponse(req, instanceId);
        }
    }
}
```

Když zavoláte `Sample_HttpStart` s Postman nebo prohlížeče, trvanlivý funkce spustí odesílat události životního cyklu. Koncový bod je obvykle `http://localhost:7071/api/Sample_HttpStart` pro místní ladění.

Najdete v souborech protokolů z funkce, kterou jste vytvořili na portálu Azure.

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
        "eventType": 0
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
        "eventType": 1
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2018-04-20T09:28:36.5061317Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}
2018-04-20T09:28:37.098 [Info] Function completed (Success, Id=36fadea5-198b-4345-bb8e-2837febb89a2, Duration=0ms)
```

## <a name="event-schema"></a>Schématu události

Následující seznam popisuje schéma události životního cyklu:

* **ID**: Jedinečný identifikátor pro událost událostí mřížky.
* **Předmět**: cesta k subjektu událostí. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` bude `Running`, `Completed`, `Failed`, a `Terminated`.  
* **data**: trvanlivý specifické parametry funkce.
    * **hubName**: [TaskHub](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-task-hubs) název.
    * **%{FunctionName/**: název funkce produktu Orchestrator.
    * **identifikátor instanceId**: identifikátor instanceId trvanlivý funkce.
    * **důvod**: další data přidružená k události sledování. Další informace najdete v tématu [diagnostiky trvanlivý funkcí (Azure Functions)](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-diagnostics)
    * **Typ události**: Orchestration běhový stav. 0: operačním systémem, 1: dokončení 2: ContinuedAsNew, 3: selhal, 4: zrušení 5: ukončeno, 6: čekající na vyřízení. 
* **Typ události**: "orchestratorEvent"
* **eventTime**: událost čas (UTC).
* **dataVersion**: verzi schématu události životního cyklu.
* **metadataVersion**: verze metadat.
* **téma**: EventGrid tématu prostředků.

## <a name="how-to-test-locally"></a>Postup testování místně

Chcete-li otestovat místně, použijte [ngrok](functions-bindings-event-grid.md#local-testing-with-ngrok).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další instance správu trvanlivý funkcí](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Další správu verzí v trvanlivý funkce](durable-functions-versioning.md)
