---
title: Řešení potíží s vaší místní nastavení clusteru Azure Service Fabric | Dokumentace Microsoftu
description: Tento článek popisuje sadu doporučení pro řešení potíží s místním vývojovém clusteru
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: chackdan
editor: ''
ms.assetid: 97f4feaa-bba0-47af-8fdd-07f811fe2202
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 8bb32b2bded061bd19bcd7cfda4ef259a75b0626
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60864435"
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>Řešení potíží s nastavením vašeho místního vývojového clusteru
Pokud narazíte na problém při interakci s vaší místní vývojový cluster Azure Service Fabric, přečtěte si následující návrhy pro potenciální řešení.

## <a name="cluster-setup-failures"></a>Selháními instalace clusteru
### <a name="cannot-clean-up-service-fabric-logs"></a>Nelze vymazat protokoly Service Fabric
#### <a name="problem"></a>Problém
Při spouštění skriptu DevClusterSetup, se zobrazí následující chyba:

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>Řešení
Zavřete aktuální okno Powershellu a otevřete nové okno Powershellu jako správce. Nyní můžete úspěšně spustit skript.

## <a name="cluster-connection-failures"></a>Chyby připojení clusteru

### <a name="type-initialization-exception"></a>Typ výjimky inicializace
#### <a name="problem"></a>Problém
Když se připojujete ke clusteru v prostředí PowerShell, se zobrazí chyba typeinitializationexception – pro System.Fabric.Common.AppTrace.

#### <a name="solution"></a>Řešení
Proměnné path nebyla nastavena správně během instalace. Odhlásit z Windows a znovu nepřihlásí. Tím se aktualizuje vaše cesta.

### <a name="cluster-connection-fails-with-object-is-closed"></a>Připojení clusteru selže s "Objekt je zavřený."
#### <a name="problem"></a>Problém
Volání Connect-ServiceFabricCluster selže s chybou takto:

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### <a name="solution"></a>Řešení
Zavřete aktuální okno Powershellu a otevřete nové okno Powershellu jako správce.

### <a name="fabric-connection-denied-exception"></a>Výjimka bylo spojení zamítnuto prostředků infrastruktury
#### <a name="problem"></a>Problém
Při ladění ze sady Visual Studio, zobrazí se chyba FabricConnectionDeniedException.

#### <a name="solution"></a>Řešení
K této chybě obvykle dochází při pokusu o spuštění procesu služby hostitele ručně.

Ujistěte se, že nemáte žádné projekty služeb nastavit jako projektů po spuštění ve vašem řešení. Pouze projekty aplikace Service Fabric musí být nastavená jako projektů po spuštění.

> [!TIP]
> Pokud po instalaci místní cluster začíná neobvyklým způsobem chovat, můžete obnovit pomocí aplikace na hlavním panelu systému místního clusteru správce. Tím se odebere existující cluster a vytvořte nový. Všimněte si, že všechny nasazené aplikace a související data se odeberou.
> 
> 

## <a name="next-steps"></a>Další postup
* [Pochopení a odstraňování potíží se systémovými stavovými sestavami clusteru](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Vizualizace clusteru pomocí nástroje Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

