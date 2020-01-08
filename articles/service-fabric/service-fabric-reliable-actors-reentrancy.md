---
title: Vícenásobný přístup v Azure Service Fabric Actors
description: Úvod do Vícenásobný přístup pro Service Fabric Reliable Actors, způsob, jak logicky zabránit zablokování na základě kontextu volání.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 46ce91e607341e2fbdc0b6a3018e74cb24e76839
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645527"
---
# <a name="reliable-actors-reentrancy"></a>Reliable Actors Vícenásobný přístup
Modul runtime Reliable Actors ve výchozím nastavení umožňuje logický volání Vícenásobný přístup na základě kontextu. To umožňuje, aby se aktéri znovu zavolaly, pokud jsou ve stejném řetězci kontextu volání. Například objekt actor A pošle zprávu objektu actor B, který pošle zprávu objektu actor C. V rámci zpracování zprávy, pokud objekt actor C volá objekt actor A, je zpráva znovu zavolána, takže bude povolena. Všechny ostatní zprávy, které jsou součástí jiného kontextu volání, budou blokovány u objektu actor A do doby, než dokončí zpracování.

Pro objekt actor Vícenásobný přístup definované ve výčtu `ActorReentrancyMode` jsou k dispozici dvě možnosti:

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
Vícenásobný přístup se dá nakonfigurovat v nastaveních `ActorService`během registrace. Nastavení platí pro všechny instance objektu actor vytvořené ve službě objektu actor.

Následující příklad ukazuje službu objektu actor, která nastaví režim Vícenásobný přístup na `ActorReentrancyMode.Disallowed`. V tomto případě, pokud objekt actor odešle zprávu o předanému objektu jinému objektu actor, vyvolá se výjimka typu `FabricException`.

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
* Další informace o Vícenásobný přístup najdete v [referenční dokumentaci k rozhraní API actor](https://msdn.microsoft.com/library/azure/dn971626.aspx) .
