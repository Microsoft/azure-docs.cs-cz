---
title: Zobrazení výčtu aktéři na Azure Service Fabric | Microsoft Docs
description: Naučte se vytvořit výčet Reliable Actors a jejich metadat.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: d5d6ac87db18815aa945d6964338626365b08e64
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="enumerate-service-fabric-reliable-actors"></a>Zobrazení výčtu Service Fabric Reliable Actors
Služby Reliable Actors umožňuje klientovi výčet metadata o aktéři, která je hostitelem služby. Protože služby objektu actor oddílů stavové služby, výčet se provádí na oddíl. Protože každý oddíl může obsahovat mnoho aktéři, výčtu se vrátí jako sadu stránkových výsledků. Na stránkách jsou smyčce přes, dokud se číst všechny stránky. Následující příklad ukazuje, jak vytvořit seznam všech active aktéři v jednom oddílu služby objektu actor:

```csharp
IActorService actorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new List<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = await actorServiceProxy.GetActorsAsync(continuationToken, cancellationToken);

    activeActors.AddRange(page.Items.Where(x => x.IsActive));

    continuationToken = page.ContinuationToken;
}
while (continuationToken != null);
```

```Java
ActorService actorServiceProxy = ActorServiceProxy.create(
    new URI("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new ArrayList<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = actorServiceProxy.getActorsAsync(continuationToken);

    while(ActorInformation x: page.getItems())
    {
         if(x.isActive()){
              activeActors.add(x);
         }
    }

    continuationToken = page.getContinuationToken();
}
while (continuationToken != null);
```



## <a name="next-steps"></a>Další postup
* [Řízení stavu objektu actor](service-fabric-reliable-actors-state-management.md)
* [Kolekce paměti a životního cyklu objektu actor](service-fabric-reliable-actors-lifecycle.md)
* [Referenční dokumentace rozhraní API actors](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Ukázkový kód rozhraní .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Ukázkový kód Java](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
