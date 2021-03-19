---
title: Aktualizace uživatelského jména a hesla protokolu RDP v PowerShellu
description: Ukázkový skript Azure PowerShellu – Aktualizace uživatelského jména a hesla protokolu RDP pro všechny uzly clusteru Service Fabric konkrétního typu
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
ms.date: 03/19/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: bcf619e2251f5c1b641190549da45f721835ce0e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87076146"
---
# <a name="update-the-admin-username-and-password-of-the-vms-in-a-cluster"></a>Aktualizace uživatelského jména a hesla správce virtuálních počítačů v clusteru

Každý [typ uzlu](../service-fabric-cluster-nodetypes.md) v clusteru Service Fabric je škálovací sada virtuálních počítačů. Tento ukázkový skript aktualizuje uživatelské jméno a heslo správce virtuálních počítačů v clusteru na konkrétním typu uzlu.  Přidejte do škálovací sady rozšíření VMAccessAgent, protože heslo správce není upravitelná vlastnost škálovací sady.  Změny uživatelského jména a hesla se použijí pro všechny uzly ve škálovací sadě. Podle potřeby upravte parametry.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

V případě potřeby nainstalujte Azure PowerShell podle pokynů uvedených v [příručce k Azure PowerShellu](/powershell/azure/). 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-user-and-pw/change-rdp-user-and-pw.ps1 "Updates a RDP username and password for cluster nodes")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy: Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Get-AzVmss](/powershell/module/az.compute/get-azvmss) | Získá vlastnosti typu uzlu clusteru (škálovací sada virtuálních počítačů).   |
| [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension)| Přidá do škálovací sady virtuálních počítačů rozšíření.|
| [Update – AzVmss](/powershell/module/az.compute/update-azvmss)|Aktualizuje stav škálovací sady virtuálních počítačů na stav místního objektu škálovací sady virtuálních počítačů.|

## <a name="duration"></a>Doba trvání

Jeden typ uzlu s pěti uzly má například dobu trvání 45 až 60 minut ke změně uživatelského jména nebo hesla. 

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/).

Další ukázky Azure PowerShellu pro Azure Service Fabric najdete v [ukázkách Azure PowerShellu](../service-fabric-powershell-samples.md).
