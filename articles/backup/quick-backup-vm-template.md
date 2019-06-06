---
title: Rychlý start Azure – zálohování virtuálního počítače pomocí šablony Resource Manageru
description: Zjistěte, jak zálohovat virtuální počítače pomocí šablony Azure Resource Manageru
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/14/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: b144d7509562b8ca0bca6299caee4a7ce292f4a6
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481366"
---
# <a name="back-up-a-virtual-machine-in-azure-with-resource-manager-template"></a>Zálohování virtuálního počítače v Azure pomocí šablony Resource Manageru

[Azure Backup](backup-overview.md) zálohuje místních počítačů a aplikací a virtuálních počítačů Azure. V tomto článku se dozvíte, jak zálohovat virtuální počítač Azure pomocí šablony Resource Manageru a Azure Powershellu. V tomto rychlém startu se zaměřuje na postup nasazení šablony Resource Manageru pro vytvoření trezoru služeb zotavení. Další informace o vývoji šablon Resource Manageru najdete v tématu [dokumentace ke službě Správce prostředků](/azure/azure-resource-manager/) a [referenčními informacemi k šablonám](/azure/templates/microsoft.recoveryservices/allversions).

Alternativně můžete zálohovat virtuální počítač pomocí [prostředí Azure PowerShell](./quick-backup-vm-powershell.md), [rozhraní příkazového řádku Azure](quick-backup-vm-cli.md), nebo [webu Azure portal](quick-backup-vm-portal.md).

## <a name="create-a-vm-and-recovery-services-vault"></a>Vytvořte virtuální počítač a trezor služby Recovery Services

A [trezor služby Recovery Services](backup-azure-recovery-services-vault-overview.md) je logický kontejner, který zálohuje data na chráněný prostředek, jako jsou virtuální počítače Azure. Když se spouští úloha zálohování, vytvoří bod obnovení v trezoru služby Recovery Services. Pomocí některého z těchto bodů obnovení pak můžete obnovit data k danému bodu v čase.

Šablona použitá v tomto rychlém startu je z [šablony rychlý start Azure](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/). Tato šablona umožňuje nasadit jednoduchý virtuální počítač Windows a trezor služby Recovery Services nakonfigurovaná s DefaultPolicy pro ochranu.

Pokud chcete nasadit šablonu, vyberte **vyzkoušet** otevřete Azure Cloud shell, a vložte následující skript prostředí PowerShell do okna prostředí. Vložte kód, pravým tlačítkem myši na okno prostředí a potom vyberte **vložte**.

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

Prostředí Azure PowerShell slouží k nasazení šablony Resource Manageru v rámci tohoto rychlého startu. [Webu Azure portal](../azure-resource-manager/resource-group-template-deploy-portal.md), [rozhraní příkazového řádku Azure](../azure-resource-manager/resource-group-template-deploy-cli.md), a [rozhraní Rest API](../azure-resource-manager/resource-group-template-deploy-rest.md) lze také použít k nasazení šablony.

## <a name="start-a-backup-job"></a>Spuštění úlohy zálohování

Šablona vytvoří virtuální počítač a umožňuje zpět na virtuálním počítači. Po nasazení šablony, budete muset spustit úlohu zálohování. Další informace najdete v tématu [spustit úlohu zálohování](./quick-backup-vm-powershell.md#start-a-backup-job).

## <a name="monitor-the-backup-job"></a>Monitorování úlohy zálohování

Monitorování úlohy zálohování, naleznete v tématu [monitorování úlohy zálohování](./quick-backup-vm-powershell.md#monitor-the-backup-job).

## <a name="clean-up-the-deployment"></a>Vyčištění nasazení

Pokud už nepotřebujete zálohování virtuálního počítače, můžete vyčistit ho.

- Pokud chcete vyzkoušet obnovení virtuálního počítače, přejděte vyčištění nahoru.
- Pokud jste použili existující virtuální počítač, můžete vynechat poslední [odebrat AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) rutiny tak, aby se zachovala skupina prostředků a virtuální počítač.

Zakažte ochranu, odebrat body obnovení a trezor. Potom odstraňte skupinu prostředků a související prostředky virtuálního počítače následujícím způsobem:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili trezor služby Recovery Services, povolili ochranu virtuálního počítače a vytvořili prvotní bod obnovení.

- [Zjistěte, jak](tutorial-backup-vm-at-scale.md) k zálohování virtuálních počítačů na webu Azure Portal.
- [Zjistěte, jak](tutorial-restore-disk.md) rychle obnovit virtuální počítač
