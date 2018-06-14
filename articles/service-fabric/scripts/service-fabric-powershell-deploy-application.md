---
title: Azure skript prostředí PowerShell ukázkový – nasazení aplikace do clusteru s podporou | Microsoft Docs
description: Azure skript prostředí PowerShell ukázkový – nasazení aplikace do clusteru Service Fabric.
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
ms.openlocfilehash: c81514fb4b1c1da483ebd55deae149caf22d4b63
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2018
ms.locfileid: "27927596"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Nasazení aplikace do clusteru Service Fabric

Tento ukázkový skript zkopíruje balíček aplikace do úložiště bitové kopie clusteru, zaregistruje typ aplikace v clusteru, odebere balíček nepotřebné aplikace a vytvoří instanci aplikace z typu aplikace.  Pokud žádné výchozí služby byly definovány v manifestu aplikace cílového typu aplikace, vytvoří se v tuto chvíli těchto služeb. Podle potřeby upravte parametry. 

V případě potřeby nainstalujte modul Service Fabric prostředí PowerShell s [Service Fabric SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/service-fabric/deploy-application/deploy-application.ps1 "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Po ukázka skriptu spuštění, skript [odebrání aplikace](service-fabric-powershell-remove-application.md) slouží k odebrání instance aplikace, zrušení registrace typu aplikace a odstranění balíčku aplikace z úložiště bitových kopií.

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazy na dokumentaci konkrétní příkaz.

| Příkaz | Poznámky |
|---|---|
|[Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)| Vytvoří připojení ke clusteru Service Fabric. |
|[Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Kopie balíček aplikace pro bitovou kopii clusteru úložiště.  |
|[Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)| Zaregistruje typ a verze aplikace v clusteru. |
|[New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps)| Vytvoří aplikace z typu zaregistrovanou aplikaci. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Balíček aplikace Service Fabric se odebere z úložiště bitových kopií.|

## <a name="next-steps"></a>Další kroky

Další informace o modulu Service Fabric prostředí PowerShell najdete v tématu [dokumentace Azure PowerShell](/powershell/azure/service-fabric/?view=azureservicefabricps).

Další ukázky pro Azure Service Fabric Powershell najdete v [prostředí Azure PowerShell ukázky](../service-fabric-powershell-samples.md).
