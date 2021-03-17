---
title: Vícenásobný přístup v Azure Service Fabric Actors
description: Úvod do Vícenásobný přístup pro Service Fabric Reliable Actors, způsob, jak logicky zabránit zablokování na základě kontextu volání.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 90a8e9146d18d628fbf124307f1b79b912c9cef9
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98789376"
---
# <a name="reliable-actors-reentrancy"></a>Reliable Actors Vícenásobný přístup
Modul runtime Reliable Actors ve výchozím nastavení umožňuje logický volání Vícenásobný přístup na základě kontextu. To umožňuje, aby se aktéri znovu zavolaly, pokud jsou ve stejném řetězci kontextu volání. Například objekt actor A pošle zprávu objektu actor B, který pošle zprávu objektu actor C. V rámci zpracování zprávy, pokud objekt actor C volá objekt actor A, je zpráva znovu zavolána, takže bude povolena. Všechny ostatní zprávy, které jsou součástí jiného kontextu volání, budou blokovány u objektu actor A do doby, než dokončí zpracování.

Existují dvě možnosti, které jsou pro objekt actor Vícenásobný přístup definovány ve `ActorReentrancyMode` výčtu:

* `LogicalCallContext` (výchozí chování)
* `Disallowed` – zakáže Vícenásobný přístup

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
Vícenásobný přístup se dá nakonfigurovat v `ActorService` nastaveních během registrace. Nastavení platí pro všechny instance objektu actor vytvořené ve službě objektu actor.

Následující příklad ukazuje službu objektu actor, která nastaví režim Vícenásobný přístup na `ActorReentrancyMode.Disallowed` . V tomto případě, pokud objekt actor odešle zprávu o předanému jinému objektu actor, vyvolá se výjimka typu `FabricException` .

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
* Další informace o Vícenásobný přístup najdete v [referenční dokumentaci k rozhraní API actor](/dotnet/api/microsoft.servicefabric.actors).)
