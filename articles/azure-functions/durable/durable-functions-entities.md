---
title: Trvalé entity – Funkce Azure
description: Zjistěte, co jsou trvalé entity a jak je používat v rozšíření Trvalé funkce pro funkce Azure.
author: cgillum
ms.topic: overview
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 6ecf3bb5999296b2f5f8f5c25616fac8e0278cda
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80132512"
---
# <a name="entity-functions"></a>Funkce entity

Funkce entity definují operace pro čtení a aktualizaci malých částí stavu, označované jako *trvalé entity*. Podobně jako funkce orchestratoru jsou funkce entity funkce mise se speciálním typem aktivační události, *aktivační událost entity*. Na rozdíl od funkcí orchestratoru funkce entity spravují stav entity explicitně, nikoli implicitně reprezentující stav prostřednictvím toku řízení.
Entity poskytují prostředky pro horizontální navýšení kapacity aplikací distribucí práce mezi mnoho entit, z nichž každá má mírně velikosti stavu.

> [!NOTE]
> Funkce entity a související funkce jsou k dispozici pouze v režimech Durable Functions 2.0 a vyšší.

## <a name="general-concepts"></a>Obecné pojmy

Entity se chovají trochu jako malé služby, které komunikují prostřednictvím zpráv. Každá entita má jedinečnou identitu a vnitřní stav (pokud existuje). Podobně jako služby nebo objekty provádějí entity operace, když k tomu budou vyzvány. Při spuštění operace může aktualizovat vnitřní stav entity. Může také volat externí služby a čekat na odpověď. Entity komunikují s jinými entitami, orchestracemi a klienty pomocí zpráv, které jsou implicitně odesílány prostřednictvím spolehlivých front. 

Aby se zabránilo konfliktům, všechny operace na jedné entitě jsou zaručeny sériové, to znamená jeden po druhém. 

### <a name="entity-id"></a>Entity ID
K entitaem se přistupuje prostřednictvím jedinečného identifikátoru, *ID entity*. ID entity je jednoduše dvojice řetězců, které jednoznačně identifikují instanci entity. Skládá se z:

* **Název entity**, což je název, který identifikuje typ entity. Příkladem je "Counter". Tento název se musí shodovat s názvem funkce entity, která entitu implementuje. Není to citlivé na případ.
* **Klíč entity**, což je řetězec, který jednoznačně identifikuje entitu mezi všemi ostatními entitami se stejným názvem. Příkladem je identifikátor GUID.

Funkce entity `Counter` může být například použita pro udržování skóre v online hře. Každá instance hry má jedinečné ID entity, například `@Counter@Game1` a `@Counter@Game2`. Všechny operace, které cílí na určitou entitu, vyžadují zadání ID entity jako parametru.

### <a name="entity-operations"></a>Operace s entitami ###

Chcete-li vyvolat operaci entity, zadejte:

* **ID entity** cílové entity.
* **Název operace**, což je řetězec, který určuje operaci, která má být vykonána. Entita `Counter` může například `get`podporovat `reset` `add`, nebo operace.
* **Vstup operace**, což je volitelný vstupní parametr pro operaci. Například operace přidání může trvat celé číslo jako vstup.
* **Plánovaný čas**, což je volitelný parametr pro určení doby dodání operace. Například operace může být spolehlivě naplánováno spustit několik dní v budoucnu.

Operace mohou vrátit výslednou hodnotu nebo výsledek chyby, například chybu JavaScriptu nebo výjimku .NET. Tento výsledek nebo chybu lze pozorovat orchestrations, které volal operaci.

Operace entity můžete také vytvořit, číst, aktualizovat a odstranit stav entity. Stav entity je vždy trvale trvalé v úložišti.

## <a name="define-entities"></a>Definování entit

V současné době dvě odlišná řešení API pro definování entit jsou:

**Syntaxe založená na funkci**, kde jsou entity reprezentovány jako funkce a operace jsou explicitně odesílány aplikací. Tato syntaxe funguje dobře pro entity s jednoduchým stavem, několik operací nebo dynamickou sadu operací, jako v rozhraní aplikace. Tato syntaxe může být zdlouhavá udržovat, protože nezachytává chyby typu v době kompilace.

**Syntaxe založená na třídě (pouze.NET)**, kde entity a operace jsou reprezentovány třídami a metodami. Tato syntaxe vytváří snadněji čitelný kód a umožňuje operace, které mají být vyvolány způsobem bezpečné ho typu. Syntaxe založená na třídě je tenká vrstva nad syntaxí založenou na funkcích, takže obě varianty lze zaměnitve stejnou aplikaci zaměnitelně.

# <a name="c"></a>[C #](#tab/csharp)

### <a name="example-function-based-syntax---c"></a>Příklad: Syntaxe založená na funkci - C #

Následující kód je příkladem jednoduché `Counter` entity implementované jako trvalá funkce. Tato funkce definuje tři `add` `reset`operace `get`, , a , z nichž každá pracuje ve stavu celéčíslo.

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
            ctx.Return(ctx.GetState<int>());
            break;
    }
}
```

Další informace o syntaxi založené na funkcích a o tom, jak ji používat, naleznete [v tématu Syntaxe založená na funkcích](durable-functions-dotnet-entities.md#function-based-syntax).

### <a name="example-class-based-syntax---c"></a>Příklad: Syntaxe založená na třídě - C #

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

Stav této entity je objekt `Counter`typu , který obsahuje pole, které ukládá aktuální hodnotu čítače. Chcete-li zachovat tento objekt v úložišti, je serializován a deserializován [knihovnou Json.NET.](https://www.newtonsoft.com/json) 

Další informace o syntaxi založené na třídě a o tom, jak ji používat, naleznete [v tématu Definování tříd entit](durable-functions-dotnet-entities.md#defining-entity-classes).

# <a name="javascript"></a>[Javascript](#tab/javascript)

### <a name="example-javascript-entity"></a>Příklad: Entita JavaScriptu

Trvalé entity jsou k dispozici v Jazyce JavaScript `durable-functions` počínaje verzí **1.3.0** balíčku npm. Následující kód je `Counter` entita implementovaná jako trvalá funkce napsaná v Jazyce JavaScript.

**Čítač/funkce.json**
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

**Čítač/index.js**
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

---

## <a name="access-entities"></a>Přístup entity

Entity lze přistupovat pomocí jednosměrné nebo obousměrné komunikace. Následující terminologie rozlišuje dvě formy komunikace: 

* **Volání** entity používá obousměrnou (round-trip) komunikaci. Odešlete zprávu operace do entity a počkejte na zprávu odpovědi, než budete pokračovat. Zpráva s odpovědí může poskytnout výslednou hodnotu nebo výsledek chyby, například chybu JavaScriptu nebo výjimku .NET. Tento výsledek nebo chyba je pak pozorovánvolajícím.
* **Signalizace** entity používá jednosměrnou (požární a zapomenout) komunikaci. Odešlete zprávu operace, ale nečekejte na odpověď. Zatímco zpráva je zaručena, že bude doručena nakonec, odesílatel neví, kdy a nemůže sledovat žádné výsledné hodnoty nebo chyby.

Entity lze přistupovat z klientských funkcí, z funkcí orchestrátoru nebo z funkcí v rámci entity. Ne všechny formy komunikace jsou podporovány ve všech kontextech:

* V rámci klientů můžete signalizovat entity a číst stav entity.
* V rámci orchestrations můžete signalizovat entity a můžete volat entity.
* Z entity můžete signalizovat entity.

Následující příklady ilustrují tyto různé způsoby přístupu k entitám.

### <a name="example-client-signals-an-entity"></a>Příklad: Klient signalizuje entitu

Chcete-li získat přístup k entitám z běžné funkce Azure, která se také označuje jako funkce klienta, použijte [vazbu klienta entity](durable-functions-bindings.md#entity-client). Následující příklad ukazuje funkci aktivovanou frontou signalizací entity pomocí této vazby.

# <a name="c"></a>[C #](#tab/csharp)

> [!NOTE]
> Pro jednoduchost následující příklady ukazují volně zadanou syntaxi pro přístup k entitám. Obecně doporučujeme [přistupovat k entitám prostřednictvím rozhraní,](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) protože poskytuje další kontrolu typů.

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await client.signalEntity(entityId, "add", 1);
};
```

---

Termín *signál* znamená, že vyvolání rozhraní API entity je jednosměrné a asynchronní. Není možné, aby funkce klienta věděla, kdy entita zpracovala operaci. Také funkce klienta nemůže sledovat žádné hodnoty výsledků nebo výjimky. 

### <a name="example-client-reads-an-entity-state"></a>Příklad: Klient přečte stav entity

Klientské funkce mohou také dotazovat stav entity, jak je znázorněno v následujícím příkladu:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("QueryCounter")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function)] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");
    EntityStateResponse<JObject> stateResponse = await client.ReadEntityStateAsync<JObject>(entityId);
    return req.CreateResponse(HttpStatusCode.OK, stateResponse.EntityState);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    const stateResponse = await context.df.readEntityState(entityId);
    return stateResponse.entityState;
};
```

---

Dotazy na stav entity jsou odesílány do úložiště trvalésledování a vrátit poslední trvalý stav entity. Tento stav je vždy "potvrzený" stav, to znamená, že nikdy není dočasný mezilehlý stav předpokládá uprostřed provádění operace. Je však možné, že tento stav je zastaralý ve srovnání se stavem v paměti entity. Pouze orchestrations můžete číst entity ve stavu v paměti, jak je popsáno v následující části.

### <a name="example-orchestration-signals-and-calls-an-entity"></a>Příklad: Signály orchestrace a volání entity

Funkce Orchestrator mohou přistupovat k entitám pomocí api na [vazbě aktivační události orchestrace](durable-functions-bindings.md#orchestration-trigger). Následující ukázkový kód ukazuje funkci orchestrator, `Counter` která volá a signalizuje entitu.

# <a name="c"></a>[C #](#tab/csharp)

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const entityId = new df.EntityId("Counter", "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    currentValue = yield context.df.callEntity(entityId, "get");
});
```

> [!NOTE]
> JavaScript v současné době nepodporuje signalizaci entity z orchestrátoru. Místo toho použijte `callEntity`.

---

Pouze orchestrations jsou schopny volání entit a získání odpovědi, která může být buď vrácená hodnota nebo výjimka. Klientské funkce, které používají [vazby klienta](durable-functions-bindings.md#entity-client) může signalizovat pouze entity.

> [!NOTE]
> Volání entity z funkce orchestrator je podobné volání [funkce aktivity](durable-functions-types-features-overview.md#activity-functions) z funkce orchestratoru. Hlavní rozdíl je, že funkce entity jsou trvalé objekty s adresou, což je ID entity. Funkce entity podporují zadání názvu operace. Funkce aktivity, na druhé straně jsou bezstavové a nemají koncept operací.

### <a name="example-entity-signals-an-entity"></a>Příklad: Entity signalizují entitu

Funkce entity může odesílat signály jiným entitám nebo dokonce sám sobě, zatímco provádí operaci.
Například můžeme upravit předchozí `Counter` příklad entity tak, aby odešle signál "milník-reached" na některé entity monitoru, když čítač dosáhne hodnoty 100.

# <a name="c"></a>[C #](#tab/csharp)

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

---

## <a name="entity-coordination-currently-net-only"></a><a name="entity-coordination"></a>Koordinace entity (aktuálně pouze .NET)

Může nastat čas, kdy je třeba koordinovat operace napříč více entitami. Například v bankovní aplikaci můžete mít entity, které představují jednotlivé bankovní účty. Při převodu finančních prostředků z jednoho účtu na druhý musíte zajistit, aby zdrojový účet měl dostatečné prostředky. Je také nutné zajistit, aby aktualizace zdrojového i cílového účtu byly prováděny způsobem, který je v transakci konzistentní.

### <a name="example-transfer-funds-c"></a>Příklad: Převod finančních prostředků (C#)

Následující ukázkový kód převádí prostředky mezi dvěma entitami účtu pomocí funkce orchestrator. Koordinace aktualizace entit `LockAsync` vyžaduje použití metody k vytvoření _kritické části_ v orchestraci.

> [!NOTE]
> Pro jednoduchost tento příklad znovu `Counter` použije entitu definovanou dříve. V reálné aplikaci by bylo lepší definovat `BankAccount` podrobnější entitu.

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

V rozhraní `LockAsync` .NET vrátí `IDisposable`funkce , která při vyřazení ukončí kritický oddíl. Tento `IDisposable` výsledek lze použít `using` společně s blokem získat syntaktické reprezentace kritické části.

V předchozím příkladu orchestrátorská funkce převedla finanční prostředky ze zdrojové entity do cílové entity. Metoda `LockAsync` uzamkla entity zdrojového i cílového účtu. Toto uzamčení zajistilo, že žádný jiný klient nemohl dotazovat nebo upravovat stav obou `using` účtů, dokud logika orchestrace neukončila kritickou část na konci příkazu. Toto chování zabraňuje možnosti kontokorentu ze zdrojového účtu.

> [!NOTE] 
> Když orchestrace ukončí, buď normálně nebo s chybou, všechny kritické oddíly v průběhu jsou implicitně ukončeny a všechny zámky jsou uvolněny.

### <a name="critical-section-behavior"></a>Chování kritického oddílu

Metoda `LockAsync` vytvoří kritický oddíl v orchestraci. Tyto kritické oddíly zabraňují provádění překrývajících se změn v zadané sadě entit. Interně `LockAsync` rozhraní API odešle operace "lock" entity a vrátí, když obdrží "lock získané" odpověď zprávy z každé z těchto stejných entit. Uzamčení a odemknutí jsou integrované operace podporované všemi entitami.

Žádné operace od jiných klientů jsou povoleny na entitu, zatímco je v uzamčeném stavu. Toto chování zajišťuje, že pouze jedna instance orchestrace můžete zamknout entitu najednou. Pokud se volající pokusí vyvolat operaci entity, zatímco je uzamčen orchestrací, tato operace je umístěna do fronty čekající operace. Žádné čekající operace jsou zpracovány až poté, co blokování orchestrace uvolní jeho zámek.

> [!NOTE] 
> Toto chování se mírně liší od synchronizace primitiv `lock` používaných ve většině programovacích jazyků, jako je například příkaz v jazyce C#. Například v C#, `lock` příkaz musí být používán všemi vlákny k zajištění správné synchronizace mezi více vlákny. Entity však nevyžadují, aby všichni volající explicitně uzamkli entitu. Pokud některý volající uzamkne entitu, všechny ostatní operace v této entitě jsou blokovány a zařazeny do fronty za tímto zámkem.

Zámky na entity jsou trvalé, takže přetrvávají i v případě, že je recyklován proces provádění. Zámky jsou interně trvalé jako součást trvalého stavu entity.

Na rozdíl od transakcí kritické oddíly nejsou automaticky vrátit zpět změny v případě chyb. Místo toho jakékoli zpracování chyb, jako je například vrácení zpět nebo opakování, musí být explicitně kódován, například zachycení chyby nebo výjimky. Tato volba návrhu je záměrná. Automatické vrácení zpět všechny účinky orchestrace je obtížné nebo nemožné obecně, protože orchestrations může spouštět aktivity a volání externích služeb, které nelze vrátit zpět. Také pokusy o vrácení zpět může selhat a vyžadují další zpracování chyb.

### <a name="critical-section-rules"></a>Pravidla kritického oddílu

Na rozdíl od nižší úrovně uzamčení primitiv ve většině programovacích jazyků kritické části jsou *zaručeně není zablokování*. Abychom zabránili zablokování, vynucujeme následující omezení: 

* Kritické řezy nelze vnořit.
* Kritické oddíly nelze vytvořit suborchestrations.
* Kritické oddíly mohou volat pouze entity, které mají uzamčeny.
* Kritické oddíly nelze volat stejnou entitu pomocí více paralelních volání.
* Kritické oddíly mohou signalizovat pouze entity, které nebyly uzamčeny.

Jakékoli porušení těchto pravidel způsobit chybu za `LockingRulesViolationException` běhu, například v .NET, která obsahuje zprávu, která vysvětluje, jaké pravidlo bylo přerušeno.

## <a name="comparison-with-virtual-actors"></a>Porovnání s virtuálními aktéry

Mnoho prvků odolných entit je inspirováno [modelem objektu actor](https://en.wikipedia.org/wiki/Actor_model). Pokud jste již obeznámeni s herci, můžete rozpoznat mnoho konceptů popsaných v tomto článku. Trvanlivé entity jsou obzvláště podobné [virtuálním aktérům](https://research.microsoft.com/projects/orleans/), neboli zrnům, jak je popularizoval [projekt Orleans](http://dotnet.github.io/orleans/). Například:

* Trvalé entity lze adresovat prostřednictvím ID entity.
* Operace trvalé entity provádět sériově, jeden po druhém, aby se zabránilo časování podmínky.
* Trvalé entity jsou vytvořeny implicitně, když jsou volány nebo signalizovány.
* Pokud nejsou prováděny operace, trvalé entity jsou tiše uvolněny z paměti.

Tam jsou některé důležité rozdíly, které stojí za zmínku:

* Trvalé entity upřednostňují odolnost před latencí, a proto nemusí být vhodné pro aplikace s přísnými požadavky na latenci.
* Trvalé entity nemají předdefinované časové osy pro zprávy. V Orleans, všechny zprávy časový mat po konfigurovatelné době. Výchozí hodnota je 30 sekund.
* Zprávy odesílané mezi entitami jsou doručovány spolehlivě a v pořadí. V Orleans je spolehlivé nebo objednané doručení podporováno pro obsah odeslaný prostřednictvím datových proudů, ale není zaručeno pro všechny zprávy mezi zrny.
* Vzory odezvy na požadavek v entitách jsou omezeny na orchestrations. Z entity, pouze jednosměrné zasílání zpráv (také známý jako signalizace) je povoleno, stejně jako v původním modelu actor, a na rozdíl od zrna v Orleans. 
* Trvalé entity nejsou zablokování. V Orleans může dojít k zablokování a nevyřeší, dokud zprávy časový mat.
* Trvalé entity lze použít ve spojení s trvalé orchestrace a podporu distribuované zamykání mechanismy. 


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přečtěte si příručku pro vývojáře k trvalým entitám v rozhraní .NET](durable-functions-dotnet-entities.md)

> [!div class="nextstepaction"]
> [Informace o centrech úloh](durable-functions-task-hubs.md)
