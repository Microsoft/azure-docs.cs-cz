---
title: Zobrazení výčtu objektů actor v Azure Service Fabric
description: Seznamte se s výčtem Reliable Actors a jejich metadaty v aplikaci Azure Service Fabric pomocí příkladů.
author: vturecek
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 1516c9005a7c4dd0adcb279e9954e5f882c575c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "75645595"
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
* [Referenční dokumentace k rozhraní actor API](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Vzorový kód .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Vzorový kód Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
