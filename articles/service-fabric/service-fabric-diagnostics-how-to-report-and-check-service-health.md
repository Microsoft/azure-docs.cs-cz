---
title: Vytvoření sestavy a zkontrolujte stav s Azure Service Fabric | Microsoft Docs
description: Zjistěte, jak chcete zasílat zprávy o stavu z vašeho kódu služby a jak zkontrolovat stav služby pomocí nástroje pro sledování stavu, které poskytuje Azure Service Fabric.
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
ms.openlocfilehash: 82ee3cbca40713d527f64ae4698cb9ce64a10215
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="report-and-check-service-health"></a>Hlášení a kontrola stavu služeb
Pokud vaše služby dojde k potížím, budete moci reagovat na a opravte incidentů a výpadky závisí na budete moci rychle zjistit problémy. Pokud hlášení problémů a selhání do Azure Service Fabric správce stavu z vašeho kódu služby můžete použít standardní stavu monitorování nástroje, které Service Fabric nabízí zkontrolovat stav.

Existují tři způsoby, můžete odesílat zprávy o stavu ze služby:

* Použití [oddílu](https://docs.microsoft.com/dotnet/api/system.fabric.istatefulservicepartition) nebo [CodePackageActivationContext](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext) objekty.  
  Můžete použít `Partition` a `CodePackageActivationContext` objekty hlášení stavu elementů, které jsou součástí aktuální kontext. Například kód, který spouští jako součást repliku můžete sestavy stavu pouze repliky, oddílu, který patří do a aplikace, která je součástí.
* Použití `FabricClient`.   
  Můžete použít `FabricClient` stav sestavy z kódu služby clusteru, není-li [zabezpečené](service-fabric-cluster-security.md) nebo pokud je služba spuštěna s oprávněními správce. Většina scénářích reálného světa nepoužívejte zabezpečená clustery, nebo zadejte oprávnění správce. S `FabricClient`, stavu může podávat Každá entita, která je součástí clusteru. V ideálním případě by však kódu služby by měl odesílat pouze sestavy, které se vztahují k vlastní stavu.
* Pomocí rozhraní API REST v clusteru, aplikace, nasazení aplikace, služby, balíček služby, oddíl, repliky nebo úrovně uzlu. To může používají k hlášení stavu z do kontejneru.

Tento článek vás provede příklad, který hlásí stav z kódu služby. Tento příklad také ukazuje, jak lze pomocí nástroje poskytované subsystémem pro Service Fabric zkontrolujte stav. Tento článek je určený jako rychlý úvod do stavu možnosti Service Fabric monitorování. Podrobnější informace si můžete přečíst řadu podrobných článků o stavu, které začínají na odkaz na konci tohoto článku.

## <a name="prerequisites"></a>Požadavky
Musíte mít nainstalované tyto položky:

* Visual Studio 2015 nebo Visual Studio 2017
* Service Fabric SDK

## <a name="to-create-a-local-secure-dev-cluster"></a>K vytvoření clusteru místní zabezpečené vývojářů
* Otevřete prostředí PowerShell s oprávněními správce a spusťte následující příkazy:

![Příkazy, které ukazují, jak vytvořit cluster zabezpečené vývojářů](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## <a name="to-deploy-an-application-and-check-its-health"></a>Pokud chcete nasadit aplikaci a zkontrolovat jeho stav
1. Otevřete Visual Studio jako správce.
2. Vytvoření projektu pomocí **stavové služby** šablony.
   
    ![Vytvoření aplikace Service Fabric pomocí stavové služby](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)
3. Stiskněte klávesu **F5** ke spuštění aplikace v režimu ladění. Aplikace je nasazený na místním clusteru.
4. Jakmile je aplikace spuštěna, klikněte pravým tlačítkem na ikonu Local Cluster Manager v oznamovací oblasti a vyberte **spravovat místní Cluster** z místní nabídky k otevření Service Fabric Exploreru.
   
    ![Otevření Service Fabric Explorer z oznamovací oblasti](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)
5. Stav aplikace má být zobrazena jako tuto bitovou kopii. V tomto okamžiku by aplikace měla být v pořádku bez chyb.
   
    ![V pořádku aplikace v Service Fabric Exploreru](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)
6. Můžete také zkontrolovat stav pomocí prostředí PowerShell. Můžete použít ```Get-ServiceFabricApplicationHealth``` ke kontrole stavu aplikace a vy můžete použít ```Get-ServiceFabricServiceHealth``` ke kontrole stavu služby. Sestava stavu pro stejnou aplikaci v prostředí PowerShell je na tomto obrázku.
   
    ![V pořádku aplikace v prostředí PowerShell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## <a name="to-add-custom-health-events-to-your-service-code"></a>Přidání události vlastní stavu do kódu služby
Šablony projektů Service Fabric v sadě Visual Studio obsahovat ukázkový kód. Následující postup ukazuje, jak může hlásit události vlastní stavu z kódu vaší služby. Tyto zprávy zobrazí automaticky v standardní nástroje pro sledování stavu, že poskytuje Service Fabric, jako je Service Fabric Exploreru, zobrazení stavu Azure portálu a prostředí PowerShell.

1. Otevřete aplikaci, která jste předtím vytvořili v sadě Visual Studio, nebo vytvořit novou aplikaci pomocí **stavové služby** šablony sady Visual Studio.
2. Otevřete soubor Stateful1.cs a najít `myDictionary.TryGetValueAsync` volání v `RunAsync` metoda. Uvidíte, že tato metoda vrátí hodnotu `result` , protože logice klíče v této aplikaci je udržovat počet spuštění udržuje aktuální hodnotu čítače. Pokud to byly reálné aplikaci a nedostatek výsledek určený k selhání, je vhodné příznak tuto událost.
3. K hlášení o stavu událost při chybějícím výsledek představuje selhání, přidáte následující kroky.
   
    a. Přidat `System.Fabric.Health` oboru názvů do souboru Stateful1.cs.
   
    ```csharp
    using System.Fabric.Health;
    ```
   
    b. Přidejte následující kód po `myDictionary.TryGetValueAsync` volání
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
    Vytvoříme sestavy stavu repliky, protože je právě nahlásila stavové služby. `HealthInformation` Parametr ukládá informace o stavu problém, který je hlášena.
   
    Pokud jste vytvořili bezstavové služby, použijte následující kód
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```
4. Pokud vaše služba je spuštěn s oprávněními správce, nebo pokud clusteru není [zabezpečené](service-fabric-cluster-security.md), můžete použít také `FabricClient` stav sestavy, jak je znázorněno v následujícím postupu.  
   
    a. Vytvořte `FabricClient` instance po `var myDictionary` deklarace.
   
    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```
   
    b. Přidejte následující kód po `myDictionary.TryGetValueAsync` volání.
   
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
5. Pojďme simulovat tuto chybu, abyste viděli, zobrazí v nástroje pro sledování stavu. Simulace selhání komentář v prvním řádku kódu generování sestav stavu, které jste přidali dříve. Po komentář první řádek kód bude vypadat jako v následujícím příkladu.
   
    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
   Tento kód sestava stavu aktivuje se pokaždé, když `RunAsync` provede. Po provedení změny, stiskněte klávesu **F5** ke spuštění aplikace.
6. Jakmile je aplikace spuštěna, otevřete Service Fabric Explorer ke kontrole stavu aplikace. Tentokrát Service Fabric Explorer ukazuje, že aplikace je není v pořádku. To je kvůli chybě, která byla nahlášena z kódu jsme přidali dříve.
   
    ![Není v pořádku aplikace v Service Fabric Exploreru](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)
7. Pokud vyberete primární repliky v zobrazení stromu Service Fabric Explorer, zobrazí se **stav** příliš označuje k chybě. Také zobrazuje podrobnosti o stavu sestavy, které byly přidány do Service Fabric Explorer `HealthInformation` parametr v kódu. Zobrazí se stejné sestavy stavu v prostředí PowerShell a portálu Azure.
   
    ![Stav repliky v Service Fabric Exploreru](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Tato sestava zůstane v správce stavu, dokud je nahrazen jinou sestavu nebo dokud nebude tato replika se odstraní. Protože jsme nenastavili `TimeToLive` pro tuto sestavu stavu v `HealthInformation` objektu sestavy nikdy nevyprší.

Doporučujeme vám, že by měl na nejvíce podrobné úrovni, které v tomto případě je replika hlásí stav. Můžete také sestavy stavu `Partition`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

Stav sestavy na `Application`, `DeployedApplication`, a `DeployedServicePackage`, použijte `CodePackageActivationContext`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## <a name="next-steps"></a>Další postup
* [Podrobné informace o stavu Service Fabric](service-fabric-health-introduction.md)
* [REST API pro vytváření sestav stavu služby](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-service)
* [REST API pro vytváření sestav stavu aplikací](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-an-application)

