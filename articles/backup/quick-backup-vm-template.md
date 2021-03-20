---
title: Rychlý Start – Správce prostředků šablonou zálohování virtuálních počítačů
description: Naučte se zálohovat virtuální počítače pomocí šablony Azure Resource Manager.
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/14/2019
ms.custom: mvc,subject-armqs
ms.openlocfilehash: e64aa10d8ddadd367d04b2b480770a99f3ece1dc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88826527"
---
# <a name="quickstart-back-up-a-virtual-machine-in-azure-with-an-arm-template"></a>Rychlý Start: zálohování virtuálního počítače v Azure pomocí šablony ARM

[Azure Backup](backup-overview.md) zálohuje místní počítače a aplikace a virtuální počítače Azure. V tomto článku se dozvíte, jak zálohovat virtuální počítač Azure pomocí šablony Azure Resource Manager (šablona ARM) a Azure PowerShell. Tento rychlý Start se zaměřuje na proces nasazení šablony ARM pro vytvoření trezoru Recovery Services. Další informace o vývoji šablon ARM naleznete v [dokumentaci Azure Resource Manager](../azure-resource-manager/index.yml) a v [referenčních](/azure/templates/microsoft.recoveryservices/allversions)informacích k šabloně.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

[Recovery Services trezor](backup-azure-recovery-services-vault-overview.md) je logický kontejner, který ukládá zálohovaná data pro chráněné prostředky, jako jsou například virtuální počítače Azure. Při spuštění úlohy zálohování se v rámci Recovery Services trezoru vytvoří bod obnovení. Pomocí některého z těchto bodů obnovení pak můžete obnovit data k danému bodu v čase. Alternativně můžete virtuální počítač zálohovat pomocí [Azure PowerShell](./quick-backup-vm-powershell.md), rozhraní příkazového [řádku Azure](quick-backup-vm-cli.md)nebo v [Azure Portal](quick-backup-vm-portal.md).

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-create-vm-and-configure-backup%2Fazuredeploy.json)

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/). Tato šablona umožňuje nasazení jednoduchého virtuálního počítače s Windows a Recovery Services trezoru nakonfigurovaného s DefaultPolicy pro ochranu.

:::code language="json" source="~/quickstart-templates/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json":::

Prostředky definované v šabloně jsou:

- [**Microsoft. Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts)
- [**Microsoft. Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft. COMPUTE/virutalMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft. RecoveryServices/trezory**](/azure/templates/microsoft.recoveryservices/2016-06-01/vaults)
- [**Microsoft. RecoveryServices/trezory/backupFabrics/protectionContainers/protectedItems**](/azure/templates/microsoft.recoveryservices/vaults/backupfabrics/protectioncontainers/protecteditems)

## <a name="deploy-the-template"></a>Nasazení šablony

Pokud chcete šablonu nasadit, vyberte **zkusit** , aby se Azure Cloud Shell otevřít, a pak do okna prostředí vložte následující skript PowerShellu. Kód vložíte tak, že kliknete pravým tlačítkem myši na okno prostředí a pak vyberete **Vložit**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name (limited to eight characters) that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (for example, centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username for the virtual machine"
$adminPassword = Read-Host -Prompt "Enter the administrator password for the virtual machine" -AsSecureString
$dnsPrefix = Read-Host -Prompt "Enter the unique DNS Name for the Public IP used to access the virtual machine"

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -adminUsername $adminUsername -adminPassword $adminPassword -dnsLabelPrefix $dnsPrefix
```

V tomto rychlém startu se používá Azure PowerShell k nasazení šablony ARM. K nasazení šablon můžete také použít [Azure Portal](../azure-resource-manager/templates/deploy-portal.md), rozhraní příkazového [řádku Azure](../azure-resource-manager/templates/deploy-cli.md)a [rozhraní REST API](../azure-resource-manager/templates/deploy-rest.md) .

## <a name="validate-the-deployment"></a>Ověření nasazení

### <a name="start-a-backup-job"></a>Spuštění úlohy zálohování

Šablona vytvoří virtuální počítač a povolí zpátky na VIRTUÁLNÍm počítači. Po nasazení šablony je nutné spustit úlohu zálohování. Další informace najdete v tématu [spuštění úlohy zálohování](./quick-backup-vm-powershell.md#start-a-backup-job).

### <a name="monitor-the-backup-job"></a>Monitorování úlohy zálohování

Chcete-li monitorovat úlohu zálohování, přečtěte si téma [monitorování úlohy zálohování](./quick-backup-vm-powershell.md#monitor-the-backup-job).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete zálohovat virtuální počítač, můžete ho vyčistit.

- Pokud chcete vyzkoušet obnovení virtuálního počítače, vynechejte vyčištění.
- Pokud jste použili existující virtuální počítač, můžete přeskočit poslední rutinu [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) a ponechat skupinu prostředků a virtuální počítač na místě.

Zakažte ochranu, odeberte body obnovení a trezor. Pak odstraňte skupinu prostředků a související prostředky virtuálního počítače následujícím způsobem:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili trezor služby Recovery Services, povolili ochranu virtuálního počítače a vytvořili prvotní bod obnovení.

- [Naučte](tutorial-backup-vm-at-scale.md) se, jak zálohovat virtuální počítače v Azure Portal.
- [Zjistěte, jak](tutorial-restore-disk.md) rychle obnovit virtuální počítač.
- [Naučte](../azure-resource-manager/templates/template-tutorial-create-first-template.md) se vytvářet šablony ARM.
