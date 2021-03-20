---
title: Vývoj testů jednotek pro stavové služby
description: Přečtěte si o testování částí v Azure Service Fabric pro stavové služby a o speciálních faktorech, které byste měli mít na paměti při vývoji.
ms.topic: conceptual
ms.date: 09/04/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 287c0544daa3c44d91fd336b502c496b9b4bb266
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89011391"
---
# <a name="create-unit-tests-for-stateful-services"></a>Vytváření testů jednotek pro stavové služby
Testování částí Service Fabric stavové služby odhalí běžné chyby, které by se nemusely zachytit konvenční aplikací nebo testováním jednotek specifických pro doménu. Při vývoji testů jednotek pro stavové služby je potřeba mít na paměti několik zvláštních hledisek, které byste měli mít na paměti.

1. Každá replika spustí kód aplikace, ale v jiném kontextu. Pokud služba používá tři repliky, kód služby se spouští na třech uzlech paralelně pod jiným kontextem/rolí.
2. Stav uložený v rámci stavové služby by měl být konzistentní mezi všemi replikami. Tato konzistence bude poskytovat správce stavu a spolehlivé kolekce. Stav v paměti bude však nutné spravovat pomocí kódu aplikace.
3. Každá replika při spuštění v clusteru změní role v určitém bodě. Sekundární replika se stane primárním v případě, že uzel hostující primární uzel přestane být dostupný nebo přetížený. Jedná se o přirozené chování, které Service Fabric proto, aby služby bylo nutné naplánovat na jejich případné provedení v rámci jiné role.

V tomto článku se předpokládá, že jste si přečetli [stavové služby testování částí v Service Fabric](service-fabric-concepts-unit-testing.md) .

## <a name="the-servicefabricmocks-library"></a>Knihovna ServiceFabric. makety
Od verze 3.3.0 poskytuje [ServiceFabric. makety](https://www.nuget.org/packages/ServiceFabric.Mocks/) rozhraní API pro napodobování orchestrace replik a správy stavů. Tato akce bude použita v příkladech.

[NuGet](https://www.nuget.org/packages/ServiceFabric.Mocks/) 
 [GitHub](https://github.com/loekd/ServiceFabric.Mocks)

*ServiceFabric. makety nejsou vlastněny nebo spravovány společností Microsoft. Tato služba je však v současnosti doporučenou knihovnou pro stavové služby testování částí.*

## <a name="set-up-the-mock-orchestration-and-state"></a>Nastavení modelu a orchestrace a stavu
V rámci uspořádání v rámci testu se vytvoří podobná sada replik a stavový správce. Sada replik pak bude vlastnit vytvoření instance testované služby pro každou repliku. Bude také vlastnit události životního cyklu, jako je `OnChangeRole` a `RunAsync` . Správce státních stavů zajistí, že všechny operace provedené proti správci stavu jsou spuštěny a udržovány jako skutečný správce stavu.

1. Vytvořte delegáta továrny služby, který vytvoří instanci testované služby. To by mělo být podobné nebo stejné jako zpětné volání služby Service Factory obvykle nalezeno v nástroji `Program.cs` Service Fabric služby nebo objektu actor. Mělo by následovat následující signatura:
   ```csharp
   MyStatefulService CreateMyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica2 stateManager)
   ```
2. Vytvoří instanci `MockReliableStateManager` třídy. Tím se navede všechny interakce se správcem stavu.
3. Vytvořte instanci, `MockStatefulServiceReplicaSet<TStatefulService>` kde `TStatefulService` je typ testované služby. To bude vyžadovat, aby byl delegát vytvořen v kroku #1 a vytvořila se instance Správce stavu v #2
4. Přidejte repliky do sady replik. Zadejte roli (například primární, ActiveSecondary, IdleSecondary) a ID repliky.
   > Přidržte se k ID repliky. Tyto akce budou pravděpodobně použity během provádění a vyhodnocení částí testu jednotek.

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

## <a name="execute-service-requests"></a>Spustit žádosti o služby
Žádosti o službu se dají provádět na konkrétní replice s použitím vlastností a vyhledávání na pohodlí.
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

## <a name="execute-a-service-move"></a>Provést přesun služby
Sada popsaných replik zpřístupňuje několik pohodlných metod, které aktivují různé typy přesunů služeb.
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
Následující test znázorňuje nastavení sady replik tří uzlů a ověření, že data jsou k dispozici ze sekundárního po změně role. Typický problém, který může být zachycen, je v případě, že data přidaná během `InsertAsync` byla uložena do paměti nebo do spolehlivé kolekce bez spuštění `CommitAsync` . V obou případech by sekundární databáze nebyla synchronizovaná s primární. To by mohlo vést k nekonzistentním odpovědím po přesunu služby.

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
Naučte se testovat [komunikaci](service-fabric-testability-scenarios-service-communication.md) mezi službami a [Simulovat selhání pomocí řízených chaos](service-fabric-controlled-chaos.md).
