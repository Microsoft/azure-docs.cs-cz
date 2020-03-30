---
title: Průvodce vývojářskými entitami v rozhraní .NET – Funkce Azure
description: Jak pracovat s trvalými entitami v rozhraní .NET s rozšířením Durable Functions pro funkce Azure.
author: sebastianburckhardt
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: azfuncdf
ms.openlocfilehash: 01e07eaee705634b03cc4462c4058e290daa8bc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278126"
---
# <a name="developers-guide-to-durable-entities-in-net"></a>Průvodce vývojářem odolných entit v rozhraní .NET

V tomto článku popisujeme dostupná rozhraní pro vývoj trvalých entit s rozhraním .NET podrobně, včetně příkladů a obecného poradenství. 

Funkce entity poskytují vývojářům aplikací bez serveru pohodlný způsob uspořádání stavu aplikace jako kolekce jemně odstupňovaných entit. Další podrobnosti o základních konceptech najdete v článku [Trvalé entity: Koncepty.](durable-functions-entities.md)

V současné době nabízíme dvě api pro definování entit:

- **Syntaxe založená na třídě** představuje entity a operace jako třídy a metody. Tato syntaxe vytváří snadno čitelný kód a umožňuje operace, které mají být vyvolány způsobem, který kontroluje typ prostřednictvím rozhraní. 

- **Syntaxe založená na funkci** je rozhraní nižší úrovně, které představuje entity jako funkce. Poskytuje přesnou kontrolu nad tím, jak jsou odesílány operace entity a jak je spravován stav entity.  

Tento článek se zaměřuje především na syntaxi založenou na třídě, protože očekáváme, že bude vhodnější pro většinu aplikací. [Syntaxe založená na funkci](#function-based-syntax) však může být vhodná pro aplikace, které chtějí definovat nebo spravovat své vlastní abstrakce pro stav entity a operace. Také může být vhodné pro implementaci knihoven, které vyžadují geniálnost, která není aktuálně podporována syntaxí založenou na třídě. 

> [!NOTE]
> Syntaxe založená na třídě je pouze vrstva nad syntaxí založenou na funkci, takže obě varianty lze zaměnitve něve stejnou aplikaci. 
 
## <a name="defining-entity-classes"></a>Definování tříd entit

Následující příklad je implementace `Counter` entity, která ukládá jednu hodnotu celého `Add`čísla `Reset` `Get`typu `Delete`a nabízí čtyři operace , , a .

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int Value { get; set; }

    public void Add(int amount) 
    {
        this.Value += amount;
    }

    public Task Reset() 
    {
        this.Value = 0;
        return Task.CompletedTask;
    }

    public Task<int> Get() 
    {
        return Task.FromResult(this.Value);
    }

    public void Delete() 
    {
        Entity.Current.DeleteState();
    }

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

Funkce `Run` obsahuje často používaný text potřebný pro použití syntaxe založené na třídě. Musí se jednat o *statickou* funkci Azure. Spustí se jednou pro každou zprávu operace, která je zpracována entitou. Když `DispatchAsync<T>` je volána a entita již není v paměti, vytvoří objekt typu `T` a naplní jeho pole z poslední trvalé JSON nalezené v úložišti (pokud existuje). Potom vyvolá metodu s odpovídající název.

> [!NOTE]
> Stav entity založené na třídě je **vytvořen implicitně** před entita zpracuje operaci a `Entity.Current.DeleteState()`lze odstranit **explicitně** v operaci voláním .

### <a name="class-requirements"></a>Požadavky na třídu
 
Entity třídy jsou POCO (plain staré CLR objekty), které nevyžadují žádné speciální superclasses, rozhraní nebo atributy. Nicméně:

- Třída musí být konstrukčně konstrukčně (viz [Konstrukce entity](#entity-construction)).
- Třída musí být JSON serializovatelné (viz [Serializace entit](#entity-serialization)).

Také každá metoda, která má být vyvolána jako operace, musí splňovat další požadavky:

- Operace musí mít maximálně jeden argument a nesmí mít žádné přetížení nebo obecné argumenty typu.
- Operace, která má být volána z orchestraci pomocí rozhraní, musí vrátit `Task` nebo `Task<T>`.
- Argumenty a vrácené hodnoty musí být serializovatelné hodnoty nebo objekty.

### <a name="what-can-operations-do"></a>Co mohou operace dělat?

Všechny operace entity můžete číst a aktualizovat stav entity a změny stavu jsou automaticky trvalé do úložiště. Kromě toho operace můžete provádět externí vstupně-v nebo jiných výpočtů, v rámci obecných limitů společných pro všechny funkce Azure.

Operace mají také přístup k funkcím poskytovaným kontextem: `Entity.Current`

* `EntityName`: název aktuálně spuštěné entity.
* `EntityKey`: klíč aktuálně spuštěné entity.
* `EntityId`: ID aktuálně spuštěné entity (včetně názvu a klíče).
* `SignalEntity`: odešle entitě jednosměrnou zprávu.
* `CreateNewOrchestration`: spustí novou orchestraci.
* `DeleteState`: odstraní stav této entity.

Například můžeme upravit entitu čítače tak, aby se spustí orchestrace, když čítač dosáhne 100 a předá ID entity jako vstupní argument:

```csharp
    public void Add(int amount) 
    {
        if (this.Value < 100 && this.Value + amount > 100)
        {
            Entity.Current.StartNewOrchestration("MilestoneReached", Entity.Current.EntityId)
        }
        this.Value += amount;      
    }
```

## <a name="accessing-entities-directly"></a>Přímý přístup k entitam

Entity založené na třídách lze přistupovat přímo, pomocí explicitní názvy řetězců pro entitu a její operace. Uvádíme některé příklady níže; pro hlubší vysvětlení základních pojmů (například signály vs volání) viz diskuse v [entitách aplikace Access](durable-functions-entities.md#access-entities). 

> [!NOTE]
> Pokud je to možné, doporučujeme [přístup k entitám prostřednictvím rozhraní](#accessing-entities-through-interfaces), protože poskytuje další kontrolu typů.

### <a name="example-client-signals-entity"></a>Příklad: entita signálů klienta

Následující funkce Azure Http implementuje operaci DELETE pomocí konvencí REST. Odešle signál odstranění entitě čítače, jejíž klíč je předán v cestě URL.

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync(entityId, "Delete");    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

### <a name="example-client-reads-entity-state"></a>Příklad: Klient čte stav entity

Následující funkce Azure Http implementuje operaci GET pomocí konvencí REST. Přečte aktuální stav entity čítače, jehož klíč je předán v cestě URL.

```csharp
[FunctionName("GetCounter")]
public static async Task<HttpResponseMessage> GetCounter(
    [HttpTrigger(AuthorizationLevel.Function, "get", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    var state = await client.ReadEntityStateAsync<Counter>(entityId); 
    return req.CreateResponse(state);
}
```

> [!NOTE]
> Objekt vrácený `ReadEntityStateAsync` je pouze místní kopie, to znamená snímek stavu entity z některých dřívějších bodů v čase. Zejména může být zastaralé a úprava tohoto objektu nemá žádný vliv na skutečnou entitu. 

### <a name="example-orchestration-first-signals-then-calls-entity"></a>Příklad: orchestrace první signály, pak volání entity

Následující orchestrace signalizuje entitu čítače, aby ji zvýšila, a pak zavolá stejnou entitu ke čtení její nejnovější hodnoty.

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");

    // One-way signal to the entity - does not await a response
    context.SignalEntity(entityId, "Add", 1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");

    return currentValue;
}
```

## <a name="accessing-entities-through-interfaces"></a>Přístup k entitám prostřednictvím rozhraní

Rozhraní lze použít pro přístup k entitám prostřednictvím generovaných proxy objektů. Tento přístup zajišťuje, že název a typ argumentu operace odpovídá co je implementováno. Doporučujeme používat rozhraní pro přístup k entitám, kdykoli je to možné.

Například můžeme upravit příklad čítače takto:

```csharp
public interface ICounter
{
    void Add(int amount);
    Task Reset();
    Task<int> Get();
    void Delete();
}

public class Counter : ICounter
{
    ...
}
```

Entity třídy a entity rozhraní jsou podobné zrna a zrna rozhraní popularizoval [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/). Další informace o podobnostech a rozdílech mezi trvalými entitami a orleanskými informacemi najdete [v tématu Porovnání s virtuálními aktéry](durable-functions-entities.md#comparison-with-virtual-actors).

Kromě poskytování kontroly typu, rozhraní jsou užitečné pro lepší oddělení obavy v rámci aplikace. Například vzhledem k tomu, že entita může implementovat více rozhraní, může jedna entita obsluhovat více rolí. Vzhledem k tomu, že rozhraní může být implementováno více entitami, obecné vzory komunikace mohou být implementovány jako opakovaně použitelné knihovny.

### <a name="example-client-signals-entity-through-interface"></a>Příklad: entita signálů klienta prostřednictvím rozhraní

Klientský kód `SignalEntityAsync<TEntityInterface>` lze použít k odesílání `TEntityInterface`signálů entitám, které implementují . Například:

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync<ICounter>(entityId, proxy => proxy.Delete());    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

V tomto příkladu `proxy` je parametr dynamicky `ICounter`generovaná instance , která `Delete` interně převádí volání do signálu.

> [!NOTE]
> Api `SignalEntityAsync` lze použít pouze pro jednosměrné operace. I v případě, že operace vrátí `Task<T>`, hodnota parametru `T` bude vždy null nebo `default`, nikoli skutečný výsledek.
Například nemá smysl signalizovat `Get` operaci, protože není vrácena žádná hodnota. Místo toho klienti `ReadStateAsync` mohou použít buď pro přístup ke stavu čítače `Get` přímo, nebo můžete spustit funkci orchestrator, která volá operaci. 

### <a name="example-orchestration-first-signals-then-calls-entity-through-proxy"></a>Příklad: orchestrace první signály, pak volání entity přes proxy

Chcete-li volat nebo signalizovat entitu z orchestrování, `CreateEntityProxy` lze spolu s typem rozhraní vygenerovat proxy server pro entitu. Tento proxy server pak lze použít k volání nebo signálu operace:

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");
    var proxy = context.CreateEntityProxy<ICounter>(entityId);

    // One-way signal to the entity - does not await a response
    proxy.Add(1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await proxy.Get();

    return currentValue;
}
```

Implicitně všechny operace, `void` které vracejí jsou signalizovány a všechny operace, které vracejí `Task` nebo `Task<T>` jsou volány. Jeden můžete změnit toto výchozí chování a signáloperace i `SignalEntity<IInterfaceType>` v případě, že vrátí Task, pomocí metody explicitně.

### <a name="shorter-option-for-specifying-the-target"></a>Kratší možnost pro určení cíle

Při volání nebo signalizaci entity pomocí rozhraní musí první argument určit cílovou entitu. Cíl lze zadat buď zadáním ID entity, nebo v případech, kdy existuje pouze jedna třída, která implementuje entitu, pouze klíč entity:

```csharp
context.SignalEntity<ICounter>(new EntityId(nameof(Counter), "myCounter"), ...);
context.SignalEntity<ICounter>("myCounter", ...);
```

Pokud je zadán pouze klíč entity a jedinečná implementace nelze `InvalidOperationException` nalézt za běhu, je vyvolána. 

### <a name="restrictions-on-entity-interfaces"></a>Omezení rozhraní entit

Jako obvykle musí být všechny typy parametrů a návratů json-serializovatelné. V opačném případě jsou vyvolány výjimky serializace za běhu.

Vynucujeme také některá další pravidla:
* Rozhraní entit musí definovat pouze metody.
* Rozhraní entit nesmí obsahovat obecné parametry.
* Metody rozhraní entity nesmí mít více než jeden parametr.
* Metody rozhraní entity `void` `Task`se musí vrátit , nebo`Task<T>` 

Pokud jsou některá z těchto `InvalidOperationException` pravidel porušena, je vyvolána za běhu `SignalEntity` při `CreateProxy`použití rozhraní jako argument typu nebo . Zpráva o výjimce vysvětluje, které pravidlo bylo přerušeno.

> [!NOTE]
> Vrácené `void` metody rozhraní mohou být signalizovány pouze (jednosměrné), nikoli volány (obousměrné). Metody rozhraní `Task` vracející `Task<T>` se nebo mohou být volány nebo signalizovány. Pokud je volána, vrátí výsledek operace nebo znovu vyvolat výjimky vyvoláné operací. Však při signalizaci, nevracejí skutečný výsledek nebo výjimku z operace, ale pouze výchozí hodnotu.

## <a name="entity-serialization"></a>Serializace entity

Vzhledem k tomu, že stav entity je trvale trvalé, musí být třída entity serializovatelné. Za běhu Durable Functions používá pro tento účel [knihovnu Json.NET,](https://www.newtonsoft.com/json) která podporuje řadu zásad a atributů pro řízení procesu serializace a deserializace. Nejčastěji používané datové typy Jazyka C# (včetně polí a typů kolekcí) jsou již serializovatelné a lze je snadno použít k definování stavu trvalých entit.

Json.NET můžete například snadno serializovat a rekonstruovat následující třídu:

```csharp
[JsonObject(MemberSerialization = MemberSerialization.OptIn)]
public class User
{
    [JsonProperty("name")]
    public string Name { get; set; }

    [JsonProperty("yearOfBirth")]
    public int YearOfBirth { get; set; }

    [JsonProperty("timestamp")]
    public DateTime Timestamp { get; set; }

    [JsonProperty("contacts")]
    public Dictionary<Guid, Contact> Contacts { get; set; } = new Dictionary<Guid, Contact>();

    [JsonObject(MemberSerialization = MemberSerialization.OptOut)]
    public struct Contact
    {
        public string Name;
        public string Number;
    }

    ...
}
```

### <a name="serialization-attributes"></a>Atributy serializace

Ve výše uvedeném příkladu jsme se rozhodli zahrnout několik atributů, aby byla základní serializace viditelnější:
- Jsme opatří třídu `[JsonObject(MemberSerialization.OptIn)]` s námpřipomenout, že třída musí být serializovatelné a zachovat pouze členy, které jsou explicitně označeny jako vlastnosti JSON.
-  Okomentovali jsme pole, která mají `[JsonProperty("name")]` být zachována, abychom připomněli, že pole je součástí stavu trvalé entity, a zadali název vlastnosti, která má být použita v reprezentaci JSON.

Tyto atributy však nejsou vyžadovány; jiné konvence nebo atributy jsou povoleny, pokud pracují s Json.NET. Například jeden může `[DataContract]` používat atributy nebo žádné atributy vůbec:

```csharp
[DataContract]
public class Counter
{
    [DataMember]
    public int Value { get; set; }
    ...
}

public class Counter
{
    public int Value;
    ...
}
```

Ve výchozím nastavení název třídy *není* uložen jako součást reprezentace JSON: to znamená, že používáme `TypeNameHandling.None` jako výchozí nastavení. Toto výchozí chování lze `JsonObject` `JsonProperty` přepsat pomocí nebo atributy.

### <a name="making-changes-to-class-definitions"></a>Provádění změn definic tříd

Při provádění změn definice třídy po spuštění aplikace je nutná určitá péče, protože uložený objekt JSON již nemusí odpovídat nové definici třídy. Přesto je často možné správně řešit změny formátů dat tak dlouho, dokud jeden `JsonConvert.PopulateObject`chápe proces deserializace používané .

Zde jsou například některé příklady změn a jejich účinku:

1. Pokud je přidána nová vlastnost, která není k dispozici v uložené mson, předpokládá jeho výchozí hodnotu.
1. Pokud je odebrána vlastnost, která je přítomna v uložené mson, předchozí obsah je ztracen.
1. Pokud je vlastnost přejmenována, efekt je jako při odebrání staré a přidání nové.
1. Pokud je typ vlastnosti změněntak, že již nelze rekonstruovat z uložené JSON, je vyvolána výjimka.
1. Pokud se změní typ vlastnosti, ale stále ji lze rekonstruovat z uloženého zařízení JSON, bude tak činit.

Existuje mnoho možností, které jsou k dispozici pro přizpůsobení chování Json.NET. Chcete-li například vynutit výjimku, pokud uložený jazyk JSON obsahuje `JsonObject(MissingMemberHandling = MissingMemberHandling.Error)`pole, které není ve třídě k dispozici, zadejte atribut . Je také možné napsat vlastní kód pro deserializaci, který umí číst JSON uložené v libovolných formátech.

## <a name="entity-construction"></a>Konstrukce entity

Někdy chceme mít větší kontrolu nad tím, jak jsou objekty entit vytvářeny. Nyní popisujeme několik možností pro změnu výchozího chování při vytváření objektů entity. 

### <a name="custom-initialization-on-first-access"></a>Vlastní inicializace při prvním přístupu

V některých případě musíme provést některé zvláštní inicializace před odesláním operace do entity, která nebyla nikdy přístupná nebo která byla odstraněna. Chcete-li zadat toto chování, `DispatchAsync`lze přidat podmínku před :

```csharp
[FunctionName(nameof(Counter))]
public static Task Run([EntityTrigger] IDurableEntityContext ctx)
{
    if (!ctx.HasState)
    {
        ctx.SetState(...);
    }
    return ctx.DispatchAsync<Counter>();
}
```

### <a name="bindings-in-entity-classes"></a>Vazby ve třídách entit

Na rozdíl od běžných funkcí metody třídy entit nemají přímý přístup ke vstupním a výstupním vazbám. Místo toho musí být data vazby zachycena v deklaraci `DispatchAsync<T>` funkce vstupního bodu a poté předána metodě. Všechny objekty `DispatchAsync<T>` předané budou automaticky předány do konstruktoru třídy entity jako argument.

Následující příklad ukazuje, `CloudBlobContainer` jak lze zpřístupnit odkaz ze [vstupní vazby objektu blob](../functions-bindings-storage-blob-input.md) entitu založenou na třídě.

```csharp
public class BlobBackedEntity
{
    [JsonIgnore]
    private readonly CloudBlobContainer container;

    public BlobBackedEntity(CloudBlobContainer container)
    {
        this.container = container;
    }

    // ... entity methods can use this.container in their implementations ...
    
    [FunctionName(nameof(BlobBackedEntity))]
    public static Task Run(
        [EntityTrigger] IDurableEntityContext context,
        [Blob("my-container", FileAccess.Read)] CloudBlobContainer container)
    {
        // passing the binding object as a parameter makes it available to the
        // entity class constructor
        return context.DispatchAsync<BlobBackedEntity>(container);
    }
}
```

Další informace o vazby v Azure Functions, najdete v dokumentaci [k aktivační události a vazby Azure funkce.](../functions-triggers-bindings.md)

### <a name="dependency-injection-in-entity-classes"></a>Vkládání závislostí ve třídách entit

Entity třídy podporují [vkládání závislostí azure funkce](../functions-dotnet-dependency-injection.md). Následující příklad ukazuje, jak `IHttpClientFactory` zaregistrovat službu do entity založené na třídě.

```csharp
[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
        }
    }
}
```

Následující fragment příkazu ukazuje, jak začlenit vstřikované služby do třídy entity.

```csharp
public class HttpEntity
{
    [JsonIgnore]
    private readonly HttpClient client;

    public class HttpEntity(IHttpClientFactory factory)
    {
        this.client = factory.CreateClient();
    }

    public Task<int> GetAsync(string url)
    {
        using (var response = await this.client.GetAsync(url))
        {
            return (int)response.StatusCode;
        }
    }

    [FunctionName(nameof(HttpEntity))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<HttpEntity>();
}
```

> [!NOTE]
> Chcete-li se vyhnout problémům se serializací, vylučte pole určená k uložení vložených hodnot ze serializace.

> [!NOTE]
> Na rozdíl od použití vkládání konstruktoru v pravidelných funkcích .NET Azure, `static`musí *být* deklarována metoda vstupního bodu funkcí pro entity založené na třídách . Deklarování vstupního bodu nestatické funkce může způsobit konflikty mezi normálním inicializátorem objektu Azure Functions a inicializátorem objektu Durable Entities.

## <a name="function-based-syntax"></a>Syntaxe založená na funkcích

Zatím jsme se zaměřili na syntaxi založenou na třídě, protože očekáváme, že bude vhodnější pro většinu aplikací. Syntaxe založená na funkci však může být vhodná pro aplikace, které chtějí definovat nebo spravovat své vlastní abstrakce pro stav entity a operace. Také může být vhodné při implementaci knihoven, které vyžadují genialitu, která není aktuálně podporována syntaxí založenou na třídě. 

Pomocí syntaxe založené na funkci funkce funkce entity explicitně zpracovává odeslání operace a explicitně spravuje stav entity. Například následující kód zobrazuje entitu *Čítače* implementovanou pomocí syntaxe založené na funkci.  

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
        case "delete":
            ctx.DeleteState();
            break;
    }
}
```

### <a name="the-entity-context-object"></a>Kontextový objekt entity

Funkce specifické pro entitu lze přistupovat `IDurableEntityContext`prostřednictvím objektu kontextu typu . Tento objekt kontextu je k dispozici jako parametr pro funkci entity `Entity.Current`a prostřednictvím asynchronní místní vlastnosti .

Následující členové poskytují informace o aktuální operaci a umožňují nám zadat vrácenou hodnotu. 

* `EntityName`: název aktuálně spuštěné entity.
* `EntityKey`: klíč aktuálně spuštěné entity.
* `EntityId`: ID aktuálně spuštěné entity (včetně názvu a klíče).
* `OperationName`: název aktuální operace.
* `GetInput<TInput>()`: získá vstup pro aktuální operaci.
* `Return(arg)`: vrátí hodnotu orchestraci, která volala operaci.

Následující členové spravují stav entity (vytvořit, číst, aktualizovat, odstranit). 

* `HasState`: zda entita existuje, to znamená, že má nějaký stav. 
* `GetState<TState>()`: získá aktuální stav entity. Pokud ještě neexistuje, je vytvořen.
* `SetState(arg)`: vytvoří nebo aktualizuje stav entity.
* `DeleteState()`: odstraní stav entity, pokud existuje. 

Pokud je stav `GetState` vrácený objektem, může být přímo upraven kódem aplikace. Není třeba volat `SetState` znovu na konci (ale také bez újmy). Pokud `GetState<TState>` je volána vícekrát, musí být použit stejný typ.

Nakonec následující členy se používají k signalizaci jiných entit nebo spuštění nových orchestrací:

* `SignalEntity(EntityId, operation, input)`: odešle entitě jednosměrnou zprávu.
* `CreateNewOrchestration(orchestratorFunctionName, input)`: spustí novou orchestraci.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Informace o konceptech entit](durable-functions-entities.md)
