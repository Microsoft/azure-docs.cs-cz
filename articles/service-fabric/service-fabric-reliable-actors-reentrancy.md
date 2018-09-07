---
title: Vícenásobný přístup v Azure Service Fabric actors | Dokumentace Microsoftu
description: Úvod do vícenásobného přístupu pro Service Fabric Reliable Actors.
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
ms.openlocfilehash: c7a4066a949ad6e66c45dff67f1e80801f2fa4cd
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44055256"
---
# <a name="reliable-actors-reentrancy"></a>Vícenásobný přístup Reliable Actors
Modul runtime Reliable Actors ve výchozím nastavení, umožňuje vícenásobného přístupu na základě kontextu logické volání. To umožňuje koncepce actorů je naprosto vícenásobné, když jsou v tomtéž řetězu volání kontextu. Například objektu Actor A odešle zprávu do objektu Actor B, který odešle zprávu do objektu Actor C. Při zpracování zprávy Pokud objekt Actor C volání objektu Actor A zpráva je vícenásobné, tak bude možné. Další zprávy, které jsou součástí jiné volání kontextu se zablokuje na objektu Actor A dokud se nedokončí zpracování.

Existují dvě možnosti k dispozici pro objekt actor opětovný vstup definovaný v `ActorReentrancyMode` výčtu:

* `LogicalCallContext` (výchozí nastavení)
* `Disallowed` – Zakáže vícenásobného přístupu

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
Vícenásobný přístup se dá nakonfigurovat v `ActorService`pro nastavení během registrace. Toto nastavení platí pro všechny instance objektu actor v objektu actor service vytvoří.

Následující příklad ukazuje služby objektu actor, který nastaví režim opětovný vstup do `ActorReentrancyMode.Disallowed`. V tomto případě, pokud prvek "actor" odešle zprávu vícenásobné na jiný objekt actor výjimku typu `FabricException` bude vyvolána výjimka.

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
* Další informace o vícenásobný přístup v [referenční dokumentace rozhraní API objektu Actor](https://msdn.microsoft.com/library/azure/dn971626.aspx)
