---
title: Odstranit Azure Service Fabric Actors
description: Přečtěte si, jak ručně a úplně odstranit Reliable Actors a jejich stav v aplikaci Azure Service Fabric.
author: amanbha
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: amanbha
ms.custom: devx-track-csharp
ms.openlocfilehash: 80192aef564317e36fba56025aa31c787676d974
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89006852"
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
* [Referenční dokumentace k rozhraní API actor](/previous-versions/azure/dn971626(v=azure.100))
* [Ukázkový kód C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Vzorový kód Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
