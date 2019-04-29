---
title: Polymorfismus v Reliable Actors rozhraní | Dokumentace Microsoftu
description: Vytvoření hierarchie rozhraní .NET a typů v Reliable Actors v rámci opakovaně používat funkce a definice rozhraní API.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: vturecek
ms.assetid: ef0eeff6-32b7-410d-ac69-87cba8b8fd46
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: c14b3006184f7bd6dcd1eb67be11bd0214957d72
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60725482"
---
# <a name="polymorphism-in-the-reliable-actors-framework"></a>Polymorfismus v Reliable Actors rozhraní framework
Reliable Actors rozhraní umožňuje vytvářet objekty actor využívá spoustu stejné techniky, které použijete v objektově orientovaný návrh. Jeden z těchto postupů je polymorfismus, která umožňuje typy a rozhraní dědit z více zobecnit nadřazených objektů. Dědičnost v Reliable Actors v rámci obvykle řídí modelem .NET s několika další omezení. V případě Java/Linux se řídí modelem Java.

## <a name="interfaces"></a>Rozhraní
Reliable Actors rozhraní framework vyžaduje, abyste definovat alespoň jedno rozhraní k implementaci typ actor. Toto rozhraní se používá ke generování třídy proxy, který může komunikovat s vaší actors používají klienti. Rozhraní může dědit z jiných rozhraní za předpokladu, každé rozhraní, které je implementované typem objektu actor a všech jejích nadřazených tříd nakonec odvozovat IActor (C#) nebo Actor(Java). IActor (C#) a Actor(Java) platformou definované základní rozhraní pro objekty actor v rozhraní .NET a Javu jsou v uvedeném pořadí. Klasické polymorfismus příklad použití tvarů proto může vypadat přibližně takto:

![Hierarchie rozhraní pro objekty actor obrazce][shapes-interface-hierarchy]

## <a name="types"></a>Typy
Můžete také vytvořit hierarchii typů objektu actor, které jsou odvozeny od základní třídy objektu Actor, který je poskytovaný platformou. V případě tvary, může mít základnu `Shape`(C#) nebo `ShapeImpl`(Java) typu:

```csharp
public abstract class Shape : Actor, IShape
{
    public abstract Task<int> GetVerticeCount();

    public abstract Task<double> GetAreaAsync();
}
```
```Java
public abstract class ShapeImpl extends FabricActor implements Shape
{
    public abstract CompletableFuture<int> getVerticeCount();

    public abstract CompletableFuture<double> getAreaAsync();
}
```

Subtypes z `Shape`(C#) nebo `ShapeImpl`(Java) také přepsat metody ze základní třídy.

```csharp
[ActorService(Name = "Circle")]
[StatePersistence(StatePersistence.Persisted)]
public class Circle : Shape, ICircle
{
    public override Task<int> GetVerticeCount()
    {
        return Task.FromResult(0);
    }

    public override async Task<double> GetAreaAsync()
    {
        CircleState state = await this.StateManager.GetStateAsync<CircleState>("circle");

        return Math.PI *
            state.Radius *
            state.Radius;
    }
}
```
```Java
@ActorServiceAttribute(name = "Circle")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class Circle extends ShapeImpl implements Circle
{
    @Override
    public CompletableFuture<Integer> getVerticeCount()
    {
        return CompletableFuture.completedFuture(0);
    }

    @Override
    public CompletableFuture<Double> getAreaAsync()
    {
        return (this.stateManager().getStateAsync<CircleState>("circle").thenApply(state->{
          return Math.PI * state.radius * state.radius;
        }));
    }
}
```

Poznámka: `ActorService` atribut na typ objektu actor. Tento atribut oznamuje Reliable Actors rozhraní framework, že by měl automaticky vytvoří služba pro hostování actors tohoto typu. V některých případech můžete chtít vytvořit základní typ, který je určený výhradně pro funkce pro sdílení obsahu s podtypy a nebude nikdy používat pro vytvoření instance konkrétní objekty actor. V těchto případech byste měli použít `abstract` – klíčové slovo k označení, že se nikdy nevytvářejte prvek "actor" na základě tohoto typu.

## <a name="next-steps"></a>Další postup
* Zobrazit [jak rozhraní Reliable Actors využívají platformu Service Fabric](service-fabric-reliable-actors-platform.md) zajistit spolehlivost, škálovatelnost a konzistentního stavu.
* Další informace o [životního cyklu objektu actor](service-fabric-reliable-actors-lifecycle.md).

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png
