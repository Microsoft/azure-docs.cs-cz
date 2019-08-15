---
title: Ukázkový skript Azure PowerShell – odebrání aplikace z clusteru | Microsoft Docs
description: Ukázkový skript Azure PowerShell – odebrání aplikace z clusteru Service Fabric
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
ms.openlocfilehash: f572996b4e460480335dbe32c4599bde206251d4
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035499"
---
# <a name="remove-an-application-from-a-service-fabric-cluster"></a>Odebrání aplikace z clusteru Service Fabric

Tento ukázkový skript odstraní spuštěnou instanci aplikace Service Fabric a zruší registraci typu a verze aplikace z clusteru.  Odstraněním instance aplikace dojde také k odstranění všech spuštěných instancí služby přidružených dané aplikaci. Podle potřeby upravte parametry. 

V případě potřeby nainstalujte modul PowerShellu pro Service Fabric se sadou [Service Fabric SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/service-fabric/remove-application/remove-application.ps1 "Remove an application from a cluster")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) | Odebere instanci běžící Service Fabric aplikace z clusteru.  |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Zruší registraci typu a verze Service Fabric aplikace z clusteru. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Service Fabric PowerShellu najdete v [dokumentaci k Azure PowerShell](/powershell/azure/service-fabric/?view=azureservicefabricps).

Další ukázky PowerShellu pro Azure Service Fabric najdete v [ukázkách Azure PowerShellu](../service-fabric-powershell-samples.md).
