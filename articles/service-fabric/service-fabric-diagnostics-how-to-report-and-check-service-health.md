---
title: Sestavte a zkontrolujte stav pomocí Azure Service Fabric
description: Zjistěte, jak odesílat zprávy o stavu z kódu služby a jak zkontrolovat stav vaší služby pomocí nástrojů pro sledování stavu, které poskytuje Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 2b7a9c44a84e3ce15eaec22c8f57bb48f79dae05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464639"
---
# <a name="report-and-check-service-health"></a>Hlášení a kontrola stavu služeb
Když se vaše služby setkají s problémy, vaše schopnost reagovat na incidenty a výpadky a opravit je závisí na vaší schopnosti rychle rozpoznat problémy. Pokud nahlásíte problémy a selhání správce stavu Azure Service Fabric z vašeho kódu služby, můžete použít standardní nástroje pro sledování stavu, které service fabric poskytuje ke kontrole stavu.

Stav ze služby můžete nahlásit třemi způsoby:

* Použijte [objekty Partition](https://docs.microsoft.com/dotnet/api/system.fabric.istatefulservicepartition) nebo [CodePackageActivationContext.](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext)  
  Objekty `Partition` a `CodePackageActivationContext` můžete použít k vykazovat stav prvků, které jsou součástí aktuálního kontextu. Například kód, který běží jako součást repliky můžete hlásit stav pouze na této replice, oddíl, který patří a aplikace, která je součástí.
* Použijte `FabricClient`.   
  Stav můžete `FabricClient` použít k hlášení stavu z kódu služby, pokud cluster není [zabezpečený](service-fabric-cluster-security.md) nebo pokud je služba spuštěna s oprávněními správce. Většina reálných scénářů nepoužívá nezabezpečené clustery ani neposkytuje oprávnění správce. Pomocí `FabricClient`služby můžete vykazovat stav libovolné entity, která je součástí clusteru. V ideálním případě by však kód služby měl odesílat pouze sestavy, které souvisejí s vlastním stavem.
* Použijte rest API na úrovni clusteru, aplikace, nasazené aplikace, služby, balíčku služeb, oddílu, repliky nebo uzlu. To lze použít k hlášení stavu z kontejneru.

Tento článek vás provede příkladem, který hlásí stav z kódu služby. Příklad také ukazuje, jak nástroje poskytované Service Fabric lze použít ke kontrole stavu. Tento článek má být rychlý úvod do funkce monitorování stavu Service Fabric. Podrobnější informace naleznete v sérii podrobných článků o stavu, které začínají odkazem na konci tohoto článku.

## <a name="prerequisites"></a>Požadavky
Musíte mít nainstalované následující:

* Visual Studio 2015 nebo Visual Studio 2019
* Service Fabric SDK

## <a name="to-create-a-local-secure-dev-cluster"></a>Vytvoření místního zabezpečeného dev clusteru
* Otevřete PowerShell s oprávněními správce a spusťte následující příkazy:

![Příkazy, které ukazují, jak vytvořit zabezpečený dev cluster](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## <a name="to-deploy-an-application-and-check-its-health"></a>Nasazení aplikace a kontrola jejího stavu
1. Otevřete Visual Studio jako správce.
1. Vytvořte projekt pomocí šablony **Stavové služby.**
   
    ![Vytvoření aplikace Service Fabric se stavovou službou](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)
1. Stisknutím **klávesy F5** spusťte aplikaci v režimu ladění. Aplikace se nasazuje do místního clusteru.
1. Po spuštění aplikace klepněte pravým tlačítkem myši na ikonu Správce místního clusteru v oznamovací oblasti a v místní nabídce vyberte **Spravovat místní cluster** a otevřete aplikaci Service Fabric Explorer.
   
    ![Otevřít Průzkumníka prostředků infrastruktury služeb z oznamovací oblasti](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)
1. Stav aplikace by měl být zobrazen jako na tomto obrázku. V tomto okamžiku by měla být aplikace v pořádku bez chyb.
   
    ![Aplikace v pořádku v průzkumníku infrastruktury služby](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)
1. Můžete také zkontrolovat stav pomocí prostředí PowerShell. Můžete použít ```Get-ServiceFabricApplicationHealth``` ke kontrole stavu aplikace a můžete ```Get-ServiceFabricServiceHealth``` použít ke kontrole stavu služby. Sestava stavu pro stejnou aplikaci v prostředí PowerShell je v tomto obrázku.
   
    ![Aplikace v pořádku v PowerShellu](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## <a name="to-add-custom-health-events-to-your-service-code"></a>Přidání vlastních událostí stavu do kódu služby
Šablony projektu Service Fabric v sadě Visual Studio obsahují ukázkový kód. Následující kroky ukazují, jak můžete nahlásit vlastní události stavu z kódu služby. Tyto sestavy se automaticky zobrazují ve standardních nástrojích pro monitorování stavu, které poskytuje Service Fabric, jako je service fabric explorer, zobrazení stavu portálu Azure a Prostředí PowerShell.

1. Znovu otevřete aplikaci, kterou jste vytvořili dříve v sadě Visual Studio, nebo vytvořte novou aplikaci pomocí šablony **Stateful Service** Visual Studio.
1. Otevřete soubor Stateful1.cs a `myDictionary.TryGetValueAsync` najděte `RunAsync` volání v metodě. Můžete vidět, že tato `result` metoda vrátí, který obsahuje aktuální hodnotu čítače, protože logika klíče v této aplikaci je zachovat počet spuštěn. Pokud tato aplikace byla skutečná aplikace a nedostatek výsledku představoval selhání, měli byste tuto událost označit příznakem.
1. Chcete-li nahlásit událost stavu, když nedostatek výsledku představuje selhání, přidejte následující kroky.
   
    a. Přidejte `System.Fabric.Health` obor názvů do souboru Stateful1.cs.
   
    ```csharp
    using System.Fabric.Health;
    ```
   
    b. Přidání následujícího kódu `myDictionary.TryGetValueAsync` za volání
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
    Hlásíme stav repliky, protože je hlášena ze stavové služby. Parametr `HealthInformation` ukládá informace o problému se stavem, který je hlášen.
   
    Pokud jste vytvořili bezstavovou službu, použijte následující kód
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```
1. Pokud je vaše služba spuštěna s oprávněními správce nebo pokud `FabricClient` cluster není [zabezpečený](service-fabric-cluster-security.md), můžete také nahlásit stav, jak je znázorněno v následujících krocích.  
   
    a. Vytvořte `FabricClient` instanci po deklaraci. `var myDictionary`
   
    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```
   
    b. Za `myDictionary.TryGetValueAsync` volání přidejte následující kód.
   
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
1. Pojďme simulovat toto selhání a uvidíme, že se zobrazí v nástrojích pro sledování stavu. Chcete-li simulovat selhání, zakomentujte první řádek v kódu vykazování stavu, který jste přidali dříve. Po komentáři z prvního řádku bude kód vypadat jako v následujícím příkladu.
   
    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
   Tento kód vyvolá sestavy `RunAsync` stavu pokaždé, když se spustí. Po provedení změny spusťte aplikaci stisknutím **klávesy F5.**
1. Po spuštění aplikace otevřete Service Fabric Explorer a zkontrolujte stav aplikace. Tentokrát Service Fabric Explorer ukazuje, že aplikace není v pořádku. Aplikace se zobrazí jako není v pořádku, protože chyba, která byla hlášena z kódu, který jsme přidali dříve.
   
    ![Aplikace Není v pořádku v aplikaci Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)
1. Pokud vyberete primární repliku ve stromovém zobrazení aplikace Service Fabric Explorer, uvidíte, že **stav** také označuje chybu. Service Fabric Explorer také zobrazí podrobnosti sestavy `HealthInformation` stavu, které byly přidány do parametru v kódu. Stejné sestavy stavu uvidíte v PowerShellu a na webu Azure Portal.
   
    ![Stav repliky v Průzkumníku infrastruktury služby](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Tato sestava zůstane ve Správci stavu, dokud nebude nahrazena jinou sestavou nebo dokud nebude tato replika odstraněna. Protože jsme tuto `TimeToLive` sestavu stavu `HealthInformation` v objektu nenastavili, platnost sestavy nikdy nevyprší.

Doporučujeme, aby stav by měly být hlášeny na nejvíce podrobné úrovni, což je v tomto případě replika. Můžete také nahlásit `Partition`stav na .

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

Chcete-li `Application`oznamovat stav v , `DeployedApplication`, a `DeployedServicePackage`, použijte `CodePackageActivationContext`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## <a name="next-steps"></a>Další kroky
* [Hloubkové prosazení stavu service fabric](service-fabric-health-introduction.md)
* [ROZHRANÍ REST API pro vytváření sestav stavu služby](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-service)
* [ROZHRANÍ REST API pro vykazování stavu aplikace](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-an-application)

