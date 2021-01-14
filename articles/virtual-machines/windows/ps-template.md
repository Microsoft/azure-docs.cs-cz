---
title: Vytvoření virtuálního počítače s Windows ze šablony v Azure
description: K snadnému vytvoření nového virtuálního počítače s Windows použijte šablonu Správce prostředků a PowerShell.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: how-to
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c327f5ffbf7c0fbfadf443e80cc1f7540855f59e
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201820"
---
# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>Vytvoření virtuálního počítače s Windows ze šablony Resource Manageru

Naučte se, jak vytvořit virtuální počítač s Windows pomocí šablony Azure Resource Manager a Azure PowerShell z Azure Cloud shellu. Šablona použitá v tomto článku nasadí jeden virtuální počítač se systémem Windows Server v nové virtuální síti s jednou podsítí. Informace o vytvoření virtuálního počítače se systémem Linux najdete v tématu [Vytvoření virtuálního počítače se systémem Linux pomocí šablon Azure Resource Manager](../linux/create-ssh-secured-vm-from-template.md).

Alternativou je nasazení šablony z Azure Portal. Pokud chcete otevřít šablonu na portálu, vyberte tlačítko **nasadit do Azure** .

[![Nasazení do Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-simple-windows%2Fazuredeploy.json)

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvoření virtuálního počítače Azure obvykle zahrnuje dva kroky:

- Vytvořte skupinu prostředků. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Skupina prostředků musí být vytvořená už před vytvořením virtuálního počítače.
- Vytvoří virtuální počítač.

Následující příklad vytvoří virtuální počítač ze [šablony Azure pro rychlý Start](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json). Tady je kopie šablony:

[!code-json[create-windows-vm](~/quickstart-templates/101-vm-simple-windows/azuredeploy.json)]

Pokud chcete spustit skript PowerShellu, vyberte **vyzkoušet** a otevřete službu Azure Cloud Shell. Skript vložíte tak, že kliknete pravým tlačítkem na prostředí a pak vyberete **Vložit**:

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

Pokud se rozhodnete nainstalovat a používat PowerShell místně místo z Azure Cloud shellu, tento kurz vyžaduje modul Azure PowerShell. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte prostředí PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

V předchozím příkladu jste zadali šablonu uloženou v GitHubu. Můžete si také stáhnout nebo vytvořit šablonu a zadat místní cestu s `--template-file` parametrem.

Tady je několik dalších zdrojů:

- Další informace o vývoji šablon Správce prostředků najdete v [dokumentaci k Azure Resource Manager](../../azure-resource-manager/index.yml).
- Schémata virtuálních počítačů Azure najdete v tématu Referenční informace k [šablonám Azure](/azure/templates/microsoft.compute/allversions).
- Další ukázky šablon virtuálních počítačů najdete v tématu [šablony pro rychlý Start pro Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-the-virtual-machine"></a>Připojení k virtuálnímu počítači

Poslední příkaz prostředí PowerShell z předchozího skriptu zobrazuje název virtuálního počítače. Pokud se chcete připojit k virtuálnímu počítači, přečtěte si téma [jak se připojit a přihlásit se k virtuálnímu počítači Azure s Windows](./connect-logon.md).

## <a name="next-steps"></a>Další kroky

- V případě, že došlo k potížím s nasazením, můžete se podívat na problémy s [běžnými chybami při nasazení Azure pomocí Azure Resource Manager](../../azure-resource-manager/templates/common-deployment-errors.md).
- Naučte se vytvářet a spravovat virtuální počítače v tématu [vytváření a správa virtuálních počítačů s Windows pomocí modulu Azure PowerShell](tutorial-manage-vm.md).

Další informace o vytváření šablon najdete v syntaxi a vlastnostech JSON pro typy prostředků, které jste nasadili:

- [Microsoft. Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft. Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft. COMPUTE/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
