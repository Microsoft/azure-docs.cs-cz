---
title: Vícenásobný přístup v na základě objektu actor Azure mikroslužeb | Microsoft Docs
description: Úvod do vícenásobný přístup pro Service Fabric Reliable Actors
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: be23464a-0eea-4eca-ae5a-2e1b650d365e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 40f52cb399f2d7391657ce4356a0c30921d46e5f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207090"
---
# <a name="reliable-actors-reentrancy"></a>Spolehlivé aktéři vícenásobný přístup
Modul runtime Reliable Actors standardně umožňuje vícenásobný přístup na základě kontextu logické volání. To umožňuje aktéři být vícenásobné, pokud jsou v tomtéž řetězu volání kontextu. Například objektu Actor A odešle zprávu do objektu Actor B, který odešle zprávu do objektu Actor C. Jako součást zpracování zpráv Pokud objektu Actor C volání objektu Actor A, zpráva je vícenásobné, takže bude možné. Všechny ostatní zprávy, které jsou součástí jiné volání kontextu se zablokuje na objektu Actor A její dokončení zpracování.

Existují dvě možnosti k dispozici pro opětovné zadání objektu actor definovaný v `ActorReentrancyMode` výčtu:

* `LogicalCallContext` (výchozí nastavení)
* `Disallowed` – Zakáže vícenásobný přístup

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
Vícenásobný přístup se dá nakonfigurovat v `ActorService`na nastavení během registrace. Toto nastavení platí pro všechny instance objektu actor vytvořen v rámci služby objektu actor.

Následující příklad ukazuje služby objektu actor, která nastaví režim vícenásobný přístup k `ActorReentrancyMode.Disallowed`. V tomto případě, pokud objekt actor odešle vícenásobné zprávu do jiného objektu actor, k výjimce typu `FabricException` bude vyvolána.

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


## <a name="next-steps"></a>Další postup
* Další informace o opětovné zadání v [referenční dokumentace rozhraní API objektu Actor](https://msdn.microsoft.com/library/azure/dn971626.aspx)
