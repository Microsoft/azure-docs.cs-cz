---
title: Vývoj testů částí pro stavové služby
description: Další informace o testování částí v Azure Service Fabric pro stavové služby a zvláštní aspekty, které je třeba mít na paměti během vývoje.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 9c657bd8295d01a4e0fa4e44e969b33946684bfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639832"
---
# <a name="create-unit-tests-for-stateful-services"></a>Vytvořit testy částí pro stavové služby
Testování částí Service Fabric stavové služby odkrývá běžné chyby, které by nutně být zachyceny konvenční aplikace nebo testování částí specifické pro doménu. Při vývoji testování částí pro stavové služby, existují některé zvláštní aspekty, které je třeba mít na paměti.

1. Každá replika spustí kód aplikace, ale v jiném kontextu. Pokud služba používá tři repliky, kód služby se provádí na třech uzlech paralelně v jiném kontextu nebo roli.
2. Stav uložený v rámci stavové služby by měl být konzistentní mezi všemi replikami. Správce stavu a spolehlivé kolekce bude poskytovat tuto konzistenci out-of-the-box. Stav v paměti však bude muset být spravován kódem aplikace.
3. Každá replika změní role v určitém okamžiku při spuštění v clusteru. Sekundární replika se stane primární v případě, že uzel hostující primární stane nedostupný nebo přetížené. Toto je přirozené chování pro Service Fabric proto služby musí plánovat pro nakonec provádění pod jinou roli.

Tento článek předpokládá, že [testování částí stavové služby v Service Fabric](service-fabric-concepts-unit-testing.md) byla přečtena.

## <a name="the-servicefabricmocks-library"></a>Knihovna ServiceFabric.Mocks
Od verze 3.3.0 [ServiceFabric.Mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/) poskytuje rozhraní API pro zesměšňování orchestraci replik a správy stavu. To bude použito v příkladech.

[Nuget](https://www.nuget.org/packages/ServiceFabric.Mocks/)
[GitHub](https://github.com/loekd/ServiceFabric.Mocks)

*ServiceFabric.Mocks není vlastněna nebo udržována společností Microsoft. Vsoučasné době se však jedná o knihovnu doporučenou společností Microsoft pro stavové služby testování částí.*

## <a name="set-up-the-mock-orchestration-and-state"></a>Nastavení falešné orchestrace a stavu
Jako součást uspořádat část testu mock replika sada a správce stavu budou vytvořeny. Sada replik pak bude vlastnit vytvoření instance testované služby pro každou repliku. Bude také vlastnit provádění událostí životního `OnChangeRole` `RunAsync`cyklu, jako je například a . Falešný správce stavu zajistí, že všechny operace prováděné proti správci stavu jsou spuštěny a udržovány jako skutečný správce stavu.

1. Vytvořte delegáta factory služby, který vytvoří instanci testované služby. To by mělo být podobné nebo stejné jako `Program.cs` zpětné volání factory služby obvykle nalézt v pro službu Service Fabric nebo objekt actor. To by mělo následovat následující podpis:
   ```csharp
   MyStatefulService CreateMyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica2 stateManager)
   ```
2. Vytvořte instanci třídy. `MockReliableStateManager` To bude zesměšňovat všechny interakce se správcem stavu.
3. Vytvořte instanci, `MockStatefulServiceReplicaSet<TStatefulService>` kde `TStatefulService` je typ testované služby. To bude vyžadovat delegáta vytvořeného v kroku #1 a správce stavu vytvořený v #2
4. Přidejte repliky do sady replik. Zadejte roli (například Primární, ActiveSecondary, IdleSecondary) a ID repliky
   > Držte si id repliky! Ty budou pravděpodobně použity během úkonu a uplatnit části testu částí.

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

## <a name="execute-service-requests"></a>Spouštět požadavky na služby
Požadavky na služby lze provést na konkrétní repliku pomocí pohodlí vlastnosti a vyhledávání.
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

## <a name="execute-a-service-move"></a>Provedení přesunu služby
Falešná sada replik poskytuje několik metod pohodlí pro aktivaci různých typů přesunů služby.
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
Následující test ukazuje nastavení sady replik tří uzlů a ověření, zda jsou data po změně role k dispozici ze sekundárního stavu. Typický problém to může zachytit je, `InsertAsync` pokud data přidaná během byl uložen buď `CommitAsync`do něco v paměti nebo spolehlivé kolekce bez spuštění . V obou případech sekundární by být synchronizovány s primární. To by vedlo k nekonzistentní odpovědi po přesuny služby.

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

## <a name="next-steps"></a>Další kroky
Naučte se testovat [komunikaci mezi službami](service-fabric-testability-scenarios-service-communication.md) a [simulovat selhání pomocí řízeného chaosu](service-fabric-controlled-chaos.md).
