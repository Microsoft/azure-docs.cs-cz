---
title: Trvalé entity – Azure Functions
description: Seznamte se s trvalými entitami a jejich použitím v rozšíření Durable Functions pro Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: overview
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 1a9ad16d6ecd7e75848ca1ea56e6238ee3b29c2d
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614950"
---
# <a name="entity-functions"></a>Funkce entit

Funkce entit definují operace pro čtení a aktualizaci malých částí stavu, označovaných jako *odolné entity*. Podobně jako funkce nástroje Orchestrator jsou funkce entit funkce se speciálním typem triggeru, *triggerem entity*. Na rozdíl od funkcí Orchestrator nástroj entity Functions spravuje stav entity explicitně, spíše než implicitně představující stav prostřednictvím toku řízení.
Entity poskytují prostředky pro horizontální navýšení kapacity aplikací tím, že distribuují práci napříč mnoha entitami, každý s mírným stavem.

> [!NOTE]
> Funkce entit a související funkce jsou dostupné jenom v Durable Functions 2,0 a novějších.

## <a name="general-concepts"></a>Obecné koncepty

Entity se chovají jako malé služby, které komunikují prostřednictvím zpráv. Každá entita má jedinečnou identitu a vnitřní stav (pokud existuje). Podobně jako služby a objekty, entity provádějí operace po zobrazení výzvy. Když se spustí, může operace aktualizovat vnitřní stav entity. Může také volat externí služby a čekat na odpověď. Entity komunikují s jinými entitami, orchestrací a klienty pomocí zpráv, které jsou implicitně odesílány prostřednictvím spolehlivých front. 

Aby se zabránilo konfliktům, budou mít všechny operace na jedné entitě zaručené spouštění na základě sériového běhu, tj. jednoho po druhém. 

### <a name="entity-id"></a>ID entity
K entitám se dostanete pomocí jedinečného identifikátoru *ID entity*. ID entity je jednoduše dvojice řetězců, které jedinečně identifikují instanci entity. Skládá se z těchto:

* **Název entity**: název, který identifikuje typ entity (například "čítač"). Tento název se musí shodovat s názvem funkce entity, která implementuje entitu. Nerozlišuje se případ.
* **Klíč entity**: řetězec, který jedinečně identifikuje entitu mezi všemi ostatními entitami stejného názvu (například GUID).

Například funkce entita *čítače* může být použita k udržení skóre v online hře. Každá instance hry bude mít jedinečné ID entity, například `@Counter@Game1`, `@Counter@Game2`atd. Všechny operace, které cílí na konkrétní entitu, vyžadují zadání ID entity jako parametru.

### <a name="entity-operations"></a>Operace s entitami ###

Pokud chcete vyvolat operaci u entity, jedna určuje

* *ID entity* cílové entity
* *Název operace*, řetězec, který určuje operaci, která má být provedena. Například entita čítače může podporovat operace "Přidat", "získat" nebo "resetovat".
* *Vstup operace*, což je volitelný vstupní parametr pro operaci. Například operace "Přidat" může jako vstup převzít celočíselnou hodnotu.

Operace mohou vracet výslednou hodnotu nebo výsledek chyby (například Chyba JavaScriptu nebo výjimka rozhraní .NET). Tento výsledek nebo chybu mohou být pozorovány orchestrací, které volaly operaci.

Operace entity může také vytvořit, číst, aktualizovat a odstranit stav entity. Stav entity je vždy trvale trvalým úložištěm.

## <a name="defining-entities"></a>Definování entit

V současnosti nabízíme dvě různá rozhraní API pro definování entit.

**Syntaxe založená na funkcích** , kde jsou entity reprezentovány jako funkce a operace jsou explicitně odesílány aplikací. Tato syntaxe funguje dobře pro entity s jednoduchým stavem, malým počtem operací nebo dynamickou sadou operací (například v aplikačních architekturách). Nicméně může být zdlouhavé pro údržbu, protože nezachycuje chyby typu v době kompilace.

**Syntaxe založená na třídě** , kde jsou entity a operace reprezentovány třídami a metodami. Tato syntaxe vytváří snadněji čitelný kód a umožňuje, aby operace byly vyvolány způsobem bezpečným pro typ. Syntaxe založená na třídě je pouze tenká vrstva nad syntaxí založenou na funkci, takže obě varianty lze ve stejné aplikaci použít zaměnitelné.

### <a name="example-function-based-syntax---c"></a>Příklad: syntaxe založená na funkcích –C#

Následující kód je příkladem jednoduché entity *čítače* implementované jako trvalá funkce. Tato funkce definuje tři operace, `add`, `reset`a `get`, z nichž každá funguje v celočíselném stavu.

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

### <a name="example-class-based-syntax---c"></a>Příklad: syntaxe založená na třídě-C#

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

Stav této entity je objekt typu `Counter`, který obsahuje pole, které ukládá aktuální hodnotu čítače. Chcete-li zachovat tento objekt v úložišti, je serializován a rekonstruován knihovnou [JSON.NET](https://www.newtonsoft.com/json) . 

Další informace o syntaxi založené na třídě a způsobu jejich použití naleznete v tématu [definování tříd entit](durable-functions-dotnet-entities.md#defining-entity-classes).

### <a name="example-javascript-entity"></a>Příklad: entita JavaScript

Trvalé entity jsou k dispozici v JavaScriptu počínaje verzí **1.3.0** balíčku `durable-functions` npm. Následující kód je entita *čítače* implementovaná jako trvalá funkce napsaná v JavaScriptu.

**Function. JSON**
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

**index. js**
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

## <a name="accessing-entities"></a>Přístup k entitám

K entitám lze přistupovat pomocí jednosměrné nebo obousměrné komunikace. Následující terminologie používá k rozlišení: 

* **Volání** entity znamená, že používáme obousměrnou komunikaci (round-trip): pošleme zprávu o operaci entitě a potom před pokračováním vyčkejte na zprávu s odpovědí. Zpráva odpovědi může poskytnout výslednou hodnotu nebo výsledek chyby (například Chyba JavaScriptu nebo výjimka rozhraní .NET). Tento výsledek nebo chyba je následně pozorován volajícím.
* **Signalizace** entity znamená, že používáme jednosměrnou komunikaci (oheň a zapomenout): pošleme zprávu o operaci, ale nečeká na odpověď. I když je zaručeno doručení zprávy, odesílatel neví, kdy a nemůže sledovat žádnou hodnotu výsledku nebo chyby.

K entitám je možné přistupovat z funkcí klienta, z funkcí nástroje Orchestrator nebo z funkcí entity. Všechny typy komunikace nejsou podporovány všemi kontexty:

* V rámci klientů můžete *identifikovat* entity a můžete *si přečíst* stav entity.
* V rámci orchestrace můžete *identifikovat* entity a můžete *volat* entity.
* V rámci entit můžete *identifikovat* entity.

Níže uvádíme několik příkladů, které znázorňují různé způsoby přístupu k entitám.

> [!NOTE]
> V níže uvedených příkladech se pro přístup k entitám zobrazují následující příklady s volným typem. Obecně doporučujeme [přistupovat k entitám prostřednictvím rozhraní](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) , protože poskytuje další kontrolu typu.

### <a name="example-client-signals-an-entity"></a>Příklad: klient signalizuje entitu.

Pro přístup k entitám z běžné funkce Azure, která se označuje také jako *funkce klienta* – použijte [výstupní vazbu klienta entity](durable-functions-bindings.md#entity-client). Následující příklad ukazuje funkce aktivované frontou *, která tuto* vazbu používá.

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

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

Termínový *signál* znamená, že volání rozhraní API entity je jednosměrné a asynchronní. Pro *funkci klienta* není možné zjistit, kdy entita tuto operaci zpracovala. Funkce klienta navíc nemůže sledovat žádné hodnoty výsledků ani výjimky. 

### <a name="example-client-reads-an-entity-state"></a>Příklad: klient přečte stav entity.

Funkce klienta se také mohou dotazovat na stav entity, jak je znázorněno v následujícím příkladu:

```csharp
[FunctionName("QueryCounter")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function)] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");
    JObject state = await client.ReadEntityStateAsync<JObject>(entityId);
    return req.CreateResponse(HttpStatusCode.OK, state);
}
```

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    return context.df.readEntityState(entityId);
};
```

Dotazy na stav entity se odesílají do trvalého úložiště sledování a vracejí poslední *trvalý* stav entity. Tento stav je vždy stav "potvrzený", to znamená, že se během provádění operace nikdy dokončí dočasný přechodný stav. Je ale možné, že je tento stav zastaralý ve srovnání se stavem v paměti entity. Pouze orchestrace mohou číst stav v paměti entity, jak je popsáno v následující části.

### <a name="example-orchestration-signals-and-calls-an-entity"></a>Příklad: signály Orchestrace a volání entity

Funkce Orchestrator mají přístup k entitám pomocí rozhraní API ve [vazbě triggeru orchestrace](durable-functions-bindings.md#orchestration-trigger). Následující příklad kódu ukazuje *volání* funkce Orchestrator a *signalizaci* entity *čítače* .

```csharp
[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");
    if (currentValue < 10)
    {
        // One-way signal to the entity which updates the value - does not await a response
        context.SignalEntity(entityId, "Add", 1);
    }
}
```

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const entityId = new df.EntityId("Counter", "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    currentValue = yield context.df.callEntity(entityId, "get");
    if (currentValue < 10) {
        // One-way signal to the entity which updates the value - does not await a response
        yield context.df.signalEntity(entityId, "add", 1);
    }
});
```

Pouze orchestrace jsou schopny volat entity a získat odpověď, což může být buď návratová hodnota, nebo výjimka. Klientské funkce využívající [vazbu klienta](durable-functions-bindings.md#entity-client) mohou *signalizovat* pouze entity.

> [!NOTE]
> Volání entity z funkce Orchestrator je podobné volání [funkce aktivity](durable-functions-types-features-overview.md#activity-functions) z funkce Orchestrator. Hlavním rozdílem je, že funkce entit jsou trvalé objekty s adresou ( *ID entity*) a podporují zadání názvu operace. Funkce aktivit na druhé straně jsou bezstavové a nemají koncept operací.

### <a name="example-entity-signals-an-entity"></a>Příklad: entita signalizuje entitu.

Funkce entity může posílat signály jiným entitám (nebo dokonce sobě samým!), když spustí operaci.
Například můžeme upravit výše uvedený příklad entity čítače, aby mohl některé entitě monitorování odeslat signál "milník-dosažen", když čítač dosáhne hodnoty 100:

```csharp
   case "add":
        var currentValue = ctx.GetState<int>();
        var amount = ctx.GetInput<int>();
        if (currentValue < 100 && currentValue + amount >= 100)
        {
            ctx.SignalEntity(new EntityId("MonitorEntity", ""), "milestone-reached", ctx.EntityKey);
        }

        ctx.SetState(currentValue + amount);
        break;
```

```javascript
    case "add":
        const amount = context.df.getInput();
        if (currentValue < 100 && currentValue + amount >= 100) {
            const entityId = new df.EntityId("MonitorEntity", "");
            context.df.signalEntity(entityId, "milestone-reached", context.df.instanceId);
        }
        context.df.setState(currentValue + amount);
        break;
```

## <a name="entity-coordination"></a>Koordinace entit

Může nastat situace, kdy potřebujete koordinovat operace mezi několika entitami. Například v bankovní aplikaci můžete mít entity, které představují jednotlivé bankovní účty. Při převádění finančních prostředků z jednoho účtu na jiný je potřeba zajistit, aby měl _zdrojový_ účet dostatečné prostředky a aby se aktualizace _zdrojového_ i _cílového_ účtu prováděly nevhodným způsobem.

### <a name="example-transfer-funds-c"></a>Příklad: převodní prostředky (C#)

Následující příklad kódu přenáší prostředky mezi dvěma entitami _účtu_ pomocí funkce Orchestrator. Koordinace aktualizací entit vyžaduje použití metody `LockAsync` k vytvoření _kritické části_ v orchestraci:

> [!NOTE]
> Pro zjednodušení tento příklad znovu používá dříve definovanou entitu `Counter`. V reálné aplikaci je ale lepší místo toho definovat podrobnější `BankAccount` entitu.

```csharp
// This is a method called by an orchestrator function
public static async Task<bool> TransferFundsAsync(
    string sourceId,
    string destinationId,
    int transferAmount,
    IDurableOrchestrationContext context)
{
    var sourceEntity = new EntityId(nameof(Counter), sourceId);
    var destinationEntity = new EntityId(nameof(Counter), destinationId);

    // Create a critical section to avoid race conditions.
    // No operations can be performed on either the source or
    // destination accounts until the locks are released.
    using (await context.LockAsync(sourceEntity, destinationEntity))
    {
        ICounter sourceProxy = 
            context.CreateEntityProxy<ICounter>(sourceEntity);
        ICounter destinationProxy =
            context.CreateEntityProxy<ICounter>(destinationEntity);

        int sourceBalance = await sourceProxy.Get();

        if (sourceBalance >= transferAmount)
        {
            await sourceProxy.Add(-transferAmount);
            await destinationProxy.Add(transferAmount);

            // the transfer succeeded
            return true;
        }
        else
        {
            // the transfer failed due to insufficient funds
            return false;
        }
    }
}
```

V rozhraní .NET `LockAsync` vrátí `IDisposable`, který po vyřazení ukončí oddíl kritické. Tento `IDisposable` výsledek lze použít společně s blokem `using` pro získání syntaktické reprezentace důležité části.

V předchozím příkladu funkce Orchestrator přenesla prostředky ze _zdrojové_ entity na _cílovou_ entitu. Metoda `LockAsync` uzamkl entity _zdrojového_ i _cílového_ účtu. Toto uzamykání zajišťuje, že žádný jiný klient by nemohl zadat dotaz nebo změnit stav obou účtů, dokud logika orchestrace neukončila _kritickou část_ na konci `using`ho příkazu. Toto chování brání v tom, aby bylo možné přečerpání ze _zdrojového_ účtu.

> [!NOTE] 
> Když orchestrace skončí (obvykle nebo s chybou), všechny kritické oddíly v průběhu jsou implicitně ukončeny a všechny zámky jsou uvolněny.

### <a name="critical-section-behavior"></a>Kritické chování oddílu

Metoda `LockAsync` vytvoří _kritickou část_ v orchestraci. Tyto _kritické oddíly_ zabraňují jiným orchestraci v provádění překrývajících se změn v zadané sadě entit. Interně rozhraní `LockAsync` API pošle operace "Lock" do entit a vrátí, když obdrží zprávu s odpovědí "zámek" z každé z těchto stejných entit. *Zámky* i *odemknutí* jsou integrované operace podporované všemi entitami.

V entitě, která je v uzamčeném stavu, nejsou povoleny žádné operace od jiných klientů. Toto chování zajistí, že entita může současně uzamknout pouze jedna instance orchestrace. Pokud se volající pokusí vyvolat operaci u entity, když je uzamčena orchestrací, tato operace bude umístěna do *fronty čekajících operací*. Dokud orchestrace podniku neuvolní zámek, nebudou zpracovány žádné probíhající operace.

> [!NOTE] 
> To se mírně liší od primitiv synchronizace používaných ve většině programovacích jazyků, jako je například příkaz `lock` v C#. Například v C#příkazu musí být příkaz `lock` používán všemi vlákny k zajištění správného synchronizaci napříč více vlákny. Entity ale nevyžadují, aby všichni volající explicitně _zamkli_ entitu. Pokud nějaký volající zamkne entitu, všechny ostatní operace v této entitě se zablokují a zařadí se do fronty za tímto zámkem.

Zámky entit jsou trvalé, takže budou uchovány i v případě, že je spuštěn proces recyklován. Zámky jsou interně trvale zachované jako součást trvalého stavu entity.

Na rozdíl od transakcí nekritické oddíly automaticky vrátí zpět změny v případě chyb. Místo toho je nutné explicitně kódovat všechny zpracování chyb (vrácení zpět, opakovat nebo jiné); například zachycením chyb nebo výjimek. Tato volba návrhu je úmyslné. Automatické vrácení všech důsledků orchestrace je obtížné nebo nemožné, protože orchestrace můžou spouštět aktivity a volat externí služby, které se nedají vrátit zpátky. Také se pokusy o vrácení zpět mohou selhat a vyžadují další zpracování chyb.

### <a name="critical-section-rules"></a>Kritická pravidla oddílů

Na rozdíl od primitivních zámků nízké úrovně ve většině programovacích jazyků jsou důležité oddíly **zaručené zablokování**. Chcete-li zabránit zablokování, vynutili následující omezení: 

* Kritické oddíly nelze vnořovat.
* Kritické oddíly nemůžou vytvářet podorchestry.
* Kritické oddíly mohou volat pouze entity, které byly uzamčeny.
* Kritické oddíly nemohou volat stejnou entitu pomocí více paralelních volání.
* Kritické oddíly mohou signalizovat pouze entity, které nebyly uzamčeny.

Jakékoli porušení těchto pravidel způsobí chybu za běhu (například `LockingRulesViolationException` v rozhraní .NET), která obsahuje zprávu s vysvětlením, jaké pravidlo bylo přerušeno.

## <a name="comparison-with-virtual-actors"></a>Porovnání s virtuálními aktéry

Mnohé z funkcí trvalé entity nechte inspirovat [model actor](https://en.wikipedia.org/wiki/Actor_model). Pokud jste již obeznámeni s nástrojem Actors, můžete pochopit mnoho konceptů popsaných v tomto článku. Trvalé entity jsou obzvláště podobné [virtuálním aktérům](https://research.microsoft.com/projects/orleans/)nebo *zrnam*, jak je oblíbená v rámci [projektu Orleans](http://dotnet.github.io/orleans/). Příklad:

* Trvalé entity jsou adresovatelné prostřednictvím *ID entity*.
* Trvalé operace s entitami se v jednom okamžiku spouštějí po jednom, aby se zabránilo konfliktům časování.
* Trvalé entity jsou vytvořeny implicitně při volání nebo signalizaci.
* Neprovádíte-li operace, odolné entity budou tiše odpojeny od paměti.

Existují však některé důležité rozdíly, které je třeba zaznamenat:

* Odolné entity mají *přednost* před *latencí*, a proto nemusí být vhodná pro aplikace s přísnými požadavky na latenci.
* Trvalé entity nemají předdefinované časové limity pro zprávy. V Orleans vyprší časový limit všech zpráv po konfigurovatelném čase (ve výchozím nastavení 30 sekund).
* Zprávy odesílané mezi entitami jsou spolehlivě doručovány a v daném pořadí. V Orleans je podporováno spolehlivé nebo seřazené doručování obsahu odesílaného prostřednictvím datových proudů, ale není zaručeno pro všechny zprávy mezi zrna.
* Vzory požadavků a odpovědí v entitách jsou omezené na orchestrace. V rámci entit je povolen pouze jednosměrné zasílání zpráv (označované také jako "signalizace"), jako v původním modelu objektu actor, a na rozdíl od zrn v Orleans. 
* Trvalé entity se zablokují. V Orleans může probíhat zablokování (a neřešit, dokud nevyprší časový limit zpráv).
* Odolné entity lze použít ve spojení s trvalými orchestrací a podporují mechanismy distribuovaného zamykání. 


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přečtěte si příručku pro vývojáře k trvalým entitám v .NET.](durable-functions-dotnet-entities.md)

> [!div class="nextstepaction"]
> [Další informace o centrech úloh](durable-functions-task-hubs.md)
