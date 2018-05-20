---
title: Události v na základě objektu actor Azure mikroslužeb | Microsoft Docs
description: Úvod do události pro Service Fabric Reliable Actors.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: aa01b0f7-8f88-403a-bfe1-5aba00312c24
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/06/2017
ms.author: amanbha
ms.openlocfilehash: ed920c8d4ff7254b19c6eef8f5961593bb56bacf
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="actor-events"></a>Události objektu actor
Události objektu actor poskytují způsob, jak odeslat oznámení best effort z objektu actor pro klienty. Události objektu actor navržených pro komunikaci objektu actor klienta a by se neměla používat pro komunikaci objektu actor actor.

Následující fragmenty kódu ukazují, jak pomocí objektu actor události ve vaší aplikaci.

Definujte rozhraní, které popisuje události, které zveřejnil objektu actor. Toto rozhraní musí být odvozen od `IActorEvents` rozhraní. Argumenty metody musí být [kontraktů dat serializovatelný](service-fabric-reliable-actors-notes-on-actor-type-serialization.md). Metody musí vracet typ void, jako událost oznámení jsou jednou z možností a usilovně.

```csharp
public interface IGameEvents : IActorEvents
{
    void GameScoreUpdated(Guid gameId, string currentScore);
}
```
```Java
public interface GameEvents implements ActorEvents
{
    void gameScoreUpdated(UUID gameId, String currentScore);
}
```
Události, které zveřejnil objektu actor v objektu actor rozhraní deklarujte.

```csharp
public interface IGameActor : IActor, IActorEventPublisher<IGameEvents>
{
    Task UpdateGameStatus(GameStatus status);

    Task<string> GetGameScore();
}
```
```Java
public interface GameActor extends Actor, ActorEventPublisherE<GameEvents>
{
    CompletableFuture<?> updateGameStatus(GameStatus status);

    CompletableFuture<String> getGameScore();
}
```
Na straně klienta Implementujte obslužné rutiny události.

```csharp
class GameEventsHandler : IGameEvents
{
    public void GameScoreUpdated(Guid gameId, string currentScore)
    {
        Console.WriteLine(@"Updates: Game: {0}, Score: {1}", gameId, currentScore);
    }
}
```

```Java
class GameEventsHandler implements GameEvents {
    public void gameScoreUpdated(UUID gameId, String currentScore)
    {
        System.out.println("Updates: Game: "+gameId+" ,Score: "+currentScore);
    }
}
```

Na klientovi vytvoření proxy server k objektu actor, který publikuje události a přihlásit se k události.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

```Java
GameActor actorProxy = ActorProxyBase.create<GameActor>(GameActor.class, new ActorId(UUID.fromString(args)));

return ActorProxyEventUtility.subscribeAsync(actorProxy, new GameEventsHandler());
```

V případě převzetí služeb při selhání objektu actor může převzetí služeb při selhání jiným procesem nebo uzel. Proxy objektu actor spravuje aktivní odběry a automaticky je odběratel znovu. Můžete řídit interval opakovaného předplatné prostřednictvím `ActorProxyEventExtensions.SubscribeAsync<TEvent>` rozhraní API. Chcete-li odhlásit, použijte `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` rozhraní API.

V objektu actor publikujte události při jejich provádění. Pokud existují odběratele, kteří mají událost, modul runtime aktéři je odešle oznámení.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```
```Java
GameEvents event = getEvent<GameEvents>(GameEvents.class);
event.gameScoreUpdated(Id.getUUIDId(), score);
```


## <a name="next-steps"></a>Další postup
* [Vícenásobný přístup objektu actor](service-fabric-reliable-actors-reentrancy.md)
* [Objektu actor Diagnostika a sledování výkonu](service-fabric-reliable-actors-diagnostics.md)
* [Referenční dokumentace rozhraní API objektu actor](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C# ukázkový kód](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [C# .NET Core ukázkový kód](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)
* [Java ukázkový kód](http://github.com/Azure-Samples/service-fabric-java-getting-started)
