---
title: Odstranit Azure Service Fabric Actors
description: Přečtěte si, jak ručně a úplně odstranit Reliable Actors a jejich stav v aplikaci Azure Service Fabric.
ms.topic: conceptual
ms.date: 03/19/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 16d4ab6a3c155f897cf9212fb1cd6c34d977b9ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96574016"
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
