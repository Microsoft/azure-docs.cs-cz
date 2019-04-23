---
title: Spuštění počítače pomocí runbooků Automation ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Zjistěte, jak spustit virtuální počítače v testovacím prostředí ve službě Azure DevTest Labs pomocí runbooků Azure Automation.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: spelluru
ms.openlocfilehash: 8d3885ba25e479316f97ecbb0681a1680650fc09
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "59996658"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>Spuštění virtuálních počítačů v testovacím prostředí v pořadí pomocí runbooků Azure Automation
[Autostart](devtest-lab-set-lab-policy.md#set-autostart) funkce DevTest Labs umožňuje konfigurovat virtuální počítače na automatické spouštění v určenou dobu. Tato funkce nepodporuje počítače ke spuštění v určitém pořadí. Existuje několik scénářů, ve kterém tento typ automation může být užitečné.  Jeden scénář je, kde Jumpbox virtuálního počítače v testovacím prostředí musí být spuštěna nejprve, než ostatní virtuální počítače, jako Jumpbox slouží jako přístupový bod pro ostatní virtuální počítače.  V tomto článku se dozvíte, jak vytvořit účet Azure Automation s Powershellového runbooku, který se spustí skript. Skript používá značky na virtuálních počítačích v testovacím prostředí a umožňuje tak řídit pořadí spouštění bez nutnosti změny skriptu.

## <a name="setup"></a>Nastavení
V tomto příkladu se virtuální počítače v testovacím prostředí musí mít značka **StartupOrder** přidán s odpovídající hodnotou (0,1,2, atd.). Určete všechny počítače, které nemusí být spuštěna jako hodnotu -1.

## <a name="create-an-azure-automation-account"></a>Vytvoření účtu Azure Automation
Vytvoření účtu Azure Automation podle pokynů v [v tomto článku](../automation/automation-create-standalone-account.md). Zvolte **účty spustit jako** možnosti při vytváření účtu. Po vytvoření účtu automation, otevřete **moduly** stránku a vybrat **aktualizace modulů Azure** na řádku nabídek. Moduly, ve výchozím nastavení se že několik verzí starý a bez aktualizace skriptu nemusí fungovat.

## <a name="add-a-runbook"></a>Přidat runbook
Teď přidáte sady runbook do účtu automation vyberte **sady Runbook** v nabídce vlevo. Vyberte **přidat runbook** v nabídce a postupujte podle pokynů [vytvořte Powershellový runbook](../automation/automation-first-runbook-textual-powershell.md).

## <a name="powershell-script"></a>Skript PowerShellu
Tento skript má název předplatného, název testovacího prostředí jako parametry. Tok souboru, který je k získání všech virtuálních počítačů v testovacím prostředí a pak pomocí parsování značek informací pro vytvoření seznamu názvy virtuálních počítačů a jejich pořadí spouštění. Skript provede virtuální počítače postupně a spustí virtuální počítače. Pokud existuje více virtuálních počítačů v určité pořadové číslo, jsou spuštěny asynchronně použití úloh Powershellu. U těchto počítačů, které nemají značku, sada spouštěcí hodnota byla poslední (10), spustí se poslední, ve výchozím nastavení.  Pokud testovací prostředí nechce být automaticky spustit virtuální počítač, nastavte hodnotu značky 11 a bude se ignorovat.

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

## <a name="create-a-schedule"></a>Vytvoření plánu
Aby tento skript spusťte každý den, [vytvořit plán](../automation/shared-resources/schedules.md#creating-a-schedule) v účtu automation. Po vytvoření plánu [odkaz na sadu runbook](../automation/shared-resources/schedules.md#linking-a-schedule-to-a-runbook). 

V rozsáhlých situaci, kdy existuje více předplatných s více testovacích prostředí, ukládat informace o parametrech v souboru pro různé testovací prostředí a předat soubor skriptu, takže jednotlivé parametry. Tento skript by bylo potřeba změnit, ale provádění jádra by byla stejná. Přestože tato ukázka používá Azure Automation a spustit skript prostředí PowerShell, existují další možnosti, například úlohy v kanálu sestavení/vydané verze.

## <a name="next-steps"></a>Další postup
Přečtěte si článek získat další informace o službě Azure Automation: [Úvod do služby Azure Automation](../automation/automation-intro.md).
