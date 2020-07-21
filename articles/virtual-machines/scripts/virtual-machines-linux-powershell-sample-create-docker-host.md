---
title: Ukázkový skript Azure PowerShellu – Docker
description: Ukázkový skript Azure PowerShellu – Docker
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
ms.date: 03/02/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6e9fbf4f9e571813bc27f962a2510e380fcd3532
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86509677"
---
# <a name="create-a-docker-host-with-powershell"></a>Vytvoření hostitele Dockeru pomocí PowerShellu

Tento skript vytvoří virtuální počítač s povoleným Dockerem a spustí kontejner se serverem NGINX. Po spuštění skriptu můžete k webovému serveru NGINX přistupovat přes plně kvalifikovaný název domény virtuálního počítače Azure.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-docker-host/create-docker-host.ps1 "Create Docker host")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript pomocí následujících příkazů vytvoří nasazení. Každá položka v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Vytvoří konfiguraci podsítě. Tato konfigurace se použije v procesu vytváření virtuální sítě. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Vytvoří virtuální síť. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Vytvoří veřejnou IP adresu. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | Vytvoří konfiguraci pravidla skupiny zabezpečení sítě. Tato konfigurace se použije k vytvoření pravidla skupiny zabezpečení sítě při jejím vytváření. |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Vytvoří skupinu zabezpečení sítě. |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Získá informace o podsíti. Tyto informace se použijí při vytváření síťového rozhraní. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Vytvoří síťové rozhraní. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Vytvoří konfiguraci virtuálního počítače. Tato konfigurace zahrnuje informace, jako je název virtuálního počítače, operační systém a přihlašovací údaje pro správu. Tato konfigurace se použije při vytváření virtuálního počítače. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Vytvoří virtuální počítač. |
| [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) | Přidá do virtuálního počítače rozšíření virtuálního počítače. V této ukázce se rozšíření Dockeru používá ke konfiguraci Dockeru a spuštění kontejneru Dockeru se serverem NGINX. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odebere skupinu prostředků a všechny prostředky, které obsahuje. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro virtuální počítače najdete v [dokumentaci k virtuálním počítačům Azure s Linuxem](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
