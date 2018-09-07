---
title: Události v Azure Service Fabric actors na základě objektů actor | Dokumentace Microsoftu
description: Úvod do událostí pro Service Fabric Reliable Actors.
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
ms.openlocfilehash: c228821383a1bfedf380f97e3411fdacc322a6f9
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44054474"
---
# <a name="actor-events"></a>Události objektu actor
Objekt actor události poskytují způsob, jak odesílat oznámení best effort z objektu actor pro klienty. Události objektu actor jsou navrženy pro komunikaci objektu actor klienta a nemělo používat pro komunikaci objektu actor actor.

Následující fragmenty kódu ukazují, jak pomocí objektu actor události ve vaší aplikaci.

Definujte rozhraní, které popisuje události publikuje objekt actor. Toto rozhraní musí být odvozen od `IActorEvents` rozhraní. Argumenty metody musí být [kontraktů dat serializovatelný](service-fabric-reliable-actors-notes-on-actor-type-serialization.md). Metody musí vracet typ void, jako událost oznámení jsou jedním ze způsobů a nejlepší úsilí.

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
Deklarování událostí publikovat podle objektu actor v rozhraní objektu actor.

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

Na straně klienta vytvořit proxy pro objekt actor, který publikuje události a přihlášení k odběru jeho událostí.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

```Java
GameActor actorProxy = ActorProxyBase.create<GameActor>(GameActor.class, new ActorId(UUID.fromString(args)));

return ActorProxyEventUtility.subscribeAsync(actorProxy, new GameEventsHandler());
```

V případě převzetí služeb při selhání objekt actor mohou převzetí služeb při selhání na jiný proces nebo uzel. Objekt actor proxy spravuje aktivní odběry a automaticky je znovu přihlásí. Můžete řídit interval opakovaného předplatného prostřednictvím `ActorProxyEventExtensions.SubscribeAsync<TEvent>` rozhraní API. Chcete-li zrušit odběr, použijte `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` rozhraní API.

Na objekt actor publikujte události při jejich provádění. Pokud existují Odběratelé událostí, modul runtime Actors je odešle oznámení.

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
* [Monitorování výkonu a Diagnostika objektů actor](service-fabric-reliable-actors-diagnostics.md)
* [Referenční dokumentace rozhraní API objektu actor](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Ukázka v jazyce C# kód](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [C# .NET Core ukázkový kód](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)
* [Java ukázkový kód](http://github.com/Azure-Samples/service-fabric-java-getting-started)
