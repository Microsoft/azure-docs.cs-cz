---
title: Funkce ve verzi Preview Durable Functions – Azure Functions
description: Přečtěte si o funkcích verze Preview pro Durable Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: article
ms.date: 07/08/2019
ms.author: azfuncdf
ms.openlocfilehash: 94784ee9a72d11e168828f1543cbe62219ac4a74
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097940"
---
# <a name="durable-functions-20-preview-azure-functions"></a>Durable Functions 2,0 Preview (Azure Functions)

*Durable Functions* je rozšíření [Azure Functions](../functions-overview.md) a [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) , které umožňuje psát stavové funkce v prostředí bez serveru. Toto rozšíření za vás spravuje stav, kontrolní body a restartování. Pokud ještě neznáte Durable Functions, přečtěte si [dokumentaci přehled](durable-functions-overview.md).

Durable Functions 1. x je funkce Azure Functions GA (všeobecně dostupná), ale také obsahuje několik dílčích funkcí, které jsou aktuálně ve verzi Public Preview. Tento článek popisuje nově vydané funkce verze Preview a obsahuje podrobnosti o tom, jak fungují a jak je můžete začít používat.

> [!NOTE]
> Tyto funkce verze Preview jsou součástí vydání Durable Functions 2,0, což je aktuálně **verze Preview** s několika zásadními změnami. Sestavení balíčku Azure Functions trvalého rozšíření lze najít v nuget.org s verzemi ve formě **2.0.0-betaX**. Tato sestavení nejsou určena pro produkční úlohy a následné verze mohou obsahovat další zásadní změny.

## <a name="breaking-changes"></a>Změny způsobující chyby

V Durable Functions 2,0 se zavádí několik přerušujících změn. Neočekává se, že existující aplikace budou kompatibilní s Durable Functions 2,0 bez změny kódu. V této části jsou uvedené některé změny:

### <a name="hostjson-schema"></a>Schéma Host. JSON

Následující fragment kódu ukazuje nové schéma pro Host. JSON. Hlavní změny, které je třeba znát, jsou nové pododdíly:

* `"storageProvider"`(a `"azureStorage"` dílčí část) pro konfiguraci specifickou pro úložiště
* `"tracking"`pro sledování a konfiguraci protokolování
* `"notifications"`(a `"eventGrid"` dílčí část) pro konfiguraci oznámení služby Event Grid

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "azureStorage": {
          "connectionStringName": <string>,
          "controlQueueBatchSize": <int?>,
          "partitionCount": <int?>,
          "controlQueueVisibilityTimeout": <hh:mm:ss?>,
          "workItemQueueVisibilityTimeout": <hh:mm:ss?>,
          "trackingStoreConnectionStringName": <string?>,
          "trackingStoreNamePrefix": <string?>,
          "maxQueuePollingInterval": <hh:mm:ss?>
        }
      },
      "tracking": {
        "traceInputsAndOutputs": <bool?>,
        "traceReplayEvents": <bool?>,
      },
      "notifications": {
        "eventGrid": {
          "topicEndpoint": <string?>,
          "keySettingName": <string?>,
          "publishRetryCount": <string?>,
          "publishRetryInterval": <hh:mm:ss?>,
          "publishRetryHttpStatus": <int[]?>,
          "publishEventTypes": <string[]?>,
        }
      },
      "maxConcurrentActivityFunctions": <int?>,
      "maxConcurrentOrchestratorFunctions": <int?>,
      "extendedSessionsEnabled": <bool?>,
      "extendedSessionIdleTimeoutInSeconds": <int?>,
      "customLifeCycleNotificationHelperType": <string?>
  }
}
```

Jak Durable Functions 2,0 pokračuje ve stabilizaci, do `durableTask` oddílu Host. JSON budou zavedeny další změny. Další informace o těchto změnách najdete v [tomto problému GitHubu](https://github.com/Azure/azure-functions-durable-extension/issues/641).

### <a name="public-interface-changes"></a>Změny veřejného rozhraní

Různé "kontextové" objekty podporované Durable Functions měly abstraktní základní třídy určené pro použití při testování částí. Jako součást Durable Functions 2,0 byly tyto abstraktní základní třídy nahrazeny rozhraními. Kód funkce, který používá konkrétní typy přímo, není ovlivněn.

Následující tabulka představuje hlavní změny:

| Starý typ | Nový typ |
|----------|----------|
| DurableOrchestrationClientBase | IDurableOrchestrationClient |
| DurableOrchestrationContextBase | IDurableOrchestrationContext |
| DurableActivityContextBase | IDurableActivityContext |

V případě, kdy abstraktní základní třída obsahovala virtuální metody, byly tyto virtuální metody nahrazeny metodami rozšíření definovanými `DurableContextExtensions`v.

## <a name="entity-functions"></a>Funkce entit

Funkce entit definují operace pro čtení a aktualizaci malých částí stavu, označovaných jako *odolné entity*. Podobně jako funkce nástroje Orchestrator jsou funkce entit funkce se speciálním typem triggeru, *triggerem entity*. Na rozdíl od funkcí Orchestrator nemají entity Functions žádná konkrétní omezení kódu. Funkce entit také spravují stav explicitně namísto implicitního reprezentace stavu prostřednictvím řízení toku.

### <a name="net-programing-models"></a>Modely programování .NET

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

Tento model funguje nejlépe pro jednoduché implementace entit nebo implementace, které mají dynamickou sadu operací. Existují však i programovací model založený na třídě, který je užitečný pro entity, které jsou statické, ale mají složitější implementace. Následující příklad je ekvivalentní implementace `Counter` entity pomocí tříd a metod .NET.

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

Model založený na třídě je podobný programovacímu modelu, který je oblíbený pomocí [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/). V tomto modelu je typ entity definován jako třída .NET. Každá metoda třídy je operace, kterou může vyvolat externí klient. Na rozdíl od Orleans jsou však rozhraní .NET volitelná. Předchozí příklad *čítače* nepoužil rozhraní, ale může být stále vyvolán prostřednictvím jiných funkcí nebo prostřednictvím volání HTTP API.

K *instancím* entit se dostanete prostřednictvím jedinečného identifikátoru *ID entity*. ID entity je jednoduše dvojice řetězců, které jedinečně identifikují instanci entity. Skládá se z těchto:

* **Název entity**: název, který identifikuje typ entity (například "čítač").
* **Klíč entity**: řetězec, který jedinečně identifikuje entitu mezi všemi ostatními entitami stejného názvu (například GUID).

Například funkce entita *čítače* může být použita k udržení skóre v online hře. Každá instance hry bude mít jedinečné ID entity, například `@Counter@Game1`, `@Counter@Game2`a tak dále.

### <a name="comparison-with-virtual-actors"></a>Porovnání s virtuálními aktéry

Návrh odolných entit je silně ovlivněn modelem objektu [actor](https://en.wikipedia.org/wiki/Actor_model). Pokud už jste obeznámeni s aktéry, měli byste znát koncepty za trvalými entitami. Konkrétně se odolné entity podobají virtuálním [aktérům](https://research.microsoft.com/projects/orleans/) mnoha způsoby:

* Trvalé entity jsou adresovatelné prostřednictvím *ID entity*.
* Trvalé operace s entitami se v jednom okamžiku spouštějí po jednom, aby se zabránilo konfliktům časování.
* Trvalé entity jsou vytvořeny automaticky při jejich volání nebo při signalizaci.
* Neprovádíte-li operace, odolné entity budou tiše odpojeny od paměti.

Existují však některé důležité rozdíly, které je třeba zaznamenat:

* Odolné entity mají přednost před *latencí*, a proto nemusí být vhodná pro aplikace s přísnými požadavky na latenci.
* Zprávy odesílané mezi entitami jsou spolehlivě doručovány a v daném pořadí.
* Odolné entity lze použít ve spojení s trvalými orchestrací a mohou sloužit jako distribuované zámky, které jsou popsány dále v tomto článku.
* Vzory požadavků a odpovědí v entitách jsou omezené na orchestrace. Pro komunikaci mezi entitami se povolují pouze jednosměrné zprávy (označované také jako "signalizace"), stejně jako v původním modelu actor. Toto chování brání distribuovaným zablokování.

### <a name="durable-entity-net-apis"></a>Trvalá entita API .NET

Podpora entit zahrnuje několik rozhraní API. U jednoho je k dispozici nové rozhraní API pro definování funkcí entity, jak je uvedeno výše, které určují, co se má stát při vyvolání operace u entity. Stávající rozhraní API pro klienty a orchestrace se taky aktualizovala o nové funkce pro interakci s entitami.

#### <a name="implementing-entity-operations"></a>Implementace operací entit

Provedení operace s entitou může volat tyto členy objektu Context (`IDurableEntityContext` v rozhraní .NET):

* **Operace**: Získá název operace.
* **GetInput\<TInput >** : Získá vstup pro operaci.
* **GetState\<TState >** : Získá aktuální stav entity.
* **Setstate**: aktualizuje stav entity.
* **SignalEntity**: pošle jednosměrnou zprávu entitě.
* **Samo**: Získá ID entity.
* **Return**: vrátí hodnotu klientovi nebo orchestraci, která volala operaci.
* **IsNewlyConstructed**: vrátí `true` , pokud entita neexistovala před touto operací.
* **DestructOnExit**: odstraní entitu po dokončení operace.

Operace jsou méně omezené než orchestrace:

* Operace můžou volat externí vstupně-výstupní operace pomocí synchronních nebo asynchronních rozhraní API (doporučujeme používat jenom ty asynchronní).
* Operace mohou být nedeterministické. Například je bezpečné volat `DateTime.UtcNow` `Guid.NewGuid()` nebo `new Random()`.

#### <a name="accessing-entities-from-clients"></a>Přístup k entitám z klientů

Trvalé entity lze vyvolat z běžných funkcí prostřednictvím `orchestrationClient` vazby (`IDurableOrchestrationClient` v rozhraní .NET). Podporovány jsou následující metody:

* **ReadEntityStateAsync\<T >** : přečte stav entity.
* **SignalEntityAsync**: pošle jednosměrnou zprávu entitě a počká, až se zazařazuje do fronty.
* **SignalEntityAsync\<T >** : totéž jako `SignalEntityAsync` ale používá vygenerovaný proxy objekt typu `T`.

Předchozí `SignalEntityAsync` volání vyžaduje zadání názvu operace entity `string` jako a datové části operace jako `object`. Následující vzorový kód je příkladem tohoto vzoru:

```csharp
EntityId id = // ...
object amount = 5;
context.SignalEntityAsync(id, "Add", amount);
```

Je také možné vygenerovat proxy objekt pro typově bezpečný přístup. Chcete-li vygenerovat typ proxy bezpečného typu, musí být pro typ entity implementováno rozhraní. Předpokládejme například, že `Counter` entita zmíněná dříve `ICounter` implementovala rozhraní, které je definováno následujícím způsobem:

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

Klientský kód pak může použít `SignalEntityAsync<T>` a `ICounter` zadat rozhraní jako parametr typu pro vygenerování proxy typu s bezpečným typem. Toto použití typu proxy bezpečného typu je znázorněno v následující ukázce kódu:

```csharp
[FunctionName("UserDeleteAvailable")]
public static async Task AddValueClient(
    [QueueTrigger("my-queue")] string message,
    [OrchestrationClient] IDurableOrchestrationClient client)
{
    int amount = int.Parse(message);
    var target = new EntityId(nameof(Counter), "MyCounter");
    await client.SignalEntityAsync<ICounter>(target, proxy => proxy.Add(amount));
}
```

V předchozím příkladu `proxy` je parametr dynamicky generovanou `ICounter`instancí, která interně `Add` překládá volání do do `SignalEntityAsync`ekvivalentního (netypového) volání.

Parametr `SignalEntityAsync<T>` typu má následující omezení:

* Parametr typu musí být rozhraní.
* V rozhraní lze definovat pouze metody. Vlastnosti nejsou podporovány.
* Každá metoda musí definovat buď jeden, nebo žádný parametr.
* Každá metoda musí vracet buď `void`, `Task`nebo `Task<T>` , kde `T` je nějaký typ, který je serializován jako JSON.
* Rozhraní musí být implementováno právě jedním typem v rámci sestavení rozhraní.

Ve většině případů rozhraní, která nesplňují tyto požadavky, způsobí výjimku za běhu.

> [!NOTE]
> Je důležité si uvědomit, že `ReadEntityStateAsync` metody `IDurableOrchestrationClient` a `SignalEntityAsync` upřednostnit výkon nad konzistencí. `ReadEntityStateAsync`může vracet zastaralou hodnotu a `SignalEntityAsync` může vracet před dokončením operace.

#### <a name="accessing-entities-from-orchestrations"></a>Přístup k entitám z orchestrace

Orchestrace mají přístup k entitám `IDurableOrchestrationContext` pomocí objektu. Můžou si vybrat komunikaci mezi jednosměrnou komunikací (požárem a zapomenoutm) a obousměrnou komunikací (žádost a odpověď). Příslušné metody jsou:

* **SignalEntity**: pošle jednosměrnou zprávu entitě.
* **CallEntityAsync**: pošle zprávu entitě a počká na odpověď oznamující, že se operace dokončila.
* **CallEntityAsync\<T >** : pošle zprávu entitě a počká na odpověď obsahující výsledek typu T.

Při použití obousměrné komunikace jsou všechny výjimky vyvolané během provádění operace předány zpět do orchestrace volání a znovu vyvolány. Naopak při použití brány fire a zapomenutí nejsou výjimky pozorovány.

Pro typově bezpečný přístup mohou funkce orchestrace generovat proxy na základě rozhraní. Metodu `CreateEntityProxy` rozšíření lze použít pro tento účel:

```csharp
public interface IAsyncCounter
{
    Task AddAsync(int amount);
    Task ResetAsync();
    Task<int> GetAsync();
}

[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    // ...
    IAsyncCounter proxy = context.CreateEntityProxy<IAsyncCounter>("MyCounter");
    await proxy.AddAsync(5);
    int newValue = await proxy.GetAsync();
    // ...
}
```

V předchozím příkladu se předpokládá, že existuje entita "čítač", která implementuje `IAsyncCounter` rozhraní. Orchestrace potom dokázala použít `IAsyncCounter` definici typu k vygenerování proxy typu pro synchronní interakci s entitou.

### <a name="locking-entities-from-orchestrations"></a>Zamykání entit z orchestrací

Orchestrace můžou uzamknout entity. Tato funkce poskytuje jednoduchý způsob, jak zabránit nežádoucím racesům pomocí *důležitých oddílů*.

Kontextový objekt poskytuje následující metody:

* **LockAsync**: Získá zámky pro jednu nebo více entit.
* **Uzamknout**: vrátí hodnotu true, pokud je aktuálně v kritické sekci, jinak false.

Kritická část končí a všechny zámky jsou uvolněny, jakmile orchestrace skončí. V rozhraní .NET `LockAsync` vrátí znak `IDisposable` , který ukončí kritickou část, je-li uvolněn, který lze použít společně `using` s klauzulí k získání syntaktické reprezentace důležité části.

Zvažte například orchestraci, která potřebuje otestovat, jestli jsou k dispozici dva přehrávače, a pak je přiřaďte ke hře. Tato úloha se dá implementovat pomocí oddílu kritického pro tyto kroky:

```csharp
[FunctionName("Orchestrator")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext ctx)
{
    EntityId player1 = /* ... */;
    EntityId player2 = /* ... */;

    using (await ctx.LockAsync(player1, player2))
    {
        bool available1 = await ctx.CallEntityAsync<bool>(player1, "is-available");
        bool available2 = await ctx.CallEntityAsync<bool>(player2, "is-available");

        if (available1 && available2)
        {
            Guid gameId = ctx.NewGuid();

            await ctx.CallEntityAsync(player1, "assign-game", gameId);
            await ctx.CallEntityAsync(player2, "assign-game", gameId);
        }
    }
}
```

V části důležité jsou obě entity přehrávače uzamčené, což znamená, že neprovádějí žádné jiné operace než ty, které jsou volány v kritické části. Toto chování brání v Races s konfliktními operacemi, jako jsou například hráči přiřazené k jiné hře nebo odhlášení.

V případě, že je možné použít kritické oddíly, ukládáme několik omezení. Tato omezení slouží k tomu, aby se zabránilo zablokování a Vícenásobný přístup.

* Kritické oddíly nelze vnořovat.
* Kritické oddíly nemůžou vytvářet podorchestry.
* Kritické oddíly mohou volat pouze entity, které byly uzamčeny.
* Kritické oddíly nemohou volat stejnou entitu pomocí více paralelních volání.
* Kritické oddíly mohou signalizovat pouze entity, které nebyly uzamčeny.

## <a name="alternate-storage-providers"></a>Alternativní poskytovatelé úložiště

Prostředí trvalého úkolu podporuje více poskytovatelů úložiště, včetně [Azure Storage](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage), [Azure Service Bus](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus), emulátoru [v paměti](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator)a experimentálního poskytovatele [Redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis) . Nicméně až do této chvíle bude rozšíření trvalé úlohy pro Azure Functions podporovalo pouze poskytovatele Azure Storage. Počínaje Durable Functions 2,0 se přidávají podpora alternativních poskytovatelů úložiště, počínaje poskytovatelem Redis.

> [!NOTE]
> Durable Functions 2,0 podporuje pouze poskytovatele kompatibilního s .NET Standard 2,0. V době psaní nepodporují poskytovatel Azure Service Bus .NET Standard 2,0, a proto není k dispozici jako alternativní poskytovatel úložiště.

### <a name="emulator"></a>Hlavní

Zprostředkovatel [DurableTask. emulátoru](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/) je místní paměť, neodolný poskytovatel úložiště vhodný pro místní testovací scénáře. Dá se nakonfigurovat pomocí následujícího schématu minimale **Host. JSON** :

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "emulator": { }
      }
    }
  }
}
```

### <a name="redis-experimental"></a>Redis (experimentální)

Poskytovatel [DurableTask. Redis](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Redis/) uchovává všechny stavy orchestrace v nakonfigurovaném clusteru Redis.

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "redis": {
          "connectionStringName": <string>,
        }
      }
    }
  }
}
```

`connectionStringName` Musí odkazovat na název nastavení aplikace nebo proměnné prostředí. Toto nastavení aplikace nebo proměnná prostředí by měla obsahovat hodnotu připojovacího řetězce Redis ve formě *serveru: port*. Například `localhost:6379` pro připojení k místnímu clusteru Redis.

> [!NOTE]
> Poskytovatel Redis je aktuálně experimentální a podporuje jenom aplikace Function App běžící na jednom uzlu. Není zaručeno, že poskytovatel Redis bude někdy zpřístupněn všeobecně k dispozici a bude možné ho v budoucí verzi odebrat.
