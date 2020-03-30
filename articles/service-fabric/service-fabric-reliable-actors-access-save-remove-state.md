---
title: Správa stavu azure service fabric
description: Další informace o přístupu, ukládání a odebrání stavu pro Azure Service Fabric reliable actor a důležité informace při navrhování aplikace.
author: vturecek
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 788c337a37ec66c5aa1521c5cd9f2816ed7a8bf9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645629"
---
# <a name="access-save-and-remove-reliable-actors-state"></a>Přístup, uložení a odebrání stavu Spolehlivé objekty actor
[Reliable Actors](service-fabric-reliable-actors-introduction.md) jsou objekty s jedním podprocesem, které mohou zapouzdřit logiku i stav a spolehlivě udržovat stav. Každá instance objektu actor má vlastní [správce stavu](service-fabric-reliable-actors-state-management.md): datová struktura podobné slovníku, která spolehlivě ukládá dvojice klíč/hodnota. Správce stavu je obálka kolem zprostředkovatele stavu. Můžete ji použít k ukládání dat bez ohledu na to, které [nastavení trvalosti](service-fabric-reliable-actors-state-management.md#state-persistence-and-replication) se používá.

Klíče správce stavu musí být řetězce. Hodnoty jsou obecné a mohou být libovolný mač, včetně vlastních typů. Hodnoty uložené ve správci stavu musí být data smlouvy serializovatelné, protože mohou být přenášeny po síti do jiných uzlů během replikace a mohou být zapsány na disk, v závislosti na nastavení trvalého stavu objektu actor.

Správce stavu zveřejňuje běžné slovníkové metody pro správu stavu, podobné těm, které se nacházejí v spolehlivém slovníku.

Informace naleznete [v tématu doporučené postupy při správě stavu objektu actor](service-fabric-reliable-actors-state-management.md#best-practices).

## <a name="access-state"></a>Stav přístupu
Stav je přístupný prostřednictvím správce stavu pomocí klíče. Metody správce stavu jsou všechny asynchronní, protože mohou vyžadovat vstupně-to, když jsou objekty actor trvalého stavu. Při prvním přístupu jsou objekty stavu ukládány do mezipaměti. Operace opakovaného přístupu k objektům přistupují přímo z paměti a synchronně se vracejí bez vzniku vstupně-va disku nebo asynchronního přepínání kontextu. Objekt stavu je odebrán z mezipaměti v následujících případech:

* Metoda objektu actor vyvolá neošetřenou výjimku poté, co načte objekt ze správce stavu.
* Objekt actor je znovu aktivován, po deaktivaci nebo po selhání.
* Stav stránky zprostředkovatele stavu na disk. Toto chování závisí na implementaci zprostředkovatele stavu. Výchozí zprostředkovatel stavu `Persisted` pro nastavení má toto chování.

Stav můžete načíst pomocí standardní operace `KeyNotFoundException` *Get,* která `NoSuchElementException`vyvolá (C#) nebo (Java), pokud pro klíč neexistuje položka:

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

Můžete také načíst stav pomocí *TryGet* metoda, která nevyvolá, pokud položka neexistuje pro klíč:

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
Metody načítání správce stavu vrátí odkaz na objekt v místní paměti. Úprava tohoto objektu v samotné místní paměti nezpůsobí jeho uložení trvale. Při načtení objektu ze správce stavu a upravit, musí být znovu vložen do správce stavu, které mají být uloženy trvale.

Stav můžete vložit pomocí bezpodmínečné *sady*, která `dictionary["key"] = value` je ekvivalentem syntaxe:

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

Stav můžete přidat pomocí metody *Add.* Tato metoda `InvalidOperationException`vyvolá (C#) nebo `IllegalStateException`(Java) při pokusu o přidání klíče, který již existuje.

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

Můžete také přidat stav pomocí *TryAdd* metody. Tato metoda není vyvolána při pokusu o přidání klíče, který již existuje.

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

Na konci metody objektu actor správce stavu automaticky uloží všechny hodnoty, které byly přidány nebo změněny operací vložení nebo aktualizace. "Uložit" může zahrnovat uchování na disku a replikace, v závislosti na použitém nastavení. Hodnoty, které nebyly změněny, nejsou trvalé nebo replikovány. Pokud nebyly změněny žádné hodnoty, operace uložení neprovede žádnou akci. Pokud se uložení nezdaří, upravený stav je zahozen a původní stav je znovu načten.

Stav můžete také uložit ručně `SaveStateAsync` voláním metody na základně objektu actor:

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

## <a name="remove-state"></a>Odebrat stav
Stav můžete trvale odebrat ze správce stavu objektu actor voláním *Remove* metoda. Tato metoda `KeyNotFoundException`vyvolá (C#) nebo `NoSuchElementException`(Java) při pokusu o odebrání klíče, který neexistuje.

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

Můžete také trvale odebrat stav pomocí *TryRemove* metoda. Tato metoda není vyvolána při pokusu o odebrání klíče, který neexistuje.

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

## <a name="next-steps"></a>Další kroky

Stav, který je uložen v reliable actors musí být serializovánpřed jeho zapsány na disk a replikovány pro vysokou dostupnost. Další informace o [serializaci typu actor](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Další informace o [diagnostice a sledování výkonu objektu Actor](service-fabric-reliable-actors-diagnostics.md).
