---
title: Durable Functions publikování do Azure Event Grid (Preview)
description: Naučte se konfigurovat automatické publikování Azure Event Grid pro Durable Functions.
ms.topic: conceptual
ms.date: 04/25/2020
ms.openlocfilehash: 44df100a5c794abf918a09dea0f94d30ddf916d3
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175953"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Durable Functions publikování do Azure Event Grid (Preview)

Tento článek popisuje, jak nastavit Durable Functions pro publikování událostí životního cyklu orchestrace (například vytvoření, dokončení a selhání) do vlastního [tématu Azure Event Grid](../../event-grid/overview.md).

Tato funkce je užitečná v následujících situacích:

* **DevOps scénáře jako modrá/zelená nasazení**: možná budete chtít zjistit, jestli nějaké úlohy běží před implementací [Souběžné strategie nasazení](durable-functions-versioning.md#side-by-side-deployments).

* **Rozšířená podpora monitorování a diagnostiky**: informace o stavu orchestrace můžete sledovat v externím úložišti optimalizovaném pro dotazy, například Azure SQL Database nebo Azure Cosmos DB.

* **Dlouhodobě běžící aktivita na pozadí**: Pokud použijete Durable Functions pro dlouhou běžící aktivitu na pozadí, tato funkce vám pomůže seznámit se s aktuálním stavem.

## <a name="prerequisites"></a>Požadavky

* Do projektu Durable Functions nainstalujte [Microsoft. Azure. WebJobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) .
* Nainstalujte [emulátor Azure Storage](../../storage/common/storage-use-emulator.md) (jenom Windows) nebo použijte existující účet Azure Storage.
* Instalace rozhraní příkazového [řádku Azure](/cli/azure/) nebo použití [Azure Cloud Shell](../../cloud-shell/overview.md)

## <a name="create-a-custom-event-grid-topic"></a>Vytvoření vlastního Event Gridho tématu

Vytvoří Event Grid téma pro odesílání událostí z Durable Functions. Následující pokyny ukazují, jak vytvořit téma pomocí Azure CLI. Téma můžete také vytvořit pomocí [PowerShellu](../../event-grid/custom-event-quickstart-powershell.md) nebo [pomocí Azure Portal](../../event-grid/custom-event-quickstart-portal.md).

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Pomocí příkazu `az group create` vytvořte skupinu prostředků. V současné době Azure Event Grid nepodporuje všechny oblasti. Informace o podporovaných oblastech najdete v tématu [přehled Azure Event Grid](../../event-grid/overview.md).

```azurecli
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Vytvoření vlastního tématu

Event Grid téma poskytuje uživatelsky definovaný koncový bod, do kterého odesíláte událost. Nahraďte `<topic_name>` jedinečným názvem vašeho tématu. Název tématu musí být jedinečný, protože se jedná o položku DNS.

```azurecli
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>Získání koncového bodu a klíče

Získejte koncový bod tématu. Nahraďte `<topic_name>` názvem, který jste zvolili.

```azurecli
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

Získejte klíč tématu. Nahraďte `<topic_name>` názvem, který jste zvolili.

```azurecli
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Nyní můžete odesílat události do tématu.

## <a name="configure-event-grid-publishing"></a>Konfigurace publikování Event Grid

V projektu Durable Functions vyhledejte `host.json` soubor.

### <a name="durable-functions-1x"></a>Durable Functions 1. x

Přidejte `eventGridTopicEndpoint` a `eventGridKeySettingName` do `durableTask` Vlastnosti.

```json
{
  "durableTask": {
    "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName": "EventGridKey"
  }
}
```

### <a name="durable-functions-2x"></a>Durable Functions 2. x

Přidejte `notifications` oddíl do `durableTask` vlastnosti souboru a nahraďte ho názvem, `<topic_name>` který jste zvolili. Pokud `durableTask` vlastnosti nebo `extensions` neexistují, vytvořte je jako v tomto příkladu:

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "notifications": {
        "eventGrid": {
          "topicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
          "keySettingName": "EventGridKey"
        }
      }
    }
  }
}
```

Možné konfigurační vlastnosti Azure Event Grid najdete v [host.jsv dokumentaci](../functions-host-json.md#durabletask). Po dokončení konfigurace `host.json` souboru vaše aplikace Function App odešle události životního cyklu do tématu Event Grid. Tato akce se spustí, když aplikaci Function App spustíte místně i v Azure.

Nastavte nastavení aplikace pro klíč tématu v Function App a `local.settings.json` . Následující JSON je ukázka `local.settings.json` pro místní ladění. Nahraďte `<topic_key>` klíčem tématu.  

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

Pokud používáte [emulátor úložiště](../../storage/common/storage-use-emulator.md) (jenom Windows), ujistěte se, že funguje. Před spuštěním příkazu je vhodné spustit tento `AzureStorageEmulator.exe clear all` příkaz.

Pokud používáte existující účet Azure Storage, nahraďte ho `UseDevelopmentStorage=true` `local.settings.json` připojovacím řetězcem.

## <a name="create-functions-that-listen-for-events"></a>Vytvořit funkce, které naslouchají událostem

Pomocí Azure Portal vytvořte další aplikaci Function App, která bude naslouchat událostem publikovaným aplikací Durable Functions. Je nejvhodnější je najít ve stejné oblasti jako téma Event Grid.

### <a name="create-an-event-grid-trigger-function"></a>Vytvoření funkce triggeru Event Grid

1. Ve vaší aplikaci Function App vyberte **funkce** a pak vyberte **+ Přidat** . 

   :::image type="content" source="./media/durable-functions-event-publishing/function-add-function.png" alt-text="Přidejte funkci do Azure Portal." border="true":::

1. Vyhledejte **Event Grid** a pak vyberte šablonu **triggeru Azure Event Grid** . 

    :::image type="content" source="./media/durable-functions-event-publishing/function-select-event-grid-trigger.png" alt-text="Vyberte šablonu triggeru Event gridu v Azure Portal." border="true":::

1. Pojmenujte novou aktivační událost a pak vyberte **vytvořit funkci**.

    :::image type="content" source="./media/durable-functions-event-publishing/function-name-event-grid-trigger.png" alt-text="Pojmenujte Trigger Event gridu v Azure Portal." border="true":::


    Vytvoří se funkce s následujícím kódem:

    # <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

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

   # <a name="javascript"></a>[JavaScript](#tab/javascript)

   ```javascript
   module.exports = async function(context, eventGridEvent) {
       context.log(typeof eventGridEvent);
       context.log(eventGridEvent);
   }
   ```

---

### <a name="add-an-event-grid-subscription"></a>Přidání předplatného Event Grid

Nyní můžete přidat Event Grid předplatné pro Event Grid téma, které jste vytvořili. Další informace najdete v tématu [Koncepty v Azure Event Grid](../../event-grid/concepts.md).

1. V nové funkci vyberte **integrace** a pak vyberte **Event Grid Trigger (eventGridEvent)**. 

    :::image type="content" source="./media/durable-functions-event-publishing/eventgrid-trigger-link.png" alt-text="Vyberte odkaz Event Grid aktivační události." border="true":::

1. Vyberte **vytvořit Event Grid popis**.

    :::image type="content" source="./media/durable-functions-event-publishing/create-event-grid-subscription.png" alt-text="Vytvořte předplatné Event Grid." border="true":::

1. Pojmenujte své předplatné události a vyberte **Event Grid témata** typ tématu. 

1. Vyberte předplatné. Pak vyberte skupinu prostředků a prostředek, který jste vytvořili pro Event Grid téma. 

1. Vyberte **Vytvořit**.

    :::image type="content" source="./media/durable-functions-event-publishing/event-grid-subscription-details.png" alt-text="Vytvoří odběr Event Gridu." border="true":::

Teď jste připraveni přijímat události životního cyklu.

## <a name="run-durable-functions-app-to-send-the-events"></a>Spuštění aplikace Durable Functions pro odeslání událostí

V projektu Durable Functions, který jste nakonfigurovali dříve, spusťte ladění na místním počítači a spusťte orchestraci. Aplikace publikuje Durable Functions události životního cyklu do Event Grid. Ověřte, že Event Grid aktivuje funkci naslouchacího procesu, kterou jste vytvořili, kontrolou jejího protokolu v Azure Portal.

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

* **`id`**: Jedinečný identifikátor pro událost Event Grid.
* **`subject`**: Cesta k předmětu události. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` budou `Running` ,, `Completed` `Failed` a `Terminated` .  
* **`data`**: Durable Functions konkrétní parametry.
  * **`hubName`**: [TaskHub](durable-functions-task-hubs.md) název.
  * **`functionName`**: Název funkce nástroje Orchestrator.
  * **`instanceId`**: Durable Functions instanceId.
  * **`reason`**: Další data přidružená k události sledování. Další informace najdete v tématu [Diagnostika v Durable Functions (Azure Functions)](durable-functions-diagnostics.md) .
  * **`runtimeStatus`**: Stav běhu orchestrace. Spuštění, dokončení, selhání, zrušeno.
* **`eventType`**: "orchestratorEvent"
* **`eventTime`**: Čas události (UTC).
* **`dataVersion`**: Verze schématu událostí životního cyklu.
* **`metadataVersion`**: Verze metadat.
* **`topic`**: Prostředek tématu Event Grid.

## <a name="how-to-test-locally"></a>Jak místně testovat

Pokud chcete místně testovat test, přečtěte si téma [Azure Function Event Grid aktivovat místní ladění](../functions-debug-event-grid-trigger-local.md).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Naučte se správu instancí v Durable Functions](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Seznámení se správou verzí v Durable Functions](durable-functions-versioning.md)
