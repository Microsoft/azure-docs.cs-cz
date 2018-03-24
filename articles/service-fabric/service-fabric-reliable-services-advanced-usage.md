---
title: "Rozšířené použití spolehlivé služby | Microsoft Docs"
description: "Další informace o pokročilé využití spolehlivé služby Service Fabric přidané flexibilitu v službě."
services: Service-Fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: masnider
ms.assetid: f2942871-863d-47c3-b14a-7cdad9a742c7
ms.service: Service-Fabric
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/9/2018
ms.author: vturecek
ms.openlocfilehash: 48504f258b13a7ff5f4c91db2d9de09269e92424
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="advanced-usage-of-the-reliable-services-programming-model"></a>Rozšířené použití spolehlivé služby programovací model
Azure Service Fabric zjednodušuje zápis a správu spolehlivé bezstavové a stavové služby. Tato příručka pojednává o Pokročilé použití spolehlivé služby k získání další kontrolu a flexibilitu přes vaše služby. Před přečtení tohoto průvodce, seznamte se s [programovací model spolehlivé služby](service-fabric-reliable-services-introduction.md).

Stavová a Bezstavová služby mají dvě primární vstupní body pro uživatelský kód:

* `RunAsync(C#) / runAsync(Java)` je pro obecné účely vstupní bod pro kód služby.
* `CreateServiceReplicaListeners(C#)` a `CreateServiceInstanceListeners(C#) / createServiceInstanceListeners(Java)` je pro otevření naslouchací procesy komunikace pro požadavky klientů.

Pro většinu služby jsou tyto dva vstupní body dostatečná. Ve výjimečných případech Pokud větší kontrolu nad životního cyklu služby je nutné, další životního cyklu události jsou k dispozici.

## <a name="stateless-service-instance-lifecycle"></a>Instance bezstavové služby životního cyklu
Životní cyklus bezstavové služby je velmi jednoduchý. Bezstavové služby můžete pouze otevřít, ukončeno nebo byl zrušen. `RunAsync` v bezstavové služby se spustí, až instance služby je otevřít a zrušit, pokud instance služby je uzavřen nebo přerušena.

I když `RunAsync` by mělo být dostatečné v téměř všech případech otevřené, zavřete a přerušení události v bezstavové služby jsou k dispozici také:

* `Task OnOpenAsync(IStatelessServicePartition, CancellationToken) - C# / CompletableFuture<String> onOpenAsync(CancellationToken) - Java` OnOpenAsync je volána, když instance bezstavové služby se má použít. V tuto chvíli můžete spustit úlohy inicializace služby rozšířených.
* `Task OnCloseAsync(CancellationToken) - C# / CompletableFuture onCloseAsync(CancellationToken) - Java` OnCloseAsync je volána, když bude instance bezstavové služby korektně vypnout. To může dojít, když probíhá upgrade služby kódu, instance služby přesouvá kvůli Vyrovnávání zatížení nebo se detekuje přechodná chyba. OnCloseAsync lze bezpečně zavřete všechny prostředky, zastavte všechny zpracování na pozadí, dokončení ukládání externí stavu nebo dolů existující připojení.
* `void OnAbort() - C# / void onAbort() - Java` OnAbort je volána, když instance bezstavové služby je vynuceně vypnut. Obecně se používá při zjištění trvalé selhání na uzlu, nebo když Service Fabric se nedají spravovat spolehlivě životního cyklu instance služby z důvodu vnitřní chyby.

## <a name="stateful-service-replica-lifecycle"></a>Životní cyklus repliky stavové služby

Životní cyklus repliku stavové služby je mnohem složitější než instance bezstavové služby. Kromě toho pokud chcete otevřít, zavřete a zrušení události, obsahuje repliku stavové služby role změny během celé jeho životnosti. Když se změní repliku stavové služby role, `OnChangeRoleAsync` je aktivována událost:

* `Task OnChangeRoleAsync(ReplicaRole, CancellationToken)` OnChangeRoleAsync je volána, když replika stavové služby je například změna role na primární nebo sekundární. Primární repliky mají stav zápisu (je povoleno vytvoření a zápis do spolehlivé kolekcí). Sekundární repliky jsou uvedeny čtení stav (můžete číst jenom z existující spolehlivé kolekce). Většinu práce v stavové služby se provádí na primární replice. Sekundární repliky lze provést ověření jen pro čtení, generování sestav, dolování dat nebo jiné úlohy jen pro čtení.

V stavové služby pouze primární replika má oprávnění k zápisu do stavu a proto je obecně při službu provádí samotnou práci. `RunAsync` Metoda v stavové služby se spustí jenom v případě, že je primární replika stavové služby. `RunAsync` Metoda se zruší, když se změní role primární repliky z primární i během události zavřít a přerušení.

Pomocí `OnChangeRoleAsync` událostí můžete pro práci v závislosti na role repliky také reakci na změnu role.

Stavové služby také poskytuje stejné události životního cyklu čtyři jako bezstavové služby, se stejnou sémantiku a případy použití:

```csharp
* Task OnOpenAsync(IStatefulServicePartition, CancellationToken)
* Task OnCloseAsync(CancellationToken)
* void OnAbort()
```

## <a name="next-steps"></a>Další postup
Pro pokročilejší témata týkající se Service Fabric naleznete v následujících článcích:

* [Konfigurace stavové spolehlivé služby](service-fabric-reliable-services-configuration.md)
* [Úvod stavu Service Fabric](service-fabric-health-introduction.md)
* [Pomocí sestav o stavu systému pro řešení potíží](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Konfigurace služeb pomocí Service Fabric clusteru správce prostředků](service-fabric-cluster-resource-manager-configure-services.md)
