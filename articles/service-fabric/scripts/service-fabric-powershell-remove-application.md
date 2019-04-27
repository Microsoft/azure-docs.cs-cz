---
title: Ukázkový skript Azure Powershellu – odebrat aplikaci z clusteru | Dokumentace Microsoftu
description: Ukázkový skript Azure Powershellu – odebrat aplikaci z clusteru Service Fabric.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: c8956b8bbbbe6d0928511da3cf75bfd23a76ed21
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621770"
---
# <a name="remove-an-application-from-a-service-fabric-cluster"></a>Odebrání aplikace z clusteru Service Fabric

Tento ukázkový skript odstraní spuštěné instance aplikace Service Fabric a zruší registraci typ a verze aplikace z clusteru.  Odstraněním instance aplikace dojde také k odstranění všech spuštěných instancí služby přidružených dané aplikaci. Podle potřeby upravte parametry. 

V případě potřeby nainstalujte modul PowerShellu pro Service Fabric se sadou [Service Fabric SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/service-fabric/remove-application/remove-application.ps1 "Remove an application from a cluster")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) | Spuštěné instance aplikace Service Fabric se odebere z clusteru.  |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Zruší registraci typu aplikace Service Fabric a verzi z clusteru. |

## <a name="next-steps"></a>Další postup

Další informace o modulu Powershellu pro Service Fabric najdete v tématu [dokumentaci k Azure Powershellu](/powershell/azure/service-fabric/?view=azureservicefabricps).

Další ukázky PowerShellu pro Azure Service Fabric najdete v [ukázkách Azure PowerShellu](../service-fabric-powershell-samples.md).
