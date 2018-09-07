---
title: Vytvořit testy chaos a převzetí služeb při selhání pro Azure Service Fabric | Dokumentace Microsoftu
description: Pomocí Service Fabric chaos testování a převzetí služeb při selhání otestování scénářů vyvolat chyby a ověření spolehlivost vašich služeb.
services: service-fabric
documentationcenter: .net
author: motanv
manager: rsinha
editor: toddabel
ms.assetid: 8eee7e89-404a-4605-8f00-7e4d4fb17553
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2017
ms.author: motanv
ms.openlocfilehash: d12c5097d4ba5e0ccfe0e2b2cbc8ccd758c32d98
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44051285"
---
# <a name="testability-scenarios"></a>Scénářů testovatelnosti
Velkých distribuovaných systémech jako cloudových infrastruktur jsou ze své podstaty nespolehlivé. Azure Service Fabric umožňuje vývojářům zápis služeb pro spuštění nad rámec nespolehlivé infrastruktury. Chcete-li vysoké kvality služeb, vývojáři musí mít možnost vyvolat takovou nespolehlivé infrastrukturu k testování stability svých služeb.

Fault Analysis Service umožňuje vývojářům způsobit selhání akce k testování služeb v případě chyb. Ale cílové simulované chyby přináší pouze zatím. Provádět testování další, můžete použít v Service Fabric scénáře testování: chaos testu a testovací převzetí služeb při selhání. Tyto scénáře simulace průběžné prokládané chyb, bezproblémové a vynuceném v rámci clusteru za dlouhou dobu. Test se nakonfigurují frekvence a typ chyby, umožní spuštění prostřednictvím ke generování chyb v clusteru a vaše služba API jazyka C# nebo prostředí PowerShell.

> [!WARNING]
> Teď nahrazuje ChaosTestScenario odolnější, založené na službě Chaos. Nové článku [řízený Chaos](service-fabric-controlled-chaos.md) další podrobnosti.
> 
> 

## <a name="chaos-test"></a>Chaos testu
Scénář chaos generuje chyby napříč celý cluster Service Fabric. Scénář komprimuje chyby obvykle vidět měsíců nebo let na několik hodin. Kombinace prokládané chyb s vysokou odolnost rychlost vyhledá krajní případy, které jsou jinak provedena. To vede k významné zvýšení kvality kódu služby.

### <a name="faults-simulated-in-the-chaos-test"></a>Simulovat v testu chaos chyb
* Restartovat uzel
* Restartujte balíček nasazenému kódu
* Odstranění repliky
* Restartujte na repliku
* Přesunutí primární repliky (volitelné)
* Přesunout na sekundární repliku (volitelné)

Chaos testovací běhy více iterací chyb a cluster ověření pro zadané časové období. Čas strávený clusteru stabilizovat a pro ověření úspěšné je také možné konfigurovat. Scénář selže, pokud dosáhnete jednoho selhání ověření clusteru.

Představte si třeba test nastavena pro spuštění na jednu hodinu s délkou maximálně tři souběžných chyb. Test bude zahájit tři chyby a potom ověřte stav clusteru. Test bude iteraci v rámci předchozího kroku do clusteru nebude v pořádku nebo předá jednu hodinu. Pokud cluster nebude v pořádku v kteroukoli iteraci, tedy ne stabilizaci v nakonfigurovaném čase, test se nezdaří s výjimkou. Tato výjimka označuje, že něco se pokazilo a vyžaduje další šetření.

V současné podobě modul pro generování selhání při testu chaos indukuje pouze bezpečné chyb. To znamená, že chybí externí chyb, ke ztrátě kvora nebo data nikdy nedojde.

### <a name="important-configuration-options"></a>Důležité konfigurační možnosti
* **TimeToRun**: celkový čas, který test bude spuštěn před dokončením s úspěchem. Test můžete dokončit dříve namísto selhání ověření.
* **MaxClusterStabilizationTimeout**: maximální dobu čekání na cluster tak, aby se obnoví dobrý stav před selháním testu. Provádí kontroly se určuje, zda stav clusteru je v pořádku, stav služby je v pořádku, velikost cílové sady replik je dosaženo oddílu služby a neexistuje žádná replik InBuild.
* **MaxConcurrentFaults**: maximální počet souběžných chyb vyvolaných v každé iteraci. Větší číslo, mnohem vyššími testu, takže výsledkem je složitější převzetí služeb při selhání a přechod kombinace. Test zaručuje, že v absence externí chyb, nebude existovat kvora nebo ztrátu, bez ohledu na to, jak vysoká je tato konfigurace.
* **EnableMoveReplicaFaults**: Povolí nebo zakáže chyb, které jsou příčinou přesunu primární nebo sekundární repliky. Tyto chyby jsou ve výchozím nastavení zakázané.
* **WaitTimeBetweenIterations**: dobu čekat mezi iteracemi, např. po kruhové chyb a odpovídající ověření.

### <a name="how-to-run-the-chaos-test"></a>Jak spustit testovací chaosu
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

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```


## <a name="failover-test"></a>Testovací převzetí služeb při selhání
Scénáře testovacího převzetí služeb při selhání je verze chaos testovací scénář, který cílí na konkrétní službu oddílu. Ověřuje vliv převzetí služeb při selhání na oddíl konkrétní službu a ponechání ostatním službám to neovlivní. Jakmile se nakonfigurují informace o cílovém oddílu a dalších parametrů, poběží brána jako nástroj na straně klienta, který používá rozhraní API jazyka C# nebo prostředí PowerShell k vygenerování chyby pro oddíl služby. Scénář prochází posloupnost simulované chyb a ověření služby průběhu obchodní logiky na straně poskytnout úlohy. Chyba při ověřování služby naznačuje problém, který vyžaduje další šetření.

### <a name="faults-simulated-in-the-failover-test"></a>Chyby s Simulovaná v testu převzetí služeb při selhání
* Restartujte nasazenému kódu balíček, který je hostitelem oddílu
* Odebrání primární nebo sekundární repliky nebo bezstavových instance
* Restartujte primární sekundární repliky (je-li trvalé služby)
* Přesunutí primární repliky
* Přesunout na sekundární repliku
* Restartujte oddíl

Testovací převzetí služeb při selhání indukuje zvolené chybu a pak spustí ověření ve službě k zajištění jeho stabilitu. Testovací převzetí služeb při selhání indukuje jenom jedna chyba najednou, možná na rozdíl od více chyb v testu chaos. Pokud oddíl služby není stabilizaci v nakonfigurovaném časovém limitu po každé chyby, test se nezdaří. Test indukuje pouze bezpečné chyb. To znamená, neexistence externí selhání nedojde ke ztrátě kvora nebo data.

### <a name="important-configuration-options"></a>Důležité konfigurační možnosti
* **PartitionSelector**: selektoru objektu, který určuje oddíl, který je potřeba cílit.
* **TimeToRun**: celkový čas, který test bude spuštěn před dokončením.
* **MaxServiceStabilizationTimeout**: maximální dobu čekání na cluster tak, aby se obnoví dobrý stav před selháním testu. Provádí kontroly se určuje, zda je v pořádku stav služby, velikost cílové sady replik je dosaženo pro všechny oddíly a neexistují žádné replik InBuild.
* **WaitTimeBetweenFaults**: dobu čekání mezi každý cyklus selhání a ověření.

### <a name="how-to-run-the-failover-test"></a>Jak spustit testovací převzetí služeb při selhání
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
