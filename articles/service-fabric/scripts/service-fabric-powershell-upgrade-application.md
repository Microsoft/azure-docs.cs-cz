---
title: Upgrade aplikace Service Fabric v PowerShellu
description: Ukázka skriptu Azure PowerShell – upgrade a monitorování aplikace Azure Service Fabric pomocí prostředí PowerShell.
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
ms.openlocfilehash: 1b04a0c4d2865cf72d9fbccb51f0f083872ea799
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87037878"
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
| [Zrušit registraci – ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Zruší registraci typu Service Fabric aplikace.  |
| [Kopírování – ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Zkopíruje balíček aplikace Service Fabric do úložiště imagí.  |
| [Registrovat – ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) | Zaregistruje typ aplikace Service Fabric. |
| [Spustit – ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) | Upgraduje Service Fabric aplikaci na určenou verzi typu aplikace. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Odebere balíček aplikace Service Fabric z úložiště imagí.|


## <a name="next-steps"></a>Další kroky

Další informace o modulu Service Fabric PowerShellu najdete v [dokumentaci k Azure PowerShell](/powershell/azure/service-fabric/overview?view=azureservicefabricps).

Další ukázky PowerShellu pro Azure Service Fabric najdete v [ukázkách Azure PowerShellu](../service-fabric-powershell-samples.md).
