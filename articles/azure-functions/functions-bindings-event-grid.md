---
title: Aktivační událost mřížky pro Azure Functions
description: Pochopení způsobu zpracování události událostí mřížky v Azure Functions.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/26/2018
ms.author: tdykstra
ms.openlocfilehash: 9228b1e80c8c46780a24d33e13fcedbd8da63ac3
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="event-grid-trigger-for-azure-functions"></a>Aktivační událost mřížky pro Azure Functions

Tento článek vysvětluje, jak bude zpracováván [událostí mřížky](../event-grid/overview.md) události v Azure Functions.

Mřížky událostí je služba Azure, která odešle požadavky HTTP s oznámením o událostech, které ve dojde *vydavatelů*. Vydavatel je služba nebo prostředek, který pochází událost. Například účet úložiště objektů blob v Azure je vydavatel, a [nahrání objektu blob nebo odstranění je událost](../storage/blobs/storage-blob-event-overview.md). Některé [služby Azure mají integrovanou podporu pro publikování událostí do mřížky událostí](../event-grid/overview.md#event-sources). 

Událost *obslužné rutiny* přijímat a zpracovávat události. Azure Functions je jedním z několika [služby Azure, které mají integrovanou podporu pro zpracování událostí mřížky událostí](../event-grid/overview.md#event-handlers). V tomto článku zjistěte, jak použít aktivační událost INSTEAD mřížky události k vyvolání funkce při přijetí události z události mřížky.

Pokud dáváte přednost, můžete použít aktivační událost INSTEAD HTTP ke zpracování událostí mřížky událostí; v tématu [použít aktivační událost INSTEAD HTTP jako aktivační procedury událostí mřížky](#use-an-http-trigger-as-an-event-grid-trigger) dále v tomto článku.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>Balíčky

Je součástí aktivační událost mřížky [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) balíček NuGet. Zdrojový kód pro balíček je v [azure funkce eventgrid rozšíření](https://github.com/Azure/azure-functions-eventgrid-extension) úložiště GitHub.

<!--
If you want to bind to the `Microsoft.Azure.EventGrid.Models.EventGridEvent` type instead of `JObject`, install the [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) package.
-->

[!INCLUDE [functions-package](../../includes/functions-package.md)]

[!INCLUDE [functions-package-versions](../../includes/functions-package-versions.md)]

## <a name="example"></a>Příklad:

Podívejte se na příklad konkrétní jazyk pro aktivační procedury mřížky událostí:

* [C#](#c-example)
* [C# skript (.csx)](#c-script-example)
* [JavaScript](#javascript-example)

Příklad aktivace protokolu HTTP, naleznete v části [použití triggeru protokolu HTTP](#use-an-http-trigger-as-an-event-grid-trigger) dále v tomto článku.

### <a name="c-example"></a>Příklad jazyka C#

Následující příklad ukazuje [C# funkce](functions-dotnet-class-library.md) která sváže `JObject`:

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTriggerCSharp")]
        public static void Run([EventGridTrigger]JObject eventGridEvent, TraceWriter log)
        {
            log.Info(eventGridEvent.ToString(Formatting.Indented));
        }
    }
}
```

<!--
The following example shows a [C# function](functions-dotnet-class-library.md) that binds to `EventGridEvent`:

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTest")]
            public static void EventGridTest([EventGridTrigger] Microsoft.Azure.EventGrid.Models.EventGridEvent eventGridEvent, TraceWriter log)
        {
            log.Info("C# Event Grid function processed a request.");
            log.Info($"Subject: {eventGridEvent.Subject}");
            log.Info($"Time: {eventGridEvent.EventTime}");
            log.Info($"Data: {eventGridEvent.Data.ToString()}");
        }
    }
}
```
-->

Další informace najdete v tématu [balíčky](#packages), [atributy](#attributes), [konfigurace](#configuration), a [využití](#usage).

### <a name="c-script-example"></a>Příklad skriptu jazyka C#

Následující příklad ukazuje, aktivační události vazby v *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) používající vazby.

Zde je vazba dat v *function.json* souboru:

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

Zde je kód C# skript s vazbou na `JObject`:

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

<!--
Here's C# script code that binds to `EventGridEvent`:

```csharp
#r "Newtonsoft.Json"
#r "Microsoft.Azure.WebJobs.Extensions.EventGrid"
#r "Microsoft.Azure.EventGrid"

using Microsoft.Azure.WebJobs.Extensions.EventGrid;
Using Microsoft.Azure.EventGrid.Models;

public static void Run(EventGridEvent eventGridEvent, TraceWriter log)
{
    log.Info("C# Event Grid function processed a request.");
    log.Info($"Subject: {eventGridEvent.Subject}");
    log.Info($"Time: {eventGridEvent.EventTime}");
    log.Info($"Data: {eventGridEvent.Data.ToString()}");
}
```
-->

Další informace najdete v tématu [balíčky](#packages), [atributy](#attributes), [konfigurace](#configuration), a [využití](#usage).

### <a name="javascript-example"></a>Příklad v jazyce JavaScript

Následující příklad ukazuje, aktivační události vazby v *function.json* souboru a [funkce JavaScript, která](functions-reference-node.md) používající vazby.

Zde je vazba dat v *function.json* souboru:

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

Tady je kód jazyka JavaScript:

```javascript
module.exports = function (context, eventGridEvent) {
    context.log("JavaScript Event Grid function processed a request.");
    context.log("Subject: " + eventGridEvent.subject);
    context.log("Time: " + eventGridEvent.eventTime);
    context.log("Data: " + JSON.stringify(eventGridEvent.data));
    context.done();
};
```
     
## <a name="attributes"></a>Atributy

V [knihovny tříd jazyka C#](functions-dotnet-class-library.md), použijte [EventGridTrigger](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/EventGridTriggerAttribute.cs) atribut.

Tady je `EventGridTrigger` atribut v podpis metody:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, TraceWriter log)
{
    ...
}
 ```

Úplný příklad najdete v tématu [příklad jazyka C#](#c-example).

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru. Neexistují žádné parametry konstruktor nebo vlastnosti nastavit na `EventGridTrigger` atribut.

|Vlastnost Function.JSON |Popis|
|---------|---------|----------------------|
| **type** | Vyžaduje - musí být nastavena na `eventGridTrigger`. |
| **direction** | Vyžaduje - musí být nastavena na `in`. |
| **Jméno** | Požadovaná proměnná používá v kódu funkce pro parametr, který přijímá data události. |

## <a name="usage"></a>Využití

Pro C# a F # funkce můžete použít následující typy parametrů pro aktivační událost mřížky událostí:

* `JObject`
* `string`
* `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`-Definuje vlastnosti pro pole, které jsou společné pro všechny typy událostí. **Tento typ je zastaralý**, ale jeho náhrada není dosud publikován na NuGet.

Pro funkce jazyka JavaScript, parametr s názvem podle *function.json* `name` vlastnost obsahuje odkaz na objekt události.

## <a name="event-schema"></a>Schéma událostí

Přijatá data pro událost mřížky událostí jako objekt JSON v textu požadavku HTTP. JSON vypadá podobně jako v následujícím příkladu:

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

Příkladu je pole jeden element. Událost mřížky vždy odešle pole a mohou odesílat více než jednu událost v poli. Modul runtime vyvolá funkce jednou pro každý element pole.

Vlastnosti nejvyšší úrovně v události JSON data jsou stejné mezi všechny typy událostí, při obsah `data` vlastnost jsou specifické pro každý typ události. Příkladu je pro událost úložiště objektů blob.

Vysvětlení vlastností běžné a konkrétní události, najdete v části [vlastnosti události](../event-grid/event-schema.md#event-properties) v dokumentaci k události mřížky.

`EventGridEvent` Typ definuje pouze nejvyšší úrovně vlastnosti; `Data` vlastnost je `JObject`. 

## <a name="create-a-subscription"></a>Vytvoření odběru

Chcete-li začít přijímat požadavky mřížky HTTP událostí, vytvořte předplatné mřížky událostí, který určuje adresu URL koncového bodu, která volá funkci.

### <a name="azure-portal"></a>Azure Portal

Pro funkce, které vyvíjíte na portálu Azure s aktivační událostí mřížky, vyberte **předplatné přidat mřížky událostí**.

![Vytvoření odběru na portálu](media/functions-bindings-event-grid/portal-sub-create.png)

Když vyberete tento odkaz, portál ho otevře **vytvořit odběr událostí** předem stránka s adresu URL koncového bodu.

![Adresa URL koncového bodu předem](media/functions-bindings-event-grid/endpoint-url.png)

Další informace o tom, jak vytvářet odběry pomocí portálu Azure najdete v tématu [vytvořit vlastní událost - portál Azure](../event-grid/custom-event-quickstart-portal.md) v dokumentaci k události mřížky.

### <a name="azure-cli"></a>Azure CLI

Vytvořit odběr pomocí [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest), použijte [vytvoření předplatného se službou az eventgrid události](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_create) příkaz.

Příkaz vyžaduje adresu URL koncového bodu, která volá funkci. Následující příklad ukazuje vzor adresy URL:

```
https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}
```

Klíč systému je autorizační klíč, který musí být součástí adresu URL koncového bodu pro aktivační procedury událostí mřížky. V následující části vysvětluje, jak získat klíč systému.

Tady je příklad, který se přihlásí k účtu úložiště blob (s zástupný symbol pro systémový klíč):

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name glengablobstorage --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://glengastorageevents.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=LUwlnhIsNtSiUjv/sNtSiUjvsNtSiUjvsNtSiUjvYb7XDonDUr/RUg==
```

Další informace o tom, jak vytvořit odběr najdete v tématu [rychlý start úložiště objektů blob](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) nebo jiných quickstarts událostí mřížky.

### <a name="get-the-system-key"></a>Získat klíč systému

Systémový klíč můžete získat pomocí následující rozhraní API (HTTP GET):

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={adminkey}
```

Toto je správce rozhraní API, takže vyžaduje vaše [klíč správce](functions-bindings-http-webhook.md#authorization-keys). Nepleťte si systémový klíč (pro vyvolání funkce aktivační událost mřížky) s klíče správce (pro provádění úloh správy v aplikaci funkce). Když se přihlásíte tématu událostí mřížky, nezapomeňte použít systémový klíč.

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

Další informace najdete v tématu [autorizace klíče](functions-bindings-http-webhook.md#authorization-keys) v článku odkaz aktivace protokolu HTTP. 

Alternativně můžete odeslat HTTP PUT se zadat hodnotu klíče sami.

## <a name="local-testing-with-requestbin"></a>Místní testování pomocí RequestBin

> [!NOTE]
> RequestBin lokality není momentálně k dispozici, ale můžete použít tento způsob se https://hookbin.com místo. Pokud této lokality je vypnutý, můžete použít [ngrok](#local-testing-with-ngrok).

K testování aktivační procedury událostí mřížky místně, musíte získat požadavků HTTP mřížky událostí doručit od jejich původ v cloudu do místního počítače. Jeden způsob, jak to udělat, je zachycení požadavků online a ručně je odešlete na místním počítači:

2. [Vytvoření koncového bodu RequestBin](#create-a-RequestBin-endpoint).
3. [Předplatné událostí mřížky, vytvořte](#create-an-event-grid-subscription) která odesílá události RequestBin koncový bod.
4. [Generování žádosti](#generate-a-request) a zkopírujte z webu RequestBin textu požadavku.
5. [Ručně odeslat žádost](#manually-post-the-request) na adresu URL místního hostitele mřížky událost aktivaci funkce.

Po dokončení testování, můžete použít stejné předplatné pro produkční prostředí tím, že aktualizuje koncový bod. Použití [az eventgrid předplatného události aktualizace](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update) příkazu příkazového řádku Azure CLI.

### <a name="create-a-requestbin-endpoint"></a>Vytvoření koncového bodu RequestBin

RequestBin je otevřený nástroj, který přijímá požadavky protokolu HTTP a textu žádosti se dozvíte. http://requestb.in Zvláštní zacházení podle mřížky událostí Azure získá adresu URL. Usnadňuje testování mřížky událostí odesílá události na adresu URL RequestBin bez nutnosti správné odpovědí na požadavky na ověření předplatného. Jeden jiný nástroj testování se zpracuje stejným způsobem: http://hookbin.com.

RequestBin není určen pro použití vysoké propustnosti. Pokud najednou nabídnete více než jednu událost, možná se v nástroji nezobrazí všechny.

Vytvoření koncového bodu.

![Vytvoření koncového bodu RequestBin](media/functions-bindings-event-grid/create-requestbin.png)

Zkopírujte adresu URL koncového bodu.

![Zkopírujte RequestBin koncový bod](media/functions-bindings-event-grid/save-requestbin-url.png)

### <a name="create-an-event-grid-subscription"></a>Vytvořte předplatné událostí mřížky

Předplatné mřížky události typu, který chcete testovat, vytvořte a dejte mu RequestBin koncový bod. Informace o tom, jak vytvořit odběr najdete v tématu [vytvořit odběr](#create-a-subscription) výše v tomto článku.

### <a name="generate-a-request"></a>Generování žádosti

Aktivovat událost, která bude vytvářet provozu HTTP na koncový bod RequestBin.  Například pokud vytvoříte odběr úložiště objektů blob, nahrát nebo odstranit objekt blob. Při požadavku se zobrazí v stránku RequestBin, zkopírujte textu požadavku.

Požadavek na ověření předplatného bude přijímat nejprve; Ignorovat všechny žádosti o ověření a zkopírujte požadavek události.

![Zkopírujte text žádosti z RequestBin](media/functions-bindings-event-grid/copy-request-body.png)

### <a name="manually-post-the-request"></a>Ručně odeslat požadavek

Funkce mřížky událostí spusťte místně.

Pomocí některého nástroje, jako například [Postman](https://www.getpostman.com/) nebo [curl](https://curl.haxx.se/docs/httpscripting.html) vytvořit požadavek HTTP POST:

* Nastavte `Content-Type: application/json` záhlaví.
* Nastavit `aeg-event-type: Notification` záhlaví.
* Vložení dat RequestBin do textu žádosti. 
* POST na adresu URL funkce aktivační událost mřížky, pomocí následujícího vzorce:

```
http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={functionname}
``` 

`functionName` Parametr musí být název zadaný v `FunctionName` atribut.

Na následujících snímcích obrazovky zobrazit záhlaví a text v Postman žádosti:

![Hlavičky v Postman](media/functions-bindings-event-grid/postman2.png)

![Text v Postman žádosti](media/functions-bindings-event-grid/postman.png)

Funkce aktivační událost mřížky spustí a ukazuje protokoly, podobně jako v následujícím příkladu:

![Ukázka události mřížky aktivační událost funkce protokoly](media/functions-bindings-event-grid/eg-output.png)

## <a name="local-testing-with-ngrok"></a>Místní testování pomocí ngrok

Jiný způsob, jak otestovat aktivační procedury událostí mřížky místně, je k automatizaci připojení protokolu HTTP mezi Internetu a vývojovém počítači. Můžete to udělat pomocí nástrojů pro open source s názvem [ngrok](https://ngrok.com/):

3. [Vytvoření koncového bodu ngrok](#create-an-ngrok-endpoint).
4. [Spuštění funkce aktivační událost mřížky](#run-the-event-grid-trigger-function).
5. [Předplatné událostí mřížky, vytvořte](#create-a-subscription) která odesílá události ngrok koncový bod.
6. [Aktivovat událost](#trigger-an-event).

Po dokončení testování, můžete použít stejné předplatné pro produkční prostředí tím, že aktualizuje koncový bod. Použití [az eventgrid předplatného události aktualizace](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update) příkazu příkazového řádku Azure CLI.

### <a name="create-an-ngrok-endpoint"></a>Vytvořit koncový bod ngrok

Stáhněte si *ngrok.exe* z [ngrok](https://ngrok.com/)a spusťte pomocí následujícího příkazu:

```
ngrok http -host-header=localhost 7071
```

-Host-záhlaví parametr je potřeba, protože functions runtime očekává požadavky od localhost, když je spuštěna na místním hostiteli. 7071 je výchozí číslo portu při spuštění modulu runtime místně.

Příkaz vytvoří výstupní takto:

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

Použijete adresu URL https://{subdomain}.ngrok.io pro vaše předplatné událostí mřížky.

### <a name="run-the-event-grid-trigger-function"></a>Spuštění funkce aktivační událost mřížky

Adresa URL ngrok není získat zvláštní zpracování podle událostí mřížky, tak funkce musí být spuštěna místně, při vytvoření odběru. Pokud tomu tak není, odpověď ověřování není odeslána a vytváření odběru se nezdaří.

### <a name="create-a-subscription"></a>Vytvoření odběru

Vytvoření předplatné mřížky události typu, který chcete testovat a pojmenujte ho váš koncový bod ngrok, pomocí následujícího vzorce:

```
https://{subdomain}.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName={functionname}
``` 

`functionName` Parametr musí být název zadaný v `FunctionName` atribut.

Tady je příklad použití Azure CLI:

```
az eventgrid event-subscription create --resource-id /subscriptions/aeb4b7cb-b7cb-b7cb-b7cb-b7cbb6607f30/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstor0122 --name egblobsub0126 --endpoint https://263db807.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName=EventGridTrigger
```

Informace o tom, jak vytvořit odběr najdete v tématu [vytvořit odběr](#create-a-subscription) výše v tomto článku.

### <a name="trigger-an-event"></a>Aktivace události

Aktivovat událost, která bude vytvářet provozu HTTP na koncový bod ngrok.  Například pokud vytvoříte odběr úložiště objektů blob, nahrát nebo odstranit objekt blob.

Funkce aktivační událost mřížky spustí a ukazuje protokoly, podobně jako v následujícím příkladu:

![Ukázka události mřížky aktivační událost funkce protokoly](media/functions-bindings-event-grid/eg-output.png)

## <a name="use-an-http-trigger-as-an-event-grid-trigger"></a>Použít aktivační událost INSTEAD HTTP jako aktivační procedury událostí mřížky

Přijetí události mřížky událostí jako požadavky HTTP, může zpracovat události pomocí aktivační procedury protokolu HTTP místo aktivační procedury událostí mřížky. Jedním z možných důvodů pro tento krok je získáte větší kontrolu nad adresu URL koncového bodu, která volá funkci. 

Pokud chcete použít triggeru protokolu HTTP, budete muset napsat kód pro aktivační událost mřížky jaké automaticky:

* Odešle odpověď ověřování [požadavek na ověření předplatného](../event-grid/security-authentication.md#webhook-event-delivery).
* Volá funkci jednou za element pole události obsažené v textu požadavku.

Následující ukázka kódu C# pro aktivační procedury HTTP simuluje chování aktivační událost mřížky:

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequestMessage req,
    TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    var messages = await req.Content.ReadAsAsync<JArray>();

    // If the request is for subscription validation, send back the validation code.
    if (messages.Count > 0 && string.Equals((string)messages[0]["eventType"], 
        "Microsoft.EventGrid.SubscriptionValidationEvent", 
        System.StringComparison.OrdinalIgnoreCase))
    {
        log.Info("Validate request received");
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
        log.Info($"Subject: {eventGridEvent.Subject}");
        log.Info($"Time: {eventGridEvent.EventTime}");
        log.Info($"Event data: {eventGridEvent.Data.ToString()}");
    }

    return req.CreateResponse(HttpStatusCode.OK);
}
```

Následující vzorový kód JavaScript pro aktivační procedury HTTP simuluje chování aktivační událost mřížky:

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    var messages = req.body;
    // If the request is for subscription validation, send back the validation code.
    if (messages.length > 0 && messages[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent") {
        context.log('Validate request received');
        context.res = { status: 200, body: JSON.stringify({validationResponse: messages[0].data.validationCode}) }
    }
    else {
        // The request is not for subscription validation, so it's for one or more events.
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

Zpracování událostí kódu přejde uvnitř cyklus prostřednictvím `messages` pole.

Informace o adrese URL pro vyvolání funkce místně nebo při spuštění v Azure najdete v tématu [HTTP aktivační událost Vazba referenční dokumentaci k nástroji](functions-bindings-http-webhook.md) 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o Azure functions triggerů a vazeb](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Další informace o události mřížky](../event-grid/overview.md)
