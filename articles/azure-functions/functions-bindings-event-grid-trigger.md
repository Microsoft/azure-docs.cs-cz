---
title: Aktivační událost Azure Event Grid pro Azure Functions
description: Naučte se spouštět kód, když se Event Grid události v Azure Functions odesílají.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: devx-track-csharp, fasttrack-edit, devx-track-python
ms.openlocfilehash: 886db905008af94b66a902cc551e4d55b36572a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98250124"
---
# <a name="azure-event-grid-trigger-for-azure-functions"></a>Aktivační událost Azure Event Grid pro Azure Functions

Pomocí triggeru funkce můžete reagovat na událost odeslanou Event Grid tématu.

Informace o nastavení a podrobnostech o konfiguraci najdete v tématu [Přehled](./functions-bindings-event-grid.md).

## <a name="example"></a>Příklad

# <a name="c"></a>[C#](#tab/csharp)

Příklad triggeru HTTP najdete v tématu [příjem událostí do koncového bodu http](../event-grid/receive-events.md).

### <a name="c-2x-and-higher"></a>C# (2. x a vyšší)

Následující příklad ukazuje [funkci jazyka C#](functions-dotnet-class-library.md) , která se váže k `EventGridEvent` :

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

Další informace najdete v tématu balíčky, [atributy](#attributes-and-annotations), [Konfigurace](#configuration)a [použití](#usage).

### <a name="version-1x"></a>Verze 1. x

Následující příklad ukazuje funkce 1. x [C#](functions-dotnet-class-library.md) , která se váže k `JObject` :

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

Následující příklad ukazuje aktivační vazbu v *function.js* souboru a [funkci skriptu jazyka C#](functions-reference-csharp.md) , která používá vazbu.

Tady jsou data vazby v *function.js* souboru:

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

### <a name="version-2x-and-higher"></a>Verze 2. x a vyšší

Tady je příklad, na který se váže `EventGridEvent` :

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(EventGridEvent eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.Data.ToString());
}
```

Další informace najdete v tématu balíčky, [atributy](#attributes-and-annotations), [Konfigurace](#configuration)a [použití](#usage).

### <a name="version-1x"></a>Verze 1. x

Tady je funkce 1. x C# kód skriptu, ke kterému se váže `JObject` :

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

# <a name="java"></a>[Java](#tab/java)

Tato část obsahuje následující příklady:

* [Aktivační procedura Event Grid, řetězcový parametr](#event-grid-trigger-string-parameter)
* [Aktivační událost Event Grid, parametr POJO](#event-grid-trigger-pojo-parameter)

V následujících příkladech je znázorněna vazba triggeru v [jazyce Java](functions-reference-java.md) , která používá vazbu a vytiskne událost, nejprve přijme událost jako `String` Pojo a sekundy.

### <a name="event-grid-trigger-string-parameter"></a>Aktivační procedura Event Grid, řetězcový parametr

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

### <a name="event-grid-trigger-pojo-parameter"></a>Aktivační událost Event Grid, parametr POJO

V tomto příkladu se používá následující POJO, který představuje vlastnosti nejvyšší úrovně události Event Grid:

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

Po doručení je datová část JSON události deserializovaná do ```EventSchema``` Pojo pro použití funkcí. Tento proces umožňuje funkci přístup k vlastnostem události v objektově orientovaném způsobem.

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

V [knihovně modulu runtime Functions jazyka Java](/java/api/overview/azure/functions/runtime)použijte `EventGridTrigger` anotaci pro parametry, jejichž hodnota by pocházela z EventGrid. Parametry s těmito poznámkami způsobí, že se funkce spustí při přijetí události.  Tato poznámka se dá použít s nativními typy s možnou hodnotou null, Pojo nebo Nullable pomocí `Optional<T>` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje aktivační vazbu v *function.js* souboru a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu.

Tady jsou data vazby v *function.js* souboru:

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

Tady je kód JavaScriptu:

```javascript
module.exports = function (context, eventGridEvent) {
    context.log("JavaScript Event Grid function processed a request.");
    context.log("Subject: " + eventGridEvent.subject);
    context.log("Time: " + eventGridEvent.eventTime);
    context.log("Data: " + JSON.stringify(eventGridEvent.data));
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Následující příklad ukazuje, jak nakonfigurovat vazbu triggeru Event Grid v souboru *function.json* .

```powershell
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ]
}
```

Událost Event Grid zpřístupňuje funkci prostřednictvím parametru s názvem `eventGridEvent` , jak je znázorněno v následujícím příkladu PowerShellu.

```powershell
param($eventGridEvent, $TriggerMetadata)

# Make sure to pass hashtables to Out-String so they're logged correctly
$eventGridEvent | Out-String | Write-Host
```

# <a name="python"></a>[Python](#tab/python)

Následující příklad ukazuje aktivační vazbu v *function.js* souboru a [funkci Pythonu](functions-reference-python.md) , která používá vazbu.

Tady jsou data vazby v *function.js* souboru:

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

Tady je kód Pythonu:

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

---

## <a name="attributes-and-annotations"></a>Atributy a poznámky

# <a name="c"></a>[C#](#tab/csharp)

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte atribut [EventGridTrigger](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/TriggerBinding/EventGridTriggerAttribute.cs) .

Zde je `EventGridTrigger` atribut v signatuře metody:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, ILogger log)
{
    ...
}
```

Úplný příklad najdete v tématu příklad v jazyce C#.

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Skripty jazyka C# nepodporují atributy.

# <a name="java"></a>[Java](#tab/java)

[EventGridTrigger](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/EventGridTrigger.java) anotace umožňuje deklarativní konfiguraci vazby Event Grid poskytováním hodnot konfigurace. Další podrobnosti najdete v částech s [příkladem](#example) a [konfigurací](#configuration) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell nepodporuje atributy.

# <a name="python"></a>[Python](#tab/python)

Python nepodporuje atributy.

---

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.js* v souboru. Nejsou k dispozici žádné parametry konstruktoru nebo vlastnosti, které by bylo možné nastavit v `EventGridTrigger` atributu.

|function.jsvlastnost |Description|
|---------|---------|
| **textový** | Požadováno – musí být nastavené na `eventGridTrigger` . |
| **směr** | Požadováno – musí být nastavené na `in` . |
| **Jméno** | Required – název proměnné použitý v kódu funkce pro parametr, který přijímá data události. |

## <a name="usage"></a>Využití

# <a name="c"></a>[C#](#tab/csharp)

V Azure Functions 1. x můžete pro aktivační událost Event Grid použít následující typy parametrů:

* `JObject`
* `string`

V Azure Functions 2. x a vyšší máte také možnost pro aktivační událost Event Grid použít následující typ parametru:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`– Definuje vlastnosti pro pole společné pro všechny typy událostí.

> [!NOTE]
> Ve funkcích v1 Pokud se pokusíte vytvořit vazby na `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent` , kompilátor zobrazí "zastaralé" zprávy a pomůže vám `Microsoft.Azure.EventGrid.Models.EventGridEvent` místo toho použít. Chcete-li použít novější typ, odkazujte na balíček NuGet [Microsoft. Azure. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) a plně kvalifikováním `EventGridEvent` názvu typu pomocí předpony `Microsoft.Azure.EventGrid.Models` .

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

V Azure Functions 1. x můžete pro aktivační událost Event Grid použít následující typy parametrů:

* `JObject`
* `string`

V Azure Functions 2. x a vyšší máte také možnost pro aktivační událost Event Grid použít následující typ parametru:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`– Definuje vlastnosti pro pole společné pro všechny typy událostí.

> [!NOTE]
> Ve funkcích v1 Pokud se pokusíte vytvořit vazby na `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent` , kompilátor zobrazí "zastaralé" zprávy a pomůže vám `Microsoft.Azure.EventGrid.Models.EventGridEvent` místo toho použít. Chcete-li použít novější typ, odkazujte na balíček NuGet [Microsoft. Azure. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) a plně kvalifikováním `EventGridEvent` názvu typu pomocí předpony `Microsoft.Azure.EventGrid.Models` . Informace o tom, jak odkazovat na balíčky NuGet ve funkci skriptu jazyka C#, najdete v tématu [použití balíčků NuGet](functions-reference-csharp.md#using-nuget-packages) .

# <a name="java"></a>[Java](#tab/java)

Instance události Event Grid je k dispozici prostřednictvím parametru přidruženého k `EventGridTrigger` atributu, který je zadaný jako `EventSchema` . Další podrobnosti najdete v [příkladu](#example) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Instance Event Grid je k dispozici prostřednictvím parametru nakonfigurovaného ve vlastnosti *function.jsna* souboru `name` .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Instance Event Grid je k dispozici prostřednictvím parametru nakonfigurovaného ve vlastnosti *function.jsna* souboru `name` .

# <a name="python"></a>[Python](#tab/python)

Instance Event Grid je k dispozici prostřednictvím parametru nakonfigurovaného ve vlastnosti *function.jsu* souboru, který je `name` zadaný jako `func.EventGridEvent` .

---

## <a name="event-schema"></a>Schéma událostí

Data pro událost Event Grid se v těle požadavku HTTP přijímají jako objekt JSON. Formát JSON vypadá podobně jako v následujícím příkladu:

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

Zobrazený příklad je pole jednoho prvku. Event Grid vždy odesílá pole a může v poli poslat více než jednu událost. Modul runtime vyvolá funkci jednou pro každý prvek pole.

Vlastnosti nejvyšší úrovně v datech JSON události jsou stejné mezi všemi typy událostí, zatímco obsah `data` vlastnosti je specifický pro každý typ události. Zobrazený příklad je pro událost BLOB Storage.

Vysvětlení běžných vlastností a specifických pro události naleznete v tématu [vlastnosti události](../event-grid/event-schema.md#event-properties) v dokumentaci k Event Grid.

`EventGridEvent`Typ definuje pouze vlastnosti nejvyšší úrovně; `Data` vlastnost je `JObject` .

## <a name="create-a-subscription"></a>Vytvoření odběru

Pokud chcete začít přijímat Event Grid požadavky HTTP, vytvořte Event Grid předplatné, které určuje adresu URL koncového bodu, která funkci vyvolá.

### <a name="azure-portal"></a>portál Azure

U funkcí, které vyvíjíte v Azure Portal pomocí triggeru Event Grid vyberte možnost **integrace** a potom vyberte **aktivační událost Event Grid** a vyberte **vytvořit Event Grid předplatné**.

:::image type="content" source="media/functions-bindings-event-grid/portal-sub-create.png" alt-text="Připojte nové předplatné události, které se aktivuje na portálu.":::

Po výběru tohoto odkazu otevře portál stránku **vytvořit odběr události** s aktuálním koncovým bodem triggeru, který je již definován.

:::image type="content" source="media/functions-bindings-event-grid/endpoint-url.png" alt-text="Vytvoření odběru událostí s koncovým bodem funkce je už definované." :::

Další informace o tom, jak vytvořit odběry pomocí Azure Portal, najdete v tématu [Vytvoření vlastní události – Azure Portal](../event-grid/custom-event-quickstart-portal.md) v dokumentaci k Event Grid.

### <a name="azure-cli"></a>Azure CLI

Pokud chcete vytvořit předplatné pomocí [Azure CLI](/cli/azure/get-started-with-azure-cli), použijte příkaz [AZ eventgrid Event-Subscription Create](/cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create) .

Příkaz vyžaduje adresu URL koncového bodu, která vyvolá funkci. Následující příklad ukazuje vzor adresy URL pro konkrétní verzi:

#### <a name="version-2x-and-higher-runtime"></a>Modul runtime verze 2. x (a vyšší)

```http
https://{functionappname}.azurewebsites.net/runtime/webhooks/eventgrid?functionName={functionname}&code={systemkey}
```

#### <a name="version-1x-runtime"></a>Modul runtime verze 1. x

```http
https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}
```

Systémový klíč je autorizační klíč, který musí být zahrnutý do adresy URL koncového bodu pro aktivační událost Event Grid. Následující část vysvětluje, jak získat systémový klíč.

Tady je příklad, který se přihlásí k odběru účtu úložiště objektů BLOB (se zástupným symbolem pro systémový klíč):

#### <a name="version-2x-and-higher-runtime"></a>Modul runtime verze 2. x (a vyšší)

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
    --provider-namespace Microsoft.Storage --resource-type storageAccounts \
    --resource-name myblobstorage12345 --name myFuncSub \
    --included-event-types Microsoft.Storage.BlobCreated \
    --subject-begins-with /blobServices/default/containers/images/blobs/ \
    --endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup ^
    --provider-namespace Microsoft.Storage --resource-type storageAccounts ^
    --resource-name myblobstorage12345 --name myFuncSub ^
    --included-event-types Microsoft.Storage.BlobCreated ^
    --subject-begins-with /blobServices/default/containers/images/blobs/ ^
    --endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

---

#### <a name="version-1x-runtime"></a>Modul runtime verze 1. x

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
    --provider-namespace Microsoft.Storage --resource-type storageAccounts \
    --resource-name myblobstorage12345 --name myFuncSub \
    --included-event-types Microsoft.Storage.BlobCreated \
    --subject-begins-with /blobServices/default/containers/images/blobs/ \
    --endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup ^
    --provider-namespace Microsoft.Storage --resource-type storageAccounts ^
    --resource-name myblobstorage12345 --name myFuncSub ^
    --included-event-types Microsoft.Storage.BlobCreated ^
    --subject-begins-with /blobServices/default/containers/images/blobs/ ^
    --endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

---

Další informace o tom, jak vytvořit předplatné, najdete v tématu [rychlý Start úložiště objektů BLOB](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) nebo v ostatních Event Grid rychlých startech.

### <a name="get-the-system-key"></a>Získat systémový klíč

Systémový klíč můžete získat pomocí následujícího rozhraní API (HTTP GET):

#### <a name="version-2x-and-higher-runtime"></a>Modul runtime verze 2. x (a vyšší)

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgrid_extension?code={masterkey}
```

#### <a name="version-1x-runtime"></a>Modul runtime verze 1. x

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={masterkey}
```

Toto je rozhraní API pro správu, takže vyžaduje [hlavní klíč](functions-bindings-http-webhook-trigger.md#authorization-keys)aplikace Function App. Nepleťte si systémový klíč (pro vyvolání funkce triggeru Event Grid) s hlavním klíčem (pro provádění úloh správy v aplikaci Function App). Když se přihlásíte k odběru Event Gridho tématu, nezapomeňte použít systémový klíč.

Tady je příklad odpovědi, která poskytuje systémový klíč:

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

Hlavní klíč pro aplikaci Function App můžete získat z karty **nastavení aplikace Function App** na portálu.

> [!IMPORTANT]
> Hlavní klíč poskytuje přístup správcům k vaší aplikaci Function App. Tento klíč nesdílejte třetím stranám nebo ho distribuujte v nativních klientských aplikacích.

Další informace najdete v tématu [autorizační klíče](functions-bindings-http-webhook-trigger.md#authorization-keys) v referenčním článku o aktivačních událostech http.

Alternativně můžete odeslat příkaz HTTP PUT a zadat hodnotu klíče sami.

## <a name="local-testing-with-viewer-web-app"></a>Místní testování pomocí webové aplikace v prohlížeči

K otestování triggeru Event Grid v místním počítači musíte získat Event Grid požadavky HTTP, které jsou od svého původu v cloudu doručeny do místního počítače. Jedním ze způsobů, jak to udělat, je zachytávání požadavků online a ručním odesláním na místním počítači:

1. [Vytvořte webovou aplikaci v prohlížeči](#create-a-viewer-web-app) , která zachycuje zprávy událostí.
1. [Vytvořte předplatné Event Grid](#create-an-event-grid-subscription) , které odesílá události do aplikace prohlížeče.
1. [Vygenerujte požadavek](#generate-a-request) a zkopírujte text žádosti z aplikace pro prohlížeč.
1. [Požadavek odešlete ručně](#manually-post-the-request) na adresu URL místního hostitele funkce triggeru Event Grid.

Až budete s testováním hotovi, můžete použít stejné předplatné pro produkci aktualizací koncového bodu. Použijte příkaz [AZ eventgrid Event-Subscription aktualizace pro](/cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-update) Azure CLI.

### <a name="create-a-viewer-web-app"></a>Vytvoření webové aplikace v prohlížeči

Chcete-li zjednodušit zachytávání zpráv o událostech, můžete nasadit [předem vytvořenou webovou aplikaci](https://github.com/Azure-Samples/azure-event-grid-viewer) , která zobrazí zprávy o událostech. Nasazené řešení zahrnuje plán služby App Service, webovou aplikaci App Service a zdrojový kód z GitHubu.

Vyberte **Nasadit do Azure** a nasaďte řešení do svého předplatného. Na webu Azure Portal zadejte hodnoty pro parametry.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png" alt="Button to Deploy to Azure." /></a>

Dokončení nasazení může trvat několik minut. Po úspěšném nasazení si webovou aplikaci prohlédněte, abyste se ujistili, že funguje. Ve webovém prohlížeči přejděte na: `https://<your-site-name>.azurewebsites.net`

Zobrazí se web, na který se však zatím neodeslaly žádné události.

![Zobrazení nového webu](media/functions-bindings-event-grid/view-site.png)

### <a name="create-an-event-grid-subscription"></a>Vytvoření odběru Event Gridu

Vytvořte Event Grid předplatné typu, který chcete otestovat, a přiřaďte mu adresu URL z vaší webové aplikace jako koncový bod pro oznamování událostí. Koncový bod pro webovou aplikaci musí obsahovat příponu `/api/updates/`. Úplná adresa URL je tedy `https://<your-site-name>.azurewebsites.net/api/updates`

Informace o tom, jak vytvořit odběry pomocí Azure Portal, najdete v tématu [Vytvoření vlastní události – Azure Portal](../event-grid/custom-event-quickstart-portal.md) v dokumentaci k Event Grid.

### <a name="generate-a-request"></a>Vygenerovat žádost

Aktivujte událost, která bude generovat přenosy HTTP do koncového bodu webové aplikace.  Pokud jste například vytvořili odběr služby Blob Storage, nahrajte nebo odstraňte objekt BLOB. Po zobrazení žádosti ve webové aplikaci zkopírujte text žádosti.

Jako první se přijme žádost o ověření předplatného. Ignorujte všechny žádosti o ověření a zkopírujte požadavek na událost.

![Kopírovat text žádosti z webové aplikace](media/functions-bindings-event-grid/view-results.png)

### <a name="manually-post-the-request"></a>Ruční odeslání žádosti

Spusťte funkci Event Grid lokálně. `Content-Type`Hlavičky a `aeg-event-type` se musí nastavit ručně, zatímco všechny ostatní hodnoty můžou být ponechány jako výchozí.

K vytvoření požadavku HTTP POST použijte nástroj, jako je například [post](https://www.getpostman.com/) nebo [kudrlinkou](https://curl.haxx.se/docs/httpscripting.html) :

* Nastavte `Content-Type: application/json` hlavičku.
* Nastaví `aeg-event-type: Notification` hlavičku.
* Vložte RequestBin data do textu žádosti.
* Odešlete na adresu URL funkce triggeru Event Grid.
  * Pro 2. x a vyšší použijte následující vzor:

    ```
    http://localhost:7071/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
    ```

  * Pro 1. x použijte:

    ```
    http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
    ```

`functionName`Parametr musí být název zadaný v `FunctionName` atributu.

Následující snímky obrazovky ukazují záhlaví a text žádosti v poli post:

![Hlavičky v předzálohovacím](media/functions-bindings-event-grid/postman2.png)

![Text žádosti v příspěvku](media/functions-bindings-event-grid/postman.png)

Spustí se funkce triggeru Event Grid a zobrazí protokoly podobné následujícímu příkladu:

![Ukázka protokolů funkce triggeru Event Grid](media/functions-bindings-event-grid/eg-output.png)

## <a name="next-steps"></a>Další kroky

* [Odeslání události Event Grid](./functions-bindings-event-grid-output.md)
