---
title: Správa instancí v trvalých funkcích – Azure
description: Zjistěte, jak spravovat instance v rozšíření Durable Functions pro funkce Azure.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 1837d342c4476633ee33a8579abe7389ac9bbddf
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476825"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Správa instancí v aplikaci Durable Functions v Azure

Pokud používáte rozšíření [Durable Functions](durable-functions-overview.md) pro funkce Azure nebo chcete začít dělat, ujistěte se, že jste získali nejlepší využití z něj. Můžete optimalizovat trvalé funkce orchestrace instance tím, že se dozví více o tom, jak je spravovat. Tento článek přejde do podrobností o každé operaci správy instance.

Můžete například spustit a ukončit instance a můžete dotazovat instance, včetně možnosti dotazovat se na všechny instance a instance dotazu pomocí filtrů. Kromě toho můžete odesílat události do instancí, čekat na dokončení orchestrace a načíst adresy URL webového háku pro správu PROTOKOLU HTTP. Tento článek popisuje i další operace správy, včetně převíjení instancí, vymazání historie instancí a odstranění centra úloh.

V trvalé funkce máte možnosti, jak chcete implementovat každou z těchto operací správy. Tento článek obsahuje příklady, které používají [nástroje Core Functions Azure](../functions-run-local.md) pro rozhraní .NET (C#) i JavaScript.

## <a name="start-instances"></a>Počáteční instance

Je důležité, aby bylo možné spustit instanci orchestraci. To se běžně provádí, když používáte vazby durable functions v aktivační události jiné funkce.

Metoda `StartNewAsync` (.NET) `startNew` nebo (JavaScript) na [vazbě klienta orchestrace](durable-functions-bindings.md#orchestration-client) spustí novou instanci. Interně tato metoda zařadí zprávu do fronty ovládacího prvku, která pak spustí spuštění funkce se zadaným názvem, který používá [vazbu aktivační události orchestrace](durable-functions-bindings.md#orchestration-trigger).

Tato asynchronní operace je dokončena, když je úspěšně naplánován proces orchestrace.

Parametry pro spuštění nové instance orchestrace jsou následující:

* **Název**: Název funkce orchestrator naplánovat.
* **Vstup**: Všechna data JSON-serializovatelná, která by měla být předána jako vstup do funkce orchestratoru.
* **InstanceId**: (Volitelné) Jedinečné ID instance. Pokud tento parametr nezadáte, metoda použije náhodné ID.

> [!TIP]
> Pro ID instance použijte náhodný identifikátor. ID náhodných instancí pomáhají zajistit rovnoměrné rozložení zatížení při škálování funkcí orchestrátoru na více virtuálních počítačích. Správný čas pro použití id nenáhodných instancí je, když ID musí pocházet z externího zdroje nebo když implementujete vzor [orchestrátoru singleton.](durable-functions-singletons.md)

Následující kód je ukázková funkce, která spustí novou instanci orchestrace:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HelloWorldQueueTrigger")]
public static async Task Run(
    [QueueTrigger("start-queue")] string input,
    [DurableClient] IDurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

> [!NOTE]
> Předchozí kód Jazyka C# je pro durable functions 2.x. Pro trvalé funkce 1.x `OrchestrationClient` je nutné `DurableClient` použít atribut namísto `DurableOrchestrationClient` atributu a `IDurableOrchestrationClient`místo . Další informace o rozdílech mezi verzemi naleznete v článku [verze durable functions.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<a name="javascript-function-json"></a>Pokud není uvedeno jinak, příklady na této stránce používají aktivační událost HTTP s následující funkcí.json.

**function.json**

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": ["post"]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> Tento příklad se zaměřuje na trvalé funkce verze 2.x. Ve verzi 1.x `orchestrationClient` použijte `durableClient`místo .

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

---

### <a name="azure-functions-core-tools"></a>Základní nástroje Azure Functions

Instanci můžete spustit také přímo pomocí příkazu [Nástroje pro základní funkce](../functions-run-local.md) `durable start-new` Azure. To trvá následující parametry:

* (povinné) : Název funkce, kterou má být zahájena. ** `function-name` **
* (nepovinné) : Vstup do funkce, buď vložený, nebo prostřednictvím souboru JSON. ** `input` ** U souborů přidejte k cestě k souboru předponu `@`s například `@path/to/file.json`.
* (nepovinné) : ID instance orchestrace. ** `id` ** Pokud tento parametr nezadáte, příkaz použije náhodný identifikátor GUID.
* (nepovinné): Název nastavení aplikace obsahující připojovací řetězec úložiště, který má být používán. ** `connection-string-setting` ** Výchozí hodnota je AzureWebJobsStorage.
* (nepovinné) : Název centra úloh Durable Functions, které má být používáno. ** `task-hub-name` ** Výchozí hodnota je DurableFunctionsHub. Můžete také nastavit v [host.json](durable-functions-bindings.md#host-json) pomocí durableTask:HubName.

> [!NOTE]
> Příkazy základnínástroje předpokládají, že je spouštíte z kořenového adresáře aplikace funkce. Pokud explicitně `connection-string-setting` zadáte `task-hub-name` parametry a, můžete spustit příkazy z libovolného adresáře. I když můžete spustit tyto příkazy bez spuštění hostitele aplikace funkce, můžete zjistit, že některé efekty nemůžete pozorovat, pokud je hostitel spuštěn. `start-new` Například příkaz zařadí zprávu o spuštění do centra cílové úlohy, ale orchestrace se ve skutečnosti nespustí, pokud není spuštěn proces hostitele aplikace funkce, který může zprávu zpracovat.

Následující příkaz spustí funkci s názvem HelloWorld a `counter-data.json` předá mu obsah souboru:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Instance dotazu

V rámci úsilí o správu orchestrace budete s největší pravděpodobností muset shromažďovat informace o stavu instance orchestrace (například zda byla dokončena normálně nebo se nezdařila).

Metoda `GetStatusAsync` (.NET) `getStatus` nebo (JavaScript) na [vazbě klienta orchestrace](durable-functions-bindings.md#orchestration-client) dotazuje stav instance orchestrace.

Trvá `instanceId` (povinné), `showHistory` (volitelné), `showHistoryOutput` (volitelné) `showInput` a (volitelné) jako parametry.

* **`showHistory`**: Pokud `true`je nastavena na , odpověď obsahuje historii spuštění.
* **`showHistoryOutput`**: Pokud `true`je nastavena na , historie provádění obsahuje výstupy aktivity.
* **`showInput`**: Pokud `false`je nastavena na , odpověď nebude obsahovat vstup funkce. Výchozí hodnota je `true`.

Metoda vrátí objekt s následujícími vlastnostmi:

* **Název**: Název funkce orchestrator.
* **InstanceId**: ID instance orchestraci (by měla `instanceId` být stejná jako vstup).
* **CreatedTime**: Čas spuštění funkce orchestrator.
* **Poslední AktualizovánoČas**: Čas, kdy orchestrace poslední kontrolní chod.
* **Vstup**: Vstup funkce jako hodnota JSON. Toto pole není vyplněno, pokud `showInput` je false.
* **CustomStatus**: Stav vlastní orchestrace ve formátu JSON.
* **Výstup**: Výstup funkce jako hodnota JSON (pokud je funkce dokončena). Pokud se funkce orchestrator nezdařila, tato vlastnost obsahuje podrobnosti selhání. Pokud byla ukončena funkce orchestrator, tato vlastnost obsahuje důvod ukončení (pokud existuje).
* **RuntimeStatus**: Jedna z následujících hodnot:
  * **Čeká na vyřízení**: Instance byla naplánována, ale ještě nebyla spuštěna.
  * **Spuštění**: Instance byla spuštěna.
  * **Dokončeno**: Instance byla dokončena normálně.
  * **ContinuedAsNew**: Instance se restartuje s novou historií. Tento stav je přechodný stav.
  * **Nezdařilo**se: Instance se nezdařila s chybou.
  * **Ukončeno**: Instance byla náhle zastavena.
* **Historie**: Historie provádění orchestrace. Toto pole je `showHistory` vyplněno `true`pouze v případě, že je nastaveno na .

Tato metoda `null` vrátí (.NET) nebo `undefined` (JavaScript), pokud instance neexistuje.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("check-status-queue")] string instanceId)
{
    DurableOrchestrationStatus status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

> [!NOTE]
> Předchozí kód Jazyka C# je pro durable functions 2.x. Pro trvalé funkce 1.x `OrchestrationClient` je nutné `DurableClient` použít atribut namísto `DurableOrchestrationClient` atributu a `IDurableOrchestrationClient`místo . Další informace o rozdílech mezi verzemi naleznete v článku [verze durable functions.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

Viz [Počáteční instance](#javascript-function-json) pro konfiguraci function.json.

---

### <a name="azure-functions-core-tools"></a>Základní nástroje Azure Functions

Je také možné získat stav instance orchestrace přímo pomocí příkazu [Nástroje jádra azure.](../functions-run-local.md) `durable get-runtime-status` To trvá následující parametry:

* (povinné) : ID instance orchestrace. ** `id` **
* (nepovinné) : `true`Je-li nastavena na , odpověď obsahuje vstup funkce. ** `show-input` ** Výchozí hodnota je `false`.
* (nepovinné) : `true`Je-li nastavena na , odpověď obsahuje výstup funkce. ** `show-output` ** Výchozí hodnota je `false`.
* (nepovinné): Název nastavení aplikace obsahující připojovací řetězec úložiště, který má být používán. ** `connection-string-setting` ** Výchozí formát je `AzureWebJobsStorage`.
* (nepovinné) : Název centra úloh Durable Functions, které má být používáno. ** `task-hub-name` ** Výchozí formát je `DurableFunctionsHub`. Lze také nastavit v [host.json](durable-functions-bindings.md#host-json)pomocí durableTask:HubName.

Následující příkaz načte stav (včetně vstupu a výstupu) instance s ID instance orchestrace 0ab8c55a66644d68a3a8b220b12d209c. Předpokládá, že používáte `func` příkaz z kořenového adresáře aplikace funkce:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

`durable get-history` Příkaz můžete použít k načtení historie instance orchestrace. To trvá následující parametry:

* (povinné) : ID instance orchestrace. ** `id` **
* (nepovinné): Název nastavení aplikace obsahující připojovací řetězec úložiště, který má být používán. ** `connection-string-setting` ** Výchozí formát je `AzureWebJobsStorage`.
* (nepovinné) : Název centra úloh Durable Functions, které má být používáno. ** `task-hub-name` ** Výchozí formát je `DurableFunctionsHub`. Lze také nastavit v host.json, pomocí durableTask:HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Dotaz na všechny instance

Spíše než dotaz jedné instance v orchestraci najednou, může být efektivnější dotaz na všechny najednou.

Metodu `GetStatusAsync` (.NET) nebo `getStatusAll` (JavaScript) můžete použít k dotazování na stavy všech instancí orchestrace. V rozhraní .NET můžete `CancellationToken` objekt předat v případě, že jej chcete zrušit. Metoda vrátí objekty se stejnými vlastnostmi jako `GetStatusAsync` metoda s parametry.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await client.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

> [!NOTE]
> Předchozí kód Jazyka C# je pro durable functions 2.x. Pro trvalé funkce 1.x `OrchestrationClient` je nutné `DurableClient` použít atribut namísto `DurableOrchestrationClient` atributu a `IDurableOrchestrationClient`místo . Další informace o rozdílech mezi verzemi naleznete v článku [verze durable functions.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instances = await client.getStatusAll();
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

Viz [Počáteční instance](#javascript-function-json) pro konfiguraci function.json.

---

### <a name="azure-functions-core-tools"></a>Základní nástroje Azure Functions

Je také možné dotazovat instance přímo pomocí příkazu [Nástroje pro základní funkce](../functions-run-local.md) `durable get-instances` Azure. To trvá následující parametry:

* (volitelné) : Tento příkaz podporuje stránkování. ** `top` ** Tento parametr odpovídá počtu instancí načtených na požadavek. Výchozí hodnota je 10.
* (nepovinné) : Token označující, kterou stránku nebo část instancí chcete načíst. ** `continuation-token` ** Každé `get-instances` spuštění vrátí token do další sady instancí.
* (nepovinné): Název nastavení aplikace obsahující připojovací řetězec úložiště, který má být používán. ** `connection-string-setting` ** Výchozí formát je `AzureWebJobsStorage`.
* (nepovinné) : Název centra úloh Durable Functions, které má být používáno. ** `task-hub-name` ** Výchozí formát je `DurableFunctionsHub`. Lze také nastavit v [host.json](durable-functions-bindings.md#host-json)pomocí durableTask:HubName.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Instance dotazu s filtry

Co když ve skutečnosti nepotřebujete všechny informace, které může poskytnout dotaz na standardní instanci? Co například, když právě hledáte čas vytvoření orchestrace nebo stav runtime orchestrace? Dotaz můžete zúžit použitím filtrů.

Pomocí `GetStatusAsync` metody (.NET) nebo `getStatusBy` (JavaScript) získáte seznam instancí orchestrace, které odpovídají sadě předdefinovaných filtrů.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    var runtimeStatus = new List<OrchestrationRuntimeStatus> {
        OrchestrationRuntimeStatus.Completed,
        OrchestrationRuntimeStatus.Running
    };
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(
        new DateTime(2018, 3, 10, 10, 1, 0),
        new DateTime(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    ); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

> [!NOTE]
> Předchozí kód Jazyka C# je pro durable functions 2.x. Pro trvalé funkce 1.x `OrchestrationClient` je nutné `DurableClient` použít atribut namísto `DurableOrchestrationClient` atributu a `IDurableOrchestrationClient`místo . Další informace o rozdílech mezi verzemi naleznete v článku [verze durable functions.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const runtimeStatus = [
        df.OrchestrationRuntimeStatus.Completed,
        df.OrchestrationRuntimeStatus.Running,
    ];
    const instances = await client.getStatusBy(
        new Date(2018, 3, 10, 10, 1, 0),
        new Date(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    );
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

Viz [Počáteční instance](#javascript-function-json) pro konfiguraci function.json.

---

### <a name="azure-functions-core-tools"></a>Základní nástroje Azure Functions

V nástrojích Core Functions Azure můžete `durable get-instances` příkaz použít také s filtry. Kromě výše uvedených `top`parametrů , `continuation-token` `connection-string-setting`a `task-hub-name` parametrů můžete použít tři parametry`created-after`filtru `created-before`( `runtime-status`, , a ).

* (nepovinné) : Načtěte instance vytvořené po tomto datu a čase (UTC). ** `created-after` ** ISO 8601 formátován datatimes přijat.
* (nepovinné) : Načtěte instance vytvořené před tímto datem a časem (UTC). ** `created-before` ** ISO 8601 formátován datatimes přijat.
* (nepovinné) : Načtení instancí s určitým stavem (například spuštění nebo dokončení). ** `runtime-status` ** Může poskytnout více (prostor ověnčené) stavy.
* (nepovinné) : Počet instancí načtených na jeden požadavek. ** `top` ** Výchozí hodnota je 10.
* (nepovinné) : Token označující, kterou stránku nebo část instancí chcete načíst. ** `continuation-token` ** Každé `get-instances` spuštění vrátí token do další sady instancí.
* (nepovinné): Název nastavení aplikace obsahující připojovací řetězec úložiště, který má být používán. ** `connection-string-setting` ** Výchozí formát je `AzureWebJobsStorage`.
* (nepovinné) : Název centra úloh Durable Functions, které má být používáno. ** `task-hub-name` ** Výchozí formát je `DurableFunctionsHub`. Lze také nastavit v [host.json](durable-functions-bindings.md#host-json)pomocí durableTask:HubName.

Pokud nezadáte žádné filtry`created-after` `created-before`( `runtime-status`, , nebo `top` ), příkaz jednoduše načte instance, bez ohledu na stav za běhu nebo čas vytvoření.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Ukončit instance

Pokud máte instanci orchestrace, která trvá příliš dlouho spustit, nebo stačí zastavit před dokončením z jakéhokoli důvodu, máte možnost ji ukončit.

Metodu `TerminateAsync` (.NET) nebo `terminate` (JavaScript) [vazby klienta orchestrace](durable-functions-bindings.md#orchestration-client) můžete použít k ukončení instancí. Tyto dva parametry `instanceId` jsou `reason` a řetězec, které jsou zapsány do protokolů a do stavu instance.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("terminate-queue")] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> Předchozí kód Jazyka C# je pro durable functions 2.x. Pro trvalé funkce 1.x `OrchestrationClient` je nutné `DurableClient` použít atribut namísto `DurableOrchestrationClient` atributu a `IDurableOrchestrationClient`místo . Další informace o rozdílech mezi verzemi naleznete v článku [verze durable functions.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

Viz [Počáteční instance](#javascript-function-json) pro konfiguraci function.json.

---

Ukončená instance nakonec přejde `Terminated` do stavu. Tento přechod se však nestane okamžitě. Spíše bude operace ukončení zařazena do fronty v centru úloh spolu s dalšími operacemi pro tuto instanci. Pomocí api [dotazu instance](#query-instances) můžete zjistit, kdy ukončená `Terminated` instance skutečně dosáhla stavu.

> [!NOTE]
> Ukončení instance se v současné době nerozšíří. Funkce aktivity a dílčí orchestrace spustit k dokončení, bez ohledu na to, zda jste ukončili instanci orchestrace, která je volala.

### <a name="azure-functions-core-tools"></a>Základní nástroje Azure Functions

Instanci orchestrace můžete také ukončit přímo pomocí příkazu [Nástroje pro základní funkce](../functions-run-local.md) `durable terminate` Azure. To trvá následující parametry:

* (povinné) : ID instance orchestrace, kterou chcete ukončit. ** `id` **
* (nepovinné) : Důvod ukončení. ** `reason` **
* (nepovinné): Název nastavení aplikace obsahující připojovací řetězec úložiště, který má být používán. ** `connection-string-setting` ** Výchozí formát je `AzureWebJobsStorage`.
* (nepovinné) : Název centra úloh Durable Functions, které má být používáno. ** `task-hub-name` ** Výchozí formát je `DurableFunctionsHub`. Lze také nastavit v [host.json](durable-functions-bindings.md#host-json)pomocí durableTask:HubName.

Následující příkaz ukončí instanci orchestrace s ID 0ab8c55a66644d68a3a8b220b12d209c:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Odeslání událostí do instancí

V některých scénářích je důležité, aby funkce orchestrátoru mohly čekat a poslouchat externí události. To zahrnuje [funkce monitorování](durable-functions-overview.md#monitoring) a funkce, které čekají na [lidskou interakci](durable-functions-overview.md#human).

Odesílat oznámení událostí spuštěným instancím pomocí metody `RaiseEventAsync` `raiseEvent` (.NET) nebo (JavaScript) [vazby klienta orchestrace](durable-functions-bindings.md#orchestration-client). Instance, které mohou zpracovávat tyto události jsou `WaitForExternalEvent` ty, které čekají na `waitForExternalEvent` volání (.NET) nebo výnosy z (JavaScript) volání.

Parametry `RaiseEventAsync` (.NET) a `raiseEvent` (JavaScript) jsou následující:

* **InstanceId**: Jedinečné ID instance.
* **Název_události**: Název události, která má být odeslána.
* **EventData**: A JSON-serializovatelné datové části odeslat do instance.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("event-queue")] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!NOTE]
> Předchozí kód Jazyka C# je pro durable functions 2.x. Pro trvalé funkce 1.x `OrchestrationClient` je nutné `DurableClient` použít atribut namísto `DurableOrchestrationClient` atributu a `IDurableOrchestrationClient`místo . Další informace o rozdílech mezi verzemi naleznete v článku [verze durable functions.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

Viz [Počáteční instance](#javascript-function-json) pro konfiguraci function.json.

---

> [!NOTE]
> Pokud neexistuje žádná instance orchestrace se zadaným ID instance, zpráva o události je zahozena. Pokud instance existuje, ale ještě nečeká na událost, událost bude uložena ve stavu instance, dokud nebude připravena k přijetí a zpracování.

### <a name="azure-functions-core-tools"></a>Základní nástroje Azure Functions

Můžete také vyvolat událost do instance orchestrace přímo pomocí příkazu [Nástroje pro základní funkce](../functions-run-local.md) `durable raise-event` Azure. To trvá následující parametry:

* (povinné) : ID instance orchestrace. ** `id` **
* **`event-name`**: Název události, která má být vyvěšena.
* (nepovinné) : Data, která mají být odeslána do instance orchestrace. ** `event-data` ** Může se jedná o cestu k souboru JSON nebo můžete poskytnout data přímo na příkazovém řádku.
* (nepovinné): Název nastavení aplikace obsahující připojovací řetězec úložiště, který má být používán. ** `connection-string-setting` ** Výchozí formát je `AzureWebJobsStorage`.
* (nepovinné) : Název centra úloh Durable Functions, které má být používáno. ** `task-hub-name` ** Výchozí formát je `DurableFunctionsHub`. Lze také nastavit v [host.json](durable-functions-bindings.md#host-json)pomocí durableTask:HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Počkejte na dokončení orchestrace

V dlouhotrvající orchestrations můžete chtít počkat a získat výsledky orchestrace. V těchto případech je také užitečné definovat časový čas orchestraci. Pokud je překročen časový limit, stav orchestrace by měla být vrácena namísto výsledků.

Metodu `WaitForCompletionOrCreateCheckStatusResponseAsync` `waitForCompletionOrCreateCheckStatusResponse` (.NET) nebo (JavaScript) lze použít k synchronního získání skutečného výstupu z instance orchestrace. Ve výchozím nastavení tyto metody používají výchozí `timeout`hodnotu 10 `retryInterval`sekund pro a 1 sekundu pro .  

Zde je příklad funkce aktivační události HTTP, která ukazuje, jak používat toto rozhraní API:

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

Viz [Počáteční instance](#javascript-function-json) pro konfiguraci function.json.

---

Volání funkce s následujícím řádkem. Pro časový interval použijte 2 sekundy a interval opakování 0,5 sekundy:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

V závislosti na době potřebné k získání odpovědi z instance orchestrace existují dva případy:

* Instance orchestrace se dokončí v rámci definovaného časového času (v tomto případě 2 sekundy) a odpověď je výstup instance skutečné orchestrace, dodaný synchronně:

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2018 06:14:29 GMT
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

* Instance orchestrace nelze dokončit v rámci definovaného časového limitu a odpověď je výchozí jedna je popsána v [zjišťování adresy URL rozhraní HTTP API](durable-functions-http-api.md):

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2018 06:13:51 GMT
        Location: http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> Formát adres URL webhooku se může lišit v závislosti na tom, kterou verzi hostitele Azure Functions používáte. Předchozí příklad je pro hostitele Azure Functions 2.0.

## <a name="retrieve-http-management-webhook-urls"></a>Načtení adres URL webového háku pro správu protokolu HTTP

Externí systém můžete použít ke sledování nebo ke zvýšení události orchestraci. Externí systémy mohou komunikovat s durable functions prostřednictvím adres URL webhooku, které jsou součástí výchozí odpovědi popsané v [zjišťování adres URL rozhraní HTTP API](durable-functions-http-features.md#http-api-url-discovery). Adresy URL webhooku lze alternativně přistupovat programově pomocí [vazby klienta orchestrace](durable-functions-bindings.md#orchestration-client). Metody `CreateHttpManagementPayload` (.NET) `createHttpManagementPayload` nebo (JavaScript) lze použít k získání serializovatelného objektu, který obsahuje tyto adresy URL webhooku.

Metody `CreateHttpManagementPayload` (.NET) `createHttpManagementPayload` a (JavaScript) mají jeden parametr:

* **instanceId**: Jedinečné ID instance.

Metody vrátí objekt s následujícími vlastnostmi řetězce:

* **Id**: ID instance orchestrace (by měla `InstanceId` být stejná jako vstup).
* **StatusQueryGetUri**: Adresa URL stavu instance orchestrace.
* **SendEventPostUri**: Adresa URL instance orchestrace "raise event".
* **TerminatePostUri**: Adresa URL "terminate" instance orchestrace.
* **PurgeHistoryDeleteUri**: Adresa URL "historie vymazání" instance orchestrace.

Funkce mohou odesílat instance těchto objektů do externích systémů ke sledování nebo vyvolávání událostí v odpovídajících orchestracích, jak je znázorněno v následujících příkladech:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] IDurableActivityContext ctx,
    [DurableClient] IDurableOrchestrationClient client,
    [DocumentDB(
        databaseName: "MonitorDB",
        collectionName: "HttpManagementPayloads",
        ConnectionStringSetting = "CosmosDBConnection")]out dynamic document)
{
    HttpManagementPayload payload = client.CreateHttpManagementPayload(ctx.InstanceId);

    // send the payload to Cosmos DB
    document = new { Payload = payload, id = ctx.InstanceId };
}
```

> [!NOTE]
> Předchozí kód Jazyka C# je pro durable functions 2.x. Pro trvalé funkce 1.x, `DurableActivityContext` musíte `IDurableActivityContext`použít místo `OrchestrationClient` , musíte `DurableClient` použít atribut namísto `DurableOrchestrationClient` atributu a `IDurableOrchestrationClient`musíte použít typ parametru místo . Další informace o rozdílech mezi verzemi naleznete v článku [verze durable functions.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

modules.exports = async function(context, ctx) {
    const client = df.getClient(context);

    const payload = client.createHttpManagementPayload(ctx.instanceId);

    // send the payload to Cosmos DB
    context.bindings.document = JSON.stringify({
        id: ctx.instanceId,
        payload,
    });
};
```

Viz [Počáteční instance](#javascript-function-json) pro konfiguraci function.json.

---

## <a name="rewind-instances-preview"></a>Instance převíjení zpět (náhled)

Pokud máte selhání orchestrace z neočekávaného důvodu, můžete *převinout* instance do dříve stavu v pořádku pomocí rozhraní API vytvořené pro tento účel.

> [!NOTE]
> Toto rozhraní API není určen jako náhrada za správné zpracování chyb a opakování zásad. Spíše je určen pouze pro případy, kdy instance orchestrace nezdaří z neočekávaných důvodů. Další informace o zpracování chyb a opakování zásady, naleznete v článku [zpracování chyb.](durable-functions-error-handling.md)

Pomocí `RewindAsync` metody (.NET) nebo `rewind` (JavaScript) [vazby klienta orchestrace](durable-functions-bindings.md#orchestration-client) převést orchestraci zpět do stavu *Spuštěno.* Tato metoda bude také znovu spustit selhání provádění aktivity nebo dílčí orchestrace, která způsobila selhání orchestrace.

Řekněme například, že máte pracovní postup zahrnující řadu [lidských schválení](durable-functions-overview.md#human). Předpokládejme, že existuje řada funkcí aktivity, které někoho upozorní, že je potřeba jeho schválení, a přečkat odezvu v reálném čase. Poté, co všechny aktivity schválení obdrželi odpovědi nebo vypršel časový limit, předpokládejme, že jiná aktivita selže z důvodu chybné konfigurace aplikace, jako je například neplatný připojovací řetězec databáze. Výsledkem je selhání orchestrace hluboko do pracovního postupu. Pomocí `RewindAsync` rozhraní API (.NET) nebo `rewind` (JavaScript) může správce aplikace opravit chybu konfigurace a převinout neúspěšnou orchestraci zpět do stavu bezprostředně před selháním. Žádný z kroků lidské interakce nemusí být znovu schválen a orchestraci lze nyní úspěšně dokončit.

> [!NOTE]
> Funkce *převíjení zpět* nepodporuje převíjení instancí orchestrace, které používají trvalé časovače.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("rewind-queue")] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

> [!NOTE]
> Předchozí kód Jazyka C# je pro durable functions 2.x. Pro trvalé funkce 1.x `OrchestrationClient` je nutné `DurableClient` použít atribut namísto `DurableOrchestrationClient` atributu a `IDurableOrchestrationClient`místo . Další informace o rozdílech mezi verzemi naleznete v článku [verze durable functions.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

Viz [Počáteční instance](#javascript-function-json) pro konfiguraci function.json.

---

### <a name="azure-functions-core-tools"></a>Základní nástroje Azure Functions

Instanci orchestrace můžete také převíjet zpět přímo pomocí příkazu [Nástroje jádra funkcí](../functions-run-local.md) `durable rewind` Azure. To trvá následující parametry:

* (povinné) : ID instance orchestrace. ** `id` **
* (nepovinné) : Důvod pro převíjení instance orchestrace. ** `reason` **
* (nepovinné): Název nastavení aplikace obsahující připojovací řetězec úložiště, který má být používán. ** `connection-string-setting` ** Výchozí formát je `AzureWebJobsStorage`.
* (nepovinné) : Název centra úloh Durable Functions, které má být používáno. ** `task-hub-name` ** Ve výchozím nastavení se používá název centra úloh v souboru [host.json.](durable-functions-bindings.md#host-json)

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Vyčistit historii instancí

Chcete-li odebrat všechna data spojená s orchestrací, můžete vymazat historii instancí. Například můžete chtít odstranit všechny řádky tabulky Azure a velké objekty BLOB zpráv přidružené k dokončené instanci. Chcete-li tak `PurgeInstanceHistoryAsync` učinit, použijte `purgeInstanceHistory` metodu (.NET) nebo (JavaScript) [vazby klienta orchestrace](durable-functions-bindings.md#orchestration-client).

Tato metoda má dvě přetížení. První přetížení vyprázdní historii ID instance orchestrace:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("purge-queue")] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    return client.purgeInstanceHistory(instanceId);
};
```

Viz [Počáteční instance](#javascript-function-json) pro konfiguraci function.json.

---

Další příklad ukazuje funkci aktivovanou časovačem, která vyčistí historii pro všechny instance orchestrace, které byly dokončeny po zadaném časovém intervalu. V tomto případě odebere data pro všechny instance dokončené před 30 nebo více dny. Je naplánováno spustit jednou denně, na 12 AM:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [TimerTrigger("0 0 12 * * *")]TimerInfo myTimer)
{
    return client.PurgeInstanceHistoryAsync(
        DateTime.MinValue,
        DateTime.UtcNow.AddDays(-30),  
        new List<OrchestrationStatus>
        {
            OrchestrationStatus.Completed
        });
}
```

> [!NOTE]
> Předchozí kód Jazyka C# je pro durable functions 2.x. Pro trvalé funkce 1.x `OrchestrationClient` je nutné `DurableClient` použít atribut namísto `DurableOrchestrationClient` atributu a `IDurableOrchestrationClient`místo . Další informace o rozdílech mezi verzemi naleznete v článku [verze durable functions.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Metodu `purgeInstanceHistoryBy` lze použít k podmíněnému vymazání historie instancí pro více instancí.

**function.json**

```json
{
  "bindings": [
    {
      "schedule": "0 0 12 * * *",
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> Tento příklad se zaměřuje na trvalé funkce verze 2.x. Ve verzi 1.x `orchestrationClient` použijte `durableClient`místo .

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function (context, myTimer) {
    const client = df.getClient(context);
    const createdTimeFrom = new Date(0);
    const createdTimeTo = new Date().setDate(today.getDate() - 30);
    const runtimeStatuses = [ df.OrchestrationRuntimeStatus.Completed ];
    return client.purgeInstanceHistoryBy(createdTimeFrom, createdTimeTo, runtimeStatuses);
};
```

---

> [!NOTE]
> Aby byla operace historie vymazání úspěšná, musí být stav za běhu cílové instance **dokončen,** **ukončen**nebo **neúspěšný**.

### <a name="azure-functions-core-tools"></a>Základní nástroje Azure Functions

Historii instance orchestrace můžete vymazat pomocí příkazu [Nástroje jádra funkcí](../functions-run-local.md) `durable purge-history` Azure. Podobně jako druhý příklad Jazyka C# v předchozím oddílu vyčistí historii pro všechny instance orchestrace vytvořené během zadaného časového intervalu. Můžete dále filtrovat vyčištěné instance podle stavu za běhu. Příkaz má několik parametrů:

* (nepovinné) : Vymazání historie instancí vytvořených po tomto datu a čase (UTC). ** `created-after` ** ISO 8601 formátován datatimes přijat.
* (nepovinné) : Vymazání historie instancí vytvořených před tímto datem a časem (UTC). ** `created-before` ** ISO 8601 formátován datatimes přijat.
* (nepovinné) : Vymazání historie instancí s určitým stavem (například spuštění nebo dokončení). ** `runtime-status` ** Může poskytnout více (prostor ověnčené) stavy.
* (nepovinné): Název nastavení aplikace obsahující připojovací řetězec úložiště, který má být používán. ** `connection-string-setting` ** Výchozí formát je `AzureWebJobsStorage`.
* (nepovinné) : Název centra úloh Durable Functions, které má být používáno. ** `task-hub-name` ** Ve výchozím nastavení se používá název centra úloh v souboru [host.json.](durable-functions-bindings.md#host-json)

Následující příkaz odstraní historii všech neúspěšných instancí vytvořených před 14.

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Odstranění centra úloh

Pomocí příkazu [Nástroje pro základní funkce](../functions-run-local.md) `durable delete-task-hub` Azure můžete odstranit všechny artefakty úložiště přidružené k určitému centru úloh, včetně tabulek úložiště Azure, front a objektů BLOB. Příkaz má dva parametry:

* (nepovinné): Název nastavení aplikace obsahující připojovací řetězec úložiště, který má být používán. ** `connection-string-setting` ** Výchozí formát je `AzureWebJobsStorage`.
* (nepovinné) : Název centra úloh Durable Functions, které má být používáno. ** `task-hub-name` ** Ve výchozím nastavení se používá název centra úloh v souboru [host.json.](durable-functions-bindings.md#host-json)

Následující příkaz odstraní všechna data úložiště `UserTest` Azure přidružená k centru úloh.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přečtěte si, jak zpracovat správu verzí](durable-functions-versioning.md)

> [!div class="nextstepaction"]
> [Integrovaný odkaz http api pro správu instancí](durable-functions-http-api.md)
