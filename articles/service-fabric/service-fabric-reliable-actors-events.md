---
title: Události v objektech actor Service Fabric Azure založených na objektu actor
description: Získejte informace o událostech pro Service Fabric Reliable Actors, efektivním způsobu komunikace mezi objektem actor a klientem.
author: vturecek
ms.topic: conceptual
ms.date: 10/06/2017
ms.author: amanbha
ms.custom: devx-track-csharp
ms.openlocfilehash: f5634a33dccb06437f2e5f095e7880221dba9d6e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89007906"
---
# <a name="actor-events"></a>Události objektu actor
Události objektu actor poskytují způsob, jak odesílat osvědčená oznámení od objektu actor klientům. Události objektu actor jsou navržené pro komunikaci pomocí objektu actor-klient a neměly by se používat pro komunikaci actor-to-actor.

Následující fragmenty kódu ukazují, jak používat události objektu actor ve vaší aplikaci.

Definujte rozhraní, které popisuje události publikované objektem actor. Toto rozhraní musí být odvozeno od `IActorEvents` rozhraní. Argumenty metody musí být [serializovatelných kontraktů dat](service-fabric-reliable-actors-notes-on-actor-type-serialization.md). Metody musí vracet typ void, protože oznámení událostí představují jeden ze způsobů a co nejlepší úsilí.

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
Deklarujte události publikované objektem actor v rozhraní objektu actor.

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
Na straně klienta Implementujte obslužnou rutinu události.

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

Na straně klienta vytvořte proxy pro objekt actor, který publikuje událost a přihlásí se k odběru událostí.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

```Java
GameActor actorProxy = ActorProxyBase.create<GameActor>(GameActor.class, new ActorId(UUID.fromString(args)));

return ActorProxyEventUtility.subscribeAsync(actorProxy, new GameEventsHandler());
```

V případě převzetí služeb při selhání může objekt actor převzít služby při selhání na jiný proces nebo uzel. Proxy objekt actor spravuje aktivní odběry a automaticky je znovu přihlásí. Interval opětovného předplatného můžete řídit přes `ActorProxyEventExtensions.SubscribeAsync<TEvent>` rozhraní API. Pokud chcete zrušit odběr, použijte `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` rozhraní API.

V objektu actor publikujte události tak, jak se vyskytují. Pokud existují předplatitelé události, modul runtime actor jim pošle oznámení.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```
```Java
GameEvents event = getEvent<GameEvents>(GameEvents.class);
event.gameScoreUpdated(Id.getUUIDId(), score);
```


## <a name="next-steps"></a>Další kroky
* [Vícenásobný přístup objektu actor](service-fabric-reliable-actors-reentrancy.md)
* [Monitorování diagnostiky a výkonu objektu actor](service-fabric-reliable-actors-diagnostics.md)
* [Referenční dokumentace k rozhraní API actor](/previous-versions/azure/dn971626(v=azure.100))
* [Ukázkový kód C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Ukázkový kód C# .NET Core](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)
* [Vzorový kód Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)
