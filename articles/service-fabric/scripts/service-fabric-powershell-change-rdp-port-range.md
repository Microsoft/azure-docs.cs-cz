---
title: Ukázkový skript Azure PowerShellu – Změna rozsahu portů RDP | Microsoft Docs
description: Ukázkový skript Azure PowerShellu – Změna rozsahu portů RDP u nasazeného clusteru
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
ms.date: 03/19/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 4470cf8898f1d174f8ec2bb6860e2e30aeff9fe2
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592308"
---
# <a name="update-the-rdp-port-range-values"></a>Aktualizace hodnot rozsahu portů RDP

Tento ukázkový skript po nasazení clusteru změní hodnoty rozsahu portů RDP na virtuálních počítačích uzlu clusteru.  Aby se základní virtuální počítače nezacyklily, používá se Azure PowerShell.  Skript získá prostředek `Microsoft.Network/loadBalancers` ve skupině prostředků clusteru a aktualizuje hodnoty `inboundNatPools.frontendPortRangeStart` a `inboundNatPools.frontendPortRangeEnd`. Podle potřeby upravte parametry.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

V případě potřeby nainstalujte Azure PowerShell podle pokynů uvedených v [příručce k Azure PowerShellu](/powershell/azure/overview). 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-port-range/change-rdp-port-range.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Získá prostředek `Microsoft.Network/loadBalancers`. |
|[Set-AzResource](/powershell/module/az.resources/set-azresource)|Aktualizuje prostředek `Microsoft.Network/loadBalancers`.|

## <a name="next-steps"></a>Další postup

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázky Azure PowerShellu pro Azure Service Fabric najdete v [ukázkách Azure PowerShellu](../service-fabric-powershell-samples.md).
