---
title: Vazby pro Durable Functions – Azure
description: Použití aktivačních procedur a vazeb pro rozšíření Durable Functions pro Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: f297c89d2c3ba5692a44fab631c0d46c75f48692
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033589"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Vazby pro Durable Functions (Azure Functions)

Rozšíření [Durable Functions](durable-functions-overview.md) zavádí dvě nové aktivační vazby, které ovládají provádění funkcí Orchestrator a Activity. Zavádí také výstupní vazbu, která funguje jako klient pro modul runtime Durable Functions.

## <a name="orchestration-trigger"></a>Aktivační událost orchestrace

Aktivační událost orchestrace umožňuje vytvářet [trvalé funkce nástroje Orchestrator](durable-functions-types-features-overview.md#orchestrator-functions). Tato aktivační událost podporuje spouštění nových instancí funkcí Orchestrator a obnovení stávajících instancí funkcí Orchestrator, které čekají na úkol.

Když použijete nástroje sady Visual Studio pro Azure Functions, aktivační událost orchestrace se nakonfiguruje pomocí atributu .NET [OrchestrationTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationTriggerAttribute.html) .

Při psaní funkcí Orchestrator v skriptovacích jazycích (například JavaScript nebo C# skriptování) je aktivační událost Orchestration definována následujícím objektem JSON v `bindings` poli *funkce Function. JSON* :

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration`je název orchestrace. Jedná se o hodnotu, kterou klienti musí použít, když chtějí začít nové instance této funkce nástroje Orchestrator. Tato vlastnost je nepovinná. Pokud není zadaný, použije se název funkce.

Interně se tato triggerová vazba dotazuje řady front ve výchozím účtu úložiště pro aplikaci Function App. Tyto fronty jsou podrobnosti o interní implementaci tohoto rozšíření, což znamená, proč nejsou explicitně nakonfigurované ve vlastnostech vazby.

### <a name="trigger-behavior"></a>Chování triggeru

Tady jsou některé poznámky k triggeru orchestrace:

* **Multithreading** – jediné vlákno dispečera se používá pro všechna spuštění funkcí Orchestrator na jedné instanci hostitele. Z tohoto důvodu je důležité zajistit, aby kód funkce nástroje Orchestrator byl efektivní a neprováděl žádné vstupně-výstupní operace. Je také důležité zajistit, aby toto vlákno nevedlo žádnou asynchronní práci s výjimkou případů, kdy čekají na konkrétní Durable Functions typy úloh.
* **Zpracování nezpracovatelných zpráv** – v aktivačních událostech orchestrace se nepodporují žádné nepoškozené zprávy.
* **Viditelnost zprávy** – aktivační události orchestrace se odřadí do fronty a zůstanou v konfigurovatelné době neviditelné. Viditelnost těchto zpráv se automaticky obnoví, pokud je aplikace Function App spuštěná a v pořádku.
* **Návratové hodnoty** – návratové hodnoty jsou serializovány do formátu JSON a trvalé v tabulce historie orchestrace v úložišti tabulek Azure. Na tyto návratové hodnoty se může dotázat vazba klienta Orchestration, která je popsána dále.

> [!WARNING]
> Funkce nástroje Orchestrator by nikdy neměly používat žádné vstupní ani výstupní vazby kromě triggeru triggeru orchestrace. To by mohlo způsobit problémy s rozšířením trvalé úlohy, protože tyto vazby nepodléhají pravidlům v jednom vláknu a I/O. Pokud chcete použít jiné vazby, přidejte je do funkce aktivity volané z vaší funkce Orchestrator.

> [!WARNING]
> Funkce Orchestrator jazyka JavaScript by nikdy neměly `async`být deklarovány.

### <a name="trigger-usage-net"></a>Využití triggeru (.NET)

Aktivační vazba Orchestration podporuje vstupy i výstupy. Zde je několik věcí, které je potřeba znát při zpracování vstupu a výstupu:

* **vstupy** – funkce orchestrace .NET podporují jako typ parametru jenom [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) . Deserializace vstupů přímo v signatuře funkce není podporována. Aby bylo možné načíst vstupy funkcí nástroje Orchestrator, kód musí použít `getInput` metodu [GetInput\<T >](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetInput__1)(.NET) nebo (JavaScript). Tyto vstupy musí být typy serializovatelných hodnot JSON.
* **výstupy** – triggery orchestrace podporují výstupní hodnoty a také vstupy. Návratová hodnota funkce slouží k přiřazení výstupní hodnoty a musí být serializovatelný pomocí formátu JSON. Pokud funkce .NET vrátí `Task` nebo `void`, `null` uloží se jako výstup hodnota.

### <a name="trigger-sample"></a>Ukázka triggeru

Následující příklad kódu ukazuje, jak by nejjednodušší funkce "Hello World" Orchestrator vypadala takto:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> Objekt v JavaScriptu nepředstavuje DurableOrchestrationContext, ale [kontext funkce jako celek.](../functions-reference-node.md#context-object) `context` K metodám orchestrace můžete přistupovat prostřednictvím `context` `df` vlastnosti objektu.

> [!NOTE]
> Orchestrace JavaScriptu by měla `return`použít. Knihovna `durable-functions` se postará o `context.done` volání metody.

Většina funkcí Orchestrator Functions Activity Functions, takže zde je příklad "Hello World", který ukazuje, jak zavolat funkci aktivity:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-trigger"></a>Aktivační událost aktivity

Aktivační událost aktivity umožňuje vytvářet funkce, které jsou volány funkcemi Orchestrator, známé jako [funkce aktivity](durable-functions-types-features-overview.md#activity-functions).

Pokud používáte aplikaci Visual Studio, je aktivační událost aktivity nakonfigurována pomocí atributu [ActivityTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.ActivityTriggerAttribute.html) .NET.

Pokud používáte vs Code nebo Azure Portal pro vývoj, aktivační událost aktivity je definována následujícím objektem JSON v `bindings` poli *Function. JSON*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity`je název aktivity. Tato hodnota je název, který funkce Orchestrator používá k vyvolání této funkce aktivity. Tato vlastnost je nepovinná. Pokud není zadaný, použije se název funkce.

Interně se tato triggerová vazba dotazuje fronty ve výchozím účtu úložiště pro aplikaci Function App. Tato fronta představuje interní podrobnosti implementace rozšíření, což je důvod, proč není explicitně nakonfigurován ve vlastnostech vazby.

### <a name="trigger-behavior"></a>Chování triggeru

Zde jsou některé poznámky týkající se triggeru aktivity:

* **Dělení na vlákna** – na rozdíl od triggeru orchestrace, triggery aktivit nemají žádná omezení týkající se dělení na vlákna nebo v/v. Můžou se nacházet jako s běžnými funkcemi.
* **Zpracování nezpracovatelných zpráv** – v aktivačních událostech aktivit se nepodporují žádné nepoškozené zprávy.
* **Viditelnost zprávy** – zprávy triggeru aktivit se odřadí do fronty a udržují se v konfigurovatelné době jako neviditelné. Viditelnost těchto zpráv se automaticky obnoví, pokud je aplikace Function App spuštěná a v pořádku.
* **Návratové hodnoty** – návratové hodnoty jsou serializovány do formátu JSON a trvalé v tabulce historie orchestrace v úložišti tabulek Azure.

> [!WARNING]
> Služba Storage back-endu pro funkce aktivity je podrobností implementace a kód uživatele by neměl pracovat s těmito entitami úložiště přímo.

### <a name="trigger-usage-net"></a>Využití triggeru (.NET)

Vazba triggeru aktivity podporuje vstupy i výstupy, stejně jako Trigger orchestrace. Zde je několik věcí, které je potřeba znát při zpracování vstupu a výstupu:

* **vstupy** – funkce aktivity .NET nativně používají [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) jako typ parametru. Alternativně lze funkci aktivity deklarovat s libovolným typem parametru, který je serializovatelný pomocí JSON. Při použití `DurableActivityContext`můžete volat GetInput [\<T >](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html#Microsoft_Azure_WebJobs_DurableActivityContext_GetInput__1) pro načtení a deserializaci vstupu funkce Activity.
* **výstupy** – funkce aktivity podporují výstupní hodnoty a také vstupy. Návratová hodnota funkce slouží k přiřazení výstupní hodnoty a musí být serializovatelný pomocí formátu JSON. Pokud funkce .NET vrátí `Task` nebo `void`, `null` uloží se jako výstup hodnota.
* funkce aktivit rozhraní .NET může vytvořit vazby k `string instanceId` parametru, aby získala ID instance nadřazené orchestrace.

### <a name="trigger-sample"></a>Ukázka triggeru

Následující příklad kódu ukazuje, jak může vypadat jednoduchá "Hello World" funkce aktivity "", například:

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] DurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

Výchozí typ parametru vazby rozhraní .NET `ActivityTriggerAttribute` je. `DurableActivityContext` Nicméně triggery aktivit rozhraní .NET také podporují vazbu přímo na typy serializace JSON (včetně primitivních typů), takže stejnou funkci lze zjednodušit takto:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

Vazby JavaScriptu lze také předat jako další parametry, takže stejnou funkci lze zjednodušit takto:

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```


### <a name="using-input-and-output-bindings"></a>Použití vstupních a výstupních vazeb

Kromě vazby triggeru aktivity můžete použít regulární vstupní a výstupní vazby. Můžete například převést vstup na vazbu aktivity a odeslat zprávu centru EventHub pomocí výstupní vazby EventHub:

```json
{
  "bindings": [
    {
      "name": "message",
      "type": "activityTrigger",
      "direction": "in"
    },
    {
      "type": "eventHub",
      "name": "outputEventHubMessage",
      "connection": "EventhubConnectionSetting",
      "eventHubName": "eh_messages",
      "direction": "out"
  }
  ]
}
```

```javascript
module.exports = async function (context) {
    context.bindings.outputEventHubMessage = context.bindings.message;
};
```

## <a name="orchestration-client"></a>Klient Orchestration

Vazba klienta Orchestration umožňuje psát funkce, které komunikují s funkcemi nástroje Orchestrator. Tyto funkce jsou někdy označovány jako [klientské funkce](durable-functions-types-features-overview.md#client-functions). Například můžete pracovat na instancích orchestrace následujícími způsoby:

* Spusťte je.
* Dotaz na jejich stav
* Ukončete je.
* Posílat do nich události při jejich spuštění.
* Vyprázdnit historii instance

Pokud používáte aplikaci Visual Studio, můžete vytvořit propojení s klientem Orchestration pomocí atributu [OrchestrationClientAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) .net pro Durable Functions 1,0. Počínaje verzí Durable Functions 2,0 Preview můžete vytvořit propojení s klientem Orchestration pomocí `DurableClientAttribute` atributu .NET.

Pokud pro vývoj používáte skriptovací jazyky (například soubory *. csx* nebo *. js* ), aktivační událost orchestrace je definována následujícím `bindings` objektem JSON v poli *Function. JSON*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub`– Používá se ve scénářích, kdy více aplikací Function App sdílí stejný účet úložiště, ale musí být izolované od sebe navzájem. Pokud není zadaný, použije se výchozí hodnota `host.json` z. Tato hodnota musí odpovídat hodnotě používané cílovými funkcemi nástroje Orchestrator.
* `connectionName`– Název nastavení aplikace, které obsahuje připojovací řetězec účtu úložiště. Účet úložiště reprezentovaný tímto připojovacím řetězcem musí být stejný jako ten, který používá cílové funkce nástroje Orchestrator. Pokud tento parametr nezadáte, použije se výchozí připojovací řetězec účtu úložiště pro aplikaci Function App.

> [!NOTE]
> Ve většině případů doporučujeme tyto vlastnosti vynechat a spoléhat se na výchozí chování.

### <a name="client-usage"></a>Využití klienta

Ve funkcích .NET se obvykle vytváří vazba na `DurableOrchestrationClient`, která poskytuje úplný přístup ke všem klientským rozhraním API, která podporuje Durable Functions. Počínaje Durable Functions 2,0 se místo toho svážete s `IDurableOrchestrationClient` rozhraním. V jazyce JavaScript jsou stejná rozhraní API vystavena objektem vráceným `getClient`z. Mezi rozhraní API u objektu klienta patří:

* [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)
* [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)
* [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)
* [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)
* [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_)
* [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_)
* [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_)

Alternativně mohou funkce .NET navazovat `IAsyncCollector<T>` vazby `T` na [](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.StartOrchestrationArgs.html) WHERE je `JObject`StartOrchestrationArgs nebo.

Další informace o těchto operacích najdete v dokumentaci k rozhraní [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) API.

### <a name="client-sample-visual-studio-development"></a>Ukázka klienta (vývoj v aplikaci Visual Studio)

Tady je příklad funkce aktivované frontou, která spouští orchestraci HelloWorld.

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [OrchestrationClient] DurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

### <a name="client-sample-not-visual-studio"></a>Ukázka klienta (ne Visual Studio)

Pokud Visual Studio nepoužíváte pro vývoj, můžete vytvořit následující soubor *Function. JSON* . Tento příklad ukazuje, jak nakonfigurovat funkci spuštěnou ve frontě, která používá vazbu klienta trvalé orchestrace:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    }
  ]
}
```

Níže jsou uvedené ukázky pro konkrétní jazyk, které spouštějí nové instance funkcí nástroje Orchestrator.

#### <a name="c-sample"></a>Ukázka v jazyce C#

Následující příklad ukazuje, jak použít vazbu klienta trvalé orchestrace ke spuštění nové instance funkce ze funkce C# skriptu:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static Task<string> Run(string input, DurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

#### <a name="javascript-sample"></a>Ukázky jazyka JavaScript

Následující příklad ukazuje, jak použít vazbu klienta trvalé orchestrace k zahájení nové instance funkce z funkce JavaScriptu:

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

Další podrobnosti o počátečních instancích najdete ve [správě instancí](durable-functions-instance-management.md).

## <a name="entity-trigger"></a>Aktivační událost entity

Triggery entit umožňují vytvářet [funkce entit](durable-functions-entities.md). Tato aktivační událost podporuje zpracování událostí pro konkrétní instanci entity.

Když použijete nástroje sady Visual Studio pro Azure Functions, je aktivační událost entity nakonfigurovaná pomocí `EntityTriggerAttribute` atributu .NET.

> [!NOTE]
> Aktivační procedury entit jsou k dispozici v Durable Functions 2,0 a vyšších. Triggery entit ještě nejsou k dispozici pro JavaScript.

Interně se tato triggerová vazba dotazuje řady front ve výchozím účtu úložiště pro aplikaci Function App. Tyto fronty jsou podrobnosti o interní implementaci tohoto rozšíření, což znamená, proč nejsou explicitně nakonfigurované ve vlastnostech vazby.

### <a name="trigger-behavior"></a>Chování triggeru

Tady jsou některé poznámky k triggeru entit:

* **Jeden podproces**: Jedno dispečerský podproces se používá ke zpracování operací konkrétní entity. Pokud je jedna entita souběžně odeslána více zpráv, operace budou zpracovávány po jednom čase.
* **Zpracování nezpracovatelných zpráv** – v aktivačních událostech entit se nepodporují žádné nepoškozené zprávy.
* **Viditelnost zprávy** – zprávy triggerů entit se odřadí do fronty a jsou pro konfigurovatelnou dobu neviditelné. Viditelnost těchto zpráv se automaticky obnoví, pokud je aplikace Function App spuštěná a v pořádku.
* **Návratové hodnoty** – funkce entity nepodporují návratové hodnoty. K dispozici jsou konkrétní rozhraní API, která lze použít k uložení stavu nebo předávat hodnoty zpět do orchestrace.

Všechny změny stavu provedené v entitě během jejího spuštění budou automaticky uchovány po dokončení spuštění.

### <a name="trigger-usage-net"></a>Využití triggeru (.NET)

Každá funkce entity má typ `IDurableEntityContext`parametru, který má následující členy:

* **EntityName**: Získá název aktuálně spuštěné entity.
* **EntityKey**: Získá klíč aktuálně vykonávané entity.
* **EntityId**: Získá ID aktuálně vykonávané entity.
* **Operace**: Získá název aktuální operace.
* **IsNewlyConstructed**: vrátí `true` , pokud entita neexistovala před touto operací.
* **GetState\<TState > ()** : Získá aktuální stav entity. `TState` Parametr musí být primitivního typu nebo typu serializace JSON.
* **Setstate (objekt)** : aktualizuje stav entity. `object` Parametr musí být primitivní objekt nebo objekt JSON, který lze serializovat.
* **GetInput\<TInput > ()** : Získá vstup pro aktuální operaci. Parametr `TInput` typu musí představovat primitivní typ nebo typ, který je serializován jako JSON.
* **Return (objekt)** : vrátí hodnotu orchestrace, která volala operaci. `object` Parametr musí být primitivní objekt nebo objekt JSON, který lze serializovat.
* **DestructOnExit ()** : odstraní entitu po dokončení aktuální operace.
* **SignalEntity (EntityId, String, Object)** : pošle jednosměrnou zprávu entitě. `object` Parametr musí být primitivní objekt nebo objekt JSON, který lze serializovat.

Při použití režimu `IDurableEntityContext` programování entit založených na třídě lze objekt odkazovat `Entity.Current` pomocí vlastnosti vlákna-static.

### <a name="trigger-sample---entity-function"></a>Ukázka triggeru – funkce entity

Následující kód je příkladem jednoduché entity *čítače* implementované jako standardní funkce. Tato funkce definuje tři *operace*, `add` `reset`, a `get`, `currentValue`každý z nich pracuje s hodnotou celočíselného stavu.

```csharp
[FunctionName(nameof(Counter))]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();

    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            currentValue += operand;
            break;
        case "reset":
            currentValue = 0;
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }

    ctx.SetState(currentValue);
}
```

### <a name="trigger-sample---entity-class"></a>Trigger Sample – Třída entity

Následující příklad je ekvivalentní implementace předchozí `Counter` entity pomocí tříd a metod .NET.

```csharp
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;
    
    public void Reset() => this.CurrentValue = 0;
    
    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

> [!NOTE]
> Metoda vstupního bodu funkce s `[FunctionName]` atributem *musí* být deklarována `static` při použití tříd entit. Nestatické metody vstupního bodu mohou způsobit inicializaci více objektů a potenciálně jiné nedefinované chování.

Třídy entit mají zvláštní mechanismy pro interakci s vazbami a vkládání závislostí .NET. Další informace najdete v článku [trvalé entity](durable-functions-entities.md) .

## <a name="entity-client"></a>Klient entit

Vazba klienta entit umožňuje asynchronně aktivovat [funkce entit](#entity-trigger). Tyto funkce jsou někdy označovány jako [klientské funkce](durable-functions-types-features-overview.md#client-functions).

Pokud používáte aplikaci Visual Studio, můžete vytvořit propojení s klientem entity pomocí `DurableClientAttribute` atributu .NET.

> [!NOTE]
> Lze také použít pro vytvoření vazby na [klienta Orchestration.](#orchestration-client) `[DurableClientAttribute]`

Pokud pro vývoj používáte skriptovací jazyky (například soubory *. csx* nebo *. js* ), aktivační událost entity je definována následujícím `bindings` objektem JSON v poli *Function. JSON*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "out"
}
```

* `taskHub`– Používá se ve scénářích, kdy více aplikací Function App sdílí stejný účet úložiště, ale musí být izolované od sebe navzájem. Pokud není zadaný, použije se výchozí hodnota `host.json` z. Tato hodnota musí odpovídat hodnotě používané funkcemi cílové entity.
* `connectionName`– Název nastavení aplikace, které obsahuje připojovací řetězec účtu úložiště. Účet úložiště reprezentovaný tímto připojovacím řetězcem musí být stejný, jaký používá funkce cílové entity. Pokud tento parametr nezadáte, použije se výchozí připojovací řetězec účtu úložiště pro aplikaci Function App.

> [!NOTE]
> Ve většině případů doporučujeme vynechat volitelné vlastnosti a spoléhat se na výchozí chování.

### <a name="entity-client-usage"></a>Využití klienta entit

Ve funkcích .NET se obvykle vytváří vazba na `IDurableEntityClient`, která poskytuje úplný přístup ke všem klientským rozhraním API podporovaným trvalými entitami. Můžete také vytvořit `IDurableClient` připojení k rozhraní, které poskytuje přístup k klientským rozhraním API pro entity i orchestrace. Mezi rozhraní API u objektu klienta patří:

* **ReadEntityStateAsync\<T >** : přečte stav entity.
* **SignalEntityAsync**: pošle jednosměrnou zprávu entitě a počká, až se zazařazuje do fronty.
* **SignalEntityAsync\<TEntityInterface >** : totéž jako `SignalEntityAsync` , ale používá vygenerovaný objekt proxy typu `TEntityInterface`.
* **CreateEntityProxy\<TEntityInterface >** : dynamicky generuje dynamický proxy typ `TEntityInterface` pro zajištění typově bezpečných volání entit.

> [!NOTE]
> Je důležité pochopit, že předchozí operace "signál" jsou všechny asynchronní. Není možné vyvolat funkci entity a vrátit návratovou hodnotu z klienta. Podobně `SignalEntityAsync` může vrátit zpět, aby entita spustila provádění operace. Pouze funkce Orchestrator mohou vyvolat funkce entit synchronně a zpracovávat návratové hodnoty.

Rozhraní API vyžadují zadání jedinečného identifikátoru entity `EntityId`jako. `SignalEntityAsync` Tato rozhraní API také volitelně přebírají název operace entity jako `string` a datovou část operace jako `object`serializaci JSON. Pokud cílová entita neexistuje, vytvoří se automaticky se zadaným ID entity.

### <a name="client-sample-untyped"></a>Ukázka klienta (netypové)

Tady je příklad funkce aktivované frontou, která vyvolá entitu "Counter".

```csharp
[FunctionName("AddFromQueue")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableEntityClient client)
{
    // Entity operation input comes from the queue message content.
    var entityId = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(input);
    return client.SignalEntityAsync(entityId, "Add", amount);
}
```

### <a name="client-sample-typed"></a>Ukázka klienta (typované)

Pro typově bezpečný přístup k operacím entit je možné vygenerovat objekt proxy. Chcete-li vygenerovat typ proxy bezpečného typu, musí být pro typ entity implementováno rozhraní. Předpokládejme například, že `Counter` entita zmíněná dříve `ICounter` implementovala rozhraní, které je definováno následujícím způsobem:

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    int Get();
}

public class Counter : ICounter
{
    // ...
}
```

Klientský kód pak může použít `SignalEntityAsync<TEntityInterface>` a `ICounter` zadat rozhraní jako parametr typu pro vygenerování proxy typu s bezpečným typem. Toto použití typu proxy bezpečného typu je znázorněno v následující ukázce kódu:

```csharp
[FunctionName("UserDeleteAvailable")]
public static async Task AddValueClient(
    [QueueTrigger("my-queue")] string message,
    [DurableClient] IDurableEntityClient client)
{
    var target = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(message);
    await client.SignalEntityAsync<ICounter>(target, proxy => proxy.Add(amount));
}
```

V předchozím příkladu `proxy` je parametr dynamicky generovanou `ICounter`instancí, která interně `Add` překládá volání do do `SignalEntityAsync`ekvivalentního (netypového) volání.

Pro definování rozhraní entit je k dispozici několik pravidel:

* Parametr `TEntityInterface` typu v `SignalEntityAsync<TEntityInterface>` musí být rozhraní.
* Rozhraní entit musí definovat pouze metody.
* Metody rozhraní entit nesmí definovat více než jeden parametr.
* Metody rozhraní entit musí vracet `void`, `Task`nebo `Task<T>` , kde `T` je některá návratová hodnota.
* Rozhraní entit musí mít přesně jednu konkrétní implementační třídu v rámci stejného sestavení (tj. Třída entity).

V případě porušení `InvalidOperationException` některého z těchto pravidel bude vyvolána za běhu. Zpráva o výjimce bude vysvětlit, které pravidlo bylo přerušeno.

> [!NOTE]
> `SignalEntityAsync` Rozhraní API označují jednosměrné operace. Pokud rozhraní entit vrátí `Task<T>`, bude mít hodnota `T` parametru vždycky hodnotu null nebo `default`.

<a name="host-json"></a>

## <a name="hostjson-settings"></a>nastavení Host.JSON

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Reference k integrovanému HTTP API pro správu instancí](durable-functions-http-api.md)