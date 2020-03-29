---
title: Poradce při potížích s nastavením místního clusteru Azure Service Fabric
description: Tento článek popisuje sadu návrhů pro řešení potíží s místním vývojovým clusterem
author: mikkelhegn
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: ea313adb43f8d91ec9e57dd1d0b8d3447a8075f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465500"
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>Řešení potíží s místním nastavením clusteru pro vývoj
Pokud narazíte na problém při interakci s místním clusterem vývoje Azure Service Fabric, přečtěte si následující návrhy pro potenciální řešení.

## <a name="cluster-setup-failures"></a>Selhání instalace clusteru
### <a name="cannot-clean-up-service-fabric-logs"></a>Nelze vyčistit protokoly service fabric
#### <a name="problem"></a>Problém
Při spuštění skriptu DevClusterSetup se zobrazí následující chyba:

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>Řešení
Zavřete aktuální okno PowerShellu a otevřete nové okno PowerShellu jako správce. Nyní můžete úspěšně spustit skript.

## <a name="cluster-connection-failures"></a>Selhání připojení clusteru

### <a name="type-initialization-exception"></a>Výjimka inicializace typu
#### <a name="problem"></a>Problém
Při připojování ke clusteru v prostředí PowerShell se zobrazí chyba TypeInitializationException for System.Fabric.Common.AppTrace.

#### <a name="solution"></a>Řešení
Proměnná cesty nebyla během instalace správně nastavena. Odhlaste se z Windows a přihlaste se zpět. Tím se aktualizuje vaše cesta.

### <a name="cluster-connection-fails-with-object-is-closed"></a>Připojení clusteru se nezdaří s "Objekt je uzavřen"
#### <a name="problem"></a>Problém
Volání Connect-ServiceFabricCluster se nezdaří s chybou, jako je tato:

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### <a name="solution"></a>Řešení
Zavřete aktuální okno PowerShellu a otevřete nové okno PowerShellu jako správce.

### <a name="fabric-connection-denied-exception"></a>Výjimka Připojení prostředků infrastruktury byla odepřena.
#### <a name="problem"></a>Problém
Při ladění z Visual Studio se zobrazí fabricConnectionDeniedException chyba.

#### <a name="solution"></a>Řešení
K této chybě obvykle dochází při pokusu o ruční spuštění procesu hostitele služby.

Ujistěte se, že nemáte žádné projekty služeb nastaveny jako projekty při spuštění ve vašem řešení. Pouze projekty aplikace Service Fabric by měly být nastaveny jako projekty spuštění.

> [!TIP]
> Pokud se po instalaci místní cluster začne chovat abnormálně, můžete jej obnovit pomocí aplikace místního správce clusteru. Tím odeberete existující cluster a nastavíte nový. Všimněte si, že všechny nasazené aplikace a přidružená data jsou odebrány.
> 
> 

## <a name="next-steps"></a>Další kroky
* [Principy clusteru a řešení potíží se sestavami stavu systému](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Vizualizace clusteru pomocí nástroje Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

