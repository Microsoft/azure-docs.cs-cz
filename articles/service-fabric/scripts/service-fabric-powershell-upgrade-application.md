---
title: Ukázka skriptu Azure PowerShell – upgrade Service Fabric aplikace | Microsoft Docs
description: Ukázka skriptu Azure PowerShell – upgrade Service Fabric aplikace
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
ms.openlocfilehash: 45186f497371b533451ff374e68b38f9a7eebe51
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035485"
---
# <a name="upgrade-a-service-fabric-application"></a>Upgrade aplikace Service Fabric

Tento ukázkový skript upgraduje spuštěnou instanci aplikace Service Fabric na verzi 1.3.0. Skript zkopíruje nový balíček aplikace do úložiště imagí clusteru, zaregistruje typ aplikace a odebere nepotřebný balíček aplikace.  Skript spustí monitorovaný upgrade a nepřetržitě kontroluje stav upgradu, dokud se upgrade nedokončí nebo nevrátí zpět. Podle potřeby upravte parametry. 

V případě potřeby nainstalujte modul PowerShellu pro Service Fabric se sadou [Service Fabric SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/service-fabric/upgrade-application/upgrade-application.ps1 "Upgrade an application")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) | Načte všechny aplikace v clusteru Service Fabric nebo v konkrétní aplikaci.  |
| [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) | Získá stav upgradu aplikace Service Fabric. |
| [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) | Získá typy aplikací Service Fabric zaregistrovaných v clusteru Service Fabric. |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Zruší registraci typu Service Fabric aplikace.  |
| [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Zkopíruje balíček aplikace Service Fabric do úložiště imagí.  |
| [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) | Zaregistruje typ aplikace Service Fabric. |
| [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) | Upgraduje Service Fabric aplikaci na určenou verzi typu aplikace. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Odebere balíček aplikace Service Fabric z úložiště imagí.|


## <a name="next-steps"></a>Další postup

Další informace o modulu Service Fabric PowerShellu najdete v [dokumentaci k Azure PowerShell](/powershell/azure/service-fabric/?view=azureservicefabricps).

Další ukázky PowerShellu pro Azure Service Fabric najdete v [ukázkách Azure PowerShellu](../service-fabric-powershell-samples.md).
