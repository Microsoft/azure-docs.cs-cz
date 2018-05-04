---
title: Vyvolat Chaos v prostředí clusterů Service Fabric | Microsoft Docs
description: Použití clusteru Analysis Service API a pravděpodobnost vkládání ke správě Chaos v clusteru.
services: service-fabric
documentationcenter: .net
author: motanv
manager: anmola
editor: motanv
ms.assetid: 2bd13443-3478-4382-9a5a-1f6c6b32bfc9
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/05/2018
ms.author: motanv
ms.openlocfilehash: 40ceb62e544d2aa71296e24da957cb062029da9f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Vyvolat řízené Chaos v prostředí clusterů Service Fabric
Ve velkém měřítku distribuovaných systémů, jako jsou ze své podstaty nespolehlivé cloudových infrastruktur. Azure Service Fabric umožňuje vývojářům psát spolehlivé distribuované služby nespolehlivé infrastruktuře. Zápis robustní distribuované služby nespolehlivé infrastruktuře, vývojáři potřeba otestovat stability svých služeb, při odpovídající nespolehlivé infrastruktury prochází přes přechodů mezi stavy složité z důvodu chyb.

[Vkládání selhání a clusteru Analysis Service](https://docs.microsoft.com/azure/service-fabric/service-fabric-testability-overview) (také označované jako služba analýza selhání) poskytuje vývojářům možnost způsobit chyby k otestování svých služeb. Tyto cílové simulated chyb, jako je třeba [restartování oddíl](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricpartitionrestart?view=azureservicefabricps), může pomoci prověření nejběžnější přechodů mezi stavy. Ale cílové simulované chyb jsou tendenční podle definice a proto může dojít chyby které zobrazují se pouze v pevném předpovědi, dlouhé a komplikované posloupnost přechodů mezi stavy. Pro neposunutého testování, můžete použít Chaos.

Chaos simuluje pravidelné, prokládaná chyb (řádně i vynuceném) v rámci clusteru přes dlouhou dobu. Řádné chybu obsahuje sadu volání rozhraní API služby infrastruktury, například selhání repliky restartování je řádně chybu, protože to je zavřít, následuje otevřenou v replice. Odebrat repliky, přesunout, že primární replika a sekundární replika přesunutí jsou jiné řádné vykonávají Chaos chyb. Vynuceném chyb se proces ukončí, jako jsou restartovat uzel a restrat pacakge kódu. 

Po konfiguraci Chaos s rychlost a druh chyb, můžete začít Chaos prostřednictvím jazyka C#, prostředí Powershell nebo rozhraní REST API spustit generování chyb v clusteru a vaše služby. Chaos spuštění pro zadané časové období (například pro jednu hodinu), po které zastaví Chaos lze nastavit automaticky nebo můžete volat rozhraní API StopChaos (C#, prostředí Powershell nebo REST) pro zastavení kdykoli.

> [!NOTE]
> V současné podobě Chaos indukuje pouze bezpečné chyb, což znamená, že chybí externí chyb ztrátě kvora nebo ztrátě dat se nikdy neprovádí.
>

Je spuštěn Chaos, vyvolá různé události, které zaznamenat stav spuštění v tuto chvíli. Například ExecutingFaultsEvent obsahuje všechny chyb, které se rozhodla Chaos provést v této iteraci. ValidationFailedEvent obsahuje podrobnosti o selhání ověření (stavu nebo stabilitu problémy), který byl nalezen během ověření clusteru. Rozhraní API GetChaosReport (C#, prostředí Powershell nebo REST) Chcete-li získat sestavu Chaos spustí můžete vyvolat. Tyto události získat uchovávané v [spolehlivé slovník](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-reliable-collections), který má zásady zkrácení závisí na dvě konfigurace: **MaxStoredChaosEventCount** (výchozí hodnota je 25 000) a **StoredActionCleanupIntervalInSeconds** (výchozí hodnota je 3600). Každý *StoredActionCleanupIntervalInSeconds* Chaos kontroly a všechny, ale nejnovější *MaxStoredChaosEventCount* události, jsou vymazány ze slovníku spolehlivé.

## <a name="faults-induced-in-chaos"></a>Vyvolané v Chaos chyb
Chaos generuje chyby napříč celý cluster Service Fabric a komprimaci chyb, které jsou zobrazená v měsíců nebo let do několik hodin. Kombinace prokládaná chyb s vysokou odolnost rychlost vyhledá náročnějších případech, které jinak může být načteni. Toto cvičení Chaos vede k významné zlepšení kvality kódu služby.

Chaos indukuje chyb z následujících kategorií:

* Restartovat uzel
* Restartujte nasazený balíček kódu
* Odstranění repliky
* Restartujte repliky
* Přesunutí primární repliky (Konfigurovat)
* Přesunutí sekundární repliky (Konfigurovat)

Chaos běží ve více iterací. Každé iteraci se skládá z chyb a ověření clusteru pro toto období. Můžete nakonfigurovat čas strávený clusteru stabilizovat a pro ověření úspěšné. Pokud selhání naleznete v ověření clusteru, Chaos generuje a přetrvává ValidationFailedEvent s časovým razítkem UTC a podrobnosti o chybě. Představte si třeba instanci Chaos, který je nastaven na spouštění jednu hodinu s maximálně tři souběžných chyb. Chaos indukuje tři chyb a poté ověří stav clusteru. Ho iteruje předchozí krok, dokud není explicitně zastaven prostřednictvím rozhraní API StopChaosAsync nebo hodinových předá. Pokud cluster se změní na není v pořádku v kteroukoli iteraci (to znamená, není stabilizovat nebo ho se nezotavila během MaxClusterStabilizationTimeout předané), Chaos generuje ValidationFailedEvent. Tato událost označuje, že něco nepovede a může být třeba další šetření.

Získat chyb, které Chaos vyvolané, můžete použít rozhraní API GetChaosReport (powershell, C# nebo REST). Rozhraní API získá další segment Chaos sestavy na základě token pro pokračování předané nebo předané čas rozsahu. Můžete buď zadat ContinuationToken získat další segment Chaos sestavy nebo můžete zadat časový rozsah prostřednictvím StartTimeUtc a EndTimeUtc, ale ContinuationToken a časový rozsah nelze zadat ve stejném volání. Pokud nejsou k dispozici více než 100 Chaos události, Chaos sestavy je vrácený v segmenty, kde segment obsahuje více než 100 Chaos událostí.

## <a name="important-configuration-options"></a>Důležité konfigurační možnosti
* **TimeToRun**: celkový čas která Chaos spouští před dokončením s úspěch. Předtím, než byl spuštěn po dobu TimeToRun prostřednictvím rozhraní API StopChaos můžete zastavit Chaos.

* **MaxClusterStabilizationTimeout**: maximální množství času čekání na clusteru se nezotavila před generovala ValidationFailedEvent. Toto čekání je ke snížení zatížení v clusteru, zatímco probíhá obnovení. Provést kontroly jsou:
  * Pokud stav clusteru je v pořádku
  * Pokud stav služby je v pořádku.
  * Pokud cílový repliky nastavit velikost se dá dosáhnout oddílu služby
  * Že neexistuje žádná replik InBuild
* **MaxConcurrentFaults**: maximální počet souběžných chyb, které jsou v každé iteraci vyvolané. Je vyšší číslo, tím agresivnější Chaos a převzetí služeb při selhání a kombinace přechod stavu, které procházejí clusteru jsou i složitější. 

> [!NOTE]
> Bez ohledu na to jak velkou hodnotu *MaxConcurrentFaults* má Chaos zaručuje – chybí externí chyb - neexistuje ztrátě kvora nebo ztrátě dat.
>

* **EnableMoveReplicaFaults**: Povolí nebo zakáže chyb, které způsobují primární nebo sekundární repliky přesunout. Tyto chyby jsou povolené ve výchozím nastavení.
* **WaitTimeBetweenIterations**: dobu čekání mezi iterací. To znamená množství času Chaos se pozastaví po provedení zaokrouhlit chyb a nutnosti dokončení odpovídající ověření stavu clusteru. Čím vyšší hodnota, čím nižší je míra vkládání průměrná selhání.
* **WaitTimeBetweenFaults**: dobu čekání mezi dvě po sobě jdoucích chyb v jednom iterací. Vyšší hodnota, čím nižší současnému (nebo překryv mezi) chyb.
* **ClusterHealthPolicy**: zásady stavu clusteru se používá k ověření stavu clusteru mezi Chaos iterací. Pokud stav clusteru došlo k chybě nebo pokud se stane neočekávané výjimce během zpracování chyby, budou Chaos Počkejte 30 minut před další-kontrolou stavu - poskytnout chvíli recuperate clusteru.
* **Kontext**: kolekce (řetězec, řetězec) zadejte páry klíč hodnota. Mapy slouží k zaznamenání informací o Chaos spustit. Nemůže být více než 100 tyto dvojice a každý řetězec (klíč nebo hodnota) může mít maximálně povolených 4095 znaků dlouhé. Tato mapa je nastavena podle starter zmatku spustit, aby se volitelně ukládat kontext o konkrétní spustit.
* **ChaosTargetFilter**: Tento filtr lze použít k cílové Chaos chyb pouze na určité typy uzlů nebo pouze na určité instance aplikace. Pokud se nepoužívá ChaosTargetFilter, Chaos závady všechny entity clusteru. Pokud se používá ChaosTargetFilter Chaos závady jenom entity, které splňují specifikace ChaosTargetFilter. NodeTypeInclusionList a ApplicationInclusionList povolit pouze union sémantiku. Jinými slovy není možné zadat průnik NodeTypeInclusionList a ApplicationInclusionList. Například není možné zadat "poruch tuto aplikaci, pouze pokud je na daný typ uzlu." Jakmile entity je součástí NodeTypeInclusionList nebo ApplicationInclusionList, nelze dané entity vyloučit pomocí ChaosTargetFilter. I v případě, že applicationX se nezobrazí v ApplicationInclusionList, v některých Chaos iteraci applicationX může být došlo k chybě protože Odehrává se na uzlu nodeTypeY, který je součástí NodeTypeInclusionList. Pokud NodeTypeInclusionList i ApplicationInclusionList jsou null nebo prázdný, je vyvolána ArgumentException.
    * **NodeTypeInclusionList**: seznam typy uzlů, které chcete zahrnout do Chaos chyb. Všechny typy chyb (restartovat uzel, restartujte codepackage, odeberte repliky, restartujte repliky, přesunutí primární a sekundární) jsou povolené pro uzly z těchto typů uzlu. Pokud nodetype (vyslovení NodeTypeX) nejsou uvedené v NodeTypeInclusionList, pak uzlu úrovni chyb (např. NodeRestart) budou nikdy povolené pro uzly NodeTypeX, ale kód balíčku a repliky chyb lze povolit stále pro NodeTypeX, pokud aplikace v Jsou umístěny na uzlu NodeTypeX ApplicationInclusionList se stane. V tomto seznamu, tento počet zvýšit mohou obsahovat maximálně 100 názvy typu uzlu, se vyžaduje ke konfiguraci MaxNumberOfNodeTypesInChaosTargetFilter konfigurace upgradu.
    * **ApplicationInclusionList**: seznam aplikací identifikátory URI pro zahrnutí do Chaos chyb. Všechny repliky, které patří do služby tyto aplikace se přenášejí pomocí Chaos repliky chyb (restartování replika, replika odebrat, přesunutí primární a sekundární přesunutí). Chaos restartovat balíček kódu jenom v případě, že balíček kódu je hostitelem repliky jenom tyto aplikace. Pokud aplikace v tomto seznamu nezobrazí, ho můžete stále být došlo k chybě v některé Chaos iteraci Pokud ukončení aplikace v uzlu typu uzlu, který je incuded v NodeTypeInclusionList. Ale pokud applicationX je vázaný na nodeTypeY prostřednictvím omezení umístění a applicationX chybí z ApplicationInclusionList a nodeTypeY chybí z NodeTypeInclusionList, pak applicationX nikdy selže. Maximálně 1000 názvy aplikací mohou být součástí tohoto seznamu, tento počet zvýšit, se vyžaduje ke konfiguraci MaxNumberOfApplicationsInChaosTargetFilter konfigurace upgradu.

## <a name="how-to-run-chaos"></a>Jak spustit Chaos

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
