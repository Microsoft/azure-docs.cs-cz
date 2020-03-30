---
title: Aktivační událost Azure Event Grid pro funkce Azure
description: Naučte se spouštět kód při odesílání událostí Grid v Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 2027629e1e9e297c97cbf40485ebe7dc2e3e6c0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277723"
---
# <a name="azure-event-grid-trigger-for-azure-functions"></a>Aktivační událost Azure Event Grid pro funkce Azure

Pomocí aktivační události na reakci na událost odeslanou do tématu Mřížky událostí.

Informace o nastavení a konfiguraci naleznete v [přehledu](./functions-bindings-event-grid.md).

## <a name="example"></a>Příklad

# <a name="c"></a>[C #](#tab/csharp)

Příklad aktivační události PROTOKOLU HTTP naleznete v tématu [Příjem událostí do koncového bodu HTTP](../event-grid/receive-events.md).

### <a name="c-2x-and-higher"></a>C# (2.x a vyšší)

Následující příklad ukazuje [funkci Jazyka C#,](functions-dotnet-class-library.md) která se váže na `EventGridEvent`:

```cs
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTest")]
        public static void EventGridTest([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

Další informace naleznete v tématu Balíčky, [atributy](#attributes-and-annotations), [Konfigurace](#configuration)a [Použití](#usage).

### <a name="version-1x"></a>Verze 1.x

Následující příklad ukazuje funkce Functions 1.x [C# funkce,](functions-dotnet-class-library.md) která se váže na `JObject`:

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Microsoft.Extensions.Logging;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTriggerCSharp")]
        public static void Run([EventGridTrigger]JObject eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
        }
    }
}
```

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Následující příklad ukazuje aktivační vazby v souboru *function.json* a [c# skript funkce,](functions-reference-csharp.md) která používá vazbu.

Zde jsou data vazby v souboru *function.json:*

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

### <a name="version-2x-and-higher"></a>Verze 2.x a vyšší

Zde je příklad, který `EventGridEvent`se váže na :

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(EventGridEvent eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.Data.ToString());
}
```

Další informace naleznete v tématu Balíčky, [atributy](#attributes-and-annotations), [Konfigurace](#configuration)a [Použití](#usage).

### <a name="version-1x"></a>Verze 1.x

Zde je funkce 1.x C# skript `JObject`kód, který váže na :

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

Následující příklad ukazuje aktivační vazbu v souboru *function.json* a [funkci JavaScript,](functions-reference-node.md) která používá vazbu.

Zde jsou data vazby v souboru *function.json:*

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Zde je kód JavaScript:

```javascript
module.exports = function (context, eventGridEvent) {
    context.log("JavaScript Event Grid function processed a request.");
    context.log("Subject: " + eventGridEvent.subject);
    context.log("Time: " + eventGridEvent.eventTime);
    context.log("Data: " + JSON.stringify(eventGridEvent.data));
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Následující příklad ukazuje aktivační vazby v souboru *function.json* a [funkce Pythonu,](functions-reference-python.md) který používá vazbu.

Zde jsou data vazby v souboru *function.json:*

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "event",
      "direction": "in"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Zde je kód Pythonu:

```python
import json
import logging

import azure.functions as func

def main(event: func.EventGridEvent):

    result = json.dumps({
        'id': event.id,
        'data': event.get_json(),
        'topic': event.topic,
        'subject': event.subject,
        'event_type': event.event_type,
    })

    logging.info('Python EventGrid trigger processed an event: %s', result)
```

# <a name="java"></a>[Java](#tab/java)

Tato část obsahuje následující příklady:

* [Aktivační událost mřížky událostí, parametr String](#event-grid-trigger-string-parameter)
* [Aktivační událost, parametr POJO](#event-grid-trigger-pojo-parameter)

Následující příklady ukazují aktivační vazbu v [jazyce Java,](functions-reference-java.md) které používají `String` vazbu a vytisknout událost, nejprve obdrží událost jako druhý jako POJO.

### <a name="event-grid-trigger-string-parameter"></a>Aktivační událost mřížky událostí, parametr String

```java
  @FunctionName("eventGridMonitorString")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    String content, 
    final ExecutionContext context) {
      context.getLogger().info("Event content: " + content);      
  }
```

### <a name="event-grid-trigger-pojo-parameter"></a>Aktivační událost, parametr POJO

Tento příklad používá následující POJO, představující vlastnosti nejvyšší úrovně události Event Grid:

```java
import java.util.Date;
import java.util.Map;

public class EventSchema {

  public String topic;
  public String subject;
  public String eventType;
  public Date eventTime;
  public String id;
  public String dataVersion;
  public String metadataVersion;
  public Map<String, Object> data;

}
```

Po příjezdu je datová část JSON události deserializována do ```EventSchema``` POJO pro použití funkcí. Tento proces umožňuje funkci přístup k vlastnostem události objektově orientovaným způsobem.

```java
  @FunctionName("eventGridMonitor")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    EventSchema event, 
    final ExecutionContext context) {
      context.getLogger().info("Event content: ");
      context.getLogger().info("Subject: " + event.subject);
      context.getLogger().info("Time: " + event.eventTime); // automatically converted to Date by the runtime
      context.getLogger().info("Id: " + event.id);
      context.getLogger().info("Data: " + event.data);
  }
```

V [knihovně runtime funkcí Javy](/java/api/overview/azure/functions/runtime)použijte poznámku `EventGridTrigger` k parametrům, jejichž hodnota by pocházela z EventGrid. Parametry s těmito poznámkami způsobit spuštění funkce při příchodu události.  Tuto poznámku lze použít s nativními typy Jazyka Java, `Optional<T>`POJOnebo hodnotami s možnou hodnotou s hodnotou s nulou pomocí .

---

## <a name="attributes-and-annotations"></a>Atributy a poznámky

# <a name="c"></a>[C #](#tab/csharp)

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte atribut [EventGridTrigger.](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/TriggerBinding/EventGridTriggerAttribute.cs)

Zde je `EventGridTrigger` atribut v podpisu metody:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, ILogger log)
{
    ...
}
```

Úplný příklad naleznete v příkladu jazyka C#.

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Atributy nejsou podporovány skriptem jazyka C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Atributy nejsou podporovány javascriptem.

# <a name="python"></a>[Python](#tab/python)

Atributy nejsou podporovány Pythonem.

# <a name="java"></a>[Java](#tab/java)

Anotace [EventGridTrigger](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/EventGridTrigger.java) umožňuje deklarativně konfigurovat vazbu Event Grid poskytnutím hodnot konfigurace. Další podrobnosti najdete v [příkladu](#example) a v částech [konfigurace.](#configuration)

---

## <a name="configuration"></a>Konfigurace

Následující tabulka vysvětluje vlastnosti konfigurace vazby, které jste nastavili v souboru *function.json.* V atributu nejsou nastaveny žádné parametry konstruktoru nebo vlastnosti. `EventGridTrigger`

|vlastnost function.json |Popis|
|---------|---------|
| **Typ** | Povinné - musí `eventGridTrigger`být nastavena na . |
| **direction** | Povinné - musí `in`být nastavena na . |
| **Jméno** | Povinné - název proměnné použitý v kódu funkce pro parametr, který přijímá data události. |

## <a name="usage"></a>Využití

# <a name="c"></a>[C #](#tab/csharp)

Ve službě Azure Functions 1.x můžete pro aktivační událost Mřížka událostí použít následující typy parametrů:

* `JObject`
* `string`

Ve službě Azure Functions 2.x a vyšší máte také možnost použít následující typ parametru pro aktivační událost mřížky událostí:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`- Definuje vlastnosti polí společných pro všechny typy událostí.

> [!NOTE]
> V funkce v1, pokud `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`se pokusíte vytvořit vazbu na , kompilátor zobrazí `Microsoft.Azure.EventGrid.Models.EventGridEvent` "zastaralé" zprávy a radí použít místo. Chcete-li použít novější typ, odkazujte na balíček [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet a plně kvalifikujte `EventGridEvent` název typu předponou . `Microsoft.Azure.EventGrid.Models`

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Ve službě Azure Functions 1.x můžete pro aktivační událost Mřížka událostí použít následující typy parametrů:

* `JObject`
* `string`

Ve službě Azure Functions 2.x a vyšší máte také možnost použít následující typ parametru pro aktivační událost mřížky událostí:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`- Definuje vlastnosti polí společných pro všechny typy událostí.

> [!NOTE]
> V funkce v1, pokud `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`se pokusíte vytvořit vazbu na , kompilátor zobrazí `Microsoft.Azure.EventGrid.Models.EventGridEvent` "zastaralé" zprávy a radí použít místo. Chcete-li použít novější typ, odkazujte na balíček [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet a plně kvalifikujte `EventGridEvent` název typu předponou . `Microsoft.Azure.EventGrid.Models` Informace o tom, jak odkazovat na balíčky NuGet ve funkci skriptu jazyka C#, naleznete [v tématu Použití balíčků NuGet](functions-reference-csharp.md#using-nuget-packages)

# <a name="javascript"></a>[Javascript](#tab/javascript)

Instance Event Grid je k dispozici prostřednictvím parametru nakonfigurovaného ve `name` vlastnosti souboru *function.json.*

# <a name="python"></a>[Python](#tab/python)

Instance Event Grid je k dispozici prostřednictvím parametru nakonfigurovaného `func.EventGridEvent`ve `name` vlastnosti souboru *function.json,* zadaný jako .

# <a name="java"></a>[Java](#tab/java)

Instance události Event Grid je k dispozici `EventGridTrigger` prostřednictvím parametru `EventSchema`přidruženého k atributu, zadaný jako . Další podrobnosti najdete v [příkladu.](#example)

---

## <a name="event-schema"></a>Schéma událostí

Data pro událost Event Grid jsou přijímána jako objekt JSON v těle požadavku HTTP. JSON vypadá podobně jako v následujícím příkladu:

```json
[{
  "topic": "/subscriptions/{subscriptionid}/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstore",
  "subject": "/blobServices/default/containers/{containername}/blobs/blobname.jpg",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2018-01-23T17:02:19.6069787Z",
  "id": "{guid}",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "{guid}",
    "requestId": "{guid}",
    "eTag": "0x8D562831044DDD0",
    "contentType": "application/octet-stream",
    "contentLength": 2248,
    "blobType": "BlockBlob",
    "url": "https://egblobstore.blob.core.windows.net/{containername}/blobname.jpg",
    "sequencer": "000000000000272D000000000003D60F",
    "storageDiagnostics": {
      "batchId": "{guid}"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Zobrazený příklad je pole jednoho prvku. Event Grid vždy odešle pole a může odeslat více než jednu událost v poli. Runtime vyvolá vaši funkci jednou pro každý prvek pole.

Vlastnosti nejvyšší úrovně v případě, že data JSON jsou stejná `data` mezi všemi typy událostí, zatímco obsah vlastnosti je specifický pro každý typ události. Zobrazený příklad je pro událost úložiště objektů blob.

Vysvětlení běžných vlastností a vlastností specifických pro událost naleznete v [tématu Vlastnosti události](../event-grid/event-schema.md#event-properties) v dokumentaci k programu Event Grid.

Typ `EventGridEvent` definuje pouze vlastnosti nejvyšší úrovně; `Data` vlastnost je `JObject`.

## <a name="create-a-subscription"></a>Vytvoření odběru

Chcete-li začít přijímat požadavky HTTP v programu Event Grid, vytvořte odběr mřížky událostí, které určuje adresu URL koncového bodu, která tuto funkci vyvolá.

### <a name="azure-portal"></a>portál Azure

U funkcí, které vyvíjíte na portálu Azure s aktivační událostí, vyberte **Přidat předplatné Event Grid**.

![Vytvoření předplatného na portálu](media/functions-bindings-event-grid/portal-sub-create.png)

Když vyberete tento odkaz, portál otevře stránku **Vytvořit odběr události** s předvyplněnou adresou URL koncového bodu.

![Adresa URL koncového bodu je předvyplněná](media/functions-bindings-event-grid/endpoint-url.png)

Další informace o tom, jak vytvořit předplatná pomocí portálu Azure, najdete v tématu [Vytvoření vlastní události – portálu Azure](../event-grid/custom-event-quickstart-portal.md) v dokumentaci k event gridu.

### <a name="azure-cli"></a>Azure CLI

Chcete-li vytvořit předplatné pomocí [příkazového příkazu Konto Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest), použijte příkaz [az eventgrid event-subscription create.](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-create)

Příkaz vyžaduje adresu URL koncového bodu, která vyvolá funkci. Následující příklad ukazuje vzor adresy URL specifické pro verzi:

#### <a name="version-2x-and-higher-runtime"></a>Doba runtime verze 2.x (a vyšší)

    https://{functionappname}.azurewebsites.net/runtime/webhooks/eventgrid?functionName={functionname}&code={systemkey}

#### <a name="version-1x-runtime"></a>Doba runtime verze 1.x

    https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}

Systémový klíč je autorizační klíč, který musí být zahrnut do adresy URL koncového bodu pro aktivační událost. V následující části je vysvětleno, jak získat systémový klíč.

Tady je příklad, který se přihlásí k odběru účtu úložiště objektů blob (se zástupným symbolem pro systémový klíč):

#### <a name="version-2x-and-higher-runtime"></a>Doba runtime verze 2.x (a vyšší)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

#### <a name="version-1x-runtime"></a>Doba runtime verze 1.x

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

Další informace o tom, jak vytvořit předplatné, najdete [v tématu rychlého startu úložiště objektů blob](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) nebo jiných rychlých startů eventgrid.

### <a name="get-the-system-key"></a>Získání systémového klíče

Systémový klíč můžete získat pomocí následujícího rozhraní API (HTTP GET):

#### <a name="version-2x-and-higher-runtime"></a>Doba runtime verze 2.x (a vyšší)

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgrid_extension?code={masterkey}
```

#### <a name="version-1x-runtime"></a>Doba runtime verze 1.x

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={masterkey}
```

Jedná se o rozhraní API pro správu, takže vyžaduje hlavní [klíč](functions-bindings-http-webhook-trigger.md#authorization-keys)aplikace funkce . Nepleťte si systémový klíč (pro vyvolání spouštěcí funkce Mřížka událostí) s hlavním klíčem (pro provádění úloh správy v aplikaci funkce). Když se přihlásíte k odběru tématu Event Grid, nezapomeňte použít systémový klíč.

Zde je příklad odpovědi, která poskytuje systémový klíč:

```
{
  "name": "eventgridextensionconfig_extension",
  "value": "{the system key for the function}",
  "links": [
    {
      "rel": "self",
      "href": "{the URL for the function, without the system key}"
    }
  ]
}
```

Hlavní klíč pro aplikaci funkcí můžete získat na kartě **Nastavení aplikace Funkce** na portálu.

> [!IMPORTANT]
> Hlavní klíč poskytuje administrátorovi přístup k vaší aplikaci funkcí. Nesdílejte tento klíč s třetími stranami ani jej nedistribuujte v nativních klientských aplikacích.

Další informace naleznete v [tématu Autorizace klíčů](functions-bindings-http-webhook-trigger.md#authorization-keys) v článku odkaz na aktivační událost HTTP.

Případně můžete odeslat HTTP PUT určit hodnotu klíče sami.

## <a name="local-testing-with-viewer-web-app"></a>Místní testování pomocí webové aplikace prohlížeče

Chcete-li otestovat aktivační událost grid místně, musíte získat požadavky HTTP Event Grid dodané z jejich původu v cloudu do místního počítače. Jedním ze způsobů, jak toho vychytávat žádosti online a ručně je odesílat do místního počítače:

1. [Vytvořte webovou aplikaci prohlížeče,](#create-a-viewer-web-app) která zachycuje zprávy událostí.
1. [Vytvořte odběr mřížky událostí,](#create-an-event-grid-subscription) které odesílá události do aplikace prohlížeče.
1. [Vygenerujte požadavek](#generate-a-request) a zkopírujte text požadavku z aplikace prohlížeče.
1. [Ručně zaúčtovat požadavek](#manually-post-the-request) na adresu URL localhost vaší aktivační funkce Event Grid.

Po dokončení testování můžete použít stejné předplatné pro produkční prostředí aktualizací koncového bodu. Použijte příkaz Azure CLI [aktualizace předplatného událostí az eventgrid.](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-update)

### <a name="create-a-viewer-web-app"></a>Vytvoření webové aplikace prohlížeče

Chcete-li zjednodušit zachytávání zpráv událostí, můžete nasadit [předem vytvořenou webovou aplikaci,](https://github.com/Azure-Samples/azure-event-grid-viewer) která zobrazuje zprávy o událostech. Nasazené řešení zahrnuje plán služby App Service, webovou aplikaci App Service a zdrojový kód z GitHubu.

Vyberte **Nasadit do Azure** a nasaďte řešení do svého předplatného. Na webu Azure Portal zadejte hodnoty pro parametry.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

Dokončení nasazení může trvat několik minut. Po úspěšném nasazení si webovou aplikaci prohlédněte, abyste se ujistili, že funguje. Ve webovém prohlížeči přejděte na: `https://<your-site-name>.azurewebsites.net`

Zobrazí se web, na který se však zatím neodeslaly žádné události.

![Zobrazení nového webu](media/functions-bindings-event-grid/view-site.png)

### <a name="create-an-event-grid-subscription"></a>Vytvoření odběru Event Gridu

Vytvořte odběr služby Event Grid typu, který chcete testovat, a přidejte mu adresu URL z webové aplikace jako koncový bod pro oznámení události. Koncový bod pro webovou aplikaci musí obsahovat příponu `/api/updates/`. Takže úplná adresa URL je`https://<your-site-name>.azurewebsites.net/api/updates`

Informace o tom, jak vytvořit předplatná pomocí portálu Azure, najdete v tématu [Vytvoření vlastní události – portálu Azure](../event-grid/custom-event-quickstart-portal.md) v dokumentaci k event gridu.

### <a name="generate-a-request"></a>Generovat požadavek

Aktivujte událost, která bude generovat přenosy HTTP do koncového bodu webové aplikace.  Pokud jste například vytvořili odběr úložiště objektů blob, nahrajte nebo odstraňte objekt blob. Když se ve webové aplikaci zobrazí žádost, zkopírujte text žádosti.

Žádost o ověření předplatného bude přijata jako první; ignorovat všechny požadavky na ověření a zkopírovat požadavek na událost.

![Kopírování textu požadavku z webové aplikace](media/functions-bindings-event-grid/view-results.png)

### <a name="manually-post-the-request"></a>Ruční zaúčtování požadavku

Spusťte funkci Event Grid místně.

K vytvoření požadavku HTTP POST použijte nástroj, například [Pošťák](https://www.getpostman.com/) nebo [curl:](https://curl.haxx.se/docs/httpscripting.html)

* Nastavte `Content-Type: application/json` záhlaví.
* Nastavte `aeg-event-type: Notification` záhlaví.
* Vložte data RequestBin do těla požadavku.
* Zaúčtovat na adresu URL aktivační funkce Mřížky událostí.
  * Pro 2.x a vyšší použijte následující vzor:

    ```
    http://localhost:7071/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
    ```

  * Pro použití 1.x:

    ```
    http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
    ```

Parametr `functionName` musí být název zadaný v atributu. `FunctionName`

Následující snímky obrazovky ukazují záhlaví a tělo požadavku v Postman:

![Záhlaví v Pořštič](media/functions-bindings-event-grid/postman2.png)

![Žádost tělo v Pošťák](media/functions-bindings-event-grid/postman.png)

Funkce aktivační události spustí a zobrazí protokoly podobné následujícímu příkladu:

![Protokoly aktivačních funkcí mřížky vzorových událostí](media/functions-bindings-event-grid/eg-output.png)

## <a name="next-steps"></a>Další kroky

* [Odeslání události Mřížky událostí](./functions-bindings-event-grid-trigger.md)
