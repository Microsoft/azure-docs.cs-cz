---
title: Příručka pro vývojáře k odolným entitám v .NET – Azure Functions
description: Jak pracovat s trvalými entitami v rozhraní .NET s rozšířením Durable Functions pro Azure Functions.
author: sebastianburckhardt
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: azfuncdf
ms.openlocfilehash: d480b8db69b34eda7ca1ea8e1b2755179f9c673f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88055169"
---
# <a name="developers-guide-to-durable-entities-in-net"></a>Příručka pro vývojáře k odolným entitám v .NET

V tomto článku jsme popsali dostupná rozhraní pro vývoj trvalých entit s .NET, včetně příkladů a obecného poradenství. 

Funkce entit poskytují vývojářům aplikací bez serveru pohodlný způsob, jak uspořádat stav aplikace jako kolekci jemně odstupňovaných entit. Další podrobnosti o základních konceptech najdete v článku [odolné entity: koncepty](durable-functions-entities.md) .

Momentálně nabízíme dvě rozhraní API pro definování entit:

- **Syntaxe založená na třídě** představuje entity a operace jako třídy a metody. Tato syntaxe generuje snadno čitelný kód a umožňuje vyvolávat operace v kontrolovaném typu prostřednictvím rozhraní. 

- **Syntaxe založená na funkcích** je rozhraní nižší úrovně, které představuje entity jako funkce. Poskytuje přesnou kontrolu nad tím, jak se operace entity odesílají a jak se spravují stav entity.  

Tento článek se zaměřuje hlavně na syntaxi založenou na třídě, protože očekáváme, že bude vhodnější pro většinu aplikací. [Syntaxe založená na funkcích](#function-based-syntax) ale může být vhodná pro aplikace, které chtějí definovat nebo spravovat své vlastní abstrakce pro stav a operace entity. Také může být vhodné pro implementaci knihoven, které vyžadují obecu, která není aktuálně podporována syntaxí založenou na třídě. 

> [!NOTE]
> Syntaxe založená na třídě je pouze vrstva nad syntaxí založenou na funkci, takže obě varianty lze ve stejné aplikaci použít zaměnitelné. 
 
## <a name="defining-entity-classes"></a>Definování tříd entit

Následující příklad je implementace `Counter` entity, která ukládá jednu hodnotu typu Integer a nabízí čtyři operace `Add` , `Reset` , `Get` a `Delete` .

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

`Run`Funkce obsahuje často používaný text, který je vyžadován pro použití syntaxe založené na třídě. Musí se jednat o *statickou* funkci Azure Functions. Provádí se jednou pro každou zprávu operace, která je zpracována entitou. Když `DispatchAsync<T>` je volána a entita již není v paměti, vytvoří objekt typu `T` a naplní jeho pole z posledního trvalého formátu JSON nalezeného v úložišti (pokud existuje). Poté vyvolá metodu se shodným názvem.

> [!NOTE]
> Stav entity založené na třídě je **vytvořen implicitně** předtím, než entita zpracuje operaci a lze ji v operaci **explicitně odstranit** voláním `Entity.Current.DeleteState()` .

### <a name="class-requirements"></a>Požadavky na třídy
 
Třídy entit jsou POCOs (prosté staré objekty CLR), které nevyžadují žádné speciální třídy, rozhraní nebo atributy. Naopak

- Třída musí být constructible (viz [konstrukce entity](#entity-construction)).
- Třída musí být serializovatelný ve formátu JSON (viz [serializace entity](#entity-serialization)).

Také všechny metody, které mají být vyvolány jako operace, musí splňovat další požadavky:

- Operace musí mít maximálně jeden argument a nesmí mít žádná přetížení ani obecné argumenty typu.
- Operace, která má být volána z orchestrace pomocí rozhraní, musí vracet `Task` nebo `Task<T>` .
- Argumenty a návratové hodnoty musí být serializovatelných hodnot nebo objektů.

### <a name="what-can-operations-do"></a>K čemu operace slouží?

Všechny operace entity můžou číst a aktualizovat stav entity a změny stavu se automaticky uchovávají do úložiště. Kromě toho mohou operace provádět externí vstupně-výstupní operace nebo jiné výpočty v rámci obecných omezení společných pro všechny Azure Functions.

Operace mají také přístup k funkcím, které poskytuje `Entity.Current` kontext:

* `EntityName`: název aktuálně spuštěné entity.
* `EntityKey`: klíč aktuálně vykonávané entity.
* `EntityId`: ID aktuálně vykonávané entity (včetně názvu a klíče).
* `SignalEntity`: pošle jednosměrnou zprávu entitě.
* `CreateNewOrchestration`: spustí novou orchestraci.
* `DeleteState`: odstraní stav této entity.

Můžete například upravit entitu čítače, aby spustila orchestraci, když počítadlo dosáhne 100 a předá ID entity jako vstupní argument:

```csharp
    public void Add(int amount) 
    {
        if (this.Value < 100 && this.Value + amount >= 100)
        {
            Entity.Current.StartNewOrchestration("MilestoneReached", Entity.Current.EntityId);
        }
        this.Value += amount;      
    }
```

## <a name="accessing-entities-directly"></a>Přímý přístup k entitám

K entitám založeným na třídě lze získat přímý pøístup pomocí explicitních řetězcových názvů pro entitu a její operace. Níže uvádíme několik příkladů; Podrobnější vysvětlení základních konceptů (jako jsou signály vs. volání) najdete v tématu diskuze v části věnované [přístupu](durable-functions-entities.md#access-entities). 

> [!NOTE]
> Pokud je to možné, doporučujeme přistoupit [k entitám prostřednictvím rozhraní](#accessing-entities-through-interfaces), protože poskytuje další kontrolu typu.

### <a name="example-client-signals-entity"></a>Příklad: Client – signály entit

Následující funkce Azure http implementuje pomocí konvencí REST operaci DELETE. Pošle signál k odstranění pro entitu čítače, jejíž klíč se předává v cestě URL.

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

### <a name="example-client-reads-entity-state"></a>Příklad: klient čte stav entity

Následující funkce Azure http implementuje operaci GET pomocí konvencí REST. Přečte aktuální stav entity čítače, jejíž klíč se předává v cestě URL.

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
> Objekt vrácený funkcí `ReadEntityStateAsync` je pouze místní kopie, tj. snímek stavu entity z nějakého dřívějšího bodu v čase. Konkrétně může být zastaralý a změna tohoto objektu nemá žádný vliv na skutečnou entitu. 

### <a name="example-orchestration-first-signals-then-calls-entity"></a>Příklad: první signály Orchestrace a potom volá entitu

Následující orchestrace signalizuje entitě čítače, aby ji přivolala, a potom zavolá stejnou entitu, aby četla nejnovější hodnotu.

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

Rozhraní lze použít pro přístup k entitám prostřednictvím generovaných objektů proxy. Tento přístup zajišťuje, že název a argument typu operace se shodují s tím, co je implementováno. Pokud je to možné, doporučujeme používat rozhraní pro přístup k entitám.

Například můžeme upravit příklad čítače následujícím způsobem:

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

Třídy entit a rozhraní entit jsou podobné zrnam a rozhraním zrn, která jsou oblíbená pomocí [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/). Další informace o podobnostech a rozdílech mezi trvalými entitami a Orleans najdete v tématu [porovnání s virtuálními aktéry](durable-functions-entities.md#comparison-with-virtual-actors).

Kromě poskytování kontroly typu rozhraní jsou užitečná pro lepší oddělení obav v rámci aplikace. Například vzhledem k tomu, že entita může implementovat více rozhraní, může jedna entita obsluhovat více rolí. Kromě toho, vzhledem k tomu, že rozhraní může být implementováno více entitami, mohou být obecné způsoby komunikace implementovány jako opakovaně použitelné knihovny.

### <a name="example-client-signals-entity-through-interface"></a>Příklad: klient signalizuje entitu prostřednictvím rozhraní.

Klientský kód může použít `SignalEntityAsync<TEntityInterface>` k odeslání signálů do entit, které implementují `TEntityInterface` . Například:

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

V tomto příkladu `proxy` je parametr dynamicky generovanou instancí `ICounter` , která interně překládá volání `Delete` do do signálu.

> [!NOTE]
> `SignalEntityAsync`Rozhraní API je možné použít pouze pro jednosměrné operace. I v případě, že operace se vrátí `Task<T>` , hodnota `T` parametru bude vždy null nebo `default` , nikoli skutečný výsledek.
Například nemá smysl signalizovat `Get` operaci, protože není vrácena žádná hodnota. Místo toho mohou klienti použít buď `ReadStateAsync` pro přístup ke stavu čítače přímo, nebo mohou spustit funkci Orchestrator, která volá `Get` operaci. 

### <a name="example-orchestration-first-signals-then-calls-entity-through-proxy"></a>Příklad: první signály Orchestrace a pak volají entitu prostřednictvím proxy

Chcete-li volat nebo signalizovat entitu v rámci orchestrace, `CreateEntityProxy` lze použít společně s typem rozhraní k vygenerování proxy serveru pro danou entitu. Tento proxy server pak můžete použít k volání nebo signalizaci operací:

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

Implicitně se všechny operace, které vrací, `void` signalizují a všechny operace, které vrací `Task` nebo `Task<T>` jsou volány. Jeden může změnit toto výchozí chování a signalizovat operace, i když vrátí úlohu, pomocí `SignalEntity<IInterfaceType>` metody explicitně.

### <a name="shorter-option-for-specifying-the-target"></a>Kratší možnost pro určení cíle

Při volání nebo signalizaci entity pomocí rozhraní musí první argument určovat cílovou entitu. Cíl lze zadat zadáním ID entity, nebo v případě, že existuje pouze jedna třída, která implementuje entitu, pouze klíč entity:

```csharp
context.SignalEntity<ICounter>(new EntityId(nameof(Counter), "myCounter"), ...);
context.SignalEntity<ICounter>("myCounter", ...);
```

Je-li zadán pouze klíč entity a v době běhu nelze nalézt jedinečnou implementaci, `InvalidOperationException` je vyvolána. 

### <a name="restrictions-on-entity-interfaces"></a>Omezení pro rozhraní entit

V obvyklých případech musí být všechny parametry a návratové typy zaserializovatelnýy do formátu JSON. V opačném případě jsou výjimky serializace generovány za běhu.

Vynutili jsme také některá další pravidla:
* Rozhraní entit musí definovat pouze metody.
* Rozhraní entit nesmí obsahovat obecné parametry.
* Metody rozhraní entit nesmí mít více než jeden parametr.
* Metody rozhraní entit musí vracet `void` , `Task` nebo. `Task<T>` 

V případě porušení některého z těchto pravidel `InvalidOperationException` je vyvolána za běhu, když je rozhraní použito jako argument typu `SignalEntity` nebo `CreateProxy` . Zpráva výjimky vysvětluje, které pravidlo bylo přerušeno.

> [!NOTE]
> Metody rozhraní vracející `void` lze pouze signalizovat (jednosměrně), nikoli volat (obousměrný). Metody rozhraní vracejí `Task` nebo `Task<T>` mohou být buď volány nebo správcem signalizována. Při volání vrátí výsledek operace nebo znovu vyvolá výjimky vyvolané operací. Nicméně pokud správcem signalizována, nevrátí skutečný výsledek nebo výjimku z operace, ale pouze výchozí hodnotu.

## <a name="entity-serialization"></a>Serializace entity

Vzhledem k tomu, že stav entity je trvale trvalé, Třída entity musí být serializovatelný. Modul runtime Durable Functions používá pro tento účel knihovnu [JSON.NET](https://www.newtonsoft.com/json) , která podporuje řadu zásad a atributů pro řízení procesu serializace a deserializace. Nejčastěji používané datové typy jazyka C# (včetně polí a typů kolekcí) jsou již serializovatelný a lze je snadno použít pro definování stavu trvalých entit.

Například Json.NET může snadno serializovat a deserializovat následující třídu:

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

V předchozím příkladu jsme se rozhodli zahrnout několik atributů, aby se podkladová serializace lépe zobrazila:
- Třídu přiřadíme s `[JsonObject(MemberSerialization.OptIn)]` cílem připomenout nám, že třída musí být serializovatelný a zachovat pouze členy, které jsou explicitně označeny jako vlastnosti JSON.
-  Pole, která se mají zachovat, přihlásíme `[JsonProperty("name")]` tak, aby se nám připomenout, že pole je součástí trvalého stavu entity, a jestli chcete zadat název vlastnosti, která se má použít v reprezentaci JSON.

Tyto atributy se ale nevyžadují. jiné konvence nebo atributy jsou povoleny, pokud pracují s Json.NET. Například jeden může používat `[DataContract]` atributy nebo vůbec žádné atributy:

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

Ve výchozím *nastavení není název třídy uložen jako* součást reprezentace JSON: to znamená, že používáme `TypeNameHandling.None` jako výchozí nastavení. Toto výchozí chování lze přepsat pomocí `JsonObject` atributů nebo `JsonProperty` .

### <a name="making-changes-to-class-definitions"></a>Provádění změn v definicích tříd

Při provádění změn definice třídy po spuštění aplikace se vyžaduje určitá péče, protože uložený objekt JSON se už nemusí shodovat s definicí nové třídy. Stále je často možné pracovat správně se změnou formátů dat, pokud jedna rozumí procesu deserializace používaného nástrojem `JsonConvert.PopulateObject` .

Například zde jsou některé příklady změn a jejich účinek:

1. Pokud je přidána nová vlastnost, která není přítomna v uloženém formátu JSON, předpokládá, že její výchozí hodnota je.
1. Pokud je odebrána vlastnost, která je k dispozici v uloženém formátu JSON, předchozí obsah bude ztracen.
1. Pokud je vlastnost přejmenována, efekt je jako při odebrání starého a přidání nového.
1. Pokud je typ vlastnosti změněn, takže již nelze deserializovat z uloženého formátu JSON, je vyvolána výjimka.
1. Pokud se změní typ vlastnosti, ale je možné ji i nadále deserializovat z uloženého formátu JSON, bude to mít.

K dispozici je celá řada možností pro přizpůsobení chování Json.NET. Například chcete-li vynutit výjimku, pokud uložený kód JSON obsahuje pole, které není přítomno ve třídě, zadejte atribut `JsonObject(MissingMemberHandling = MissingMemberHandling.Error)` . Je také možné napsat vlastní kód pro deserializaci, který může číst JSON uložený v libovolných formátech.

## <a name="entity-construction"></a>Vytváření entit

Někdy chceme mít větší kontrolu nad tím, jak se objekty entit vytvářejí. Nyní popisujeme několik možností, jak změnit výchozí chování při vytváření objektů entit. 

### <a name="custom-initialization-on-first-access"></a>Vlastní inicializace při prvním přístupu

Občas musíme provést několik zvláštních inicializací před odesláním operace do entity, která nikdy nebyla k dispozici, nebo která byla odstraněna. Chcete-li určit toto chování, může jedna přidat podmíněný objekt před `DispatchAsync` :

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

### <a name="bindings-in-entity-classes"></a>Vazby v třídách entit

Na rozdíl od regulárních funkcí nemají metody třídy entit přímý přístup k vstupní a výstupní vazbě. Místo toho musí být vazba dat zachycena v deklaraci funkce vstupního bodu a pak předána `DispatchAsync<T>` metodě. Všechny předané objekty `DispatchAsync<T>` budou automaticky předány do konstruktoru třídy entity jako argument.

Následující příklad ukazuje, jak `CloudBlobContainer` lze zpřístupnit odkaz ze [vstupní vazby objektu BLOB](../functions-bindings-storage-blob-input.md) pro entitu založenou na třídě.

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

Další informace o vazbách v Azure Functions naleznete v dokumentaci k [aktivačním událostem Azure functions a vazebm](../functions-triggers-bindings.md) .

### <a name="dependency-injection-in-entity-classes"></a>Vkládání závislostí v třídách entit

Třídy entit podporují [vkládání závislostí Azure Functions](../functions-dotnet-dependency-injection.md). Následující příklad ukazuje, jak zaregistrovat `IHttpClientFactory` službu do entity založené na třídě.

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

Následující fragment kódu ukazuje, jak začlenit vloženou službu do vaší třídy entit.

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
> Chcete-li se vyhnout problémům s serializací, nezapomeňte vyloučit pole, která jsou určena k ukládání vložených hodnot z serializace.

> [!NOTE]
> Na rozdíl od při použití injektáže konstruktoru v běžném Azure Functions .NET *musí* být deklarována metoda vstupního bodu pro entity založené na třídě `static` . Deklarace vstupního bodu nestatické funkce může způsobit konflikty mezi normálním inicializátorem objektu Azure Functions a inicializátorem objektu trvalé entity.

## <a name="function-based-syntax"></a>Syntaxe založená na funkcích

Zatím jsme se zaměřili na syntaxi založenou na třídě, protože očekáváme, že bude vhodnější pro většinu aplikací. Syntaxe založená na funkcích ale může být vhodná pro aplikace, které chtějí definovat nebo spravovat své vlastní abstrakce pro stav a operace entity. Také může být vhodné při implementaci knihoven, které vyžadují obecu, která není aktuálně podporována syntaxí založenou na třídě. 

Pomocí syntaxe založené na funkci entita explicitně zpracovává odeslání operace a explicitně spravuje stav entity. Například následující kód ukazuje, že je entita *čítače* implementována pomocí syntaxe založené na funkci.  

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
        case "delete":
            ctx.DeleteState();
            break;
    }
}
```

### <a name="the-entity-context-object"></a>Objekt kontextu entity

K funkcím specifickým pro entitu lze přistupovat prostřednictvím objektu kontextu typu `IDurableEntityContext` . Tento kontextový objekt je k dispozici jako parametr pro funkci entity a prostřednictvím vlastnosti Async-Local `Entity.Current` .

Následující členové poskytují informace o aktuální operaci a nám umožňují zadat návratovou hodnotu. 

* `EntityName`: název aktuálně spuštěné entity.
* `EntityKey`: klíč aktuálně vykonávané entity.
* `EntityId`: ID aktuálně vykonávané entity (včetně názvu a klíče).
* `OperationName`: název aktuální operace.
* `GetInput<TInput>()`: Získá vstup pro aktuální operaci.
* `Return(arg)`: vrátí hodnotu do orchestrace, která volala operaci.

Následující členové spravují stav entity (vytvořit, číst, aktualizovat, odstranit). 

* `HasState`: zda entita existuje, to znamená, že má nějaký stav. 
* `GetState<TState>()`: načte aktuální stav entity. Pokud ještě neexistuje, vytvoří se.
* `SetState(arg)`: vytvoří nebo aktualizuje stav entity.
* `DeleteState()`: odstraní stav entity, pokud existuje. 

Pokud je stav vrácený `GetState` objektem, může být přímo upraven pomocí kódu aplikace. Nemusíte volat `SetState` znovu na konci (ale také bez poškození). Pokud `GetState<TState>` je voláno vícekrát, je nutné použít stejný typ.

Nakonec se k signalizaci jiných entit používají následující členové, nebo začnou nové orchestrace:

* `SignalEntity(EntityId, operation, input)`: pošle jednosměrnou zprávu entitě.
* `CreateNewOrchestration(orchestratorFunctionName, input)`: spustí novou orchestraci.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o konceptech entit](durable-functions-entities.md)
