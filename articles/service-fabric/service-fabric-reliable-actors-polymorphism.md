---
title: Polymorfismus v rozhraní Reliable Actors Framework
description: Umožňuje sestavit hierarchie rozhraní a typů .NET v rozhraní Reliable Actors, aby bylo možné znovu použít funkce a definice rozhraní API.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 568e306979e862c325264a4e12a64d95e6d13c5f
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575988"
---
# <a name="polymorphism-in-the-reliable-actors-framework"></a>Polymorfismus v rozhraní Reliable Actors Framework
Rozhraní Reliable Actors Framework umožňuje sestavovat objekty Actors pomocí mnoha stejných postupů, které byste použili v objektově orientovaném návrhu. Jedním z těchto postupů je polymorfismu, což umožňuje typům a rozhraním dědit z obecnější nadřazené položky. Dědičnost v rozhraní Reliable Actors Framework obvykle sleduje model .NET s několika dodatečnými omezeními. V případě jazyka Java/Linux se řídí modelem Java.

## <a name="interfaces"></a>Rozhraní
Rozhraní Reliable Actors Framework vyžaduje, abyste definovali aspoň jedno rozhraní, které má váš typ objektu actor implementovat. Toto rozhraní se používá ke generování proxy třídy, kterou mohou klienti používat ke komunikaci s objekty actor. Rozhraní mohou dědit z jiných rozhraní, pokud je každé rozhraní implementované typem objektu actor a všemi jeho nadřazenými prvky nakonec odvozeny od IActor (C#) nebo actor (Java). IActor (C#) a actor (Java) jsou základní rozhraní definovaná platformou pro objekty actor v rozhraních .NET a Java v uvedeném pořadí. Proto příklad klasické polymorfismu pomocí tvarů může vypadat přibližně takto:

![Hierarchie rozhraní pro objekty actor obrazce][shapes-interface-hierarchy]

## <a name="types"></a>Typy
Můžete také vytvořit hierarchii typů objektů Actor, které jsou odvozeny ze základní třídy Actor, která je poskytována platformou. V případě tvarů je možné, že máte `Shape` typ Base (C#) nebo `ShapeImpl` (Java):

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

Podtypy `Shape` (C#) nebo `ShapeImpl` (Java) mohou přepsat metody ze základní třídy.

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

Poznamenejte si `ActorService` atribut typu objektu actor. Tento atribut oznamuje rozhraní Reliable Actor, že by měl automaticky vytvořit službu pro hostování aktérů tohoto typu. V některých případech můžete chtít vytvořit základní typ, který je určen výhradně pro sdílení funkcí s podtypy a nebude nikdy použit k vytvoření instance konkrétních aktérů. V těchto případech byste měli použít `abstract` klíčové slovo k označení, že nikdy nevytvoříte objekt actor na základě tohoto typu.

## <a name="next-steps"></a>Další kroky
* Podívejte se, [jak Reliable Actors Framework využívá Service Fabric platformu](service-fabric-reliable-actors-platform.md) k zajištění spolehlivosti, škálovatelnosti a konzistentního stavu.
* Přečtěte si informace o [životním cyklu objektu actor](service-fabric-reliable-actors-lifecycle.md).

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png
