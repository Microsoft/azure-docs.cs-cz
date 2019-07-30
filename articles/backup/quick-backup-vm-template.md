---
title: Rychlý Start Azure – zálohování virtuálního počítače pomocí šablony Správce prostředků
description: Naučte se zálohovat virtuální počítače pomocí šablony Azure Resource Manager.
author: dcurwin
manager: carmonm
ms.service: backup
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/14/2019
ms.author: dacurwin
ms.custom: mvc
ms.openlocfilehash: 7075e127192635c08aa2da2b4798dea6d6abb13b
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639410"
---
# <a name="back-up-a-virtual-machine-in-azure-with-resource-manager-template"></a>Zálohování virtuálního počítače v Azure pomocí šablony Správce prostředků

[Azure Backup](backup-overview.md) zálohuje místní počítače a aplikace a virtuální počítače Azure. V tomto článku se dozvíte, jak zálohovat virtuální počítač Azure pomocí Správce prostředků šablony a Azure PowerShell. Tento rychlý Start se zaměřuje na proces nasazení šablony Správce prostředků pro vytvoření trezoru Recovery Services. Další informace o vývoji šablon Správce prostředků naleznete v [dokumentaci správce prostředků](/azure/azure-resource-manager/) a odkazu na [šablonu](/azure/templates/microsoft.recoveryservices/allversions).

Alternativně můžete virtuální počítač zálohovat pomocí [Azure PowerShell](./quick-backup-vm-powershell.md), rozhraní příkazového [řádku Azure](quick-backup-vm-cli.md)nebo v [Azure Portal](quick-backup-vm-portal.md).

## <a name="create-a-vm-and-recovery-services-vault"></a>Vytvoření virtuálního počítače a trezoru Recovery Services

[Recovery Services trezor](backup-azure-recovery-services-vault-overview.md) je logický kontejner, který ukládá zálohovaná data pro chráněné prostředky, jako jsou například virtuální počítače Azure. Při spuštění úlohy zálohování se v rámci Recovery Services trezoru vytvoří bod obnovení. Pomocí některého z těchto bodů obnovení pak můžete obnovit data k danému bodu v čase.

Šablona použitá v tomto rychlém startu je ze [šablon Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/). Tato šablona umožňuje nasazení jednoduchého virtuálního počítače s Windows a Recovery Services trezoru nakonfigurovaného s DefaultPolicy pro ochranu.

Pokud chcete šablonu nasadit, vyberte **zkusit znovu** a otevřete službu Azure Cloud Shell a vložte do okna prostředí následující skript PowerShellu. Kód vložíte tak, že kliknete pravým tlačítkem myši na okno prostředí a pak vyberete **Vložit**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name (limited to eight characters) that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username for the virtual machine"
$adminPassword = Read-Host -Prompt "Enter the administrator password for the virtual machine" -AsSecureString
$dnsPrefix = Read-Host -Prompt "Enter the unique DNS Name for the Public IP used to access the virtual machine"

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -adminUsername $adminUsername -adminPassword $adminPassword -dnsLabelPrefix $dnsPrefix
```

Azure PowerShell slouží k nasazení Správce prostředků šablony v rámci tohoto rychlého startu. K nasazení šablon můžete také použít [Azure Portal](../azure-resource-manager/resource-group-template-deploy-portal.md), rozhraní příkazového [řádku Azure](../azure-resource-manager/resource-group-template-deploy-cli.md)a [rozhraní REST API](../azure-resource-manager/resource-group-template-deploy-rest.md) .

## <a name="start-a-backup-job"></a>Spuštění úlohy zálohování

Šablona vytvoří virtuální počítač a povolí zpátky na VIRTUÁLNÍm počítači. Po nasazení šablony je nutné spustit úlohu zálohování. Další informace najdete v tématu [spuštění úlohy zálohování](./quick-backup-vm-powershell.md#start-a-backup-job).

## <a name="monitor-the-backup-job"></a>Monitorování úlohy zálohování

Chcete-li monitorovat úlohu zálohování, přečtěte si téma [monitorování úlohy zálohování](./quick-backup-vm-powershell.md#monitor-the-backup-job).

## <a name="clean-up-the-deployment"></a>Vyčištění nasazení

Pokud už nepotřebujete zálohovat virtuální počítač, můžete ho vyčistit.

- Pokud chcete vyzkoušet obnovení virtuálního počítače, přeskočte vyčištění.
- Pokud jste použili existující virtuální počítač, můžete přeskočit poslední rutinu [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) a ponechat skupinu prostředků a virtuální počítač na místě.

Zakažte ochranu, odeberte body obnovení a trezor. Pak odstraňte skupinu prostředků a související prostředky virtuálního počítače následujícím způsobem:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili trezor služby Recovery Services, povolili ochranu virtuálního počítače a vytvořili prvotní bod obnovení.

- Naučte se, [jak](tutorial-backup-vm-at-scale.md) zálohovat virtuální počítače v Azure Portal.
- [Zjistěte, jak](tutorial-restore-disk.md) rychle obnovit virtuální počítač.
