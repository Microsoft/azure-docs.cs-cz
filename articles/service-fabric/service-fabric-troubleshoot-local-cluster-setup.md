---
title: Řešení potíží s vaší místní cluster instalací Azure Service Fabric | Microsoft Docs
description: Tento článek popisuje sadu doporučení pro řešení potíží s místní vývojový cluster
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: ''
ms.assetid: 97f4feaa-bba0-47af-8fdd-07f811fe2202
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 6879a24df434d5bf69c9ba14aa00cdc9cd67df57
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>Řešení potíží s instalací vašeho místního vývojového clusteru
Pokud narazíte na problém při interakci s místním clusteru vývoj Azure Service Fabric, přečtěte si následující návrhy možná řešení.

## <a name="cluster-setup-failures"></a>Selhání instalace clusteru
### <a name="cannot-clean-up-service-fabric-logs"></a>Nelze vyčistit protokoly Service Fabric
#### <a name="problem"></a>Problém
Při spouštění skriptu DevClusterSetup, zobrazí následující chyba:

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>Řešení
Zavřete aktuální okno prostředí PowerShell a otevřete nové okno prostředí PowerShell jako správce. Nyní můžete úspěšně spustit skript.

## <a name="cluster-connection-failures"></a>Selhání připojení ke clusteru

### <a name="type-initialization-exception"></a>Typ výjimky inicializace
#### <a name="problem"></a>Problém
Při připojování ke clusteru v prostředí PowerShell, zobrazí tato chyba typeinitializationexception – pro System.Fabric.Common.AppTrace.

#### <a name="solution"></a>Řešení
Během instalace nebyl správně nastaven vaše proměnná path. Odhlásit se ze systému Windows a přihlaste se zpět. To obnoví cestu.

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
Zavřete aktuální okno prostředí PowerShell a otevřete nové okno prostředí PowerShell jako správce.

### <a name="fabric-connection-denied-exception"></a>Výjimka připojení odepřen prostředků infrastruktury
#### <a name="problem"></a>Problém
Při ladění ze sady Visual Studio, dojde k chybě FabricConnectionDeniedException.

#### <a name="solution"></a>Řešení
K této chybě obvykle dochází při pokusu o spuštění procesu služby hostitele ručně.

Ujistěte se, že nemáte žádné projekty služeb nastavit jako spouštěné projekty v řešení. Pouze projekty aplikací Service Fabric musí být nastavená jako projektů po spuštění.

> [!TIP]
> Pokud po instalaci, začne chovat neobvyklým způsobem místním clusteru, můžete obnovit pomocí aplikace panelu systému místního clusteru manager. Odebere existující cluster a nastavte novou. Všimněte si, že všechny nasazené aplikace a související data se odeberou.
> 
> 

## <a name="next-steps"></a>Další postup
* [Rady pro pochopení a řešení potíží s clusteru pomocí sestav o stavu systému](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Vizualizace clusteru pomocí nástroje Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

