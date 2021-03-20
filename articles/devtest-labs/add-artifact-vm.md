---
title: Přidání artefaktu do virtuálního počítače v Azure DevTest Labs | Microsoft Docs
description: Naučte se, jak přidat artefakt do virtuálního počítače v testovacím prostředí v Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b4772755d8077f7a659c4d403961ffaeb9e1d483
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85483886"
---
# <a name="add-an-artifact-to-a-vm"></a>Přidání artefaktu do virtuálního počítače
Při vytváření virtuálního počítače můžete do něj přidat existující artefakty. Tyto artefakty můžou být buď z [úložiště Git veřejného DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) , nebo z vlastního úložiště Git. V tomto článku se dozvíte, jak přidat artefakty do Azure Portal a pomocí Azure PowerShell. 

Azure DevTest Labs *artefakty* umožňují určit *Akce* , které se provedou při zřizování virtuálního počítače, například spouštění skriptů prostředí Windows PowerShell, spouštění příkazů bash a instalace softwaru. *Parametry* artefaktu umožňují přizpůsobit artefakt pro konkrétní scénář.

Další informace o tom, jak vytvořit vlastní artefakty, najdete v článku: [Vytvoření vlastních artefaktů](devtest-lab-artifact-author.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-azure-portal"></a>Použití webu Azure Portal 
1. Přihlaste se na [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Vyberte **všechny služby** a v seznamu vyberte **DevTest Labs** .
1. V seznamu cvičení vyberte testovací prostředí obsahující virtuální počítač, se kterým chcete pracovat.  
1. Vyberte **moje virtuální počítače**.
1. Vyberte požadovaný virtuální počítač.
1. Vyberte **Spravovat artefakty**. 
1. Vyberte **použít artefakty**.
1. V podokně **použít artefakty** vyberte artefakt, který chcete přidat do virtuálního počítače.
1. V podokně **Přidat artefakt** zadejte požadované hodnoty parametrů a všechny volitelné parametry, které potřebujete.  
1. Vyberte **Přidat** a přidejte artefakt a vraťte se do podokna **použít artefakty** .
1. Pokračujte v přidávání artefaktů podle potřeby pro váš virtuální počítač.
1. Po přidání artefaktů můžete [změnit pořadí, ve kterém jsou artefakty spuštěny](#change-the-order-in-which-artifacts-are-run). Můžete také přejít zpátky a [Zobrazit nebo upravit artefakt](#view-or-modify-an-artifact).
1. Až skončíte s přidáváním artefaktů, vyberte **použít** .

### <a name="change-the-order-in-which-artifacts-are-run"></a>Změna pořadí, ve kterém jsou artefakty spuštěny
Ve výchozím nastavení jsou akce artefaktů spouštěny v pořadí, ve kterém jsou přidány do virtuálního počítače. Následující postup ukazuje, jak změnit pořadí, ve kterém jsou artefakty spuštěny.

1. V horní části podokna **použít artefakty** vyberte odkaz označující počet artefaktů přidaných do virtuálního počítače.
   
    ![Počet artefaktů přidaných do virtuálního počítače](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. V podokně **vybrané artefakty** přetáhněte artefakty do požadovaného pořadí. Pokud máte potíže při přetahování artefaktem, ujistěte se, že přetahujete z levé strany artefaktu. 
1. Až to budete mít, vyberte **OK**.  

### <a name="view-or-modify-an-artifact"></a>Zobrazení nebo úprava artefaktu
Následující postup ukazuje, jak zobrazit nebo upravit parametry artefaktu:

1. V horní části podokna **použít artefakty** vyberte odkaz označující počet artefaktů přidaných do virtuálního počítače.
   
    ![Počet artefaktů přidaných do virtuálního počítače](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. V podokně **vybrané artefakty** vyberte artefakt, který chcete zobrazit nebo upravit.  
1. V podokně **Přidat artefakt** proveďte potřebné změny a kliknutím na **tlačítko OK** zavřete podokno **Přidat artefakt** .
1. Výběrem **OK** zavřete podokno **vybrané artefakty** .

## <a name="use-powershell"></a>Použití prostředí PowerShell
Následující skript aplikuje zadaný artefakt na zadaný virtuální počítač. Příkaz [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) je ten, který provádí operaci.  

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
$resourceGroupName = (Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $DevTestLabName}).ResourceGroupName
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

## <a name="next-steps"></a>Další kroky
Podívejte se na následující články o artefaktech:

- [Zadejte pro testovací prostředí povinné artefakty.](devtest-lab-mandatory-artifacts.md)
- [Přidání vlastních artefaktů](devtest-lab-artifact-author.md)
- [Přidání úložiště artefaktů do testovacího prostředí](devtest-lab-artifact-author.md)
- [Diagnostikování selhání artefaktů](devtest-lab-troubleshoot-artifact-failure.md)
