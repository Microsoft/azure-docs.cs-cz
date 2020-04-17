---
title: Ukázkový skript Azure PowerShellu – WordPress
description: Ukázkový skript Azure PowerShellu – WordPress
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 78a7d284df2b1cf30965f1aadf43b4461e6b8de7
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459657"
---
# <a name="create-a-wordpress-vm-with-powershell"></a>Vytvoření virtuálního počítače WordPress pomocí PowerShellu

Tento skript vytvoří virtuální počítač a pomocí rozšíření vlastních skriptů virtuálního počítače Azure nainstaluje WordPress. Po spuštění skriptu můžete ke konfiguračnímu webu WordPressu přistupovat na adrese `http://<public IP of VM>/wordpress`.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-wordpress-mysql/create-wordpress-mysql.ps1 "Create VM WordPress")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript pomocí následujících příkazů vytvoří nasazení. Každá položka v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [Nová-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Vytvoří konfiguraci podsítě. Tato konfigurace se použije v procesu vytváření virtuální sítě. |
| [Nová virtuální síť Az](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) | Vytvoří virtuální síť. |
| [Nová adresa AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) | Vytvoří veřejnou IP adresu. |
| [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig) | Vytvoří konfiguraci pravidla skupiny zabezpečení sítě. Tato konfigurace se použije k vytvoření pravidla skupiny zabezpečení sítě při jejím vytváření. |
| [Nová skupina zabezpečení sítě AzNetwork](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup) | Vytvoří skupinu zabezpečení sítě. |
| [Get-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Získá informace o podsíti. Tyto informace se použijí při vytváření síťového rozhraní. |
| [Nové rozhraní AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) | Vytvoří síťové rozhraní. |
| [Nový-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) | Vytvoří konfiguraci virtuálního počítače. Tato konfigurace zahrnuje informace, jako je název virtuálního počítače, operační systém a přihlašovací údaje pro správu. Tato konfigurace se použije při vytváření virtuálního počítače. |
| [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) | Vytvoří virtuální počítač. |
| [Rozšíření Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) | Přidá do virtuálního počítače rozšíření vlastních skriptů, které vyvolá skript pro instalaci WordPressu. |
|[Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Odebere skupinu prostředků a všechny prostředky, které obsahuje. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro virtuální počítače najdete v [dokumentaci k virtuálním počítačům Azure s Linuxem](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
