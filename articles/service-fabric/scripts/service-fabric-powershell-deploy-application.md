---
title: Nasazení aplikace do clusteru v PowerShellu
description: Ukázkový skript Azure PowerShell – nasazení aplikace do clusteru Service Fabric
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
ms.openlocfilehash: 1d25ede5ae871eddd965594224b518ec42525451
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791286"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Nasazení aplikace do clusteru Service Fabric

Tento ukázkový skript zkopíruje balíček aplikace do úložiště imagí clusteru, zaregistruje typ aplikace v clusteru, odebere nepotřebný balíček aplikace a vytvoří instanci aplikace z typu aplikace.  Pokud byly v manifestu aplikace typu cílové aplikace definovány nějaké výchozí služby, pak se tyto služby v tuto chvíli vytvoří. Podle potřeby upravte parametry. 

V případě potřeby nainstalujte modul PowerShellu pro Service Fabric se sadou [Service Fabric SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/service-fabric/deploy-application/deploy-application.ps1 "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Po spuštění ukázkového skriptu lze pomocí skriptu v části [odebrat aplikaci](service-fabric-powershell-remove-application.md) odebrat instanci aplikace, zrušit registraci typu aplikace a odstranit balíček aplikace z úložiště imagí.

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
|[Connect – ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster)| Vytvoří připojení ke clusteru Service Fabric. |
|[Kopírování – ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) | Zkopíruje balíček aplikace do úložiště imagí clusteru.  |
|[Registrovat – ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype)| Zaregistruje typ a verzi aplikace v clusteru. |
|[New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication)| Vytvoří aplikaci z registrovaného typu aplikace. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage) | Odebere balíček aplikace Service Fabric z úložiště imagí.|

## <a name="next-steps"></a>Další kroky

Další informace o modulu Service Fabric PowerShellu najdete v [dokumentaci k Azure PowerShell](/powershell/azure/service-fabric/overview).

Další ukázky PowerShellu pro Azure Service Fabric najdete v [ukázkách Azure PowerShellu](../service-fabric-powershell-samples.md).
