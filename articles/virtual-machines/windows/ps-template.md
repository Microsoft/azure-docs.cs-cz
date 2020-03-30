---
title: Vytvoření virtuálního počítače s Windows ze šablony v Azure
description: Pomocí šablony Správce prostředků a prostředí PowerShell můžete snadno vytvořit nový virtuální virtuální počítače se systémem Windows.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 19129d61-8c04-4aa9-a01f-361a09466805
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 99e292930414ae027c9cbbf3a901d550041899d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74065552"
---
# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>Vytvoření virtuálního počítače s Windows ze šablony Správce prostředků

Zjistěte, jak vytvořit virtuální počítač s Windows pomocí šablony Azure Resource Manager a Azure PowerShell u prostředí Azure Cloud. Šablona použitá v tomto článku nasazuje jeden virtuální počítač se systémem Windows Server v nové virtuální síti s jednou podsítí. Pokud chcete vytvořit virtuální počítač s Linuxem, [přečtěte si, jak vytvořit virtuální počítač s Linuxem pomocí šablon Azure Resource Manager .](../linux/create-ssh-secured-vm-from-template.md)

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvoření virtuálního počítače Azure obvykle zahrnuje dva kroky:

- Vytvořte skupinu prostředků. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Skupina prostředků musí být vytvořená už před vytvořením virtuálního počítače.
- Vytvoří virtuální počítač.

Následující příklad vytvoří virtuální počítač ze [šablony Azure Quickstart](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json). Zde je kopie šablony:

[!code-json[create-windows-vm](~/quickstart-templates/101-vm-simple-windows/azuredeploy.json)]

Chcete-li spustit skript PowerShellu, vyberte **Try to** to open the Azure Cloud shell. Chcete-li skript vložit, klepněte pravým tlačítkem myši na prostředí a pak vyberte **Vložit**:

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

Pokud se rozhodnete nainstalovat a používat PowerShell místně místo z prostředí Azure Cloud, tento kurz vyžaduje modul Azure PowerShell. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, musíte taky `Connect-AzAccount` spustit k vytvoření připojení s Azure.

V předchozím příkladu jste zadali šablonu uloženou v GitHubu. Můžete také stáhnout nebo vytvořit šablonu a `--template-file` určit místní cestu s parametrem.

Tady je několik dalších zdrojů:

- Informace o vývoji šablon Správce prostředků najdete v [dokumentaci ke Správci prostředků Azure](/azure/azure-resource-manager/).
- Pokud jde o schémata virtuálních strojů Azure, přečtěte si informace [o odkazech na šablonu Azure](/azure/templates/microsoft.compute/allversions).
- Další ukázky šablon virtuálních strojů najdete v [tématu Šablony rychlých startů Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-the-virtual-machine"></a>Připojení k virtuálnímu počítači

Poslední příkaz PowerShellu z předchozího skriptu zobrazuje název virtuálního počítače. Pokud se chcete připojit k virtuálnímu počítači, přečtěte [si informace o tom, jak se připojit a přihlásit k virtuálnímu počítači Azure se systémem Windows](./connect-logon.md).

## <a name="next-steps"></a>Další kroky

- Pokud došlo k problémům s nasazením, můžete se podívat na [řešení běžných chyb nasazení Azure s Azure Resource Manager](../../resource-manager-common-deployment-errors.md).
- Zjistěte, jak vytvořit a spravovat virtuální počítač v [části Vytváření a správa virtuálních aplikací Windows pomocí modulu Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Další informace o vytváření šablon naleznete v syntaxi JSON a vlastnostech typů prostředků, které jste nasadili:

- [Microsoft.Network/publicIPAdresy](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
