---
title: Vytvoření chaos a testů převzetí služeb při selhání pro Azure Service Fabric
description: Pomocí scénářů Service Fabric chaos test a převzetí služeb při selhání můžete navolávat chyby a ověřit spolehlivost vašich služeb.
ms.topic: conceptual
ms.date: 10/1/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: ef49d541cd00d289176ca61e055102faf6df7514
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735552"
---
# <a name="testability-scenarios"></a>Scénáře testování
Velké distribuované systémy, jako jsou cloudové infrastruktury, jsou v podstatě nespolehlivé. Azure Service Fabric poskytuje vývojářům možnost psát služby, které se spouštějí na nespolehlivých infrastrukturách. Aby bylo možné zapisovat vysoce kvalitní služby, vývojáři musí být schopni tuto nespolehlivou infrastrukturu vyvolávat, aby otestovali stabilitu svých služeb.

Služba analýzy chyb dává vývojářům možnost přimět akce při selhání do testovacích služeb v případě výskytu selhání. Cílené simulované chyby ale budou mít jenom to daleko. Chcete-li provést testování dále, můžete použít testovací scénáře v Service Fabric: test chaos a test převzetí služeb při selhání. Tyto scénáře simulují průběžné i nedarované chyby v celém clusteru po delší dobu. Jakmile je test nakonfigurován s četností a typem chyb, může být spuštěn prostřednictvím rozhraní API jazyka C# nebo PowerShellu za účelem generování chyb v clusteru a službě.

> [!WARNING]
> ChaosTestScenario se nahrazuje pružnou Chaosou založenou na službách. Další podrobnosti najdete v novém článku s [chaos řízeným](service-fabric-controlled-chaos.md) článkem.
> 
> 

## <a name="chaos-test"></a>Chaos test
Scénář chaos generuje chyby napříč celým clusterem Service Fabric. Scénář komprimuje chyby obecně v měsících nebo rocích až na pár hodin. Kombinace vzájemně vykládaných chyb s vysokou mírou chyb vyhledá rohové případy, které se jinak neobjeví. To vede k výraznému zlepšení kvality kódu služby.

### <a name="faults-simulated-in-the-chaos-test"></a>Chyby simulované v testu chaos
* Restartování uzlu
* Restartování nasazeného balíčku kódu
* Odebrání repliky
* Restartování repliky
* Přesunutí primární repliky (volitelné)
* Přesunutí sekundární repliky (volitelné)

Test chaos spustí v zadaném časovém období několik iterací chyb a ověření clusteru. Je možné nastavit i čas strávený při stabilizaci clusteru a pro úspěšné ověření. Scénář selže, když dojde k jedné chybě při ověřování clusteru.

Představte si třeba, že sada testů bude běžet po dobu jedné hodiny s maximálně třemi souběžnými chybami. Test provede tři chyby a potom ověří stav clusteru. Test projde předchozí krok do chvíle, kdy se cluster přestane nacházet v pořádku nebo v průběhu jedné hodiny. Pokud cluster v jakékoli iteraci přestane být v pořádku, to znamená, že se nestabilizovat v nakonfigurovaném čase, test se nezdaří s výjimkou. Tato výjimka znamená, že se něco pokazilo a potřebuje další šetření.

V jeho současné podobě modul generování chyb v testu chaos vystavuje jenom bezpečné chyby. To znamená, že při absenci externích chyb nedojde k žádnému kvoru nebo ztrátě dat.

### <a name="important-configuration-options"></a>Důležité možnosti konfigurace
* **TimeToRun**: celková doba, kterou test spustí před dokončením s úspěchem. Test může skončit dříve než selhání ověření.
* **MaxClusterStabilizationTimeout**: maximální doba, po kterou se má čekat, než se cluster před selháním testu stane v pořádku. Provedené kontroly jsou bez ohledu na to, zda je stav clusteru OK, stav služby je v pořádku, je dosaženo cílové velikosti sady replik pro oddíl služby a žádné repliky inbuildu neexistují.
* **MaxConcurrentFaults**: maximální počet souběžných chyb vyvolaných v každé iteraci. Čím vyšší je číslo, tím lépe agresivní test, což vede k složitějším převzetí služeb při selhání a přechodům. Test zaručuje, že v případě nepřítomnosti externích chyb nebude k dispozici kvorum ani ztráta dat bez ohledu na to, jak vysoká je tato konfigurace.
* **EnableMoveReplicaFaults**: povolí nebo zakáže chyby, které způsobují přesunutí primárních nebo sekundárních replik. Ve výchozím nastavení jsou tyto chyby zakázané.
* **WaitTimeBetweenIterations**: množství času, které se má čekat mezi iteracemi, tj. po zaokrouhlení chyb a odpovídajícím ověření.

### <a name="how-to-run-the-chaos-test"></a>Jak spustit test chaos
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

Modul Service Fabric PowerShell obsahuje dva způsoby, jak začít scénář chaos. `Invoke-ServiceFabricChaosTestScenario` je založen na klientech a pokud je klientský počítač vypínán prostřednictvím testu, nebudou zavedeny žádné další chyby. Alternativně je k dispozici sada příkazů, které mají za následek, že je test spuštěn v případě vypnutí počítače. `Start-ServiceFabricChaos` používá stavovou a spolehlivou systémovou službu nazvanou FaultAnalysisService a zajišťuje, že chyby zůstanou zavedeny až do chvíle, kdy TimeToRun nebude. `Stop-ServiceFabricChaos` dá se použít k ručnímu zastavení scénáře a obdrží `Get-ServiceFabricChaosReport` sestavu. Další informace najdete v tématu [Reference k Azure Service Fabric PowerShellu](/powershell/module/ServiceFabric/New-ServiceFabricService?view=azureservicefabricps) a vystavování [chaos řízených v clusterech Service Fabric](service-fabric-controlled-chaos.md).

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
Scénář testu převzetí služeb při selhání je verze scénáře testu chaos, který cílí na konkrétní oddíl služby. Testuje účinek převzetí služeb při selhání u konkrétního oddílu služby, zatímco ostatní služby zůstanou beze změn. Po nakonfigurování s informacemi o cílovém oddílu a dalších parametrech se spustí jako nástroj na straně klienta, který používá rozhraní API jazyka C# nebo PowerShell k vygenerování chyb pro oddíl služby. Scénář projde sekvencí simulovaných chyb a ověření služby, zatímco vaše obchodní logika běží na straně, aby poskytovala úlohu. Selhání při ověřování služby indikuje problém, který potřebuje další šetření.

### <a name="faults-simulated-in-the-failover-test"></a>Chyby simulované v rámci testu převzetí služeb při selhání
* Restartujte nasazený balíček kódu, kde je oddíl hostovaný.
* Odebrat primární nebo sekundární repliku nebo bezstavovou instanci
* Restartování primární sekundární repliky (Pokud trvalá služba)
* Přesunout primární repliku
* Přesunutí sekundární repliky
* Restartovat oddíl

Test převzetí služeb při selhání vyzkouší zvolenou chybu a potom spustí ověření služby, aby se zajistila její stabilita. Test převzetí služeb při selhání vyzkouší jenom jednu chybu v jednom okamžiku, a to na rozdíl od možného více chyb v testu chaos. Pokud oddíl služby není stabilizován v nakonfigurovaném časovém limitu po každé chybě, test se nezdařil. Test vyzkouší pouze bezpečné chyby. To znamená, že při neexistenci externích selhání nedojde k kvoru nebo ztrátě dat.

### <a name="important-configuration-options"></a>Důležité možnosti konfigurace
* **PartitionSelector**: objekt Selector určující oddíl, který je třeba cílit.
* **TimeToRun**: celková doba, kterou test spustí před dokončením.
* **MaxServiceStabilizationTimeout**: maximální doba, po kterou se má čekat, než se cluster před selháním testu stane v pořádku. Provedené kontroly jsou bez ohledu na to, jestli je stav služby v pořádku, velikost cílové sady repliky se dosáhne pro všechny oddíly a žádné repliky inbuildu neexistují.
* **WaitTimeBetweenFaults**: doba čekání mezi každým selháním a cyklem ověřování.

### <a name="how-to-run-the-failover-test"></a>Jak spustit test převzetí služeb při selhání
**C#**

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
