---
title: Ukázkový skript Azure PowerShellu – Vytvoření sítě pro vícevrstvé aplikace | Microsoft Docs
description: Ukázkový skript Azure PowerShellu – Vytvoření virtuální sítě pro vícevrstvé aplikace
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 12/13/2018
ms.author: kumud
ms.openlocfilehash: 2fad78db4fdc92f3dc9c0f320c36d12dea554a61
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64725383"
---
# <a name="create-a-network-for-multi-tier-applications-script-sample"></a>Ukázkový skript pro vytvoření sítě pro vícevrstvé aplikace

Tento ukázkový skript vytvoří virtuální síť s front-endovou a back-endovou podsítí. Provoz do front-endové podsítě je omezený na HTTP a SSH, zatímco provoz do back-endové podsítě je omezený na MySQL na portu 3306. Po spuštění skriptu budete mít dva virtuální počítače, v každé podsíti jeden, na které můžete nasadit webový server a software MySQL.

Skript můžete spustit ve službě Azure [Cloud Shell](https://shell.azure.com/powershell) nebo v místně nainstalovaném PowerShellu. Pokud používáte PowerShell místně, vyžaduje tento skript modul Azure PowerShell verze 1.0.0 nebo novějším. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

<!-- gitHub issue https://github.com/MicrosoftDocs/azure-docs/issues/17748 -->
ID podsítě je přiřazena již vytvořené virtuální síti. Konkrétně, pomocí rutiny New-AzVirtualNetwork s parametrem - podsítě. Při konfiguraci podsítě pomocí rutiny New-AzVirtualNetworkSubnetConfig před voláním do New-AzVirtualNetwork, neuvidíte ID podsítě do po zavolání AzVirtualNetwork nový.

[!code-azurepowershell-interactive[main](../../../powershell_scripts/virtual-network/virtual-network-multi-tier-application/virtual-network-multi-tier-application.ps1  "Virtual network for multi-tier application")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky:

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, virtuální sítě a skupin zabezpečení sítě používá následující příkazy. Každý příkaz v následující tabulce odkazuje na příslušnou část dokumentace:

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Vytvoří virtuální síť Azure a front-endovou podsíť. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Vytvoří back-endovou podsíť. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Vytvoří veřejnou IP adresu pro přístup k virtuálnímu počítači z internetu. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Vytvoří virtuální síťová rozhraní a připojí je k front-endové a back-endové podsíti virtuální sítě. |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Vytvoří skupiny zabezpečení sítě (NSG), které se přidruží k front-endové a back-endové podsíti. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) |Vytvoří pravidla NSG, která povolí nebo zablokují konkrétní porty v konkrétních podsítích. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Vytvoří virtuální počítače a ke každému z nich připojí síťovou kartu. Tento příkaz také určuje image virtuálního počítače, která se má použít, a přihlašovací údaje pro správu. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků a všechny prostředky, které obsahuje. |

## <a name="next-steps"></a>Další postup

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro virtuální síť najdete v tématu [Ukázky PowerShellu pro virtuální síť](../powershell-samples.md).
