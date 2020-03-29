---
title: Spuštění počítačů s runbooky automatizace v Azure DevTest Labs
description: Zjistěte, jak spustit virtuální počítače v testovacím prostředí v Azure DevTest Labs pomocí runbooků Azure Automation.
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
ms.openlocfilehash: 9bb97a73b7ca570ca122323e8e9c5a70c9348b15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76166310"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>Spuštění virtuálních počítačů v testovacím prostředí v pořadí pomocí runbooků Azure Automation
Funkce [automatického spuštění](devtest-lab-set-lab-policy.md#set-autostart) DevTest Labs umožňuje nakonfigurovat virtuální počítače tak, aby se automaticky spouštěla v určený čas. Tato funkce však nepodporuje počítače spustit v určitém pořadí. Existuje několik scénářů, kde by tento typ automatizace bylo užitečné.  Jeden scénář je, kde jumpbox virtuální ho disponující v rámci testovacího prostředí musí být spuštěn a první, před ostatními virtuálními počítačemi, jako Jumpbox se používá jako přístupový bod k ostatním virtuálním počítačem.  Tento článek ukazuje, jak nastavit účet Azure Automation s runbook prostředí PowerShell, který spustí skript. Skript používá značky na virtuálních počítačích v testovacím prostředí, které umožňují řídit pořadí při spuštění bez nutnosti měnit skript.

## <a name="setup"></a>Nastavení
V tomto příkladu virtuálních stránek v testovacím prostředí musí mít značku **StartupOrder** přidán s příslušnou hodnotou (0,1,2, atd.). Určete libovolný stroj, který nemusí být spuštěn jako -1.

## <a name="create-an-azure-automation-account"></a>Vytvoření účtu Azure Automation
Vytvořte účet Azure Automation podle následujících pokynů v [tomto článku](../automation/automation-create-standalone-account.md). Při vytváření účtu zvolte možnost **Spustit jako účty.** Po vytvoření účtu automatizace otevřete stránku **Moduly** a na řádku nabídek vyberte **Aktualizovat moduly Azure.** Výchozí moduly jsou několik verzí staré a bez aktualizace skript nemusí fungovat.

## <a name="add-a-runbook"></a>Přidání runbooku
Chcete-li přidat runbook do účtu automatizace, vyberte **runbooky** v levé nabídce. V nabídce vyberte **Přidat runbook** a podle pokynů [vytvořte runbook prostředí PowerShell](../automation/automation-first-runbook-textual-powershell.md).

## <a name="powershell-script"></a>Skript PowerShellu
Následující skript přebírá název předplatného, název testovacího prostředí jako parametry. Tok skriptu je získat všechny virtuální chody v testovacím prostředí a potom analyzovat informace o značce k vytvoření seznamu názvů virtuálních počítačů a jejich pořadí spuštění. Skript prochází virtuálních discích v pořadí a spustí virtuální chod. Pokud existuje více virtuálních počítačů v určitém čísle objednávky, jsou spuštěny asynchronně pomocí úloh prostředí PowerShell. Pro virtuální stránky, které nemají značku, nastavte hodnotu spuštění jako poslední (10), budou ve výchozím nastavení spuštěny jako poslední.  Pokud testovací prostředí nechce, aby se virtuální ms automaticky spustil, nastavte hodnotu značky na 11 a bude ignorována.

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
Chcete-li, aby byl tento skript spuštěn denně, [vytvořte plán](../automation/shared-resources/schedules.md#creating-a-schedule) v účtu automatizace. Po vytvoření plánu [jej propojte se souborem Runbook](../automation/shared-resources/schedules.md#linking-a-schedule-to-a-runbook). 

Ve velkém měřítku situace, kdy existuje více odběrů s více testovacích prostředí, uložte informace o parametrech v souboru pro různé testovací prostředí a předat soubor do skriptu namísto jednotlivých parametrů. Skript by musel být změněn, ale základní spuštění by bylo stejné. Zatímco tato ukázka používá Azure Automation ke spuštění skriptu PowerShell, existují další možnosti, jako je použití úlohy v kanálu sestavení nebo vydání.

## <a name="next-steps"></a>Další kroky
Další informace o Azure Automation: [Úvod do Azure Automation](../automation/automation-intro.md)najdete v následujícím článku.
