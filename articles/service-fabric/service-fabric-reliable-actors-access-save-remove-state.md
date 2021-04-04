---
title: Správa stavu Azure Service Fabric
description: Přečtěte si informace o přístupu, uložení a odebrání stavu pro Azure Service Fabric Reliable actor a důležitých informací při návrhu aplikace.
ms.topic: conceptual
ms.date: 03/19/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 1e796232719342883efc2aa5dd377d586f3039ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96571313"
---
# <a name="access-save-and-remove-reliable-actors-state"></a>Přístup, uložení a odebrání stavu Reliable Actors
[Reliable Actors](service-fabric-reliable-actors-introduction.md) jsou objekty s jedním vláknem, které mohou spolehlivě zapouzdřovat logiku i stav a udržovat stav spolehlivě. Každá instance objektu actor má svého vlastního [správce stavu](service-fabric-reliable-actors-state-management.md): datová struktura, která spolehlivě ukládá páry klíč/hodnota. Správce stavu je Obálka kolem poskytovatele stavu. Můžete ho použít k ukládání dat bez ohledu na to, jaké [nastavení trvalosti](service-fabric-reliable-actors-state-management.md#state-persistence-and-replication) se používá.

Klíče správce stavu musí být řetězce. Hodnoty jsou obecné a můžou být libovolného typu, včetně vlastních typů. Hodnoty uložené ve Správci stavů musí být serializovatelné kontrakty dat, protože můžou být přenášeny přes síť do jiných uzlů během replikace a můžou být zapsané na disk v závislosti na nastavení trvalosti stavu objektu actor.

Správce stavů zveřejňuje běžné Slovníkové metody pro správu stavu, podobně jako u těch, které se nacházejí ve spolehlivém slovníku.

Informace najdete v tématu [osvědčené postupy při správě stavu objektu actor](service-fabric-reliable-actors-state-management.md#best-practices).

## <a name="access-state"></a>Stav přístupu
Stav je k dispozici prostřednictvím Správce stavu pomocí klíče. Metody správce stavu jsou všechny asynchronní, protože v případě trvalého stavu objektů actor můžou vyžadovat vstupně-výstupní operace disku. Při prvním přístupu jsou objekty stavu ukládány do mezipaměti v paměti. Opakujte přístup k objektům přístup k objektům přímo z paměti a vraťte se synchronně, aniž by došlo k vstupně-výstupním operacím disku nebo asynchronnímu přepínání kontextu. Stavový objekt je odstraněn z mezipaměti v následujících případech:

* Metoda actor vyvolá neošetřenou výjimku poté, co načte objekt ze Správce stavu.
* Objekt actor se znovu aktivuje buď po deaktivaci, nebo po selhání.
* Stav stránek poskytovatele stavu na disk. Toto chování závisí na implementaci zprostředkovatele stavu. Výchozí zprostředkovatel stavu pro `Persisted` nastavení má toto chování.

Stav můžete načíst pomocí standardní operace *Get* , která vyvolá `KeyNotFoundException` (C#) nebo `NoSuchElementException` (Java), pokud pro tento klíč neexistuje položka:

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

Stav lze také načíst pomocí metody *TryGet* , která nevyvolává, pokud položka neexistuje pro klíč:

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
Metody načítání správce stavu vracejí odkaz na objekt v místní paměti. Změna tohoto objektu pouze v místní paměti nezpůsobí uložení trvale. Když je objekt načten z správce stavu a upraven, musí být znovu vložen do Správce stavu, aby jej bylo možné uložit trvale.

Můžete vložit stav pomocí nepodmíněné *sady*, která je ekvivalentem `dictionary["key"] = value` syntaxe:

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

Můžete přidat stav pomocí metody *Add* . Tato metoda vyvolá `InvalidOperationException` (C#) nebo `IllegalStateException` (Java), když se pokusí přidat klíč, který již existuje.

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

Můžete také přidat stav pomocí metody *TryAdd* . Tato metoda nevyvolá, když se pokusí přidat klíč, který už existuje.

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

Na konci metody objektu actor správce stavu automaticky uloží všechny hodnoty, které byly přidány nebo změněny operací INSERT nebo Update. Možnost Save (Uložit) může zahrnovat zachování na disk a replikaci v závislosti na použitém nastavení. Hodnoty, které se nezměnily, nejsou trvalé ani replikované. Pokud se nezměnily žádné hodnoty, operace uložení neprovede žádnou akci. Pokud se ukládání nepovede, změněný stav se zahodí a původní stav se znovu načte.

Stav lze také uložit ručně voláním `SaveStateAsync` metody na bázi objektu actor:

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
Stav můžete trvale odebrat ze Správce stavu objektu actor voláním metody *Remove* . Tato metoda vyvolá `KeyNotFoundException` (C#) nebo `NoSuchElementException` (Java), když se pokusí odebrat klíč, který neexistuje.

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

Stav lze také trvale odebrat pomocí metody *TryRemove* . Tato metoda nevyvolá, když se pokusí odebrat klíč, který neexistuje.

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

Stav, který je uložen v Reliable Actors musí být serializován před zapsáním na disk a replikován pro zajištění vysoké dostupnosti. Přečtěte si další informace o [serializaci typu objektu actor](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

V dalším kroku se dozvíte další informace o [diagnostice objektu actor a monitorování výkonu](service-fabric-reliable-actors-diagnostics.md).
