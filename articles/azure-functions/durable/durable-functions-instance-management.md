---
title: Správa instancí v Durable Functions – Azure
description: Další informace o správě instance v rozšíření Durable Functions pro službu Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: ee96bc5e17051ab37be34eecbb8e4fe35599cd5d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60730765"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Správa instancí v odolná služba Functions v Azure

Pokud používáte [Durable Functions](durable-functions-overview.md) rozšíření pro Azure Functions nebo chcete začít tím, ujistěte se, že se vám co nejlíp využít z ní. Vaše instance Orchestrace Durable Functions můžete optimalizovat podle dalších informací o tom, jak je spravovat. Tento článek probírá do podrobnosti o jednotlivých operacích správy instance.

Můžete spustit a ukončit instancí, například a můžete dát dotaz na instance, včetně možnosti dotazování všechny instance a instance dotazu pomocí filtrů. Kromě toho můžete odesílání událostí do instancí, počkat na dokončení Orchestrace a načíst adresy URL webhooku správy HTTP. Tento článek obsahuje jiné operace správy, příliš, včetně zpět instancí, odstraňovat zařízení nepřipojená k instanci historie a odstraňuje se Centrum úkolů.

V Durable Functions máte možnosti, jak chcete provádět každou z těchto operací správy. Tento článek obsahuje příklady, které používají [nástrojů Azure Functions Core](../functions-run-local.md) pro obě rozhraní .NET (C#) a JavaScript.

## <a name="start-instances"></a>Spuštění instancí

Je důležité, aby bylo možné spustit instanci Orchestrace. To se obvykle provádí při použití vazby Durable Functions v jiné funkce triggeru.

[StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) metodu [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) nebo `startNew` na `DurableOrchestrationClient` (JavaScript) spouští novou instanci. Získání instance této třídy pomocí `orchestrationClient` vazby. Interně tato to metoda zařadí zprávu do fronty, ovládací prvek, který potom aktivuje spuštění funkce se zadaným názvem, který používá `orchestrationTrigger` aktivovat vazby.

Tato asynchronní operace dokončí, když se úspěšně naplánovala procesu Orchestrace. Během 30 sekund se měl spustit proces Orchestrace. Pokud to trvá déle, zobrazí se vám `TimeoutException`.

> [!WARNING]
> Při vývoji místně v jazyce JavaScript, nastavte proměnnou prostředí `WEBSITE_HOSTNAME` k `localhost:<port>` (například `localhost:7071`) použití metod na `DurableOrchestrationClient`. Další informace o tomto požadavku najdete v tématu [problém Githubu](https://github.com/Azure/azure-functions-durable-js/issues/28).

### <a name="net"></a>.NET

Parametry tak, aby [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) jsou následující:

* **Název**: Název funkce orchestrátoru, který chcete naplánovat.
* **Vstup**: Všechna serializovat JSON data, která mají být předány jako vstup do funkce orchestrátoru.
* **instanceId**: (Volitelné) Jedinečné ID instance. Pokud tento parametr nezadáte, metoda používá náhodné ID.

Tady je jednoduchý příklad jazyka C#:

```csharp
[FunctionName("HelloWorldManualStart")]
public static async Task Run(
    [ManualTrigger] string input,
    [OrchestrationClient] DurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (funguje pouze 2.x)

Parametry tak, aby `startNew` jsou následující:

* **Název**: Název funkce orchestrátoru, který chcete naplánovat.
* **instanceId**: (Volitelné) Jedinečné ID instance. Pokud tento parametr nezadáte, metoda používá náhodné ID.
* **Vstup**: (Volitelné) Všechna serializovat JSON data, která mají být předány jako vstup do funkce orchestrátoru.

Tady je jednoduchý příklad v jazyce JavaScript:

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

> [!TIP]
> Použijte náhodné identifikátor pro ID instance. To pomáhá zajistit distribuci zatížení pomocí stejné, když funkce orchestrátoru škálujeme kapacitu na víc virtuálních počítačů. Správný čas použití ID nenáhodný instancí je při ID musí pocházet z externího zdroje, nebo když implementujete [singleton orchestrator](durable-functions-singletons.md) vzor.

### <a name="azure-functions-core-tools"></a>Nástroje Azure Functions Core

Můžete také spustit instanci přímo pomocí [nástrojů Azure Functions Core](../functions-run-local.md) `durable start-new` příkazu. Ji používá následující parametry:

* **`function-name` (povinné)** : Název funkce, který má spustit.
* **`input` (volitelné)** : Vstup do funkce, buď jako vložené nebo pomocí souboru JSON. Pro soubory, přidejte do cesty k souboru s předponu `@`, jako například `@path/to/file.json`.
* **`id` (volitelné)** : ID instance Orchestrace. Pokud tento parametr nezadáte, příkaz používá náhodný GUID.
* **`connection-string-setting` (volitelné)** : Název nastavení aplikace s připojovacím řetězcem úložiště, které můžete používat. Výchozí hodnota je AzureWebJobsStorage.
* **`task-hub-name` (volitelné)** : Název centra úloh Durable Functions používat. Výchozí hodnota je DurableFunctionsHub. Je to také nastavit v [host.json](durable-functions-bindings.md#host-json) pomocí durableTask:HubName.

> [!NOTE]
> Základní nástroje příkazů se předpokládá, že spouštíte je z kořenového adresáře aplikace function app. Když explicitně zadáte `connection-string-setting` a `task-hub-name` parametry, můžete spustit příkazy z libovolného adresáře. Ačkoli můžete spustit tyto příkazy bez aplikace hostitele se funkce, můžete zjistit, že pokud je na hostiteli spuštěn nelze sledovat některé efekty. Například `start-new` příkaz zařadí zpráva spuštění do cílovému rozbočovači úkol, ale orchestraci nespustí ve skutečnosti, pokud je hostitelský proces aplikace funkce systémem, který může zpracovat zprávy.

Následující příkaz spustí funkci s názvem Hello World a předá obsah souboru `counter-data.json` do ní:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Instance dotazu

Jako součást vašeho úsilí ke správě vašich Orchestrace bude pravděpodobně nutné shromažďovat informace o stavu instance orchestration (například určuje, zda se má provést běžným způsobem nebo neúspěšných).

[GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) metodu na [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) tříd (.NET) nebo `getStatus` metodu `DurableOrchestrationClient` stavové Orchestrace dotazuje třídy (JavaScript) instance.

Trvá, než `instanceId` (povinné), `showHistory` (volitelné), `showHistoryOutput` (volitelné), a `showInput` (volitelné, pouze .NET) jako parametry.

* **`showHistory`** : Pokud hodnotu `true`, odpověď obsahuje historii spuštění.
* **`showHistoryOutput`** : Pokud hodnotu `true`, historie spouštění obsahuje výstupů aktivity.
* **`showInput`** : Pokud hodnotu `false`, odpověď nebude obsahovat vstup funkce. Výchozí hodnota je `true`. (Pouze .NET)

Metoda vrátí objekt JSON s následujícími vlastnostmi:

* **Název**: Název funkce orchestrátoru.
* **instanceId**: ID instance orchestraci (by měl být stejný jako `instanceId` vstupu).
* **CreatedTime**: Čas, ve kterém funkce nástroje orchestrator spuštěn.
* **LastUpdatedTime**: Čas, kdy orchestraci poslední kontrolní bod.
* **Vstup**: Vstup funkce jako hodnotu JSON. Toto pole není naplněn, pokud `showInput` má hodnotu false.
* **CustomStatus**: Stav vlastní Orchestrace ve formátu JSON.
* **Výstup**: Výstupní funkci jako hodnotu JSON (je-li funkci dokončí). Pokud selže funkce orchestrátoru, tato vlastnost obsahuje podrobnosti o chybě. Pokud byl ukončen funkce orchestrátoru, tato vlastnost obsahuje důvod ukončení (pokud existuje).
* **RuntimeStatus**: Jeden z následujících hodnot:
  * **Čekající**: Instance je naplánovaná, ale nebyla dosud spuštěna.
  * **Spuštění**: Instance byla spuštěna.
  * **Dokončení**: Instance byla dokončena normálně.
  * **ContinuedAsNew**: Instance samotného restartování s novou historie. Jedná se o přechodný stav.
  * **Nepovedlo**: Instance se nezdařilo s chybou.
  * **Byla ukončena**: Instance byla náhle zastavena.
* **Historie**: Historie provádění orchestraci. Toto pole je vyplněný, pouze když `showHistory` je nastavena na `true`.

Tato metoda vrátí `null` Pokud instance neexistuje nebo nebyl dosud spuštěn.

### <a name="c"></a>C#

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    var status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (funguje pouze 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

### <a name="azure-functions-core-tools"></a>Nástroje Azure Functions Core

Je také možné načíst stav instance Orchestrace přímo, pomocí [nástrojů Azure Functions Core](../functions-run-local.md) `durable get-runtime-status` příkazu. Ji používá následující parametry:

* **`id` (povinné)** : ID instance Orchestrace.
* **`show-input` (volitelné)** : Pokud hodnotu `true`, odpověď obsahuje vstup funkce. Výchozí hodnota je `false`.
* **`show-output` (volitelné)** : Pokud hodnotu `true`, odpověď obsahuje výstup funkce. Výchozí hodnota je `false`.
* **`connection-string-setting` (volitelné)** : Název nastavení aplikace s připojovacím řetězcem úložiště, které můžete používat. Výchozí formát je `AzureWebJobsStorage`.
* **`task-hub-name` (volitelné)** : Název centra úloh Durable Functions používat. Výchozí formát je `DurableFunctionsHub`. Můžete také nastavit [host.json](durable-functions-bindings.md#host-json), s použitím durableTask:HubName.

Následující příkaz načte stav instance s ID instance Orchestrace 0ab8c55a66644d68a3a8b220b12d209c (včetně vstup a výstup). Předpokládá, že používáte `func` z kořenového adresáře aplikace function app:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

Můžete použít `durable get-history` příkaz, který načte historie Orchestrace instance. Ji používá následující parametry:

* **`id` (povinné)** : ID instance Orchestrace.
* **`connection-string-setting` (volitelné)** : Název nastavení aplikace s připojovacím řetězcem úložiště, které můžete používat. Výchozí formát je `AzureWebJobsStorage`.
* **`task-hub-name` (volitelné)** : Název centra úloh Durable Functions používat. Výchozí formát je `DurableFunctionsHub`. To lze také nastavit v host.json, s použitím durableTask:HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Dotázat na všechny instance

Místo dotazu aspoň jedna instance vaší Orchestrace najednou možná pro vás bude efektivnější dotazu je všechny najednou.

Můžete použít `GetStatusAsync` (.NET) nebo `getStatusAll` – metoda (JavaScript) k dotazování stavy všech instancí Orchestrace. V rozhraní .NET, můžete předat `CancellationToken` objektu v případě, že chcete ho zrušit. Metoda vrátí objekty se stejnými vlastnostmi, jako `GetStatusAsync` metodu s parametry.

### <a name="c"></a>C#

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await client.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (funguje pouze 2.x)

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

### <a name="azure-functions-core-tools"></a>Nástroje Azure Functions Core

Je také možné dotazu instance přímo, pomocí [nástrojů Azure Functions Core](../functions-run-local.md) `durable get-instances` příkazu. Ji používá následující parametry:

* **`top` (volitelné)** : Tento příkaz podporuje stránkování. Tento parametr odpovídá počtu instancí načteno na jednu žádost. Výchozí hodnota je 10.
* **`continuation-token` (volitelné)** : Token chcete určit, který stránka nebo část instance, které chcete načíst. Každý `get-instances` spuštění se vrátí token k další sadu instancí.
* **`connection-string-setting` (volitelné)** : Název nastavení aplikace s připojovacím řetězcem úložiště, které můžete používat. Výchozí formát je `AzureWebJobsStorage`.
* **`task-hub-name` (volitelné)** : Název centra úloh Durable Functions používat. Výchozí formát je `DurableFunctionsHub`. Můžete také nastavit [host.json](durable-functions-bindings.md#host-json), s použitím durableTask:HubName.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Instance dotazu s filtry

Co dělat, pokud nepotřebujete všechny informace, které můžete zadat dotaz standardní instance? Například co když jenom hledáte čas vytvoření Orchestrace nebo stav runtime Orchestrace? Dotaz můžete zúžit použitím filtry.

Použití `GetStatusAsync` (.NET) nebo `getStatusBy` – metoda (JavaScript) Chcete-li získat seznam instancí Orchestrace, které splňují sadu předdefinovaných filtrů.

### <a name="c"></a>C#

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IEnumerable<OrchestrationRuntimeStatus> runtimeStatus = new List<OrchestrationRuntimeStatus> {
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

### <a name="javascript-functions-2x-only"></a>JavaScript (funguje pouze 2.x)

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

### <a name="azure-functions-core-tools"></a>Nástroje Azure Functions Core

V Azure Functions Core Tools, můžete také použít `durable get-instances` příkaz s filtry. Kromě výše uvedenému `top`, `continuation-token`, `connection-string-setting`, a `task-hub-name` parametry, můžete použít tři parametry filtru (`created-after`, `created-before`, a `runtime-status`).

* **`created-after` (volitelné)** : Načtení instancí vytvořené po tomto datum a čas (UTC). ISO 8601 ve formátu data a času přijmout.
* **`created-before` (volitelné)** : Načtení instancí vytvořené před toto datum a čas (UTC). ISO 8601 ve formátu data a času přijmout.
* **`runtime-status` (volitelné)** : Načtení instancí s konkrétním stavem (například spuštěna nebo k dokončení). Můžete zadat více stavů (oddělené mezerou).
* **`top` (volitelné)** : Počet instancí načteno na jednu žádost. Výchozí hodnota je 10.
* **`continuation-token` (volitelné)** : Token chcete určit, který stránka nebo část instance, které chcete načíst. Každý `get-instances` spuštění se vrátí token k další sadu instancí.
* **`connection-string-setting` (volitelné)** : Název nastavení aplikace s připojovacím řetězcem úložiště, které můžete používat. Výchozí formát je `AzureWebJobsStorage`.
* **`task-hub-name` (volitelné)** : Název centra úloh Durable Functions používat. Výchozí formát je `DurableFunctionsHub`. Můžete také nastavit [host.json](durable-functions-bindings.md#host-json), s použitím durableTask:HubName.

Pokud nezadáte žádné filtry (`created-after`, `created-before`, nebo `runtime-status`), příkaz jednoduše načte `top` instancí, bez ohledu na čas vytvoření nebo stav modulu runtime.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Ukončit instance

Pokud máte instanci Orchestrace, která trvá příliš dlouho, nebo stačí ji zastavit před dokončením z jakéhokoli důvodu, máte možnost ho ukončit.

Můžete použít [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) metodu [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) tříd (.NET), nebo `terminate` metodu `DurableOrchestrationClient` třídy (JavaScript). Jsou dva parametry `instanceId` a `reason` řetězec, který se zapisují do protokolů a stav instance. Zastavené instance se zastaví ihned poté, co dosáhne Další `await` (.NET) nebo `yield` bod (JavaScript) nebo ji okamžitě ukončí pokud je již `await` nebo `yield`.

### <a name="c"></a>C#

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (funguje pouze 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

> [!NOTE]
> Ukončení instance není momentálně rozšíří. Funkce aktivity a dílčí Orchestrace poběží do konce, bez ohledu na to, zda jste ukončena Orchestrace instanci, která je volána.

### <a name="azure-functions-core-tools"></a>Nástroje Azure Functions Core

Můžete také ukončit Orchestrace instance přímo, pomocí [nástrojů Azure Functions Core](../functions-run-local.md) `durable terminate` příkazu. Ji používá následující parametry:

* **`id` (povinné)** : ID instance Orchestrace ukončit.
* **`reason` (volitelné)** : Důvod ukončení.
* **`connection-string-setting` (volitelné)** : Název nastavení aplikace s připojovacím řetězcem úložiště, které můžete používat. Výchozí formát je `AzureWebJobsStorage`.
* **`task-hub-name` (volitelné)** : Název centra úloh Durable Functions používat. Výchozí formát je `DurableFunctionsHub`. Můžete také nastavit [host.json](durable-functions-bindings.md#host-json), s použitím durableTask:HubName.

Následující příkaz ukončí Orchestrace instance s ID 0ab8c55a66644d68a3a8b220b12d209c:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Odesílání událostí do instance

V některých případech je důležité pro vaše funkce produktu orchestrator lze počkat a naslouchat událostem externí. Jedná se o [monitorování funkce](durable-functions-concepts.md#monitoring) a funkce, které čekají na [zásahem ze strany](durable-functions-concepts.md#human).

Odeslat oznámení události spuštěné instance pomocí [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) metodu [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) tříd (.NET) nebo `raiseEvent` metodu `DurableOrchestrationClient` třídy () Jazyk JavaScript). Instance, které dokáže zpracovat tyto události jsou ty, které čekají na volání [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) (.NET) nebo `waitForExternalEvent` (JavaScript).

Parametry tak, aby [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) (.NET) a `raiseEvent` (JavaScript) jsou následující:

* **instanceId**: Jedinečné ID instance.
* **EventName**: Název události k odeslání.
* **EventData**: JSON serializovat datovou část k odeslání do instance.

### <a name="c"></a>C#

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (funguje pouze 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

> [!IMPORTANT]
> Pokud neexistuje žádná instance Orchestrace s ID zadanou instanci nebo instance nečeká na názvu zadanou událost, zpráva o události se zahodí. Další informace o tomto chování najdete v tématu [problém Githubu](https://github.com/Azure/azure-functions-durable-extension/issues/29).

### <a name="azure-functions-core-tools"></a>Nástroje Azure Functions Core

Je také možné vyvolat událost, která instance Orchestrace přímo, pomocí [nástrojů Azure Functions Core](../functions-run-local.md) `durable raise-event` příkazu. Ji používá následující parametry:

* **`id` (povinné)** : ID instance Orchestrace.
* **`event-name` (volitelné)** : Název události k vyvolání. Výchozí formát je `$"Event_{RandomGUID}"`.
* **`event-data` (volitelné)** : Data k odeslání do instance Orchestrace. To může být cesta k souboru JSON, nebo můžete zadat data přímo na příkazovém řádku.
* **`connection-string-setting` (volitelné)** : Název nastavení aplikace s připojovacím řetězcem úložiště, které můžete používat. Výchozí formát je `AzureWebJobsStorage`.
* **`task-hub-name` (volitelné)** : Název centra úloh Durable Functions používat. Výchozí formát je `DurableFunctionsHub`. Můžete také nastavit [host.json](durable-functions-bindings.md#host-json), s použitím durableTask:HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Počkat na dokončení Orchestrace

V orchestracích dlouhotrvající můžete počkat a načte výsledky Orchestrace. V těchto případech je také užitečné mít možnost definovat časový limit pro orchestraci. Pokud je překročen časový limit, má být vrácen stav orchestraci místo výsledků.

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) třídy zpřístupňuje [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) rozhraní API v .NET. Toto rozhraní API můžete použít k získání aktuální výstup z instance Orchestrace synchronně. V jazyce JavaScript `DurableOrchestrationClient` třídy zpřístupňuje `waitForCompletionOrCreateCheckStatusResponse` rozhraní API k tomuto účelu. Pokud není nastavena, použijte metody výchozí hodnota je 10 sekund, `timeout`a 1 sekundu `retryInterval`.  

Tady je příklad funkce triggeru HTTP, který ukazuje, jak pomocí tohoto rozhraní API:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

Volání funkce tento řádek. Použijte 2 sekund časového limitu a 0,5 sekund pro interval opakování:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

V závislosti na čas potřebný k získání odpovědi z instance Orchestrace existují dva možné případy:

* Instance Orchestrace dokončit během definovaný časový limit (v tomto případě 2 sekundy), a odpověď je výstup instance skutečné Orchestrace, doručí synchronně:

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:14:29 GMT
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

* Instance Orchestrace nelze dokončit v rámci definovaný časový limit a odpověď je výchozí, jeden je popsáno v [zjišťování adresy URL rozhraní API HTTP](durable-functions-http-api.md):

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:13:51 GMT
        Location: http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}",
            "rewindPostUri": "https://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/rewind?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> Formát adresy URL webhooku se mohou lišit v závislosti na tom, které verze hostitelů Azure Functions, kterou používáte. V předchozím příkladu je pro hostitele 2.x Azure Functions.

## <a name="retrieve-http-management-webhook-urls"></a>Získání adresy URL webhooku správy protokolu HTTP

Externí systém můžete použít k monitorování nebo vyvolat události na Orchestrace. Externí systémy mohou komunikovat s Durable Functions prostřednictvím adresy URL webhooku, které jsou součástí výchozí odpověď je popsáno v [zjišťování adresy URL rozhraní API HTTP](durable-functions-http-api.md). Ale adresy URL webhooku také je možné programově přistupovat v klientovi Orchestrace nebo ve funkci aktivity. To provést pomocí [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) metodu [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) tříd (.NET), nebo `createHttpManagementPayload` metodu `DurableOrchestrationClient` třídy (JavaScript).

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) a `createHttpManagementPayload` mít jeden parametr:

* **instanceId**: Jedinečné ID instance.

Metody vracet instanci [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) (.NET) nebo objekt (JavaScript), s následujícími vlastnostmi řetězec:

* **ID**: ID instance orchestraci (by měl být stejný jako `InstanceId` vstupu).
* **StatusQueryGetUri**: Adresa URL stavu instance Orchestrace.
* **SendEventPostUri**: "Vyvolat událost" adresa URL instance Orchestrace.
* **TerminatePostUri**: "Ukončit" adresa URL instance Orchestrace.
* **RewindPostUri**: "Zpět" adresa URL instance Orchestrace.

Aktivita funkce můžete odeslat instanci tyto objekty k externím systémům ke sledování nebo vyvolat události na Orchestrace:

### <a name="c"></a>C#

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] DurableActivityContext ctx,
    [OrchestrationClient] DurableOrchestrationClient client,
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

### <a name="javascript-functions-2x-only"></a>JavaScript (funguje pouze 2.x)

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

## <a name="rewind-instances-preview"></a>REWIND instance (preview)

Pokud máte k orchestraci selhání z důvodu neočekávané, můžete *rewind* instance dříve dobrý stav pomocí rozhraní API vytvořené pro tento účel.

> [!NOTE]
> Toto rozhraní API není určen jako náhrada za zpracování správné chyb a zásady opakování. Místo toho je určena pro použití pouze v případech, kde instance Orchestrace selhat z důvodu neočekávané. Další podrobnosti o chybě zásady zpracování a zkuste to znovu, najdete v článku [zpracování chyb](durable-functions-error-handling.md) tématu.

Použití [RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_) (.NET) nebo `rewindAsync` (JavaScript) rozhraní API pro umístění orchestraci zpět do *systémem* stavu. Znovu spustí aktivity nebo suborchestration selhání spuštění, které způsobily selhání Orchestrace.

Například Řekněme, že máte pracovní postup zahrnující řadu [lidské schválení](durable-functions-concepts.md#human). Předpokládejme, že je potřeba zvážit řadu funkcí aktivity, které upozornit uživatele, který jejich schválení je potřeba a čekat na odpověď v reálném čase. Po všech aktivit schválení obdrželi odpovědi nebo vypršení časového limitu, Předpokládejme, že další aktivita selže z důvodu chybné konfigurace aplikace, jako je například připojovací řetězec služby databáze je neplatná. Výsledkem je selhání Orchestrace hlouběji do pracovního postupu. S `RewindAsync` (.NET) nebo `rewindAsync` (JavaScript) rozhraní API, aplikace Správce konfigurace chybu opravit a rewind neúspěšné Orchestrace zpět do stavu bezprostředně před selháním. Žádný z kroků lidské interakce musí být znovu schválit a orchestraci teď můžete dokončit úspěšně.

> [!NOTE]
> *Rewind* funkce nepodporuje převíjecí Orchestrace instancí, které používají trvalý časovače.

### <a name="c"></a>C#

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (funguje pouze 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

### <a name="azure-functions-core-tools"></a>Nástroje Azure Functions Core

Můžete také rewind instanci Orchestrace přímo pomocí [nástrojů Azure Functions Core](../functions-run-local.md) `durable rewind` příkazu. Ji používá následující parametry:

* **`id` (povinné)** : ID instance Orchestrace.
* **`reason` (volitelné)** : Důvod převíjení Orchestrace instance.
* **`connection-string-setting` (volitelné)** : Název nastavení aplikace s připojovacím řetězcem úložiště, které můžete používat. Výchozí formát je `AzureWebJobsStorage`.
* **`task-hub-name` (volitelné)** : Název centra úloh Durable Functions používat. Výchozí formát je `DurableFunctionsHub`. Můžete také nastavit [host.json](durable-functions-bindings.md#host-json), s použitím durableTask:HubName.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Vyprázdnit historii instance

Chcete-li odebrat všechna data přidružená k Orchestrace, mohou vyprázdnit historii instance. Například můžete chtít odstranit řádky tabulky Azure a objekty BLOB velkých zpráv, pokud existují. Chcete-li tak učinit, použijte [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_) rozhraní API.

> [!NOTE]
> `PurgeInstanceHistoryAsync` Rozhraní API je aktuálně k dispozici pouze pro C#.

 Tato metoda má dvě přetížení. První z nich vymaže historii podle ID instance Orchestrace:

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

Druhý příklad ukazuje funkce aktivované pomocí časovače, který vymaže historii pro všechny instance Orchestrace, které dokončena po zadaný časový interval. V takovém případě odebere data pro všechny instance dokončit před 30 nebo více dny. Je naplánováno spuštění jednou za den v 12: 00:

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
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
> Proces funkce aktivované čase úspěšné, musí být stav runtime **dokončeno**, **ukončeno**, nebo **neúspěšné**.

### <a name="azure-functions-core-tools"></a>Nástroje Azure Functions Core

Mohou vyprázdnit historii instanci Orchestrace pomocí [nástrojů Azure Functions Core](../functions-run-local.md) `durable purge-history` příkazu. Podobně jako druhý C# příklad v předchozím oddílu, vymaže historii pro všechny instance Orchestrace vytvořené během zadaného časového intervalu. Odstraněný instance můžete dále filtrovat podle stavu modulu runtime. Příkaz má několik parametrů:

* **`created-after` (volitelné)** : Vyprázdnit historii instance vytvořené po tomto datum a čas (UTC). ISO 8601 ve formátu data a času přijmout.
* **`created-before` (volitelné)** : Vyprázdnit historii instance vytvořené před toto datum a čas (UTC). ISO 8601 ve formátu data a času přijmout.
* **`runtime-status` (volitelné)** : Vymazat historii instancí s konkrétním stavem (například spuštěna nebo k dokončení). Můžete zadat více stavů (oddělené mezerou).
* **`connection-string-setting` (volitelné)** : Název nastavení aplikace s připojovacím řetězcem úložiště, které můžete používat. Výchozí formát je `AzureWebJobsStorage`.
* **`task-hub-name` (volitelné)** : Název centra úloh Durable Functions používat. Výchozí formát je `DurableFunctionsHub`. Můžete také nastavit [host.json](durable-functions-bindings.md#host-json), s použitím durableTask:HubName.

Následující příkaz odstraní historii všech neúspěšných instance vytvořené před 14. listopadu 2018 v 7:35 PM (UTC).

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Odstranit Centrum úkolů

Použití [nástrojů Azure Functions Core](../functions-run-local.md) `durable delete-task-hub` příkazu, můžete odstranit všechny artefakty úložiště spojené s rozbočovači určité úlohy. To zahrnuje tabulky v úložišti Azure, fronty a objekty BLOB. Příkaz má dva parametry:

* **`connection-string-setting` (volitelné)** : Název nastavení aplikace s připojovacím řetězcem úložiště, které můžete používat. Výchozí formát je `AzureWebJobsStorage`.
* **`task-hub-name` (volitelné)** : Název centra úloh Durable Functions používat. Výchozí formát je `DurableFunctionsHub`. Můžete také nastavit [host.json](durable-functions-bindings.md#host-json), s použitím durableTask:HubName.

Následující příkaz odstraní všechna data služby Azure storage související s `UserTest` centra úloh.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o použití protokolu HTTP rozhraní API pro správu](durable-functions-http-api.md)
