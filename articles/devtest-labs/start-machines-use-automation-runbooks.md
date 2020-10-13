---
title: Spouštění počítačů pomocí runbooků Automation v Azure DevTest Labs
description: Naučte se spouštět virtuální počítače v testovacím prostředí v Azure DevTest Labs pomocí Azure Automation sad Runbook.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 231e79d594aab7c59fa21f9ee512abaa9ac67043
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87282258"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>Spouštění virtuálních počítačů v testovacím prostředí pomocí Azure Automationch runbooků
Funkce [autostart](devtest-lab-set-lab-policy.md#set-autostart) v DevTest Labs vám umožní nakonfigurovat, aby se virtuální počítače spouštěly automaticky v zadaném čase. Tato funkce ale nepodporuje, aby se počítače spouštěly v určitém pořadí. Existuje několik scénářů, kdy by tento typ automatizace mohl být užitečný.  Jedním z nich je, že virtuální počítač s JumpBox v testovacím prostředí je potřeba nejdřív spustit, ale před ostatními virtuálními počítači, protože JumpBox se používá jako přístupový bod k ostatním virtuálním počítačům.  V tomto článku se dozvíte, jak nastavit účet Azure Automation pomocí Runbooku PowerShellu, který spustí skript. Skript používá značky na virtuálních počítačích v testovacím prostředí, aby bylo možné řídit pořadí spouštění bez nutnosti změny skriptu.

## <a name="setup"></a>Nastavení
V tomto příkladu musí virtuální počítače v testovacím prostředí mít přidaný tag **StartupOrder** s odpovídající hodnotou (0, 1, 2 atd.). Určete všechny počítače, které nemusíte spouštět jako-1.

## <a name="create-an-azure-automation-account"></a>Vytvoření účtu Azure Automation
Vytvořte účet Azure Automation podle pokynů v [tomto článku](../automation/automation-create-standalone-account.md). Při vytváření účtu vyberte možnost **účty Spustit jako** . Po vytvoření účtu Automation otevřete stránku **moduly** a v řádku nabídek vyberte **aktualizovat moduly Azure** . Výchozí moduly jsou staré starší verze a bez aktualizace skript nemusí fungovat.

## <a name="add-a-runbook"></a>Přidat Runbook
Pokud teď chcete přidat Runbook do účtu Automation, v levé nabídce vyberte **Runbooky** . V nabídce vyberte **Přidat Runbook** a podle pokynů [vytvořte powershellový Runbook](../automation/learn/automation-tutorial-runbook-textual-powershell.md).

## <a name="powershell-script"></a>Skript prostředí PowerShell
Následující skript převezme název předplatného, název testovacího prostředí jako parametry. Tok skriptu je získat všechny virtuální počítače v testovacím prostředí a pak analyzovat informace o značce a vytvořit seznam názvů virtuálních počítačů a jejich pořadí spouštění. Skript projde virtuálními počítači v uvedeném pořadí a spustí virtuální počítače. Pokud je v konkrétním pořadovém čísle více virtuálních počítačů, spouští se asynchronně pomocí úloh prostředí PowerShell. Pro virtuální počítače, které nemají značku, nastavte počáteční hodnotu jako poslední (10), ve výchozím nastavení se spustí jako poslední.  Pokud testovací prostředí nechce, aby se virtuální počítač spouštěl správně, nastavte značku na hodnotu 11 a bude ignorována.

```powershell
#Requires -Version 3.0
#Requires -Module AzureRM.Resources

param
(
    [Parameter(Mandatory=$false, HelpMessage="Name of the subscription that has the lab")]
    [string] $SubscriptionName,

    [Parameter(Mandatory=$false, HelpMessage="Lab name")]
    [string] $LabName
)

# Connect and add the appropriate subscription
$Conn = Get-AutomationConnection -Name AzureRunAsConnection

Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationId -Subscription $SubscriptionName -CertificateThumbprint $Conn.CertificateThumbprint

# Find the lab
$dtLab = Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $LabName

# Get the VMs
$dtlAllVms = New-Object System.Collections.ArrayList
$AllVMs = Get-AzResource -ResourceId "$($dtLab.ResourceId)/virtualmachines" -ApiVersion 2016-05-15

# Get the StartupOrder tag, if missing set to be run last (10)
ForEach ($vm in $AllVMs) {
    if ($vm.Tags) {
        if ($vm.Tags['StartupOrder']) {
            $startupValue = $vm.Tags['StartupOrder']
        } else {
            $startupValue = 10
        }
        } else {
            $startupValue = 10
        }
        $dtlAllVms.Add(@{$vm.Name = $startupValue}) > $null
}

# Setup for the async multiple vm start

# Save profile
$profilePath = Join-Path $env:Temp "profile.json"
If (Test-Path $profilePath){
    Remove-Item $profilePath
}
Save-AzContext -Path $profilePath

# Job to start VMs asynch
$startVMBlock = {
    Param($devTestLab,$vmToStart,$profilePath)
    Import-AzContext -Path ($profilePath)
    Invoke-AzResourceAction `
        -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vmToStart" `
        -Action Start `
        -Force
    Write-Output "Started: $vmToStart"
}

$current = 0
# Start in order from 0 to 10

While ($current -le 10) {
# Get the VMs in the current stage
    $tobeStarted = $null
    $tobeStarted = $dtlAllVms | Where-Object { $_.Values -eq $current}
    if ($tobeStarted.Count -eq 1) {
        # Run sync – jobs not necessary for a single VM
        $returnStatus = Invoke-AzResourceAction `
                -ResourceId "$($dtLab.ResourceId)/virtualmachines/$($tobeStarted.Keys)" `
                -Action Start `
                -Force
        Write-Output "$($tobeStarted.Keys) status: $($returnStatus.status)"
    } elseif ($tobeStarted.Count -gt 1) {
        # Start multiple VMs async
        $jobs = @()
        Write-Output "Start Jobs start: $(Get-Date)"
        
        # Jobs
        $jobs += Start-Job -ScriptBlock $startVMBlock -ArgumentList $dtLab, $($singlevm.Keys), $profilePath
        Write-Output "Start Jobs end: $(Get-Date)"
    }

    # Get results from all jobs
    if($jobs.Count -ne 0) {
        Write-Output "Receive Jobs start: $(Get-Date)"
        foreach ($job in $jobs){
            $jobResult = Receive-Job -Job $job -Wait | Write-Output
        }
        Remove-Job -Job $jobs -Force
    }
    else
    {
        Write-Output "Information: No jobs available"
    }
}
```

## <a name="create-a-schedule"></a>Vytvořit plán
Aby se tento skript spouštěl každý den, vytvořte v účtu Automation [plán](../automation/shared-resources/schedules.md#create-a-schedule) . Po vytvoření plánu [ho připojte k sadě Runbook](../automation/shared-resources/schedules.md#link-a-schedule-to-a-runbook). 

V rozsáhlých situacích, kdy existuje více předplatných s více cvičeními, uložte informace o parametrech do souboru pro jinou laboratoř a předejte soubor do skriptu místo jednotlivých parametrů. Skript by musel být upraven, ale základní spuštění bude stejné. I když tato ukázka používá Azure Automation ke spuštění skriptu PowerShellu, existují další možnosti, jako je použití úlohy v kanálu sestavení nebo vydání.

## <a name="next-steps"></a>Další kroky
Další informace o Azure Automation najdete v následujícím článku: [Úvod do Azure Automation](../automation/automation-intro.md).
