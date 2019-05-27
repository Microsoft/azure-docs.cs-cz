---
title: Ukázkový skript Azure PowerShellu – Změna rozsahu portů RDP | Microsoft Docs
description: Ukázkový skript Azure PowerShellu – Změna rozsahu portů RDP u nasazeného clusteru
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
ms.date: 03/19/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 001574cef8f0ea074b65fdc94f6d8b255f98bff5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "66159943"
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
