---
title: Vazby pro Durable Functions – Azure
description: Použití aktivačních procedur a vazeb pro rozšíření Durable Functions pro Azure Functions.
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: fe64945aeb71011e769ba4016ae36cb557f41f33
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102424253"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Vazby pro Durable Functions (Azure Functions)

Rozšíření [Durable Functions](durable-functions-overview.md) zavádí dvě nové aktivační vazby, které ovládají provádění funkcí Orchestrator a Activity. Zavádí také výstupní vazbu, která funguje jako klient pro modul runtime Durable Functions.

## <a name="orchestration-trigger"></a>Aktivační událost orchestrace

Aktivační událost orchestrace umožňuje vytvářet [trvalé funkce nástroje Orchestrator](durable-functions-types-features-overview.md#orchestrator-functions). Tato aktivační událost podporuje spouštění nových instancí funkcí Orchestrator a obnovení stávajících instancí funkcí Orchestrator, které čekají na úkol.

Když použijete nástroje sady Visual Studio pro Azure Functions, aktivační událost orchestrace se nakonfiguruje pomocí atributu .NET [OrchestrationTriggerAttribute](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.orchestrationtriggerattribute) .

Při psaní funkcí Orchestrator v skriptovacích jazycích (například skriptování v jazyce JavaScript nebo C#) je aktivační událost Orchestration definována následujícím objektem JSON v poli `bindings` *function.jsv* souboru:

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` je název orchestrace, kterou musí klienti používat, když chtějí začít nové instance této funkce nástroje Orchestrator. Tato vlastnost je nepovinná. Pokud není zadaný, použije se název funkce.

Interně se tato triggerová vazba dotazuje řady front ve výchozím účtu úložiště pro aplikaci Function App. Tyto fronty jsou podrobnosti o interní implementaci tohoto rozšíření, což znamená, proč nejsou explicitně nakonfigurované ve vlastnostech vazby.

### <a name="trigger-behavior"></a>Chování aktivační události

Tady jsou některé poznámky k triggeru orchestrace:

* **Multithreading** – jediné vlákno dispečera se používá pro všechna spuštění funkcí Orchestrator na jedné instanci hostitele. Z tohoto důvodu je důležité zajistit, aby kód funkce nástroje Orchestrator byl efektivní a neprováděl žádné vstupně-výstupní operace. Je také důležité zajistit, aby toto vlákno nevedlo žádnou asynchronní práci s výjimkou případů, kdy čekají na konkrétní Durable Functions typy úloh.
* **Zpracování nezpracovatelných zpráv** – v aktivačních událostech orchestrace se nepodporují žádné nepoškozené zprávy.
* **Viditelnost zprávy** – aktivační události orchestrace se odřadí do fronty a zůstanou v konfigurovatelné době neviditelné. Viditelnost těchto zpráv se automaticky obnoví, pokud je aplikace Function App spuštěná a v pořádku.
* **Návratové hodnoty** – návratové hodnoty jsou serializovány do formátu JSON a trvalé v tabulce historie orchestrace v úložišti tabulek Azure. Na tyto návratové hodnoty se může dotázat vazba klienta Orchestration, která je popsána dále.

> [!WARNING]
> Funkce nástroje Orchestrator by nikdy neměly používat žádné vstupní ani výstupní vazby kromě triggeru triggeru orchestrace. To by mohlo způsobit problémy s rozšířením trvalé úlohy, protože tyto vazby nepodléhají pravidlům v jednom vláknu a I/O. Pokud chcete použít jiné vazby, přidejte je do funkce aktivity volané z vaší funkce Orchestrator.

> [!WARNING]
> Funkce Orchestrator jazyka JavaScript by nikdy neměly být deklarovány `async` .

### <a name="trigger-usage-net"></a>Využití triggeru (.NET)

Aktivační vazba Orchestration podporuje vstupy i výstupy. Zde je několik věcí, které je potřeba znát při zpracování vstupu a výstupu:

* **vstupy** – funkce orchestrace .NET podporují jenom `DurableOrchestrationContext` jako typ parametru. Deserializace vstupů přímo v signatuře funkce není podporována. Kód musí `GetInput<T>` `getInput` pro načtení vstupních funkcí nástroje Orchestrator použít metodu (.NET) nebo (JavaScript). Tyto vstupy musí být typy serializovatelných hodnot JSON.
* **výstupy** – triggery orchestrace podporují výstupní hodnoty a také vstupy. Návratová hodnota funkce slouží k přiřazení výstupní hodnoty a musí být serializovatelný pomocí formátu JSON. Pokud funkce .NET vrátí `Task` nebo `void` , uloží se `null` jako výstup hodnota.

### <a name="trigger-sample"></a>Ukázka triggeru

Následující příklad kódu ukazuje, jak by nejjednodušší funkce "Hello World" Orchestrator vypadala takto:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```
> [!NOTE]
> Předchozí kód je pro Durable Functions 2. x. Pro Durable Functions 1. x je nutné použít `DurableOrchestrationContext` místo `IDurableOrchestrationContext` . Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

#### <a name="javascript-functions-20-only"></a>JavaScript (pouze funkce 2,0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> `context`Objekt v JavaScriptu nepředstavuje DurableOrchestrationContext, ale [kontext funkce jako celek](../functions-reference-node.md#context-object). K metodám orchestrace můžete přistupovat prostřednictvím `context` `df` vlastnosti objektu.

> [!NOTE]
> Orchestrace JavaScriptu by měla použít `return` . `durable-functions`Knihovna se postará o volání `context.done` metody.

Většina funkcí Orchestrator Functions Activity Functions, takže zde je příklad "Hello World", který ukazuje, jak zavolat funkci aktivity:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

> [!NOTE]
> Předchozí kód je pro Durable Functions 2. x. Pro Durable Functions 1. x je nutné použít `DurableOrchestrationContext` místo `IDurableOrchestrationContext` . Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

#### <a name="javascript-functions-20-only"></a>JavaScript (pouze funkce 2,0)

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

Pokud používáte aplikaci Visual Studio, je aktivační událost aktivity nakonfigurována pomocí `ActivityTriggerAttribute` atributu rozhraní .NET.

Pokud používáte VS Code nebo Azure Portal pro vývoj, aktivační událost aktivity je definována následujícím objektem JSON v `bindings` poli *function.jsna*:

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

### <a name="trigger-behavior"></a>Chování aktivační události

Zde jsou některé poznámky týkající se triggeru aktivity:

* **Dělení na vlákna** – na rozdíl od triggeru orchestrace, triggery aktivit nemají žádná omezení týkající se dělení na vlákna nebo v/v. Můžou se nacházet jako s běžnými funkcemi.
* **Zpracování nezpracovatelných zpráv** – v aktivačních událostech aktivit se nepodporují žádné nepoškozené zprávy.
* **Viditelnost zprávy** – zprávy triggeru aktivit se odřadí do fronty a udržují se v konfigurovatelné době jako neviditelné. Viditelnost těchto zpráv se automaticky obnoví, pokud je aplikace Function App spuštěná a v pořádku.
* **Návratové hodnoty** – návratové hodnoty jsou serializovány do formátu JSON a trvalé v tabulce historie orchestrace v úložišti tabulek Azure.

> [!WARNING]
> Služba Storage back-endu pro funkce aktivity je podrobností implementace a kód uživatele by neměl pracovat s těmito entitami úložiště přímo.

### <a name="trigger-usage-net"></a>Využití triggeru (.NET)

Vazba triggeru aktivity podporuje vstupy i výstupy, stejně jako Trigger orchestrace. Zde je několik věcí, které je potřeba znát při zpracování vstupu a výstupu:

* **vstupy** – funkce aktivity .NET nativně používají `DurableActivityContext` jako typ parametru. Alternativně lze funkci aktivity deklarovat s libovolným typem parametru, který je serializovatelný pomocí JSON. Při použití `DurableActivityContext` můžete zavolat `GetInput<T>` na načtení a deserializaci vstupu funkce Activity.
* **výstupy** – funkce aktivity podporují výstupní hodnoty a také vstupy. Návratová hodnota funkce slouží k přiřazení výstupní hodnoty a musí být serializovatelný pomocí formátu JSON. Pokud funkce .NET vrátí `Task` nebo `void` , uloží se `null` jako výstup hodnota.
* funkce **aktivit rozhraní .NET** může vytvořit vazby k `string instanceId` parametru, aby získala ID instance nadřazené orchestrace.

### <a name="trigger-sample"></a>Ukázka triggeru

Následující příklad kódu ukazuje, jak může vypadat jednoduchá "Hello World" funkce aktivity "", například:

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] IDurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

> [!NOTE]
> Předchozí kód je pro Durable Functions 2. x. Pro Durable Functions 1. x je nutné použít `DurableActivityContext` místo `IDurableActivityContext` . Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

Výchozí typ parametru vazby rozhraní .NET `ActivityTriggerAttribute` je `IDurableActivityContext` . Nicméně triggery aktivit rozhraní .NET také podporují vazbu přímo na typy serializace JSON (včetně primitivních typů), takže stejnou funkci lze zjednodušit takto:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-20-only"></a>JavaScript (pouze funkce 2,0)

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

Pokud používáte aplikaci Visual Studio, můžete vytvořit propojení s klientem Orchestration pomocí `OrchestrationClientAttribute` atributu .NET pro Durable Functions 1,0. Počínaje Durable Functions 2,0 můžete vytvořit propojení s klientem Orchestration pomocí `DurableClientAttribute` atributu .NET.

Pokud pro vývoj používáte skriptovací jazyky (například soubory *. csx* nebo *. js* ), aktivační událost Orchestration je definována následujícím objektem JSON v `bindings` poli *function.jsna*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub` – Používá se ve scénářích, kdy více aplikací Function App sdílí stejný účet úložiště, ale musí být izolované od sebe navzájem. Pokud není zadaný, použije se výchozí hodnota z `host.json` . Tato hodnota musí odpovídat hodnotě používané cílovými funkcemi nástroje Orchestrator.
* `connectionName` – Název nastavení aplikace, které obsahuje připojovací řetězec účtu úložiště. Účet úložiště reprezentovaný tímto připojovacím řetězcem musí být stejný jako ten, který používá cílové funkce nástroje Orchestrator. Pokud tento parametr nezadáte, použije se výchozí připojovací řetězec účtu úložiště pro aplikaci Function App.

> [!NOTE]
> Ve většině případů doporučujeme tyto vlastnosti vynechat a spoléhat se na výchozí chování.

### <a name="client-usage"></a>Využití klienta

Ve funkcích .NET se obvykle vytváří vazba na `IDurableOrchestrationClient` , která poskytuje úplný přístup ke všem klientským rozhraním API, která podporuje Durable Functions. Ve starších verzích Durable Functions 2. x se místo toho svážete s `DurableOrchestrationClient` třídou. V jazyce JavaScript jsou stejná rozhraní API vystavena objektem vráceným z `getClient` . Mezi rozhraní API u objektu klienta patří:

* `StartNewAsync`
* `GetStatusAsync`
* `TerminateAsync`
* `RaiseEventAsync`
* `PurgeInstanceHistoryAsync`
* `CreateCheckStatusResponse`
* `CreateHttpManagementPayload`

Alternativně mohou funkce .NET navazovat vazby na `IAsyncCollector<T>` Where `T` je `StartOrchestrationArgs` nebo `JObject` .

Další informace o těchto operacích najdete v `IDurableOrchestrationClient` dokumentaci k rozhraní API.

### <a name="client-sample-visual-studio-development"></a>Ukázka klienta (vývoj v aplikaci Visual Studio)

Tady je příklad funkce aktivované frontou, která spouští orchestraci HelloWorld.

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> Předchozí kód jazyka C# je pro Durable Functions 2. x. Pro Durable Functions 1. x je nutné použít `OrchestrationClient` atribut namísto `DurableClient` atributu a místo toho musíte použít `DurableOrchestrationClient` typ parametru `IDurableOrchestrationClient` . Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

### <a name="client-sample-not-visual-studio"></a>Ukázka klienta (ne Visual Studio)

Pokud Visual Studio nepoužíváte pro vývoj, můžete v souboru vytvořit následující *function.js* . Tento příklad ukazuje, jak nakonfigurovat funkci spuštěnou ve frontě, která používá vazbu klienta trvalé orchestrace:

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
      "type": "durableClient",
      "direction": "in"
    }
  ]
}
```

> [!NOTE]
> Předchozí kód JSON je pro Durable Functions 2. x. Pro Durable Functions 1. x je nutné použít `orchestrationClient` místo `durableClient` typu triggeru. Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

Níže jsou uvedené ukázky pro konkrétní jazyk, které spouštějí nové instance funkcí nástroje Orchestrator.

#### <a name="c-script-sample"></a>Ukázka skriptu C#

Následující příklad ukazuje, jak použít vazbu klienta trvalé orchestrace ke spuštění nové instance funkce z funkce jazyka C# aktivované ve frontě:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

using Microsoft.Azure.WebJobs.Extensions.DurableTask;

public static Task Run(string input, IDurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> Předchozí kód je pro Durable Functions 2. x. Pro Durable Functions 1. x je nutné použít `DurableOrchestrationClient` typ parametru místo `IDurableOrchestrationClient` . Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

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

Když použijete nástroje sady Visual Studio pro Azure Functions, je aktivační událost entity nakonfigurovaná pomocí `EntityTriggerAttribute` atributu .NET.

> [!NOTE]
> Triggery entit jsou k dispozici od Durable Functions 2. x.

Interně se tato triggerová vazba dotazuje řady front ve výchozím účtu úložiště pro aplikaci Function App. Tyto fronty jsou podrobnosti o interní implementaci tohoto rozšíření, což znamená, proč nejsou explicitně nakonfigurované ve vlastnostech vazby.

### <a name="trigger-behavior"></a>Chování aktivační události

Tady jsou některé poznámky k triggeru entit:

* **Jeden podproces**: jeden dispečerský podproces se používá ke zpracování operací konkrétní entity. Pokud je jedna entita souběžně odeslána více zpráv, operace budou zpracovávány po jednom čase.
* **Zpracování nezpracovatelných zpráv** – v aktivačních událostech entit se nepodporují žádné nepoškozené zprávy.
* **Viditelnost zprávy** – zprávy triggerů entit se odřadí do fronty a jsou pro konfigurovatelnou dobu neviditelné. Viditelnost těchto zpráv se automaticky obnoví, pokud je aplikace Function App spuštěná a v pořádku.
* **Návratové hodnoty** – funkce entity nepodporují návratové hodnoty. K dispozici jsou konkrétní rozhraní API, která lze použít k uložení stavu nebo předávat hodnoty zpět do orchestrace.

Všechny změny stavu provedené v entitě během jejího spuštění budou automaticky uchovány po dokončení spuštění.

### <a name="trigger-usage-net"></a>Využití triggeru (.NET)

Každá funkce entity má typ parametru `IDurableEntityContext` , který má následující členy:

* **EntityName**: název aktuálně spuštěné entity.
* **EntityKey**: klíč aktuálně vykonávané entity.
* **EntityId**: ID aktuálně vykonávané entity.
* **OperationName**: název aktuální operace.
* **HasState**: zda entita existuje, to znamená, že má nějaký stav. 
* **GetState \<TState> ()**: Získá aktuální stav entity. Pokud ještě neexistuje, vytvoří se a inicializuje na `default<TState>` . `TState`Parametr musí být primitivního typu nebo typu serializace JSON. 
* **GetState \<TState> (initfunction)**: Získá aktuální stav entity. Pokud ještě neexistuje, vytvoří se voláním poskytnutého `initfunction` parametru. `TState`Parametr musí být primitivního typu nebo typu serializace JSON. 
* **Setstate (ARG)**: vytvoří nebo aktualizuje stav entity. `arg`Parametr musí být objekt nebo primitivum s serializací JSON.
* **DeleteState ()**: odstraní stav entity. 
* **GetInput \<TInput> ()**: Získá vstup pro aktuální operaci. `TInput`Parametr typu musí být primitivního typu nebo typu JSON, který lze serializovat.
* **Return (ARG)**: vrátí hodnotu do orchestrace, která volala operaci. `arg`Parametr musí být primitivní objekt nebo objekt JSON, který lze serializovat.
* **SignalEntity (EntityId, scheduledTimeUtc, operace, vstup)**: pošle jednosměrnou zprávu entitě. `operation`Parametr musí být řetězec, který není null, nepovinné `scheduledTimeUtc` musí být hodnota DateTime UTC, pro kterou má být vyvolána operace, a `input` parametr musí být primitivní objekt nebo objekt JSON, který lze serializovat.
* **CreateNewOrchestration (orchestratorFunctionName, Input)**: spustí novou orchestraci. `input`Parametr musí být primitivní objekt nebo objekt JSON, který lze serializovat.

`IDurableEntityContext`Objekt předaný do funkce entity lze použít pomocí `Entity.Current` vlastnosti Async-Local. Tento přístup je vhodný při použití programovacího modelu založeného na třídě.

### <a name="trigger-sample-c-function-based-syntax"></a>Ukázka triggeru (syntaxe založená na funkcích C#)

Následující kód je příkladem jednoduché entity *čítače* implementované jako trvalá funkce. Tato funkce definuje tři operace, `add` , `reset` a `get` , každý z nich pracuje na celočíselném stavu.

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

### <a name="trigger-sample-c-class-based-syntax"></a>Ukázka triggeru (syntaxe založená na třídě C#)

Následující příklad je ekvivalentní implementace `Counter` entity pomocí tříd a metod.

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

Stav této entity je objekt typu `Counter` , který obsahuje pole, které ukládá aktuální hodnotu čítače. Chcete-li zachovat tento objekt v úložišti, je serializován a rekonstruován knihovnou [JSON.NET](https://www.newtonsoft.com/json) . 

Další informace o syntaxi založené na třídě a způsobu jejich použití naleznete v tématu [definování tříd entit](durable-functions-dotnet-entities.md#defining-entity-classes).

> [!NOTE]
> Metoda vstupního bodu funkce s `[FunctionName]` atributem *musí* být deklarována `static` při použití tříd entit. Nestatické metody vstupního bodu mohou způsobit inicializaci více objektů a potenciálně jiné nedefinované chování.

Třídy entit mají zvláštní mechanismy pro interakci s vazbami a vkládání závislostí .NET. Další informace najdete v tématu [vytváření entit](durable-functions-dotnet-entities.md#entity-construction).

### <a name="trigger-sample-javascript"></a>Ukázka triggeru (JavaScript)

Následující kód je příkladem jednoduché entity *čítače* implementované jako trvalá funkce napsaná v JavaScriptu. Tato funkce definuje tři operace, `add` , `reset` a `get` , každý z nich pracuje na celočíselném stavu.

**function.json**
```json
{
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

**index.js**
```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```

> [!NOTE]
> Trvalé entity jsou k dispozici v JavaScriptu počínaje verzí **1.3.0** `durable-functions` balíčku npm.

## <a name="entity-client"></a>Klient entit

Vazba klienta entit umožňuje asynchronně aktivovat [funkce entit](#entity-trigger). Tyto funkce jsou někdy označovány jako [klientské funkce](durable-functions-types-features-overview.md#client-functions).

Pokud používáte aplikaci Visual Studio, můžete vytvořit propojení s klientem entity pomocí `DurableClientAttribute` atributu .NET.

> [!NOTE]
> `[DurableClientAttribute]`Lze také použít pro vytvoření vazby na [klienta Orchestration](#orchestration-client).

Pokud pro vývoj používáte skriptovací jazyky (například soubory *. csx* nebo *. js* ), aktivační událost entity je definována následujícím objektem JSON v `bindings` poli *function.jsv*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "in"
}
```

* `taskHub` – Používá se ve scénářích, kdy více aplikací Function App sdílí stejný účet úložiště, ale musí být izolované od sebe navzájem. Pokud není zadaný, použije se výchozí hodnota z `host.json` . Tato hodnota musí odpovídat hodnotě používané funkcemi cílové entity.
* `connectionName` – Název nastavení aplikace, které obsahuje připojovací řetězec účtu úložiště. Účet úložiště reprezentovaný tímto připojovacím řetězcem musí být stejný, jaký používá funkce cílové entity. Pokud tento parametr nezadáte, použije se výchozí připojovací řetězec účtu úložiště pro aplikaci Function App.

> [!NOTE]
> Ve většině případů doporučujeme vynechat volitelné vlastnosti a spoléhat se na výchozí chování.

### <a name="entity-client-usage"></a>Využití klienta entit

Ve funkcích .NET se obvykle vytváří vazba na `IDurableEntityClient` , která poskytuje úplný přístup ke všem klientským rozhraním API podporovaným trvalými entitami. Můžete také vytvořit `IDurableOrchestrationClient` připojení k rozhraní, které poskytuje přístup k klientským rozhraním API pro entity i orchestrace. Mezi rozhraní API u objektu klienta patří:

* **ReadEntityStateAsync \<T>**: přečte stav entity. Vrátí odpověď, která označuje, zda cílová entita existuje, a pokud ano, jaký je její stav.
* **SignalEntityAsync**: pošle jednosměrnou zprávu entitě a počká, až se zazařazuje do fronty.
* **ListEntitiesAsync**: dotazování na stav více entit. Do entit se dá zadat dotaz podle *názvu* a *času poslední operace*.

Před odesláním signálu není nutné vytvořit cílovou entitu – stav entity lze vytvořit v rámci funkce entity, která zpracovává signál.

> [!NOTE]
> Je důležité si uvědomit, že "signály" odesílané z klienta jsou jednoduše zařazené do fronty, aby je bylo možné zpracovat asynchronně později. Konkrétně se `SignalEntityAsync` obvykle vrací před tím, než entita spustí operaci, a není možné vrátit návratovou hodnotu nebo pozorovat výjimky. Pokud jsou vyžadovány silnější záruky (např. pro pracovní postupy), měly by být použity *funkce nástroje Orchestrator* , které mohou čekat na dokončení operací entit a mohou zpracovávat návratové hodnoty a sledovat výjimky.

### <a name="example-client-signals-entity-directly---c"></a>Příklad: klient signalizuje entitu přímo-C. #

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

### <a name="example-client-signals-entity-via-interface---c"></a>Příklad: klient signalizuje entitu prostřednictvím rozhraní-C. #

Pokud je to možné, doporučujeme přistoupit [k entitám prostřednictvím rozhraní](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) , protože poskytuje další kontrolu typu. Předpokládejme například, že `Counter` entita zmíněná dříve implementovala `ICounter` rozhraní, které je definováno následujícím způsobem:

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

Klientský kód pak může použít `SignalEntityAsync<ICounter>` k vygenerování proxy typu s bezpečným typem:

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

`proxy`Parametr je dynamicky generovaná instance `ICounter` , která interně překládá volání `Add` do do ekvivalentního (netypového) volání `SignalEntityAsync` .

> [!NOTE]
> `SignalEntityAsync`Rozhraní API označují jednosměrné operace. Pokud rozhraní entit vrátí `Task<T>` , bude `T` mít hodnota parametru vždycky hodnotu null nebo `default` .

Konkrétně není vhodné signalizovat `Get` operaci, protože není vrácena žádná hodnota. Místo toho mohou klienti použít buď `ReadStateAsync` pro přístup ke stavu čítače přímo, nebo mohou spustit funkci Orchestrator, která volá `Get` operaci.

### <a name="example-client-signals-entity---javascript"></a>Příklad: klient signalizuje jazykovou entitu – JavaScript

Tady je příklad funkce aktivované frontou, která signalizuje entitu "čítač" v JavaScriptu.

**function.json**
```json
{
    "bindings": [
      {
        "name": "input",
        "type": "queueTrigger",
        "queueName": "durable-entity-trigger",
        "direction": "in",
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

**index.js**
```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

> [!NOTE]
> Trvalé entity jsou k dispozici v JavaScriptu počínaje verzí **1.3.0** `durable-functions` balíčku npm.

<a name="host-json"></a>
## <a name="hostjson-settings"></a>host.jsnastavení

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Reference k integrovanému HTTP API pro správu instancí](durable-functions-http-api.md)
