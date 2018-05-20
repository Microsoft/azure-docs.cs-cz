---
title: Odstranit Azure Service Fabric aktéři | Microsoft Docs
description: Zjistěte, jak ručně odstranit Service Fabric Reliable Actors a jejich stavu.
services: service-fabric
documentationcenter: .net
author: amanbha
manager: timlt
editor: vturecek
ms.assetid: b91384cc-804c-49d6-a6cb-f3f3d7d65a8e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: amanbha
ms.openlocfilehash: fa4fe018a9e6b32158f5bbd13c44ff57069cb1cf
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="delete-reliable-actors-and-their-state"></a>Odstranit Reliable Actors a jejich stavu
Uvolnění paměti deaktivované aktéři pouze vyčistí objekt actor, ale neodebere data, která je uložená v objektu actor správce stavu. Při opětovné aktivaci objektu actor jeho data se znovu k dispozici k němu pomocí Správce stavu. V případech, kdy aktéři ukládání dat do Správce stavu a jsou deaktivována ale nikdy znovu aktivovat může být nutné vyčistit svá data.

[Služby objektu Actor](service-fabric-reliable-actors-platform.md) poskytuje funkce pro odstranění aktéři ze vzdáleného volající:

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

Odstranění objektu actor má následující důsledky v závislosti na tom, jestli je aktuálně aktivní objektu actor:

* **Aktivní objektu Actor**
  * Objektu actor se odebral ze seznamu active aktéři a je deaktivována.
  * Jeho stav se trvale odstraní.
* **Neaktivní objektu Actor**
  * Jeho stav se trvale odstraní.

Nelze volat objekt actor odstranit sám na sobě z jednoho z jeho metody objektu actor, protože objekt actor nelze odstranit, při provádění v rámci kontextu volání objektu actor, ve kterém má modul runtime získat zámek kolem volání objektu actor pro vynucení jednovláknové přístupu.

Další informace o Reliable Actors přečtěte si následující:
* [Časovače objektu actor a upomínek](service-fabric-reliable-actors-timers-reminders.md)
* [Události objektu actor](service-fabric-reliable-actors-events.md)
* [Vícenásobný přístup objektu actor](service-fabric-reliable-actors-reentrancy.md)
* [Objektu actor Diagnostika a sledování výkonu](service-fabric-reliable-actors-diagnostics.md)
* [Referenční dokumentace rozhraní API objektu actor](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C# ukázkový kód](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java ukázkový kód](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
