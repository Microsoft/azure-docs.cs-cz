---
title: Trigger služby Event Grid pro službu Azure Functions
description: Vysvětlení zpracování událostí služby Event Grid ve službě Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 4bfc1fa167185023b71566bde6e6199bdb18849b
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50087631"
---
# <a name="event-grid-trigger-for-azure-functions"></a>Trigger služby Event Grid pro službu Azure Functions

Tento článek vysvětluje, jak zpracovat [služby Event Grid](../event-grid/overview.md) událostí ve službě Azure Functions.

Event Grid je služba Azure, která odesílá požadavky HTTP k oznamování události, ke kterým dochází v *vydavatelé*. Vydavatel je službě nebo prostředku, který pochází událost. Účet úložiště objektů blob v Azure je třeba vydavatele, a [odeslání objektu blob nebo odstranění je událost](../storage/blobs/storage-blob-event-overview.md). Některé [služeb Azure má integrovanou podporu publikování událostí do služby Event Grid](../event-grid/overview.md#event-sources). 

Událost *obslužné rutiny* přijímat a zpracovávat události. Služba Azure Functions je jedním z několika [služby Azure, které mají integrovanou podporu pro zpracování událostí služby Event Grid](../event-grid/overview.md#event-handlers). V tomto článku se dozvíte, jak použít aktivační událost služby Event Grid se vyvolat funkci po přijetí události ze služby Event Grid.

Pokud dáváte přednost, můžete použít aktivační událost HTTP pro zpracování událostí služby Event Grid; Zobrazit [použít aktivační událost HTTP jako aktivační událost služby Event Grid](#use-an-http-trigger-as-an-event-grid-trigger) dále v tomto článku. V současné době nelze použít aktivační událost služby Event Grid pro aplikaci Azure Functions doručení události [schématu CloudEvents](../event-grid/cloudevents-schema.md). Místo toho používejte aktivační událost HTTP.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Balíčky – funkce 1.x

Trigger služby Event Grid je k dispozici v [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) balíčku NuGet, verzi 1.x. Zdrojový kód pro tento balíček je v [azure-functions-eventgrid-extension](https://github.com/Azure/azure-functions-eventgrid-extension/tree/master) úložiště GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Balíčky – funkce 2.x

Trigger služby Event Grid je k dispozici v [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) balíčku NuGet, verze 2.x. Zdrojový kód pro tento balíček je v [azure-functions-eventgrid-extension](https://github.com/Azure/azure-functions-eventgrid-extension/tree/v2.x) úložiště GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example"></a>Příklad:

Podívejte se na příklad konkrétní jazyk pro trigger Event Grid:

* [C#](#c-example)
* [C# skript (.csx)](#c-script-example)
* [JavaScript](#javascript-example)
* [Java](#trigger---java-example)

Příklad trigger HTTP, naleznete v tématu [použití triggeru HTTP](#use-an-http-trigger-as-an-event-grid-trigger) dále v tomto článku.

### <a name="c-version-1x"></a>C#(Verze 1.x)

Následující příklad ukazuje funkci 1.x [funkce jazyka C#](functions-dotnet-class-library.md) , která vytvoří vazbu `JObject`:

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

### <a name="c-2x"></a>C#(2.x)

Následující příklad ukazuje funkci 2.x [funkce jazyka C#](functions-dotnet-class-library.md) , která vytvoří vazbu `EventGridEvent`:

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

Další informace najdete v tématu [balíčky](#packages), [atributy](#attributes), [konfigurace](#configuration), a [využití](#usage).

### <a name="c-script-example"></a>Ukázkový skript jazyka C#

Následující příklad ukazuje vazbu aktivační události v *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) , který používá vazba.

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

#### <a name="c-script-version-1x"></a>C#skript (verzi 1.x)

Tady je kód skriptu 1.x C# funkce s vazbou na `JObject`:

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

#### <a name="c-script-version-2x"></a>C#skript (verze 2.x)

Tady je kód skriptu 2.x C# funkce s vazbou na `EventGridEvent`:

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(EventGridEvent eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.Data.ToString());
}
```

Další informace najdete v tématu [balíčky](#packages), [atributy](#attributes), [konfigurace](#configuration), a [využití](#usage).

### <a name="javascript-example"></a>Příklad v jazyce JavaScript

Následující příklad ukazuje vazbu aktivační události v *function.json* souboru a [funkce jazyka JavaScript](functions-reference-node.md) , který používá vazba.

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

### <a name="trigger---java-example"></a>Aktivační události – příklad v jazyce Java

Následující příklad ukazuje vazbu aktivační události v *function.json* souboru a [Java funkce](functions-reference-java.md) , který používá vazbu a vytiskne událost.

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

Tady je kód Java:

```java
@FunctionName("eventGridMonitor")
  public void logEvent(
     @EventGridTrigger(name = "event") String content,
      final ExecutionContext context
  ) { 
      context.getLogger().info(content);
    }
```

V [Java funkce knihovny prostředí runtime](/java/api/overview/azure/functions/runtime), použijte `EventGridTrigger` poznámku o parametrech, jehož hodnota bude pocházet z EventGrid. Parametry těchto poznámek způsobit funkce spustit, když dorazí událost.  Tato poznámka je možné s nativní typy v jazyce Java, objektů Pojo nebo s povolenou hodnotou Null hodnoty pomocí `Optional<T>`. 

## <a name="attributes"></a>Atributy

V [knihoven tříd C#](functions-dotnet-class-library.md), použijte [EventGridTrigger](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/EventGridTriggerAttribute.cs) atribut.

Tady je `EventGridTrigger` atribut v podpisu metody:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, ILogger log)
{
    ...
}
```

Kompletní příklad naleznete v tématu [příklad jazyka C#](#c-example).

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru. Neexistují žádné parametry konstruktoru nebo vlastnosti nastavení `EventGridTrigger` atribut.

|Vlastnost Function.JSON |Popis|
|---------|---------|----------------------|
| **type** | Povinné – musí být nastavena na `eventGridTrigger`. |
| **direction** | Povinné – musí být nastavena na `in`. |
| **Jméno** | Požadovaná: název této proměnné v kódu funkce pro parametr, který přijímá data události. |

## <a name="usage"></a>Využití

Pro funkce C# a F # v Azure Functions 1.x, můžete použít následující typy parametrů pro trigger služby Event Grid:

* `JObject`
* `string`

Pro funkce C# a F # v Azure Functions 2.x, máte také možnost použít následující typ parametru pro trigger služby Event Grid:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`-Definuje vlastnosti pro pole společná pro všechny typy událostí.

> [!NOTE]
> Ve v1 funkce, pokud se pokusíte vytvořit vazbu na `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`, kompilátor zobrazí zprávu "zastaralé" a dokáží vám používat `Microsoft.Azure.EventGrid.Models.EventGridEvent` místo. Pokud chcete používat novější typ, odkazovat [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet balíček a plnému `EventGridEvent` název typu jsou s `Microsoft.Azure.EventGrid.Models`. Informace o tom, jak odkazovat na balíčky NuGet ve funkci skriptu jazyka C# najdete v tématu [balíčků pomocí NuGet](functions-reference-csharp.md#using-nuget-packages)

Funkce jazyka JavaScript, parametr s názvem podle *function.json* `name` vlastnost obsahuje odkaz na objekt události.

## <a name="event-schema"></a>Schéma událostí

Data pro události služby Event Grid přijme jako objekt JSON v textu požadavku HTTP. JSON vypadá podobně jako v následujícím příkladu:

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

V uvedeném příkladu je pole jednoho prvku. Event Grid vždy odešle pole a mohou odesílat více než jednu událost v poli. Modul runtime volá funkci jednou pro každý prvek pole.

Vlastnosti nejvyšší úrovně v případě dat JSON jsou stejné mezi všechny typy událostí, při obsah `data` vlastnosti jsou specifické pro každý typ události. Jak ukazuje příklad je pro události úložiště objektů blob.

Vysvětlení vlastností běžné a specifické pro události, naleznete v tématu [vlastnosti události](../event-grid/event-schema.md#event-properties) v dokumentaci služby Event Grid.

`EventGridEvent` Typ definuje pouze vlastnosti nejvyšší úrovně; `Data` je vlastnost `JObject`. 

## <a name="create-a-subscription"></a>Vytvoření odběru

Chcete-li začít přijímat požadavky Event Grid HTTP, vytvořte odběr Event gridu, který určuje adresu URL koncového bodu, která volá funkci.

### <a name="azure-portal"></a>portál Azure

Funkce, které vyvíjíte na webu Azure Portal s aktivační událostí služby Event Grid, vyberte **přidat předplatné Event gridu**.

![Vytvoření odběru na portálu](media/functions-bindings-event-grid/portal-sub-create.png)

Při výběru tohoto odkazu na portálu se otevře **vytvořit odběr události** předem stránka s adresu URL koncového bodu.

![Adresa URL koncového bodu předem](media/functions-bindings-event-grid/endpoint-url.png)

Další informace o tom, jak vytvořit odběry s využitím webu Azure portal najdete v tématu [vytvoření vlastní události – Azure portal](../event-grid/custom-event-quickstart-portal.md) v dokumentaci služby Event Grid.

### <a name="azure-cli"></a>Azure CLI

Vytvoření odběru s použitím [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest), použijte [az eventgrid-odběr události vytvoření](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-create) příkazu.

Příkaz vyžaduje adresu URL koncového bodu, která volá funkci. Následující příklad ukazuje vzor adresy URL specifické pro verzi:

#### <a name="version-2x-runtime"></a>Modul runtime verze 2.x

    https://{functionappname}.azurewebsites.net/runtime/webhooks/eventgrid?functionName={functionname}&code={systemkey}

#### <a name="version-1x-runtime"></a>Modul runtime verze 1.x

    https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}

Klíč systému je autorizačního klíče, který musí být uvedený v adrese URL koncového bodu pro trigger Event Grid. Následující část vysvětluje, jak získat klíč systému.

Tady je příklad, který se přihlásí k účtu úložiště objektů blob (s zástupný symbol pro systémový klíč):

#### <a name="version-2x-runtime"></a>Modul runtime verze 2.x

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

#### <a name="version-1x-runtime"></a>Modul runtime verze 1.x

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

Další informace o tom, jak vytvoříte odběr, naleznete v tématu [rychlému startu pro úložiště objektů blob](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) nebo v jiných rychlých startech služby Event Grid.

### <a name="get-the-system-key"></a>Získání klíče systému

Klíč systému můžete získat pomocí následující rozhraní API (HTTP GET):

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={masterkey}
```

Toto je správce rozhraní API, takže vyžaduje vaši aplikaci function app [hlavní klíč](functions-bindings-http-webhook.md#authorization-keys). Nepleťte si klíč systému (pro volání funkce triggeru Event Grid) s hlavním klíčem (pro provádění úloh správy v aplikaci function app). Když se přihlásíte k odběru téma Event gridu, nezapomeňte použít systémový klíč.

Tady je příklad, který obsahuje klíč systému odpovědi:

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

Získáte hlavní klíč pro aplikaci function app z **fungovat nastavení aplikace** karta na portálu.

> [!IMPORTANT]
> Hlavní klíč poskytuje přístup správce ke své aplikaci function app. Není tento klíč sdílet s třetími stranami nebo distribuovat v nativní klientské aplikace.

Další informace najdete v tématu [autorizace klíče](functions-bindings-http-webhook.md#authorization-keys) v referenční článek triggeru HTTP.

Alternativně můžete odeslat HTTP PUT se zadat hodnotu klíče sami.

## <a name="local-testing-with-viewer-web-app"></a>Místní testování pomocí prohlížeče webové aplikace

K otestování Event Grid trigger místně, musíte získat požadavků Event Grid HTTP doručovaný původ v cloudu do místního počítače. Jednou z možností, které se zachytáváním požadavky online a ručně opakovaným odesláním ji opravte je na místním počítači:

2. [Vytvoření webové aplikace v prohlížeči](#create-a-viewer-web-app) , který zachycuje zprávy o událostech.
3. [Vytvořit odběr Event gridu](#create-an-event-grid-subscription) , která zasílá události do aplikací prohlížeče.
4. [Vytvořit žádost o](#generate-a-request) a zkopírujte text požadavku z aplikace prohlížeče.
5. [Ručně odeslat požadavek](#manually-post-the-request) odeslané na adresu místního hostitele služby Event Grid aktivovat funkci.

Po dokončení testování, můžete použít stejné předplatné pro produkční prostředí aktualizuje se koncový bod. Použití [az eventgrid odběr události update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-update) rozhraní příkazového řádku Azure.

### <a name="create-a-viewer-web-app"></a>Vytvoření webové aplikace v prohlížeči

Pro zjednodušení zachycené zprávy o událostech, můžete nasadit [předem vytvořené webové aplikace](https://github.com/Azure-Samples/azure-event-grid-viewer) , která zobrazuje zprávy o událostech. Nasazené řešení zahrnuje plán služby App Service, webovou aplikaci App Service a zdrojový kód z GitHubu.

Vyberte **Nasadit do Azure** a nasaďte řešení do svého předplatného. Na webu Azure Portal zadejte hodnoty pro parametry.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

Dokončení nasazení může trvat několik minut. Po úspěšném nasazení si webovou aplikaci prohlédněte, abyste se ujistili, že funguje. Ve webovém prohlížeči přejděte na: `https://<your-site-name>.azurewebsites.net`

Zobrazí se web, na který se však zatím neodeslaly žádné události.

![Zobrazení nového webu](media/functions-bindings-event-grid/view-site.png)

### <a name="create-an-event-grid-subscription"></a>Vytvoření odběru Event Gridu

Vytvořit odběr Event gridu typu, který chcete testovat a poskytněte adresu URL z vaší webové aplikace jako koncový bod pro oznámení události. Koncový bod pro webovou aplikaci musí obsahovat příponu `/api/updates/`. Proto je úplná adresa URL `https://<your-site-name>.azurewebsites.net/api/updates`

Informace o tom, jak vytvořit odběry s využitím webu Azure portal najdete v tématu [vytvoření vlastní události – Azure portal](../event-grid/custom-event-quickstart-portal.md) v dokumentaci služby Event Grid.

### <a name="generate-a-request"></a>Generování žádosti

Spustí událost, která bude generovat provozu HTTP na koncový bod webové aplikace.  Například pokud jste nevytvořili předplatné úložiště objektů blob, nahrání nebo odstranit objekt blob. Když žádost zobrazí ve webové aplikaci, zkopírujte text požadavku.

Žádost o ověření předplatného vyřídily dříve; Ignorovat všechny žádosti o ověření a zkopírujte žádost o události.

![Text požadavku kopírování z webové aplikace](media/functions-bindings-event-grid/view-results.png)

### <a name="manually-post-the-request"></a>Ručně odeslat žádost

Funkce služby Event Grid můžete spusťte lokálně.

Použijte nástroj, jako [Postman](https://www.getpostman.com/) nebo [curl](https://curl.haxx.se/docs/httpscripting.html) vytvoření požadavku HTTP POST:

* Nastavte `Content-Type: application/json` záhlaví.
* Nastavení `aeg-event-type: Notification` záhlaví.
* RequestBin dat vložte do textu žádosti. 
* Odeslání na adresu URL vaší funkce triggeru služby Event Grid pomocí následujícímu vzoru:

```
http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={functionname}
```

`functionName` Parametr musí být název zadaný v `FunctionName` atribut.

Na následujících snímcích obrazovky zobrazit záhlaví a text v nástroji Postman žádosti:

![Záhlaví v nástroji Postman](media/functions-bindings-event-grid/postman2.png)

![Text požadavku v nástroji Postman](media/functions-bindings-event-grid/postman.png)

Funkce triggeru služby Event Grid spustí a zobrazí protokoly podobné následujícímu příkladu:

![Ukázky služby Event Grid trigger funkce protokolů](media/functions-bindings-event-grid/eg-output.png)

## <a name="local-testing-with-ngrok"></a>Místní testování s ngrok

Jiný způsob, jak otestovat místně aktivační událost služby Event Grid je k automatizaci připojení HTTP mezi Internetem a vývojového počítače. Můžete to udělat open source nástroj s názvem [ngrok](https://ngrok.com/):

3. [Vytvoření koncového bodu ngrok](#create-an-ngrok-endpoint).
4. [Spusťte funkci triggeru služby Event Grid](#run-the-event-grid-trigger-function).
5. [Vytvořit odběr Event gridu](#create-a-subscription) , která zasílá události do koncového bodu ngrok.
6. [Spustí událost](#trigger-an-event).

Po dokončení testování, můžete použít stejné předplatné pro produkční prostředí aktualizuje se koncový bod. Použití [az eventgrid odběr události update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-update) rozhraní příkazového řádku Azure.

### <a name="create-an-ngrok-endpoint"></a>Vytvoření koncového bodu ngrok

Stáhněte si *ngrok.exe* z [ngrok](https://ngrok.com/)a spusťte následující příkaz:

```
ngrok http -host-header=localhost 7071
```

-Host-parametr hlavičky je potřeba proto, že modul runtime služby functions očekává, že požadavky od localhost při spuštění v místním hostiteli. 7071 je výchozí číslo portu, když modul runtime spustí místně.

Tento příkaz vytváří výstup podobný tomuto:

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

Budete používat `https://{subdomain}.ngrok.io` adresu URL pro váš odběr služby Event Grid.

### <a name="run-the-event-grid-trigger-function"></a>Spusťte funkci triggeru služby Event Grid

Adresa URL ngrok nezíská zvláštní zacházení Event grid, funkce musí být spuštěn lokálně, při vytvoření odběru. Pokud tomu tak není, není odeslána odpověď ověření a vytvoření předplatného se nezdaří.

### <a name="create-a-subscription"></a>Vytvoření odběru

Vytvořit odběr Event gridu typu, který chcete testovat a poskytněte ngrok koncový bod.

Tento model koncový bod se používá pro funkce 1.x:
```
https://{subdomain}.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName={functionname}
```
Tento model koncový bod se používá pro funkce 2.x:
```
https://{subdomain}.ngrok.io/runtime/webhooks/eventgrid?functionName={functionName}
```
`functionName` Parametr musí být název zadaný v `FunctionName` atribut.

Tady je příklad použití Azure CLI:

```
az eventgrid event-subscription create --resource-id /subscriptions/aeb4b7cb-b7cb-b7cb-b7cb-b7cbb6607f30/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstor0122 --name egblobsub0126 --endpoint https://263db807.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName=EventGridTrigger
```

Informace o tom, jak vytvoříte odběr, naleznete v tématu [vytvoření odběru](#create-a-subscription) dříve v tomto článku.

### <a name="trigger-an-event"></a>Aktivace události

Spustí událost, která bude generovat provozu HTTP na koncový bod ngrok.  Například pokud jste nevytvořili předplatné úložiště objektů blob, nahrání nebo odstranit objekt blob.

Funkce triggeru služby Event Grid spustí a zobrazí protokoly podobné následujícímu příkladu:

![Ukázky služby Event Grid trigger funkce protokolů](media/functions-bindings-event-grid/eg-output.png)

## <a name="use-an-http-trigger-as-an-event-grid-trigger"></a>Pomocí aktivační událost HTTP jako aktivační událost služby Event Grid

Událostí služby Event Grid přijme jako požadavky HTTP, tak může zpracovávat události pomocí triggeru HTTP Event Grid aktivační událost Instead of. Jednoho z možných důvodů způsobem, který se má získat větší kontrolu nad adresu URL koncového bodu, která volá funkci. Dalším důvodem je, když budete chtít přijímat události v [schématu CloudEvents](../event-grid/cloudevents-schema.md). V současné době nepodporuje aktivační události Event Grid schématu CloudEvents. Řešení pro Event Grid schématu a schématu CloudEvents ukazují příklady v této části.

Pokud používáte aktivační událost HTTP, budete muset psát kód pro co trigger služby Event Grid umožňuje automaticky:

* Odešle odpověď ověřování [žádost o ověření předplatného](../event-grid/security-authentication.md#webhook-event-delivery).
* Zavolá funkci jednou za prvkem pole události obsažené v textu požadavku.

Informace o adrese URL pro vyvolání funkce místně nebo při spuštění v Azure najdete v tématu [HTTP aktivační událost vazby referenční dokumentaci](functions-bindings-http-webhook.md)

### <a name="event-grid-schema"></a>Schéma služby Event Grid

Následující ukázkový kód jazyka C# pro aktivační událost HTTP simuluje chování trigger služby Event Grid. Použijte tento příklad pro události v události doručené mřížka schématu.

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

Následující vzorový kód jazyka JavaScript pro aktivační událost HTTP simuluje chování trigger služby Event Grid. Použijte tento příklad pro události v události doručené mřížka schématu.

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

Váš kód pro zpracování události přejde uvnitř smyčka prostřednictvím `messages` pole.

### <a name="cloudevents-schema"></a>Schématu CloudEvents

Následující ukázkový kód jazyka C# pro aktivační událost HTTP simuluje chování trigger služby Event Grid.  Použijte tento příklad pro události, které zajišťujeme ve schématu CloudEvents.

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

Následující vzorový kód jazyka JavaScript pro aktivační událost HTTP simuluje chování trigger služby Event Grid. Použijte tento příklad pro události, které zajišťujeme ve schématu CloudEvents.

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

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech Azure functions a vazby](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Další informace o službě Event Grid](../event-grid/overview.md)
