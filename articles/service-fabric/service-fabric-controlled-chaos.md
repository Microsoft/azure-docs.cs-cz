---
title: Vyvolat chaos v clusterech Service Fabric
description: Správa chaos v clusteru pomocí injektáže chyb a rozhraní API služby Analysis Services pro clustery
ms.topic: conceptual
ms.date: 02/05/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 72b8f7e9e4934b516f843ae8bc9bb7adc1c349ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101720500"
---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>NaChaos řízených v clusterech Service Fabric
Rozsáhlé distribuované systémy, jako jsou cloudové infrastruktury, jsou v podstatě nespolehlivé. Azure Service Fabric umožňuje vývojářům psát spolehlivé distribuované služby nad nespolehlivou infrastrukturou. Aby mohli vývojáři psát robustní distribuované služby nad nespolehlivou infrastrukturou, musí být schopni testovat stabilitu svých služeb, zatímco základní nespolehlivá infrastruktura prochází složitými přechody stavu v důsledku chyb.

[Vkládání chyb a služba Analysis Service clusteru](./service-fabric-testability-overview.md) (označované také jako služba analýzy chyb) umožňují vývojářům navolávat chyby při testování svých služeb. Tyto cílené simulované chyby, jako je [restartování oddílu](/powershell/module/servicefabric/start-servicefabricpartitionrestart), můžou přispět k provádění nejběžnějších přechodů mezi stavy. Cílené simulované chyby se ale účtují podle definice, takže můžou přijít o chyby, které se zobrazují jenom v pevně předpokládaném, dlouhém a složitém pořadí přechodů mezi stavy. Pro nevyvážené testování můžete použít chaos.

Chaos simuluje pravidelné, prokládané chyby (jak v rámci celého clusteru jsou bezproblémové i nedarované) po delší dobu. Plynulá chyba se skládá ze sady Service Fabric volání rozhraní API, například restartování repliky je bezproblémové selhání, protože se jedná o blízko následovaný otevřeným na replice. Odebrání repliky, přesunutí primární repliky a přesunutí sekundární repliky jsou další plynulé chyby, na které chaos přichází. Nedarované chyby se ukončí proces, jako je restartování uzlu a restartování balíčku kódu. 

Jakmile nakonfigurujete chaos s frekvencí a typem chyb, můžete spustit chaos prostřednictvím C#, PowerShellu nebo REST API a začít vytvářet chyby v clusteru a ve vašich službách. Chaos můžete nakonfigurovat tak, aby se spouštěla po zadaném časovém období (například po dobu jedné hodiny), po kterém se chaos automaticky zastaví, nebo můžete volat rozhraní StopChaos API (C#, PowerShell nebo REST) a kdykoli ho zastavit.

> [!NOTE]
> V jeho aktuální podobě chaos vystavuje pouze bezpečné chyby, což znamená, že při absenci externích chyb v důsledku ztráty kvora nedochází k neztrátě dat.
>

I když je spuštěný chaos, vytváří různé události, které zachytí stav běhu v daném okamžiku. Například ExecutingFaultsEvent obsahuje všechny chyby, které chaos se rozhodly spustit v této iteraci. ValidationFailedEvent obsahuje podrobnosti o selhání ověřování (stav nebo problémy se stabilitou), které byly zjištěny během ověřování clusteru. Můžete vyvolat rozhraní API GetChaosReport (C#, PowerShell nebo REST) a získat tak zprávu o běhu chaos. Tyto události se chovají ve [spolehlivém slovníku](./service-fabric-reliable-services-reliable-collections.md), který má zásady zkracování, které jsou vynásobené dvěma konfiguracemi: **MaxStoredChaosEventCount** (výchozí hodnota je 25000) a **StoredActionCleanupIntervalInSeconds** (výchozí hodnota je 3600). Všechny *StoredActionCleanupIntervalInSeconds* chaos kontroly a všechny kromě nejaktuálnějších událostí *MaxStoredChaosEventCount* jsou vymazány ze spolehlivého slovníku.

## <a name="faults-induced-in-chaos"></a>Chyby vyvolané v chaos
Chaos vygeneruje chyby napříč celým clusterem Service Fabric a komprimuje chyby, které se zobrazují v měsících nebo rocích, do několika hodin. Kombinace vzájemně vykládaných chyb s vysokou mírou chyb vyhledává rohové případy, které by jinak mohly chybět. Toto cvičení chaos vede k výraznému zlepšení kvality kódu služby.

Chaos vygenerovala chyby z následujících kategorií:

* Restartování uzlu
* Restartování nasazeného balíčku kódu
* Odebrání repliky
* Restartování repliky
* Přesunutí primární repliky (konfigurovatelné)
* Přesunutí sekundární repliky (konfigurovatelné)

Chaos se spouští v několika iteracích. Každá iterace se skládá z chyb a ověření clusteru v zadaném období. Můžete nakonfigurovat čas strávený na stabilizaci clusteru a ověřit úspěšné ověření. Pokud se v ověření clusteru najde chyba, chaos vygeneruje a zachovává ValidationFailedEvent s časovým razítkem UTC a podrobnostmi o selhání. Představte si například instanci chaos, která je nastavená tak, aby běžela po určitou hodinu s maximálně třemi souběžnými chybami. Chaos vystaví tři chyby a potom ověří stav clusteru. Provede iteraci v předchozím kroku, dokud se explicitně nezastaví prostřednictvím rozhraní StopChaosAsync API nebo hodinových průchodů. Pokud cluster v žádné iteraci přestane být v pořádku (to znamená, že se nejedná o dobrý stav v rámci předaného MaxClusterStabilizationTimeout), chaos vygeneruje ValidationFailedEvent. Tato událost znamená, že došlo k chybě nějakého problému a může vyžadovat další šetření.

Pokud chcete zjistit, které chyby chaos vyvolané, můžete použít rozhraní GetChaosReport API (PowerShell, C# nebo REST). Rozhraní API získá další segment sestavy chaos na základě tokenu pro pokračování nebo předaného časového rozsahu. Můžete buď zadat token continuationtoken k získání dalšího segmentu sestavy chaos, nebo můžete zadat časový rozsah prostřednictvím StartTimeUtc a EndTimeUtc, ale nemůžete zadat token continuationtoken i časový rozsah ve stejném volání. V případě, že existuje více než 100 událostí chaos, je vrácena zpráva chaos v segmentech, kde segment neobsahuje více než 100 událostí chaos.

## <a name="important-configuration-options"></a>Důležité možnosti konfigurace
* **TimeToRun**: celkový čas, který chaos spustí před tím, než se dokončí úspěšně. Chaos můžete zastavit předtím, než se spustí pro TimeToRun období prostřednictvím rozhraní StopChaos API.

* **MaxClusterStabilizationTimeout**: maximální doba, po kterou se má čekat na to, aby se cluster stal dobrým stavem, než bude vyprodukována ValidationFailedEvent. To čeká na snížení zatížení clusteru během obnovování. Provedené kontroly:
  * Pokud je stav clusteru v pořádku
  * Pokud je stav služby v pořádku
  * Pokud je dosažena cílová velikost sady replik pro oddíl služby
  * Neexistují žádné repliky InBuild.
* **MaxConcurrentFaults**: maximální počet souběžných chyb, které jsou v každé iteraci vyvolané. Čím vyšší je počet, čímější je chaos a že jsou kombinace převzetí služeb při selhání a přechody stavu, se kterými cluster projde, taky složitější. 

> [!NOTE]
> Bez ohledu na to, jak vysoká hodnota *MaxConcurrentFaults* má, chaos záruky – při absenci externích chyb – nedochází ke ztrátě kvora nebo ztrátě dat.
>

* **EnableMoveReplicaFaults**: povolí nebo zakáže chyby, které způsobují přesunutí primárních nebo sekundárních replik. Ve výchozím nastavení jsou tyto chyby povolené.
* **WaitTimeBetweenIterations**: doba, po kterou se mají čekat mezi iteracemi. To znamená, že množství času chaos se po provedení zaokrouhlení chyb a dokončení odpovídajícího ověření stavu clusteru pozastaví. Čím vyšší je hodnota, tím nižší je průměrná míra vkládání chyb.
* **WaitTimeBetweenFaults**: množství času, které se má čekat mezi dvěma po sobě jdoucími chybami v rámci jedné iterace. Čím vyšší je hodnota, tím nižší je souběžnost (nebo překrytí mezi) chybami.
* **ClusterHealthPolicy**: zásada stavu clusteru se používá k ověření stavu clusteru v mezi chaos iteracemi. Pokud je stav clusteru chyba nebo pokud dojde k neočekávané výjimce během provádění chyby, chaos počká 30 minut před dalším stavem-check-k tomu, aby cluster po nějakou dobu recuperate.
* **Context**: kolekce (String, String) typu páry klíč-hodnota. Mapu lze použít k zaznamenání informací o spuštění chaos. Takové páry nesmí být větší než 100 a každý řetězec (klíč nebo hodnota) může mít maximálně 4095 znaků. Tato mapa je nastavená počáteční verzí chaos spuštění na volitelně uložit kontext týkající se konkrétního běhu.
* **ChaosTargetFilter**: Tento filtr lze použít k zacílení chyb chaos pouze na určité typy uzlů nebo pouze na určité instance aplikace. Pokud se ChaosTargetFilter nepoužívá, chaos chyby všechny entity clusteru. Pokud se používá ChaosTargetFilter, chaos chyby pouze ty entity, které splňují specifikaci ChaosTargetFilter. NodeTypeInclusionList a ApplicationInclusionList umožňují jenom sémantiku sjednocení. Jinými slovy není možné zadat průnik NodeTypeInclusionList a ApplicationInclusionList. Například není možné zadat "selhání této aplikace pouze v případě, že je na tomto typu uzlu". Jakmile je entita zahrnutá v NodeTypeInclusionList nebo ApplicationInclusionList, tuto entitu nejde vyloučit pomocí ChaosTargetFilter. I v případě, že se applicationX v ApplicationInclusionList nezobrazí, může v některých chaos iterace applicationX dojít k chybě, protože se stane na uzlu nodeType, který je součástí NodeTypeInclusionList. Pokud mají hodnoty NodeTypeInclusionList i ApplicationInclusionList hodnotu null nebo jsou prázdné, je vyvolána výjimka ArgumentException.
    * **NodeTypeInclusionList**: seznam typů uzlů, které se mají zahrnout do chyb chaos. Všechny typy chyb (restartovat uzel, restartovat codepackage, odebrat repliku, restartovat repliku, přesunout primární a přesunout sekundární) jsou povolené pro uzly těchto typů uzlů. Pokud se NodeTypeX (NodeType) nezobrazí v NodeTypeInclusionList, pak chyby na úrovni uzlu (jako NodeRestart) nikdy nebudou povolené pro uzly NodeTypeX, ale pokud se aplikace v NodeTypeX stane umístěnou v uzlu ApplicationInclusionList, může se i přesto povolit balíček kódu a chyby repliky. V tomto seznamu může být zahrnutých maximálně 100 názvů typů uzlů. aby se tento počet zvýšil, je pro konfiguraci MaxNumberOfNodeTypesInChaosTargetFilter nutný upgrade konfigurace.
    * **ApplicationInclusionList**: seznam identifikátorů URI aplikace, které mají být zahrnuty do chyb chaos. Všechny repliky patřící ke službám těchto aplikací jsou snadněji k chybám repliky (restartování repliky, odebrání repliky, přesunutí primárního a přesunutí sekundárního) pomocí chaos. Chaos může restartovat balíček kódu pouze v případě, že balíček kódu hostuje pouze repliky těchto aplikací. Pokud se aplikace v tomto seznamu nezobrazí, může být stále chyba v některém chaos iteraci, pokud aplikace skončí na uzlu typu uzlu, který je součástí NodeTypeInclusionList. Pokud je však applicationX svázán s nodeType prostřednictvím omezení umístění a applicationX chybí od ApplicationInclusionList a uzel nodeType není v NodeTypeInclusionList, pak applicationX nebude nikdy chybět. V tomto seznamu může být zahrnutých maximálně 1000 názvů aplikací. aby se tento počet zvýšil, je pro konfiguraci MaxNumberOfApplicationsInChaosTargetFilter nutný upgrade konfigurace.

## <a name="how-to-run-chaos"></a>Jak spustit chaos

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
