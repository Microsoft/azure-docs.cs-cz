---
title: Konfigurace upgradu aplikace Service Fabric | Microsoft Docs
description: "Zjistěte, jak nakonfigurovat nastavení pro upgrade aplikace Service Fabric pomocí sady Microsoft Visual Studio."
services: service-fabric
documentationcenter: na
author: mikkelhegn
manager: mfussell
editor: tglee
ms.assetid: 1757ba85-0b7b-4f16-8a23-2ddaa61c86c6
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/29/2017
ms.author: mikkelhegn
ms.openlocfilehash: 3ff85991e87a4996890520a566b3909d331ed27f
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="configure-the-upgrade-of-a-service-fabric-application-in-visual-studio"></a>Konfigurace upgradu aplikace Service Fabric v sadě Visual Studio
Nástroje sady Visual Studio pro Azure Service Fabric zajištění upgradu podpory pro publikování do místního nebo vzdáleného clusterů. Existují tři scénáře, ve kterých chcete upgradovat aplikaci na novější verzi místo nahrazení aplikace v průběhu testování a ladění:

* Během upgradu nedojde ke ztrátě dat. aplikace.
* Dostupnost zůstane vysoké, takže nebudou existovat žádné přerušení služby během upgradu případné že dostatek instance služby rozloženy domén upgradu.
* Vzhledem aplikaci lze spustit testy, je během upgradu.

## <a name="parameters-needed-to-upgrade"></a>Parametry, které jsou potřebné pro upgrade
Můžete vybrat z dva typy nasazení: standardním nebo upgradu. Regulární nasazení vymaže všechny předchozí informace o nasazení a data v clusteru, při upgradu nasazení se zachová. Při upgradu aplikace Service Fabric v sadě Visual Studio, je třeba zadat, že parametry upgradu aplikace a stavu zkontrolujte zásady. Parametry upgradu aplikace pomoc při ovládání upgradu, zatímco zásady kontroly stavu určit, zda upgradu byla úspěšná. V tématu [upgradu aplikace Service Fabric: upgrade parametry](service-fabric-application-upgrade-parameters.md) další podrobnosti.

Existují tři režimy upgradu: *monitorované*, *UnmonitoredAuto*, a *UnmonitoredManual*.

* Upgrade monitorované automatizuje upgradu a kontrola stavu aplikace.
* UnmonitoredAuto upgrade automatizuje upgrade, ale přeskočí kontrolu stavu aplikace.
* Když provedete UnmonitoredManual upgrade, je třeba ručně upgradovat každé upgradované domény.

Každý režimu upgradu vyžaduje různé sady parametrů. V tématu [parametry upgradu aplikace](service-fabric-application-upgrade-parameters.md) Další informace o dostupných možnostech upgradu.

## <a name="upgrade-a-service-fabric-application-in-visual-studio"></a>Upgrade aplikace Service Fabric v sadě Visual Studio
Pokud používáte nástroje Visual Studio Service Fabric k upgradu aplikace Service Fabric, můžete zadat proces publikování jako upgrade, ne regulární nasazení kontrolou **upgradu aplikace** zaškrtávací políčko.

### <a name="to-configure-the-upgrade-parameters"></a>Konfigurace upgradu parametrů
1. Klikněte **nastavení** tlačítko vedle pole. **Upravit parametry upgradu** zobrazí se dialogové okno. **Upravit parametry upgradu** dialogové okno podporuje monitorované UnmonitoredAuto a UnmonitoredManual upgradu režimy.
2. Vyberte režim upgradu, který chcete použít a potom vyplňte parametr mřížky.

    Každý parametr má výchozí hodnoty. Volitelný parametr *DefaultServiceTypeHealthPolicy* trvá vstupní tabulky hodnota hash. Tady je příklad formát vstupu tabulku hash pro *DefaultServiceTypeHealthPolicy*:

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```

    *ServiceTypeHealthPolicyMap* je jiný volitelný parametr, který přijímá vstup hash tabulku v následujícím formátu:

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```

    Tady je příklad reálnými:

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```
3. Pokud vyberete UnmonitoredManual režimu upgradu, musíte spustit ručně konzole PowerShell chcete pokračovat a dokončit proces upgradu. Odkazovat na [upgradu aplikace Service Fabric: advanced témata](service-fabric-application-upgrade-advanced.md) další jak ruční upgrade funguje.

## <a name="upgrade-an-application-by-using-powershell"></a>Upgrade aplikace pomocí prostředí PowerShell
Rutiny prostředí PowerShell můžete použít k upgradu aplikace Service Fabric. V tématu [kurz upgradu aplikace Service Fabric](service-fabric-application-upgrade-tutorial.md) a [Start-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/mt125975.aspx) podrobné informace.

## <a name="specify-a-health-check-policy-in-the-application-manifest-file"></a>Zadejte zásady stavu zkontrolujte v souboru manifestu aplikace
Všechny služby v Service Fabric aplikace může mít svůj vlastní parametry zásad stavu, které přepsat výchozí hodnoty. Můžete zadat tyto hodnoty parametrů v souboru manifestu aplikace.

Následující příklad ukazuje, jak použít zásady kontrola jedinečný stav pro každou službu v manifestu aplikace.

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