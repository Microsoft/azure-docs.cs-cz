---
title: Ukázkový skript Azure PowerShellu – Změna rozsahu portů RDP | Microsoft Docs
description: Ukázkový skript Azure PowerShellu – Změna rozsahu portů RDP u nasazeného clusteru
services: service-fabric
tags: azure-service-management
author: athinanthny
ms.author: atsenthi
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 03/19/2018
ms.custom: sfrev
ms.openlocfilehash: 5cc72423d7e901f82cdf7e40f5de72bd54e9cec1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87076178"
---
# <a name="update-the-rdp-port-range-values"></a>Aktualizace hodnot rozsahu portů RDP

Tento ukázkový skript po nasazení clusteru změní hodnoty rozsahu portů RDP na virtuálních počítačích uzlu clusteru.  Aby se základní virtuální počítače nezacyklily, používá se Azure PowerShell.  Skript získá `Microsoft.Network/loadBalancers` prostředek ve skupině prostředků clusteru a aktualizuje `inboundNatPools.frontendPortRangeStart` `inboundNatPools.frontendPortRangeEnd` hodnoty a. Podle potřeby upravte parametry.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

V případě potřeby nainstalujte Azure PowerShell podle pokynů uvedených v [příručce k Azure PowerShellu](/powershell/azure/).

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-port-range/change-rdp-port-range.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Získá prostředek `Microsoft.Network/loadBalancers`. |
|[Set-AzResource](/powershell/module/az.resources/set-azresource)|Aktualizuje prostředek `Microsoft.Network/loadBalancers`.|

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/).

Další ukázky Azure PowerShellu pro Azure Service Fabric najdete v [ukázkách Azure PowerShellu](../service-fabric-powershell-samples.md).
