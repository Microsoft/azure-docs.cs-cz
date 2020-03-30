---
title: Simulace selhání v mikroslužbách Azure
description: Tento článek popisuje akce testovatelnosti, které se nacházejí ve službě Microsoft Azure Fabric.
author: motanv
ms.topic: conceptual
ms.date: 06/07/2017
ms.author: motanv
ms.openlocfilehash: 4bdb00eec38addc0c9f88eba8b73185ec5721277
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282039"
---
# <a name="testability-actions"></a>Akce testovatelnosti
Aby bylo možné simulovat nespolehlivé infrastruktury, Azure Service Fabric poskytuje vám, vývojář, způsoby, jak simulovat různé selhání reálného světa a přechody stavu. Ty jsou vystaveny jako akce testovatelnosti. Akce jsou nízkoúrovňová prostředí API, která způsobují konkrétní vkládání chyb, přechod stavu nebo ověření. Kombinací těchto akcí můžete napsat komplexní testovací scénáře pro vaše služby.

Service Fabric poskytuje některé běžné testovací scénáře složené z těchto akcí. Důrazně doporučujeme využít tyto předdefinované scénáře, které jsou pečlivě vybrány k testování běžných přechodů stavu a selhání. Akce však lze použít k vytvoření vlastní testovací scénáře, pokud chcete přidat pokrytí pro scénáře, které nejsou zahrnuty do předdefinované scénáře ještě nebo které jsou přizpůsobeny pro vaši aplikaci.

C# implementace akcí se nacházejí v sestavení System.Fabric.dll. Modul System Fabric PowerShell se nachází v sestavení Microsoft.ServiceFabric.Powershell.dll. V rámci instalace modulu runtime je nainstalován modul ServiceFabric PowerShell, který umožňuje snadné použití.

## <a name="graceful-vs-ungraceful-fault-actions"></a>Elegantní vs. ungraceful chybové akce
Akce testovatelnosti jsou rozděleny do dvou hlavních kbelíků:

* Neřádné chyby: Tyto chyby simulují chyby, jako je restartování počítače a selhání procesu. V takových případech selhání spuštění kontextu procesu zastaví náhle. To znamená, že žádné vyčištění stavu lze spustit před spuštěním aplikace znovu.
* Ladné chyby: Tyto chyby simulují ladné akce, jako jsou přesuny a poklesy repliky spouštěné vyrovnáváním zatížení. V takových případech služba získá oznámení o zavřít a můžete vyčistit stav před ukončením.

Pro lepší kvalitu ověření, spusťte služby a obchodní úlohy při vyvolání různých elegantní a ungraceful chyby. Ungraceful chyby cvičení scénáře, kde proces služby náhle ukončí uprostřed některé pracovního postupu. Tím otestujete cestu obnovení, jakmile je replika služby obnovena service fabric. To pomůže otestovat konzistenci dat a zda je stav služby udržován správně po selhání. Další sada selhání (řádné selhání) test, který služba správně reaguje na repliky přesunutí service fabric. To testuje zpracování zrušení v RunAsync metoda. Služba musí zkontrolovat, zda je nastaven token zrušení, správně uložit jeho stav a ukončit metodu RunAsync.

## <a name="testability-actions-list"></a>Seznam akcí testovatelnosti
| Akce | Popis | Spravované rozhraní API | Rutina prostředí PowerShell | Ladné/neladivé chyby |
| --- | --- | --- | --- | --- |
| CleanTestState |Odebere veškerý testovací stav z clusteru v případě chybnévypnutí testovacího ovladače. |CleanTestStateAsync |Remove-ServiceFabricTestState |Neuvedeno |
| InvokeDataLoss |Vyvolá ztrátu dat do oddílu služby. |InvokeDataLossAsync |Invoke-ServiceFabricPartitionDataLoss |Elegantní |
| InvokeQuorumLoss |Vloží daný oddíl stavové služby do ztráty kvora. |InvokeQuorumLossAsync |Vyvolat-ServiceFabricQuorumLoss |Elegantní |
| PřesunoutPrimární |Přesune zadanou primární repliku stavové služby do zadaného uzlu clusteru. |MovePrimaryAsync |Přesunout ServiceFabricPrimárníreplika |Elegantní |
| MoveSekundární |Přesune aktuální sekundární repliku stavové služby do jiného uzlu clusteru. |MoveSecondaryAsync |Přesunout ServiceFabricSekundární replika |Elegantní |
| Odebrat repliku |Simuluje selhání repliky odebráním repliky z clusteru. Tím se zavře replika a převede ji do role Žádný, odebrání majestátu z clusteru. |Odstranitreplikasynchronizaci |Odebrat repliku služby ServiceFabric |Elegantní |
| Balíček RestartDeployedCodePackage |Simuluje selhání procesu balíčku kódu restartováním balíčku kódu nasazeného v uzlu v clusteru. Tím se přeruší proces balíčku kódu, který restartuje všechny repliky uživatelských služeb hostované v tomto procesu. |RestartdeployedCodePackageAsync |Restart-ServiceFabricDeployedCodePackage |Ungraceful |
| Restartnode |Simuluje selhání uzlu clusteru Service Fabric restartováním uzlu. |RestartNodeAsync |Restartování aplikace ServiceFabricNode |Ungraceful |
| Restartovat oddíl |Simuluje scénář výpadku nebo zatemnění clusteru datovým centrem restartováním některých nebo všech replik oddílu. |RestartpartitionAsync |Restart-ServiceFabricPartition |Elegantní |
| Restartovat repliku |Simuluje selhání repliky restartováním trvalé repliky v clusteru, zavřením repliky a opětovným otevřením repliky. |RestartReplicaAsync |Restartovat repliku služby ServiceFabric |Elegantní |
| StartNode |Spustí uzel v clusteru, který je již zastaven. |Synchronizace StartNodeAsync |Start-ServiceFabricNode |Neuvedeno |
| StopNode |Simuluje selhání uzlu zastavením uzlu v clusteru. Uzel zůstane dolů, dokud startNode je volána. |StopNodeAsync |Stop-ServiceFabricNode |Ungraceful |
| Ověřit žádost |Ověří dostupnost a stav všech služeb Service Fabric v rámci aplikace, obvykle po vyvolání některé chyby do systému. |OvěřitApplicationAsync |Test-ServiceFabricAplikace |Neuvedeno |
| Ověřit službu |Ověří dostupnost a stav služby Service Fabric, obvykle po vyvolání některé chyby do systému. |OvěřitServiceAsync |Test-ServiceFabricService |Neuvedeno |

## <a name="running-a-testability-action-using-powershell"></a>Spuštění akce testovatelnosti pomocí PowerShellu
Tento kurz ukazuje, jak spustit akci testovatelnosti pomocí prostředí PowerShell. Dozvíte se, jak spustit akci testovatelnosti proti místnímu (one-box) clusteru nebo clusteru Azure. Microsoft.Fabric.Powershell.dll -- modul Service Fabric PowerShell - je nainstalován automaticky při instalaci Microsoft Service Fabric MSI. Modul se načte automaticky při otevření výzvy prostředí PowerShell.

Výukové segmenty:

* [Spuštění akce proti clusteru s jedním polem](#run-an-action-against-a-one-box-cluster)
* [Spuštění akce proti clusteru Azure](#run-an-action-against-an-azure-cluster)

### <a name="run-an-action-against-a-one-box-cluster"></a>Spuštění akce proti clusteru s jedním polem
Chcete-li spustit akci testovatelnosti proti místnímu clusteru, nejprve se připojte ke clusteru a otevřete výzvu prostředí PowerShell v režimu správce. Podívejme se na **akci Restart-ServiceFabricNode.**

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

Zde je akce **Restart-ServiceFabricNode** spuštěna na uzlu s názvem "Node1". Režim dokončení určuje, že by neměl ověřovat, zda akce restartování uzlu skutečně proběhla úspěšně. Určení režimu dokončení jako "Ověřit" způsobí, že ověří, zda akce restartování skutečně proběhla úspěšně. Namísto přímého zadání uzlu podle jeho názvu jej můžete zadat pomocí klíče oddílu a druhu repliky takto:

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**Restart-ServiceFabricNode** by měl být použit k restartování uzlu Service Fabric v clusteru. Tím se zastaví fabric.exe proces, který restartuje všechny repliky systémových služeb a uživatelských služeb hostované na tomto uzlu. Použití tohoto rozhraní API k testování služby pomáhá odhalit chyby podél cesty obnovení převzetí služeb při selhání. Pomáhá simulovat selhání uzlů v clusteru.

Následující snímek obrazovky ukazuje **příkaz Testovatelnost restartu ServiceFabricNode** v akci.

![](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

Výstup první **Get-ServiceFabricNode** (rutina z modulu Service Fabric PowerShell) ukazuje, že místní cluster má pět uzlů: Node.1 na Node.5. Po akce testovatelnosti (rutina) **Restart-ServiceFabricNode** je spuštěn a v uzlu s názvem Node.4, vidíme, že byla resetována doba provozu uzlu.

### <a name="run-an-action-against-an-azure-cluster"></a>Spuštění akce proti clusteru Azure
Spuštění akce testovatelnosti (pomocí prostředí PowerShell) proti clusteru Azure je podobné spuštění akce proti místnímu clusteru. Jediným rozdílem je, že před spuštěním akce, namísto připojení k místnímu clusteru, musíte se nejprve připojit ke clusteru Azure.

## <a name="running-a-testability-action-using-c35"></a>Spuštění akce testovatelnosti pomocí c&#35;
Chcete-li spustit akci testovatelnosti pomocí jazyka C#, musíte se nejprve připojit ke clusteru pomocí služby FabricClient. Potom získejte parametry potřebné ke spuštění akce. Ke spuštění stejné akce lze použít různé parametry.
Při pohledu na akci RestartServiceFabricNode je jedním ze způsobů, jak ji spustit, použití informací o uzlu (název uzlu a ID instance uzlu) v clusteru.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

Vysvětlení parametru:

* **CompleteMode** určuje, že režim by neměl ověřit, zda akce restartování skutečně proběhla úspěšně. Určení režimu dokončení jako "Ověřit" způsobí, že ověří, zda akce restartování skutečně proběhla úspěšně.  
* **OperationTimeout** nastaví dobu, po kterou má být operace dokončena před vyvolání výjimky výjimky timeoutexception.
* **CancellationToken** umožňuje čekající volání, které má být zrušeno.

Namísto přímého zadání uzlu podle jeho názvu jej můžete zadat pomocí klíče oddílu a druhu repliky.

Další informace naleznete v tématu PartitionSelector a ReplicaSelector.

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

## <a name="partitionselector-and-replicaselector"></a>Výběr oddílů a volič replik
### <a name="partitionselector"></a>Volič oddílů
PartitionSelector je pomocník vystavený v testovatelnosti a slouží k výběru konkrétního oddílu, na kterém chcete provést některou z akcí testovatelnosti. Lze jej použít k výběru konkrétního oddílu, pokud je ID oddílu předem známo. Nebo můžete zadat klíč oddílu a operace vyřeší ID oddílu interně. Máte také možnost vybrat náhodný oddíl.

Chcete-li použít tento pomocník, vytvořte objekt PartitionSelector a vyberte oddíl pomocí jedné z metod Select*. Pak předajte v PartitionSelector objektu rozhraní API, které ji vyžaduje. Pokud není vybrána žádná možnost, je výchozí na náhodný oddíl.

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

### <a name="replicaselector"></a>Volič replik
ReplicaSelector je pomocník vystavený v testovatelnosti a slouží k výběru repliky, na které chcete provést některou z akcí testovatelnosti. Lze jej použít k výběru konkrétní repliky, pokud id repliky je známpředem. Kromě toho máte možnost vybrat primární repliku nebo náhodné sekundární. ReplicaSelector je odvozen od PartitionSelector, takže je třeba vybrat repliku i oddíl, na kterém chcete provést operaci testovatelnosti.

Chcete-li použít tento pomocník, vytvořte objekt ReplicaSelector a nastavte způsob, jakým chcete vybrat repliku a oddíl. Potom jej můžete předat do rozhraní API, které to vyžaduje. Pokud není vybrána žádná možnost, je výchozí náhodná replika a náhodný oddíl.

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
* [Scénáře testovatelnosti](service-fabric-testability-scenarios.md)
* Jak otestovat službu
  * [Simulace selhání během úloh služby](service-fabric-testability-workload-tests.md)
  * [Selhání komunikace mezi službami](service-fabric-testability-scenarios-service-communication.md)

