---
title: Odstranit Azure Service Fabric Actors
description: Přečtěte si, jak ručně a úplně odstranit Reliable Actors a jejich stav v aplikaci Azure Service Fabric.
author: amanbha
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: amanbha
ms.openlocfilehash: b90c5a10c64e273f1c8f48c7bf5713859796db65
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "75645612"
---
# <a name="delete-reliable-actors-and-their-state"></a>Odstranění objektů Reliable Actors a jejich stavu
Uvolňování paměti deaktivovaných aktérů vyčistí jenom objekt actor, ale neodebere data uložená ve Správci stavu objektu actor. Po opětovné aktivaci objektu actor se data znovu zpřístupní prostřednictvím Správce stavu. V případech, kdy objekty actor ukládají data do Správce stavů a deaktivují se, ale nikdy se neaktivují, může být nutné vyčistit svá data.

[Služba objektu actor](service-fabric-reliable-actors-platform.md) poskytuje funkci pro odstranění objektů actor ze vzdáleného volajícího:

```csharp
ActorId actorToDelete = new ActorId(id);

IActorService myActorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

await myActorServiceProxy.DeleteActorAsync(actorToDelete, cancellationToken)
```
```Java
ActorId actorToDelete = new ActorId(id);

ActorService myActorServiceProxy = ActorServiceProxy.create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

myActorServiceProxy.deleteActorAsync(actorToDelete);
```

Odstranění objektu actor má následující důsledky v závislosti na tom, zda je objekt actor aktuálně aktivní:

* **Aktivní objekt actor**
  * Objekt actor je odebrán ze seznamu aktivních objektů actor a deaktivován.
  * Stav je trvale odstraněn.
* **Neaktivní objekt actor**
  * Stav je trvale odstraněn.

Objekt actor nemůže volat odstranění samotného z jedné z jeho metod Actor, protože objekt actor nelze odstranit při provádění v kontextu volání objektu actor, ve kterém modul runtime získal zámek kolem volání objektu actor, aby vynutil přístup s jedním vláknem.

Další informace o Reliable Actors najdete v následujícím článku:
* [Časovače a připomenutí objektu actor](service-fabric-reliable-actors-timers-reminders.md)
* [Události objektu actor](service-fabric-reliable-actors-events.md)
* [Vícenásobný přístup objektu actor](service-fabric-reliable-actors-reentrancy.md)
* [Monitorování diagnostiky a výkonu objektu actor](service-fabric-reliable-actors-diagnostics.md)
* [Referenční dokumentace k rozhraní API actor](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Ukázkový kód C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Vzorový kód Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
