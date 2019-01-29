---
title: Vývoj testů jednotek pro stavové služby v Azure Service Fabric | Dokumentace Microsoftu
description: Zjistěte, jak vyvinout testy jednotky pro Service Fabric stavové služby.
services: service-fabric
documentationcenter: .net
author: charleszipp
manager: timlt
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/04/2018
ms.author: ryanwi
ms.openlocfilehash: a030860bcef41d7276e1356553b984f55e27ae1e
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55164151"
---
# <a name="create-unit-tests-for-stateful-services"></a>Vytvoření testovaní částí pro stavové služby
Testování stavové služby Service Fabric řešit běžné chyby, které nemusí nutně být zachycena konvenční aplikace nebo testování částí specifického pro doménu. Při vývoji, testování částí pro stavové služby, jsou některé důležité informace, které se uchovávají v úvahu.

1. Každé repliky spustí kód aplikace, ale v jiném kontextu. Pokud služba používá tři repliky, je na třech uzlech souběžně v rámci různých kontextu/role provádění kódu služby.
2. Stav uložené ve stavové služby by měl být konzistentní mezi všemi replikami. Tato konzistence out-of-the-box bude poskytovat state manager a spolehlivých kolekcí. Stav v paměti bude ale potřeba spravovat kódem aplikace.
3. Každá replika se změní role v určitém okamžiku během spuštěné v clusteru. Sekundární repliky se stane primární, v případě, že uzel, který je hostitelem primární stane nedostupná nebo přetížená. Toto je přirozeně chování pro Service Fabric, proto musíte počítat nakonec provádění v rámci jiné role služby.

Tento článek předpokládá, že [testování stavové služby v Service Fabric](service-fabric-concepts-unit-testing.md) byla načtena.

## <a name="the-servicefabricmocks-library"></a>Knihovna ServiceFabric.Mocks
Od verze 3.3.0 [ServiceFabric.Mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/) poskytuje rozhraní API pro vytvoření modelu orchestraci repliky a správu stavu. Ten se použije v příkladech.

[Nuget](https://www.nuget.org/packages/ServiceFabric.Mocks/)
[GitHub](https://github.com/loekd/ServiceFabric.Mocks)

*ServiceFabric.Mocks není vlastní nebo spravuje Microsoft. To je však aktuálně Microsoft doporučuje knihovny pro stavové služby testování částí.*

## <a name="set-up-the-mock-orchestration-and-state"></a>Nastavení stavu a mock Orchestrace
Jako součást uspořádat část testu nastavte mock repliky a vytvoří se správce stavu. Sady replik se pak vlastní vytvoření instance otestované služby pro jednotlivé repliky. Například ho budou spuštěné události životního cyklu také vlastní `OnChangeRole` a `RunAsync`. Správce stavu mock zajistí jsou všechny operace provedené na správce stavu spuštění a nejlépe by správce stavu.

1. Vytvořte objekt pro vytváření delegáta služby, který vytvoří instanci služby testování. To by měl být podobné nebo stejné jako zpětné volání objekt pro vytváření služeb se většinou nacházejí ve `Program.cs` pro službu Service Fabric nebo objektu actor. To by měl postupovat podle následující podpis:
```csharp
MyStatefulService CreateMyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica2 stateManager)
```
2. Vytvoření instance `MockReliableStateManager` třídy. To bude napodobení všechny interakce s správce stavu.
3. Vytvoření instance `MockStatefulServiceReplicaSet<TStatefulService>` kde `TStatefulService` je typ služby testování. To vyžaduje delegáta vytvořili v kroku #1 a správce stavu instance v #2
4. Přidání repliky do sady replik. Zadejte role (jako je primární, ActiveSecondary, IdleSecondary) a s ID repliky
> Blokovat repliky ID! Jedná se pravděpodobně použijí během operace a vyhodnocení části testování částí.

```csharp
//service factory to instruct how to create the service instance
var serviceFactory = (StatefulServiceContext context, IReliableStateManagerReplica2 stateManager) => new MyStatefulService(context, stateManager);
//instantiate a new mock state manager
var stateManager = new MockReliableStateManager();
//instantiate a new replica set with the service factory and state manager
var replicaSet = new MockStatefulServiceReplicaSet<MyStatefulService>(CreateStatefulService, stateManager);
//add a new Primary replica with id 1
await replicaSet.AddReplicaAsync(ReplicaRole.Primary, 1);
//add a new ActiveSecondary replica with id 2
await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 2);
//add a second ActiveSecondary replica with id 3
await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 3);
```

## <a name="execute-service-requests"></a>Provedení žádosti o služby
Žádosti o služby je možné provést v konkrétní repliky pomocí vlastnosti pohodlí a vyhledávání.
```csharp
const string stateName = "test";
var payload = new Payload(StatePayload);

//execute a request on the primary replica using
await replicaSet.Primary.ServiceInstance.InsertAsync(stateName, payload);

//execute a request against replica with id 2
await replicaSet[2].ServiceInstance.InsertAsync(stateName, payload);

//execute a request against one of the active secondary replicas
await replicaSet.FirstActiveSecondary.InsertAsync(stateName, payload);
```

## <a name="execute-a-service-move"></a>Provést přesun služeb
Mock replik uvádí několik vhodných metod k aktivaci různé druhy přesune služby.
```csharp
//promote the first active secondary to primary
replicaSet.PromoteNewReplicaToPrimaryAsync();
//promote the secondary with replica id 4 to primary
replicaSet.PromoteNewReplicaToPrimaryAsync(4);

//promote the first idle secondary to an active secondary
PromoteIdleSecondaryToActiveSecondaryAsync();
//promote idle secondary with replica id 4 to active secondary
PromoteIdleSecondaryToActiveSecondaryAsync(4);

//add a new replica with randomly assigned replica id and promote it to primary
PromoteNewReplicaToPrimaryAsync()
//add a new replica with replica id 4 and promote it to primary
PromoteNewReplicaToPrimaryAsync(4)
```

## <a name="putting-it-all-together"></a>Spojení všech součástí dohromady
Následující testovací ukazuje nastavení sady replik třemi uzly a ověření, že data jsou k dispozici ze sekundární po změně role. Typické potíže to může zachytit, je-li přidat data během `InsertAsync` byl uložen do něco paměti nebo do spolehlivé kolekce bez spuštění `CommitAsync`. V obou případech bude synchronizován s primární sekundární. To by vedlo ke nekonzistentní odpovědi po přesunutí služby.

```csharp
[TestMethod]
public async Task TestServiceState_InMemoryState_PromoteActiveSecondary()
{
    var stateManager = new MockReliableStateManager();
    var replicaSet = new MockStatefulServiceReplicaSet<MyStatefulService>(CreateStatefulService, stateManager);
    await replicaSet.AddReplicaAsync(ReplicaRole.Primary, 1);
    await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 2);
    await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 3);

    const string stateName = "test";
    var payload = new Payload(StatePayload);

    //insert data
    await replicaSet.Primary.ServiceInstance.InsertAsync(stateName, payload);
    //promote one of the secondaries to primary
    await replicaSet.PromoteActiveSecondaryToPrimaryAsync(2);
    //get data
    var payloads = (await replicaSet.Primary.ServiceInstance.GetPayloadsAsync()).ToList();

    //data should match what was inserted against the primary
    Assert.IsTrue(payloads.Count == 1);
    Assert.IsTrue(payloads[0].Content == payload.Content);

    //verify the data was saved against the reliable dictionary
    var dictionary = await StateManager.GetOrAddAsync<IReliableDictionary<string, Payload>>(MyStatefulService.StateManagerDictionaryKey);
    using(var tx = StateManager.CreateTransaction())
    {
        var payload = await dictionary.TryGetValue(stateName);
        Assert.IsTrue(payload.HasValue);
        Assert.IsTrue(payload.Value.Content == payload.Content);
    }
}
```

## <a name="next-steps"></a>Další postup
Zjistěte, jak otestovat [komunikace service to service](service-fabric-testability-scenarios-service-communication.md) a [simulace chyb pomocí řízeného chaosu](service-fabric-controlled-chaos.md).
