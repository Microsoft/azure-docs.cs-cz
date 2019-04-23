---
title: Pomocí nástrojů příkazového řádku spouštět a zastavovat virtuální počítače Azure DevTest Labs | Dokumentace Microsoftu
description: Další informace o použití nástroje příkazového řádku spouštět a zastavovat virtuální počítače ve službě Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: a8132735d1af08055e9341608dcac0564ed4b927
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60236686"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>Pomocí nástrojů příkazového řádku spouštět a zastavovat virtuální počítače Azure DevTest Labs
V tomto článku se dozvíte, jak pomocí Azure Powershellu nebo Azure CLI spuštěním a zastavením virtuálních počítačů v testovacím prostředí ve službě Azure DevTest Labs. Můžete vytvořit skripty Powershellu/CLI k automatizaci těchto operací. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Přehled
Azure DevTest Labs je způsob, jak vytvářet rychlá, snadná a Štíhlá vývojová a testovací prostředí. Umožňuje vám spravovat náklady, rychle zřizovat virtuální počítače a minimalizovat ztráty.  Nejsou integrované funkce na webu Azure Portal, které vám umožní nakonfigurovat virtuální počítače v testovacím prostředí pro automatické spuštění a zastavení v určitých časech. 

Ale v některých případech můžete chtít automatizovat spouštění a zastavování virtuálních počítačů pomocí skriptů prostředí PowerShell nebo rozhraní příkazového řádku. Poskytuje pružnost s spuštění a zastavení jednotlivých počítačů v okamžiku místo v určitých časech. Tady jsou některé situace, ve kterých běží tyto úkoly pomocí skriptů by mohl být užitečný.

- Při použití 3vrstvé aplikace jako součást testovacího prostředí, úrovně nutné ke spuštění v sekvenci. 
- Vypněte virtuální počítač ještě ušetříte peníze při splnění vlastních kritérií. 
- Použijte jako úloha v pracovním postupu CI/CD na začátku toku, používejte virtuální počítače podle počítače sestavení, testovací počítače ani infrastrukturu a poté zastavte virtuální počítače po dokončení procesu. Příklad tohoto by objekt pro vytváření vlastní image s Azure DevTest Labs.  

## <a name="azure-powershell"></a>Azure PowerShell
Následující skript prostředí PowerShell spustí virtuální počítač v testovacím prostředí. [Vyvolání AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0) se zaměřuje především pro tento skript. **ResourceId** parametr je plně kvalifikované ID prostředku pro virtuální počítač v testovacím prostředí. **Akce** parametr je tam, kde **Start** nebo **Zastavit** možnosti se nastavují v závislosti na tom, co je potřeba.

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
if ($(Get-Module -Name AzureRM).Version.Major -eq 6) {
    $devTestLab = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -Name $devTestLabName
} else {
    $devTestLab = Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $devTestLabName
}

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
[Rozhraní příkazového řádku Azure](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) je další způsob, jak automatizovat spouštění a zastavování virtuálních počítačů DevTest Labs. Rozhraní příkazového řádku Azure může být [nainstalované](/cli/azure/install-azure-cli?view=azure-cli-latest) v různých operačních systémech. Následující skript vypíše příkazy za spouštění a zastavování virtuálních počítačů v testovacím prostředí. 

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


## <a name="next-steps"></a>Další postup
Najdete v následujícím článku na webu Azure portal provádět tyto operace: [Restartovat virtuální počítač](devtest-lab-restart-vm.md).
