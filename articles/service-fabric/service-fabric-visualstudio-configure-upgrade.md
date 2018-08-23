---
title: Konfigurace upgradu aplikace Service Fabric | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat nastavení pro upgrade aplikace Service Fabric pomocí sady Microsoft Visual Studio.
services: service-fabric
documentationcenter: na
author: mikkelhegn
manager: mfussell
editor: tglee
ms.assetid: 1757ba85-0b7b-4f16-8a23-2ddaa61c86c6
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/29/2017
ms.author: mikkelhegn
ms.openlocfilehash: 79120371ca2a62e5ef9f2bf38476635db12e9fcc
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2018
ms.locfileid: "42058640"
---
# <a name="configure-the-upgrade-of-a-service-fabric-application-in-visual-studio"></a>Konfigurace upgradu aplikace Service Fabric v sadě Visual Studio
Visual Studio tools pro Azure Service Fabric poskytuje podporu upgradu pro publikování do místního nebo vzdáleného clusteru. Existují tři scénáře, ve kterých chcete provést upgrade na novější verzi místo abyste jím nahrazovali aplikace v průběhu testování a ladění aplikace:

* Aplikace data nebudou ztracena během upgradu.
* Dostupnost zůstane vysoké a nesmí být žádné přerušení služby během upgradu, pokud jsou že dostatek instancí služby rozloženy upgradovacích domén.
* Testy můžete spustit pro aplikaci, zatímco se upgraduje.

## <a name="parameters-needed-to-upgrade"></a>Parametry potřebné k upgradu
Můžete vybrat ze dvou typů nasazení: normální nebo upgradu. Pravidelné nasazení vymaže všechny předchozí informace o nasazení a data v clusteru během nasazení upgradu zachová jej. Při upgradu aplikace Service Fabric v sadě Visual Studio, je třeba zadat, že parametry upgradu aplikace a stavu se podívejte na zásady. Parametry upgradu aplikace pomáhají řídit upgradu, zatímco zásady kontroly stavu určit, zda upgrade proběhl úspěšně. Zobrazit [upgrade aplikace Service Fabric: parametry upgradu](service-fabric-application-upgrade-parameters.md) další podrobnosti.

Existují tři režimy upgradu: *monitorované*, *UnmonitoredAuto*, a *UnmonitoredManual*.

* Upgrade monitorované automatizuje inovace a kontrola stavu aplikace.
* UnmonitoredAuto upgrade automatizuje upgrade, ale přeskočí kontrolu stavu aplikací.
* Když provedete UnmonitoredManual upgrade, budete muset ručně upgradovat každou upgradovací doména.

Každý režim upgradu vyžaduje jinou sadu parametrů. Zobrazit [parametry upgradu aplikace](service-fabric-application-upgrade-parameters.md) Další informace o dostupných možnostech upgradu.

## <a name="upgrade-a-service-fabric-application-in-visual-studio"></a>Upgrade aplikace Service Fabric v sadě Visual Studio
Pokud používáte nástroje Visual Studio Service Fabric k upgradu aplikace Service Fabric, můžete publikovat procesu upgradu spíše než regulární nasazení tak, že zkontrolujete **upgradovat aplikaci** zaškrtávací políčko.

### <a name="to-configure-the-upgrade-parameters"></a>Konfigurovat parametry upgradu
1. Klikněte na tlačítko **nastavení** tlačítko vedle zaškrtnutí políčka. **Upravit parametry upgradu** zobrazí se dialogové okno. **Upravit parametry upgradu** dialogové okno podporuje monitorované UnmonitoredAuto a UnmonitoredManual upgradu režimy.
2. Vyberte režim upgradu, který chcete použít a pak vyplňte mříži parametrů.

    Každý parametr má výchozí hodnoty. Volitelný parametr *DefaultServiceTypeHealthPolicy* přijímá vstup tabulku hash. Tady je příklad formát vstupu tabulku hash pro *DefaultServiceTypeHealthPolicy*:

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```

    *ServiceTypeHealthPolicyMap* je jiný volitelný parametr, který přijímá vstup tabulky hash v následujícím formátu:

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```

    Tady je příklad reálného života:

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```
3. Pokud vyberete režim upgradu UnmonitoredManual, musíte ručně spusťte konzolu Powershellu pokračovat a dokončit proces upgradu. Odkazovat na [upgrade aplikace Service Fabric: Pokročilá témata](service-fabric-application-upgrade-advanced.md) další jak ruční upgrade funguje.

## <a name="upgrade-an-application-by-using-powershell"></a>Upgrade aplikace pomocí Powershellu
Rutiny prostředí PowerShell můžete použít k upgradu aplikace Service Fabric. Zobrazit [kurz upgradu aplikace Service Fabric](service-fabric-application-upgrade-tutorial.md) a [Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade) podrobné informace.

## <a name="specify-a-health-check-policy-in-the-application-manifest-file"></a>Zadejte zásady kontroly stavu v souboru manifestu aplikace
Každá služba v aplikaci Service Fabric může mít vlastní parametry zásad stavu, které přepíší výchozí hodnoty. Můžete zadat tyto hodnoty parametrů v souboru manifestu aplikace.

Následující příklad ukazuje, jak použít zásadu vrácení jedinečný stav pro každou službu v manifestu aplikace.

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
## <a name="next-steps"></a>Další postup
Další informace o upgradu aplikace najdete v tématu [Upgrade aplikace pomocí sady Visual Studio](service-fabric-application-upgrade-tutorial.md).
