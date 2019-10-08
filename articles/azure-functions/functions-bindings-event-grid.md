---
title: Aktivační událost Event Grid pro Azure Functions
description: Pochopte, jak zpracovávat události Event Grid v Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: ''
ms.service: azure-functions
ms.topic: reference
ms.date: 09/04/2018
ms.author: cshoe
ms.openlocfilehash: fbe41bdc5f253f1a605aa291a31191b7339b9850
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030578"
---
# <a name="event-grid-trigger-for-azure-functions"></a>Aktivační událost Event Grid pro Azure Functions

Tento článek vysvětluje, jak zpracovávat události [Event Grid](../event-grid/overview.md) v Azure Functions.

Event Grid je služba Azure, která odesílá požadavky HTTP, aby upozornila na události, ke kterým dochází ve *vydavatelích*. Vydavatel je služba nebo prostředek, který tuto událost vytvořil. Například účet Azure Blob Storage je Vydavatel a [odeslání nebo odstranění objektu BLOB je událost](../storage/blobs/storage-blob-event-overview.md). Některé [služby Azure mají integrovanou podporu pro publikování událostí do Event Grid](../event-grid/overview.md#event-sources).

*Obslužné rutiny* událostí přijímají a zpracovávají události. Azure Functions je jedna z několika [služeb Azure, které mají integrovanou podporu pro zpracování Event Gridch událostí](../event-grid/overview.md#event-handlers). V tomto článku se naučíte, jak pomocí triggeru Event Grid vyvolat funkci při přijetí události z Event Grid.

Pokud chcete, můžete použít Trigger HTTP pro zpracování událostí Event Grid; Viz část [použití triggeru http jako aktivační události Event Grid](#use-an-http-trigger-as-an-event-grid-trigger) dále v tomto článku. V současné době nemůžete použít Trigger Event Grid pro aplikaci Azure Functions, když se událost doručí ve [schématu CloudEvents](../event-grid/cloudevents-schema.md). Místo toho použijte Trigger HTTP.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Balíčky – funkce 2. x

Aktivační událost Event Grid je k dispozici v balíčku NuGet [Microsoft. Azure. WebJobs. Extensions. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) , verze 2. x. Zdrojový kód balíčku je v úložišti GitHub [Azure-Functions-eventgrid-Extension](https://github.com/Azure/azure-functions-eventgrid-extension/tree/v2.x) .

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="packages---functions-1x"></a>Balíčky – funkce 1. x

Aktivační událost Event Grid je k dispozici v balíčku NuGet [Microsoft. Azure. WebJobs. Extensions. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) , verze 1. x. Zdrojový kód balíčku je v úložišti GitHub [Azure-Functions-eventgrid-Extension](https://github.com/Azure/azure-functions-eventgrid-extension/tree/master) .

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="example"></a>Příklad:

Pro aktivační událost Event Grid můžete zobrazit příklad konkrétního jazyka:

* C#
* [C#skript (. csx)](#c-script-example)
* [Java](#trigger---java-examples)
* [JavaScript](#javascript-example)
* [Python](#python-example)

Příklad triggeru HTTP najdete v článku [Jak používat aktivační proceduru http](#use-an-http-trigger-as-an-event-grid-trigger) dále v tomto článku.

### <a name="c-2x"></a>C#(2. x)

Následující příklad ukazuje [ C# funkci](functions-dotnet-class-library.md) Functions 2. x, která se váže k `EventGridEvent`:

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

Další informace najdete v tématu balíčky, [atributy](#attributes), [Konfigurace](#configuration)a [použití](#usage).

### <a name="c-version-1x"></a>C#(Verze 1. x)

Následující příklad ukazuje [ C# funkci](functions-dotnet-class-library.md) funkce 1. x, která se váže k `JObject`:

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

### <a name="c-script-example"></a>C#Příklad skriptu

Následující příklad ukazuje aktivační vazbu v souboru *Function. JSON* a [ C# funkci skriptu](functions-reference-csharp.md) , která používá vazbu.

Tady jsou data vazby v souboru *Function. JSON* :

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

#### <a name="c-script-version-2x"></a>C#skript (verze 2. x)

Tady je funkce kódu skriptu 2 C# . x, která se váže k `EventGridEvent`:

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(EventGridEvent eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.Data.ToString());
}
```

Další informace najdete v tématu balíčky, [atributy](#attributes), [Konfigurace](#configuration)a [použití](#usage).

#### <a name="c-script-version-1x"></a>C#skript (verze 1. x)

Tady je funkční kód skriptu 1 C# . x, který se váže k `JObject`:

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

### <a name="javascript-example"></a>Příklad JavaScriptu

Následující příklad ukazuje aktivační vazbu v souboru *Function. JSON* a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu.

Tady jsou data vazby v souboru *Function. JSON* :

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

### <a name="python-example"></a>Příklad Pythonu

Následující příklad ukazuje aktivační vazbu v souboru *Function. JSON* a [funkci Pythonu](functions-reference-python.md) , která používá vazbu.

Tady jsou data vazby v souboru *Function. JSON* :

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
import logging
import azure.functions as func


def main(event: func.EventGridEvent):
    logging.info("Python Event Grid function processed a request.")
    logging.info("  Subject: %s", event.subject)
    logging.info("  Time: %s", event.event_time)
    logging.info("  Data: %s", event.get_json())
```

### <a name="trigger---java-examples"></a>Trigger – příklady jazyka Java

Tato část obsahuje následující příklady:

* [Aktivační procedura Event Grid, řetězcový parametr](#event-grid-trigger-string-parameter-java)
* [Aktivační událost Event Grid, parametr POJO](#event-grid-trigger-pojo-parameter-java)

Následující příklady znázorňují aktivační vazbu v souboru *Function. JSON* a [funkcích jazyka Java](functions-reference-java.md) , které používají vazbu a tisknou událost, nejprve obdrží událost jako ```String``` a druhá jako Pojo.

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ]
}
```

#### <a name="event-grid-trigger-string-parameter-java"></a>Aktivační událost Event Grid, řetězcový parametr (Java)

```java
  @FunctionName("eventGridMonitorString")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    String content, 
    final ExecutionContext context) {
      // log 
      context.getLogger().info("Event content: " + content);      
  }
```

#### <a name="event-grid-trigger-pojo-parameter-java"></a>Aktivační událost Event Grid, parametr POJO (Java)

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

Po doručení je datová část JSON události rozserializovaná do ```EventSchema``` POJO pro použití funkcí. To umožňuje funkci přistupovat k vlastnostem události v objektově orientovaném způsobem.

```java
  @FunctionName("eventGridMonitor")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    EventSchema event, 
    final ExecutionContext context) {
      // log 
      context.getLogger().info("Event content: ");
      context.getLogger().info("Subject: " + event.subject);
      context.getLogger().info("Time: " + event.eventTime); // automatically converted to Date by the runtime
      context.getLogger().info("Id: " + event.id);
      context.getLogger().info("Data: " + event.data);
  }
```

V [knihovně modulu runtime Functions jazyka Java](/java/api/overview/azure/functions/runtime)použijte anotaci `EventGridTrigger` u parametrů, jejichž hodnota by pocházela z EventGrid. Parametry s těmito poznámkami způsobí, že se funkce spustí při přijetí události.  Tuto poznámku lze použít s nativními typy Pojo, nebo s možnou hodnotou null pomocí `Optional<T>`.

## <a name="attributes"></a>Atributy

V [ C# knihovnách tříd](functions-dotnet-class-library.md)použijte atribut [EventGridTrigger](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/TriggerBinding/EventGridTriggerAttribute.cs) .

Tady je atribut `EventGridTrigger` v signatuře metody:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, ILogger log)
{
    ...
}
```

Úplný příklad naleznete v tématu C# příklad.

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v souboru *Function. JSON* . Nejsou k dispozici žádné parametry konstruktoru nebo vlastnosti, které by bylo možné nastavit v atributu `EventGridTrigger`.

|Function. JSON – vlastnost |Popis|
|---------|---------|
| **type** | Požadováno – musí být nastavené na `eventGridTrigger`. |
| **direction** | Požadováno – musí být nastavené na `in`. |
| **Jméno** | Required – název proměnné použitý v kódu funkce pro parametr, který přijímá data události. |

## <a name="usage"></a>Využití

Pro C# funkce F# a v Azure Functions 1. x můžete pro aktivační událost Event Grid použít následující typy parametrů:

* `JObject`
* `string`

Pro C# funkce F# a v Azure Functions 2. x máte také možnost pro aktivační událost Event Grid použít následující typ parametru:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent` – definuje vlastnosti pro pole společné pro všechny typy událostí.

> [!NOTE]
> Ve funkcích v1 Pokud se pokusíte vytvořit propojení s `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`, kompilátor zobrazí "zastaralé" zprávy a pomůže vám místo toho použít `Microsoft.Azure.EventGrid.Models.EventGridEvent`. Pokud chcete použít novější typ, odkazujte na balíček NuGet [Microsoft. Azure. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) a plně kvalifikovaného názvu typu `EventGridEvent` tím, že ho zadáte jako předponu `Microsoft.Azure.EventGrid.Models`. Informace o tom, jak odkazovat na balíčky NuGet ve C# funkci skriptu, najdete v tématu [použití balíčků NuGet](functions-reference-csharp.md#using-nuget-packages) .

Pro funkce JavaScriptu má parametr pojmenovaný vlastností *Function. json* `name` odkaz na objekt události.

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

Vlastnosti nejvyšší úrovně v datech JSON události jsou stejné mezi všemi typy událostí, zatímco obsah vlastnosti `data` je specifický pro každý typ události. Zobrazený příklad je pro událost BLOB Storage.

Vysvětlení běžných vlastností a specifických pro události naleznete v tématu [vlastnosti události](../event-grid/event-schema.md#event-properties) v dokumentaci k Event Grid.

Typ `EventGridEvent` definuje pouze vlastnosti nejvyšší úrovně; vlastnost `Data` je `JObject`.

## <a name="create-a-subscription"></a>Vytvoření odběru

Pokud chcete začít přijímat Event Grid požadavky HTTP, vytvořte Event Grid předplatné, které určuje adresu URL koncového bodu, která funkci vyvolá.

### <a name="azure-portal"></a>Portál Azure

U funkcí, které vyvíjíte v Azure Portal pomocí triggeru Event Grid vyberte **přidat Event Grid předplatné**.

![Vytvořit předplatné na portálu](media/functions-bindings-event-grid/portal-sub-create.png)

Po výběru tohoto odkazu otevře portál stránku **vytvořit odběr události** s adresou URL koncového bodu, která je předem vyplněna.

![Předem vyplněná adresa URL koncového bodu](media/functions-bindings-event-grid/endpoint-url.png)

Další informace o tom, jak vytvořit odběry pomocí Azure Portal, najdete v tématu [Vytvoření vlastní události – Azure Portal](../event-grid/custom-event-quickstart-portal.md) v dokumentaci k Event Grid.

### <a name="azure-cli"></a>Azure CLI

Pokud chcete vytvořit předplatné pomocí [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest), použijte příkaz [AZ eventgrid Event-Subscription Create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-create) .

Příkaz vyžaduje adresu URL koncového bodu, která vyvolá funkci. Následující příklad ukazuje vzor adresy URL pro konkrétní verzi:

#### <a name="version-2x-runtime"></a>Modul runtime verze 2. x

    https://{functionappname}.azurewebsites.net/runtime/webhooks/eventgrid?functionName={functionname}&code={systemkey}

#### <a name="version-1x-runtime"></a>Modul runtime verze 1. x

    https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}

Systémový klíč je autorizační klíč, který musí být zahrnutý do adresy URL koncového bodu pro aktivační událost Event Grid. Následující část vysvětluje, jak získat systémový klíč.

Tady je příklad, který se přihlásí k odběru účtu úložiště objektů BLOB (se zástupným symbolem pro systémový klíč):

#### <a name="version-2x-runtime"></a>Modul runtime verze 2. x

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

#### <a name="version-1x-runtime"></a>Modul runtime verze 1. x

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

Další informace o tom, jak vytvořit předplatné, najdete v tématu [rychlý Start úložiště objektů BLOB](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) nebo v ostatních Event Grid rychlých startech.

### <a name="get-the-system-key"></a>Získat systémový klíč

Systémový klíč můžete získat pomocí následujícího rozhraní API (HTTP GET):

#### <a name="version-2x-runtime"></a>Modul runtime verze 2. x

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgrid_extension?code={masterkey}
```

#### <a name="version-1x-runtime"></a>Modul runtime verze 1. x

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={masterkey}
```

Toto je rozhraní API pro správu, takže vyžaduje [hlavní klíč](functions-bindings-http-webhook.md#authorization-keys)aplikace Function App. Nepleťte si systémový klíč (pro vyvolání funkce triggeru Event Grid) s hlavním klíčem (pro provádění úloh správy v aplikaci Function App). Když se přihlásíte k odběru Event Gridho tématu, nezapomeňte použít systémový klíč.

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

Další informace najdete v tématu [autorizační klíče](functions-bindings-http-webhook.md#authorization-keys) v referenčním článku o aktivačních událostech http.

Alternativně můžete odeslat příkaz HTTP PUT a zadat hodnotu klíče sami.

## <a name="local-testing-with-viewer-web-app"></a>Místní testování pomocí webové aplikace v prohlížeči

K otestování triggeru Event Grid v místním počítači musíte získat Event Grid požadavky HTTP, které jsou od svého původu v cloudu doručeny do místního počítače. Jedním ze způsobů, jak to udělat, je zachytávání požadavků online a ručním odesláním na místním počítači:

1. [Vytvořte webovou aplikaci v prohlížeči](#create-a-viewer-web-app) , která zachycuje zprávy událostí.
1. [Vytvořte předplatné Event Grid](#create-an-event-grid-subscription) , které odesílá události do aplikace prohlížeče.
1. [Vygenerujte požadavek](#generate-a-request) a zkopírujte text žádosti z aplikace pro prohlížeč.
1. [Požadavek odešlete ručně](#manually-post-the-request) na adresu URL místního hostitele funkce triggeru Event Grid.

Až budete s testováním hotovi, můžete použít stejné předplatné pro produkci aktualizací koncového bodu. Použijte příkaz [AZ eventgrid Event-Subscription aktualizace pro](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-update) Azure CLI.

### <a name="create-a-viewer-web-app"></a>Vytvoření webové aplikace v prohlížeči

Chcete-li zjednodušit zachytávání zpráv o událostech, můžete nasadit [předem vytvořenou webovou aplikaci](https://github.com/Azure-Samples/azure-event-grid-viewer) , která zobrazí zprávy o událostech. Nasazené řešení zahrnuje plán služby App Service, webovou aplikaci App Service a zdrojový kód z GitHubu.

Vyberte **Nasadit do Azure** a nasaďte řešení do svého předplatného. Na webu Azure Portal zadejte hodnoty pro parametry.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

Dokončení nasazení může trvat několik minut. Po úspěšném nasazení si webovou aplikaci prohlédněte, abyste se ujistili, že funguje. Ve webovém prohlížeči přejděte na: `https://<your-site-name>.azurewebsites.net`

Zobrazí se web, na který se však zatím neodeslaly žádné události.

![Zobrazení nového webu](media/functions-bindings-event-grid/view-site.png)

### <a name="create-an-event-grid-subscription"></a>Vytvoření odběru Event Gridu

Vytvořte Event Grid předplatné typu, který chcete otestovat, a přiřaďte mu adresu URL z vaší webové aplikace jako koncový bod pro oznamování událostí. Koncový bod pro webovou aplikaci musí obsahovat příponu `/api/updates/`. Úplná adresa URL je tedy `https://<your-site-name>.azurewebsites.net/api/updates`.

Informace o tom, jak vytvořit odběry pomocí Azure Portal, najdete v tématu [Vytvoření vlastní události – Azure Portal](../event-grid/custom-event-quickstart-portal.md) v dokumentaci k Event Grid.

### <a name="generate-a-request"></a>Vygenerovat žádost

Aktivujte událost, která bude generovat přenosy HTTP do koncového bodu webové aplikace.  Pokud jste například vytvořili odběr služby Blob Storage, nahrajte nebo odstraňte objekt BLOB. Po zobrazení žádosti ve webové aplikaci zkopírujte text žádosti.

Jako první se přijme žádost o ověření předplatného. Ignorujte všechny žádosti o ověření a zkopírujte požadavek na událost.

![Kopírovat text žádosti z webové aplikace](media/functions-bindings-event-grid/view-results.png)

### <a name="manually-post-the-request"></a>Ruční odeslání žádosti

Spusťte funkci Event Grid lokálně.

K vytvoření požadavku HTTP POST použijte nástroj, jako je například [post](https://www.getpostman.com/) nebo [kudrlinkou](https://curl.haxx.se/docs/httpscripting.html) :

* Nastavte hlavičku `Content-Type: application/json`.
* Nastaví hlavičku `aeg-event-type: Notification`.
* Vložte RequestBin data do textu žádosti.
* Odešlete na adresu URL funkce triggeru Event Grid.
  * Pro 2. x použijte následující vzor:

    ```
    http://localhost:7071/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
    ```

  * Pro 1. x použijte:

    ```
    http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
    ```

Parametr `functionName` musí být název zadaný v atributu `FunctionName`.

Následující snímky obrazovky ukazují záhlaví a text žádosti v poli post:

![Hlavičky v předzálohovacím](media/functions-bindings-event-grid/postman2.png)

![Text žádosti v příspěvku](media/functions-bindings-event-grid/postman.png)

Spustí se funkce triggeru Event Grid a zobrazí protokoly podobné následujícímu příkladu:

![Ukázka protokolů funkce triggeru Event Grid](media/functions-bindings-event-grid/eg-output.png)

## <a name="local-testing-with-ngrok"></a>Místní testování pomocí ngrok

Dalším způsobem, jak místně otestovat Trigger Event Grid, je automatizace připojení HTTP mezi Internetem a vývojovým počítačem. Můžete to udělat pomocí nástroje, jako je [ngrok](https://ngrok.com/):

1. [Vytvořte koncový bod ngrok](#create-an-ngrok-endpoint).
1. [Spusťte funkci trigger Event Grid](#run-the-event-grid-trigger-function).
1. [Vytvořte předplatné Event Grid](#create-a-subscription) , které odesílá události do koncového bodu ngrok.
1. [Aktivovat událost](#trigger-an-event).

Až budete s testováním hotovi, můžete použít stejné předplatné pro produkci aktualizací koncového bodu. Použijte příkaz [AZ eventgrid Event-Subscription aktualizace pro](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-update) Azure CLI.

### <a name="create-an-ngrok-endpoint"></a>Vytvoření koncového bodu ngrok

Stáhněte si *ngrok. exe* z [ngrok](https://ngrok.com/)a spusťte příkaz s následujícím příkazem:

```
ngrok http -host-header=localhost 7071
```

Parametr-Host-Header je potřeba, protože běhový modul Functions očekává požadavky od místního hostitele, když běží na místním hostiteli. 7071 je výchozí číslo portu při spuštění v místním prostředí.

Příkaz vytvoří výstup podobný následujícímu:

```
Session Status                online
Version                       2.2.8
Region                        United States (us)
Web Interface                 http://127.0.0.1:4040
Forwarding                    http://263db807.ngrok.io -> localhost:7071
Forwarding                    https://263db807.ngrok.io -> localhost:7071

Connections                   ttl     opn     rt1     rt5     p50     p90
                              0       0       0.00    0.00    0.00    0.00
```

Pro Event Grid předplatné použijete adresu `https://{subdomain}.ngrok.io`.

### <a name="run-the-event-grid-trigger-function"></a>Spuštění funkce triggeru Event Grid

Adresa URL ngrok nezíská speciální zpracování Event Grid, takže při vytvoření předplatného musí být funkce spuštěná místně. Pokud tomu tak není, odpověď ověření se neodešle a vytvoření odběru se nepodaří.

### <a name="create-a-subscription"></a>Vytvoření odběru

Vytvořte Event Grid předplatné typu, který chcete otestovat, a poskytněte mu koncový bod ngrok.

Použijte tento vzor koncového bodu pro Functions 2. x:

```
https://{SUBDOMAIN}.ngrok.io/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
```

Pro funkce 1. x použijte tento vzor koncového bodu:

```
https://{SUBDOMAIN}.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
```

Parametr `{FUNCTION_NAME}` musí být název zadaný v atributu `FunctionName`.

Tady je příklad použití Azure CLI:

```azurecli
az eventgrid event-subscription create --resource-id /subscriptions/aeb4b7cb-b7cb-b7cb-b7cb-b7cbb6607f30/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstor0122 --name egblobsub0126 --endpoint https://263db807.ngrok.io/runtime/webhooks/eventgrid?functionName=EventGridTrigger
```

Informace o tom, jak vytvořit předplatné, najdete v části [vytvoření předplatného](#create-a-subscription) výše v tomto článku.

### <a name="trigger-an-event"></a>Aktivace události

Aktivujte událost, která bude generovat přenosy HTTP do koncového bodu ngrok.  Pokud jste například vytvořili odběr služby Blob Storage, nahrajte nebo odstraňte objekt BLOB.

Spustí se funkce triggeru Event Grid a zobrazí protokoly podobné následujícímu příkladu:

![Ukázka protokolů funkce triggeru Event Grid](media/functions-bindings-event-grid/eg-output.png)

## <a name="use-an-http-trigger-as-an-event-grid-trigger"></a>Použití triggeru HTTP jako triggeru Event Grid

Události Event Grid jsou přijímány jako požadavky HTTP, takže můžete zpracovávat události pomocí triggeru protokolu HTTP namísto triggeru Event Grid. Jedním z možných důvodů, jak získat větší kontrolu nad adresou URL koncového bodu, která vyvolá funkci. Dalším důvodem je, že potřebujete přijímat události ve [schématu CloudEvents](../event-grid/cloudevents-schema.md). V současné době aktivační událost Event Grid nepodporuje schéma CloudEvents. Příklady v této části ukazují řešení pro Event Grid schématu i schématu CloudEvents.

Pokud používáte Trigger HTTP, musíte napsat kód pro to, co aktivační událost Event Grid automaticky:

* Odešle odpověď ověření k žádosti o [ověření předplatného](../event-grid/security-authentication.md#webhook-event-delivery).
* Vyvolá funkci jednou pro každý prvek pole události obsaženého v textu požadavku.

Informace o adrese URL, která se má použít k místnímu vyvolání funkce nebo při spuštění v Azure, najdete v tématu [Referenční dokumentace k aktivačním vazbám http](functions-bindings-http-webhook.md) .

### <a name="event-grid-schema"></a>Schéma služby Event Grid

Následující vzorový C# kód triggeru HTTP simuluje chování triggeru Event Grid. Tento příklad slouží pro události doručené ve schématu Event Grid.

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequestMessage req,
    ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    var messages = await req.Content.ReadAsAsync<JArray>();

    // If the request is for subscription validation, send back the validation code.
    if (messages.Count > 0 && string.Equals((string)messages[0]["eventType"],
        "Microsoft.EventGrid.SubscriptionValidationEvent",
        System.StringComparison.OrdinalIgnoreCase))
    {
        log.LogInformation("Validate request received");
        return req.CreateResponse<object>(new
        {
            validationResponse = messages[0]["data"]["validationCode"]
        });
    }

    // The request is not for subscription validation, so it's for one or more events.
    foreach (JObject message in messages)
    {
        // Handle one event.
        EventGridEvent eventGridEvent = message.ToObject<EventGridEvent>();
        log.LogInformation($"Subject: {eventGridEvent.Subject}");
        log.LogInformation($"Time: {eventGridEvent.EventTime}");
        log.LogInformation($"Event data: {eventGridEvent.Data.ToString()}");
    }

    return req.CreateResponse(HttpStatusCode.OK);
}
```

Následující ukázkový kód JavaScriptu pro Trigger HTTP simuluje chování triggeru Event Grid. Tento příklad slouží pro události doručené ve schématu Event Grid.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    var messages = req.body;
    // If the request is for subscription validation, send back the validation code.
    if (messages.length > 0 && messages[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent") {
        context.log('Validate request received');
        var code = messages[0].data.validationCode;
        context.res = { status: 200, body: { "ValidationResponse": code } };
    }
    else {
        // The request is not for subscription validation, so it's for one or more events.
        // Event Grid schema delivers events in an array.
        for (var i = 0; i < messages.length; i++) {
            // Handle one event.
            var message = messages[i];
            context.log('Subject: ' + message.subject);
            context.log('Time: ' + message.eventTime);
            context.log('Data: ' + JSON.stringify(message.data));
        }
    }
    context.done();
};
```

Kód pro zpracování událostí projde uvnitř smyčky přes pole `messages`.

### <a name="cloudevents-schema"></a>CloudEvents schéma

Následující vzorový C# kód triggeru HTTP simuluje chování triggeru Event Grid.  Tento příklad slouží pro události doručené ve schématu CloudEvents.

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run([HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    var requestmessage = await req.Content.ReadAsStringAsync();
    var message = JToken.Parse(requestmessage);

    if (message.Type == JTokenType.Array)
    {
        // If the request is for subscription validation, send back the validation code.
        if (string.Equals((string)message[0]["eventType"],
        "Microsoft.EventGrid.SubscriptionValidationEvent",
        System.StringComparison.OrdinalIgnoreCase))
        {
            log.LogInformation("Validate request received");
            return req.CreateResponse<object>(new
            {
                validationResponse = message[0]["data"]["validationCode"]
            });
        }
    }
    else
    {
        // The request is not for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        log.LogInformation($"Source: {message["source"]}");
        log.LogInformation($"Time: {message["eventTime"]}");
        log.LogInformation($"Event data: {message["data"].ToString()}");
    }

    return req.CreateResponse(HttpStatusCode.OK);
}
```

Následující ukázkový kód JavaScriptu pro Trigger HTTP simuluje chování triggeru Event Grid. Tento příklad slouží pro události doručené ve schématu CloudEvents.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    var message = req.body;
    // If the request is for subscription validation, send back the validation code.
    if (message.length > 0 && message[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent") {
        context.log('Validate request received');
        var code = message[0].data.validationCode;
        context.res = { status: 200, body: { "ValidationResponse": code } };
    }
    else {
        // The request is not for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        var event = JSON.parse(message);
        context.log('Source: ' + event.source);
        context.log('Time: ' + event.eventTime);
        context.log('Data: ' + JSON.stringify(event.data));
    }
    context.done();
};
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech a vazbách Azure Functions](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Další informace o Event Grid](../event-grid/overview.md)
