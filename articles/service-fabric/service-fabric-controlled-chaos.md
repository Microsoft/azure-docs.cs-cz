---
title: Indukovat Chaos v clusterech Service Fabric
description: Použití api injection a clusterové služby pro správu chaosu v clusteru.
author: motanv
ms.topic: conceptual
ms.date: 02/05/2018
ms.author: motanv
ms.openlocfilehash: 37b451abd0a519dff17aba9b2d6c42b4762f30cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463166"
---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Indukovat řízený chaos v clusterech Service Fabric
Rozsáhlé distribuované systémy, jako jsou cloudové infrastruktury, jsou ze své podstaty nespolehlivé. Azure Service Fabric umožňuje vývojářům psát spolehlivé distribuované služby nad nespolehlivé infrastruktury. Chcete-li psát robustní distribuované služby na nespolehlivé infrastruktury, vývojáři musí být schopni testovat stabilitu svých služeb, zatímco základní nespolehlivé infrastruktury prochází složité přechody stavu kvůli chybám.

[Služba vkládání chyb a clusterová analýza](https://docs.microsoft.com/azure/service-fabric/service-fabric-testability-overview) (označovaná také jako Služba analýzy poruch) poskytuje vývojářům možnost vyvolat chyby při testování svých služeb. Tyto cílené simulované chyby, jako [je restartování oddílu](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricpartitionrestart?view=azureservicefabricps), může pomoci při cvičení nejběžnější přechody stavu. Nicméně cílené simulované chyby jsou zkreslené podle definice, a proto mohou chybět chyby, které se zobrazují pouze v těžko předpovědět, dlouhé a složité pořadí přechodů stavu. Pro nestranné testování můžete použít Chaos.

Chaos simuluje periodické, proložené chyby (ladné i ungraceful) v celém clusteru po delší časové období. Ladná chyba se skládá ze sady volání rozhraní API service fabric, například restartování chyby repliky je elegantní chyba, protože se jedná o close následuje otevřít na replice. Odebrat repliku, přesunout primární repliku a přesunout sekundární repliku jsou další ladné chyby vykonávané chaosem. Ungraceful chyby jsou ukončení procesu, jako je restartování uzlu a restartovat kód balíčku. 

Jakmile nakonfigurujete Chaos s rychlostí a druhchyb, můžete spustit Chaos prostřednictvím Jazyka C#, Powershell nebo ROZHRANÍ REST API a začít generovat chyby v clusteru a ve vašich službách. Můžete nakonfigurovat Chaos spustit po určitou dobu (například po dobu jedné hodiny), po kterém Chaos zastaví automaticky, nebo můžete volat StopChaos API (C#, Powershell, nebo REST) zastavit kdykoli.

> [!NOTE]
> Ve své současné podobě Chaos indukuje pouze bezpečné chyby, což znamená, že při absenci externích chyb dojde ke ztrátě kvora nebo ke ztrátě dat nikdy nedojde.
>

Zatímco Chaos běží, vytváří různé události, které zachycují stav běhu v tuto chvíli. Například ExecutingFaultsEvent obsahuje všechny chyby, které Chaos se rozhodl provést v této iteraci. A ValidationFailedEvent obsahuje podrobnosti o selhání ověření (problémy se stavem nebo stabilitou), která byla nalezena během ověřování clusteru. Můžete vyvolat GetChaosReport API (C#, Powershell nebo REST) získat zprávu chaos běží. Tyto události získat trvalé ve [spolehlivém slovníku](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-reliable-collections), který má zásady zkrácení diktované dvě konfigurace: **MaxStoredChaosEventCount** (výchozí hodnota je 25000) a **StoredActionCleanupIntervalInSeconds** (výchozí hodnota je 3600). Každý *StoredActionCleanupIntervalInSeconds* Chaos kontroly a všechny, ale nejnovější *MaxStoredChaosEventCount* události, jsou vymazány ze spolehlivého slovníku.

## <a name="faults-induced-in-chaos"></a>Chyby vyvolané v chaosu
Chaos generuje chyby v celém clusteru Service Fabric a komprimuje chyby, které jsou vidět v měsících nebo letech do několika hodin. Kombinace prokládání chyb s vysokou poruchovou rychlostí najde rohové případy, které by jinak mohly být vynechány. Toto cvičení Chaos vede k významnému zlepšení kvality kódu služby.

Chaos vyvolává chyby z následujících kategorií:

* Restartování uzlu
* Restartování balíčku nasazeného kódu
* Odebrání repliky
* Restartování repliky
* Přesunutí primární repliky (konfigurovatelné)
* Přesunutí sekundární repliky (konfigurovatelné)

Chaos běží ve více iterací. Každá iterace se skládá z chyb a ověření clusteru za zadané období. Můžete nakonfigurovat čas strávený pro cluster stabilizovat a pro ověření úspěšné. Pokud je při ověřování clusteru nalezena chyba, chaos generuje a zachová validaciFailedEvent s časovým razítkem UTC a podrobnostmi selhání. Zvažte například instanci Chaos, která je nastavena na spuštění po dobu jedné hodiny s maximálně třemi souběžnými chybami. Chaos vyvolá tři chyby a potom ověří stav clusteru. Iterem přes předchozí krok, dokud je explicitně zastavena prostřednictvím Rozhraní API StopChaosAsync nebo jednu hodinu průchodů. Pokud cluster ustane není v pořádku v jakékoli iteraci (to znamená, že se nestabilizuje nebo se nestane v pořádku v rámci předávaný MaxClusterStabilizationTimeout), Chaos generuje ValidationFailedEvent. Tato událost naznačuje, že se něco pokazilo a může vyžadovat další šetření.

Chcete-li získat chyby Chaos vyvolané, můžete použít GetChaosReport API (powershell, C #, nebo REST). Rozhraní API získá další segment sestavy Chaos na základě předávaného tokenu pokračování nebo předávaného časového rozsahu. Můžete buď zadat ContinuationToken získat další segment sestavy Chaos nebo můžete zadat časový rozsah prostřednictvím StartTimeUtc a EndTimeUtc, ale nelze zadat continuationtoken a časový rozsah ve stejném volání. Pokud existuje více než 100 událostí chaosu, sestava Chaos je vrácena v segmentech, kde segment neobsahuje více než 100 událostí Chaosu.

## <a name="important-configuration-options"></a>Důležité možnosti konfigurace
* **TimeToRun**: Celkový čas, který Chaos běží před dokončením s úspěchem. Chaos můžete zastavit před spuštěním období TimeToRun prostřednictvím rozhraní API StopChaos.

* **MaxClusterStabilizationTimeout**: Maximální doba čekání na clusteru, aby se stal v pořádku před vytvořením ValidationFailedEvent. Toto čekání je snížit zatížení clusteru při jeho obnovení. Provedené kontroly jsou:
  * Pokud je stav clusteru v pořádku
  * Pokud je stav služby v pořádku
  * Pokud je dosaženo velikosti sady cílové repliky pro oddíl služby
  * Že neexistují žádné repliky InBuild
* **MaxConcurrentFaults**: Maximální počet souběžných chyb, které jsou vyvolány v každé iteraci. Čím vyšší číslo, tím agresivnější chaos je a převzetí služeb při selhání a kombinace přechodu stavu, které cluster prochází, jsou také složitější. 

> [!NOTE]
> Bez ohledu na to, jak vysokou hodnotu *má MaxConcurrentFaults,* Chaos zaručuje - v nepřítomnosti externích chyb - nedochází ke ztrátě kvora nebo ztrátě dat.
>

* **EnableMoveReplicaFaults**: Povolí nebo zakáže chyby, které způsobují přesunutí primárních nebo sekundárních replik. Tyto chyby jsou ve výchozím nastavení povoleny.
* **WaitTimeBetweenIterations**: Doba čekání mezi iteracemi. To znamená, že doba Chaos pozastaví po provedení kola chyb a po dokončení odpovídající ověření stavu clusteru. Čím vyšší je hodnota, tím nižší je průměrná rychlost vstřikování poruchy.
* **WaitTimeBetweenFaults**: Doba čekání mezi dvěma po sobě jdoucími chybami v jedné iteraci. Čím vyšší je hodnota, tím nižší je souběžnost (nebo překrytí mezi) chyb.
* **ClusterHealthPolicy**: Zásady stavu clusteru se používají k ověření stavu clusteru mezi iteracemi chaosu. Pokud je stav clusteru v chybě nebo pokud dojde k neočekávané výjimce během spuštění poruchy, Chaos bude čekat 30 minut před další kontrolou stavu - poskytnout clusteru nějaký čas na zotavení.
* **Kontext**: Kolekce párů typu klíč-hodnota (řetězec, řetězec). Mapu lze použít k zaznamenání informací o spuštění Chaosu. Nemůže existovat více než 100 takových párů a každý řetězec (klíč nebo hodnota) může být maximálně 4095 znaků dlouhý. Tato mapa je nastavena startérchaos spustit volitelně ukládat kontext o konkrétní spuštění.
* **ChaosTargetFilter**: Tento filtr lze použít k cílení chyb Chaos pouze na určité typy uzlů nebo pouze na určité instance aplikace. Pokud ChaosTargetFilter není použit, Chaos chyby všechny entity clusteru. Pokud ChaosTargetFilter se používá, Chaos chyby pouze entity, které splňují ChaosTargetFilter specifikace. NodeTypeInclusionList a ApplicationInclusionList povolují pouze sémantiku sjednocení. Jinými slovy není možné zadat průsečík NodeTypeInclusionList a ApplicationInclusionList. Například není možné zadat "chyba této aplikace pouze v případě, že je na tomto typu uzlu." Jakmile je entita zahrnuta do nodeTypeInclusionList nebo ApplicationInclusionList, nelze tuto entitu vyloučit pomocí chaostargetfilter. I v případě, že applicationX nezobrazí v ApplicationInclusionList, v některých chaos iterace applicationX může být vadný, protože se stane být na uzlu nodeTypeY, který je součástí NodeTypeInclusionList. Pokud jsou nodeTypeInclusionList a ApplicationInclusionList null nebo empty, je vyvolána výjimka ArgumentException.
    * **NodeTypeInclusionList**: Seznam typů uzlů, které mají být zahrnuty do chyb Chaos. Pro uzly těchto typů uzlů jsou povoleny všechny typy chyb (restartování uzlu, restartování balíčku kódů, odebrání repliky, restartování repliky, přesunutí primárního a sekundárního přesunutí). Pokud se nodetype (řekněme NodeTypeX) nezobrazí v NodeTypeInclusionList, pak chyby na úrovni uzlu (jako NodeRestart) nikdy nebude povolena pro uzly NodeTypeX, ale kód balíček a replika chyby mohou být stále povolena pro NodeTypeX, pokud aplikace v ApplicationInclusionList se zobrazí v uzlu NodeTypeX. Do tohoto seznamu může být zahrnuto maximálně 100 názvů typů uzlů, aby se toto číslo zvýšilo, je pro konfiguraci MaxNumberOfNodeTypesInChaosTargetFilter vyžadován upgrade konfigurace.
    * **ApplicationInclusionList**: Seznam identifikátorů URI aplikací, které mají být zahrnuty do chyb chaosu. Všechny repliky, které patří do služeb těchto aplikací jsou přístupné chyby replik (restartovat repliku, odebrat repliku, přesunout primární a přesunout sekundární) podle Chaos. Chaos může restartovat balíček kódu pouze v případě, že balíček kódu hostuje pouze repliky těchto aplikací. Pokud se aplikace nezobrazí v tomto seznamu, může být stále chybována v některé iteraci Chaos, pokud aplikace skončí na uzlu typu uzlu, který je součástí NodeTypeInclusionList. Však pokud applicationX je vázána na nodeTypeY prostřednictvím umístění omezení a applicationX chybí ApplicationInclusionList a nodeTypeY chybí NodeTypeInclusionList, pak applicationX nikdy nebude vadný. Do tohoto seznamu může být zahrnuto maximálně 1000 názvů aplikací, aby se toto číslo zvýšilo, je pro konfiguraci MaxNumberOfApplicationsInChaosTargetFilter vyžadován upgrade konfigurace.

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
            // Chaos executes in iterations and at the start of each iteration it validates the health of cluster
            // entities. 
            // During validation if a cluster entity is not stable and healthy within
            // MaxClusterStabilizationTimeoutInSeconds, Chaos generates a validation failed event.
            var maxClusterStabilizationTimeout = TimeSpan.FromSeconds(30.0);

            var timeToRun = TimeSpan.FromMinutes(60.0);

            // MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration. 
            // Chaos executes in iterations and two consecutive iterations are separated by a validation phase.
            // The higher the concurrency, the more aggressive the injection of faults -- inducing more complex
            // series of states to uncover bugs.
            // The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
            var maxConcurrentFaults = 3;

            // Describes a map, which is a collection of (string, string) type key-value pairs. The map can be
            // used to record information about the Chaos run. There cannot be more than 100 such pairs and
            // each string (key or value) can be at most 4095 characters long.
            // This map is set by the starter of the Chaos run to optionally store the context about the specific run.
            var startContext = new Dictionary<string, string>{{"ReasonForStart", "Testing"}};

            // Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the
            // lower the fault injection rate.
            var waitTimeBetweenIterations = TimeSpan.FromSeconds(10);

            // Wait time (in seconds) between consecutive faults within a single iteration.
            // The larger the value, the lower the overlapping between faults and the simpler the sequence of
            // state transitions that the cluster goes through. 
            // The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
            var waitTimeBetweenFaults = TimeSpan.Zero;

            // Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
            var clusterHealthPolicy = new ClusterHealthPolicy
            {
                ConsiderWarningAsError = false,
                MaxPercentUnhealthyApplications = 100,
                MaxPercentUnhealthyNodes = 100
            };

            // All types of faults, restart node, restart code package, restart replica, move primary replica,
            // and move secondary replica will happen for nodes of type 'FrontEndType'
            var nodetypeInclusionList = new List<string> { "FrontEndType"};

            // In addition to the faults included by nodetypeInclusionList,
            // restart code package, restart replica, move primary replica, move secondary replica faults will
            // happen for 'fabric:/TestApp2' even if a replica or code package from 'fabric:/TestApp2' is residing
            // on a node which is not of type included in nodeypeInclusionList.
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
# During validation if a cluster entity is not stable and healthy within MaxClusterStabilizationTimeoutInSeconds,
# Chaos generates a validation failed event.
$maxClusterStabilizationTimeSecs = 30

# MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration.
# Chaos executes in iterations and two consecutive iterations are separated by a validation phase.
# The higher the concurrency, the more aggressive the injection of faults -- inducing more complex series of
# states to uncover bugs.
# The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
$maxConcurrentFaults = 3

# Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the lower the
# fault injection rate.
$waitTimeBetweenIterationsSec = 10

# Wait time (in seconds) between consecutive faults within a single iteration.
# The larger the value, the lower the overlapping between faults and the simpler the sequence of state
# transitions that the cluster goes through.
# The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
$waitTimeBetweenFaultsSec = 0

# Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
$clusterHealthPolicy = new-object -TypeName System.Fabric.Health.ClusterHealthPolicy
$clusterHealthPolicy.MaxPercentUnhealthyNodes = 100
$clusterHealthPolicy.MaxPercentUnhealthyApplications = 100
$clusterHealthPolicy.ConsiderWarningAsError = $False

# Describes a map, which is a collection of (string, string) type key-value pairs. The map can be used to record
# information about the Chaos run.
# There cannot be more than 100 such pairs and each string (key or value) can be at most 4095 characters long.
# This map is set by the starter of the Chaos run to optionally store the context about the specific run.
$context = @{"ReasonForStart" = "Testing"}

#List of cluster entities to target for Chaos faults.
$chaosTargetFilter = new-object -TypeName System.Fabric.Chaos.DataStructures.ChaosTargetFilter
$chaosTargetFilter.NodeTypeInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# All types of faults, restart node, restart code package, restart replica, move primary replica, and move
# secondary replica will happen for nodes of type 'FrontEndType'
$chaosTargetFilter.NodeTypeInclusionList.AddRange( [string[]]@("FrontEndType") )
$chaosTargetFilter.ApplicationInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# In addition to the faults included by nodetypeInclusionList, 
# restart code package, restart replica, move primary replica, move secondary replica faults will happen for
# 'fabric:/TestApp2' even if a replica or code package from 'fabric:/TestApp2' is residing on a node which is
# not of type included in nodeypeInclusionList.
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
