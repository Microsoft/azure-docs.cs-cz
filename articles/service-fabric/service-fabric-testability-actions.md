---
title: Simulace chyb v mikroslužeb Azure | Dokumentace Microsoftu
description: Tento článek pojednává o akcemi testovatelnosti v Microsoft Azure Service Fabric.
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
ms.openlocfilehash: 70ed1561af6dc06b4d1db89e6449540dd76b67be
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55815877"
---
# <a name="testability-actions"></a>Akce testovatelnosti
K simulaci nespolehlivé infrastruktury Azure Service Fabric nabízí vás jako na vývojáři se způsoby, jak simulovat různých skutečná selhání a přechodů mezi stavy. Tyto jsou vystaveny jako akcemi testovatelnosti. Akce, které jsou nízké úrovně rozhraní API, která způsobí vkládání konkrétních chyb, přechod stavu nebo ověření. Díky kombinaci těchto akcí, můžete napsat komplexní testovací scénáře pro služby.

Service Fabric poskytuje že několik běžných scénářů testovací skládá z těchto akcí. Důrazně doporučujeme využívat tyto předdefinované scénáře, které jsou pečlivě zvolili k testování běžné přechodů mezi stavy a případy selhání. Akce je však možné vytvářet vlastní testovací scénáře, když chcete přidat pokrytí pro scénáře, které nejsou pokryty všemi integrované scénáře ještě nebo které jsou vlastní přizpůsobená pro vaši aplikaci.

C#implementace akce, které se nacházejí v sestavení System.Fabric.dll. Modul prostředí PowerShell systému prostředků infrastruktury se nachází ve Microsoft.ServiceFabric.Powershell.dll sestavení. Jako součást instalace modulu runtime je nainstalován modul ServiceFabric PowerShell umožňující snadné použití.

## <a name="graceful-vs-ungraceful-fault-actions"></a>Bezproblémové vs. vynuceném selhání akce
Akcemi testovatelnosti dělí do dvou hlavních bloků:

* Vynuceném chyb: Tyto chyby simulace selhání, jako je restartování počítače a zpracování chyb. V takových případech selhání zastaví náhle kontextu spuštění procesu. To znamená, že se že žádné čištění stavu můžete spustit před spuštěním aplikace znovu.
* Bezproblémové chyb: Tyto chyby simulovat řádné akce, jako je replika přesunutí a drops aktivované služby Vyrovnávání zatížení. V takových případech služba obdržela oznámení okno zavřít a můžete vyčistit stavu před ukončením.

Pro lepší ověření kvality spuštění úlohy služby a obchodní při různých chyb bezproblémové a vynuceném nevyvolá. Vynuceném chyb výkonu scénáře, kde služba náhlé ukončení procesu uprostřed některé pracovního postupu. Tato funkce testuje cestu obnovení po obnovení repliky služby Service Fabric. To vám pomůže otestovat konzistenci dat a určuje, zda je stav služby udržuje správně po selhání. Další sadu test selhání (řádné selhání), který služba správně reaguje na repliky se přesouvat Service Fabric. To testy zpracování zrušení v metodě RunAsync. Služba potřebuje ke kontrole token rušení, který je nastaven, správně uložit svůj stav a ukončete metodě RunAsync.

## <a name="testability-actions-list"></a>Seznam akcí testovatelnosti
| Akce | Popis | Spravované rozhraní API | Rutiny Powershellu | Bezproblémové/vynuceném chyb |
| --- | --- | --- | --- | --- |
| CleanTestState |Odebere všechny stav testu z clusteru v případě chybný vypnutí ovladače testu. |CleanTestStateAsync |Remove-ServiceFabricTestState |Neuvedeno |
| InvokeDataLoss |Indukuje ztrátě dat do oddílu služby. |InvokeDataLossAsync |Invoke-ServiceFabricPartitionDataLoss |Bezproblémové |
| InvokeQuorumLoss |Oddíl dané stavové služby umístí do ztrátě kvora. |InvokeQuorumLossAsync |Vyvolání ServiceFabricQuorumLoss |Bezproblémové |
| MovePrimary |Zadaný primární repliky stavové služby přesune do určeného uzlu clusteru. |MovePrimaryAsync |Move-ServiceFabricPrimaryReplica |Bezproblémové |
| MoveSecondary |Posune aktuální sekundární repliky do stavové služby do jiného uzlu clusteru. |MoveSecondaryAsync |Move-ServiceFabricSecondaryReplica |Bezproblémové |
| RemoveReplica |Simuluje selhání repliky tak, že odeberete repliku z clusteru. To se zavře repliky a přejde k roli 'None', veškerý její stav odebrání z clusteru. |RemoveReplicaAsync |Remove-ServiceFabricReplica |Bezproblémové |
| RestartDeployedCodePackage |Selhání kódu balíček proces simuluje restartováním balíček kódu nasazené na uzly v clusteru. To přeruší proces balíček kódu, který restartuje všechny uživatele repliky služby hostované v tomto procesu. |RestartDeployedCodePackageAsync |Restart-ServiceFabricDeployedCodePackage |Vynuceném |
| RestartNode |Po restartování uzlu simuluje selhání uzlu clusteru Service Fabric. |RestartNodeAsync |Restart-ServiceFabricNode |Vynuceném |
| RestartPartition |Simuluje scénáři nedostupnosti nedostupnosti nebo cluster datacentra restartováním některé nebo všechny repliky oddílu. |RestartPartitionAsync |Restart-ServiceFabricPartition |Bezproblémové |
| RestartReplica |Simuluje selhání replik restartováním trvalý repliky v clusteru, zavírání repliky a znovu ji otevřete. |RestartReplicaAsync |Restart-ServiceFabricReplica |Bezproblémové |
| StartNode |Spustí uzlu v clusteru, který je již zastavena. |StartNodeAsync |Start-ServiceFabricNode |Neuvedeno |
| StopNode |Simuluje selhání uzlu pomocí zastavení uzlu v clusteru. Uzel zůstane dolů, dokud se nazývá StartNode. |StopNodeAsync |Stop-ServiceFabricNode |Vynuceném |
| ValidateApplication |Ověří dostupnost a stav všech služeb Service Fabric v rámci aplikace, obvykle po nevyvolá některých chyb do systému. |ValidateApplicationAsync |Test-ServiceFabricApplication |Neuvedeno |
| ValidateService |Ověří dostupnost a stav služby Service Fabric, obvykle po nevyvolá některých chyb do systému. |ValidateServiceAsync |Test-ServiceFabricService |Neuvedeno |

## <a name="running-a-testability-action-using-powershell"></a>Spouštění akce testovatelnosti pomocí Powershellu
V tomto kurzu se dozvíte, jak provádět akce testovatelnosti pomocí prostředí PowerShell. Bude se dozvíte, jak ke spuštění akcí testovatelnosti místního clusteru (jedna box) nebo Azure. Microsoft.Fabric.Powershell.dll – modul Powershellu pro Service Fabric – je automaticky nainstalován při instalaci Microsoft Service Fabric MSI. Modul je načten automaticky, když otevřete příkazový řádek Powershellu.

Kurz segmenty:

* [Spustit akci pole jeden cluster](#run-an-action-against-a-one-box-cluster)
* [Spustit akci proti clusteru služby Azure](#run-an-action-against-an-azure-cluster)

### <a name="run-an-action-against-a-one-box-cluster"></a>Spustit akci pole jeden cluster
Spustit akce testovatelnosti místní cluster, nejprve připojte ke clusteru a otevřete příkazový řádek Powershellu v režimu správce. Podívejme se na **Restart-ServiceFabricNode** akce.

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

Tady akce **Restart-ServiceFabricNode** běží na uzlu s názvem "Node1". Režim dokončování Určuje, že by neměla ověřit, jestli akce restartování uzlu ve skutečnosti úspěšně. Určuje režim dokončování, jak je "Ověřit" způsobí, že chcete ověřit, jestli akce restartování ve skutečnosti úspěšně. Místo určení přímo uzlu pomocí jeho názvu, můžete je zadat pomocí klíče oddílu a druh repliky, následujícím způsobem:

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**Restart-ServiceFabricNode** by měla sloužit k restartování uzlu Service Fabric v clusteru. Tento kód přestane Fabric.exe proces, který restartuje všechny systémové služby a uživatel služby repliky hostované v tomto uzlu. Pomocí tohoto rozhraní API k otestování služby pomáhá získat chyb podél cest obnovení převzetí služeb při selhání. Umožňuje simulovat selhání uzlů v clusteru.

Následující snímek obrazovky ukazuje **Restart-ServiceFabricNode** příkaz testovatelnosti v akci.

![](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

Výstup prvního **Get-ServiceFabricNode** (rutiny z modulu Powershellu pro Service Fabric) ukazuje, že místní cluster obsahuje pět uzlů: Node.1 k Node.5. Po akce testovatelnosti (rutiny) **Restart-ServiceFabricNode** je spuštěn na uzlu s názvem Node.4, vidíme, že uzel dostupnost se resetovalo.

### <a name="run-an-action-against-an-azure-cluster"></a>Spustit akci proti clusteru služby Azure
Spuštění akcí testovatelnosti (s použitím prostředí PowerShell) proti clusteru služby Azure se podobá spouštění akce v místním clusteru. Jediným rozdílem je, abyste mohli spustit akci místo připojování k místnímu clusteru budete muset nejprve připojte ke clusteru Azure.

## <a name="running-a-testability-action-using-c35"></a>Spouštění akce testovatelnosti pomocí jazyka C&#35;
Spuštění akcí testovatelnosti pomocí C#, je třeba nejprve připojte ke clusteru pomocí FabricClient. Potom získejte parametry potřebné ke spuštění akce. Různé parametry lze použít ke spuštění stejnou akci.
Akce RestartServiceFabricNode podíváme, můžete ho spustit také tak je pomocí uzlu informace (název uzlu a uzel ID instance) v clusteru.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

Vysvětlení parametrů:

* **CompleteMode** Určuje, že by neměla režim ověřit, jestli akce restartování ve skutečnosti úspěšně. Určuje režim dokončování, jak je "Ověřit" způsobí, že chcete ověřit, jestli akce restartování ve skutečnosti úspěšně.  
* **OperationTimeout** Nastaví množství času pro operaci dokončit předtím, než je vyvolána výjimka TimeoutException.
* **CancellationToken** umožňuje čekající volání budou zrušeny.

Místo určení přímo uzlu pomocí jeho názvu, můžete je zadat pomocí klíče oddílu a druh repliky.

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
PartitionSelector je pomocná v testovatelnost slouží k výběru konkrétního oddílu, na kterém chcete provést některé z akcí testovatelnosti Slouží k výběru konkrétního oddílu, pokud je ID oddílu je znám předem. Nebo můžete zadat klíč oddílu a operace se přeložit ID oddílu interně. Máte také možnost výběru náhodného oddílu.

Použití této pomocné rutiny, vytvořit objekt PartitionSelector a vyberte oddíl, pomocí jedné z metod vyberte *. Pak předá objekt PartitionSelector rozhraní API, který jej vyžaduje. Pokud není zaškrtnuto, bude výchozí náhodné oddílu.

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
ReplicaSelector je pomocné rutiny v testovatelnost slouží k zajištění vyberte repliku, na kterém chcete provést některé z akcí testovatelnosti Je možné vybrat konkrétní repliky, pokud je ID repliky je znám předem. Kromě toho máte možnost vybrat primární repliku nebo náhodného sekundární lokality. ReplicaSelector je odvozena z PartitionSelector, proto je nutné vybrat repliky a oddílu, na kterém chcete provést operaci testovatelnost.

Použití této pomocné rutiny, vytvořte objekt ReplicaSelector a nastavit tak, jak chcete vybrat repliky a oddílu. Poté jej lze předat do rozhraní API, který jej vyžaduje. Pokud není zaškrtnuto, bude výchozí náhodné repliky a náhodné oddílu.

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
* [Scénářů testovatelnosti](service-fabric-testability-scenarios.md)
* Postup testování služby
  * [Simulace chyb během zatížení služeb](service-fabric-testability-workload-tests.md)
  * [Selhání komunikace Service to service](service-fabric-testability-scenarios-service-communication.md)

