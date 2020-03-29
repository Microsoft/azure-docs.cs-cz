---
title: Spuštění a zastavení virtuálních počítačů Azure DevTest Labs pomocí nástrojů příkazového řádku
description: Zjistěte, jak pomocí nástrojů příkazového řádku spouštět a zastavovat virtuální počítače v laboratořích Azure DevTest.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: fd643559a09d5c75aad9be5f35c653994c8488cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169256"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>Spuštění a zastavení virtuálních počítačů Azure DevTest Labs pomocí nástrojů příkazového řádku
Tento článek ukazuje, jak používat Azure PowerShell nebo Azure CLI ke spuštění nebo zastavení virtuálních počítačů v testovacím prostředí v Azure DevTest Labs. Můžete vytvořit skripty PowerShell/CLI pro automatizaci těchto operací. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Přehled
Azure DevTest Labs je způsob, jak vytvořit rychlá, snadná a štíhlá vývojová a testovací prostředí. Umožňuje spravovat náklady, rychle zřizováné virtuální aplikace a minimalizovat plýtvání.  Na portálu Azure jsou integrované funkce, které umožňují konfigurovat virtuální počítače v testovacím prostředí tak, aby se automaticky spouštěly a zastavovaly v konkrétních časech. 

V některých případech však můžete chtít automatizovat spouštění a zastavování virtuálních aplikací ze skriptů PowerShell/CLI. To vám dává určitou flexibilitu při spouštění a zastavování jednotlivých strojů kdykoliv namísto v konkrétních časech. Zde jsou některé ze situací, ve kterých by bylo užitečné spustit tyto úlohy pomocí skriptů.

- Při použití třívrstvé aplikace jako součást testovacího prostředí, vrstvy je třeba spustit v pořadí. 
- Vypněte virtuální hospo, když je splněna vlastní kritéria, abyste ušetřili peníze. 
- Použijte ji jako úkol v rámci pracovního postupu CI/CD spustit na začátku toku, použijte virtuální počítače jako sestavení počítače, testovací počítače nebo infrastruktury, pak zastavit virtuální počítače po dokončení procesu. Příkladem může být vlastní image factory s Azure DevTest Labs.  

## <a name="azure-powershell"></a>Azure PowerShell

> [!NOTE]
> Následující skript používá modul Azure PowerShell Az. 

Následující skript Prostředí PowerShell spustí virtuální ho v testovacím prostředí. [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0) je primární fokus pro tento skript. Parametr **ResourceId** je plně kvalifikované ID prostředků pro virtuální ho svícen v testovacím prostředí. Action Parametr je **místo,** kde start **nebo** **stop** možnosti jsou nastaveny v závislosti na tom, co je potřeba.

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
Azure [CLI](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) je další způsob, jak automatizovat spuštění a zastavení virtuálních počítačů DevTest Labs. Azure CLI lze [nainstalovat](/cli/azure/install-azure-cli?view=azure-cli-latest) do různých operačních systémů. Následující skript poskytuje příkazy pro spuštění a zastavení virtuálního virtuálního ms v testovacím prostředí. 

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
V následujícím článku, jak pomocí portálu Azure k provedení těchto operací: [Restartování virtuálního počítače](devtest-lab-restart-vm.md).
