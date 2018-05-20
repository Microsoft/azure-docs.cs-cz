---
title: Simulace selhání v Azure mikroslužeb | Microsoft Docs
description: V tomto článku bude zmíněn testovatelnosti akce v Microsoft Azure Service Fabric nalezen.
services: service-fabric
documentationcenter: .net
author: motanv
manager: timlt
editor: heeldin
ms.assetid: ed53ca5c-4d5e-4b48-93c9-e386f32d8b7a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2017
ms.author: motanv
ms.openlocfilehash: 087a0f12f765b55c2e2976abd93d791409ff6d44
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="testability-actions"></a>Testovatelnosti akce
Chcete-li simulovat nespolehlivé infrastruktury, poskytuje Azure Service Fabric vás jako na vývojáři způsoby pro simulaci různé chyby reálného a přechodů mezi stavy. Tyto jsou zveřejněné jako testovatelnosti akce. Akce jsou nízké úrovně rozhraní API, která způsobí ověření, přechod stavu nebo konkrétní chyby vkládání. Kombinací těchto akcí můžete napsat scénáře komplexní testování pro vaše služby.

Service Fabric najdete že některé běžné scénáře, test se skládá z těchto akcí. Důrazně doporučujeme, aby využíváte těchto předdefinovaných scénáře, které jsou pečlivě zvolili k testování běžné přechodů mezi stavy a selhání případech. Však akce slouží k vytvoření scénáře vlastní testování, když chcete přidat pokrytí pro scénáře, které nejsou předmětem předdefinované scénáře ještě nebo které jsou vlastní, přizpůsobené pro vaši aplikaci.

C# implementace akce, které se nacházejí v sestavení System.Fabric.dll. V sestavení Microsoft.ServiceFabric.Powershell.dll nebyl nalezen modul prostředí PowerShell systému prostředků infrastruktury. Jako součást instalace modulu runtime je nainstalován modul ServiceFabric PowerShell umožňující snadné použití.

## <a name="graceful-vs-ungraceful-fault-actions"></a>Řádné oproti vynuceném selhání akce
Akce testovatelnosti se zařazují do dvou hlavních kbelíků:

* Vynuceném chyb: Simulace selhání, jako je restartování počítače tyto chyby a zpracování dojde k chybě. V takových případech chyb zastaví náhle kontextu spuštění procesu. To znamená, že žádné vyčištění stavu můžete spustit před spuštěním aplikace znovu.
* Řádné chyb: tyto chyby simulace řádně akcí, jako jsou repliky přesune a vyřazuje aktivovány Vyrovnávání zatížení. V takových případech služba získá oznámení o ukončení a můžete vyčistit stav před ukončením.

Pro lepší kvalitu ověření spouštění úloh služby a obchodní při vyvolat různé řádně a vynuceném chyb. Při vynuceném chyb výkonu scénáře, kde proces služby náhle ukončí uprostřed některé pracovního postupu. To testy cesta obnovení ihned po repliky služby pomocí Service Fabric. To vám pomůže testování konzistenci dat a zda je stav služby udržovat správně po selhání. Další sadu test selhání (řádně počet selhání), který služba správně reaguje na repliky přenášeny pomocí Service Fabric. V metodě RunAsync to testy zpracování zrušení. Služba potřebuje pro kontrolu se nastavit, správně uložit stav token zrušení a ukončete metodě RunAsync.

## <a name="testability-actions-list"></a>Seznam akcí testovatelnosti
| Akce | Popis | Spravovaná rozhraní API | Rutiny prostředí PowerShell | Řádné/vynuceném chyb |
| --- | --- | --- | --- | --- |
| CleanTestState |Odebere všechny stav testu z clusteru v případě, test ovladače chybné vypnutí. |CleanTestStateAsync |Remove-ServiceFabricTestState |Neuvedeno |
| InvokeDataLoss |Indukuje ztráty dat do oddílu služby. |InvokeDataLossAsync |Invoke-ServiceFabricPartitionDataLoss |Řádné |
| InvokeQuorumLoss |Oddíl dané stavové služby umístí do ztrátě kvora. |InvokeQuorumLossAsync |Vyvolání ServiceFabricQuorumLoss |Řádné |
| Přesunutí primární |Přesune zadaný primární repliky stavové služby do určeného uzlu clusteru. |MovePrimaryAsync |Move-ServiceFabricPrimaryReplica |Řádné |
| Přesunout sekundární |Aktuální sekundární replika stavové služby se přesune do jiného uzlu clusteru. |MoveSecondaryAsync |Přesunutí ServiceFabricSecondaryReplica |Řádné |
| RemoveReplica |Repliky selhání simuluje odebráním repliku z clusteru. To se zavře repliky a přejde do role 'None', všechny její stav odebrání z clusteru. |RemoveReplicaAsync |Odebrat ServiceFabricReplica |Řádné |
| RestartDeployedCodePackage |Simuluje selhání procesu balíček kódu restartováním balíček kódu nasazené na uzlu v clusteru. To zruší proces balíčku kódu, který restartuje všechny uživatele repliky služby hostované v tomto procesu. |RestartDeployedCodePackageAsync |Restartování ServiceFabricDeployedCodePackage |Vynuceném |
| RestartNode |Selhání uzlu clusteru Service Fabric simuluje restartováním uzlu. |RestartNodeAsync |Restartování ServiceFabricNode |Vynuceném |
| RestartPartition |Simuluje scénáři přerušení spojení přerušení spojení nebo cluster datacentra restartováním některé nebo všechny repliky oddílu. |RestartPartitionAsync |Restart-ServiceFabricPartition |Řádné |
| RestartReplica |Repliky selhání simuluje restartováním trvalou repliky v clusteru, zavření repliky a pak ho znovu. |RestartReplicaAsync |Restartování ServiceFabricReplica |Řádné |
| Počáteční_uzel |Spustí uzlu v clusteru, který je již zastaveno. |StartNodeAsync |Start-ServiceFabricNode |Neuvedeno |
| Příkaz StopNode |Selhání uzlu simuluje podle zastavení uzlu v clusteru. Uzel zůstanou dolů, dokud se nazývá Počáteční_uzel. |StopNodeAsync |Stop-ServiceFabricNode |Vynuceném |
| ValidateApplication |Ověří dostupnost a stav všech služeb Service Fabric v rámci aplikace, obvykle po vyvolat některé chyby do systému. |ValidateApplicationAsync |Test ServiceFabricApplication |Neuvedeno |
| ValidateService |Ověří dostupnost a stav služby Service Fabric, obvykle po vyvolat některé chyby do systému. |ValidateServiceAsync |Test ServiceFabricService |Neuvedeno |

## <a name="running-a-testability-action-using-powershell"></a>Spuštění testovatelnosti akci pomocí prostředí PowerShell
V tomto kurzu se dozvíte, jak spustit akci testovatelnosti pomocí prostředí PowerShell. Se dozvíte, jak ke spouštění testovatelnosti akce na místní cluster (jedna box) nebo Azure clusteru. Microsoft.Fabric.Powershell.dll – modul Service Fabric prostředí PowerShell – je automaticky nainstaluje při instalaci Microsoft Service Fabric MSI. Modul je načten automaticky při otevření příkazovém řádku prostředí PowerShell.

Kurz segmenty:

* [Spouštění akce clusteru s podporou jeden pole](#run-an-action-against-a-one-box-cluster)
* [Spouštění akce clusteru služby Azure](#run-an-action-against-an-azure-cluster)

### <a name="run-an-action-against-a-one-box-cluster"></a>Spouštění akce clusteru s podporou jeden pole
Ke spouštění testovatelnosti akce na místní cluster, nejdřív připojit ke clusteru a otevřete příkazovém řádku prostředí PowerShell v režimu správce. Dejte nám podívat **restartování ServiceFabricNode** akce.

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

Zde akce **restartování ServiceFabricNode** je spuštěn na uzlu s názvem "Uzel1". Režim dokončení Určuje, že by neměl ověřte, zda akce restartování uzlu ve skutečnosti úspěšně. Režim dokončení, jak je "Ověřit" způsobí, že chcete ověřit, jestli akce restartování ve skutečnosti úspěšně. Místo zadání přímo uzlu jeho název, můžete je zadat prostřednictvím klíč oddílu a druh repliky, následujícím způsobem:

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**Restartování ServiceFabricNode** se má použít k restartování Service Fabric uzlu v clusteru. To se zastaví Fabric.exe procesu, který restartuje všechny systému služby a uživatele služby repliky hostované v tomto uzlu. Použití toto rozhraní API k testování služby pomáhá odkrýt chyby podél cesty převzetí služeb při selhání obnovení. Pomáhá simulace selhání uzlu v clusteru.

Následující snímek obrazovky ukazuje **restartování ServiceFabricNode** testovatelnosti příkaz v akci.

![](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

Výstup první **Get-ServiceFabricNode** (rutiny z modulu Service Fabric prostředí PowerShell) zobrazuje, že místní cluster s pěti uzly: Node.1 k Node.5. Po akci testovatelnosti (rutiny) **restartování ServiceFabricNode** se spustí na uzlu s názvem Node.4, vidíme, že provozu uzlu byl obnoven.

### <a name="run-an-action-against-an-azure-cluster"></a>Spouštění akce clusteru služby Azure
Spuštění akci testovatelnosti (pomocí prostředí PowerShell) proti clusteru služby Azure je podobná spuštění akce proti místní cluster. Jediným rozdílem je, před spuštěním akce, místo připojování k místnímu clusteru, je třeba nejprve připojit ke clusteru Azure.

## <a name="running-a-testability-action-using-c35"></a>Spuštění testovatelnosti akci pomocí jazyka C&#35;
Pokud chcete spustit testovatelnosti akce pomocí jazyka C#, musíte se připojit ke clusteru pomocí FabricClient. Potom získejte parametry potřebné ke spuštění akce. Různé parametry slouží ke spuštění stejné akce.
Prohlížení akce RestartServiceFabricNode, jeden způsob, jak spouštět je pomocí uzlu informace (název uzlu a ID instance uzlu) v clusteru.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

Vysvětlení parametr:

* **CompleteMode** Určuje, že režim nesmí ověřit, zda akce restartování ve skutečnosti úspěšně. Režim dokončení, jak je "Ověřit" způsobí, že chcete ověřit, jestli akce restartování ve skutečnosti úspěšně.  
* **OperationTimeout** Nastaví množství času pro operace dokončeno předtím, než je vyvolána výjimka TimeoutException.
* **CancellationToken** umožňuje čekající volání ke zrušení.

Místo zadání přímo uzlu jeho název, můžete je zadat prostřednictvím klíč oddílu a druh repliky.

Další informace najdete v tématu [partitionselector nejde a replicaselector nejde](#partition_replica_selector).

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

## <a name="partitionselector-and-replicaselector"></a>Partitionselector nejde a replicaselector nejde
### <a name="partitionselector"></a>Partitionselector nejde
Partitionselector nejde je pomocné rutiny v testovatelnosti a slouží k výběru konkrétního oddílu, na které se mají provádět operace testovatelnosti. Slouží k výběru na konkrétní oddíl, pokud je předem znám ID oddílu. Nebo můžete zadat klíč oddílu a operaci interně vyřešit ID oddílu. Máte také možnost výběru náhodného oddílu.

Pokud chcete použít tohoto pomocníka, vytvořit objekt partitionselector nejde a vyberte oddíl, a to pomocí jedné z metod vyberte *. Pak předejte v objektu partitionselector nejde do rozhraní API, kterého se vyžaduje. Pokud je vybraná žádná možnost, bude výchozí náhodných oddílu.

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

### <a name="replicaselector"></a>Replicaselector nejde
Replicaselector nejde je pomocné rutiny v testovatelnosti a slouží k zajištění vyberte repliku, na které se mají provádět operace testovatelnosti. Může sloužit k vyberte konkrétní repliku, pokud je ID repliky předem znám. Kromě toho máte možnost výběru primární repliku nebo náhodné sekundární lokality. Replicaselector nejde je odvozena z partitionselector nejde, takže je nutné vybrat repliky a oddílu, na kterém chcete provést operaci testovatelnosti.

Chcete-li použít tohoto pomocníka, vytvořit objekt replicaselector nejde a nastavte způsob, jakým chcete vybrat repliky a oddíl. Pak předejte ji do rozhraní API, kterého se vyžaduje. Pokud je vybraná žádná možnost, bude výchozí náhodných repliky a náhodných oddílu.

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

## <a name="next-steps"></a>Další postup
* [Testovatelnosti scénáře](service-fabric-testability-scenarios.md)
* Postup testování služby
  * [Simulace selhání během úloh služeb](service-fabric-testability-workload-tests.md)
  * [Selhání komunikace Service-to-service](service-fabric-testability-scenarios-service-communication.md)

