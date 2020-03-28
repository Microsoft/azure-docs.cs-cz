---
title: Odebrání aplikace z clusteru v Powershellu
description: Ukázka skriptu Azure PowerShell – odebrání aplikace z clusteru Service Fabric.
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
ms.openlocfilehash: 9ac4c23468c74b5a2c6874de2cb6b8d0e6b9e7dd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75610280"
---
# <a name="remove-an-application-from-a-service-fabric-cluster-using-powershell"></a>Odebrání aplikace z clusteru Service Fabric pomocí prostředí Powershell

Tento ukázkový skript odstraní spuštěnou instanci aplikace Service Fabric a zruší registraci typu a verze aplikace z clusteru.  Odstraněním instance aplikace dojde také k odstranění všech spuštěných instancí služby přidružených dané aplikaci. Podle potřeby upravte parametry. 

V případě potřeby nainstalujte modul PowerShellu pro Service Fabric se sadou [Service Fabric SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/service-fabric/remove-application/remove-application.ps1 "Remove an application from a cluster")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Remove-ServiceFabricAplikace](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) | Odebere spuštěnou instanci aplikace Service Fabric z clusteru.  |
| [Zrušit registraci ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Zruší registraci typu a verze aplikace Service Fabric z clusteru. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Service Fabric PowerShell najdete v [dokumentaci k Prostředí Azure PowerShell](/powershell/azure/service-fabric/?view=azureservicefabricps).

Další ukázky PowerShellu pro Azure Service Fabric najdete v [ukázkách Azure PowerShellu](../service-fabric-powershell-samples.md).
