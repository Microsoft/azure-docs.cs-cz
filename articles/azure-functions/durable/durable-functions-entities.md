---
title: Trvalé entity – Azure Functions
description: Seznamte se s trvalými entitami a jejich použitím v rozšíření Durable Functions pro Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: 99e61cef55bd97704063e4d2da90909d0376c327
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70961464"
---
# <a name="entity-functions-preview"></a>Funkce entit (Preview)

Funkce entit definují operace pro čtení a aktualizaci malých částí stavu, označovaných jako *odolné entity*. Podobně jako funkce nástroje Orchestrator jsou funkce entit funkce se speciálním typem triggeru, *triggerem entity*. Na rozdíl od funkcí Orchestrator nemají entity Functions žádné konkrétní omezení kódu. Funkce entit také spravují stav explicitně namísto implicitního reprezentace stavu prostřednictvím řízení toku.

> [!NOTE]
> Funkce entit a související funkce jsou dostupné jenom v Durable Functions 2,0 a novějších. Funkce entit jsou momentálně ve verzi Public Preview.

## <a name="entity-identity"></a>Identita entity

Entity (někdy označované jako *instance*entit) jsou k dispozici prostřednictvím jedinečného identifikátoru, *ID entity*. ID entity je jednoduše dvojice řetězců, které jedinečně identifikují instanci entity. Skládá se z těchto:

* **Název entity**: název, který identifikuje typ entity (například "čítač").
* **Klíč entity**: řetězec, který jedinečně identifikuje entitu mezi všemi ostatními entitami stejného názvu (například GUID).

Například funkce entita *čítače* může být použita k udržení skóre v online hře. Každá instance hry bude mít jedinečné ID entity, například `@Counter@Game1`, `@Counter@Game2`a tak dále. Všechny operace, které cílí na konkrétní entitu, vyžadují zadání ID entity jako parametru.

## <a name="programming-models"></a>Programovací modely

Trvalé entity podporují dva různé programovací modely. Prvním modelem je dynamický model "funkční", kde je entita definovaná jedinou funkcí. Druhý model je objektově orientovaný model, kde je entita definována třídou a metodami. Tyto modely a programovací modely pro interakci s entitami jsou popsány v následujících částech.

### <a name="defining-entities"></a>Definování entit

Existují dva volitelné programovací modely pro vytváření trvalých entit. Následující kód je příkladem jednoduché entity *čítače* implementované jako standardní funkce. Tato funkce definuje tři *operace*, `add` `reset`, a `get`, `currentValue`každý z nich pracuje s hodnotou celočíselného stavu.

```csharp
[FunctionName("Counter")]
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

Tento model funguje nejlépe pro jednoduché implementace entit nebo implementace, které mají dynamickou sadu operací. Můžete však také použít programovací model založený na třídě, který je užitečný pro entity, které jsou statické, ale mají složitější implementace. Následující příklad je ekvivalentní implementace `Counter` entity pomocí tříd a metod.

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

V programovacím modelu `IDurableEntityContext` založeném na třídě je objekt k dispozici `Entity.Current` ve statické vlastnosti.

Model založený na třídě je podobný programovacímu modelu, který je oblíbený pomocí [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/). V tomto modelu je typ entity definován jako třída .NET. Každá metoda třídy je operace, kterou může vyvolat externí klient. Na rozdíl od Orleans jsou však rozhraní .NET volitelná. Předchozí příklad *čítače* nepoužil rozhraní, ale může být stále vyvolán prostřednictvím jiných funkcí nebo prostřednictvím volání HTTP API.

> [!NOTE]
> Funkce triggeru entit jsou k dispozici v Durable Functions 2,0 a vyšších. V současné době jsou funkce triggeru entit k dispozici pouze aplikacím funkcí .NET.

### <a name="accessing-entities-from-clients"></a>Přístup k entitám z klientů

Trvalé entity lze vyvolat nebo dotazovat z běžných funkcí – také označované jako *funkce klienta* – pomocí [výstupní vazby klienta entity](durable-functions-bindings.md#entity-client). Následující příklad ukazuje funkce aktivované frontou *, která tuto* vazbu používá.

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

> [!NOTE]
> Funkce .NET podporují pouze volně typované i typově bezpečné metody pro signalizaci entit. Podrobnosti najdete v referenční dokumentaci k [vazbě klienta entit](durable-functions-bindings.md#entity-client-usage) .

Termínový *signál* znamená, že volání rozhraní API entity je jednosměrné a asynchronní. Není možné, aby *klientská funkce* věděla, kdy entita tuto operaci zpracovala, ani když není možné, aby funkce entity vrátila hodnotu do klientské funkce. Jednosměrné zasílání zpráv založené na frontě bylo záměrné rozhodnutí, které umožňuje trvalým entitám upřednostnit odolnost nad výkonem. Tato volba návrhu představuje jednu z kompromisů trvalých entit ve srovnání s jinými podobnými technologiemi. V současné době pouze orchestrace umožňují manipulaci s návratovou hodnotou z entit, jak je popsáno v následující části.

Funkce klienta se také mohou dotazovat na stav entit, jak je znázorněno v následujícím příkladu:

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

Dotazy na stav entity se odesílají do trvalého úložiště sledování a vracejí poslední *trvalý* stav entity. Je možné, že vrácený stav může být zastaralý ve srovnání se stavem v paměti entity. Pouze orchestrace mohou číst stav v paměti entity, jak je popsáno v následující části.

### <a name="accessing-entities-from-orchestrations"></a>Přístup k entitám z orchestrace

Funkce Orchestrator mají přístup k entitám pomocí rozhraní API ve [vazbě triggeru orchestrace](durable-functions-bindings.md#orchestration-trigger). Funkce Orchestrator si můžou vybrat mezi jednosměrnou komunikací (požár a zapomenutí, označovanou také jako *signalizace*) a obousměrnou komunikací (žádost a odpověď označovaná také jako *volání*). Následující příklad kódu ukazuje *volání* funkce Orchestrator a *signalizaci* entity *čítače* .

```csharp
[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");

    // Synchronous call to the entity which returns a value
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");
    if (currentValue < 10)
    {
        // Asynchronous call which updates the value
        await context.SignalEntityAsync<int>(entityId, "Add", 1);
    }
}
```

Pouze orchestrace jsou schopny volat entity a získat odpověď, což může být buď návratová hodnota, nebo výjimka. Klientské funkce využívající [vazbu klienta](durable-functions-bindings.md#entity-client) mohou *signalizovat* pouze entity.

> [!NOTE]
> Volání entity z funkce orchestrtor je podobné volání [funkce aktivity](durable-functions-types-features-overview.md#activity-functions) z funkce Orchestrator. Hlavním rozdílem je, že funkce entit jsou trvalé objekty s adresou ( *ID entity*) a podporují zadání názvu operace. Funkce aktivit na druhé straně jsou bezstavové a nemají koncept operací.

### <a name="dependency-injection-in-entity-classes-net"></a>Injektáže v závislostech v třídách entit (.NET)

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

    // The function entry point must be declared static
    [FunctionName(nameof(HttpEntity))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<HttpEntity>();
}
```

> [!NOTE]
> Na rozdíl od při použití injektáže konstruktoru v běžném Azure Functions .NET *musí* být deklarována `static`metoda vstupního bodu pro entity založené na třídě. Deklarace vstupního bodu nestatické funkce může způsobit konflikty mezi normálním inicializátorem objektu Azure Functions a inicializátorem objektu trvalé entity.

### <a name="bindings-in-entity-classes-net"></a>Vazby v třídách entit (.NET)

Na rozdíl od regulárních funkcí nemají metody třídy entit přímý přístup k vstupní a výstupní vazbě. Místo toho musí být vazba dat zachycena v deklaraci funkce vstupního bodu a pak předána `DispatchAsync<T>` metodě. Všechny předané `DispatchAsync<T>` objekty budou automaticky předány do konstruktoru třídy entity jako argument.

Následující příklad ukazuje, jak `CloudBlobContainer` lze zpřístupnit odkaz ze [vstupní vazby objektu BLOB](../functions-bindings-storage-blob.md#input) pro entitu založenou na třídě.

```csharp
public class BlobBackedEntity
{
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

## <a name="entity-coordination"></a>Koordinace entit

Může nastat situace, kdy potřebujete koordinovat operace mezi několika entitami. Například v bankovní aplikaci můžete mít entity, které představují jednotlivé bankovní účty. Při převádění finančních prostředků z jednoho účtu na jiný je potřeba zajistit, aby měl _zdrojový_ účet dostatečné prostředky a aby se aktualizace _zdrojového_ i _cílového_ účtu prováděly nevhodným způsobem.

### <a name="transfer-funds-example-in-c"></a>Příklad přenosových prostředků vC#

Následující příklad kódu přenáší prostředky mezi dvěma entitami _účtu_ pomocí funkce Orchestrator. Koordinace aktualizací entit vyžaduje použití `LockAsync` metody k vytvoření _kritické části_ v orchestraci:

> [!NOTE]
> V zájmu jednoduchosti tento příklad znovu používá `Counter` dříve definovanou entitu. V reálné aplikaci je ale lepší definovat `BankAccount` podrobnější entitu.

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

V rozhraní .NET `LockAsync` vrátí objekt `IDisposable` , který ukončí kritickou část při uvolnění. Tento `IDisposable` výsledek lze použít společně `using` s blokem pro získání syntaktické reprezentace oddílu kritického.

V předchozím příkladu funkce Orchestrator přenesla prostředky ze _zdrojové_ entity na _cílovou_ entitu. Metoda uzamkl entity _zdrojového_ i cílového účtu. `LockAsync` Toto uzamykání zajišťuje, že žádný jiný klient by nemohl zadat dotaz nebo změnit stav obou účtů, dokud logika orchestrace neukončila _kritickou část_ na konci `using` příkazu. To účinně zabránilo tomu, aby bylo možné přečerpání ze _zdrojového_ účtu.

### <a name="critical-section-behavior"></a>Kritické chování oddílu

Metoda vytvoří _kritickou část_ v orchestraci. `LockAsync` Tyto _kritické oddíly_ zabraňují jiným orchestraci v provádění překrývajících se změn v zadané sadě entit. `LockAsync` Rozhraní API interně odesílá operace "uzamknout" do entit a vrátí, když obdrží zprávu s odpovědí "zámek" ze všech těchto stejných entit. *Zámky* i *odemknutí* jsou integrované operace podporované všemi entitami.

V entitě, která je v uzamčeném stavu, nejsou povoleny žádné operace od jiných klientů. Toto chování zajistí, že entita může současně uzamknout pouze jedna instance orchestrace. Pokud se volající pokusí vyvolat operaci u entity, když je uzamčena orchestrací, tato operace bude umístěna do *fronty čekajících operací*. Dokud orchestrace podniku neuvolní zámek, nebudou zpracovány žádné probíhající operace.

> [!NOTE] 
> To se mírně liší od primitiv synchronizace používaných ve většině programovacích jazyků, jako je `lock` například příkaz v. C# Například v C# `lock` příkazu musí být příkaz použit všemi vlákny k zajištění správného synchronizaci napříč více vlákny. Entity ale nevyžadují, aby všichni volající explicitně _zamkli_ entitu. Pokud nějaký volající zamkne entitu, všechny ostatní operace v této entitě se zablokují a zařadí se do fronty za tímto zámkem.

Zámky entit jsou trvalé, takže budou uchovány i v případě, že je spuštěn proces recyklován. Zámky jsou interně trvale zachované jako součást trvalého stavu entity.

### <a name="critical-section-restrictions"></a>Kritická omezení oddílů

V případě, že je možné použít kritické oddíly, ukládáme několik omezení. Tato omezení slouží k tomu, aby se zabránilo zablokování a Vícenásobný přístup.

* Kritické oddíly nelze vnořovat.
* Kritické oddíly nemůžou vytvářet podorchestry.
* Kritické oddíly mohou volat pouze entity, které byly uzamčeny.
* Kritické oddíly nemohou volat stejnou entitu pomocí více paralelních volání.
* Kritické oddíly mohou signalizovat pouze entity, které nebyly uzamčeny.

## <a name="comparison-with-virtual-actors"></a>Porovnání s virtuálními aktéry

Mnohé z funkcí trvalé entity nechte inspirovat [model actor](https://en.wikipedia.org/wiki/Actor_model). Pokud jste již obeznámeni s nástrojem Actors, můžete pochopit mnoho konceptů popsaných v tomto článku. Konkrétně se odolné entity podobají virtuálním [aktérům](https://research.microsoft.com/projects/orleans/) mnoha způsoby:

* Trvalé entity jsou adresovatelné prostřednictvím *ID entity*.
* Trvalé operace s entitami se v jednom okamžiku spouštějí po jednom, aby se zabránilo konfliktům časování.
* Trvalé entity jsou vytvořeny automaticky při jejich volání nebo při signalizaci.
* Neprovádíte-li operace, odolné entity budou tiše odpojeny od paměti.

Existují však některé důležité rozdíly, které je třeba zaznamenat:

* Odolné entity mají *přednost* před *latencí*, a proto nemusí být vhodná pro aplikace s přísnými požadavky na latenci.
* Zprávy odesílané mezi entitami jsou spolehlivě doručovány a v daném pořadí.
* Odolné entity lze použít ve spojení s trvalými orchestrací a podporují mechanismy distribuovaného zamykání.
* Vzory požadavků a odpovědí v entitách jsou omezené na orchestrace. Pro komunikaci mezi *entitami* a entitami *mezi entitami* se povoluje pouze jednosměrné zasílání zpráv (označované také jako "signalizace"), stejně jako v původním modelu actor. Toto chování brání distribuovaným zablokování.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o centrech úloh](durable-functions-task-hubs.md)