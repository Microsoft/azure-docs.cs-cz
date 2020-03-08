---
title: Správa instancí v Durable Functions – Azure
description: Naučte se spravovat instance v rozšíření Durable Functions pro Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 07a96fdd6350d8db38a92c23e510afb05f7416fb
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78371964"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Správa instancí v Durable Functions v Azure

Pokud používáte rozšíření [Durable Functions](durable-functions-overview.md) pro Azure Functions nebo chcete začít, ujistěte se, že jste si vyžádali, abyste se dostali co nejvíce využívali. Můžete optimalizovat své Durable Functions instance orchestrace tím, že se dozvíte víc o tom, jak je spravovat. Tento článek odkazuje na podrobnosti o každé operaci správy instancí.

Můžete začít a ukončovat instance, například, a můžete zadávat dotazy na instance, včetně možnosti dotazování všech instancí a instancí dotazů pomocí filtrů. Kromě toho můžete odesílat události do instancí, počkat na dokončení Orchestrace a načíst adresy URL Webhooku pro správu protokolu HTTP. Tento článek se zabývá i dalšími operacemi správy, včetně převíjení instancí, vymazáním Historie instancí a odstraněním centra úloh.

V Durable Functions máte možnosti, jak chcete implementovat jednotlivé operace správy. Tento článek popisuje příklady, které používají [Azure Functions Core Tools](../functions-run-local.md) pro .NET (C#) i JavaScript.

## <a name="start-instances"></a>Počáteční instance

Je důležité, abyste mohli spustit instanci orchestrace. To se obvykle provádí při použití vazby Durable Functions v triggeru jiné funkce.

Metoda `StartNewAsync` (.NET) nebo `startNew` (JavaScript) na [vazbě klienta Orchestration](durable-functions-bindings.md#orchestration-client) spouští novou instanci. Interně Tato metoda zařadí zprávu do fronty ovládacích prvků, která pak aktivuje začátek funkce se zadaným názvem, který používá [aktivační vazbu orchestrace](durable-functions-bindings.md#orchestration-trigger).

Tato asynchronní operace se dokončí při úspěšném naplánování procesu orchestrace.

Parametry pro spuštění nové instance orchestrace jsou následující:

* **Name**(název): název funkce Orchestrator, která má být naplánovaná.
* **Input**: všechna data serializovatelný ve formátu JSON, která by měla být předána jako vstup do funkce Orchestrator.
* **InstanceId**: (volitelné) jedinečné ID instance. Pokud tento parametr nezadáte, použije metoda náhodné ID.

> [!TIP]
> Pro ID instance použijte náhodný identifikátor. ID náhodných instancí vám pomůžou zajistit stejnou distribuci zatížení při škálování funkcí Orchestrator napříč několika virtuálními počítači. Vhodný čas pro použití nenáhodných ID instancí je, když ID musí pocházet z externího zdroje, nebo při implementaci vzoru s [jedním prvkem Orchestrator](durable-functions-singletons.md) .

Následující kód je ukázková funkce, která spouští novou instanci orchestrace:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HelloWorldManualStart")]
public static async Task Run(
    [ManualTrigger] string input,
    [DurableClient] IDurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

> [!NOTE]
> Předchozí C# kód je pro Durable Functions 2. x. Pro Durable Functions 1. x je nutné použít atribut `OrchestrationClient` namísto atributu `DurableClient` a musíte použít typ parametru `DurableOrchestrationClient` namísto `IDurableOrchestrationClient`. Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<a name="javascript-function-json"></a>Pokud není uvedeno jinak, používají příklady na této stránce Trigger HTTP s následujícím souborem Function. JSON.

**Function. JSON**

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
> Tento příklad cílí na Durable Functions verze 2. x. V části verze 1. x použijte `orchestrationClient` místo `durableClient`.

**index. js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Instanci můžete také spustit přímo pomocí příkazu [Azure Functions Core Tools](../functions-run-local.md) `durable start-new`. Má následující parametry:

* **`function-name` (povinné)** : název funkce, která se má spustit.
* **`input` (volitelné)** : vstup do funkce, buď vloženou, nebo prostřednictvím souboru JSON. Pro soubory přidejte předponu k cestě k souboru s `@`, například `@path/to/file.json`.
* **`id` (volitelné)** : ID instance orchestrace. Pokud tento parametr nezadáte, použije příkaz náhodný identifikátor GUID.
* **`connection-string-setting` (volitelné)** : název nastavení aplikace, které obsahuje připojovací řetězec úložiště, který se má použít. Výchozí hodnota je AzureWebJobsStorage.
* **`task-hub-name` (volitelné)** : název Durable Functions centra úloh, které se má použít. Výchozí hodnota je DurableFunctionsHub. Tuto hodnotu můžete nastavit také v souboru [Host. JSON](durable-functions-bindings.md#host-json) pomocí DurableTask: HubName.

> [!NOTE]
> Základní příkazy nástrojů předpokládají, že je spouštíte z kořenového adresáře aplikace Function App. Pokud explicitně zadáte parametry `connection-string-setting` a `task-hub-name`, můžete spustit příkazy z libovolného adresáře. I když můžete spustit tyto příkazy bez hostitele aplikace Function App, může se stát, že nebudete mít pozor na některé efekty, pokud hostitel neběží. Například příkaz `start-new` zařazování počáteční zprávy do cílového centra úloh, ale orchestrace se ve skutečnosti nespustí, pokud není spuštěn hostitelský proces aplikace Functions, který může zprávu zpracovat.

Následující příkaz spustí funkci s názvem HelloWorld a předá obsah souboru, `counter-data.json` do něj:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Instance dotazů

V rámci snahy o správu orchestrací budete pravděpodobně potřebovat shromáždit informace o stavu instance orchestrace (například bez ohledu na to, zda byla dokončena normálně nebo neúspěšná).

`GetStatusAsync` (.NET) nebo metoda `getStatus` (JavaScript) na [vazbě klienta Orchestration](durable-functions-bindings.md#orchestration-client) se dotazuje na stav instance Orchestration.

Přebírá `instanceId` (povinné), `showHistory` (volitelné), `showHistoryOutput` (volitelné) a `showInput` (volitelné) jako parametry.

* **`showHistory`** : Pokud je nastavená na `true`, odpověď obsahuje historii spuštění.
* **`showHistoryOutput`** : Pokud je nastavená možnost `true`, historie spouštění obsahuje výstupy aktivit.
* **`showInput`** : Pokud je nastavená na `false`, odpověď nebude obsahovat vstup funkce. Výchozí hodnota je `true`.

Metoda vrátí objekt s následujícími vlastnostmi:

* **Název**: název funkce nástroje Orchestrator.
* **InstanceId**: ID instance orchestrace (měla by být stejná jako u `instanceId`ho vstupu).
* **CreatedTime**: čas spuštění funkce Orchestrator.
* **LastUpdatedTime**: čas poslední kontrolního bodu orchestrace.
* **Input**: vstup funkce jako hodnota JSON. Pokud je `showInput` false, toto pole se nevyplní.
* **CustomStatus**: stav vlastní orchestrace ve formátu JSON.
* **Výstup**: výstup funkce jako hodnota JSON (Pokud byla funkce dokončená). Pokud se funkce Orchestrator nezdařila, obsahuje tato vlastnost podrobnosti o selhání. Pokud byla funkce Orchestrator ukončena, obsahuje tato vlastnost důvod ukončení (pokud existuje).
* **RuntimeStatus**: jedna z následujících hodnot:
  * **Čeká na vyřízení**: instance byla naplánována, ale ještě nebyla spuštěna.
  * **Spuštěno**: instance začala běžet.
  * **Dokončeno**: instance se normálně dokončila.
  * **ContinuedAsNew**: instance se sama restartovala s novou historií. Tento stav je přechodný.
  * **Selhalo**: instance se nezdařila s chybou.
  * **Ukončeno**: instance byla náhle zastavena.
* **History**: historie spouštění orchestrace. Toto pole se naplní jenom v případě, že je `showHistory` nastavené na `true`.

Tato metoda vrací `null` (.NET) nebo `undefined` (JavaScript), pokud instance neexistuje.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    DurableOrchestrationStatus status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

> [!NOTE]
> Předchozí C# kód je pro Durable Functions 2. x. Pro Durable Functions 1. x je nutné použít atribut `OrchestrationClient` namísto atributu `DurableClient` a musíte použít typ parametru `DurableOrchestrationClient` namísto `IDurableOrchestrationClient`. Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

Viz [počáteční instance](#javascript-function-json) pro konfiguraci Function. JSON.

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Stav instance orchestrace je také možné získat přímo pomocí příkazu [Azure Functions Core Tools](../functions-run-local.md) `durable get-runtime-status`. Má následující parametry:

* **`id` (povinné)** : ID instance orchestrace.
* **`show-input` (volitelné)** : Pokud je nastavena na `true`, odpověď obsahuje vstup funkce. Výchozí hodnota je `false`.
* **`show-output` (volitelné)** : Pokud je nastavena na `true`, odpověď obsahuje výstup funkce. Výchozí hodnota je `false`.
* **`connection-string-setting` (volitelné)** : název nastavení aplikace, které obsahuje připojovací řetězec úložiště, který se má použít. Výchozí formát je `AzureWebJobsStorage`.
* **`task-hub-name` (volitelné)** : název Durable Functions centra úloh, které se má použít. Výchozí formát je `DurableFunctionsHub`. Dá se taky nastavit v souboru [Host. JSON](durable-functions-bindings.md#host-json)pomocí DurableTask: HubName.

Následující příkaz načte stav (včetně vstupu a výstupu) instance s ID instance orchestrace 0ab8c55a66644d68a3a8b220b12d209c. Předpokládá se, že spouštíte příkaz `func` z kořenového adresáře aplikace Function App:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

Pomocí příkazu `durable get-history` můžete načíst historii instance Orchestration. Má následující parametry:

* **`id` (povinné)** : ID instance orchestrace.
* **`connection-string-setting` (volitelné)** : název nastavení aplikace, které obsahuje připojovací řetězec úložiště, který se má použít. Výchozí formát je `AzureWebJobsStorage`.
* **`task-hub-name` (volitelné)** : název Durable Functions centra úloh, které se má použít. Výchozí formát je `DurableFunctionsHub`. Dá se taky nastavit v souboru Host. JSON pomocí durableTask: HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Dotazování všech instancí

Spíše než dotazování jedné instance orchestrace v čase, může být efektivnější dotazování na ně.

K dotazování stavů všech instancí orchestrace lze použít metodu `GetStatusAsync` (.NET) nebo `getStatusAll` (JavaScript). V rozhraní .NET můžete předat objekt `CancellationToken` pro případ, že ho chcete zrušit. Metoda vrátí objekty se stejnými vlastnostmi jako metoda `GetStatusAsync` s parametry.

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
> Předchozí C# kód je pro Durable Functions 2. x. Pro Durable Functions 1. x je nutné použít atribut `OrchestrationClient` namísto atributu `DurableClient` a musíte použít typ parametru `DurableOrchestrationClient` namísto `IDurableOrchestrationClient`. Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

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

Viz [počáteční instance](#javascript-function-json) pro konfiguraci Function. JSON.

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Je také možné zadat dotaz přímo na instance pomocí příkazu [Azure Functions Core Tools](../functions-run-local.md) `durable get-instances`. Má následující parametry:

* **`top` (volitelné)** : Tento příkaz podporuje stránkování. Tento parametr odpovídá počtu instancí načtených na požadavek. Výchozí hodnota je 10.
* **`continuation-token` (volitelné)** : token, který označuje, kterou stránku nebo oddíl instancí se má načíst. Každé spuštění `get-instances` vrátí token k další sadě instancí.
* **`connection-string-setting` (volitelné)** : název nastavení aplikace, které obsahuje připojovací řetězec úložiště, který se má použít. Výchozí formát je `AzureWebJobsStorage`.
* **`task-hub-name` (volitelné)** : název Durable Functions centra úloh, které se má použít. Výchozí formát je `DurableFunctionsHub`. Dá se taky nastavit v souboru [Host. JSON](durable-functions-bindings.md#host-json)pomocí DurableTask: HubName.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Dotazování instancí s filtry

Co když opravdu nepotřebujete všechny informace, které může dotaz standardní instance poskytnout? Například co když hledáte jenom čas vytvoření orchestrace, nebo běhový stav orchestrace? Dotaz můžete zúžit použitím filtrů.

Použijte metodu `GetStatusAsync` (.NET) nebo `getStatusBy` (JavaScript) k získání seznamu instancí orchestrace, které odpovídají sadě předdefinovaných filtrů.

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
> Předchozí C# kód je pro Durable Functions 2. x. Pro Durable Functions 1. x je nutné použít atribut `OrchestrationClient` namísto atributu `DurableClient` a musíte použít typ parametru `DurableOrchestrationClient` namísto `IDurableOrchestrationClient`. Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

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

Viz [počáteční instance](#javascript-function-json) pro konfiguraci Function. JSON.

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

V Azure Functions Core Tools můžete použít také příkaz `durable get-instances` s filtry. Kromě výše uvedených `top`, `continuation-token`, `connection-string-setting`a parametrů `task-hub-name` můžete použít tři parametry filtru (`created-after`, `created-before`a `runtime-status`).

* **`created-after` (volitelné)** : načíst instance vytvořené po tomto datu a čase (UTC). Byly přijaty hodnoty DateTime ve formátu ISO 8601.
* **`created-before` (volitelné)** : načíst instance vytvořené před tímto datem a časem (UTC). Byly přijaty hodnoty DateTime ve formátu ISO 8601.
* **`runtime-status` (volitelné)** : načíst instance s určitým stavem (například spuštěno nebo dokončeno). Může poskytovat více stavů (oddělené místo).
* **`top` (volitelné)** : počet instancí načtených na žádost. Výchozí hodnota je 10.
* **`continuation-token` (volitelné)** : token, který označuje, kterou stránku nebo oddíl instancí se má načíst. Každé spuštění `get-instances` vrátí token k další sadě instancí.
* **`connection-string-setting` (volitelné)** : název nastavení aplikace, které obsahuje připojovací řetězec úložiště, který se má použít. Výchozí formát je `AzureWebJobsStorage`.
* **`task-hub-name` (volitelné)** : název Durable Functions centra úloh, které se má použít. Výchozí formát je `DurableFunctionsHub`. Dá se taky nastavit v souboru [Host. JSON](durable-functions-bindings.md#host-json)pomocí DurableTask: HubName.

Pokud neposkytnete žádné filtry (`created-after`, `created-before`nebo `runtime-status`), příkaz jednoduše načte `top` instance bez ohledu na stav modulu runtime nebo čas vytvoření.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Ukončit instance

Máte-li instanci orchestrace, která trvá příliš dlouho, nebo ji pouze potřebujete zastavit, než se z nějakého důvodu dokončí, máte možnost ji ukončit.

K ukončení instancí lze použít metodu `TerminateAsync` (.NET) nebo `terminate` (JavaScript) [vazby klienta Orchestration](durable-functions-bindings.md#orchestration-client) . Dva parametry jsou `instanceId` a `reason` řetězec, který se zapisuje do protokolů a do stavu instance.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> Předchozí C# kód je pro Durable Functions 2. x. Pro Durable Functions 1. x je nutné použít atribut `OrchestrationClient` namísto atributu `DurableClient` a musíte použít typ parametru `DurableOrchestrationClient` namísto `IDurableOrchestrationClient`. Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

Viz [počáteční instance](#javascript-function-json) pro konfiguraci Function. JSON.

---

Ukončená instance bude nakonec převedena do stavu `Terminated`. Tento přechod ale nebude hned probíhat. Místo toho bude operace ukončení zařazena do fronty v centru úloh spolu s dalšími operacemi této instance. Pomocí rozhraní API pro [dotazování instance](#query-instances) můžete zjistit, kdy ukončená instance skutečně dosáhla `Terminated` stavu.

> [!NOTE]
> Ukončení instance v současné době nešíří. Funkce aktivity a dílčí orchestrace jsou spouštěny k dokončení bez ohledu na to, zda jste ukončili instanci orchestrace, která je jim volána.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Instanci orchestrace můžete také ukončit přímo pomocí příkazu [Azure Functions Core Tools](../functions-run-local.md) `durable terminate`. Má následující parametry:

* **`id` (povinné)** : ID instance orchestrace, která se má ukončit.
* **`reason` (volitelné)** : důvod ukončení.
* **`connection-string-setting` (volitelné)** : název nastavení aplikace, které obsahuje připojovací řetězec úložiště, který se má použít. Výchozí formát je `AzureWebJobsStorage`.
* **`task-hub-name` (volitelné)** : název Durable Functions centra úloh, které se má použít. Výchozí formát je `DurableFunctionsHub`. Dá se taky nastavit v souboru [Host. JSON](durable-functions-bindings.md#host-json)pomocí DurableTask: HubName.

Následující příkaz ukončí instanci orchestrace s ID 0ab8c55a66644d68a3a8b220b12d209c:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Odesílání událostí do instancí

V některých scénářích je důležité, aby funkce nástroje Orchestrator dokázala počkat a naslouchat externím událostem. To zahrnuje [funkce](durable-functions-overview.md#monitoring) a funkce monitorování, které čekají na [lidskou interakci](durable-functions-overview.md#human).

Odešlete oznámení o událostech na spuštěné instance pomocí metody `RaiseEventAsync` (.NET) nebo metody `raiseEvent` (JavaScript) pro [vazbu klienta Orchestration](durable-functions-bindings.md#orchestration-client). Instance, které mohou zpracovávat tyto události, jsou ty, které čekají na volání `WaitForExternalEvent` (.NET) nebo v volání `waitForExternalEvent` (JavaScript).

Parametry pro `RaiseEventAsync` (.NET) a `raiseEvent` (JavaScript) jsou následující:

* **InstanceId**: jedinečné ID instance.
* **EventName**: název události, která se má odeslat.
* **EventData**: datová část s možností serializace JSON pro odeslání do instance.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!NOTE]
> Předchozí C# kód je pro Durable Functions 2. x. Pro Durable Functions 1. x je nutné použít atribut `OrchestrationClient` namísto atributu `DurableClient` a musíte použít typ parametru `DurableOrchestrationClient` namísto `IDurableOrchestrationClient`. Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

Viz [počáteční instance](#javascript-function-json) pro konfiguraci Function. JSON.

---

> [!NOTE]
> Pokud není k dispozici žádná instance orchestrace se zadaným ID instance, zpráva události bude zahozena. Pokud instance existuje, ale ještě nečeká na událost, bude událost uložena ve stavu instance, dokud nebude připravena k přijetí a zpracování.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Událost můžete také vyvolat přímo do instance orchestrace pomocí příkazu [Azure Functions Core Tools](../functions-run-local.md) `durable raise-event`. Má následující parametry:

* **`id` (povinné)** : ID instance orchestrace.
* **`event-name`** : název události, která má být vyvolána.
* **`event-data` (volitelné)** : data, která se mají odeslat do instance orchestrace. Může to být cesta k souboru JSON nebo data můžete zadat přímo na příkazovém řádku.
* **`connection-string-setting` (volitelné)** : název nastavení aplikace, které obsahuje připojovací řetězec úložiště, který se má použít. Výchozí formát je `AzureWebJobsStorage`.
* **`task-hub-name` (volitelné)** : název Durable Functions centra úloh, které se má použít. Výchozí formát je `DurableFunctionsHub`. Dá se taky nastavit v souboru [Host. JSON](durable-functions-bindings.md#host-json)pomocí DurableTask: HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Počkat na dokončení orchestrace

V dlouhotrvající orchestraci můžete chtít počkat a získat výsledky orchestrace. V těchto případech je také užitečné, abyste mohli definovat dobu časového limitu pro orchestraci. Pokud dojde k překročení časového limitu, měl by se místo výsledků vracet stav orchestrace.

Metodu `WaitForCompletionOrCreateCheckStatusResponseAsync` (.NET) nebo `waitForCompletionOrCreateCheckStatusResponse` (JavaScript) lze použít k synchronnímu získání skutečného výstupu z instance orchestrace. Ve výchozím nastavení používají tyto metody výchozí hodnotu 10 sekund pro `timeout`a 1 sekundu pro `retryInterval`.  

Tady je příklad funkce triggeru HTTP, která ukazuje, jak používat toto rozhraní API:

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

Viz [počáteční instance](#javascript-function-json) pro konfiguraci Function. JSON.

---

Zavolejte funkci s následujícím řádkem. Pro interval opakování použijte 2 sekundy na časový limit a 0,5 sekund:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

V závislosti na době potřebné k získání odpovědi z instance orchestrace existují dva případy:

* Instance Orchestration se dokončí v rámci definovaného časového limitu (v tomto případě 2 sekundy) a odpověď je skutečný výstup instance orchestrace, který se doručí synchronně:

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

* Instance orchestrace se nemůžou dokončit v rámci definovaného časového limitu a odpověď je výchozí hodnotou, která je popsaná v tématu [zjišťování adresy URL protokolu HTTP API](durable-functions-http-api.md):

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
> Formát adres URL Webhooku se může lišit v závislosti na tom, kterou verzi Azure Functions hostitele používáte. Předchozí příklad je pro hostitele Azure Functions 2,0.

## <a name="retrieve-http-management-webhook-urls"></a>Načíst adresy URL Webhooku pro správu HTTP

Externí systém můžete použít k monitorování nebo vyvolání událostí pro orchestraci. Externí systémy mohou komunikovat s Durable Functions prostřednictvím adres URL Webhooku, které jsou součástí výchozí odpovědi popsané v tématu [zjišťování adresy URL protokolu HTTP API](durable-functions-http-features.md#http-api-url-discovery). Adresy URL Webhooku můžete případně programově přistupovat pomocí [vazby klienta Orchestration](durable-functions-bindings.md#orchestration-client). K získání serializovatelných objektů, které obsahují tyto adresy URL webhooků, lze použít metody `CreateHttpManagementPayload` (.NET) nebo `createHttpManagementPayload` (JavaScript).

Metody `CreateHttpManagementPayload` (.NET) a `createHttpManagementPayload` (JavaScript) mají jeden parametr:

* **InstanceId**: jedinečné ID instance.

Metody vrátí objekt s následujícími vlastnostmi řetězce:

* **ID**: ID instance orchestrace (měla by být stejná jako u `InstanceId`ho vstupu).
* **StatusQueryGetUri**: adresa URL stavu instance Orchestration.
* **SendEventPostUri**: adresa URL vyvolání události instance Orchestration.
* **TerminatePostUri**: adresa URL pro ukončení instance Orchestration.
* **PurgeHistoryDeleteUri**: adresa URL vymazání historie instance Orchestration.

Funkce mohou odesílat instance těchto objektů do externích systémů a monitorovat nebo přivolávat události pro příslušné orchestrace, jak je znázorněno v následujících příkladech:

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
> Předchozí C# kód je pro Durable Functions 2. x. Pro Durable Functions 1. x je nutné použít `DurableActivityContext` namísto `IDurableActivityContext`, je nutné použít atribut `OrchestrationClient` namísto atributu `DurableClient` a musíte použít typ parametru `DurableOrchestrationClient` namísto `IDurableOrchestrationClient`. Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

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

Viz [počáteční instance](#javascript-function-json) pro konfiguraci Function. JSON.

---

## <a name="rewind-instances-preview"></a>Instance převinutí (Preview)

Pokud dojde k selhání orchestrace z neočekávaného důvodu, můžete instanci *Převinout* do dříve funkčního stavu pomocí rozhraní API sestaveného pro tento účel.

> [!NOTE]
> Toto rozhraní API není určeno jako náhrada za správné zpracování chyb a zásady opakování. Místo toho je určeno pro použití pouze v případě, že instance Orchestration selžou z neočekávaných důvodů. Další informace o zpracování chyb a zásadách opakování najdete v článku o [zpracování chyb](durable-functions-error-handling.md) .

Použijte metodu `RewindAsync` (.NET) nebo `rewind` (JavaScript) [vazby klienta Orchestration](durable-functions-bindings.md#orchestration-client) k umístění orchestrace zpátky do *běžícího* stavu. Tato metoda také spustí opětovné spuštění aktivity nebo selhání provádění dílčí orchestrace, které způsobily selhání orchestrace.

Řekněme například, že máte pracovní postup zahrnující řadu [lidských schválení](durable-functions-overview.md#human). Předpokládejme, že existuje řada funkcí aktivity, které upozorní uživatele, že je potřeba jejich schválení, a vyčkejte na odpověď v reálném čase. Po přijetí odpovědí nebo vypršení časového limitu u všech aktivit schválení dojde k chybě z důvodu nesprávné konfigurace aplikace, jako je například Neplatný připojovací řetězec databáze. Výsledkem je selhání orchestrace hluboko do pracovního postupu. S rozhraním API `RewindAsync` (.NET) nebo `rewind` (JavaScript) může správce aplikace opravit chybu konfigurace a převinout neúspěšnou orchestraci zpátky na stav těsně před selháním. Žádný z kroků lidské interakce není nutné znovu schválit a orchestraci je teď možné úspěšně dokončit.

> [!NOTE]
> Funkce *Rewind* nepodporuje převíjení instancí orchestrace, které používají trvalé časovače.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

> [!NOTE]
> Předchozí C# kód je pro Durable Functions 2. x. Pro Durable Functions 1. x je nutné použít atribut `OrchestrationClient` namísto atributu `DurableClient` a musíte použít typ parametru `DurableOrchestrationClient` namísto `IDurableOrchestrationClient`. Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

Viz [počáteční instance](#javascript-function-json) pro konfiguraci Function. JSON.

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Instanci orchestrace můžete také převinout přímo pomocí příkazu [Azure Functions Core Tools](../functions-run-local.md) `durable rewind`. Má následující parametry:

* **`id` (povinné)** : ID instance orchestrace.
* **`reason` (volitelné)** : důvod pro převinutí instance Orchestration.
* **`connection-string-setting` (volitelné)** : název nastavení aplikace, které obsahuje připojovací řetězec úložiště, který se má použít. Výchozí formát je `AzureWebJobsStorage`.
* **`task-hub-name` (volitelné)** : název Durable Functions centra úloh, které se má použít. Ve výchozím nastavení se používá název centra úloh v souboru [Host. JSON](durable-functions-bindings.md#host-json) .

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Vymazat historii instance

Chcete-li odebrat všechna data přidružená k orchestraci, můžete historii instancí vyprázdnit. Například můžete chtít odstranit všechny řádky tabulky Azure a velké objekty blob zpráv přidružené k dokončené instanci. Chcete-li to provést, použijte metodu `PurgeInstanceHistoryAsync` (.NET) nebo `purgeInstanceHistory` (JavaScript) [vazby klienta Orchestration](durable-functions-bindings.md#orchestration-client).

Tato metoda má dvě přetížení. První přetížení vymaže historii ID instance Orchestration:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
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

Viz [počáteční instance](#javascript-function-json) pro konfiguraci Function. JSON.

---

Následující příklad ukazuje funkci aktivovanou časovačem, která vyprázdní historii pro všechny instance orchestrace, které byly dokončeny po zadaném časovém intervalu. V takovém případě dojde k odebrání dat pro všechny instance, které byly dokončeny před 30 nebo více dny. Je naplánováno, že se spustí jednou za den, ve 12 dop.:

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
> Předchozí C# kód je pro Durable Functions 2. x. Pro Durable Functions 1. x je nutné použít atribut `OrchestrationClient` namísto atributu `DurableClient` a musíte použít typ parametru `DurableOrchestrationClient` namísto `IDurableOrchestrationClient`. Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Metodu `purgeInstanceHistoryBy` lze použít k podmíněně vyprázdnit historii instancí pro více instancí.

**Function. JSON**

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
> Tento příklad cílí na Durable Functions verze 2. x. V části verze 1. x použijte `orchestrationClient` místo `durableClient`.

**index. js**

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
> Aby operace vyprázdnění historie proběhla úspěšně, musí být stav modulu runtime cílové instance **dokončený**, **ukončený**nebo **se nezdařilo**.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Historii instance orchestrace můžete vyprázdnit pomocí příkazu [Azure Functions Core Tools](../functions-run-local.md) `durable purge-history`. Podobně jako druhý C# příklad v předchozí části vyprázdní historii pro všechny instance orchestrace vytvořené během zadaného časového intervalu. Vyčištěné instance můžete dále filtrovat podle běhového stavu. Příkaz má několik parametrů:

* **`created-after` (volitelné)** : vyprázdní historii instancí vytvořených po tomto datu a čase (UTC). Byly přijaty hodnoty DateTime ve formátu ISO 8601.
* **`created-before` (volitelné)** : vyprázdní historii instancí vytvořených před tímto datem a časem (UTC). Byly přijaty hodnoty DateTime ve formátu ISO 8601.
* **`runtime-status` (volitelné)** : vyprázdní historii instancí s určitým stavem (například spuštěno nebo dokončeno). Může poskytovat více stavů (oddělené místo).
* **`connection-string-setting` (volitelné)** : název nastavení aplikace, které obsahuje připojovací řetězec úložiště, který se má použít. Výchozí formát je `AzureWebJobsStorage`.
* **`task-hub-name` (volitelné)** : název Durable Functions centra úloh, které se má použít. Ve výchozím nastavení se používá název centra úloh v souboru [Host. JSON](durable-functions-bindings.md#host-json) .

Následující příkaz odstraní historii všech neúspěšných instancí vytvořených před 14. listopadu 2018 na 7:35 odp. (UTC).

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Odstranění centra úloh

Pomocí příkazu [Azure Functions Core Tools](../functions-run-local.md) `durable delete-task-hub` můžete odstranit všechny artefakty úložiště přidružené ke konkrétnímu centru úloh, včetně tabulek, front a objektů BLOB služby Azure Storage. Příkaz má dva parametry:

* **`connection-string-setting` (volitelné)** : název nastavení aplikace, které obsahuje připojovací řetězec úložiště, který se má použít. Výchozí formát je `AzureWebJobsStorage`.
* **`task-hub-name` (volitelné)** : název Durable Functions centra úloh, které se má použít. Ve výchozím nastavení se používá název centra úloh v souboru [Host. JSON](durable-functions-bindings.md#host-json) .

Následující příkaz odstraní všechna data služby Azure Storage přidružená k `UserTest` centrum úloh.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Informace o tom, jak zpracovávat správu verzí](durable-functions-versioning.md)

> [!div class="nextstepaction"]
> [Reference k integrovanému HTTP API pro správu instancí](durable-functions-http-api.md)
