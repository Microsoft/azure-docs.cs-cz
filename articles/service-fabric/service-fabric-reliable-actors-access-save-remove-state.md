---
title: Správa stavu Azure Service Fabric | Microsoft Docs
description: Zjistěte, jak pro přístup, uložit a odeberte stavu Service Fabric Reliable Actors.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 37cf466a-5293-44c0-a4e0-037e5d292214
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: c00805fc5d8515fb743256e35a75be1546483245
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="access-save-and-remove-reliable-actors-state"></a>Přístup k, uložte a odebrat stavu Reliable Actors
[Reliable Actors](service-fabric-reliable-actors-introduction.md) jsou jedním podprocesem objekty, které můžete zapouzdření logiku a stavu a spolehlivě Udržovat stav. Všechny instance objektu actor má svou vlastní [správce stavu](service-fabric-reliable-actors-state-management.md): jako slovník datová struktura, která spolehlivě uchová dvojice klíč/hodnota. Správce stavu je obálku kolem zprostředkovatele stavu. Slouží k ukládání dat bez ohledu na to, které [trvalost nastavení](service-fabric-reliable-actors-state-management.md#state-persistence-and-replication) se používá.

Správce stavu klíče musí být řetězce. Hodnoty jsou obecné a mohou být jakéhokoli typu, včetně vlastních typů. Hodnotami uloženými v správce stavu musí být serializovatelný kontrakt dat, protože může přenést přes síť do dalších uzlů během replikace a může zapsat na disk, v závislosti na nastavení trvalost stavu objektu actor.

Správce stavu zpřístupní běžné metody slovník pro správu stavu, podobné těm, které jsou obsaženy ve slovníku pro spolehlivé.

Informace najdete v tématu [osvědčené postupy při správě stavu objektu actor](service-fabric-reliable-actors-state-management.md#best-practices).

## <a name="access-state"></a>Stav přístupu
Stav je přístupné přes správce stavu podle klíče. Metody správce stavu jsou všechny asynchronní, protože v/v disku může vyžadují při aktéři držena formou stavu. Při prvním přístupu jsou uložená v mezipaměti objektů stavu. Opakujte přístup operations přístup k objektům přímo z paměti a vrátí synchronně, aniž by docházelo k vstupně-výstupní diskové nebo asynchronní režií přepínání kontextu. Stav objektu se odebere z mezipaměti v následujících případech:

* Po ho načte objekt z správce stavu, vyvolá metoda objektu actor k neošetřené výjimce.
* Objekt actor je znovu aktivovat, po právě deaktivována nebo po selhání.
* Zprostředkovatel stavu stránky stavu na disk. Tento postup závisí na implementaci zprostředkovatele stavu. Výchozí zprostředkovatel stavu pro `Persisted` nastavení je toto chování.

Stav můžete načíst pomocí standardního *získat* operace, která vyvolá `KeyNotFoundException`(C#) nebo `NoSuchElementException`(Java), pokud položka neexistuje pro klíč:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<int> GetCountAsync()
    {
        return this.StateManager.GetStateAsync<int>("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().getStateAsync("MyState");
    }
}
```

Můžete také načíst stav pomocí *TryGet* metoda, která nevyvolá výjimku pokud záznam neexistuje pro klíč:

```csharp
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task<int> GetCountAsync()
    {
        ConditionalValue<int> result = await this.StateManager.TryGetStateAsync<int>("MyState");
        if (result.HasValue)
        {
            return result.Value;
        }

        return 0;
    }
}
```
```Java
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().<Integer>tryGetStateAsync("MyState").thenApply(result -> {
            if (result.hasValue()) {
                return result.getValue();
            } else {
                return 0;
            });
    }
}
```

## <a name="save-state"></a>Uložit stav
Metody načtení stavu manager vrátí odkaz na objekt v místní paměti. Úprava tento objekt v místní paměti samostatně nezpůsobí, je bezpečně uložit. Pokud objekt je načtena z správce stavu a upravit, musíte znovu vložit do Správce stavu bezpečně uložit.

Můžete vložit stavu pomocí nepodmíněné *nastavit*, což je ekvivalentem `dictionary["key"] = value` syntaxe:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task SetCountAsync(int value)
    {
        return this.StateManager.SetStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture setCountAsync(int value)
    {
        return this.stateManager().setStateAsync("MyState", value);
    }
}
```

Stav můžete přidat pomocí *přidat* metoda. Tato metoda vyvolá `InvalidOperationException`(C#) nebo `IllegalStateException`(Java) při pokusu o přidání klíče, která již existuje.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task AddCountAsync(int value)
    {
        return this.StateManager.AddStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().addOrUpdateStateAsync("MyState", value, (key, old_value) -> old_value + value);
    }
}
```

Můžete také přidat stavu pomocí *TryAdd* metoda. Tato metoda nevyvolá výjimku při pokusu o přidání klíče, který již existuje.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task AddCountAsync(int value)
    {
        bool result = await this.StateManager.TryAddStateAsync<int>("MyState", value);

        if (result)
        {
            // Added successfully!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().tryAddStateAsync("MyState", value).thenApply((result)->{
            if(result)
            {
                // Added successfully!
            }
        });
    }
}
```

Na konci metodu objektu actor správce stavu automaticky uloží všechny hodnoty, které byly přidány nebo upraveném operace insert nebo update. "Uložení" může obsahovat uložením na disk a replikace, v závislosti na nastavení použít. Hodnoty, které nebyly upraveny nejsou jako trvalý, nebo replikovat. Pokud byly změněny žádné hodnoty, uložení operace se nic nestane. Pokud ukládání selže, budou zahozeny upravený stav a je znovu původního stavu.

Můžete také uložit stav ručně voláním `SaveStateAsync` metodu objektu actor základní:

```csharp
async Task IMyActor.SetCountAsync(int count)
{
    await this.StateManager.AddOrUpdateStateAsync("count", count, (key, value) => count > value ? count : value);

    await this.SaveStateAsync();
}
```
```Java
interface MyActor {
    CompletableFuture setCountAsync(int count)
    {
        this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value).thenApply();

        this.stateManager().saveStateAsync().thenApply();
    }
}
```

## <a name="remove-state"></a>Odebrat stavu
Můžete odebrat stavu trvale ze Správce stavu objektu actor voláním *odebrat* metoda. Tato metoda vyvolá `KeyNotFoundException`(C#) nebo `NoSuchElementException`(Java) při pokusu o odstranění klíče, který neexistuje.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task RemoveCountAsync()
    {
        return this.StateManager.RemoveStateAsync("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().removeStateAsync("MyState");
    }
}
```

Rovněž můžete odebrat stavu trvale pomocí *TryRemove* metoda. Tato metoda nevyvolá výjimku při pokusu o odebrání klíče, který neexistuje.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task RemoveCountAsync()
    {
        bool result = await this.StateManager.TryRemoveStateAsync("MyState");

        if (result)
        {
            // State removed!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().tryRemoveStateAsync("MyState").thenApply((result)->{
            if(result)
            {
                // State removed!
            }
        });
    }
}
```

## <a name="next-steps"></a>Další postup

Musí být serializované stavu, který je uložen v Reliable Actors před jeho zapsaný na disk a replikované pro vysokou dostupnost. Další informace o [serializace typu objektu Actor](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

V dalším kroku Další informace o [objektu Actor Diagnostika a sledování výkonu](service-fabric-reliable-actors-diagnostics.md).
