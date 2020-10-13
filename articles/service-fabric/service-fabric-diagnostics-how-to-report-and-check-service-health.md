---
title: Sestavování a kontroly stavu pomocí Azure Service Fabric
description: Naučte se odesílat zprávy o stavu z kódu služby a jak kontrolovat stav služby pomocí nástrojů pro monitorování stavu, které poskytuje Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.custom: devx-track-csharp
ms.openlocfilehash: 59c8202b03bf1be2be5a68b75a1d7c7404b2213d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89020197"
---
# <a name="report-and-check-service-health"></a>Hlášení a kontrola stavu služeb
Pokud vaše služby nastanou problémy, vaše schopnost reagovat na incidenty a výpadky závisí na vaší schopnosti rychle detekovat problémy. Pokud nahlásíte problémy a chyby do služby Azure Service Fabric Health Manager z kódu služby, můžete použít standardní nástroje pro monitorování stavu, které Service Fabric poskytuje ke kontrole stavu.

Existují tři způsoby, jak můžete hlásit stav ze služby:

* Použijte [partition](/dotnet/api/system.fabric.istatefulservicepartition) nebo [CodePackageActivationContext](/dotnet/api/system.fabric.codepackageactivationcontext) objekty.  
  Pomocí `Partition` objektů a můžete `CodePackageActivationContext` nahlásit stav prvků, které jsou součástí aktuálního kontextu. Například kód, který se spouští jako součást repliky, může hlásit stav pouze v této replice, oddíl, do kterého patří, a aplikaci, které je součástí.
* Použijte `FabricClient`.   
  V případě, že `FabricClient` je cluster [nezabezpečený](service-fabric-cluster-security.md) nebo pokud je služba spuštěná s oprávněními správce, je možné použít k hlášení stavu kódu služby. Většina scénářů reálného světa nepoužívá nezabezpečené clustery nebo poskytuje oprávnění správce. Pomocí nástroje `FabricClient` můžete nahlásit stav jakékoli entity, která je součástí clusteru. V ideálním případě by však měl kód služby odesílat pouze zprávy, které souvisejí s jeho vlastním stavem.
* Použijte rozhraní REST API na úrovni clusteru, aplikace, nasazené aplikace, služby, balíčku, oddílu, repliky nebo uzlu. Dá se použít k hlášení stavu z kontejneru.

Tento článek vás provede příkladem, který hlásí stav z kódu služby. Tento příklad také ukazuje, jak lze použít nástroje poskytované Service Fabric ke kontrole stavu. Tento článek je určený jako rychlý Úvod k funkcím monitorování stavu Service Fabric. Podrobnější informace si můžete přečíst v podrobných článcích o stavu, který začíná odkazem na konci tohoto článku.

## <a name="prerequisites"></a>Požadavky
Musíte mít nainstalované následující:

* Visual Studio 2015 nebo Visual Studio 2019
* Sada Service Fabric SDK

## <a name="to-create-a-local-secure-dev-cluster"></a>Vytvoření místního clusteru zabezpečeného vývojového prostředí
* Otevřete PowerShell s oprávněními správce a spusťte následující příkazy:

![Příkazy, které ukazují, jak vytvořit zabezpečený cluster pro vývoj](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## <a name="to-deploy-an-application-and-check-its-health"></a>Nasazení aplikace a zjištění jejího stavu
1. Otevřete Visual Studio jako správce.
1. Vytvořte projekt pomocí šablony **stavové služby** .
   
    ![Vytvoření aplikace Service Fabric se stavovou službou](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)
1. Stisknutím klávesy **F5** spusťte aplikaci v režimu ladění. Aplikace se nasadí do místního clusteru.
1. Po spuštění aplikace klikněte pravým tlačítkem myši na ikonu správce místního clusteru v oznamovací oblasti a v místní nabídce vyberte **Spravovat místní cluster** a otevřete Service Fabric Explorer.
   
    ![Otevřít Service Fabric Explorer z oznamovací oblasti](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)
1. Stav aplikace by měl být zobrazen jako v tomto obrázku. V tuto chvíli by měla být aplikace v pořádku bez chyb.
   
    ![Aplikace v pořádku v Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)
1. Stav můžete také ověřit pomocí prostředí PowerShell. Můžete použít ```Get-ServiceFabricApplicationHealth``` ke kontrole stavu aplikace a můžete použít ```Get-ServiceFabricServiceHealth``` ke kontrole stavu služby. Sestava stavu pro stejnou aplikaci v prostředí PowerShell je na tomto obrázku.
   
    ![V prostředí PowerShell aplikace v pořádku](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## <a name="to-add-custom-health-events-to-your-service-code"></a>Přidání vlastních událostí stavu do kódu služby
Šablony projektu Service Fabric v aplikaci Visual Studio obsahují vzorový kód. Následující kroky ukazují, jak můžete hlásit vlastní události stavu z kódu vaší služby. Tyto sestavy se zobrazují automaticky ve standardních nástrojích pro monitorování stavu, které Service Fabric poskytuje, jako je Service Fabric Explorer, Azure Portal zobrazení stavu a prostředí PowerShell.

1. Znovu otevřete aplikaci, kterou jste vytvořili dříve v aplikaci Visual Studio, nebo vytvořte novou aplikaci pomocí šablony **stavové služby** sady Visual Studio.
1. Otevřete soubor Stateful1.cs a vyhledejte `myDictionary.TryGetValueAsync` volání v `RunAsync` metodě. Můžete vidět, že tato metoda vrátí hodnotu `result` , která obsahuje aktuální hodnotu čítače, protože klíčová logika v této aplikaci má zůstat spuštěný počet. Pokud byla tato aplikace skutečnou aplikací a chybějící výsledek představoval chybu, měli byste tuto událost označit příznakem.
1. Chcete-li ohlásit událost stavu, když chybějící výsledek představuje selhání, přidejte následující kroky.
   
    a. Přidejte `System.Fabric.Health` obor názvů do souboru Stateful1.cs.
   
    ```csharp
    using System.Fabric.Health;
    ```
   
    b. Po volání přidejte následující kód `myDictionary.TryGetValueAsync`
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
    Stav repliky oznamujeme, protože je hlášen ze stavové služby. `HealthInformation`Parametr ukládá informace o problému se stavem, který je hlášený.
   
    Pokud jste vytvořili bezstavovou službu, použijte následující kód:
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```
1. Pokud je vaše služba spuštěná s oprávněními správce nebo pokud není cluster [zabezpečený](service-fabric-cluster-security.md), můžete použít také `FabricClient` k hlášení stavu, jak je znázorněno v následujícím postupu.  
   
    a. Vytvořte `FabricClient` instanci po `var myDictionary` deklaraci.
   
    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```
   
    b. Po volání přidejte následující kód `myDictionary.TryGetValueAsync` .
   
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
1. Pojďme tuto chybu simulovat a zobrazit v nástrojích pro sledování stavu. Chcete-li simulovat chybu, odkomentujte první řádek v kódu vytváření sestav stavu, který jste přidali dříve. Po zakomentování prvního řádku bude kód vypadat jako v následujícím příkladu.
   
    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
   Tento kód při každém spuštění sestavy stavu `RunAsync` spustí. Po provedení změny stiskněte klávesu **F5** ke spuštění aplikace.
1. Po spuštění aplikace otevřete Service Fabric Explorer pro kontrolu stavu aplikace. Tentokrát Service Fabric Explorer ukáže, že aplikace není v pořádku. Aplikace zobrazuje stav není v pořádku, protože došlo k chybě, která byla hlášena z kódu, který byl dříve přidán.
   
    ![Poškozená aplikace v Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)
1. Pokud ve stromovém zobrazení Service Fabric Explorer vyberete primární repliku, zobrazí se v něm **stav** , že se zobrazuje chyba. Service Fabric Explorer také zobrazí podrobnosti o sestavě o stavu, které byly přidány do `HealthInformation` parametru v kódu. V PowerShellu a Azure Portal uvidíte stejné sestavy o stavu.
   
    ![Stav repliky v Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Tato sestava zůstane ve Správci stavu, dokud není nahrazena jinou sestavou nebo dokud se tato replika neodstraní. Vzhledem `TimeToLive` k tomu, že jsme v objektu nestavili pro tuto sestavu stavu `HealthInformation` , sestava nikdy nevyprší.

Doporučujeme, aby se stav nahlásil na nejpodrobnější úrovni, která je v tomto případě replikou. Můžete také ohlásit stav Zapnuto `Partition` .

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

K hlášení stavu v systémech `Application` , `DeployedApplication` , a `DeployedServicePackage` použijte  `CodePackageActivationContext` .

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## <a name="next-steps"></a>Další kroky
* [Hluboká podrobněa stavu Service Fabric](service-fabric-health-introduction.md)
* [REST API pro stav služby Reporting Services](/rest/api/servicefabric/report-the-health-of-a-service)
* [REST API pro vytváření sestav stavu aplikace](/rest/api/servicefabric/report-the-health-of-an-application)
