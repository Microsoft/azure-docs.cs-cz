---
title: Odstranit Azure Service Fabric actors | Dokumentace Microsoftu
description: Zjistěte, jak ručně odstranit Service Fabric Reliable Actors a jejich stav.
services: service-fabric
documentationcenter: .net
author: amanbha
manager: chackdan
editor: vturecek
ms.assetid: b91384cc-804c-49d6-a6cb-f3f3d7d65a8e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: amanbha
ms.openlocfilehash: e297a6f42774f29e2eca4a410b695d5bbb636300
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60726600"
---
# <a name="delete-reliable-actors-and-their-state"></a>Odstranit Reliable Actors a jejich stav
Uvolnění paměti deaktivované actors pouze vyčistí objekt actor, ale neodeberou se data ukládaná ve službě Správce stavu prvek "actor". Při opětovné aktivaci prvek "actor" je znovu svoje data k dispozici k němu prostřednictvím Správce stavu. V případech, kde actors ukládání dat v State Manager a se deaktivuje, ale nikdy znovu aktivovat může být potřeba vyčistit svá data.

[Služba objektu Actor](service-fabric-reliable-actors-platform.md) poskytuje funkce pro odstranění objektů actor ze vzdáleného volající:

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

Odstraňuje prvek "actor" má následujících efektů v závislosti na tom, jestli je aktuálně aktivní objekt actor:

* **Aktivní objektu Actor**
  * Objekt actor se odebere ze seznamu aktivních objektů actor a je deaktivováno.
  * Jeho stav je trvale odstraněn.
* **Neaktivní objektu Actor**
  * Jeho stav je trvale odstraněn.

Prvek "actor" nejde volat metodu odstranit sám na sobě z jednoho z jeho metod objektu actor, protože objekt actor nejde odstranit, při provádění v rámci objekt context volání objektu actor, ve kterém modul runtime získal zámek kolem volání objektu actor pro vynucení přístupu s jedním vláknem.

Další informace o Reliable Actors, přečtěte si následující:
* [Objekt actor časovače a připomenutí](service-fabric-reliable-actors-timers-reminders.md)
* [Události objektu actor](service-fabric-reliable-actors-events.md)
* [Vícenásobný přístup objektu actor](service-fabric-reliable-actors-reentrancy.md)
* [Monitorování výkonu a Diagnostika objektů actor](service-fabric-reliable-actors-diagnostics.md)
* [Referenční dokumentace rozhraní API objektu actor](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Ukázka v jazyce C# kód](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java ukázkový kód](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
