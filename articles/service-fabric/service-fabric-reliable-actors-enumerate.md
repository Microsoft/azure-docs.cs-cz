---
title: Zobrazení výčtu objektů actor v Azure Service Fabric
description: Seznamte se s výčtem Reliable Actors a jejich metadaty v aplikaci Azure Service Fabric pomocí příkladů.
ms.topic: conceptual
ms.date: 03/19/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 11742d1a1c0837588e7122ee615f3f7929aae363
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96571346"
---
# <a name="enumerate-service-fabric-reliable-actors"></a>Zobrazení výčtu Service Fabric Reliable Actors
Služba Reliable Actors umožňuje klientovi vytvořit výčet metadat objektů Actor, které služba hostuje. Vzhledem k tomu, že je služba objektu actor rozdělená stavová služba, je výčet proveden na oddíl. Vzhledem k tomu, že každý oddíl může obsahovat mnoho objektů Actor, je výčet vrácen jako sada stránkovaných výsledků. Na stránky se přeskočí, dokud nebudou načteny všechny stránky. Následující příklad ukazuje, jak vytvořit seznam všech aktivních objektů actor v jednom oddílu služby objektu actor:

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



## <a name="next-steps"></a>Další kroky
* [Správa stavu objektu actor](service-fabric-reliable-actors-state-management.md)
* [Životní cyklus objektu actor a uvolňování paměti](service-fabric-reliable-actors-lifecycle.md)
* [Referenční dokumentace k rozhraní actor API](/previous-versions/azure/dn971626(v=azure.100))
* [Vzorový kód .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Vzorový kód Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
