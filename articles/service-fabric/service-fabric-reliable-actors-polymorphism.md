---
title: Polymorfismus v rámci spolehlivých aktérů
description: Vytvořte hierarchie rozhraní a typů rozhraní .NET v rámci Reliable Actors pro opakované použití funkcí a definic rozhraní API.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 4e485463f41cdfbadeb166ecbb3a86d4a32c1589
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75348925"
---
# <a name="polymorphism-in-the-reliable-actors-framework"></a>Polymorfismus v rámci spolehlivých aktérů
Architektura Reliable Actors umožňuje vytvářet objekty actor pomocí mnoha stejných technik, které byste použili v objektově orientovaném návrhu. Jednou z těchto technik je polymorfismus, který umožňuje typům a rozhraním dědit od zobecněnějších rodičů. Dědičnost v rámci reliable actors obecně následuje model .NET s několika dalšími omezeními. V případě Java/Linuxu se řídí modelem Java.

## <a name="interfaces"></a>Rozhraní
Architektura Reliable Actors vyžaduje definovat alespoň jedno rozhraní, které má být implementováno typu objektu actor. Toto rozhraní se používá ke generování proxy třídy, kterou mohou klienti používat ke komunikaci s vašimi aktéry. Rozhraní může dědit z jiných rozhraní tak dlouho, dokud každé rozhraní, které je implementováno typu objektu actor a všechny jeho rodiče nakonec odvozeny od IActor(C#) nebo Actor(Java) . IActor(C#) a Actor(Java) jsou základní rozhraní definovaná platformou pro objekty actors .NET a Java. Klasický příklad polymorfismu pomocí tvarů tak může vypadat nějak takto:

![Hierarchie rozhraní pro objekty actor obrazce][shapes-interface-hierarchy]

## <a name="types"></a>Typy
Můžete také vytvořit hierarchii typů objektů actor, které jsou odvozeny ze základní třídy Actor, která je poskytována platformou. V případě obrazců můžete mít `Shape`základní typ (C#) nebo `ShapeImpl`(Java):

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

Podtypy `Shape`(C#) `ShapeImpl`nebo (Java) mohou přepsat metody ze základny.

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

Poznamenejte `ActorService` si atribut na typu objektu actor. Tento atribut říká architekturu reliable actor, že by měl automaticky vytvořit službu pro hostování objekty actor tohoto typu. V některých případech můžete chtít vytvořit základní typ, který je určen výhradně pro sdílení funkcí s podtypy a nikdy nebude použit k vytvoření instance konkrétní chodnících. V těchto případech byste `abstract` měli použít klíčové slovo k označení, že nikdy nevytvoříte objekt actor na základě tohoto typu.

## <a name="next-steps"></a>Další kroky
* Podívejte se, [jak architektura Reliable Actors využívá platformu Service Fabric](service-fabric-reliable-actors-platform.md) k zajištění spolehlivosti, škálovatelnosti a konzistentního stavu.
* Další informace o [životním cyklu herce](service-fabric-reliable-actors-lifecycle.md).

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png
