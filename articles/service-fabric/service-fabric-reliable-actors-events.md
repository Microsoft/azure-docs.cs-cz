---
title: Události v objektech actor-based Azure Service Fabric objekty actor
description: Další informace o událostech pro spolehlivé actor service fabric, efektivní způsob komunikace mezi objektem actor a klienta.
author: vturecek
ms.topic: conceptual
ms.date: 10/06/2017
ms.author: amanbha
ms.openlocfilehash: 73c149a0d0992fecd1acf633891057570285df64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639662"
---
# <a name="actor-events"></a>Akce herce
Události objektu actor poskytují způsob, jak odeslat oznámení o nejlepší úsilí z objektu actor klientům. Události objektu actor jsou určeny pro komunikaci mezi objekty actor-to-client a neměly by být používány pro komunikaci mezi objekty actor.

Následující fragmenty kódu ukazují, jak používat události objektu actor ve vaší aplikaci.

Definujte rozhraní, které popisuje události publikované objektem actor. Toto rozhraní musí být `IActorEvents` odvozeno z rozhraní. Argumenty metod musí být [data smlouvy serializovatelné](service-fabric-reliable-actors-notes-on-actor-type-serialization.md). Metody musí vrátit void, jako oznámení událostí jsou jedním ze způsobů a nejlepší úsilí.

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
Na straně klienta implementujte obslužnou rutinu události.

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

Na straně klienta vytvořte proxy objektu actor, který publikuje událost a přihlásit se k odběru jejích událostí.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

```Java
GameActor actorProxy = ActorProxyBase.create<GameActor>(GameActor.class, new ActorId(UUID.fromString(args)));

return ActorProxyEventUtility.subscribeAsync(actorProxy, new GameEventsHandler());
```

V případě převzetí služeb při selhání může objekt actor převzetí služeb při selhání do jiného procesu nebo uzlu. Proxy objekt u actor spravuje aktivní odběry a automaticky je znovu přihlásí. Interval opětovného předplatného můžete řídit `ActorProxyEventExtensions.SubscribeAsync<TEvent>` prostřednictvím rozhraní API. Chcete-li se `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` odhlásit, použijte rozhraní API.

Na herce, publikovat události, jak se to stane. Pokud existují předplatitelé události, actors runtime odešle jim oznámení.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```
```Java
GameEvents event = getEvent<GameEvents>(GameEvents.class);
event.gameScoreUpdated(Id.getUUIDId(), score);
```


## <a name="next-steps"></a>Další kroky
* [Herec reentrancy](service-fabric-reliable-actors-reentrancy.md)
* [Diagnostika a sledování výkonu actoru](service-fabric-reliable-actors-diagnostics.md)
* [Referenční dokumentace rozhraní API actor](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Ukázkový kód jazyka C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Ukázkový kód jádra jazyka C#](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)
* [Ukázkový kód Javy](https://github.com/Azure-Samples/service-fabric-java-getting-started)
