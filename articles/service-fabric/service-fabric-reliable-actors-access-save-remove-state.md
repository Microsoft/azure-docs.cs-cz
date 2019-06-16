---
title: Správa stavu Azure Service Fabric | Dokumentace Microsoftu
description: Zjistěte, jak získat přístup, uložení a odstranění stavu Service Fabric Reliable Actors.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 37cf466a-5293-44c0-a4e0-037e5d292214
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 7c10d00916ef65767c98616c7337bfa444c339a9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60725393"
---
# <a name="access-save-and-remove-reliable-actors-state"></a>Přístup, uložení a odstranění stavu Reliable Actors
[Reliable Actors](service-fabric-reliable-actors-introduction.md) jsou objekty s jedním vláknem, které lze zapouzdřit logiky a stavových a spolehlivě zachovat stav. Každá instance objektu actor má vlastní [správce stavu](service-fabric-reliable-actors-state-management.md): slovníku jako datová struktura, která je spolehlivě uchová dvojic klíč/hodnota. Správce stavu představuje obálku kolem zprostředkovatele stavu. Můžete ho použít k ukládání dat bez ohledu na to, které [nastavení trvalosti](service-fabric-reliable-actors-state-management.md#state-persistence-and-replication) se používá.

Správce stavu klíče musejí být řetězce. Hodnoty jsou obecné a může být libovolný typ, včetně vlastních typů. Hodnoty uložené v správce stavu musí být serializovatelný kontraktu dat, protože může přenést přes síť do jiných uzlů během replikace a může být zapsaný na disk, v závislosti na nastavení trvalého stavu prvek "actor".

Správce stavu uvádí běžné metody slovníku pro správu stavu, podobné těm v spolehlivého slovníku.

Informace najdete v tématu [osvědčené postupy při správě stavu objektu actor](service-fabric-reliable-actors-state-management.md#best-practices).

## <a name="access-state"></a>Stav přístupu
Stav je přístupný prostřednictvím Správce stavu podle klíče. Metody správce stavu jsou všechny asynchronní, protože při actors trvalé stavu se můžou vyžadovat vstup/výstup disku. Při prvním přístupu jsou uložená v mezipaměti stavu objektů. Opakujte přístup operace přístup k objektům přímo z paměti a synchronně vrátit, aniž by došlo k vstupně-výstupních operací disku nebo asynchronní přepínání kontextu režie. Stav objektu se odebere z mezipaměti v následujících případech:

* Metoda objektu actor vyvolá neošetřenou výjimku po načte objekt z správce stavu.
* Prvek "actor" je znovu aktivovat, po právě deaktivována nebo po selhání.
* Poskytovatel stavu stránky stavu na disk. Toto chování závisí na implementaci zprostředkovatele stavu. Výchozí zprostředkovatel stavu pro `Persisted` nastavení je toto chování.

Stav můžete načíst pomocí standardního *získat* operace, která vyvolá `KeyNotFoundException`(C#) nebo `NoSuchElementException`(Java), pokud záznam neexistuje pro klíč:

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

Můžete také načíst stav pomocí *TryGet* metodu, která nebude vyvolána výjimka, pokud záznam neexistuje pro klíč:

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
Metody načtení stavu správce vrátí odkaz na objekt v místní paměti. Úprava tento objekt v místní paměti samostatně nezpůsobí, je možné trvale uložit. Pokud objekt je načten ze Správce stavu a upravit, musíte znovu vloženy do správce stavů trvale uložit.

Stav můžete vložit pomocí Nepodmíněný *nastavit*, což je ekvivalent `dictionary["key"] = value` syntaxi:

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

Stav můžete přidat pomocí *přidat* metody. Tato metoda vyvolá `InvalidOperationException`(C#) nebo `IllegalStateException`(Java) při pokusu o přidání klíče, který již existuje.

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

Můžete také přidat stav pomocí *TryAdd* metody. Tato metoda nebude vyvolána výjimka při pokusu o přidání klíče, který již existuje.

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

Na konci metody objektu actor uloží správce stavu žádné hodnoty, které byly přidány nebo změněny podle operace insert nebo update. "save" může obsahovat uchování na disk a replikace v závislosti na nastavení. Hodnoty, které nebyly upraveny nejsou zachována nebo replikovat. Pokud byly změněny žádné hodnoty, ukládání operace nemá žádný účinek. Pokud uložíte selže, se zahodí změny stavu a opětovném načtení nástroje původního stavu.

Můžete také uložit stav ručně voláním `SaveStateAsync` metodu v základní třídě objektu actor:

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

## <a name="remove-state"></a>Odstranit stav
Můžete odebrat stavu trvale ze Správce stavu prvek "actor" voláním *odebrat* metody. Tato metoda vyvolá `KeyNotFoundException`(C#) nebo `NoSuchElementException`(Java) při pokusu o odebrání klíče, který neexistuje.

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

Můžete také odebrat stavu trvale pomocí *TryRemove* metody. Tato metoda nebude vyvolána výjimka při pokusu o odebrání klíče, který neexistuje.

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

Stav, který je uložen v Reliable Actors musí serializovat. před jeho zapsané na disk a replikovaný pro zajištění vysoké dostupnosti. Další informace o [serializaci typu Actor](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

V dalším kroku Další informace o [objektu Actor Diagnostika a sledování výkonu](service-fabric-reliable-actors-diagnostics.md).
