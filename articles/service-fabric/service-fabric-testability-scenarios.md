---
title: Vytváření testů chaos a převzetí služeb při selhání pro Azure mikroslužeb | Microsoft Docs
description: Pomocí Service Fabric chaos test a převzetí služeb při selhání testovací scénáře vyvolat chyb a ověřte spolehlivost vašich služeb.
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
ms.openlocfilehash: d9c05ba2b98af5ef26ef5b5a7ae0995512df4e75
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="testability-scenarios"></a>Testovatelnosti scénáře
Velkých distribuovaných systémech stejně, jako jsou ze své podstaty nespolehlivé cloudových infrastruktur. Azure Service Fabric nabízí vývojářům možnost zapisovat spuštění nad nespolehlivé infrastruktury služeb. Chcete-li potřebovat vývojáři mohli vyvolat takovou nespolehlivé infrastrukturu k testování stabilitu své služby zápisu vysoce kvalitních služeb.

Služba Analysis Service odolnost poskytuje vývojářům možnost způsobit selhání akce pro testování služeb v případě selhání. Ale cílové simulované chyb získáte pouze, pokud. Chcete-li provádět testování další, můžete použít testovací scénáře v Service Fabric: testovací chaos a testovací převzetí služeb při selhání. Tyto scénáře simulovat průběžné prokládaná chyb, řádně a vynuceném v rámci clusteru přes dlouhou dobu. Jakmile testu je nakonfigurovaný s rychlost a druh chyb, může být spuštěn prostřednictvím rozhraní API jazyka C# nebo prostředí PowerShell ke generování chyb v clusteru a služby.

> [!WARNING]
> ChaosTestScenario je nahrazován Chaos pružnější, na základě služeb. Přečtěte nový článek na [řídí Chaos](service-fabric-controlled-chaos.md) další podrobnosti.
> 
> 

## <a name="chaos-test"></a>Chaos testu
Scénář chaos generuje chyby napříč celý cluster Service Fabric. Tento scénář komprimaci chyb obecně zobrazená v měsíců nebo let několik hodin. Kombinace prokládaná chyb s vysokou odolnost rychlost vyhledá náročnějších případech, které jsou jinak vynechat. To vede k významné zlepšení kvality kódu služby.

### <a name="faults-simulated-in-the-chaos-test"></a>Simulated v testu chaos chyb
* Restartovat uzel
* Restartujte nasazený balíček kódu
* Odstranění repliky
* Restartujte repliky
* Přesunutí primární repliky (volitelné)
* Přesunutí sekundární repliky (volitelné)

Chaos testovací běhy více iterací chyb a ověření clusteru pro zadané časové období. Čas strávený clusteru stabilizovat a pro ověření, který má být úspěšné, je také možné konfigurovat. Tento scénář selže, když dosáhl jediné chyby v ověření clusteru.

Představte si třeba testu nastaven na spouštění hodinu s maximálně tři souběžných chyb. Test se vyvolat tři chyb a pak ověřte stav clusteru. Test bude iterovat v předchozím kroku, dokud clusteru se změní na není v pořádku nebo předá jednu hodinu. Pokud cluster se změní na není v pořádku v kteroukoli iteraci, tj. není stabilizaci v nakonfigurovaném čase, test se nezdaří s výjimkou. Tato výjimka označuje, že něco nepovede a potřebuje další šetření.

V současné podobě modul selhání generace v testu chaos indukuje pouze bezpečné chyb. To znamená, že chybí externí chyb, ke ztrátě kvora nebo data nikdy nedojde.

### <a name="important-configuration-options"></a>Důležité konfigurační možnosti
* **TimeToRun**: celkový čas, který se test spustí před dokončením instalace se. Test můžete dokončit dříve místo selhání ověření.
* **MaxClusterStabilizationTimeout**: maximální množství času čekání na clusteru se nezotavila před selháním test. Provést kontroly se, zda stav clusteru je v pořádku, stav služby je v pořádku, oddílu služby se dá dosáhnout velikost cílové sady replik a neexistují žádné replik InBuild.
* **MaxConcurrentFaults**: maximální počet souběžných chyb vyvolané v každé iteraci. Čím vyšší číslo, agresivnější test, proto výsledkem složitější převzetí služeb při selhání a kombinace přechodu. Test zaručuje, že neexistence externí chyb nebude existovat kvora nebo ztráty dat, bez ohledu na to, jak vysoké tato konfigurace je.
* **EnableMoveReplicaFaults**: Povolí nebo zakáže chyb, které způsobují přesun primární nebo sekundární repliky. Tyto chyby jsou ve výchozím nastavení zakázány.
* **WaitTimeBetweenIterations**: dobu čekání mezi iterací, např. po zaokrouhlit chyb a odpovídající ověření.

### <a name="how-to-run-the-chaos-test"></a>Postup spuštění testu chaos
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
Scénáře testovacího převzetí služeb při selhání je verze chaos testovací scénář, který cílí na konkrétní službu oddíl. Testy vliv převzetí služeb při selhání na oddíl konkrétní službu, a nechat neovlivní jiným službám. Jakmile je nakonfigurován s informace o cílové oddílu a dalších parametrů, spouští se jako nástroj na straně klienta, který používá rozhraní API jazyka C# nebo prostředí PowerShell ke generování chyb pro oddíl služby. Tento scénář iteruje posloupnost simulované chyb a ověření služby průběhu obchodní logiky na straně zajistit zatížení. Chyby při ověřování služby označuje potíže, které potřebuje další šetření.

### <a name="faults-simulated-in-the-failover-test"></a>Chyby simulated v testu převzetí služeb při selhání
* Restartujte nasazený balíček kódu je hostitelem oddílu
* Odebrat primární a sekundární repliky nebo bezstavové instance
* Restartujte primární sekundární repliky (Pokud trvalou služba)
* Přesunutí primární repliky
* Přesunutí sekundární repliky
* Restartujte oddílu

Testovací převzetí služeb při selhání indukuje zvolené chybu a pak spustí ověřování na službě, abyste zajistili její stability. Testovací převzetí služeb při selhání indukuje pouze jeden selhání současně, a možné několik chyb v chaos test. Pokud služba oddílu není stabilizaci s nakonfigurovaným časovým limitem po každé selhání, test se nezdaří. Test indukuje pouze bezpečné chyb. To znamená, existovat externí selhání, nedojde ke ztrátě kvora nebo data.

### <a name="important-configuration-options"></a>Důležité konfigurační možnosti
* **Partitionselector nejde**: výběr objektu, který určuje oddílu, který musí být cílem.
* **TimeToRun**: celkový čas, který se test spustí před dokončením instalace.
* **MaxServiceStabilizationTimeout**: maximální množství času čekání na clusteru se nezotavila před selháním test. Provést kontroly se, jestli stav služby je v pořádku, velikost cílové sady replik je dosaženo pro všechny oddíly a neexistují žádné replik InBuild.
* **WaitTimeBetweenFaults**: dobu čekání mezi každý cyklus selhání a ověření.

### <a name="how-to-run-the-failover-test"></a>Postup spuštění testu převzetí služeb při selhání
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
