---
title: Přidání artefaktu do virtuálního počítače ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Zjistěte, jak přidat artefakt do virtuálního počítače v testovacím prostředí ve službě Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 19a7d6052091f8889a88c61793186b7bf7d9d869
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2019
ms.locfileid: "59047009"
---
# <a name="add-an-artifact-to-a-vm"></a>Přidání artefaktu do virtuálního počítače
Při vytváření virtuálního počítače, můžete přidat existující artefakty k němu. Tyto artefakty mohou být buď z [veřejného úložiště DevTest Labs Git](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) nebo z úložiště Git. Tento článek ukazuje, jak přidat artefakty na webu Azure Portal a pomocí Azure Powershellu. 

Azure DevTest Labs *artefakty* vám umožní zadat *akce* prováděných při zřízení virtuálního počítače, jako je například spouštění skriptů Windows Powershellu, příkazů Bash a instalace softwaru. Artefakt *parametry* umožňují přizpůsobit artefakt pro váš konkrétní scénář.

Další informace o tom, jak při vytváření vlastních artefaktů, najdete v článku: [Při vytváření vlastních artefaktů](devtest-lab-artifact-author.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-azure-portal"></a>Použití webu Azure Portal 
1. Přihlaste se k webu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Vyberte **všechny služby**a pak vyberte **DevTest Labs** ze seznamu.
1. V seznamu testovacích prostředí vyberte prostředí obsahující tento virtuální počítač, se kterým chcete pracovat.  
1. Vyberte **Moje virtual machines**.
1. Vyberte požadovaný virtuální počítač.
1. Vyberte **spravovat artefakty**. 
1. Vyberte **použít artefakty**.
1. Na **použít artefakty** podokně, vyberte prosím artefakt, který chcete přidat do virtuálního počítače.
1. Na **přidání artefaktu** podokně, zadejte hodnoty požadovaných parametrů a všechny volitelné parametry, které potřebujete.  
1. Vyberte **přidat** přidat artefakt a vraťte **použít artefakty** podokně.
1. Pokračujte v přidávání artefaktů podle potřeby pro váš virtuální počítač.
1. Po přidání artefaktů můžete [změnit pořadí, ve kterém jsou spuštěny artefakty](#change-the-order-in-which-artifacts-are-run). Můžete také přejít zpět k [zobrazit nebo upravit artefakt](#view-or-modify-an-artifact).
1. Při přidávání artefaktů budete mít, vyberte **použít**

### <a name="change-the-order-in-which-artifacts-are-run"></a>Změnit pořadí, ve kterém jsou spuštěny artefaktů
Ve výchozím nastavení akcí artefakty provádějí v pořadí, ve kterém byly přidány k virtuálnímu počítači. Následující postup ukazuje, jak změnit pořadí, ve kterém jsou spuštěny artefakty.

1. V horní části **použít artefakty** podokně, vyberte odkaz, která udává počet artefaktů, které byly přidány k virtuálnímu počítači.
   
    ![Počet artefaktů, které jsou přidány do virtuálního počítače](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Na **vybrané artefakty** podokně přetáhněte a umístěte artefakty do požadovaného pořadí. Pokud máte potíže při přetahování artefaktu, ujistěte se, že přetáhnete na levé straně artefaktu. 
1. Až to budete mít, vyberte **OK**.  

### <a name="view-or-modify-an-artifact"></a>Zobrazit nebo upravit artefakt
Následující kroky ukazují, jak zobrazit nebo upravit parametry artefaktu:

1. V horní části **použít artefakty** podokně, vyberte odkaz, která udává počet artefaktů, které byly přidány k virtuálnímu počítači.
   
    ![Počet artefaktů, které jsou přidány do virtuálního počítače](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Na **vybrané artefakty** podokně, vyberte prosím artefakt, který chcete zobrazit nebo upravit.  
1. Na **přidání artefaktu** podokno, zkontrolujte všechny potřebné změny a vyberte **OK** zavřete **přidání artefaktu** podokně.
1. Vyberte **OK** zavřete **vybrané artefakty** podokně.

## <a name="use-powershell"></a>Použití prostředí PowerShell
Následující skript se týká zadaného artefaktů zadaný virtuální počítač. [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) příkaz je ten, který provádí operace.  

```powershell
#Requires -Module Az.Resources

param
(
[Parameter(Mandatory=$true, HelpMessage="The ID of the subscription that contains the lab")]
   [string] $SubscriptionId,
[Parameter(Mandatory=$true, HelpMessage="The name of the lab containing the virtual machine")]
   [string] $DevTestLabName,
[Parameter(Mandatory=$true, HelpMessage="The name of the virtual machine")]
   [string] $VirtualMachineName,
[Parameter(Mandatory=$true, HelpMessage="The repository where the artifact is stored")]
   [string] $RepositoryName,
[Parameter(Mandatory=$true, HelpMessage="The artifact to apply to the virtual machine")]
   [string] $ArtifactName,
[Parameter(ValueFromRemainingArguments=$true)]
   $Params
)

# Set the appropriate subscription
Set-AzContext -SubscriptionId $SubscriptionId | Out-Null
 
# Get the lab resource group name
$resourceGroupName = (Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $DevTestLabName}).ResourceGroupName
if ($resourceGroupName -eq $null) { throw "Unable to find lab $DevTestLabName in subscription $SubscriptionId." }

# Get the internal repo name
$repository = Get-AzResource -ResourceGroupName $resourceGroupName `
                    -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
                    -ResourceName $DevTestLabName `
                    -ApiVersion 2016-05-15 `
                    | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
                    | Select-Object -First 1

if ($repository -eq $null) { "Unable to find repository $RepositoryName in lab $DevTestLabName." }

# Get the internal artifact name
$template = Get-AzResource -ResourceGroupName $resourceGroupName `
                -ResourceType "Microsoft.DevTestLab/labs/artifactSources/artifacts" `
                -ResourceName "$DevTestLabName/$($repository.Name)" `
                -ApiVersion 2016-05-15 `
                | Where-Object { $ArtifactName -in ($_.Name, $_.Properties.title) } `
                | Select-Object -First 1

if ($template -eq $null) { throw "Unable to find template $ArtifactName in lab $DevTestLabName." }

# Find the virtual machine in Azure
$FullVMId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                /providers/Microsoft.DevTestLab/labs/$DevTestLabName/virtualmachines/$virtualMachineName"

$virtualMachine = Get-AzResource -ResourceId $FullVMId

# Generate the artifact id
$FullArtifactId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                        /providers/Microsoft.DevTestLab/labs/$DevTestLabName/artifactSources/$($repository.Name)`
                        /artifacts/$($template.Name)"

# Handle the inputted parameters to pass through
$artifactParameters = @()

# Fill artifact parameter with the additional -param_ data and strip off the -param_
$Params | ForEach-Object {
   if ($_ -match '^-param_(.*)') {
      $name = $_.TrimStart('^-param_')
   } elseif ( $name ) {
      $artifactParameters += @{ "name" = "$name"; "value" = "$_" }
      $name = $null #reset name variable
   }
}

# Create structure for the artifact data to be passed to the action

$prop = @{
artifacts = @(
    @{
        artifactId = $FullArtifactId
        parameters = $artifactParameters
    }
    )
}

# Check the VM
if ($virtualMachine -ne $null) {
   # Apply the artifact by name to the virtual machine
   $status = Invoke-AzResourceAction -Parameters $prop -ResourceId $virtualMachine.ResourceId -Action "applyArtifacts" -ApiVersion 2016-05-15 -Force
   if ($status.Status -eq 'Succeeded') {
      Write-Output "##[section] Successfully applied artifact: $ArtifactName to $VirtualMachineName"
   } else {
      Write-Error "##[error]Failed to apply artifact: $ArtifactName to $VirtualMachineName"
   }
} else {
   Write-Error "##[error]$VirtualMachine was not found in the DevTest Lab, unable to apply the artifact"
}

```

## <a name="next-steps"></a>Další postup
Na artefakty naleznete v následujících článcích:

- [Zadejte povinné artefakty testovacího prostředí.](devtest-lab-mandatory-artifacts.md)
- [Přidání vlastních artefaktů](devtest-lab-artifact-author.md)
- [Přidání úložiště artefaktů do testovacího prostředí](devtest-lab-artifact-author.md)
- [Diagnostika selhání artefaktů](devtest-lab-troubleshoot-artifact-failure.md)
