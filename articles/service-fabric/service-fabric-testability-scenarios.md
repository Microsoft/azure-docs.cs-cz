---
title: Vytvoření chaosu a testů převzetí služeb při selhání pro Azure Service Fabric
description: Pomocí service fabric chaos test a scénáře převzetí služeb při selhání test vyvolat chyby a ověřit spolehlivost vašich služeb.
author: motanv
ms.topic: conceptual
ms.date: 10/1/2019
ms.author: motanv
ms.openlocfilehash: 206b02024ad052a12e87cfdf1773815027e8aec4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465535"
---
# <a name="testability-scenarios"></a>Scénáře testovatelnosti
Velké distribuované systémy, jako jsou cloudové infrastruktury, jsou ze své podstaty nespolehlivé. Azure Service Fabric poskytuje vývojářům možnost psát služby pro spuštění na vrcholu nespolehlivé infrastruktury. Aby mohli vývojáři psát vysoce kvalitní služby, musí být schopni navodit takovou nespolehlivou infrastrukturu, aby otestovali stabilitu svých služeb.

Služba Fault Analysis Service poskytuje vývojářům možnost vyvolat chybové akce k testování služeb v případě selhání. Cílené simulované poruchy vás však dostanou jen tak daleko. Chcete-li dále provést testování, můžete použít testovací scénáře v Service Fabric: test chaosu a test převzetí služeb při selhání. Tyto scénáře simulovat souvislé proložené chyby, a to jak elegantní a ungraceful, v celém clusteru po delší časové období. Jakmile je test nakonfigurován s rychlostí a druhem chyb, lze jej spustit pomocí c# API nebo prostředí PowerShell, aby se generovaly chyby v clusteru a ve vaší službě.

> [!WARNING]
> ChaosTestScenario je nahrazen odolnější, založené na službě Chaos. Další podrobnosti naleznete v novém článku [Controlled Chaos.](service-fabric-controlled-chaos.md)
> 
> 

## <a name="chaos-test"></a>Test chaosu
Scénář chaosu generuje chyby v celém clusteru Service Fabric. Scénář komprimuje chyby obecně vidět v měsících nebo letech na několik hodin. Kombinace prokládání chyb s vysokou poruchovou rychlostí najde rohové případy, které jsou jinak vynechány. To vede k významnému zlepšení kvality kódu služby.

### <a name="faults-simulated-in-the-chaos-test"></a>Poruchy simulované v testu chaosu
* Restartování uzlu
* Restartování balíčku nasazeného kódu
* Odebrání repliky
* Restartování repliky
* Přesunutí primární repliky (volitelné)
* Přesunutí sekundární repliky (volitelné)

Test chaosu spustí několik iterací chyb a ověření clusteru za zadané časové období. Čas strávený pro clusteru stabilizovat a pro ověření úspěšné je také konfigurovatelné. Scénář se nezdaří, když narazí na jednu chybu v ověření clusteru.

Zvažte například testovací sadu, která má být spuštěna po dobu jedné hodiny s maximálně třemi souběžnými chybami. Test vyvolá tři chyby a potom ověřte stav clusteru. Test bude iterát přes předchozí krok, dokud clusteru se stane není v pořádku nebo jednu hodinu projde. Pokud se cluster v žádné iteraci neuzdraví, tj. Tato výjimka naznačuje, že se něco pokazilo a potřebuje další šetření.

Ve své současné podobě motor pro generování chyb v testu chaosu vyvolává pouze bezpečné chyby. To znamená, že při absenci externích chyb nikdy nedojde ke ztrátě kvora nebo dat.

### <a name="important-configuration-options"></a>Důležité možnosti konfigurace
* **TimeToRun**: Celkový čas, který bude test spuštěn před dokončením s úspěchem. Test může být dokončen dříve namísto selhání ověření.
* **MaxClusterStabilizationTimeout**: Maximální doba čekání clusteru, aby se stal v pořádku před selháním testu. Provedené kontroly jsou, zda je stav clusteru v pořádku, stav služby je v pořádku, je dosaženo velikosti sady cílové repliky pro oddíl služby a neexistují žádné repliky InBuild.
* **MaxConcurrentFaults**: Maximální počet souběžných chyb vyvolaných v každé iteraci. Čím vyšší číslo, tím agresivnější test, tedy výsledkem složitější převzetí služeb při selhání a kombinace přechodu. Test zaručuje, že při absenci externích chyb nedojde ke ztrátě kvora nebo dat, bez ohledu na to, jak vysoká je tato konfigurace.
* **EnableMoveReplicaFaults**: Povolí nebo zakáže chyby, které jsou příčinou přesunutí primární nebo sekundární repliky. Tyto chyby jsou ve výchozím nastavení zakázány.
* **WaitTimeBetweenIterations**: Doba čekání mezi iterací, tj.

### <a name="how-to-run-the-chaos-test"></a>Jak spustit test chaosu
Ukázka v jazyce C#

```csharp
using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunChaosTestScenarioAsync(clusterConnection).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunChaosTestScenarioAsync(string clusterConnection)
    {
        TimeSpan maxClusterStabilizationTimeout = TimeSpan.FromSeconds(180);
        uint maxConcurrentFaults = 3;
        bool enableMoveReplicaFaults = true;

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        ChaosTestScenarioParameters scenarioParameters = new ChaosTestScenarioParameters(
          maxClusterStabilizationTimeout,
          maxConcurrentFaults,
          enableMoveReplicaFaults,
          timeToRun);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        ChaosTestScenario chaosScenario = new ChaosTestScenario(fabricClient, scenarioParameters);

        try
        {
            await chaosScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```

PowerShell

Modul Service Fabric Powershell obsahuje dva způsoby, jak začít scénář chaosu. `Invoke-ServiceFabricChaosTestScenario`je založen na klientovi a pokud je klientský počítač v polovině testu vypnut, nebudou zavedeny žádné další chyby. Alternativně je sada příkazů, které mají zachovat test spuštěnv případě vypnutí počítače. `Start-ServiceFabricChaos`používá stavové a spolehlivé systémové služby s názvem FaultAnalysisService, zajištění chyb zůstane zavedena, dokud TimeToRun je nahoru. `Stop-ServiceFabricChaos`lze ručně zastavit scénář a `Get-ServiceFabricChaosReport` získá sestavu. Další informace naleznete v [referenční příručce Azure Service Fabric Powershell](https://docs.microsoft.com/powershell/module/servicefabric/?view=azureservicefabricps) a [inducing controlled chaos v clusterech Service Fabric](service-fabric-controlled-chaos.md).

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```


## <a name="failover-test"></a>Test převzetí služeb při selhání
Scénář testu převzetí služeb při selhání je verze scénáře chaos test, který se zaměřuje na konkrétní oddíl služby. Testuje vliv převzetí služeb při selhání na konkrétní oddíl služby při ponechání ostatních služeb bez e-ovlivnil. Jakmile je nakonfigurován s informacemi o cílovém oddílu a dalšími parametry, běží jako nástroj na straně klienta, který používá rozhraní API jazyka C# nebo prostředí PowerShell ke generování chyb pro oddíl služby. Scénář iteruje prostřednictvím posloupnosti simulovaných chyb a ověření služby, zatímco vaše obchodní logika běží na straně, aby bylo zajištěno zatížení. Selhání ověření služby označuje problém, který vyžaduje další šetření.

### <a name="faults-simulated-in-the-failover-test"></a>Poruchy simulované v testu převzetí služeb při selhání
* Restartování balíčku nasazeného kódu, ve kterém je oddíl hostován
* Odebrání primární/sekundární instance repliky nebo bezstavové instance
* Restartování primární sekundární repliky (pokud trvalá služba)
* Přesunutí primární repliky
* Přesunutí sekundární repliky
* Restartování oddílu

Test převzetí služeb při selhání vyvolá vybranou chybu a poté spustí ověření ve službě, aby byla zajištěna její stabilita. Test převzetí služeb při selhání vyvolá pouze jednu chybu najednou, na rozdíl od možných více chyb v testu chaosu. Pokud oddíl služby není stabilizovat v rámci nakonfigurovaného časového času po každé chybě, test se nezdaří. Test vyvolá pouze bezpečné závady. To znamená, že při absenci externích selhání nedojde ke ztrátě kvora nebo dat.

### <a name="important-configuration-options"></a>Důležité možnosti konfigurace
* **PartitionSelector**: Selector objekt, který určuje oddíl, který je třeba cílit.
* **TimeToRun**: Celkový čas, který bude test spuštěn před dokončením.
* **MaxServiceStabilizationTimeout**: Maximální doba čekání clusteru, aby se stal v pořádku před selháním testu. Provedené kontroly jsou, zda stav služby je v pořádku, cílová velikost sady replik je dosaženo pro všechny oddíly a neexistují žádné repliky InBuild.
* **WaitTimeBetweenFaults**: Doba čekání mezi každou chybou a ověřovacím cyklem.

### <a name="how-to-run-the-failover-test"></a>Jak spustit test převzetí služeb při selhání
**C #**

```csharp
using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunFailoverTestScenarioAsync(clusterConnection, serviceName).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunFailoverTestScenarioAsync(string clusterConnection, Uri serviceName)
    {
        TimeSpan maxServiceStabilizationTimeout = TimeSpan.FromSeconds(180);
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        FailoverTestScenarioParameters scenarioParameters = new FailoverTestScenarioParameters(
          randomPartitionSelector,
          timeToRun,
          maxServiceStabilizationTimeout);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        FailoverTestScenario failoverScenario = new FailoverTestScenario(fabricClient, scenarioParameters);

        try
        {
            await failoverScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```


**PowerShell**

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/SampleApp/SampleService"

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindSingleton
```
