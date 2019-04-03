---
title: Vytvoření virtuálního počítače s Windows pomocí šablony v Azure | Dokumentace Microsoftu
description: Můžete snadno vytvořit nový virtuální počítač s Windows pomocí šablony Resource Manageru a Powershellu.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 19129d61-8c04-4aa9-a01f-361a09466805
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c1ba96c76c51abcfe5bb5ef9bd66eb8a50afdfda
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2019
ms.locfileid: "58846620"
---
# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>Vytvoření virtuálního počítače s Windows pomocí šablony Resource Manageru

Zjistěte, jak vytvořit virtuální počítač s Windows pomocí šablony Azure Resource Manageru a Azure Powershellu služby ze služby Azure Cloud shell. Šablona použitá v tomto článku nasadíte jeden virtuální počítač s Windows serverem v nové virtuální sítě s jedinou podsítí. Vytvoření virtuálního počítače s Linuxem, naleznete v tématu [jak vytvořit virtuální počítač s Linuxem pomocí šablony Azure Resource Manageru](../linux/create-ssh-secured-vm-from-template.md).

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvoření virtuálního počítače Azure obvykle zahrnuje dva kroky:

- Vytvořte skupinu prostředků. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Skupina prostředků musí být vytvořená už před vytvořením virtuálního počítače.
- Vytvoří virtuální počítač.

Následující příklad vytvoří virtuální počítač ze [šablona Azure Quickstart](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json). Tady je kopie šablony:

[!code-json[create-windows-vm](~/quickstart-templates/101-vm-simple-windows/azuredeploy.json)]

Chcete-li spustit skript prostředí PowerShell, vyberte **vyzkoušet** a otevřete Azure Cloud shell. Vložte skript, klikněte pravým tlačítkem na prostředí a pak vyberte **vložte**:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString
$dnsLabelPrefix = Read-Host -Prompt "Enter an unique DNS name for the public IP"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json" `
    -adminUsername $adminUsername `
    -adminPassword $adminPassword `
    -dnsLabelPrefix $dnsLabelPrefix

 (Get-AzVm -ResourceGroupName $resourceGroupName).name

```

Pokud se rozhodnete nainstalovat a používat PowerShell místně namísto sady ze služby Azure Cloud shell, tento kurz vyžaduje modul Azure PowerShell. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, musíte také spustit `Connect-AzAccount` vytvořit připojení k Azure.

V předchozím příkladu jste zadali šablony uložené na Githubu. Můžete také stáhnout nebo vytvořit šablonu a zadejte do místní cesty `--template-file` parametru.

Tady jsou některé další prostředky:

- Zjistěte, jak vyvíjet šablony Resource Manageru, najdete v článku [dokumentace ke službě Azure Resource Manageru](/azure/azure-resource-manager/).
- Schémata virtuálních počítačů Azure najdete v tématu [referenčními informacemi k šablonám Azure](/azure/templates/microsoft.compute/allversions).
- Další ukázkové šablony virtuálního počítače najdete v tématu [šablon rychlého startu Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-the-virtual-machine"></a>Připojení k virtuálnímu počítači

Poslední příkaz prostředí PowerShell z předchozí skript ukazuje název virtuálního počítače. Připojení k virtuálnímu počítači najdete v tématu [jak se připojit a přihlaste se k virtuálnímu počítači Azure s Windows](./connect-logon.md).

## <a name="next-steps"></a>Další kroky

- Pokud byly nějaké problémy s nasazením, může trvat podívat [řešit běžné chyby nasazení v Azure pomocí Azure Resource Manageru](../../resource-manager-common-deployment-errors.md).
- Zjistěte, jak vytvářet a spravovat virtuální počítač v [vytvoření a správa virtuálních počítačů s Windows pomocí modulu Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Další informace o vytváření šablon, zobrazí se syntaxe JSON a vlastnosti pro typy prostředků, které jste nasadili:

- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
