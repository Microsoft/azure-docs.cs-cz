---
title: Nasazení aplikace do clusteru v Powershellu
description: Ukázka skriptu Azure PowerShell – nasazení aplikace do clusteru Service Fabric.
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
ms.openlocfilehash: 207f2a4e8173aa1e5009435665532973045d9198
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75610297"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Nasazení aplikace do clusteru Service Fabric

Tento ukázkový skript zkopíruje balíček aplikace do úložiště bitových kopií clusteru, zaregistruje typ aplikace v clusteru, odebere nepotřebný balíček aplikace a vytvoří instanci aplikace z typu aplikace.  Pokud byly definovány všechny výchozí služby v manifestu aplikace cílového typu aplikace, jsou tyto služby vytvořeny v tomto okamžiku. Podle potřeby upravte parametry. 

V případě potřeby nainstalujte modul PowerShellu pro Service Fabric se sadou [Service Fabric SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/service-fabric/deploy-application/deploy-application.ps1 "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Po spuštění ukázky skriptu lze skript v [části Odebrat aplikaci](service-fabric-powershell-remove-application.md) použít k odebrání instance aplikace, zrušení registrace typu aplikace a odstranění balíčku aplikace z úložiště obrázků.

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
|[Připojit servicefabriccluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)| Vytvoří připojení ke clusteru Service Fabric. |
|[Kopírovat ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Zkopíruje balíček aplikace do úložiště bitových kopií clusteru.  |
|[Registr-ServiceFabricTyp aplikace](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)| Zaregistruje typ aplikace a verzi v clusteru. |
|[Aplikace New-ServiceFabric](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps)| Vytvoří aplikaci z registrovaného typu aplikace. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Odebere balíček aplikace Service Fabric z úložiště obrázků.|

## <a name="next-steps"></a>Další kroky

Další informace o modulu Service Fabric PowerShell najdete v [dokumentaci k Prostředí Azure PowerShell](/powershell/azure/service-fabric/?view=azureservicefabricps).

Další ukázky PowerShellu pro Azure Service Fabric najdete v [ukázkách Azure PowerShellu](../service-fabric-powershell-samples.md).
