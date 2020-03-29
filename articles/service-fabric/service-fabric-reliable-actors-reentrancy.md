---
title: Reentrancy v objektech Azure Service Fabric
description: Úvod k reentrancy pro service fabric spolehlivé objekty actor, způsob, jak logicky vyhnout blokování na základě kontextu volání.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 46ce91e607341e2fbdc0b6a3018e74cb24e76839
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645527"
---
# <a name="reliable-actors-reentrancy"></a>Spolehliví herci reentrancy
Reliable Actors runtime ve výchozím nastavení umožňuje logické volání na základě kontextu reentrancy. To umožňuje, aby se subjekty znovu zapojily, pokud jsou ve stejném řetězci kontextu volání. Například actor A odešle zprávu actor B, který odešle zprávu actor C. Jako součást zpracování zprávy, pokud actor C volá actor A, zpráva je reentrant, tak to bude povoleno. Všechny ostatní zprávy, které jsou součástí jiného kontextu volání budou blokovány na objekt actor, dokud nedokončí zpracování.

Existují dvě možnosti, které jsou k `ActorReentrancyMode` dispozici pro objekt actor reentrancy definované ve výčtu:

* `LogicalCallContext`(výchozí chování)
* `Disallowed`- zakáže reentrancy

```csharp
public enum ActorReentrancyMode
{
    LogicalCallContext = 1,
    Disallowed = 2
}
```
```Java
public enum ActorReentrancyMode
{
    LogicalCallContext(1),
    Disallowed(2)
}
```
Reentrancy lze nakonfigurovat `ActorService`v 's nastavení během registrace. Nastavení platí pro všechny instance objektu actor vytvořené ve službě objektu actor.

Následující příklad ukazuje službu objektu actor, která `ActorReentrancyMode.Disallowed`nastaví režim reentrancy na . V tomto případě pokud objekt actor odešle zprávu reentrant `FabricException` do jiného objektu actor, bude vyvolána výjimka typu.

```csharp
static class Program
{
    static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<Actor1>(
                (context, actorType) => new ActorService(
                    context,
                    actorType, () => new Actor1(),
                    settings: new ActorServiceSettings()
                    {
                        ActorConcurrencySettings = new ActorConcurrencySettings()
                        {
                            ReentrancyMode = ActorReentrancyMode.Disallowed
                        }
                    }))
                .GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}
```
```Java
static class Program
{
    static void Main()
    {
        try
        {
            ActorConcurrencySettings actorConcurrencySettings = new ActorConcurrencySettings();
            actorConcurrencySettings.setReentrancyMode(ActorReentrancyMode.Disallowed);

            ActorServiceSettings actorServiceSettings = new ActorServiceSettings();
            actorServiceSettings.setActorConcurrencySettings(actorConcurrencySettings);

            ActorRuntime.registerActorAsync(
                Actor1.getClass(),
                (context, actorType) -> new FabricActorService(
                    context,
                    actorType, () -> new Actor1(),
                    null,
                    stateProvider,
                    actorServiceSettings, timeout);

            Thread.sleep(Long.MAX_VALUE);
        }
        catch (Exception e)
        {
            throw e;
        }
    }
}
```


## <a name="next-steps"></a>Další kroky
* Další informace o reentrancy v [referenční dokumentaci rozhraní API actor](https://msdn.microsoft.com/library/azure/dn971626.aspx)
