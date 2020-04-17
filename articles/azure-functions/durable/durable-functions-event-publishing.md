---
title: Publikování trvalých funkcí do Azure Event Grid (preview)
description: Přečtěte si, jak nakonfigurovat automatické publikování služby Azure Event Grid pro trvalé funkce.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 671f7bd5221a936ea9dad0f0cece895bdbe9512f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535481"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Publikování trvalých funkcí do Azure Event Grid (preview)

Tento článek ukazuje, jak nastavit trvalé funkce publikovat události životního cyklu orchestrace (například vytvořené, dokončené a neúspěšné) na vlastní [téma mřížky událostí Azure](https://docs.microsoft.com/azure/event-grid/overview).

Níže jsou uvedeny některé scénáře, kde je tato funkce užitečná:

* **Scénáře DevOps, jako je modrozelená nasazení**: Můžete chtít vědět, zda jsou spuštěny nějaké úlohy před implementací [strategie souběžného nasazení](durable-functions-versioning.md#side-by-side-deployments).

* **Pokročilá podpora monitorování a diagnostiky**: Můžete sledovat informace o stavu orchestrace v externím úložišti optimalizovaném pro dotazy, jako je Azure SQL Database nebo Azure Cosmos DB.

* **Dlouhotrvající aktivita na pozadí**: Pokud používáte trvalé funkce pro dlouhotrvající aktivitu na pozadí, tato funkce vám pomůže znát aktuální stav.

## <a name="prerequisites"></a>Požadavky

* Nainstalujte [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) v projektu durable functions.
* Nainstalujte [emulátor Úložiště Azure](../../storage/common/storage-use-emulator.md) (jenom Windows) nebo použijte existující účet Azure Storage.
* Instalace [azure cli](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) nebo použití [Azure Cloud Shellu](../../cloud-shell/overview.md)

## <a name="create-a-custom-event-grid-topic"></a>Vytvoření vlastního tématu Mřížka událostí

Vytvořte téma Mřížka událostí pro odesílání událostí z trvalých funkcí. Následující pokyny ukazují, jak vytvořit téma pomocí azure cli. Můžete to udělat také [pomocí PowerShellu](../../event-grid/custom-event-quickstart-powershell.md) nebo [pomocí portálu Azure](../../event-grid/custom-event-quickstart-portal.md).

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Pomocí příkazu `az group create` vytvořte skupinu prostředků. V současné době Azure Event Grid nepodporuje všechny oblasti. Informace o podporovaných oblastech najdete v přehledu [služby Azure Event Grid](../../event-grid/overview.md).

```azurecli
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Vytvoření vlastního tématu

Téma Mřížka událostí poskytuje uživatelem definovaný koncový bod, do kterého událost účtujete. Nahraďte `<topic_name>` jedinečným názvem vašeho tématu. Název tématu musí být jedinečný, protože se stane položkou DNS.

```azurecli
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>Získání koncového bodu a klíče

Získejte koncový bod tématu. Nahraďte `<topic_name>` zvoleným názvem.

```azurecli
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

Získejte klíč k tématu. Nahraďte `<topic_name>` zvoleným názvem.

```azurecli
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Nyní můžete odesílat události na téma.

## <a name="configure-event-grid-publishing"></a>Konfigurace publikování mřížky událostí

V projektu trvalé funkce `host.json` najděte soubor.

### <a name="durable-functions-1x"></a>Odolné funkce 1.x

Přidat `eventGridTopicEndpoint` `eventGridKeySettingName` a `durableTask` do vlastnosti.

```json
{
  "durableTask": {
    "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName": "EventGridKey"
  }
}
```

### <a name="durable-functions-2x"></a>Odolné funkce 2.x

Přidejte `notifications` oddíl `durableTask` do vlastnosti souboru `<topic_name>` a nahrazte jej zvoleným názvem. Pokud `durableTask` vlastnosti nebo `extensions` neexistují, vytvořte je v tomto příkladu:

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

Možné vlastnosti konfigurace služby Azure Event Grid najdete v [dokumentaci host.json](../functions-host-json.md#durabletask). Po konfiguraci `host.json` souboru aplikace funkce odešle události životního cyklu do tématu Mřížka událostí. To funguje, když spustíte aplikaci funkce místně i v Azure.

Nastavte nastavení aplikace pro klíč tématu `local.settings.json`v aplikaci Function App a . Následující JSON je ukázka `local.settings.json` pro místní ladění. Nahradit `<topic_key>` klávesou tématu.  

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

Pokud používáte [emulátor úložiště](../../storage/common/storage-use-emulator.md) (jenom Windows), ujistěte se, že funguje. Před spuštěním příkazu `AzureStorageEmulator.exe clear all` je vhodné spustit příkaz.

Pokud používáte existující účet Azure `UseDevelopmentStorage=true` Storage, nahraďte `local.settings.json` jej jeho připojovacím řetězcem.

## <a name="create-functions-that-listen-for-events"></a>Vytvořit funkce, které poslouchají události

Pomocí portálu Azure vytvořte další aplikaci funkcí, která bude naslouchat událostem publikovaným aplikací Durable Functions. Nejlepší je vyhledat ji ve stejné oblasti jako téma Mřížka událostí.

### <a name="create-an-event-grid-trigger-function"></a>Vytvoření aktivační funkce Mřížky událostí

Vytvořte funkci pro příjem událostí životního cyklu. Vyberte **vlastní funkci**.

![Vyberte možnost Vytvořit vlastní funkci.](./media/durable-functions-event-publishing/functions-portal.png)

Zvolte Aktivační událost mřížky událostí a vyberte jazyk.

![Vyberte aktivační událost mřížky událostí.](./media/durable-functions-event-publishing/eventgrid-trigger.png)

Zadejte název funkce a vyberte položku `Create`.

![Vytvořte aktivační událost mřížky událostí.](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

Je vytvořena funkce s následujícím kódem:

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

Vyberte `Add Event Grid Subscription`. Tato operace přidá odběr mřížky událostí pro téma Event Grid, které jste vytvořili. Další informace najdete [v tématu Koncepty ve službě Azure Event Grid.](https://docs.microsoft.com/azure/event-grid/concepts)

![Vyberte odkaz Aktivační událost mřížky událostí.](./media/durable-functions-event-publishing/eventgrid-trigger-link.png)

Tuto `Event Grid Topics` možnost vyberte pro **typ tématu**. Vyberte skupinu prostředků, kterou jste vytvořili pro téma Mřížka událostí. Pak vyberte instanci tématu Mřížka událostí. Stiskněte `Create`klávesu .

![Vytvoří odběr Event Gridu.](./media/durable-functions-event-publishing/eventsubscription.png)

Nyní jste připraveni přijímat události životního cyklu.

## <a name="run-durable-functions-app-to-send-the-events"></a>Spustit aplikaci Durable Functions pro odeslání událostí

V projektu trvalé funkce, který jste nakonfigurovali dříve, spusťte ladění v místním počítači a spusťte orchestraci. Aplikace publikuje události životního cyklu durable functions do event gridu. Ověřte, že Event Grid aktivuje naslouchací proces funkce, kterou jste vytvořili kontrolou jeho protokoly na portálu Azure.

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

* **`id`**: Jedinečný identifikátor události Event Grid.
* **`subject`**: Cesta k předmětu události. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}`bude `Running`, `Completed` `Failed`, `Terminated`, a .  
* **`data`**: Trvalé funkce specifické parametry.
  * **`hubName`**: [Název TaskHub.](durable-functions-task-hubs.md)
  * **`functionName`**: Název funkce Orchestrator.
  * **`instanceId`**: Trvalé funkce instanceId.
  * **`reason`**: Další data přidružená k události sledování. Další informace najdete [v tématu Diagnostika v trvanlivých funkcích (Funkce Azure)](durable-functions-diagnostics.md)
  * **`runtimeStatus`**: Stav běhu orchestrace. Spuštěno, dokončeno, nezdařilo se, bylo zrušeno.
* **`eventType`**: "orchestratorEvent"
* **`eventTime`**: Čas události (UTC).
* **`dataVersion`**: Verze schématu událostí životního cyklu.
* **`metadataVersion`**: Verze metadat.
* **`topic`**: Prostředek tématu mřížky událostí.

## <a name="how-to-test-locally"></a>Jak testovat lokálně

Chcete-li testovat místně, přečtěte si [Azure Funkce Event Grid Trigger místní ladění](../functions-debug-event-grid-trigger-local.md).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Naučte se správu instancí v trvalých funkcích](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Naučte se správu verzí v odolných funkcích](durable-functions-versioning.md)
