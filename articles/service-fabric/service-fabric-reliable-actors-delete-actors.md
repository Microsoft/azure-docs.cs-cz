---
title: Odstranění objektů infrastruktury služeb Azure
description: Zjistěte, jak ručně a úplně odstranit spolehlivé objekty actor a jejich stav v aplikaci Azure Service Fabric.
author: amanbha
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: amanbha
ms.openlocfilehash: b90c5a10c64e273f1c8f48c7bf5713859796db65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645612"
---
# <a name="delete-reliable-actors-and-their-state"></a>Odstranění objektů Reliable Actors a jejich stavu
Uvolňování paměti deaktivované objekty actor pouze vyčistí objekt objektu actor, ale neodebere data, která je uložena ve správci stavu objektu actor. Při opětovné aktivaci objektu actor, jeho data je znovu k dispozici prostřednictvím Správce stavu. V případech, kdy objekty actor ukládají data ve Správci stavu a jsou deaktivovány, ale nikdy znovu aktivovány, může být nutné vyčistit jejich data.

[Služba actor](service-fabric-reliable-actors-platform.md) poskytuje funkci pro odstranění objekty actor ze vzdáleného volajícího:

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

Odstranění objektu actor má následující účinky v závislosti na tom, zda je objekt actor aktuálně aktivní:

* **Aktivní herec**
  * Objekt actor je odebrán ze seznamu aktivních aktů a je deaktivován.
  * Jeho stav je trvale odstraněn.
* **Neaktivní herec**
  * Jeho stav je trvale odstraněn.

Objekt actor nemůže volat delete na sebe z jedné z jeho metody objektu actor, protože objekt actor nelze odstranit při provádění v kontextu volání objektu actor, ve kterém modul runtime získal zámek kolem volání objektu actor vynutit přístup s jedním podprocesem.

Další informace o spolehlivých aktérech naleznete v následujících hodnotách:
* [Časovače a připomenutí herce](service-fabric-reliable-actors-timers-reminders.md)
* [Akce herce](service-fabric-reliable-actors-events.md)
* [Herec reentrancy](service-fabric-reliable-actors-reentrancy.md)
* [Diagnostika a sledování výkonu actoru](service-fabric-reliable-actors-diagnostics.md)
* [Referenční dokumentace rozhraní API actor](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Ukázkový kód jazyka C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Ukázkový kód Javy](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
