---
title: Řešení potíží s místním nastavením clusteru Azure Service Fabric
description: Tento článek se zabývá sadou návrhů pro řešení potíží s místním vývojovým clusterem.
author: mikkelhegn
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 76ca0bb7b81b3896538f08ff2ef52ed1ac6b363f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87091602"
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>Řešení potíží s místním nastavením clusteru pro vývoj
Pokud narazíte na problém při interakci s místním vývojovým clusterem Azure Service Fabric, přečtěte si následující návrhy pro potenciální řešení.

## <a name="cluster-setup-failures"></a>Selhání instalace clusteru
### <a name="cannot-clean-up-service-fabric-logs"></a>Protokoly Service Fabric nejde vyčistit.
#### <a name="problem"></a>Problém
Při spuštění skriptu DevClusterSetup se zobrazí následující chyba:

```output
Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
At line:1 char:1 + .\DevClusterSetup.ps1
+ ~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
+ FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1
```

#### <a name="solution"></a>Řešení
Zavřete aktuální okno PowerShellu a otevřete nové okno PowerShellu jako správce. Teď můžete skript úspěšně spustit.

## <a name="cluster-connection-failures"></a>Selhání připojení clusteru

### <a name="type-initialization-exception"></a>Výjimka inicializace typu
#### <a name="problem"></a>Problém
Když se připojujete ke clusteru v PowerShellu, zobrazí se chyba TypeInitializationException pro System. Fabric. Common. AppTrace.

#### <a name="solution"></a>Řešení
Vaše Proměnná cesty nebyla při instalaci správně nastavena. Odhlaste se z Windows a znovu se přihlaste. Tím se aktualizuje vaše cesta.

### <a name="cluster-connection-fails-with-object-is-closed"></a>Připojení clusteru se nepovede a dojde k chybě typu "objekt je uzavřen".
#### <a name="problem"></a>Problém
Volání Connect-ServiceFabricCluster se nezdařila s chybou, například:

```output
Connect-ServiceFabricCluster : The object is closed.
At line:1 char:1
+ Connect-ServiceFabricCluster
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
+ FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster
```

#### <a name="solution"></a>Řešení
Zavřete aktuální okno PowerShellu a otevřete nové okno PowerShellu jako správce.

### <a name="fabric-connection-denied-exception"></a>Výjimka zamítnutí připojení prostředků infrastruktury
#### <a name="problem"></a>Problém
Při ladění ze sady Visual Studio se zobrazí chyba FabricConnectionDeniedException.

#### <a name="solution"></a>Řešení
K této chybě obvykle dochází při pokusu o ruční spuštění procesu hostitele služby.

Ujistěte se, že ve vašem řešení nemáte nastavené žádné projekty služeb jako spouštěné projekty. Pouze projekty aplikace Service Fabric by měly být nastaveny jako spouštěné projekty.

> [!TIP]
> Pokud se Váš místní cluster po instalaci začne chovat neobvykle, můžete ho resetovat pomocí aplikace místní Správce clusterů v hlavním panelu systému. Tím se odebere existující cluster a nastaví se nový. Všimněte si, že se odeberou všechny nasazené aplikace a přidružená data.
> 
> 

## <a name="next-steps"></a>Další kroky
* [Pochopení a řešení potíží s clusterem pomocí sestav stavu systému](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Vizualizujte cluster pomocí Service Fabric Exploreru](service-fabric-visualizing-your-cluster.md)

