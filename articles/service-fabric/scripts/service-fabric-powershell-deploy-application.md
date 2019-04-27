---
title: Skript Azure Powershellu ukázkový – nasazení aplikace do clusteru | Dokumentace Microsoftu
description: Skript Azure Powershellu ukázkový – nasazení aplikace do clusteru Service Fabric.
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 01/18/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 6de7a721a5042a20537d3f09ea9b8de1cc0e7169
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622029"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Nasazení aplikace do clusteru Service Fabric

Tento ukázkový skript zkopíruje balíček aplikace do úložiště imagí clusteru, zaregistruje typ aplikace v clusteru, odebere balíček nepotřebných aplikací a vytvoří instanci aplikace z typu aplikace.  Pokud žádné výchozí služby byly definovány v manifestu aplikace typ cílové aplikace, vytvoří se v tuto chvíli těchto služeb. Podle potřeby upravte parametry. 

V případě potřeby nainstalujte modul PowerShellu pro Service Fabric se sadou [Service Fabric SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/service-fabric/deploy-application/deploy-application.ps1 "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Po ukázkového skriptu spuštění skriptu [odebrat aplikaci](service-fabric-powershell-remove-application.md) slouží k odebrání instance aplikace, zrušte registraci typu aplikace a odstranit balíček aplikace z úložiště imagí.

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
|[Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)| Vytvoří připojení ke clusteru Service Fabric. |
|[Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Kopie ukládání balíčku aplikace k bitové kopii clusteru.  |
|[Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)| Zaregistruje typ a verze aplikace v clusteru. |
|[New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps)| Vytvoří aplikaci z typu registrované aplikaci. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Odebere balíček aplikace Service Fabric z úložiště imagí.|

## <a name="next-steps"></a>Další postup

Další informace o modulu Powershellu pro Service Fabric najdete v tématu [dokumentaci k Azure Powershellu](/powershell/azure/service-fabric/?view=azureservicefabricps).

Další ukázky PowerShellu pro Azure Service Fabric najdete v [ukázkách Azure PowerShellu](../service-fabric-powershell-samples.md).
