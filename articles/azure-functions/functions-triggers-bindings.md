---
title: Triggery a vazby ve službě Azure Functions
description: Zjistěte, jak používat triggery a vazby ve službě Azure Functions k provádění kódu, připojte se k online události a cloudové služby.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: funkce azure, funkce, zpracování událostí, webhook, dynamické výpočty, architektura bez serverů
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/24/2018
ms.author: cshoe
ms.openlocfilehash: ba2441044d63e63b969054e84e163352d2b376e8
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "53993798"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Aktivace Azure Functions a vazby koncepty

Tento článek obsahuje přehled triggerů a vazeb ve službě Azure Functions. Funkce, které jsou společné pro všechny vazby a všechny podporované jazyky jsou zde popsány.

## <a name="overview"></a>Přehled

A *aktivační událost* definuje, jak je vyvolána funkce. Funkce musí mít přesně jeden trigger. Triggery obsahují související data, což je obvykle datová část, která funkci aktivovala.

Vstup a výstup *vazby* poskytují deklarativní způsob, jak se připojit k datům z vašeho kódu. Vazby jsou volitelné a funkce mohou mít více vstupní a výstupní vazby. 

Triggery a vazby umožňují vyhnout hardcoding podrobnosti služby, které pracujete. Funkce přijímá data (například obsah zprávy fronty) v parametry funkce. Odeslat data (například k vytvoření zprávy fronty) pomocí návratová hodnota funkce. V jazyce C# a skript jazyka C#, jsou alternativní způsoby, jak odesílat data `out` parametry a [kolekcí objektů](functions-reference-csharp.md#writing-multiple-output-values).

Při vývoji funkcí s použitím webu Azure portal, triggerů a vazeb jsou nakonfigurované v *function.json* souboru. Portál poskytuje uživatelské rozhraní pro tuto konfiguraci, ale můžete upravit soubor přímo tak, že změníte na **Rozšířený editor**.

Při vývoji funkcí s použitím sady Visual Studio k vytvoření knihovny tříd, nakonfigurujete aktivačními událostmi a vazbami upravení metody a parametry s atributy.

## <a name="example-trigger-and-binding"></a>Příklad aktivační události a vazby

Předpokládejme, že chcete zadat nový řádek do služby Azure Table storage pokaždé, když se v Azure Queue storage se objeví nová zpráva. Tento scénář je možné implementovat pomocí Azure Queue storage triggeru a Azure Table storage výstupní vazby. 

Tady je *function.json* souboru pro tento scénář. 

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "name": "$return",
      "type": "table",
      "direction": "out",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

První prvek `bindings` pole je aktivační událost fronty úložiště. `type` a `direction` vlastnosti identifikovat aktivační událost. `name` Vlastnost identifikuje parametr funkce, která přijímá obsah zprávy fronty. Název fronty k monitorování se `queueName`, a připojovací řetězec je v nastavení aplikace, které jsou identifikované `connection`.

Na druhý prvek `bindings` pole je služba Azure Table Storage výstupní vazbu. `type` a `direction` vlastnosti identifikovat vazby. `name` Vlastnost určuje, jak funkce poskytuje řádku nové tabulky, v tomto případě pomocí funkce vrátí hodnotu. Název tabulky se `tableName`, a připojovací řetězec je v nastavení aplikace, které jsou identifikované `connection`.

K zobrazení a úpravám obsahu *function.json* na webu Azure Portal, klikněte na tlačítko **Rozšířený editor** možnost **integrace** kartu vaší funkce.

> [!NOTE]
> Hodnota `connection` je název nastavení aplikace, které obsahuje připojovací řetězec, ne samotný řetězec připojení. Vazby připojení řetězce uložené v nastavení aplikace k vynucení nejlepší cvičení, která *function.json* neobsahuje tajných kódů služby.

Tady je kód jazyka C# skript, který funguje s této aktivační události a vazby. Všimněte si, že je název parametru, která poskytuje obsah zprávy fronty `order`; tento název je povinný, protože `name` hodnoty vlastností v *function.json* je `order` 

```cs
#r "Newtonsoft.Json"

using Microsoft.Extensions.Logging;
using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, ILogger log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

Stejný soubor function.json lze pomocí funkce JavaScriptu:

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

Knihovna tříd, jedna aktivační událost a informace o vazbě &mdash; názvu tabulky a fronty, účty úložiště, funkce parametry pro vstup a výstup &mdash; poskytuje atributech namísto v souboru function.json. Tady je příklad:

```csharp
public static class QueueTriggerTableOutput
{
    [FunctionName("QueueTriggerTableOutput")]
    [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
    public static Person Run(
        [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order,
        ILogger log)
    {
        return new Person() {
                PartitionKey = "Orders",
                RowKey = Guid.NewGuid().ToString(),
                Name = order["Name"].ToString(),
                MobileNumber = order["MobileNumber"].ToString() };
    }
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

## <a name="supported-bindings"></a>Podporované vazby

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Informace o tom, které jsou ve verzi preview vazby, nebo jsou schválené pro použití v produkčním prostředí, najdete v části [podporované jazyky](supported-languages.md).

## <a name="register-binding-extensions"></a>Registrace rozšíření vazby

V některých vývojová prostředí, je nutné explicitně *zaregistrovat* vazbu, která chcete použít. Rozšíření vazby jsou k dispozici v balíčcích NuGet a k registraci rozšíření nainstalovat balíček. Následující tabulka udává, kdy a jak zaregistrujete rozšíření vazby.

|Vývojové prostředí |Registrace<br/> ve funkcích 1.x  |Registrace<br/> ve funkcích 2.x  |
|---------|---------|---------|
|portál Azure|Automaticky|[Automatické s řádku](#azure-portal-development)|
|Místní pomocí nástrojů Azure Functions Core|Automaticky|[Pomocí příkazů rozhraní příkazového řádku nástroje Core](#local-development-azure-functions-core-tools)|
|Knihovny tříd C# pomocí sady Visual Studio 2017|[Pomocí nástroje NuGet](#c-class-library-with-visual-studio-2017)|[Pomocí nástroje NuGet](#c-class-library-with-visual-studio-2017)|
|Knihovny tříd C# pomocí nástroje Visual Studio Code|neuvedeno|[Použití .NET Core CLI](#c-class-library-with-visual-studio-code)|

Výjimky, které nevyžadují explicitní registraci, protože jsou automaticky registrované ve všech verzí a prostředí jsou následující typy vazeb: HTTP a časovač.

### <a name="azure-portal-development"></a>Vývoj pro Azure portal

Tato část platí jenom pro funkce 2.x. Rozšíření vazby nemusí být explicitně registrován ve funkcích 1.x.

Při vytváření funkce nebo přidat vazbu, zobrazí se výzva při rozšíření pro triggerem nebo vazbou vyžaduje registrace. Na výzvy odpovíte kliknutím **nainstalovat** k registraci rozšíření. Instalace může trvat až 10 minut, než v plánu consumption.

Každé rozšíření musí nainstalovat pouze jednou pro danou funkci aplikace. U podporovaných vazeb, které nejsou k dispozici na portálu nebo aktualizovat nainstalovaného rozšíření můžete také [ruční instalaci nebo aktualizaci vazby rozšíření z portálu Azure Functions](install-update-binding-extensions-manual.md).  

### <a name="local-development-azure-functions-core-tools"></a>Místní vývoj nástrojů Azure Functions Core

Tato část platí jenom pro funkce 2.x. Rozšíření vazby nemusí být explicitně registrován ve funkcích 1.x.

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

<a name="local-csharp"></a>
### <a name="c-class-library-with-visual-studio-2017"></a>Knihovny tříd C# pomocí sady Visual Studio 2017

V **Visual Studio 2017**, balíčky můžete nainstalovat z konzoly Správce balíčků pro použití [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) příkaz, jak je znázorněno v následujícím příkladu:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <target_version>
```

Název balíčku pro danou vazbu je uvedené v článku odkaz pro danou vazbu. Příklad najdete v tématu [balíčky článku odkaz vazby služby Service Bus](functions-bindings-service-bus.md#packages---functions-1x).

Nahraďte `<target_version>` v příkladu nahraďte konkrétní verzi balíčku, jako například `3.0.0-beta5`. Platná verze jsou uvedeny v nějakém balíčku stránkách v [NuGet.org](https://nuget.org). Hlavní verze, které odpovídají modul runtime služby Functions 1.x a 2.x jsou uvedeny v článku odkaz pro vazbu.

### <a name="c-class-library-with-visual-studio-code"></a>Knihovny tříd C# pomocí Visual Studio Code

V **Visual Studio Code**, balíčky můžete nainstalovat pomocí příkazového řádku [se příkaz dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) příkaz v rozhraní příkazového řádku .NET Core, jak je znázorněno v následujícím příkladu:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <target_version>
```

Rozhraní příkazového řádku .NET Core jde použít jenom pro vývoj pro Azure Functions 2.x.

Název balíčku pro danou vazbu je uvedené v článku odkaz pro danou vazbu. Příklad najdete v tématu [balíčky článku odkaz vazby služby Service Bus](functions-bindings-service-bus.md#packages---functions-1x).

Nahraďte `<target_version>` v příkladu nahraďte konkrétní verzi balíčku, jako například `3.0.0-beta5`. Platná verze jsou uvedeny v nějakém balíčku stránkách v [NuGet.org](https://nuget.org). Hlavní verze, které odpovídají modul runtime služby Functions 1.x a 2.x jsou uvedeny v článku odkaz pro vazbu.

## <a name="binding-direction"></a>Směr vazby

Všechny aktivační události a vazby mít `direction` vlastnost *function.json* souboru:

- Pro aktivační události je vždy směru `in`
- Používat vstupní a výstupní vazby `in` a `out`
- Některé vazby podporují speciální směr `inout`. Pokud používáte `inout`, pouze **Rozšířený editor** je k dispozici v **integrace** kartu.

Při použití [atributy v knihovně tříd](functions-dotnet-class-library.md) konfigurace triggerů a vazeb, směr zadaný v konstruktoru atributu nebo odvozen od typu parametru.

## <a name="using-the-function-return-value"></a>Používat návratovou hodnotu funkce

V jazycích, které mají návratovou hodnotu můžete svázat výstupní vazbu návratovou hodnotu:

* V knihovně tříd jazyka C# použijte atribut vazby výstupu pro návratovou hodnotu metody.
* V jiných jazycích, nastavte `name` vlastnost *function.json* k `$return`.

Pokud existuje více výstupní vazby, použijte vrácenou hodnotu pouze pro jeden z nich.

V jazyce C# a skript jazyka C#, jsou alternativní způsoby, jak odesílat data do výstupní vazbu `out` parametry a [kolekcí objektů](functions-reference-csharp.md#writing-multiple-output-values).

Podívejte se na konkrétní jazyk příklad znázorňující použití návratovou hodnotu:

* [C#](#c-example)
* [C# skript (.csx)](#c-script-example)
* [F#](#f-example)
* [JavaScript](#javascript-example)
* [Python](#python-example)

### <a name="c-example"></a>Příklad jazyka C#

Tady je kód jazyka C#, který používá vrácenou hodnotu pro výstupní vazby, za nímž následuje příklad asynchronní:

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static string Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static Task<string> Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

### <a name="c-script-example"></a>Ukázkový skript jazyka C#

Zde je výstupní vazbu v *function.json* souboru:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Tady je C# kód skriptu, za nímž následuje příklad asynchronní:

```cs
public static string Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
public static Task<string> Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

### <a name="f-example"></a>F#Příklad

Zde je výstupní vazbu v *function.json* souboru:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Tady je F# kódu:

```fsharp
let Run(input: WorkItem, log: ILogger) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.LogInformation(sprintf "F# script processed queue message '%s'" json)
    json
```

### <a name="javascript-example"></a>Příklad v jazyce JavaScript

Zde je výstupní vazbu v *function.json* souboru:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

V jazyce JavaScript, návratová hodnota přejde ve druhém parametru pro `context.done`:

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

### <a name="python-example"></a>Příklad v Pythonu

Zde je výstupní vazbu v *function.json* souboru:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```
Tady je kód Pythonu:

```python
def main(input: azure.functions.InputStream) -> str:
    return json.dumps({
        'name': input.name,
        'length': input.length,
        'content': input.read().decode('utf-8')
    })
```

## <a name="binding-datatype-property"></a>Vlastnost dataType vazby

V rozhraní .NET slouží k definování datový typ pro vstupní datový typ parametru. Například použijte `string` vytvořit vazbu na text aktivační událost fronty, pole bajtů ke čtení jako binární a vlastní typ k deserializaci objektů POCO.

Pro jazyky, které jsou dynamicky typované jako je JavaScript, použijte `dataType` vlastnost *function.json* souboru. Například chcete-li číst obsah požadavku HTTP v binárním formátu, nastavte `dataType` k `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Další možnosti pro `dataType` jsou `stream` a `string`.

## <a name="binding-expressions-and-patterns"></a>Výrazy vazeb a vzory

Jednou z nejúčinnějších funkcí triggerů a vazeb je *výrazy vazeb*. V *function.json* soubor a parametry funkce a kód, můžete použít výrazy, které odkazují na hodnoty z různých zdrojů.

Většina výrazy jsou označeny obtékání ve složených závorkách. Například ve funkci aktivační událost fronty `{queueTrigger}` přeloží text zprávy fronty. Pokud `path` vlastností pro objekt blob Výstupní vazba je `container/{queueTrigger}` a aktivuje funkci zprávy fronty `HelloWorld`, objekt blob s názvem `HelloWorld` se vytvoří.

Typy výrazů vazby

* [Nastavení aplikace](#binding-expressions---app-settings)
* [Název souboru aktivační události](#binding-expressions---trigger-file-name)
* [Aktivační událost metadat](#binding-expressions---trigger-metadata)
* [Datové části JSON](#binding-expressions---json-payloads)
* [Nový GUID](#binding-expressions---create-guids)
* [Aktuální datum a čas](#binding-expressions---current-time)

### <a name="binding-expressions---app-settings"></a>Výrazy vazby – nastavení aplikace

Jako osvědčený postup tajné klíče a připojovací řetězce by měla spravovat pomocí nastavení aplikace, nikoli konfigurační soubory. To omezuje přístup k těmto tajným kódům a umožňuje bezpečně ukládat soubory, jako *function.json* v úložištích veřejné zdrojového ovládacího prvku.

Nastavení aplikace jsou také užitečné, kdykoli budete chtít změnit konfiguraci na základě prostředí. Například v testovacím prostředí můžete monitorovat různé fronty nebo objektů blob v kontejneru úložiště.

Aplikace nastavení vazbové výrazy jsou označeny odlišně od jiných výrazech vazby: jsou zabaleny v procenta spíše než složených závorek. Například pokud je výstupní cesta vazby objektů blob `%Environment%/newblob.txt` a `Environment` hodnotu nastavení aplikace je `Development`, vytvoří se objekt blob v `Development` kontejneru.

Když funkce běží místně, hodnoty nastavení aplikace pocházet z *local.settings.json* souboru.

Všimněte si, že `connection` vlastnost triggerů a vazeb je zvláštní případ a automaticky vyřeší hodnoty jako nastavení aplikace, aniž by procenta. 

V následujícím příkladu je aktivační událost Azure Queue Storage, který používá nastavení aplikace `%input-queue-name%` k definování fronty pro aktivaci.

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "%input-queue-name%",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

Stejný přístup můžete použít v knihovnách tříd:

```csharp
[FunctionName("QueueTrigger")]
public static void Run(
    [QueueTrigger("%input-queue-name%")]string myQueueItem, 
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
}
```

### <a name="binding-expressions---trigger-file-name"></a>Výrazy vazby - název souboru aktivační události

`path` Pro objekt Blob může být aktivační událost vzor, který umožňuje odkazovat na název spouštěcí objekt blob v jiných vazbách a funkce kódu. Vzor může také obsahovat kritéria filtrování, které určují, jaké objekty BLOB můžete aktivovat volání funkce.

V následující aktivační událost objektů Blob vazby, například `path` vzor je `sample-images/{filename}`, která vytvoří vazbu výraz s názvem `filename`:

```json
{
  "bindings": [
    {
      "name": "image",
      "type": "blobTrigger",
      "path": "sample-images/{filename}",
      "direction": "in",
      "connection": "MyStorageConnection"
    },
    ...
```

Výraz `filename` pak lze v výstupní vazbu k určení názvu objektu blob vytváří:

```json
    ...
    {
      "name": "imageSmall",
      "type": "blob",
      "path": "sample-images-sm/{filename}",
      "direction": "out",
      "connection": "MyStorageConnection"
    }
  ],
}
```

Kód funkce má přístup k této stejnou hodnotu s použitím `filename` jako název parametru:

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, ILogger log)  
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->

Stejné umožňuje používat výrazy vazeb a vzory se vztahuje na atributy v knihovnách tříd. V následujícím příkladu, parametry konstruktoru atributu jsou stejné `path` hodnoty jako u předchozí *function.json* příklady: 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger log)
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
}

```

Můžete také vytvořit výrazy částí názvu souboru, jako je například rozšíření. Další informace o tom, jak používat výrazy a vzorů v řetězci cesty objektu Blob najdete v tématu [odkazy vazby objektů blob úložiště](functions-bindings-storage-blob.md).
 
### <a name="binding-expressions---trigger-metadata"></a>Výrazy vazby - trigger metadat

Řada triggerů kromě datová část poskytované aktivační události (jako je třeba obsah zprávy fronty, která aktivuje funkce), poskytují další metadata hodnoty. Tyto hodnoty slouží jako vstupní parametry v C# a F# nebo vlastnosti `context.bindings` objektu v jazyce JavaScript. 

Například aktivační událost Azure Queue storage podporuje následující vlastnosti:

* QueueTrigger - aktivací obsah zprávy, pokud se platný řetězec
* DequeueCount
* expirationTime
* ID
* InsertionTime
* NextVisibleTime
* Vlastnosti PopReceipt

Tyto hodnoty metadat jsou k dispozici v *function.json* vlastnosti souboru. Předpokládejme například, můžete použít aktivační událost fronty a zpráva fronty obsahuje název objektu blob, který chcete číst. V *function.json* soubor, můžete použít `queueTrigger` metadat vlastnost v objektu blob `path` vlastnost, jak je znázorněno v následujícím příkladu:

```json
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "direction": "in",
      "connection": "MyStorageConnection"
    }
  ]
```

Podrobnosti vlastnosti metadat pro jednotlivé aktivační události jsou popsané v příslušném článku odkaz. Příklad najdete v tématu [metadat aktivační událost fronty](functions-bindings-storage-queue.md#trigger---message-metadata). Je také k dispozici v dokumentaci **integrace** kartu na portálu v **dokumentaci** části oblasti Konfigurace vazby.  

### <a name="binding-expressions---json-payloads"></a>Výrazy vazby – datové části JSON

Po triggeru datovou část JSON, můžete odkazovat na její vlastnosti v konfiguraci u jiných vazeb ve stejné funkci a v kódu funkce.

Následující příklad ukazuje *function.json* soubor funkce webhooku, která přijímá název objektu blob ve formátu JSON: `{"BlobName":"HelloWorld.txt"}`. Přečte objekt blob vstupní vazby objektů Blob a HTTP výstupní vazbu vrátí obsah objektů blob v odpovědi HTTP. Všimněte si, že vstupní vazby objektů Blob získá rekapitulací přímo na název objektu blob `BlobName` vlastnosti (`"path": "strings/{BlobName}"`)

```json
{
  "bindings": [
    {
      "name": "info",
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson"
    },
    {
      "name": "blobContents",
      "type": "blob",
      "direction": "in",
      "path": "strings/{BlobName}",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

Aby to fungovalo C# a F#, je třeba třídu, která definuje pole k deserializaci jako v následujícím příkladu:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents, ILogger log)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    log.LogInformation($"Processing: {info.BlobName}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

V jazyce JavaScript probíhá automaticky deserializace JSON.

```javascript
module.exports = function (context, info) {
    if ('BlobName' in info) {
        context.res = {
            body: { 'data': context.bindings.blobContents }
        }
    }
    else {
        context.res = {
            status: 404
        };
    }
    context.done();
}
```

#### <a name="dot-notation"></a>Zápisu s tečkou

Pokud některé vlastnosti ve vaší datové části JSON jsou objekty s vlastnostmi, mohou odkazovat na ty přímo pomocí zápisu s tečkou. Předpokládejme například, že vaše struktury JSON vypadá takto:

```json
{
  "BlobName": {
    "FileName":"HelloWorld",
    "Extension":"txt"
  }
}
```

Je možné odkazovat přímo na `FileName` jako `BlobName.FileName`. V tomto formátu JSON, tady je co `path` vlastnost v předchozím příkladu vypadat nějak takto:

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

V jazyce C# je třeba dvě třídy:

```csharp
public class BlobInfo
{
    public BlobName BlobName { get; set; }
}
public class BlobName
{
    public string FileName { get; set; }
    public string Extension { get; set; }
}
```

### <a name="binding-expressions---create-guids"></a>Výrazy vazby – vytvořit GUID

`{rand-guid}` Vazby výraz vytvoří identifikátor GUID. Následující cesta objektu blob v `function.json` soubor vytvoří objekt blob s názvem jako *50710cb5 84b9 - 4d 87 9d 83-a03d6976a682.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="binding-expressions---current-time"></a>Výrazy vazby - aktuální čas

Vazbový výraz `DateTime` přeloží na `DateTime.UtcNow`. Následující cesta objektu blob v `function.json` soubor vytvoří objekt blob s názvem jako *2018-02-16T17-59-55Z.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```

## <a name="binding-at-runtime"></a>Vazbu za běhu

V jazyce C# a jinými jazyky rozhraní .NET, můžete použít vzoru imperativní vazby, na rozdíl od deklarativní vazby v *function.json* a atributy. Imperativní vazby je užitečné, když parametry vazby musí vypočítat v době běhu spíše než návrhu. Další informace najdete v tématu [jazyka C# – reference pro vývojáře](functions-dotnet-class-library.md#binding-at-runtime) nebo [referenční informace pro vývojáře skriptů jazyka C#](functions-reference-csharp.md#binding-at-runtime).

## <a name="functionjson-file-schema"></a>Schéma souboru Function.JSON

*Function.json* soubor schématu je k dispozici na [ http://json.schemastore.org/function ](http://json.schemastore.org/function).

## <a name="handling-binding-errors"></a>Zpracování chyb vazeb

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

Odkazy na všechny příslušné chybové témata pro různé služby Functions podporuje, najdete v článku [vazby kódy chyb](functions-bindings-error-pages.md#binding-error-codes) část [zpracování chyb Azure Functions](functions-bindings-error-pages.md) – téma s přehledem.  

## <a name="next-steps"></a>Další postup

Další informace o konkrétní vazbu najdete v následujících článcích:

- [HTTP a webhooky](functions-bindings-http-webhook.md)
- [Časovač](functions-bindings-timer.md)
- [Queue Storage](functions-bindings-storage-queue.md)
- [Blob Storage](functions-bindings-storage-blob.md)
- [Table Storage](functions-bindings-storage-table.md)
- [Centrum událostí](functions-bindings-event-hubs.md)
- [Service Bus](functions-bindings-service-bus.md)
- [Azure Cosmos DB](functions-bindings-cosmosdb.md)
- [Microsoft Graph](functions-bindings-microsoft-graph.md)
- [SendGrid](functions-bindings-sendgrid.md)
- [Twilio](functions-bindings-twilio.md)
- [Notification Hubs](functions-bindings-notification-hubs.md)
- [Mobile Apps](functions-bindings-mobile-apps.md)
- [Externí soubor](functions-bindings-external-file.md)
