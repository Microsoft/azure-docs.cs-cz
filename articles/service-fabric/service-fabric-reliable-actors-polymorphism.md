---
title: Polymorfismus v rámci Reliable Actors | Microsoft Docs
description: Vytvoření hierarchie nástroje rozhraní .NET a typy v rozhraní framework Reliable Actors znovu použít funkce a definice rozhraní API.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: vturecek
ms.assetid: ef0eeff6-32b7-410d-ac69-87cba8b8fd46
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: f03330bbbf2c1c7f92310211058f7ef8858f1941
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="polymorphism-in-the-reliable-actors-framework"></a>Polymorfismus v rámci Reliable Actors
Rozhraní framework Reliable Actors umožňuje vytvářet, kteří používají řadu se stejné techniky, které byste použili v objektově orientované návrhu. Jeden z těchto postupů je polymorfismus, která umožňuje typy a rozhraní dědění z více zobecněn nadřazené položky. Dědičnost v rámci Reliable Actors obvykle následuje model rozhraní .NET s několika další omezení. V případě Java či Linux postupuje modelu Java.

## <a name="interfaces"></a>Rozhraní
Rozhraní framework Reliable Actors, musíte definovat alespoň jedno rozhraní k implementaci podle vašeho typu objektu actor. Toto rozhraní se používá ke generování třídu proxy, které je možné ke komunikaci s vaší aktéři klienty. Rozhraní může dědit vlastnosti z dalších rozhraní, dokud všechny rozhraní, které je implementované typ objektu actor a všech jejích nadřazených tříd nakonec odvozena od IActor(C#) nebo Actor(Java). IActor(C#) a Actor(Java) jsou definované platformy základní rozhraní pro aktéři v rozhraní .NET a Javu. Příklad classic polymorfismus pomocí tvarů proto může vypadat přibližně takto:

![Rozhraní hierarchie aktéři obrazce][shapes-interface-hierarchy]

## <a name="types"></a>Typy
Můžete také vytvořit hierarchii objektu actor typů, které jsou odvozeny od základní třídy objektu Actor, kterou poskytuje platformu. V případě tvarů, můžete mít na základní `Shape`(C#) nebo `ShapeImpl`typu (Java):

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

Subtypes z `Shape`(C#) nebo `ShapeImpl`(Java) můžete přepsat metody od základní.

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

Poznámka: `ActorService` atributu pro typ objektu actor. Tento atribut informuje rozhraní spolehlivé objektu Actor, že by měl automaticky vytvořit službu pro hostování aktéři tohoto typu. V některých případech můžete chtít vytvořit základní typ, který je určený výhradně pro sdílení funkce s podtypů a nebude nikdy používat k vytváření instancí konkrétní aktéři. V takových případech byste měli použít `abstract` – klíčové slovo indikující, že se nikdy vytvoří objekt actor na základě tohoto typu.

## <a name="next-steps"></a>Další postup
* V tématu [jak rozhraní Reliable Actors využívá platformy Service Fabric](service-fabric-reliable-actors-platform.md) zajistit spolehlivost, škálovatelnost a konzistentním stavu.
* Další informace o [životního cyklu objektu actor](service-fabric-reliable-actors-lifecycle.md).

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png
