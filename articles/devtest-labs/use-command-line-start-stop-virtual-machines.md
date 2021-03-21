---
title: Spuštění a zastavení virtuálních počítačů pomocí nástrojů příkazového řádku Azure DevTest Labs
description: Naučte se používat nástroje příkazového řádku ke spouštění a zastavování virtuálních počítačů v Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2ddc1620cf86fa203b2f0e31359f9fd262df8916
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102499539"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>Spuštění a zastavení Azure DevTest Labs virtuálních počítačů pomocí nástrojů příkazového řádku
V tomto článku se dozvíte, jak pomocí Azure PowerShell nebo Azure CLI spouštět a zastavovat virtuální počítače v testovacím prostředí v Azure DevTest Labs. K automatizaci těchto operací můžete vytvořit skripty PowerShellu nebo rozhraní příkazového řádku. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Přehled
Azure DevTest Labs je způsob, jak vytvářet rychlá, snadná a štíhlá vývojová a testovací prostředí. Umožňuje vám spravovat náklady, rychle zřizovat virtuální počítače a minimalizovat odpad.  V Azure Portal jsou integrované funkce, které vám umožní nakonfigurovat virtuální počítače v testovacím prostředí tak, aby se v určitých časech automaticky spouštěly a zastavily. 

V některých scénářích ale můžete chtít automatizovat spouštění a zastavování virtuálních počítačů ze skriptů PowerShellu nebo rozhraní příkazového řádku. Nabízí značnou flexibilitu při spouštění a zastavování jednotlivých počítačů kdykoli po určitou dobu. Tady je několik situací, ve kterých jsou tyto úlohy spuštěné pomocí skriptů užitečné.

- Při použití 3 vrstvy aplikace jako součásti testovacího prostředí musí být vrstvy spuštěné v sekvenci. 
- Vypněte virtuální počítač, když se splní vlastní kritéria pro uložení peněz. 
- Použijte ji jako úlohu v rámci pracovního postupu CI/CD a začněte na začátku toku, použijte virtuální počítače jako počítače sestavení, testovací počítače nebo infrastrukturu a potom po dokončení procesu zastavte virtuální počítače. Příkladem může být vlastní továrna obrázků s Azure DevTest Labs.  

## <a name="azure-powershell"></a>Azure PowerShell

> [!NOTE]
> Následující skript používá Azure PowerShell AZ Module. 

Následující skript prostředí PowerShell spustí virtuální počítač v testovacím prostředí. [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) je pro tento skript primárním fokusem. Parametr **ResourceID** je plně kvalifikované ID prostředku pro virtuální počítač v testovacím prostředí. Parametr **Action** je, kde jsou nastaveny možnosti **Spustit** nebo **zastavit** v závislosti na tom, co je potřeba.

```powershell
# The id of the subscription
$subscriptionId = "111111-11111-11111-1111111"

# The name of the lab
$devTestLabName = "yourlabname"

# The name of the virtual machine to be started
$vMToStart = "vmname"

# The action on the virtual machine (Start or Stop)
$vmAction = "Start"

# Select the Azure subscription
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab information
$devTestLab = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $devTestLabName

# Start the VM and return a succeeded or failed status
$returnStatus = Invoke-AzResourceAction `
                    -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vMToStart" `
                    -Action $vmAction `
                    -Force

if ($returnStatus.Status -eq 'Succeeded') {
    Write-Output "##[section] Successfully updated DTL machine: $vMToStart, Action: $vmAction"
}
else {
    Write-Error "##[error]Failed to update DTL machine: $vMToStart, Action: $vmAction"
}
```


## <a name="azure-cli"></a>Azure CLI
Rozhraní příkazového [řádku Azure](/cli/azure/get-started-with-azure-cli) je dalším způsobem, jak automatizovat spouštění a zastavování virtuálních počítačů DevTest Labs. Rozhraní příkazového řádku Azure CLI je možné [instalovat](/cli/azure/install-azure-cli) v různých operačních systémech. Následující skript vám poskytne příkazy pro spouštění a zastavování virtuálních počítačů v testovacím prostředí. 

```azurecli
# Sign in to Azure
az login 

## Get the name of the resource group that contains the lab
az resource list --resource-type "Microsoft.DevTestLab/labs" --name "yourlabname" --query "[0].resourceGroup" 

## Start the VM
az lab vm start --lab-name yourlabname --name vmname --resource-group labResourceGroupName

## Stop the VM
az lab vm stop --lab-name yourlabname --name vmname --resource-group labResourceGroupName
```


## <a name="next-steps"></a>Další kroky
Pokud chcete použít Azure Portal k provedení těchto operací, přečtěte si následující článek: [restartování virtuálního počítače](devtest-lab-restart-vm.md).
