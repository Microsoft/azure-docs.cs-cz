---
title: Konfigurace upgradu Service Fabric aplikace
description: Naučte se konfigurovat nastavení pro upgrade Service Fabric aplikace pomocí Microsoft Visual Studio.
author: mikkelhegn
ms.topic: conceptual
ms.date: 06/29/2017
ms.author: mikhegn
ms.openlocfilehash: 1db6cea0af229664b07e88463e279b2a64d7e267
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86256046"
---
# <a name="configure-the-upgrade-of-a-service-fabric-application-in-visual-studio"></a>Konfigurace upgradu aplikace Service Fabric v aplikaci Visual Studio
Nástroje Visual Studio Tools for Azure Service Fabric poskytují podporu upgradu pro publikování do místních nebo vzdálených clusterů. Existují tři scénáře, ve kterých chcete aplikaci upgradovat na novější verzi místo toho, aby se aplikace nahradila při testování a ladění:

* Data aplikace nebudou během upgradu ztracena.
* Dostupnost může být vysoká, takže během upgradu nedojde k žádnému přerušení služby, pokud jsou v rámci upgradovacích domén rozdělené dostatečné instance služby.
* Testy lze spustit proti aplikaci během upgradu.

## <a name="parameters-needed-to-upgrade"></a>Parametry potřebné k upgradu
Můžete si vybrat ze dvou typů nasazení: pravidelný nebo upgrade. Při běžném nasazení se vymažou všechny předchozí informace o nasazení a data v clusteru, ale nasazení upgradu ho zachová. Při upgradu aplikace Service Fabric v aplikaci Visual Studio je nutné zadat parametry upgradu aplikace a zásady kontroly stavu. Parametry upgradu aplikace vám pomůžou řídit upgrade, ale zásady kontroly stavu určují, jestli se upgrade úspěšně provedl. Další podrobnosti najdete v tématu [Service Fabric upgrade aplikace: parametry upgradu](service-fabric-application-upgrade-parameters.md) .

Existují tři režimy upgradu: *Monitored*, *UnmonitoredAuto*a *UnmonitoredManual*.

* Monitorovaný upgrade automatizuje upgrade a kontrolu stavu aplikací.
* Upgrade UnmonitoredAuto automatizuje upgrade, ale přeskočí kontrolu stavu aplikace.
* Když provedete upgrade UnmonitoredManual, je třeba ručně upgradovat každou upgradovací doménu.

Každý režim upgradu vyžaduje jiné sady parametrů. Další informace o dostupných možnostech upgradu najdete v tématu věnovaném [parametrům upgradu aplikace](service-fabric-application-upgrade-parameters.md) .

## <a name="upgrade-a-service-fabric-application-in-visual-studio"></a>Upgrade aplikace Service Fabric v aplikaci Visual Studio
Pokud používáte nástroje sady Visual Studio Service Fabric k upgradu aplikace Service Fabric, můžete místo pravidelného nasazení určit proces publikování, který bude upgradován, a to zaškrtnutím políčka **Upgrade aplikace** .

### <a name="to-configure-the-upgrade-parameters"></a>Konfigurace parametrů upgradu
1. Klikněte na tlačítko **Nastavení** vedle zaškrtávacího políčka. Zobrazí se dialogové okno **Upravit parametry upgradu** . Dialogové okno **Upravit parametry upgradu** podporuje režimy upgradu monitorované, UnmonitoredAuto a UnmonitoredManual.
2. Vyberte režim upgradu, který chcete použít, a potom vyplňte parametr Grid.

    Každý parametr má výchozí hodnoty. Nepovinný parametr *DefaultServiceTypeHealthPolicy* přebírá vstupní zatřiďovací tabulku. Tady je příklad vstupního formátu tabulky hash pro *DefaultServiceTypeHealthPolicy*:

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```

    *ServiceTypeHealthPolicyMap* je další volitelný parametr, který přebírá vstupní zatřiďovací tabulku v následujícím formátu:

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```

    Tady je příklad reálného života:

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```
3. Pokud vyberete režim upgradu UnmonitoredManual, musíte ručně spustit konzolu PowerShellu a pokračovat a dokončit proces upgradu. Informace o tom, jak ruční upgrade funguje, najdete v [tématu Service Fabric upgrade aplikace: Pokročilá témata](service-fabric-application-upgrade-advanced.md) .

## <a name="upgrade-an-application-by-using-powershell"></a>Upgrade aplikace pomocí PowerShellu
K upgradu Service Fabric aplikace můžete použít rutiny prostředí PowerShell. Podrobné informace najdete v článku [Service Fabric kurzu upgradu aplikací](service-fabric-application-upgrade-tutorial.md) a [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade) .

## <a name="specify-a-health-check-policy-in-the-application-manifest-file"></a>Zadejte zásady kontroly stavu v souboru manifestu aplikace.
Každá služba ve Service Fabric aplikaci může mít své vlastní parametry zásad stavu, které přepisují výchozí hodnoty. Tyto hodnoty parametrů můžete zadat v souboru manifestu aplikace.

Následující příklad ukazuje, jak použít jedinečnou zásadu kontroly stavu pro každou službu v manifestu aplikace.

```xml
<Policies>
    <HealthPolicy ConsiderWarningAsError="false" MaxPercentUnhealthyDeployedApplications="20">
        <DefaultServiceTypeHealthPolicy MaxPercentUnhealthyServices="20"               
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />
        <ServiceTypeHealthPolicy ServiceTypeName="ServiceTypeName1"
                MaxPercentUnhealthyServices="20"
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />      
    </HealthPolicy>
</Policies>
```
## <a name="next-steps"></a>Další kroky
Další informace o upgradu aplikace naleznete v tématu [Upgrade aplikace pomocí sady Visual Studio](service-fabric-application-upgrade-tutorial.md).
