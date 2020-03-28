---
title: Upgrade aplikace Service Fabric v Powershellu
description: Ukázka skriptu Azure PowerShell – upgrade a monitorování aplikace Azure Service Fabric pomocí Powershellu.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 3a4ef9fad8567eb145d51c6fef61773cc3a00b11
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75614736"
---
# <a name="upgrade-a-service-fabric-application"></a>Upgrade aplikace Service Fabric

Tento ukázkový skript upgraduje spuštěnou instanci aplikace Service Fabric na verzi 1.3.0. Skript zkopíruje nový balíček aplikace do úložiště bitových kopií clusteru, zaregistruje typ aplikace a odebere nepotřebný balíček aplikace.  Skript spustí monitorovaný upgrade a průběžně kontroluje stav upgradu, dokud se upgrade nedokončí nebo nevrátí zpět. Podle potřeby upravte parametry. 

V případě potřeby nainstalujte modul PowerShellu pro Service Fabric se sadou [Service Fabric SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/service-fabric/upgrade-application/upgrade-application.ps1 "Upgrade an application")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Aplikace Get-ServiceFabric](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) | Získá všechny aplikace v clusteru Service Fabric nebo konkrétní aplikace.  |
| [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) | Získá stav upgradu aplikace Service Fabric. |
| [Get-ServiceFabricTyp aplikace](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) | Získá typy aplikací Service Fabric registrované v clusteru Service Fabric. |
| [Zrušit registraci ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Zruší registraci typu aplikace Service Fabric.  |
| [Kopírovat ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Zkopíruje balíček aplikace Service Fabric do úložiště obrázků.  |
| [Registr-ServiceFabricTyp aplikace](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) | Registruje typ aplikace Service Fabric. |
| [Spuštění služby FabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) | Upgraduje aplikaci Service Fabric na zadanou verzi typu aplikace. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Odebere balíček aplikace Service Fabric z úložiště obrázků.|


## <a name="next-steps"></a>Další kroky

Další informace o modulu Service Fabric PowerShell najdete v [dokumentaci k Prostředí Azure PowerShell](/powershell/azure/service-fabric/?view=azureservicefabricps).

Další ukázky PowerShellu pro Azure Service Fabric najdete v [ukázkách Azure PowerShellu](../service-fabric-powershell-samples.md).
