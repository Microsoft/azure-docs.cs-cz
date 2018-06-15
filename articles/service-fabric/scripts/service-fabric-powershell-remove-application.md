---
title: Azure ukázkový skript prostředí PowerShell - aplikaci odebrat z clusteru s podporou | Microsoft Docs
description: Azure ukázkový skript prostředí PowerShell - odebrat aplikaci z clusteru Service Fabric.
services: service-fabric
documentationcenter: ''
author: rwike77
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 01/18/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: ea7037fc3655298fc4c03b8d9f988a55e42c9fe9
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2018
ms.locfileid: "27927858"
---
# <a name="remove-an-application-from-a-service-fabric-cluster"></a>Odebrání aplikace z clusteru Service Fabric

Tento ukázkový skript odstraní spuštěné instance aplikace Service Fabric a zrušení registrace typ a verze aplikace z clusteru.  Odstranění instance aplikace na také odstraní všechny spuštěné služby instance přidružené s touto aplikací. Podle potřeby upravte parametry. 

V případě potřeby nainstalujte modul Service Fabric prostředí PowerShell s [Service Fabric SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/service-fabric/remove-application/remove-application.ps1 "Remove an application from a cluster")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazy na dokumentaci konkrétní příkaz.

| Příkaz | Poznámky |
|---|---|
| [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) | Odebere spuštěné instance aplikace Service Fabric z clusteru.  |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Zrušení registrace typu aplikace Service Fabric a verze z clusteru. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Service Fabric prostředí PowerShell najdete v tématu [dokumentace Azure PowerShell](/powershell/azure/service-fabric/?view=azureservicefabricps).

Další ukázky pro Azure Service Fabric Powershell najdete v [prostředí Azure PowerShell ukázky](../service-fabric-powershell-samples.md).
