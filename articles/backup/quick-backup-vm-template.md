---
title: Úvodní příručka – zálohování virtuálních počítače šablony Správce prostředků
description: Přečtěte si, jak zálohovat virtuální počítače pomocí šablony Azure Resource Manageru
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/14/2019
ms.custom: mvc,subject-armqs
ms.openlocfilehash: c40dc7ef8fc55acade709b1ffbbd86ff306f7f0e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79459238"
---
# <a name="back-up-a-virtual-machine-in-azure-with-resource-manager-template"></a>Zálohování virtuálního počítače v Azure pomocí šablony Správce prostředků

[Azure Backup](backup-overview.md) zálohuje místní počítače a aplikace a virtuální počítače Azure. Tento článek ukazuje, jak zálohovat virtuální počítač Azure se šablonou Správce prostředků a Azure PowerShell. Tento rychlý start se zaměřuje na proces nasazení šablony Správce prostředků k vytvoření trezoru služby Recover Services. Další informace o vývoji šablon Správce prostředků naleznete v [dokumentaci ke Správci prostředků](/azure/azure-resource-manager/) a [v odkazu na šablonu](/azure/templates/microsoft.recoveryservices/allversions).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Případně můžete zálohovat virtuální počítač pomocí [Azure PowerShellu](./quick-backup-vm-powershell.md), [Azure CLI](quick-backup-vm-cli.md)nebo na [webu Azure Portal](quick-backup-vm-portal.md).

## <a name="create-a-vm-and-recovery-services-vault"></a>Vytvoření trezoru služby VM a služby recovery Services

[Trezor služby Recovery Services](backup-azure-recovery-services-vault-overview.md) je logický kontejner, který ukládá záložní data pro chráněné prostředky, jako jsou virtuální počítače Azure. Při spuštění úlohy zálohování vytvoří bod obnovení uvnitř trezoru služby Recovery Services. Pomocí některého z těchto bodů obnovení pak můžete obnovit data k danému bodu v čase.

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon rychlého startu Azure](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/). Tato šablona umožňuje nasadit jednoduchý trezor windows voblasti virtuálních zařízení a služby Recovery Services nakonfigurovaný pomocí zásad DefaultPolicy for Protection.

:::code language="json" source="~/quickstart-templates/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json" range="1-247" highlight="221-245":::

Zdroje definované v šabloně jsou:

- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts)
- [**Microsoft.Network/publicIPAdresy**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Compute/virutalMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.RecoveryServices/trezory**](/azure/templates/microsoft.recoveryservices/vaults)
- [**Microsoft.RecoveryServices/trezory/backupFabrics/protectionContainers/protectedItems**](/azure/templates/microsoft.recoveryservices/vaults/backupfabrics/protectioncontainers/protecteditems)

### <a name="deploy-the-template"></a>Nasazení šablony

Pokud chcete šablonu nasadit, vyberte **Vyzkoušet,** chcete otevřít Prostředí Azure Cloud Shell a pak do okna prostředí vložte následující skript Prostředí PowerShell. Pokud chcete kód vložit, klikněte pravým tlačítkem myši na okno prostředí a pak vyberte **Vložit**.

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

Azure PowerShell se používá k nasazení šablony Správce prostředků v tomto rychlém startu. Portál [Azure](../azure-resource-manager/templates/deploy-portal.md), [rozhraní API Azure](../azure-resource-manager/templates/deploy-cli.md)a rozhraní Rest [API](../azure-resource-manager/templates/deploy-rest.md) se taky můžou použít k nasazení šablon.

## <a name="validate-the-deployment"></a>Ověření nasazení

### <a name="start-a-backup-job"></a>Spuštění úlohy zálohování

Šablona vytvoří virtuální počítač a povolí zpět na virtuálním počítači. Po nasazení šablony je třeba spustit úlohu zálohování. Další informace naleznete [v tématu Spuštění úlohy zálohování](./quick-backup-vm-powershell.md#start-a-backup-job).

### <a name="monitor-the-backup-job"></a>Monitorování úlohy zálohování

Informace o sledování úlohy zálohování naleznete [v tématu Sledování úlohy zálohování](./quick-backup-vm-powershell.md#monitor-the-backup-job).

## <a name="clean-up-the-deployment"></a>Vyčištění nasazení

Pokud už nepotřebujete zálohovat virtuální ho, můžete ho vyčistit.

- Pokud chcete vyzkoušet obnovení virtuálního virtuálního mísy, přeskočte vyčištění.
- Pokud jste použili existující virtuální hod, můžete přeskočit konečné [Odebrat AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) rutina opustit skupinu prostředků a virtuálního virtuálního trhu na místě.

Zakažte ochranu, odstraňte body obnovení a trezor. Potom odstraňte skupinu prostředků a přidružené prostředky virtuálního soudu takto:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili trezor služby Recovery Services, povolili ochranu virtuálního počítače a vytvořili prvotní bod obnovení.

- [Přečtěte si, jak](tutorial-backup-vm-at-scale.md) zálohovat virtuální počítače na webu Azure Portal.
- [Přečtěte si, jak](tutorial-restore-disk.md) rychle obnovit virtuální hod.
- [Přečtěte si, jak](../azure-resource-manager/templates/template-tutorial-create-first-template.md) vytvořit šablony Správce prostředků.
