---
title: Vazby pro trvalé funkce – Azure
description: Jak používat aktivační události a vazby pro rozšíření trvalé funkce pro funkce Azure.
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 1f42c6c9b0086d49e539040334c83cfc0c6feb42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278217"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Vazby pro trvalé funkce (funkce Azure)

[Rozšíření trvalé funkce](durable-functions-overview.md) zavádí dvě nové aktivační vazby, které řídí provádění orchestrator a funkce aktivity. Také zavádí výstupní vazbu, která funguje jako klient pro dobu runtime trvalé funkce.

## <a name="orchestration-trigger"></a>Aktivační událost orchestrace

Aktivační událost orchestrace umožňuje vytvářet [trvalé funkce orchestratoru](durable-functions-types-features-overview.md#orchestrator-functions). Tato aktivační událost podporuje spuštění nové instance funkce orchestrator a obnovení existujících instancí funkce orchestrator, které "čekají" na úlohu.

Při použití nástrojů Sady Visual Studio pro funkce Azure, aktivační událost orchestrace je nakonfigurován pomocí [OrchestrationTriggerAttribute](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.OrchestrationTriggerAttribute?view=azure-dotnet) .NET atribut.

Při psaní funkcí orchestratoru ve skriptovacích jazycích (například skriptování JavaScript nebo C#) je aktivační `bindings` událost orchestrace definována následujícím objektem JSON v poli souboru *function.json:*

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration`je název orchestrace, kterou musí klienti použít, když chtějí spustit nové instance této funkce orchestratoru. Tato vlastnost je nepovinná. Pokud není zadán, bude použit název funkce.

Interně tato aktivační událost vazby dotazování řady front ve výchozím účtu úložiště pro aplikaci funkce. Tyto fronty jsou podrobnosti interní implementace rozšíření, což je důvod, proč nejsou explicitně nakonfigurovány ve vlastnostech vazby.

### <a name="trigger-behavior"></a>Chování aktivační události

Tady jsou některé poznámky o aktivační události orchestrace:

* **Jednovláknové** - jeden dispečer vlákno se používá pro všechny spuštění funkce orchestrator na jedné instanci hostitele. Z tohoto důvodu je důležité zajistit, aby kód funkce orchestrator byl efektivní a neprováděl žádné vstupně-va. Je také důležité zajistit, že toto vlákno neprovádí žádnou asynchronní práci s výjimkou čekání na typy úkolů specifické pro trvalé funkce.
* **Zpracování poškozená zpráva** – neexistuje žádná podpora poškozená zpráva v orchestraci aktivačních událostí.
* **Viditelnost zprávy** – zprávy aktivační události orchestrace jsou vyřazeny z fronty a po konfigurovatelnou dobu trvání neviditelné. Viditelnost těchto zpráv se automaticky obnoví, pokud je aplikace funkce spuštěná a v pořádku.
* **Návratové hodnoty** – vrácené hodnoty jsou serializovány do JSON a trvalé do tabulky historie orchestrace v úložišti Azure Table. Tyto vrácené hodnoty mohou být dotazovány vazbou klienta orchestrace, popsané později.

> [!WARNING]
> Funkce Orchestrator by nikdy neměly používat žádné vstupní nebo výstupní vazby než vazbu aktivační události orchestrace. Přitom má potenciál způsobit problémy s rozšíření trvanlivé úlohy, protože tyto vazby nemusí dodržovat pravidla jednoho vlákna a vstupně-v.I/V. Pokud chcete použít jiné vazby, přidejte je do funkce Aktivita volané z funkce Orchestrator.

> [!WARNING]
> Funkce orchestrátoru JavaScriptu `async`by nikdy neměly být deklarovány .

### <a name="trigger-usage-net"></a>Využití aktivační události (.NET)

Vazba aktivační události orchestrace podporuje vstupy i výstupy. Zde je několik věcí, které je třeba vědět o zpracování vstupů a výstupů:

* **vstupy** - funkce orchestrace `DurableOrchestrationContext` .NET podporují pouze jako typ parametru. Deserializace vstupů přímo v podpisu funkce není podporována. Kód musí `GetInput<T>` použít metodu `getInput` (.NET) nebo (JavaScript) k načtení vstupů funkcí orchestrator. Tyto vstupy musí být JSON serializovatelné typy.
* **výstupy** - Orchestrace spouští podporu výstupních hodnot, stejně jako vstupy. Vrácená hodnota funkce se používá k přiřazení výstupní hodnoty a musí být JSON serializovatelný. Pokud funkce .NET `Task` `void`vrátí `null` nebo , hodnota bude uložena jako výstup.

### <a name="trigger-sample"></a>Ukázka aktivační události

Následující ukázkový kód ukazuje, jak může vypadat nejjednodušší funkce orchestrátoru "Hello World":

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
> Předchozí kód je pro trvalé funkce 2.x. Pro trvalé funkce 1.x, `DurableOrchestrationContext` musíte `IDurableOrchestrationContext`použít místo . Další informace o rozdílech mezi verzemi naleznete v článku [Verze trvanlivých funkcí.](durable-functions-versions.md)

#### <a name="javascript-functions-20-only"></a>JavaScript (pouze funkce 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> Objekt `context` v jazyce JavaScript nepředstavuje DurableOrchestrationContext, ale [kontext funkce jako celek](../functions-reference-node.md#context-object). K metodám orchestrace `context` můžete přistupovat prostřednictvím `df` vlastnosti objektu.

> [!NOTE]
> JavaScript orchestrátory `return`by měly používat . Knihovna se `durable-functions` postará `context.done` o volání metody.

Většina funkcí orchestrator funkce volání aktivity, takže zde je "Hello World" příklad, který ukazuje, jak volat funkce aktivity:

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
> Předchozí kód je pro trvalé funkce 2.x. Pro trvalé funkce 1.x, `DurableOrchestrationContext` musíte `IDurableOrchestrationContext`použít místo . Další informace o rozdílech mezi verzemi naleznete v článku [verze durable functions.](durable-functions-versions.md)

#### <a name="javascript-functions-20-only"></a>JavaScript (pouze funkce 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-trigger"></a>Aktivační událost aktivity

Aktivační událost aktivity umožňuje vytvářet funkce, které jsou volány funkcemi orchestratoru, známé jako [funkce aktivity](durable-functions-types-features-overview.md#activity-functions).

Pokud používáte Visual Studio, aktivační událost aktivity je nakonfigurována pomocí atributu `ActivityTriggerAttribute` .NET.

Pokud používáte Kód VS nebo portál Azure pro vývoj, aktivační událost aktivity je `bindings` definována následujícím objektem JSON v poli *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity`je název aktivity. Tato hodnota je název, který orchestrátor funkce použít k vyvolání této funkce aktivity. Tato vlastnost je nepovinná. Pokud není zadán, bude použit název funkce.

Interně tato aktivační událost vazby dotazování fronty ve výchozím účtu úložiště pro aplikaci funkce. Tato fronta je podrobnosti vnitřní implementace rozšíření, což je důvod, proč není explicitně nakonfigurován ve vlastnostech vazby.

### <a name="trigger-behavior"></a>Chování aktivační události

Zde jsou některé poznámky o aktivační události aktivity:

* **Zřetězení** – na rozdíl od aktivační události orchestrace aktivační události aktivity nemají žádná omezení kolem zřetězení nebo vstupně-out. Mohou být považovány za běžné funkce.
* **Zpracování poškozená zpráva** - neexistuje žádná podpora poškozená zpráva v aktivační události aktivity.
* **Viditelnost zprávy** – zprávy aktivační události aktivity jsou dequeued a udržovány neviditelné po konfigurovatelnou dobu trvání. Viditelnost těchto zpráv se automaticky obnoví, pokud je aplikace funkce spuštěná a v pořádku.
* **Návratové hodnoty** – vrácené hodnoty jsou serializovány do JSON a trvalé do tabulky historie orchestrace v úložišti Azure Table.

> [!WARNING]
> Back-end úložiště pro funkce aktivity je podrobnosti implementace a uživatelský kód by neměl pracovat s těmito entitami úložiště přímo.

### <a name="trigger-usage-net"></a>Využití aktivační události (.NET)

Vazby aktivační událost aktivity podporuje vstupy i výstupy, stejně jako aktivační událost orchestrace. Zde je několik věcí, které je třeba vědět o zpracování vstupů a výstupů:

* **inputs** - .NET activity `DurableActivityContext` functions natively use as a parameter type. Alternativně funkce aktivity může být deklarována s libovolným typem parametru, který je JSON serializovatelný. Při použití `DurableActivityContext`můžete volat `GetInput<T>` načíst a rekonstruovat vstup funkce aktivity.
* **výstupy** - Funkce aktivity podporují výstupní hodnoty i vstupy. Vrácená hodnota funkce se používá k přiřazení výstupní hodnoty a musí být JSON serializovatelný. Pokud funkce .NET `Task` `void`vrátí `null` nebo , hodnota bude uložena jako výstup.
* **metadata** - funkce aktivity .NET se mohou vázat na `string instanceId` parametr a získat ID instance nadřazené orchestrace.

### <a name="trigger-sample"></a>Ukázka aktivační události

Následující ukázkový kód ukazuje, jak může vypadat jednoduchá funkce aktivity "Hello World":

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
> Předchozí kód je pro trvalé funkce 2.x. Pro trvalé funkce 1.x, `DurableActivityContext` musíte `IDurableActivityContext`použít místo . Další informace o rozdílech mezi verzemi naleznete v článku [Verze trvanlivých funkcí.](durable-functions-versions.md)

Výchozí typ parametru pro `ActivityTriggerAttribute` vazbu .NET je `IDurableActivityContext`. Aktivační události aktivity .NET však také podporují vazbu přímo na typy JSON serializovatelné (včetně primitivních typů), takže stejná funkce může být zjednodušena následujícím způsobem:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-20-only"></a>JavaScript (pouze funkce 2.0)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

JavaScript vazby mohou být také předány jako další parametry, takže stejná funkce může být zjednodušena takto:

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```


### <a name="using-input-and-output-bindings"></a>Použití vstupních a výstupních vazeb

Kromě vazby aktivační události aktivity můžete použít také běžné vstupní a výstupní vazby. Můžete například převzít vstup do vazby aktivity a odeslat zprávu na EventHub pomocí vazby výstupu EventHub:

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

## <a name="orchestration-client"></a>Klient orchestrace

Vazba klienta orchestrace umožňuje psát funkce, které interagují s funkcemi orchestratoru. Tyto funkce jsou někdy označovány jako [klientské funkce](durable-functions-types-features-overview.md#client-functions). Můžete například na instanci orchestrace působit následujícími způsoby:

* Začněte s nimi.
* Dotaz na jejich stav.
* Zlikvidujte je.
* Posílejte události, když běží.
* Vymazat historii instancí.

Pokud používáte Visual Studio, můžete vytvořit vazbu na `OrchestrationClientAttribute` klienta orchestrace pomocí atributu .NET pro trvalé funkce 1.0. Počínaje trvanlivé funkce 2.0, můžete vytvořit vazbu na klienta orchestrace pomocí atributu `DurableClientAttribute` .NET.

Pokud používáte skriptovací jazyky (například soubory *.csx* nebo *JS)* pro vývoj, aktivační událost orchestrace `bindings` je definována následujícím objektem JSON v poli *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub`- Používá se ve scénářích, kde vícefunkčních aplikací sdílí stejný účet úložiště, ale musí být izolovány od sebe navzájem. Pokud není zadán, použije `host.json` se výchozí hodnota z. Tato hodnota musí odpovídat hodnotě používané funkcemi cílového orchestrátoru.
* `connectionName`- Název nastavení aplikace, která obsahuje připojovací řetězec účtu úložiště. Účet úložiště reprezentované tímto připojovacím řetězcem musí být stejný jako ten, který používají funkce cílového orchestrátoru. Pokud není zadán, použije se výchozí připojovací řetězec účtu úložiště pro aplikaci funkce.

> [!NOTE]
> Ve většině případů doporučujeme vynechat tyto vlastnosti a spoléhat se na výchozí chování.

### <a name="client-usage"></a>Využití klienta

Ve funkcích rozhraní .NET `IDurableOrchestrationClient`se obvykle vážete na , což umožňuje úplný přístup ke všem rozhraním API klienta orchestrace podporovaným trvalými funkcemi. Ve starších verzích Durable Functions 2.x `DurableOrchestrationClient` se místo toho svážete se třídou. V jazyce JavaScript jsou stejná api `getClient`vystavena objektu vráceného z . Mezi api klientského objektu patří:

* `StartNewAsync`
* `GetStatusAsync`
* `TerminateAsync`
* `RaiseEventAsync`
* `PurgeInstanceHistoryAsync`
* `CreateCheckStatusResponse`
* `CreateHttpManagementPayload`

Alternativně mohou být funkce `IAsyncCollector<T>` .NET `StartOrchestrationArgs` `JObject`vázány na místo, kde `T` je nebo .

Další informace o těchto operacích naleznete v dokumentaci k `IDurableOrchestrationClient` rozhraní API.

### <a name="client-sample-visual-studio-development"></a>Ukázka klienta (vývoj sady Visual Studio)

Zde je příklad fronty aktivované funkce, která spustí orchestraci "HelloWorld".

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
> Předchozí kód Jazyka C# je pro durable functions 2.x. Pro trvalé funkce 1.x `OrchestrationClient` je nutné `DurableClient` použít atribut namísto `DurableOrchestrationClient` atributu a `IDurableOrchestrationClient`místo . Další informace o rozdílech mezi verzemi naleznete v článku [Verze trvanlivých funkcí.](durable-functions-versions.md)

### <a name="client-sample-not-visual-studio"></a>Ukázka klienta (ne Visual Studio)

Pokud nepoužíváte Visual Studio pro vývoj, můžete vytvořit následující *soubor function.json.* Tento příklad ukazuje, jak nakonfigurovat funkci aktivovanou frontou, která používá vazbu trvalého klienta orchestrace:

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
> Předchozí JSON je pro trvanlivé funkce 2.x. Pro trvalé funkce 1.x, `orchestrationClient` musíte `durableClient` použít místo jako typ aktivační události. Další informace o rozdílech mezi verzemi naleznete v článku [Verze trvanlivých funkcí.](durable-functions-versions.md)

Následují ukázky specifické pro jazyk, které spouštějí nové instance funkcí orchestratoru.

#### <a name="c-script-sample"></a>Ukázka skriptu jazyka C#

Následující ukázka ukazuje, jak použít vazbu trvalého klienta orchestrace ke spuštění nové instance funkce z funkce C# spouštěné frontou:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

using Microsoft.Azure.WebJobs.Extensions.DurableTask;

public static Task Run(string input, IDurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> Předchozí kód je pro trvalé funkce 2.x. Pro trvalé funkce 1.x, `DurableOrchestrationClient` musíte použít `IDurableOrchestrationClient`typ parametru namísto . Další informace o rozdílech mezi verzemi naleznete v článku [Verze trvanlivých funkcí.](durable-functions-versions.md)

#### <a name="javascript-sample"></a>Ukázka javascriptu

Následující ukázka ukazuje, jak použít vazbu trvalého klienta orchestrace ke spuštění nové instance funkce z funkce JavaScript:

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

Další podrobnosti o počátečních instancích naleznete ve [správě instancí](durable-functions-instance-management.md).

## <a name="entity-trigger"></a>Aktivační událost entity

Aktivační události entit umožňují vytvářet [funkce entity](durable-functions-entities.md). Tato aktivační událost podporuje zpracování událostí pro konkrétní instanci entity.

Při použití nástrojů Visual Studio pro funkce Azure, aktivační `EntityTriggerAttribute` událost entity je nakonfigurován pomocí atributu .NET.

> [!NOTE]
> Aktivační události entit jsou k dispozici počínaje trvalé funkce 2.x.

Interně tato aktivační událost vazby dotazování řady front ve výchozím účtu úložiště pro aplikaci funkce. Tyto fronty jsou podrobnosti interní implementace rozšíření, což je důvod, proč nejsou explicitně nakonfigurovány ve vlastnostech vazby.

### <a name="trigger-behavior"></a>Chování aktivační události

Zde jsou některé poznámky o aktivační události entity:

* **Jednovláknové**: Ke zpracování operací pro určitou entitu se používá jedno dispečerské vlákno. Pokud je současně odesláno více zpráv jedné entitě, operace budou zpracovány jednotlivě.
* **Zpracování poškozená zpráva** – neexistuje žádná podpora poškozená zpráva v entita aktivační události.
* **Viditelnost zprávy** – aktivační zprávy entity jsou dequeued a udržovány neviditelné po konfigurovatelnou dobu trvání. Viditelnost těchto zpráv se automaticky obnoví, pokud je aplikace funkce spuštěná a v pořádku.
* **Vrácené hodnoty** - Entity funkce nepodporují vrácené hodnoty. Existují konkrétní api, která lze použít k uložení stavu nebo předat hodnoty zpět do orchestrations.

Všechny změny stavu provedené v entitě během jejího provádění budou automaticky zachovány po dokončení provádění.

### <a name="trigger-usage-net"></a>Využití aktivační události (.NET)

Každá funkce entity má `IDurableEntityContext`typ parametru , který má následující členy:

* **EntityName**: název aktuálně spuštěné entity.
* **EntityKey**: klíč aktuálně spuštěné entity.
* **EntityId**: ID aktuálně vykonávající entity.
* **OperationName**: název aktuální operace.
* **HasState**: zda entita existuje, to znamená, že má nějaký stav. 
* **GetState\<TState>()**: získá aktuální stav entity. Pokud ještě neexistuje, je vytvořen a inicializován . `default<TState>` Parametr `TState` musí být primitivní nebo JSON serializovatelný typ. 
* **GetState\<TState>(initfunction)**: získá aktuální stav entity. Pokud ještě neexistuje, je vytvořen voláním zadaný `initfunction` parametr. Parametr `TState` musí být primitivní nebo JSON serializovatelný typ. 
* **SetState(arg)**: vytvoří nebo aktualizuje stav entity. Parametr `arg` musí být JSON serializovatelný objekt nebo primitivní.
* **DeleteState()**: odstraní stav entity. 
* **GetInput\<TInput>()**: získá vstup pro aktuální operaci. Parametr `TInput` typu musí být primitivní nebo JSON serializovatelný typ.
* **Return(arg)**: vrátí hodnotu orchestraci, která volala operaci. Parametr `arg` musí být primitivní nebo JSON serializovatelný objekt.
* **SignalEntity(EntityId, scheduledTimeUtc, operace, vstup)**: odešle jednosměrnou zprávu entitě. Parametr `operation` musí být non-null řetězec, `scheduledTimeUtc` volitelné musí být čas data UTC, kdy `input` chcete vyvolat operaci a parametr musí být primitivní nebo JSON serializovatelný objekt.
* **CreateNewOrchestration(orchestratorFunctionName, input)**: spustí novou orchestraci. Parametr `input` musí být primitivní nebo JSON serializovatelný objekt.

Objekt `IDurableEntityContext` předaný funkci entity lze přistupovat pomocí `Entity.Current` asynchronní místní vlastnosti. Tento přístup je vhodný při použití programovacího modelu založeného na třídě.

### <a name="trigger-sample-c-function-based-syntax"></a>Ukázka aktivační události (syntaxe založená na funkci Jazyka C#

Následující kód je příkladem jednoduché *entity Counter* implementované jako trvalá funkce. Tato funkce definuje tři `add` `reset`operace `get`, , a , z nichž každá pracuje ve stavu celého čísla.

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

Další informace o syntaxi založené na funkcích a o tom, jak ji používat, naleznete [v tématu Syntaxe založená na funkcích](durable-functions-dotnet-entities.md#function-based-syntax).

### <a name="trigger-sample-c-class-based-syntax"></a>Ukázka aktivační události (syntaxe založená na třídě Jazyka C#)

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

Stav této entity je objekt `Counter`typu , který obsahuje pole, které ukládá aktuální hodnotu čítače. Chcete-li zachovat tento objekt v úložišti, je serializován a reserializován [knihovnou Json.NET.](https://www.newtonsoft.com/json) 

Další informace o syntaxi založené na třídě a o tom, jak ji používat, naleznete [v tématu Definování tříd entit](durable-functions-dotnet-entities.md#defining-entity-classes).

> [!NOTE]
> Metoda vstupního bodu `[FunctionName]` funkce s `static` atributem *musí* být deklarována při použití tříd entit. Metody nestatického vstupního bodu mohou mít za následek více inicializace objektu a potenciálně jiné nedefinované chování.

Entity třídy mají speciální mechanismy pro interakci s vazby a .NET vkládání závislostí. Další informace naleznete [v tématu Konstrukce entity](durable-functions-dotnet-entities.md#entity-construction).

### <a name="trigger-sample-javascript"></a>Ukázka aktivační události (JavaScript)

Následující kód je příkladem jednoduché *entity Counter* implementované jako trvalá funkce napsaná v Jazyce JavaScript. Tato funkce definuje tři `add` `reset`operace `get`, , a , z nichž každá pracuje ve stavu celého čísla.

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
> Trvalé entity jsou k dispozici v Jazyce JavaScript `durable-functions` počínaje verzí **1.3.0** balíčku npm.

## <a name="entity-client"></a>Klient entity

Vazba klienta entity umožňuje asynchronně aktivovat [funkce entity](#entity-trigger). Tyto funkce jsou někdy označovány jako [klientské funkce](durable-functions-types-features-overview.md#client-functions).

Pokud používáte Visual Studio, můžete vytvořit vazbu na `DurableClientAttribute` klienta entity pomocí atributu .NET.

> [!NOTE]
> Lze `[DurableClientAttribute]` také vytvořit vazbu na [klienta orchestrace](#orchestration-client).

Pokud používáte skriptovací jazyky (například soubory *.csx* nebo *JS)* pro vývoj, aktivační událost entity `bindings` je definována následujícím objektem JSON v poli *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "in"
}
```

* `taskHub`- Používá se ve scénářích, kde vícefunkčních aplikací sdílí stejný účet úložiště, ale musí být izolovány od sebe navzájem. Pokud není zadán, použije `host.json` se výchozí hodnota z. Tato hodnota musí odpovídat hodnotě používané funkcemi cílové entity.
* `connectionName`- Název nastavení aplikace, která obsahuje připojovací řetězec účtu úložiště. Účet úložiště reprezentované tímto připojovacím řetězcem musí být stejný jako účet používaný funkcemi cílové entity. Pokud není zadán, použije se výchozí připojovací řetězec účtu úložiště pro aplikaci funkce.

> [!NOTE]
> Ve většině případů doporučujeme vynechat volitelné vlastnosti a spoléhat se na výchozí chování.

### <a name="entity-client-usage"></a>Využití klienta entity

Ve funkcích rozhraní .NET `IDurableEntityClient`se obvykle vážete na , což umožňuje úplný přístup ke všem klientským rozhraním API podporovaným trvalými entitami. Můžete také vytvořit `IDurableOrchestrationClient` vazbu na rozhraní, které poskytuje přístup k klientským rozhraním API pro entity i orchestrations. Mezi api klientského objektu patří:

* **ReadEntityStateAsync\<T>**: přečte stav entity. Vrátí odpověď, která označuje, zda cílová entita existuje, a pokud ano, jaký je její stav.
* **SignalEntityAsync**: odešle jednosměrnou zprávu entitě a čeká na zařazení do fronty.
* **ListEntitiesAsync**: dotazy na stav více entit. Entity mohou být dotazovány podle *názvu* a *času poslední operace*.

Před odesláním signálu není nutné vytvořit cílovou entitu – stav entity lze vytvořit z funkce entity, která zpracovává signál.

> [!NOTE]
> Je důležité si uvědomit, že "signály" odeslané od klienta jsou jednoduše zařazeny do fronty, které mají být zpracovány asynchronně později. Zejména `SignalEntityAsync` obvykle vrátí před entita dokonce spustí operaci a není možné získat zpět vrácenou hodnotu nebo dodržovat výjimky. Pokud jsou požadovány silnější záruky (např. pro pracovní postupy), měly by být použity *funkce orchestrator,* které mohou čekat na dokončení operací entity a mohou zpracovávat vrácené hodnoty a sledovat výjimky.

### <a name="example-client-signals-entity-directly---c"></a>Příklad: entita signálů klienta přímo - C #

Zde je příklad funkce spouštěné frontou, která vyvolá entitu Čítač.

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

### <a name="example-client-signals-entity-via-interface---c"></a>Příklad: entita signálů klienta přes rozhraní - C #

Pokud je to možné, doporučujeme [přístup k entitám prostřednictvím rozhraní,](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) protože poskytuje další kontrolu typů. Předpokládejme například, že `Counter` dříve `ICounter` uvedená entita implementovala rozhraní definované takto:

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

Kód klienta `SignalEntityAsync<ICounter>` pak můžete použít ke generování proxy typu bezpečné:

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

Parametr `proxy` je dynamicky generovaná `ICounter`instance , která interně `Add` převádí volání do `SignalEntityAsync`ekvivalentního (netypového) volání .

> [!NOTE]
> Api `SignalEntityAsync` představují jednosměrné operace. Pokud entity rozhraní `Task<T>`vrátí , hodnota `T` parametru bude `default`vždy null nebo .

Zejména nemá smysl signalizovat `Get` operaci, protože není vrácena žádná hodnota. Místo toho klienti `ReadStateAsync` mohou použít buď pro přístup ke stavu čítače `Get` přímo, nebo můžete spustit funkci orchestrator, která volá operaci.

### <a name="example-client-signals-entity---javascript"></a>Příklad: entita signálů klienta - JavaScript

Zde je příklad funkce spouštěné frontou, která signalizuje entitu "Counter" v JavaScriptu.

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
> Trvalé entity jsou k dispozici v Jazyce JavaScript `durable-functions` počínaje verzí **1.3.0** balíčku npm.

<a name="host-json"></a>
## <a name="hostjson-settings"></a>nastavení host.json

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Integrovaný odkaz http api pro správu instancí](durable-functions-http-api.md)
