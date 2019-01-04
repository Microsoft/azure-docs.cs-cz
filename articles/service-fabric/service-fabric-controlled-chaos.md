---
title: Zahájit Chaos na clusterech Service Fabric | Dokumentace Microsoftu
description: Pomocí vkládání chyb a rozhraní API služeb analýzy clusteru pro správu Chaos v clusteru.
services: service-fabric
documentationcenter: .net
author: motanv
manager: anmola
editor: motanv
ms.assetid: 2bd13443-3478-4382-9a5a-1f6c6b32bfc9
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/05/2018
ms.author: motanv
ms.openlocfilehash: a1b334b34e8e234d9ce5cc5ad5cd77bf5ba7118c
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53555519"
---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Zahájit řízeného chaosu na clusterech Service Fabric
Ve velkém měřítku distribuovaných systémů, jako jsou ze své podstaty nespolehlivé cloudových infrastruktur. Azure Service Fabric umožňuje vývojářům psát reliable services v distribuované nespolehlivé infrastrukturu. Zápis robustní distribuované služby nespolehlivé infrastrukturu, vývojáři potřeba mít možnost Testovat stabilitu svoje služby během základní nespolehlivé infrastruktury prochází složité přechodů mezi stavy z důvodu chyby.

[Vkládání chyb a Clusterová služba analýzy](https://docs.microsoft.com/azure/service-fabric/service-fabric-testability-overview) (označované také jako Fault Analysis Service) poskytuje vývojářům možnost vyvolat chyby k otestování svých služeb. Tyto cílové simulované chyby, jako je třeba [restartování oddílu](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricpartitionrestart?view=azureservicefabricps), může pomoct vykonávat nejběžnější přechodů. Ale cílové simulované chyby jsou tendenční podle definice a proto může přijít o chyby, které zobrazují se pouze v pevném předpovědět, dlouhé a komplikované posloupnost přechodů mezi stavy. Neposunutého testování, můžete použít Chaos.

Chaos simuluje pravidelné, prokládané chyby (řádné i vynuceném) v rámci clusteru za dlouhou dobu. Bezproblémové selhání se skládá ze sady volání rozhraní API Service Fabric, například selhání replik restartování je bezproblémové selhání, protože to je zavřít následovaný otevřenou v replice. Odebrat repliky, přesunutí primární replika a sekundární replika přesunutí se řádné chyby vykonány podle Chaos. Vynuceném chyby se proces ukončí, jako je restartování uzlu a restartujte balíček kódu. 

Po nakonfigurování Chaos rychlost a typ chyby, můžete začít Chaos pomocí jazyka C#, Powershellu nebo rozhraní REST API pro spuštění v clusteru a ve vašich službách generování chyb. Chaos ke spuštění za zadané časové období (třeba hodinu), po které zastaví Chaos můžete nakonfigurovat automaticky, nebo můžete volat rozhraní API StopChaos (C#, Powershellu a REST) kdykoli zastavit.

> [!NOTE]
> V současné podobě Chaos indukuje pouze bezpečné chyb, což znamená, že chybí externí chyb ztráty kvora, nebo ztrátě dat se nikdy neprovádí.
>

Je spuštěn chaosu, vyvolá různé události, které zachycují stav spuštění v tuto chvíli. Například ExecutingFaultsEvent obsahuje všechny chyby, které Chaos se rozhodl ke spuštění v dané iterace. ValidationFailedEvent obsahuje podrobnosti o selhání ověření (stavu nebo stabilitu problémy), který byl nalezen během ověření clusteru. Můžete vyvolat rozhraní API GetChaosReport (C#, Powershellu nebo REST) Chcete-li získat sestavu spuštění Chaos. Tyto události získat uchovávaných v [spolehlivého slovníku](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-reliable-collections), která má zásady zkrácení závisí dvě konfigurace: **MaxStoredChaosEventCount** (výchozí hodnota je 25 000) a **StoredActionCleanupIntervalInSeconds** (výchozí hodnota je 3600). Každý *StoredActionCleanupIntervalInSeconds* kontroly Chaos a všechny, ale nejnovější *MaxStoredChaosEventCount* události, jsou vymazány z spolehlivého slovníku.

## <a name="faults-induced-in-chaos"></a>Chyby vyvolané v chaosu
Chaos generuje chyby napříč celý cluster Service Fabric a komprimuje chyb, které se nacházejí v měsíců nebo let do několika hodin. Kombinace prokládané chyb s vysokou odolnost rychlost vyhledá krajní případy, které může jinak mohla vynechat. Toto cvičení Chaos vede ke značným vylepšením kvality kódu služby.

Chaos indukuje chyby z následujících kategorií:

* Restartovat uzel
* Restartujte balíček nasazenému kódu
* Odstranění repliky
* Restartujte na repliku
* Přesunutí primární repliky (Konfigurovat)
* Přesunout na sekundární repliku (Konfigurovat)

Chaos běží v několika iteracích. Každé iterace se skládá z chyb a ověření clusteru zadanou dobu. Můžete nakonfigurovat čas strávený clusteru stabilizovat a pro ověření úspěšné. Pokud k selhání se nachází v ověření clusteru, Chaos generuje a trvá ValidationFailedEvent časové razítko UTC a podrobnosti o chybě. Představte si třeba nějaká instance chaosu, který je nastaven na spouštění hodinu s délkou maximálně tři souběžných chyb. Chaos indukuje tři chyby a pak ověřuje stav clusteru. Iteruje přes v předchozím kroku, dokud není výslovně zastavila prostřednictvím rozhraní API StopChaosAsync nebo hodinová předá. Pokud nebude v pořádku v kteroukoli iteraci, cluster (to znamená, nejsou stabilizaci nebo nebudou v rámci MaxClusterStabilizationTimeout předané v pořádku), vygeneruje ValidationFailedEvent Chaos. Tato událost ukazuje na to, že něco se pokazilo a může být nutné další šetření.

Získat kterých chyby vyvolané chaosu, můžete použít API GetChaosReport (prostředí powershell, C# nebo REST). Rozhraní API získá další segment Chaos sestavy na základě předané pokračovací token nebo předaným čas rozsahu. Můžete zadat ContinuationToken zobrazíte další segment Chaos sestavy nebo můžete zadat časový rozsah prostřednictvím StartTimeUtc a EndTimeUtc, ale nemůže určit token ContinuationToken a časový rozsah ve stejném volání. Po více než 100 události Chaos se sestava Chaos se vrátí v příslušných segmentech, kde segment, který obsahuje více než 100 Chaos události.

## <a name="important-configuration-options"></a>Důležité konfigurační možnosti
* **TimeToRun**: Celková doba spuštění Chaos dříve, než se dokončí s úspěchem. Předtím, než bylo spuštěno dobu TimeToRun prostřednictvím rozhraní API StopChaos můžete zastavit Chaos.

* **MaxClusterStabilizationTimeout**: Maximální dobu čekání před vytváření ValidationFailedEvent se obnoví dobrý stav clusteru. Toto je ke snížení zatížení v clusteru, zatímco se obnovuje. Provádí kontroly jsou:
  * Pokud stav clusteru je v pořádku
  * Pokud je služba stavu v pořádku.
  * Pokud velikost sady replik cíl je dosaženo oddílu služby
  * Že neexistují žádné replik InBuild
* **MaxConcurrentFaults**: Maximální počet souběžných chyb, které jsou vyvolaných v každé iteraci. Převzetí služeb při selhání a přechod kombinacemi stavů, které prochází clusteru jsou také mnohem složitější, protože je vyšší číslo, tím více agresivní Chaos. 

> [!NOTE]
> Bez ohledu na to jak velkou hodnotu *MaxConcurrentFaults* má Chaos zaručuje – chybí externí chyb - neexistuje žádné ztrátě kvora nebo ztrátě dat.
>

* **EnableMoveReplicaFaults**: Povolí nebo zakáže chyb, které způsobují primární nebo sekundární repliky přesunout. Tyto chyby jsou ve výchozím nastavení povolené.
* **WaitTimeBetweenIterations**: Množství času čekat mezi iteracemi. To znamená množství času Chaos se pozastaví po provedení kruhové chyby a s dokončení odpovídající ověření stavu clusteru. Čím vyšší hodnota, nižší je míra vkládání průměrné selhání.
* **WaitTimeBetweenFaults**: Množství času mezi dvěma po sobě jdoucích chyb v jedné iterace. Čím vyšší hodnota, nižší současnému (nebo překrývají) chyb.
* **ClusterHealthPolicy**: Zásady stavu clusteru slouží k ověření stavu clusteru mezi iteracemi Chaos. Pokud stav clusteru došlo k chybě nebo pokud dojde k neočekávané výjimce během selhání spuštění, Chaos bude čekat před další-kontrolou stavu - poskytnout clusteru nějakou dobu recuperate 30 minut.
* **Kontext**: Kolekce (string, string) zadejte páry klíč hodnota. Na mapě slouží k zaznamenání informací o spuštění Chaos. Nemůže existovat více než 100 takových dvojic a každý řetězec (klíče nebo hodnoty) může obsahovat nejvýše 4095 znaků. Toto mapování se nastavil starter tento Chaos se dá spustit volitelně ukládat kontextu o konkrétního spuštění.
* **ChaosTargetFilter**: Tento filtr je možné na cílovém Chaos chyby pouze pro určité typy uzlů nebo jenom některých instancí aplikace. Pokud se nepoužívá ChaosTargetFilter, Chaos chyb všechny entity v clusteru. Pokud použijete ChaosTargetFilter Chaos chyb pouze entity, které splňují ChaosTargetFilter specifikace. Seznamy NodeTypeInclusionList a ApplicationInclusionList povolit pouze sjednocení sémantiku. Jinými slovy není možné určit průsečíkem seznamy NodeTypeInclusionList a ApplicationInclusionList. Například není možné určit "selhání tuto aplikaci, pouze pokud je na daný typ uzlu." Jakmile entity je součástí seznamy NodeTypeInclusionList a ApplicationInclusionList, nelze entity vyloučit pomocí ChaosTargetFilter. I v případě, že se nezobrazují applicationX v ApplicationInclusionList v iteraci některé Chaos applicationX může být došlo k chybě vzhledem k tomu, že se stane se v uzlu nodeTypeY, který je součástí seznamy NodeTypeInclusionList. Pokud seznamy NodeTypeInclusionList a ApplicationInclusionList jsou null nebo prázdný, je vyvolána ArgumentException.
    * **Seznamy NodeTypeInclusionList**: Seznam typů uzlů chcete zahrnout chyby Chaos. Všechny typy chyb (restartovat uzel, restartujte codepackage, repliku odstranit, restartovat repliky, přesunutí primární a sekundární) jsou povolené pro uzly z těchto typů uzlů. Pokud nodetype (Řekněme, že NodeTypeX) se nezobrazují v seznamy NodeTypeInclusionList, pak pro uzly NodeTypeX se nikdy povolí úrovně chyby uzlu (například NodeRestart), ale balíček a repliky chyb kódu je možné povolit pro NodeTypeX pořád, pokud aplikace v Jsou umístěny v uzlu NodeTypeX ApplicationInclusionList se stane. Maximálně 100 názvy typů uzlu mohou být součástí tohoto seznamu, tento počet zvýšit, je nutné konfiguraci MaxNumberOfNodeTypesInChaosTargetFilter upgradovat konfiguraci.
    * **ApplicationInclusionList**: Seznam identifikátorů URI aplikace chcete zahrnout chyby Chaos. Všechny repliky patří do těchto aplikací služeb jsou vydávání kompaktních repliky chyb (restartování repliky, odebrat repliky, přesunutí primárního a sekundárního přesunutí) Chaos. Pouze v případě, že balíček kódu je hostitelem repliky jenom tyto aplikace se může restartovat Chaos balíček kódu. Pokud aplikace v tomto seznamu nezobrazí, se můžete stále být došlo k chybě v iteraci některé Chaos Pokud ukončení aplikace na uzlu typu uzlu, který je součástí seznamy NodeTypeInclusionList. Nicméně pokud applicationX se váže na nodeTypeY prostřednictvím omezení umístění a applicationX chybí z ApplicationInclusionList a nodeTypeY chybí z seznamy NodeTypeInclusionList, pak applicationX nikdy selže. Maximálně 1000 názvy aplikací mohou být součástí tohoto seznamu, tento počet zvýšit, je nutné konfiguraci MaxNumberOfApplicationsInChaosTargetFilter upgradovat konfiguraci.

## <a name="how-to-run-chaos"></a>Jak spustit chaosu

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Fabric;

using System.Diagnostics;
using System.Fabric.Chaos.DataStructures;

class Program
{
    private class ChaosEventComparer : IEqualityComparer<ChaosEvent>
    {
        public bool Equals(ChaosEvent x, ChaosEvent y)
        {
            return x.TimeStampUtc.Equals(y.TimeStampUtc);
        }
        public int GetHashCode(ChaosEvent obj)
        {
            return obj.TimeStampUtc.GetHashCode();
        }
    }

    static void Main(string[] args)
    {
        var clusterConnectionString = "localhost:19000";
        using (var client = new FabricClient(clusterConnectionString))
        {
            var startTimeUtc = DateTime.UtcNow;

            // The maximum amount of time to wait for all cluster entities to become stable and healthy. 
            // Chaos executes in iterations and at the start of each iteration it validates the health of cluster entities. 
            // During validation if a cluster entity is not stable and healthy within MaxClusterStabilizationTimeoutInSeconds, Chaos generates a validation failed event.
            var maxClusterStabilizationTimeout = TimeSpan.FromSeconds(30.0);

            var timeToRun = TimeSpan.FromMinutes(60.0);

            // MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration. 
            // Chaos executes in iterations and two consecutive iterations are separated by a validation phase. 
            // The higher the concurrency, the more aggressive the injection of faults -- inducing more complex series of states to uncover bugs. 
            // The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
            var maxConcurrentFaults = 3;

            // Describes a map, which is a collection of (string, string) type key-value pairs. The map can be used to record information about
            // the Chaos run. There cannot be more than 100 such pairs and each string (key or value) can be at most 4095 characters long.
            // This map is set by the starter of the Chaos run to optionally store the context about the specific run.
            var startContext = new Dictionary<string, string>{{"ReasonForStart", "Testing"}};

            // Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the lower the fault injection rate.
            var waitTimeBetweenIterations = TimeSpan.FromSeconds(10);

            // Wait time (in seconds) between consecutive faults within a single iteration. 
            // The larger the value, the lower the overlapping between faults and the simpler the sequence of state transitions that the cluster goes through. 
            // The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
            var waitTimeBetweenFaults = TimeSpan.Zero;

            // Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
            var clusterHealthPolicy = new ClusterHealthPolicy
            {
                ConsiderWarningAsError = false,
                MaxPercentUnhealthyApplications = 100,
                MaxPercentUnhealthyNodes = 100
            };

            // All types of faults, restart node, restart code package, restart replica, move primary replica, and move secondary replica will happen
            // for nodes of type 'FrontEndType'
            var nodetypeInclusionList = new List<string> { "FrontEndType"};

            // In addition to the faults included by nodetypeInclusionList, 
            // restart code package, restart replica, move primary replica, move secondary replica faults will happen for 'fabric:/TestApp2'
            // even if a replica or code package from 'fabric:/TestApp2' is residing on a node which is not of type included in nodeypeInclusionList.
            var applicationInclusionList = new List<string> { "fabric:/TestApp2" };

            // List of cluster entities to target for Chaos faults.
            var chaosTargetFilter = new ChaosTargetFilter
            {
                NodeTypeInclusionList = nodetypeInclusionList,
                ApplicationInclusionList = applicationInclusionList
            };

            var parameters = new ChaosParameters(
                maxClusterStabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun,
                startContext,
                waitTimeBetweenIterations,
                waitTimeBetweenFaults,
                clusterHealthPolicy) {ChaosTargetFilter = chaosTargetFilter};

            try
            {
                client.TestManager.StartChaosAsync(parameters).GetAwaiter().GetResult();
            }
            catch (FabricChaosAlreadyRunningException)
            {
                Console.WriteLine("An instance of Chaos is already running in the cluster.");
            }

            var filter = new ChaosReportFilter(startTimeUtc, DateTime.MaxValue);

            var eventSet = new HashSet<ChaosEvent>(new ChaosEventComparer());

            string continuationToken = null;

            while (true)
            {
                ChaosReport report;
                try
                {
                    report = string.IsNullOrEmpty(continuationToken)
                        ? client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult()
                        : client.TestManager.GetChaosReportAsync(continuationToken).GetAwaiter().GetResult();
                }
                catch (Exception e)
                {
                    if (e is FabricTransientException)
                    {
                        Console.WriteLine("A transient exception happened: '{0}'", e);
                    }
                    else if(e is TimeoutException)
                    {
                        Console.WriteLine("A timeout exception happened: '{0}'", e);
                    }
                    else
                    {
                        throw;
                    }

                    Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
                    continue;
                }

                continuationToken = report.ContinuationToken;

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.Add(chaosEvent))
                    {
                        Console.WriteLine(chaosEvent);
                    }
                }

                // When Chaos stops, a StoppedEvent is created.
                // If a StoppedEvent is found, exit the loop.
                var lastEvent = report.History.LastOrDefault();

                if (lastEvent is StoppedEvent)
                {
                    break;
                }

                Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
            }
        }
    }
}
```

```powershell
$clusterConnectionString = "localhost:19000"
$timeToRunMinute = 60

# The maximum amount of time to wait for all cluster entities to become stable and healthy. 
# Chaos executes in iterations and at the start of each iteration it validates the health of cluster entities. 
# During validation if a cluster entity is not stable and healthy within MaxClusterStabilizationTimeoutInSeconds, Chaos generates a validation failed event.
$maxClusterStabilizationTimeSecs = 30

# MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration. 
# Chaos executes in iterations and two consecutive iterations are separated by a validation phase. 
# The higher the concurrency, the more aggressive the injection of faults -- inducing more complex series of states to uncover bugs. 
# The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
$maxConcurrentFaults = 3

# Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the lower the fault injection rate.
$waitTimeBetweenIterationsSec = 10

# Wait time (in seconds) between consecutive faults within a single iteration. 
# The larger the value, the lower the overlapping between faults and the simpler the sequence of state transitions that the cluster goes through. 
# The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
$waitTimeBetweenFaultsSec = 0

# Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
$clusterHealthPolicy = new-object -TypeName System.Fabric.Health.ClusterHealthPolicy
$clusterHealthPolicy.MaxPercentUnhealthyNodes = 100
$clusterHealthPolicy.MaxPercentUnhealthyApplications = 100
$clusterHealthPolicy.ConsiderWarningAsError = $False

# Describes a map, which is a collection of (string, string) type key-value pairs. The map can be used to record information about
# the Chaos run. There cannot be more than 100 such pairs and each string (key or value) can be at most 4095 characters long.
# This map is set by the starter of the Chaos run to optionally store the context about the specific run.
$context = @{"ReasonForStart" = "Testing"}

#List of cluster entities to target for Chaos faults.
$chaosTargetFilter = new-object -TypeName System.Fabric.Chaos.DataStructures.ChaosTargetFilter
$chaosTargetFilter.NodeTypeInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# All types of faults, restart node, restart code package, restart replica, move primary replica, and move secondary replica will happen
# for nodes of type 'FrontEndType'
$chaosTargetFilter.NodeTypeInclusionList.AddRange( [string[]]@("FrontEndType") )
$chaosTargetFilter.ApplicationInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# In addition to the faults included by nodetypeInclusionList, 
# restart code package, restart replica, move primary replica, move secondary replica faults will happen for 'fabric:/TestApp2'
# even if a replica or code package from 'fabric:/TestApp2' is residing on a node which is not of type included in nodeypeInclusionList.
$chaosTargetFilter.ApplicationInclusionList.Add("fabric:/TestApp2")

Connect-ServiceFabricCluster $clusterConnectionString

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRunMinute -MaxConcurrentFaults $maxConcurrentFaults -MaxClusterStabilizationTimeoutSec $maxClusterStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ClusterHealthPolicy $clusterHealthPolicy -ChaosTargetFilter $chaosTargetFilter

while($true)
{
    $stopped = $false
    $report = Get-ServiceFabricChaosReport -StartTimeUtc $now -EndTimeUtc ([System.DateTime]::MaxValue)

    foreach ($e in $report.History) {

        if(-Not ($events.Contains($e.TimeStampUtc.Ticks)))
        {
            $events.Add($e.TimeStampUtc.Ticks, $e)
            if($e -is [System.Fabric.Chaos.DataStructures.ValidationFailedEvent])
            {
                Write-Host -BackgroundColor White -ForegroundColor Red $e
            }
            else
            {
                Write-Host $e
                # When Chaos stops, a StoppedEvent is created.
                # If a StoppedEvent is found, exit the loop.
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    return
                }
            }
        }
    }

    Start-Sleep -Seconds 1
}
```
