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
ms.openlocfilehash: 1b056ce8afe86fcd6629aff23ac95acae02ed9ba
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299879"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Vazby pro Durable Functions (Azure Functions)

Rozšíření [Durable Functions](durable-functions-overview.md) zavádí dvě nové aktivační vazby, které ovládají provádění funkcí Orchestrator a Activity. Zavádí také výstupní vazbu, která funguje jako klient pro modul runtime Durable Functions.

## <a name="orchestration-trigger"></a>Aktivační událost orchestrace

Aktivační událost orchestrace umožňuje vytvářet [trvalé funkce nástroje Orchestrator](durable-functions-types-features-overview.md#orchestrator-functions). Tato aktivační událost podporuje spouštění nových instancí funkcí Orchestrator a obnovení stávajících instancí funkcí Orchestrator, které čekají na úkol.

Když použijete nástroje sady Visual Studio pro Azure Functions, aktivační událost orchestrace se nakonfiguruje pomocí atributu .NET [OrchestrationTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationTriggerAttribute.html) .

Při psaní funkcí nástroje Orchestrator ve skriptovacích jazycích (například JavaScript nebo C# skriptování) je aktivační událost Orchestration definována následujícím objektem JSON v poli `bindings` souboru *Function. JSON* :

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` je název orchestrace. Jedná se o hodnotu, kterou klienti musí použít, když chtějí začít nové instance této funkce nástroje Orchestrator. Tato vlastnost je nepovinná. Pokud není zadaný, použije se název funkce.

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
> Funkce Orchestrator pro JavaScript by nikdy neměly být deklarované `async`.

### <a name="trigger-usage-net"></a>Využití triggeru (.NET)

Aktivační vazba Orchestration podporuje vstupy i výstupy. Zde je několik věcí, které je potřeba znát při zpracování vstupu a výstupu:

* **vstupy** – funkce orchestrace .NET podporují jako typ parametru jenom [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) . Deserializace vstupů přímo v signatuře funkce není podporována. Aby bylo možné načíst vstupy funkcí nástroje Orchestrator, kód musí použít metodu [GetInput @ no__t-1T >](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetInput__1)(.NET) nebo `getInput` (JavaScript). Tyto vstupy musí být typy serializovatelných hodnot JSON.
* **výstupy** – triggery orchestrace podporují výstupní hodnoty a také vstupy. Návratová hodnota funkce slouží k přiřazení výstupní hodnoty a musí být serializovatelný pomocí formátu JSON. Pokud funkce .NET vrátí `Task` nebo `void`, bude hodnota `null` uložena jako výstup.

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
> Objekt `context` v JavaScriptu nepředstavuje DurableOrchestrationContext, ale [kontext funkce jako celek](../functions-reference-node.md#context-object). Metody orchestrace můžete přistupovat přes vlastnost `df` objektu `context`.

> [!NOTE]
> Orchestrace JavaScriptu by měly používat `return`. Knihovna `durable-functions` se stará o volání metody `context.done`.

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

Pokud používáte VS Code nebo Azure Portal pro vývoj, aktivační událost aktivity je definována následujícím objektem JSON v poli `bindings` *funkce Function. JSON*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` je název aktivity. Tato hodnota je název, který funkce Orchestrator používá k vyvolání této funkce aktivity. Tato vlastnost je nepovinná. Pokud není zadaný, použije se název funkce.

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

* **vstupy** – funkce aktivity .NET nativně používají [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) jako typ parametru. Alternativně lze funkci aktivity deklarovat s libovolným typem parametru, který je serializovatelný pomocí JSON. Když použijete `DurableActivityContext`, můžete volat [GetInput @ no__t-2T >](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html#Microsoft_Azure_WebJobs_DurableActivityContext_GetInput__1) pro načtení a deserializaci vstupu funkce Activity.
* **výstupy** – funkce aktivity podporují výstupní hodnoty a také vstupy. Návratová hodnota funkce slouží k přiřazení výstupní hodnoty a musí být serializovatelný pomocí formátu JSON. Pokud funkce .NET vrátí `Task` nebo `void`, bude hodnota `null` uložena jako výstup.
* funkce **aktivit rozhraní .NET** může vytvořit vazby na parametr `string instanceId`, aby získal ID instance nadřazené orchestrace.

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

Výchozí typ parametru pro vazbu rozhraní .NET `ActivityTriggerAttribute` je `DurableActivityContext`. Nicméně triggery aktivit rozhraní .NET také podporují vazbu přímo na typy serializace JSON (včetně primitivních typů), takže stejnou funkci lze zjednodušit takto:

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

Pokud používáte aplikaci Visual Studio, můžete vytvořit propojení s klientem Orchestration pomocí atributu [OrchestrationClientAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) .net pro Durable Functions 1,0. Počínaje verzí Durable Functions 2,0 Preview můžete vytvořit propojení s klientem Orchestration pomocí atributu `DurableClientAttribute` .NET.

Pokud pro vývoj používáte skriptovací jazyky (například soubory *. csx* nebo *. js* ), aktivační událost Orchestration je definována následujícím objektem JSON v poli `bindings` *funkce Function. JSON*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub` – používá se ve scénářích, kdy více aplikací Function App sdílí stejný účet úložiště, ale musí být od sebe izolované. Pokud není zadaný, použije se výchozí hodnota z `host.json`. Tato hodnota musí odpovídat hodnotě používané cílovými funkcemi nástroje Orchestrator.
* `connectionName` – název nastavení aplikace, které obsahuje připojovací řetězec účtu úložiště. Účet úložiště reprezentovaný tímto připojovacím řetězcem musí být stejný jako ten, který používá cílové funkce nástroje Orchestrator. Pokud tento parametr nezadáte, použije se výchozí připojovací řetězec účtu úložiště pro aplikaci Function App.

> [!NOTE]
> Ve většině případů doporučujeme tyto vlastnosti vynechat a spoléhat se na výchozí chování.

### <a name="client-usage"></a>Využití klienta

Ve funkcích .NET se obvykle vytváří vazba na `DurableOrchestrationClient`, která poskytuje úplný přístup ke všem klientským rozhraním API, která podporuje Durable Functions. Počínaje Durable Functions 2,0 se místo toho svážete s rozhraním `IDurableOrchestrationClient`. V jazyce JavaScript jsou stejná rozhraní API vystavena objektem vráceným z `getClient`. Mezi rozhraní API u objektu klienta patří:

* [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)
* [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)
* [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)
* [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)
* [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_)
* [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_)
* [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_)

Alternativně mohou funkce .NET navazovat vazby na `IAsyncCollector<T>`, kde `T` je [StartOrchestrationArgs](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.StartOrchestrationArgs.html) nebo `JObject`.

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

#### <a name="c-sample"></a>C#Vzorku

Následující příklad ukazuje, jak použít vazbu klienta trvalé orchestrace ke spuštění nové instance funkce ze funkce C# skriptu:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static Task<string> Run(string input, DurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

#### <a name="javascript-sample"></a>Ukázka JavaScriptu

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

Když použijete nástroje sady Visual Studio pro Azure Functions, je aktivační událost entity nakonfigurovaná pomocí atributu `EntityTriggerAttribute` .NET.

> [!NOTE]
> Aktivační procedury entit jsou k dispozici v Durable Functions 2,0 a vyšších. Triggery entit ještě nejsou k dispozici pro JavaScript.

Interně se tato triggerová vazba dotazuje řady front ve výchozím účtu úložiště pro aplikaci Function App. Tyto fronty jsou podrobnosti o interní implementaci tohoto rozšíření, což znamená, proč nejsou explicitně nakonfigurované ve vlastnostech vazby.

### <a name="trigger-behavior"></a>Chování triggeru

Tady jsou některé poznámky k triggeru entit:

* **Jeden podproces**: jeden dispečerský podproces se používá ke zpracování operací konkrétní entity. Pokud je jedna entita souběžně odeslána více zpráv, operace budou zpracovávány po jednom čase.
* **Zpracování nezpracovatelných zpráv** – v aktivačních událostech entit se nepodporují žádné nepoškozené zprávy.
* **Viditelnost zprávy** – zprávy triggerů entit se odřadí do fronty a jsou pro konfigurovatelnou dobu neviditelné. Viditelnost těchto zpráv se automaticky obnoví, pokud je aplikace Function App spuštěná a v pořádku.
* **Návratové hodnoty** – funkce entity nepodporují návratové hodnoty. K dispozici jsou konkrétní rozhraní API, která lze použít k uložení stavu nebo předávat hodnoty zpět do orchestrace.

Všechny změny stavu provedené v entitě během jejího spuštění budou automaticky uchovány po dokončení spuštění.

### <a name="trigger-usage-net"></a>Využití triggeru (.NET)

Každá funkce entity má typ parametru `IDurableEntityContext`, který má následující členy:

* **EntityName**: název aktuálně spuštěné entity.
* **EntityKey**: klíč aktuálně vykonávané entity.
* **EntityId**: ID aktuálně vykonávané entity.
* **OperationName**: název aktuální operace.
* **HasState**: zda entita existuje, to znamená, že má nějaký stav. 
* **GetState @ no__t-1TState > ()** : Získá aktuální stav entity. Pokud ještě neexistuje, vytvoří se a inicializuje `default<TState>`. Parametr `TState` musí být primitivního typu nebo typu JSON, který lze serializovat. 
* **GetState @ no__t-1TState > (initfunction)** : Získá aktuální stav entity. Pokud ještě neexistuje, vytvoří se voláním zadaného parametru `initfunction`. Parametr `TState` musí být primitivního typu nebo typu JSON, který lze serializovat. 
* **Setstate (ARG)** : vytvoří nebo aktualizuje stav entity. Parametr `arg` musí být objekt nebo primitivum s serializací JSON.
* **DeleteState ()** : odstraní stav entity. 
* **GetInput @ no__t-1TInput > ()** : Získá vstup pro aktuální operaci. Parametr typu `TInput` musí být primitivního typu nebo typu serializace JSON.
* **Return (ARG)** : vrátí hodnotu do orchestrace, která volala operaci. Parametr `arg` musí být objekt primitivního typu nebo serializace JSON.
* **SignalEntity (EntityId, operace, vstup)** : pošle jednosměrnou zprávu entitě. Parametr `operation` musí být řetězec, který není null, a parametr `input` musí být primitivní nebo objekt JSON, který lze serializovat.
* **CreateNewOrchestration (orchestratorFunctionName, Input)** : spustí novou orchestraci. Parametr `input` musí být objekt primitivního typu nebo serializace JSON.

Objekt `IDurableEntityContext` předaný do funkce entity lze použít pomocí vlastnosti Async-Local `Entity.Current`. Tento přístup je vhodný při použití programovacího modelu založeného na třídě.

### <a name="trigger-sample-function-based-syntax"></a>Ukázka triggeru (syntaxe založená na funkcích)

Následující kód je příkladem jednoduché entity *čítače* implementované jako trvalá funkce. Tato funkce definuje tři operace, `add`, `reset` a `get`, z nichž každý pracuje na celočíselném stavu.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>()));
            break;
    }
}
```

Další informace o syntaxi založené na funkcích a způsobu jejich použití naleznete v tématu [syntaxe založená na funkcích](durable-functions-dotnet-entities.md#function-based-syntax).

### <a name="trigger-sample-class-based-syntax"></a>Ukázka triggeru (syntaxe založená na třídě)

Následující příklad je ekvivalentní implementace entity `Counter` pomocí tříd a metod.

```csharp
[JsonObject(MemberSerialization.OptIn)]
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

Stav této entity je objekt typu `Counter`, který obsahuje pole, které ukládá aktuální hodnotu čítače. Chcete-li zachovat tento objekt v úložišti, je serializován a rekonstruován knihovnou [JSON.NET](https://www.newtonsoft.com/json) . 

Další informace o syntaxi založené na třídě a způsobu jejich použití naleznete v tématu [definování tříd entit](durable-functions-dotnet-entities.md#defining-entity-classes).

> [!NOTE]
> Metoda vstupního bodu funkce s atributem `[FunctionName]` *musí* být při použití tříd entity deklarována `static`. Nestatické metody vstupního bodu mohou způsobit inicializaci více objektů a potenciálně jiné nedefinované chování.

Třídy entit mají zvláštní mechanismy pro interakci s vazbami a vkládání závislostí .NET. Další informace najdete v tématu [vytváření entit](durable-functions-dotnet-entities.md#entity-construction).

## <a name="entity-client"></a>Klient entit

Vazba klienta entit umožňuje asynchronně aktivovat [funkce entit](#entity-trigger). Tyto funkce jsou někdy označovány jako [klientské funkce](durable-functions-types-features-overview.md#client-functions).

Pokud používáte aplikaci Visual Studio, můžete vytvořit propojení s klientem entity pomocí atributu `DurableClientAttribute` .NET.

> [!NOTE]
> @No__t-0 lze také použít k vytvoření vazby na [klienta Orchestration](#orchestration-client).

Pokud pro vývoj používáte skriptovací jazyky (například soubory *. csx* nebo *. js* ), aktivační událost entity je definována následujícím objektem JSON v poli `bindings` *funkce Function. JSON*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "out"
}
```

* `taskHub` – používá se ve scénářích, kdy více aplikací Function App sdílí stejný účet úložiště, ale musí být od sebe izolované. Pokud není zadaný, použije se výchozí hodnota z `host.json`. Tato hodnota musí odpovídat hodnotě používané funkcemi cílové entity.
* `connectionName` – název nastavení aplikace, které obsahuje připojovací řetězec účtu úložiště. Účet úložiště reprezentovaný tímto připojovacím řetězcem musí být stejný, jaký používá funkce cílové entity. Pokud tento parametr nezadáte, použije se výchozí připojovací řetězec účtu úložiště pro aplikaci Function App.

> [!NOTE]
> Ve většině případů doporučujeme vynechat volitelné vlastnosti a spoléhat se na výchozí chování.

### <a name="entity-client-usage"></a>Využití klienta entit

Ve funkcích .NET se obvykle vytváří vazba na `IDurableEntityClient`, což poskytuje úplný přístup ke všem klientským rozhraním API podporovaným trvalými entitami. Můžete také vytvořit připojení k rozhraní @no__t 0, které poskytuje přístup k klientským rozhraním API pro entity i orchestrace. Mezi rozhraní API u objektu klienta patří:

* **ReadEntityStateAsync @ no__t-1T >** : přečte stav entity. Vrátí odpověď, která označuje, zda cílová entita existuje, a pokud ano, jaký je její stav.
* **SignalEntityAsync**: pošle jednosměrnou zprávu entitě a počká, až se zazařazuje do fronty.

Před odesláním signálu není nutné vytvořit cílovou entitu – stav entity lze vytvořit v rámci funkce entity, která zpracovává signál.

> [!NOTE]
> Je důležité si uvědomit, že "signály" odesílané z klienta jsou jednoduše zařazené do fronty, aby je bylo možné zpracovat asynchronně později. Konkrétně `SignalEntityAsync` obvykle vrací před tím, než entita spustí operaci, a není možné vracet návratovou hodnotu ani sledovat výjimky. Pokud jsou vyžadovány silnější záruky (např. pro pracovní postupy), měly by být použity *funkce nástroje Orchestrator* , které mohou čekat na dokončení operací entit a mohou zpracovávat návratové hodnoty a sledovat výjimky.

### <a name="example-client-signals-entity-directly"></a>Příklad: klient signalizuje entitu přímo

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

### <a name="example-client-signals-entity-via-interface"></a>Příklad: klient signalizuje entitu prostřednictvím rozhraní.

Pokud je to možné, doporučujeme přistoupit [k entitám prostřednictvím rozhraní](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) , protože poskytuje další kontrolu typu. Předpokládejme například, že entita `Counter` zmíněná dříve implementovala rozhraní `ICounter` definované následujícím způsobem:

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    Task<int> Get();
}

public class Counter : ICounter
{
    // ...
}
```

Klientský kód pak může použít `SignalEntityAsync<ICounter>` pro vygenerování proxy typu, který je typově bezpečný:

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

Parametr `proxy` je dynamicky generovaná instance `ICounter`, která interně překládá volání `Add` do ekvivalentního (netypového) volání do `SignalEntityAsync`.

> [!NOTE]
> Rozhraní API `SignalEntityAsync` reprezentují jednosměrné operace. Pokud rozhraní entit vrátí `Task<T>`, hodnota parametru `T` bude vždy null nebo `default`.

Konkrétně není vhodné signalizovat operaci `Get`, protože není vrácena žádná hodnota. Místo toho mohou klienti použít buď `ReadStateAsync` pro přístup ke stavu čítače přímo, nebo může spustit funkci Orchestrator, která volá operaci `Get`. 

<a name="host-json"></a>
## <a name="hostjson-settings"></a>nastavení Host. JSON

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Reference k integrovanému HTTP API pro správu instancí](durable-functions-http-api.md)
