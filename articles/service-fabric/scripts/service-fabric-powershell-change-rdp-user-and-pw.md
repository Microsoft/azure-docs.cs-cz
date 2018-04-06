---
title: Ukázkový skript Azure PowerShellu – Aktualizace uživatelského jména a hesla protokolu RDP | Microsoft Docs
description: Ukázkový skript Azure PowerShellu – Aktualizace uživatelského jména a hesla protokolu RDP pro všechny uzly clusteru Service Fabric konkrétního typu
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
ms.date: 03/19/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: ff9cfabc4ac7b759a916ddaaeb3f4c95ceecd452
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="update-the-admin-username-and-password-of-the-vms-in-a-cluster"></a>Aktualizace uživatelského jména a hesla správce virtuálních počítačů v clusteru

Každý [typ uzlu](../service-fabric-cluster-nodetypes.md) v clusteru Service Fabric je škálovací sada virtuálních počítačů. Tento ukázkový skript aktualizuje uživatelské jméno a heslo správce virtuálních počítačů v clusteru na konkrétním typu uzlu.  Přidejte do škálovací sady rozšíření VMAccessAgent, protože heslo správce není upravitelná vlastnost škálovací sady.  Změny uživatelského jména a hesla se použijí pro všechny uzly ve škálovací sadě. Podle potřeby upravte parametry.

V případě potřeby nainstalujte Azure PowerShell podle pokynů uvedených v [příručce k Azure PowerShellu](/powershell/azure/overview). 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-user-and-pw/change-rdp-user-and-pw.ps1 "Updates a RDP username and password for cluster nodes")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy: Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) | Získá vlastnosti typu uzlu clusteru (škálovací sada virtuálních počítačů).   |
| [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension)| Přidá do škálovací sady virtuálních počítačů rozšíření.|
| [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss)|Aktualizuje stav škálovací sady virtuálních počítačů na stav místního objektu škálovací sady virtuálních počítačů.|

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázky Azure PowerShellu pro Azure Service Fabric najdete v [ukázkách Azure PowerShellu](../service-fabric-powershell-samples.md).
