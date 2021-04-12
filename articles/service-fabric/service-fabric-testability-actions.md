---
title: Simulace chyb v mikroslužbách Azure
description: Tento článek pojednává o akcích testování zjištěných v Microsoft Azure Service Fabric.
ms.topic: conceptual
ms.date: 03/26/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 9f8221d92ded33350b182cce5d28dd889beae4a5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732869"
---
# <a name="testability-actions"></a>Testovací akce
Pro simulaci nespolehlivé infrastruktury vám Azure Service Fabric poskytne vývojářům možnosti simulovat různé chyby reálného světa a přechody do stavu. Jsou přístupné jako akce testování. Akce jsou rozhraní API na nízké úrovni, která způsobují konkrétní vkládání chyb, přechod stavu nebo ověřování. Kombinací těchto akcí můžete napsat komplexní testovací scénáře pro vaše služby.

Service Fabric poskytuje některé běžné testovací scénáře složené z těchto akcí. Důrazně doporučujeme, abyste využili tyto integrované scénáře, které pečlivě vybraly k testování běžných přechodů mezi stavy a případů selhání. Nicméně akce lze použít k vytvoření vlastních testovacích scénářů, pokud chcete přidat pokrytí pro scénáře, které ještě nejsou zahrnuté v předdefinovaných scénářích nebo které jsou vlastní, přizpůsobené pro vaši aplikaci.

Implementace jazyka C# pro akce jsou nalezeny v sestavení System.Fabric.dll. Modul PowerShellu pro System Fabric se nachází v sestavení Microsoft.ServiceFabric.Powershell.dll. V rámci instalace modulu runtime je nainstalovaný modul ServiceFabric PowerShell, který umožňuje snadné použití.

## <a name="graceful-vs-ungraceful-fault-actions"></a>Řádné a nedarované akce při selhání
Testovací akce jsou klasifikovány do dvou hlavních sad:

* Nedarované chyby: tyto chyby simulují chyby, jako je restartování počítače a zpracování chyb. V takových případech selhání se kontext spuštění procesu náhle zastaví. To znamená, že nemůžete spustit žádné vyčištění stavu, než se aplikace znovu spustí.
* Řádné chyby: tyto chyby simulují řádné akce, jako je například přesunutí a obnovení repliky aktivované vyrovnáváním zatížení. V takových případech služba získá oznámení o zavření a před ukončením může stav vyčistit.

Pro lepší ověřování kvality spouštějte úlohy služby a podniku při vypínání různých řádných a nedarovaných chyb. Nedarované chyby způsobují scénáře, kdy se proces služby náhle ukončí v průběhu některých pracovních postupů. Tím se otestuje cesta pro obnovení po obnovení repliky služby pomocí Service Fabric. To vám pomůže zajistit konzistenci dat a to, jestli se stav služby udržuje správně po selhání. Druhá sada selhání (plynulé selhání) testuje, že služba správně reaguje na repliky, které se pohybují Service Fabric. Tento test zpracovává zrušení v metodě RunAsync. Služba musí ověřit, zda je nastaven token zrušení, správně uložit svůj stav a ukončit metodu RunAsync.

## <a name="testability-actions-list"></a>Seznam akcí testování
| Akce | Popis | Spravované rozhraní API | Rutina PowerShellu | Řádné/nedarované chyby |
| --- | --- | --- | --- | --- |
| CleanTestState |Odebere všechny stavy testu z clusteru v případě chybného vypnutí ovladače testu. |CleanTestStateAsync |Remove-ServiceFabricTestState |Neuvedeno |
| InvokeDataLoss |Vydělí ztrátu dat na oddíl služby. |InvokeDataLossAsync |Invoke-ServiceFabricPartitionDataLoss |Snížen |
| InvokeQuorumLoss |Vloží daný stavovou službu do ztráty kvora. |InvokeQuorumLossAsync |Invoke-ServiceFabricQuorumLoss |Snížen |
| Operace moveprimary |Přesune zadanou primární repliku stavové služby do zadaného uzlu clusteru. |MovePrimaryAsync |Move-ServiceFabricPrimaryReplica |Snížen |
| Movesecondary jde provést |Přesune aktuální sekundární repliku stavové služby na jiný uzel clusteru. |MoveSecondaryAsync |Move-ServiceFabricSecondaryReplica |Snížen |
| MoveInstance | Přesune aktuální instanci bezstavové služby na jiný uzel clusteru. | MoveInstanceAsync | Move-ServiceFabricInstance | Snížen |
| RemoveReplica |Simuluje selhání repliky odstraněním repliky z clusteru. Tím se replika ukončí a převede ji na role ' None '. tím se odebere celý stav z clusteru. |RemoveReplicaAsync |Remove-ServiceFabricReplica |Snížen |
| RestartDeployedCodePackage |Simuluje selhání procesu balíčku kódu restartováním balíčku kódu nasazeného na uzlu v clusteru. Tím dojde k přerušení procesu balíčku kódu, který restartuje všechny repliky služby uživatele hostované v tomto procesu. |RestartDeployedCodePackageAsync |Restart-ServiceFabricDeployedCodePackage |Nestandardním |
| RestartNode |Simuluje selhání uzlu clusteru Service Fabric restartováním uzlu. |RestartNodeAsync |Restart-ServiceFabricNode |Nestandardním |
| RestartPartition |Simuluje scénář Datacenter nedostupnosti nebo cluster nedostupnosti restartováním některých nebo všech replik oddílu. |RestartPartitionAsync |Restart-ServiceFabricPartition |Snížen |
| RestartReplica |Simuluje selhání repliky restartováním trvalé repliky v clusteru, zavřením repliky a jejím opětovným otevřením. |RestartReplicaAsync |Restart-ServiceFabricReplica |Snížen |
| StartNode |Spustí uzel v clusteru, který je již zastaven. |StartNodeAsync |Start-ServiceFabricNode |Neuvedeno |
| Příkaz stopnode |Simuluje selhání uzlu zastavením uzlu v clusteru. Uzel zůstane v nefunkčním případě volání StartNode. |StopNodeAsync |Stop-ServiceFabricNode |Nestandardním |
| ValidateApplication |Ověří dostupnost a stav všech služeb Service Fabric v rámci aplikace, obvykle po vystavení nějaké chyby do systému. |ValidateApplicationAsync |Test-ServiceFabricApplication |Neuvedeno |
| ValidateService |Ověří dostupnost a stav služby Service Fabric, obvykle po vystavení nějaké chybě systému. |ValidateServiceAsync |Test-ServiceFabricService |Neuvedeno |

## <a name="running-a-testability-action-using-powershell"></a>Spuštění akce testování pomocí prostředí PowerShell
V tomto kurzu se dozvíte, jak spustit akci testování pomocí prostředí PowerShell. Naučíte se, jak spustit akci testování v místním clusteru (s jedním box) nebo v clusteru Azure. Microsoft.Fabric.Powershell.dll – modul Service Fabric PowerShellu – se nainstaluje automaticky při instalaci Microsoft Service Fabric MSI. Modul se načte automaticky, když otevřete příkazový řádek PowerShellu.

Segmenty kurzu:

* [Spuštění akce v rámci clusteru s jedním box](#run-an-action-against-a-one-box-cluster)
* [Spuštění akce proti clusteru Azure](#run-an-action-against-an-azure-cluster)

### <a name="run-an-action-against-a-one-box-cluster"></a>Spuštění akce v rámci clusteru s jedním box
Pokud chcete spustit testovací akci pro místní cluster, nejdřív se připojte ke clusteru a otevřete příkazový řádek PowerShellu v režimu správce. Podíváme se na akci **restart-ServiceFabricNode** .

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

Tady se akce **restart-ServiceFabricNode** spouští na uzlu s názvem "Uzel1". Režim dokončování určuje, že by neměl ověřit, zda byla akce restartování uzlu skutečně úspěšná. Zadáním režimu dokončení jako "ověřit" bude možné ověřit, zda akce restartování skutečně proběhla úspěšně. Místo přímého zadání uzlu podle jeho názvu ho můžete zadat pomocí klíče oddílu a typu repliky následujícím způsobem:

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**Restart-ServiceFabricNode** by se měl použít k restartování uzlu Service Fabric v clusteru. Tím se zastaví proces Fabric.exe, čímž se restartuje všechny repliky systémové služby a služby uživatele hostované v tomto uzlu. Použití tohoto rozhraní API k otestování vaší služby pomáhá odhalit chyby po cestách obnovení převzetí služeb při selhání. Pomáhá simulovat selhání uzlu v clusteru.

Na následujícím snímku obrazovky vidíte v akci příkaz k **restartování-ServiceFabricNode** testování.

![Snímek obrazovky s spuštěním příkazu Restart-ServiceFabricNode v prostředí PowerShell](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

Výstup první rutiny **Get-ServiceFabricNode** (rutina z modulu Service Fabric PowerShellu) ukazuje, že místní cluster má pět uzlů: Node. 1 do Node. 5. Po provedení akce testování (rutina) **restart-ServiceFabricNode** na uzlu s názvem Node. 4 se zobrazí informace o tom, že doba provozu uzlu byla resetována.

### <a name="run-an-action-against-an-azure-cluster"></a>Spuštění akce proti clusteru Azure
Spuštění akce testování (pomocí prostředí PowerShell) proti clusteru Azure je podobné jako spuštění akce v rámci místního clusteru. Jediným rozdílem je, že před tím, než se připojíte k místnímu clusteru, musíte se nejdřív připojit ke clusteru Azure.

## <a name="running-a-testability-action-using-c35"></a>Spuštění akce testování pomocí jazyka C&#35;
Chcete-li spustit akci testování pomocí jazyka C#, nejprve se ke clusteru musíte připojit pomocí FabricClient. Pak Získejte parametry potřebné ke spuštění akce. Ke spuštění stejné akce lze použít různé parametry.
V případě, že se na akci RestartServiceFabricNode spustí jeden ze způsobů, jak ji spustit, je použít informace o uzlu (název uzlu a ID instance uzlu) v clusteru.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

Vysvětlení parametru:

* **CompleteMode** určuje, že režim by neměl ověřovat, zda akce restartování skutečně proběhla úspěšně. Zadáním režimu dokončení jako "ověřit" bude možné ověřit, zda akce restartování skutečně proběhla úspěšně.  
* **OperationTimeout** nastaví dobu, po kterou bude operace dokončena, než bude vyvolána výjimka TimeoutException.
* **CancellationToken** umožňuje zrušit probíhající volání.

Místo přímého určení uzlu podle jeho názvu ho můžete zadat pomocí klíče oddílu a typu repliky.

Další informace najdete v tématu PartitionSelector a ReplicaSelector.

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Fabric.Testability;
using System.Fabric;
using System.Threading;
using System.Numerics;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");
        string nodeName = "N0040";
        BigInteger nodeInstanceId = 130743013389060139;

        Console.WriteLine("Starting RestartNode test");
        try
        {
            //Restart the node by using ReplicaSelector
            RestartNodeAsync(clusterConnection, serviceName).Wait();

            //Another way to restart node is by using nodeName and nodeInstanceId
            RestartNodeAsync(clusterConnection, nodeName, nodeInstanceId).Wait();
        }
        catch (AggregateException exAgg)
        {
            Console.WriteLine("RestartNode did not complete: ");
            foreach (Exception ex in exAgg.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("RestartNode completed.");
        return 0;
    }

    static async Task RestartNodeAsync(string clusterConnection, Uri serviceName)
    {
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);
        ReplicaSelector primaryofReplicaSelector = ReplicaSelector.PrimaryOf(randomPartitionSelector);

        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(primaryofReplicaSelector, CompletionMode.Verify);
    }

    static async Task RestartNodeAsync(string clusterConnection, string nodeName, BigInteger nodeInstanceId)
    {
        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(nodeName, nodeInstanceId, CompletionMode.Verify);
    }
}
```

## <a name="partitionselector-and-replicaselector"></a>PartitionSelector a ReplicaSelector
### <a name="partitionselector"></a>PartitionSelector
PartitionSelector je pomocná pomocná pro účely testování a slouží k výběru konkrétního oddílu, na kterém se provádí akce testování. Dá se použít k výběru konkrétního oddílu, pokud je ID oddílu známé předem. Nebo můžete zadat klíč oddílu a operace vyřeší ID oddílu interně. Máte také možnost vybrat náhodný oddíl.

Pro použití této pomocné rutiny vytvořte objekt PartitionSelector a vyberte oddíl pomocí jedné z metod SELECT *. Pak předejte objekt PartitionSelector do rozhraní API, které to vyžaduje. Pokud není vybraná žádná možnost, použije se výchozí náhodný oddíl.

```csharp
Uri serviceName = new Uri("fabric:/samples/InMemoryToDoListApp/InMemoryToDoListService");
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
string partitionName = "Partition1";
Int64 partitionKeyUniformInt64 = 1;

// Select a random partition
PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

// Select a partition based on ID
PartitionSelector partitionSelectorById = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);

// Select a partition based on name
PartitionSelector namedPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionName);

// Select a partition based on partition key
PartitionSelector uniformIntPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionKeyUniformInt64);
```

### <a name="replicaselector"></a>ReplicaSelector
ReplicaSelector je pomocná pomocná při testování a slouží k výběru repliky, na které se provádí akce testování. Dá se použít k výběru konkrétní repliky, pokud je ID repliky známé předem. Kromě toho máte možnost vybrat primární repliku nebo náhodnou sekundární hodnotu. ReplicaSelector je odvozen z PartitionSelector, takže je třeba vybrat repliku i oddíl, na kterém chcete provést operaci testování.

Chcete-li použít tohoto pomocníka, vytvořte objekt ReplicaSelector a nastavte způsob, jakým chcete vybrat repliku a oddíl. Pak ho můžete předat do rozhraní API, které to vyžaduje. Pokud není vybraná žádná možnost, použije se výchozí hodnota náhodně replika a náhodný oddíl.

```csharp
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);
long replicaId = 130559876481875498;

// Select a random replica
ReplicaSelector randomReplicaSelector = ReplicaSelector.RandomOf(partitionSelector);

// Select the primary replica
ReplicaSelector primaryReplicaSelector = ReplicaSelector.PrimaryOf(partitionSelector);

// Select the replica by ID
ReplicaSelector replicaByIdSelector = ReplicaSelector.ReplicaIdOf(partitionSelector, replicaId);

// Select a random secondary replica
ReplicaSelector secondaryReplicaSelector = ReplicaSelector.RandomSecondaryOf(partitionSelector);
```

## <a name="next-steps"></a>Další kroky
* [Scénáře testování](service-fabric-testability-scenarios.md)
* Jak testovat službu
  * [Simulace chyb během úloh služby](service-fabric-testability-workload-tests.md)
  * [Chyby komunikace mezi službami a službami](service-fabric-testability-scenarios-service-communication.md)

