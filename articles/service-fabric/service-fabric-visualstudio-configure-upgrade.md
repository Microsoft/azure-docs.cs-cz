---
title: Konfigurace upgradu aplikace Service Fabric
description: Přečtěte si, jak nakonfigurovat nastavení pro inovaci aplikace Service Fabric pomocí sady Microsoft Visual Studio.
author: mikkelhegn
ms.topic: conceptual
ms.date: 06/29/2017
ms.author: mikhegn
ms.openlocfilehash: ef88a528dbb6d326e5cc742d14c27218eb7502f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464075"
---
# <a name="configure-the-upgrade-of-a-service-fabric-application-in-visual-studio"></a>Konfigurace upgradu aplikace Service Fabric v sadě Visual Studio
Nástroje Visual Studio pro Azure Service Fabric poskytují podporu upgradu pro publikování do místních nebo vzdálených clusterů. Existují tři scénáře, ve kterých chcete upgradovat aplikaci na novější verzi namísto nahrazení aplikace během testování a ladění:

* Data aplikace nebudou během upgradu ztracena.
* Dostupnost zůstává vysoká, takže během upgradu nedojde k žádnému přerušení služby, pokud existuje dostatek instancí služeb rozložených mezi upgradovací domény.
* Testy lze spustit proti aplikaci při upgradu.

## <a name="parameters-needed-to-upgrade"></a>Parametry potřebné k upgradu
Můžete si vybrat ze dvou typů nasazení: pravidelné nebo upgrade. Pravidelné nasazení vymaže všechny předchozí informace o nasazení a data v clusteru, zatímco nasazení upgradu je zachová. Při upgradu aplikace Service Fabric v sadě Visual Studio je třeba zadat parametry upgradu aplikace a zásady kontroly stavu. Parametry upgradu aplikace pomáhají řídit upgrade, zatímco zásady kontroly stavu určují, zda byl upgrade úspěšný. Další podrobnosti naleznete [v tématu Upgrade aplikace Service Fabric: parametry upgradu.](service-fabric-application-upgrade-parameters.md)

Existují tři režimy upgradu: *Monitored*, *UnmonitoredAuto*a *UnmonitoredManual*.

* Monitorovaný upgrade automatizuje kontrolu stavu upgradu a aplikace.
* Upgrade UnmonitoredAuto automatizuje upgrade, ale přeskočí kontrolu stavu aplikace.
* Při upgradu Nemonitorované příručky je třeba ručně upgradovat každou doménu upgradu.

Každý režim upgradu vyžaduje různé sady parametrů. Další informace o dostupných možnostech upgradu najdete v tématu [Parametry upgradu aplikace.](service-fabric-application-upgrade-parameters.md)

## <a name="upgrade-a-service-fabric-application-in-visual-studio"></a>Upgrade aplikace Service Fabric v sadě Visual Studio
Pokud používáte nástroje Visual Studio Service Fabric k upgradu aplikace Service Fabric, můžete zadat proces publikování jako upgrade, nikoli pravidelné nasazení zaškrtnutím políčka **Upgradovat aplikaci.**

### <a name="to-configure-the-upgrade-parameters"></a>Konfigurace parametrů upgradu
1. Klikněte na tlačítko **Nastavení** vedle zaškrtávacího políčka. Zobrazí se dialogové okno **Upravit parametry upgradu.** Dialogové okno **Upravit parametry upgradu** podporuje režimy upgradu Monitorovaný, Nemonitorovanýautomatický a Nemonitorovaný manuál.
2. Vyberte režim upgradu, který chcete použít, a vyplňte mřížku parametrů.

    Každý parametr má výchozí hodnoty. Volitelný parametr *DefaultServiceTypeHealthPolicy* přebírá vstup tabulky hash. Tady je příklad vstupního formátu tabulky hash pro *DefaultServiceTypeHealthPolicy*:

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```

    *ServiceTypeHealthPolicyMap* je další volitelný parametr, který přebírá vstup tabulky hash v následujícím formátu:

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```

    Zde je příklad z reálného života:

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```
3. Pokud vyberete režim upgradu Nemonitorované příručky, musíte ručně spustit konzolu prostředí PowerShell, abyste pokračovali v procesu upgradu a dokončili ho. Informace o tom, jak funguje ruční inovace, naleznete v [tématu Service Fabric application upgrade: advanced topics.](service-fabric-application-upgrade-advanced.md)

## <a name="upgrade-an-application-by-using-powershell"></a>Upgrade aplikace pomocí PowerShellu
Rutiny prostředí PowerShell můžete použít k upgradu aplikace Service Fabric. Podrobné informace naleznete [v tématu Service Fabric application upgrade tutorial](service-fabric-application-upgrade-tutorial.md) and [Start-ServiceFabricApplicationUpgrade.](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade)

## <a name="specify-a-health-check-policy-in-the-application-manifest-file"></a>Určení zásadkontroly stavu v souboru manifestu aplikace
Každá služba v aplikaci Service Fabric může mít vlastní parametry zásad stavu, které přepisují výchozí hodnoty. Tyto hodnoty parametrů můžete zadat v souboru manifestu aplikace.

Následující příklad ukazuje, jak použít jedinečné zásady kontroly stavu pro každou službu v manifestu aplikace.

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
Další informace o inovaci aplikace naleznete v [tématu Upgrade aplikace pomocí sady Visual Studio](service-fabric-application-upgrade-tutorial.md).
