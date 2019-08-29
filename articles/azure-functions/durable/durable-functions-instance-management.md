---
title: Správa instancí v Durable Functions – Azure
description: Naučte se spravovat instance v rozšíření Durable Functions pro Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 6548b84f9599116aaa5055324bfa4625ea621ec3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087248"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Správa instancí v Durable Functions v Azure

Pokud používáte rozšíření [Durable Functions](durable-functions-overview.md) pro Azure Functions nebo chcete začít, ujistěte se, že jste si vyžádali, abyste se dostali co nejvíce využívali. Můžete optimalizovat své Durable Functions instance orchestrace tím, že se dozvíte víc o tom, jak je spravovat. Tento článek odkazuje na podrobnosti o každé operaci správy instancí.

Můžete začít a ukončovat instance, například, a můžete zadávat dotazy na instance, včetně možnosti dotazování všech instancí a instancí dotazů pomocí filtrů. Kromě toho můžete odesílat události do instancí, počkat na dokončení Orchestrace a načíst adresy URL Webhooku pro správu protokolu HTTP. Tento článek se zabývá i dalšími operacemi správy, včetně převíjení instancí, vymazáním Historie instancí a odstraněním centra úloh.

V Durable Functions máte možnosti, jak chcete implementovat jednotlivé operace správy. Tento článek popisuje příklady, které používají [Azure Functions Core Tools](../functions-run-local.md) pro .NET (C#) i JavaScript.

## <a name="start-instances"></a>Počáteční instance

Je důležité, abyste mohli spustit instanci orchestrace. To se obvykle provádí při použití vazby Durable Functions v triggeru jiné funkce.

Metoda [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) na [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) `startNew` `DurableOrchestrationClient` nebo na (JavaScript) spouští novou instanci. Instance této třídy získáte pomocí `orchestrationClient` vazby. Interně Tato metoda zařadí zprávu do fronty ovládacích prvků, která pak aktivuje začátek funkce se zadaným názvem, který používá `orchestrationTrigger` aktivační vazbu.

Tato asynchronní operace se dokončí při úspěšném naplánování procesu orchestrace. Proces orchestrace by měl začínat do 30 sekund. Pokud bude trvat déle, zobrazí se `TimeoutException`.

> [!WARNING]
> Při vývoji místně v `WEBSITE_HOSTNAME` jazyce JavaScript nastavte proměnnou `localhost:<port>` prostředí (například `localhost:7071`) na použití metod v `DurableOrchestrationClient`. Další informace o tomto požadavku najdete v tématu [problém GitHubu](https://github.com/Azure/azure-functions-durable-js/issues/28).

### <a name="net"></a>.NET

Parametry [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) jsou následující:

* **Název**: Název funkce nástroje Orchestrator, která má být naplánována.
* **Vstup**: Všechna data serializovatelných JSON, která by měla být předána jako vstup do funkce Orchestrator.
* **InstanceId**: Volitelné Jedinečné ID instance Pokud tento parametr nezadáte, použije metoda náhodné ID.

Tady je jednoduchý C# příklad:

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

### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

Parametry `startNew` jsou následující:

* **Název**: Název funkce nástroje Orchestrator, která má být naplánována.
* **InstanceId**: Volitelné Jedinečné ID instance Pokud tento parametr nezadáte, použije metoda náhodné ID.
* **Vstup**: Volitelné Všechna data serializovatelných JSON, která by měla být předána jako vstup do funkce Orchestrator.

Tady je jednoduchý příklad JavaScriptu:

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

> [!TIP]
> Pro ID instance použijte náhodný identifikátor. To pomáhá zajistit stejnou distribuci zatížení při škálování funkcí nástroje Orchestrator napříč více virtuálními počítači. Vhodný čas pro použití nenáhodných ID instancí je, když ID musí pocházet z externího zdroje, nebo při implementaci vzoru s [jedním prvkem Orchestrator](durable-functions-singletons.md) .

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Instanci můžete také spustit přímo pomocí příkazu [Azure Functions Core Tools](../functions-run-local.md) `durable start-new` . Má následující parametry:

* (povinné): **`function-name`** Název funkce, která se má spustit.
* (volitelné): **`input`** Vstup do funkce, buď vloženou, nebo pomocí souboru JSON. Pro soubory přidejte předponu k cestě k souboru `@`, `@path/to/file.json`například.
* (volitelné): **`id`** ID instance orchestrace Pokud tento parametr nezadáte, použije příkaz náhodný identifikátor GUID.
* (volitelné): **`connection-string-setting`** Název nastavení aplikace, které obsahuje připojovací řetězec úložiště, který se má použít Výchozí hodnota je AzureWebJobsStorage.
* (volitelné): **`task-hub-name`** Název Durable Functions centra úloh, které se má použít Výchozí hodnota je DurableFunctionsHub. Tuto hodnotu můžete nastavit také v souboru [Host. JSON](durable-functions-bindings.md#host-json) pomocí DurableTask: HubName.

> [!NOTE]
> Základní příkazy nástrojů předpokládají, že je spouštíte z kořenového adresáře aplikace Function App. Pokud explicitně zadáte `connection-string-setting` parametry a `task-hub-name` , můžete spustit příkazy z libovolného adresáře. I když můžete spustit tyto příkazy bez hostitele aplikace Function App, může se stát, že nebudete mít pozor na některé efekty, pokud hostitel neběží. `start-new` Příkaz například zařazování počáteční zprávy do cílového centra úloh, ale orchestrace se ve skutečnosti nespustí, pokud není spuštěn hostitelský proces aplikace Function App, který může zprávu zpracovat.

Následující příkaz spustí funkci s názvem HelloWorld a předá obsah souboru `counter-data.json` :

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Instance dotazů

V rámci snahy o správu orchestrací budete pravděpodobně potřebovat shromáždit informace o stavu instance orchestrace (například bez ohledu na to, zda byla dokončena normálně nebo neúspěšná).

Metoda [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) třídy [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET `getStatus` ) nebo metody `DurableOrchestrationClient` třídy (JavaScript) se dotazuje na stav instance Orchestration.

Provede `instanceId` (povinné `showHistory` ) (volitelné `showHistoryOutput` ), (volitelné), (volitelné) a `showInput` (volitelné, pouze .NET) jako parametry.

* **`showHistory`** : Pokud je nastaveno `true`na, odpověď obsahuje historii spuštění.
* **`showHistoryOutput`** : Pokud je nastaveno `true`na, historie spouštění obsahuje výstupy aktivit.
* **`showInput`** : Pokud je nastaveno `false`na, odpověď nebude obsahovat vstup funkce. Výchozí hodnota je `true`. (Jenom .NET)

Metoda vrátí objekt JSON s následujícími vlastnostmi:

* **Název**: Název funkce nástroje Orchestrator.
* **InstanceId**: ID instance orchestrace (měla by být stejná jako u `instanceId` vstupu).
* **CreatedTime**: Čas spuštění funkce Orchestrator.
* **LastUpdatedTime**: Čas posledního kontrolního bodu orchestrace.
* **Vstup**: Vstup funkce jako hodnota JSON. Toto pole není naplněné, pokud `showInput` má hodnotu false.
* **CustomStatus**: Stav vlastního orchestrace ve formátu JSON.
* **Výstup**: Výstup funkce jako hodnota JSON (Pokud byla funkce dokončena). Pokud se funkce Orchestrator nezdařila, obsahuje tato vlastnost podrobnosti o selhání. Pokud byla funkce Orchestrator ukončena, obsahuje tato vlastnost důvod ukončení (pokud existuje).
* **RuntimeStatus**: Jedna z následujících hodnot:
  * **Čeká na vyřízení**: Instance byla naplánována, ale ještě nebyla spuštěna.
  * **Spuštěno**: Instance začala běžet.
  * **Dokončeno**: Instance se normálně dokončila.
  * **ContinuedAsNew**: Instance se sama restartovala s novou historií. Toto je přechodný stav.
  * **Selhalo**: Instance se nezdařila s chybou.
  * **Ukončeno**: Instance se náhle zastavila.
* **Historie**: Historie provádění orchestrace. Toto pole je vyplněno pouze v `showHistory` případě, že `true`je nastavena na hodnotu.

Tato metoda vrátí `null` , zda instance neexistuje nebo dosud nebyla spuštěna.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Stav instance orchestrace je také možné získat přímo pomocí příkazu [Azure Functions Core Tools](../functions-run-local.md) `durable get-runtime-status` . Má následující parametry:

* (povinné): **`id`** ID instance orchestrace
* (volitelné): **`show-input`** Pokud je nastaveno `true`na, odpověď obsahuje vstup funkce. Výchozí hodnota je `false`.
* (volitelné): **`show-output`** Pokud je nastaveno `true`na, odpověď obsahuje výstup funkce. Výchozí hodnota je `false`.
* (volitelné): **`connection-string-setting`** Název nastavení aplikace, které obsahuje připojovací řetězec úložiště, který se má použít Výchozí hodnota je `AzureWebJobsStorage`.
* (volitelné): **`task-hub-name`** Název Durable Functions centra úloh, které se má použít Výchozí hodnota je `DurableFunctionsHub`. Dá se taky nastavit v souboru [Host. JSON](durable-functions-bindings.md#host-json)pomocí DurableTask: HubName.

Následující příkaz načte stav (včetně vstupu a výstupu) instance s ID instance orchestrace 0ab8c55a66644d68a3a8b220b12d209c. Předpokládá, že spouštíte `func` příkaz z kořenového adresáře aplikace Function App:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

Pomocí `durable get-history` příkazu můžete načíst historii instance Orchestration. Má následující parametry:

* (povinné): **`id`** ID instance orchestrace
* (volitelné): **`connection-string-setting`** Název nastavení aplikace, které obsahuje připojovací řetězec úložiště, který se má použít Výchozí hodnota je `AzureWebJobsStorage`.
* (volitelné): **`task-hub-name`** Název Durable Functions centra úloh, které se má použít Výchozí hodnota je `DurableFunctionsHub`. Dá se taky nastavit v souboru Host. JSON pomocí durableTask: HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Dotazování všech instancí

Spíše než dotazování jedné instance orchestrace v čase, může být efektivnější dotazování na ně.

Můžete použít `GetStatusAsync` metodu (.NET) nebo `getStatusAll` (JavaScript) pro dotazování stavů všech instancí orchestrace. V rozhraní .NET můžete předat `CancellationToken` objekt pro případ, že ho chcete zrušit. Metoda vrátí objekty se stejnými vlastnostmi jako `GetStatusAsync` metoda s parametry.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

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

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Je také možné zadat dotaz přímo na instance pomocí příkazu [Azure Functions Core Tools](../functions-run-local.md) `durable get-instances` . Má následující parametry:

* (volitelné): **`top`** Tento příkaz podporuje stránkování. Tento parametr odpovídá počtu instancí načtených na požadavek. Výchozí hodnota je 10.
* (volitelné): **`continuation-token`** Token, který označuje, kterou stránku nebo oddíl instancí načíst. Každé `get-instances` spuštění vrátí token k další sadě instancí.
* (volitelné): **`connection-string-setting`** Název nastavení aplikace, které obsahuje připojovací řetězec úložiště, který se má použít Výchozí hodnota je `AzureWebJobsStorage`.
* (volitelné): **`task-hub-name`** Název Durable Functions centra úloh, které se má použít Výchozí hodnota je `DurableFunctionsHub`. Dá se taky nastavit v souboru [Host. JSON](durable-functions-bindings.md#host-json)pomocí DurableTask: HubName.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Dotazování instancí s filtry

Co když opravdu nepotřebujete všechny informace, které může dotaz standardní instance poskytnout? Například co když hledáte jenom čas vytvoření orchestrace, nebo běhový stav orchestrace? Dotaz můžete zúžit použitím filtrů.

`getStatusBy` Použijte metodu `GetStatusAsync` (.NET) nebo (JavaScript) k získání seznamu instancí orchestrace, které odpovídají sadě předdefinovaných filtrů.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

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

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

V Azure Functions Core Tools můžete použít `durable get-instances` také příkaz s filtry. Kromě `top`výše uvedených `task-hub-name` `created-after` `runtime-status` `created-before`parametrů,, a můžete použít tři parametry filtru (,, a). `continuation-token` `connection-string-setting`

* (volitelné): **`created-after`** Načte instance vytvořené po tomto datu a čase (UTC). Byly přijaty hodnoty DateTime ve formátu ISO 8601.
* (volitelné): **`created-before`** Načte instance vytvořené před tímto datem a časem (UTC). Byly přijaty hodnoty DateTime ve formátu ISO 8601.
* (volitelné): **`runtime-status`** Načte instance s určitým stavem (například spuštěno nebo dokončeno). Může poskytovat více stavů (oddělené místo).
* (volitelné): **`top`** Počet načtených instancí na žádost. Výchozí hodnota je 10.
* (volitelné): **`continuation-token`** Token, který označuje, kterou stránku nebo oddíl instancí načíst. Každé `get-instances` spuštění vrátí token k další sadě instancí.
* (volitelné): **`connection-string-setting`** Název nastavení aplikace, které obsahuje připojovací řetězec úložiště, který se má použít Výchozí hodnota je `AzureWebJobsStorage`.
* (volitelné): **`task-hub-name`** Název Durable Functions centra úloh, které se má použít Výchozí hodnota je `DurableFunctionsHub`. Dá se taky nastavit v souboru [Host. JSON](durable-functions-bindings.md#host-json)pomocí DurableTask: HubName.

`created-after`Pokud nezadáte žádné filtry (, `created-before`nebo `runtime-status`), příkaz jednoduše načte `top` instance bez ohledu na stav modulu runtime nebo čas vytvoření.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Ukončit instance

Máte-li instanci orchestrace, která trvá příliš dlouho, nebo ji pouze potřebujete zastavit, než se z nějakého důvodu dokončí, máte možnost ji ukončit.

Můžete použít metodu [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) třídy [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) nebo `terminate` metodu `DurableOrchestrationClient` třídy (JavaScript). Dva parametry jsou `instanceId` `reason` a řetězec, který se zapisuje do protokolů a do stavu instance. Ukončená instance se zastaví, jakmile dosáhne `await` následujícího (.NET) nebo `yield` (JavaScript) bodu, nebo se okamžitě ukončí, pokud už je na `await` nebo `yield`.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

> [!NOTE]
> Ukončení instance v současné době nešíří. Funkce aktivity a dílčí orchestrace jsou spouštěny k dokončení bez ohledu na to, zda jste ukončili instanci orchestrace, která je jim volána.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Instanci orchestrace můžete také ukončit přímo pomocí příkazu [Azure Functions Core Tools](../functions-run-local.md) `durable terminate` . Má následující parametry:

* (povinné): **`id`** ID instance orchestrace, která se má ukončit
* (volitelné): **`reason`** Důvod ukončení.
* (volitelné): **`connection-string-setting`** Název nastavení aplikace, které obsahuje připojovací řetězec úložiště, který se má použít Výchozí hodnota je `AzureWebJobsStorage`.
* (volitelné): **`task-hub-name`** Název Durable Functions centra úloh, které se má použít Výchozí hodnota je `DurableFunctionsHub`. Dá se taky nastavit v souboru [Host. JSON](durable-functions-bindings.md#host-json)pomocí DurableTask: HubName.

Následující příkaz ukončí instanci orchestrace s ID 0ab8c55a66644d68a3a8b220b12d209c:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Odesílání událostí do instancí

V některých scénářích je důležité, aby funkce nástroje Orchestrator dokázala počkat a naslouchat externím událostem. To zahrnuje [funkce](durable-functions-concepts.md#monitoring) a funkce monitorování, které čekají na [lidskou interakci](durable-functions-concepts.md#human).

Odesílání oznámení o událostech na spuštěné instance pomocí metody [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) třídy [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET `raiseEvent` ) nebo metody `DurableOrchestrationClient` třídy (JavaScript). Instance, které mohou zpracovávat tyto události, jsou ty, které čekají na volání [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) (.NET) nebo `waitForExternalEvent` (JavaScript).

Parametry pro [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) (.NET) a `raiseEvent` (JavaScript) jsou následující:

* **InstanceId**: Jedinečné ID instance
* **EventName**: Název události, která má být odeslána.
* **EventData**: Datová část serializace JSON pro odeslání do instance.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

> [!IMPORTANT]
> Pokud není k dispozici žádná instance orchestrace se zadaným ID instance nebo pokud instance nečeká na zadaný název události, zpráva události bude zahozena. Další informace o tomto chování najdete v tématu [problém GitHubu](https://github.com/Azure/azure-functions-durable-extension/issues/29).

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Událost můžete také vyvolat přímo do instance orchestrace pomocí příkazu [Azure Functions Core Tools](../functions-run-local.md) `durable raise-event` . Má následující parametry:

* (povinné): **`id`** ID instance orchestrace
* (volitelné): **`event-name`** Název události, která má být vyvolána. Výchozí hodnota je `$"Event_{RandomGUID}"`.
* (volitelné): **`event-data`** Data, která se mají odeslat do instance Orchestration Může to být cesta k souboru JSON nebo data můžete zadat přímo na příkazovém řádku.
* (volitelné): **`connection-string-setting`** Název nastavení aplikace, které obsahuje připojovací řetězec úložiště, který se má použít Výchozí hodnota je `AzureWebJobsStorage`.
* (volitelné): **`task-hub-name`** Název Durable Functions centra úloh, které se má použít Výchozí hodnota je `DurableFunctionsHub`. Dá se taky nastavit v souboru [Host. JSON](durable-functions-bindings.md#host-json)pomocí DurableTask: HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Počkat na dokončení orchestrace

V dlouhotrvající orchestraci můžete chtít počkat a získat výsledky orchestrace. V těchto případech je také užitečné, abyste mohli definovat dobu časového limitu pro orchestraci. Pokud dojde k překročení časového limitu, měl by se místo výsledků vracet stav orchestrace.

Třída [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) zpřístupňuje rozhraní [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) API v rozhraní .NET. Pomocí tohoto rozhraní API můžete získat skutečný výstup z instance orchestrace synchronně. V jazyce JavaScript `DurableOrchestrationClient` třída `waitForCompletionOrCreateCheckStatusResponse` zpřístupňuje rozhraní API pro stejný účel. Pokud nejsou nastaveny, metody použijí výchozí hodnotu 10 sekund pro `timeout`, a 1 sekundu pro. `retryInterval`  

Tady je příklad funkce triggeru HTTP, která ukazuje, jak používat toto rozhraní API:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

Zavolejte funkci s následujícím řádkem. Pro interval opakování použijte 2 sekundy na časový limit a 0,5 sekund:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

V závislosti na době potřebné k získání odpovědi z instance orchestrace existují dva případy:

* Instance Orchestration se dokončí v rámci definovaného časového limitu (v tomto případě 2 sekundy) a odpověď je skutečný výstup instance orchestrace, který se doručí synchronně:

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

* Instance orchestrace se nemůžou dokončit v rámci definovaného časového limitu a odpověď je výchozí hodnotou, která je popsaná v tématu [zjišťování adresy URL protokolu HTTP API](durable-functions-http-api.md):

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
> Formát adres URL Webhooku se může lišit v závislosti na tom, kterou verzi Azure Functions hostitele používáte. Předchozí příklad je pro hostitele Azure Functions 2. x.

## <a name="retrieve-http-management-webhook-urls"></a>Načíst adresy URL Webhooku pro správu HTTP

K monitorování nebo vyvolání událostí pro orchestraci můžete použít externí systém. Externí systémy mohou komunikovat s Durable Functions prostřednictvím adres URL Webhooku, které jsou součástí výchozí odpovědi popsané v tématu [zjišťování adresy URL protokolu HTTP API](durable-functions-http-api.md). Adresy URL Webhooku se ale taky dají přistupovat programově v klientovi Orchestration nebo ve funkci aktivity. Použijte k tomu metodu [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) třídy [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) nebo `createHttpManagementPayload` metodu `DurableOrchestrationClient` třídy (JavaScript).

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) a `createHttpManagementPayload` mít jeden parametr:

* **instanceId**: Jedinečné ID instance

Metody vracejí instanci třídy [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) (.NET) nebo objektu (JavaScript) s následujícími vlastnostmi řetězce:

* **ID**: ID instance orchestrace (měla by být stejná jako u `InstanceId` vstupu).
* **StatusQueryGetUri**: Adresa URL stavu instance orchestrace.
* **SendEventPostUri**: Adresa URL události vyvolání instance orchestrace.
* **TerminatePostUri**: Adresa URL pro ukončení instance orchestrace.
* **RewindPostUri**: Adresa URL "Rewind" instance orchestrace.

Funkce aktivity mohou odesílat instance těchto objektů do externích systémů za účelem monitorování nebo vyvolání událostí pro orchestraci:

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

### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

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

## <a name="rewind-instances-preview"></a>Instance převinutí (Preview)

Pokud dojde k selhání orchestrace z neočekávaného důvodu, můžete instanci *Převinout* do dříve funkčního stavu pomocí rozhraní API sestaveného pro tento účel.

> [!NOTE]
> Toto rozhraní API není určeno jako náhrada za správné zpracování chyb a zásady opakování. Místo toho je určeno pro použití pouze v případě, že instance Orchestration selžou z neočekávaných důvodů. Další informace o zpracování chyb a zásadách opakování najdete v tématu o [zpracování chyb](durable-functions-error-handling.md) .

K umístění orchestrace zpátky do běžícího stavu použijte rozhraní API [RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_) (.NET) nebo `rewindAsync` (JavaScript ). Znovu spusťte činnost nebo selhání provádění suborchestrace, které způsobily selhání orchestrace.

Řekněme například, že máte pracovní postup zahrnující řadu [lidských schválení](durable-functions-concepts.md#human). Předpokládejme, že existuje řada funkcí aktivity, které upozorní uživatele, že je potřeba jejich schválení, a vyčkejte na odpověď v reálném čase. Po přijetí odpovědí nebo vypršení časového limitu u všech aktivit schválení dojde k chybě z důvodu nesprávné konfigurace aplikace, jako je například Neplatný připojovací řetězec databáze. Výsledkem je selhání orchestrace hluboko do pracovního postupu. S rozhraním API `rewindAsync` (.NET)nebo(JavaScript)můžesprávceaplikaceopravitchybukonfiguraceapřevinoutneúspěšnouorchestracizpátkydostavuhnedpředselháním.`RewindAsync` Žádný z kroků interakce mezi lidmi není nutné znovu schválit a orchestraci je teď možné úspěšně dokončit.

> [!NOTE]
> Funkce *Rewind* nepodporuje převíjení instancí orchestrace, které používají trvalé časovače.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Instanci orchestrace můžete také převinout přímo pomocí příkazu [Azure Functions Core Tools](../functions-run-local.md) `durable rewind` . Má následující parametry:

* (povinné): **`id`** ID instance orchestrace
* (volitelné): **`reason`** Důvod pro převinutí instance Orchestration
* (volitelné): **`connection-string-setting`** Název nastavení aplikace, které obsahuje připojovací řetězec úložiště, který se má použít Výchozí hodnota je `AzureWebJobsStorage`.
* (volitelné): **`task-hub-name`** Název Durable Functions centra úloh, které se má použít Výchozí hodnota je `DurableFunctionsHub`. Dá se taky nastavit v souboru [Host. JSON](durable-functions-bindings.md#host-json)pomocí DurableTask: HubName.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Vymazat historii instance

Chcete-li odebrat všechna data přidružená k orchestraci, můžete historii instancí vyprázdnit. Například můžete chtít zbavit se řádků tabulky Azure a velkých objektů BLOB zprávy, pokud existují. Uděláte to tak, že použijete rozhraní [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_) API.

> [!NOTE]
> Rozhraní API je aktuálně dostupné jenom pro C# `PurgeInstanceHistoryAsync`

 Metoda má dvě přetížení. První z nich vyprázdní historii ID instance orchestrace:

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

Druhý příklad ukazuje funkci aktivovanou časovačem, která vyprázdní historii pro všechny instance orchestrace, které byly dokončeny po zadaném časovém intervalu. V takovém případě dojde k odebrání dat pro všechny instance, které byly dokončeny před 30 nebo více dny. Je naplánováno, že se spustí jednou za den, ve 12 dop.:

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
> Aby byl proces funkce aktivované časovým obdobím úspěšný, musí být běhový stav **dokončen**, **ukončen**nebo **se nezdařil**.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Historii instance orchestrace můžete vyprázdnit pomocí příkazu [Azure Functions Core Tools](../functions-run-local.md) `durable purge-history` . Podobně jako druhý C# příklad v předchozí části vyprázdní historii pro všechny instance orchestrace vytvořené během zadaného časového intervalu. Vyčištěné instance můžete dále filtrovat podle běhového stavu. Příkaz má několik parametrů:

* (volitelné): **`created-after`** Vyprázdní historii instancí vytvořených po tomto datu a čase (UTC). Byly přijaty hodnoty DateTime ve formátu ISO 8601.
* (volitelné): **`created-before`** Vyprázdnit historii instancí vytvořených před tímto datem a časem (UTC). Byly přijaty hodnoty DateTime ve formátu ISO 8601.
* (volitelné): **`runtime-status`** Vyprázdnit historii instancí s určitým stavem (například spuštěno nebo dokončeno). Může poskytovat více stavů (oddělené místo).
* (volitelné): **`connection-string-setting`** Název nastavení aplikace, které obsahuje připojovací řetězec úložiště, který se má použít Výchozí hodnota je `AzureWebJobsStorage`.
* (volitelné): **`task-hub-name`** Název Durable Functions centra úloh, které se má použít Výchozí hodnota je `DurableFunctionsHub`. Dá se taky nastavit v souboru [Host. JSON](durable-functions-bindings.md#host-json)pomocí DurableTask: HubName.

Následující příkaz odstraní historii všech neúspěšných instancí vytvořených před 14. listopadu 2018 na 7:35 odp. (UTC).

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Odstranění centra úloh

Pomocí příkazu [Azure Functions Core Tools](../functions-run-local.md) `durable delete-task-hub` můžete odstranit všechny artefakty úložiště přidružené k určitému centru úloh. Patří sem tabulky, fronty a objekty blob služby Azure Storage. Příkaz má dva parametry:

* (volitelné): **`connection-string-setting`** Název nastavení aplikace, které obsahuje připojovací řetězec úložiště, který se má použít Výchozí hodnota je `AzureWebJobsStorage`.
* (volitelné): **`task-hub-name`** Název Durable Functions centra úloh, které se má použít Výchozí hodnota je `DurableFunctionsHub`. Dá se taky nastavit v souboru [Host. JSON](durable-functions-bindings.md#host-json)pomocí DurableTask: HubName.

Následující příkaz odstraní všechna data služby Azure Storage přidružená k `UserTest` centru úloh.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Naučte se používat rozhraní API HTTP pro správu instancí.](durable-functions-http-api.md)
