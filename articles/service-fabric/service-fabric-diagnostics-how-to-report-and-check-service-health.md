---
title: Hlášení a kontrola stavu pomocí Azure Service Fabric | Dokumentace Microsoftu
description: Zjistěte, jak zasílání zpráv o stavu z vašeho kódu služby a o tom, aby zkontroloval stav vaší služby pomocí nástrojů pro monitorování stavu, které poskytuje Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: mfussell
editor: ''
ms.assetid: 7c712c22-d333-44bc-b837-d0b3603d9da8
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/2/2017
ms.author: dekapur
ms.openlocfilehash: d374886efb708797db1dd6352aa063a56aff4f44
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39427304"
---
# <a name="report-and-check-service-health"></a>Hlášení a kontrola stavu služeb
Když vaše služby dojde k potížím, schopnost reagovat na a vyřešit incidenty a výpadků a Dosáhněte závisí na vaši schopnost rychle rozpoznat problémy. Pokud hlásit problémy a chyby do nástroje health manager Azure Service Fabric z vašeho kódu služby můžete použít standardní nástroje, které Service Fabric nabízí ke kontrole stavu pro monitorování stavu.

Existují tři způsoby, můžete odesílat zprávy o stavu ze služby:

* Použití [oddílu](https://docs.microsoft.com/dotnet/api/system.fabric.istatefulservicepartition) nebo [CodePackageActivationContext](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext) objekty.  
  Můžete použít `Partition` a `CodePackageActivationContext` objekty hlášení stavu prvky, které jsou součástí aktuálního kontextu. Například kód, který běží jako část repliky může hlásit stav jenom u repliky, oddílu, který patří do a aplikace, která je součástí.
* Použití `FabricClient`.   
  Můžete použít `FabricClient` sestavy stav z kódu služby clusteru není-li [zabezpečené](service-fabric-cluster-security.md) nebo pokud je služba spuštěna s oprávněním správce. Většina scénářů reálného světa nepoužívejte nezabezpečené clustery, nebo zadejte oprávnění správce. S `FabricClient`, stavu může podávat Každá entita, která je součástí clusteru. V ideálním případě by však kódu služby by měl pouze odesílat zprávy, které se vztahují na svůj vlastní stav.
* Pomocí rozhraní REST API v clusteru, aplikace, nasazené aplikace, služby, balíček služby, oddílu, repliky nebo uzel úrovně. To je možné nahlásit stav z v rámci kontejneru.

Tento článek vás provede příkladem hlásí stav z kódu služby. Příklad také ukazuje, jak lze pomocí nástroje poskytované systémem Service Fabric kontroluje stav. Tento článek je určený jako rychlý úvod k monitorování funkce Service Fabric stavu. Podrobnější informace si můžete přečíst řadu podrobné články o stavu, které začínají s odkazem na konci tohoto článku.

## <a name="prerequisites"></a>Požadavky
Musíte mít nainstalované tyto položky:

* Visual Studio 2015 nebo Visual Studio 2017
* Sada Service Fabric SDK

## <a name="to-create-a-local-secure-dev-cluster"></a>Chcete-li vytvořit zabezpečené místním vývojovém clusteru
* Otevřete PowerShell s oprávněními správce a spusťte následující příkazy:

![Příkazy, které ukazují, jak vytvořit cluster zabezpečený vývoj](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## <a name="to-deploy-an-application-and-check-its-health"></a>Nasazení aplikace a zkontrolujte jeho stav
1. Otevřít Visual Studio jako správce.
1. Vytvořte projekt pomocí **stavová služba** šablony.
   
    ![Vytvoření aplikace Service Fabric s stavová služba](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)
1. Stisknutím klávesy **F5** ke spuštění aplikace v režimu ladění. Aplikace je nasazená do místního clusteru.
1. Jakmile je aplikace spuštěna, klikněte pravým tlačítkem na ikonu Local Cluster Manager v oznamovací oblasti a vyberte **Správa místního clusteru** v místní nabídce otevřete Service Fabric Explorer.
   
    ![Otevřete Service Fabric Explorer z oznamovací oblasti](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)
1. Stav aplikace má být zobrazena jako v tomto obrázku. V tuto chvíli by aplikace měla být v pořádku bez chyb.
   
    ![V dobrém stavu aplikace v Service Fabric Exploreru](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)
1. Stav můžete zkontrolovat také pomocí prostředí PowerShell. Můžete použít ```Get-ServiceFabricApplicationHealth``` ke kontrole stavu aplikace a vy můžete použít ```Get-ServiceFabricServiceHealth``` ke kontrole stavu služby. Sestava stavu pro stejnou aplikaci v prostředí PowerShell je na tomto obrázku.
   
    ![V dobrém stavu aplikace v prostředí PowerShell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## <a name="to-add-custom-health-events-to-your-service-code"></a>Přidání vlastních stavových události do vašeho kódu služby
Šablony projektu Service Fabric v sadě Visual Studio obsahují ukázkový kód. Následující kroky ukazují, jak můžete ohlásit vlastní stav události z kódu vaší služby. Tyto zprávy se nezobrazí automaticky ve standardní nástroje pro monitorování stavu, že Service Fabric nabízí, jako je Service Fabric Explorer, zobrazení stavu Azure portal a PowerShell.

1. Znovu otevřete aplikaci, kterou jste vytvořili dříve v sadě Visual Studio, nebo vytvořte novou aplikaci s použitím **stavová služba** šablony sady Visual Studio.
1. Otevřete soubor Stateful1.cs a najít `myDictionary.TryGetValueAsync` volání v `RunAsync` metody. Uvidíte, že tato metoda vrátí hodnotu `result` , který obsahuje aktuální hodnotu čítače, protože klíče logika v této aplikaci je udržovat počtu spuštění. Pokud to bylo skutečné aplikace, a chybějící výsledek selhání, chcete označit tuto událost.
1. Oznamuje událost stavu Pokud chybějící výsledek představuje selhání, přidejte následující kroky.
   
    a. Přidat `System.Fabric.Health` oboru názvů do souboru Stateful1.cs.
   
    ```csharp
    using System.Fabric.Health;
    ```
   
    b. Přidejte následující kód za `myDictionary.TryGetValueAsync` volání
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
    Vytvoříme sestavy stavu repliky, protože je hlášena z stavové služby. `HealthInformation` Parametr ukládá informace o tohoto problému se stavem, která hlásí.
   
    Pokud jste vytvořili bezstavovou službu, použijte následující kód
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```
1. Pokud vaše služba je spuštěná s oprávněními správce, nebo pokud clusteru není [zabezpečené](service-fabric-cluster-security.md), můžete použít také `FabricClient` do sestav stavu, jak je znázorněno v následujícím postupu.  
   
    a. Vytvořte `FabricClient` instance po `var myDictionary` deklarace.
   
    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```
   
    b. Přidejte následující kód za `myDictionary.TryGetValueAsync` volání.
   
    ```csharp
    if (!result.HasValue)
    {
       var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.Context.PartitionId,
            this.Context.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```
1. Můžeme simulovat selhání a zobrazit jej zobrazit v nástrojích pro monitorování stavu. K simulaci selhání, okomentujte první řádek ve stavu vytváření sestav kód, který jste přidali dříve. Po okomentujte první řádek kódu bude vypadat jako v následujícím příkladu.
   
    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
   Tento kód aktivuje pokaždé, když se sestava stavu `RunAsync` spustí. Když provedete změny, stiskněte klávesu **F5** ke spuštění aplikace.
1. Jakmile je aplikace spuštěna, otevřete Service Fabric Exploreru zkontrolujte stav aplikace. Tentokrát, Service Fabric Explorer ukazuje, že aplikace je v pořádku. Toto je z důvodu chyby, která byla nahlášena z kódu jsme přidali dříve.
   
    ![Poškozená aplikace v Service Fabric Exploreru](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)
1. Pokud vyberete primární repliky ve stromovém zobrazení Service Fabric Explorer, zobrazí se, která **stav** příliš označuje chybu. Service Fabric Explorer také zobrazí podrobnosti o stavu sestavy, které jste přidali `HealthInformation` parametru v kódu. Zobrazí se stejné sestavy stavu v prostředí PowerShell a webu Azure portal.
   
    ![Stav repliky v Service Fabric Exploreru](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Tato sestava zůstane v nástroje health manager, dokud je nahrazen jiné sestavy nebo dokud nebude tato replika se odstraní. Protože jsme nenastavil `TimeToLive` této sestavy stavu v `HealthInformation` objektu sestavy nikdy nevyprší.

Doporučujeme vám, že by měl na nejpodrobnější úrovni, které v tomto případě je replika hlásí stav. Může také nahlásit stav na `Partition`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

Sestava Stav na `Application`, `DeployedApplication`, a `DeployedServicePackage`, použijte `CodePackageActivationContext`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## <a name="next-steps"></a>Další postup
* [Podrobné informace o službě health Service Fabric](service-fabric-health-introduction.md)
* [Rozhraní REST API pro generování sestav stavu služby](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-service)
* [Rozhraní REST API pro generování sestav stavu aplikace](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-an-application)

