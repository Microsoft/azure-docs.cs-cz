---
title: Zálohování Azure stacku | Dokumentace Microsoftu
description: Proveďte zálohu na vyžádání v Azure stacku pomocí služby backup na místě.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: 08335f676437a32aa2240298be4680633eff16ba
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47432966"
---
# <a name="back-up-azure-stack"></a>Zálohování Azure stacku

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Proveďte zálohu na vyžádání v Azure stacku pomocí služby backup na místě. Pokyny ke konfiguraci prostředí PowerShell najdete v tématu [instalace Powershellu pro Azure Stack ](azure-stack-powershell-install.md). Přihlaste se ke službě Azure Stack, najdete v článku [pomocí portálu správce ve službě Azure Stack](azure-stack-manage-portals.md).

## <a name="start-azure-stack-backup"></a>Spustit zálohování Azure stacku

### <a name="start-a-new-backup-without-job-progress-tracking"></a>Spustit nové zálohování bez sledování průběhu úlohy
Použijte počáteční AzSBackup Pokud chcete okamžitě spustit nové zálohování s žádný průběh úlohy sledování.

```powershell
   Start-AzsBackup -Force
```

### <a name="start-azure-stack-backup-with-job-progress-tracking"></a>Zálohování Azure stacku začínat sledování průběhu úlohy
Můžete spustit nové zálohování s Start AzSBackup **- AsJob** parametr a uložte ho jako proměnnou můžete sledovat průběh úlohy zálohování.

> [!NOTE]
> Úloha zálohování se zobrazí jako úspěšně dokončena na portálu asi 10 až 15 minut, než se úloha dokončí.
>
> Proto se skutečný stav nezaznamenáme lépe prostřednictvím kód uvedený níže.

> [!IMPORTANT]
> Počáteční 1 milisekund zpoždění je zavedená, protože kód je příliš rychlé správně registrace úlohy a vrátí zpět bez **PSBeginTime** a zase bez **stavu** úlohy.

```powershell
    $BackupJob = Start-AzsBackup -Force -AsJob
    While (!$BackupJob.PSBeginTime) {
        Start-Sleep -Milliseconds 1
    }
    Write-Host "Start time: $($BackupJob.PSBeginTime)"
    While ($BackupJob.State -eq "Running") {
        Write-Host "Job is currently: $($BackupJob.State) - Duration: $((New-TimeSpan -Start ($BackupJob.PSBeginTime) -End (Get-Date)).ToString().Split(".")[0])"
        Start-Sleep -Seconds 30
    }

    If ($BackupJob.State -eq "Completed") {
        Get-AzsBackup | Where-Object {$_.BackupId -eq $BackupJob.Output.BackupId}
        $Duration = $BackupJob.Output.TimeTakenToCreate
        $Pattern = '^P?T?((?<Years>\d+)Y)?((?<Months>\d+)M)?((?<Weeks>\d+)W)?((?<Days>\d+)D)?(T((?<Hours>\d+)H)?((?<Minutes>\d+)M)?((?<Seconds>\d*(\.)?\d*)S)?)$'
        If ($Duration -match $Pattern) {
            If (!$Matches.ContainsKey("Hours")) {
                $Hours = ""
            } 
            Else {
                $Hours = ($Matches.Hours).ToString + 'h '
            }
            $Minutes = ($Matches.Minutes)
            $Seconds = [math]::round(($Matches.Seconds))
            $Runtime = '{0}{1:00}m {2:00}s' -f $Hours, $Minutes, $Seconds
        }
        Write-Host "BackupJob: $($BackupJob.Output.BackupId) - Completed with Status: $($BackupJob.Output.Status) - It took: $($Runtime) to run" -ForegroundColor Green
    }
    ElseIf ($BackupJob.State -ne "Completed") {
        $BackupJob
        $BackupJob.Output
    }
```

## <a name="confirm-backup-has-completed"></a>Potvrďte, že zálohování byla dokončena.

### <a name="confirm-backup-has-completed-using-powershell"></a>Potvrďte, že dokončení zálohování pomocí Powershellu
Pokud chcete zajistit, že záloha byla úspěšně dokončena. použijte následující příkazy Powershellu:

```powershell
   Get-AzsBackup
```

Výsledek by měl vypadat podobně jako následující výstup:

```powershell
    BackupDataVersion : 1.0.1
    BackupId          : <backup ID>
    RoleStatus        : {NRP, SRP, CRP, KeyVaultInternalControlPlane...}
    Status            : Succeeded
    CreatedDateTime   : 7/6/2018 6:46:24 AM
    TimeTakenToCreate : PT20M32.364138S
    DeploymentID      : <deployment ID>
    StampVersion      : 1.1807.0.41
    OemVersion        : 
    Id                : /subscriptions/<subscription ID>/resourceGroups/System.local/providers/Microsoft.Backup.Admin/backupLocations/local/backups/<backup ID>
    Name              : local/<local name>
    Type              : Microsoft.Backup.Admin/backupLocations/backups
    Location          : local
    Tags              : {}
```

### <a name="confirm-backup-has-completed-in-the-administration-portal"></a>Potvrďte, že zálohování byla dokončena v portálu pro správu
Na portálu pro správu služby Azure Stack použijte k ověření, že se že úspěšně dokončila tuto zálohu pomocí následujících kroků:

1. Otevřít [portál pro správu služby Azure Stack](azure-stack-manage-portals.md).
2. Vyberte **všechny služby**a potom v části **správu** vyberte kategorii > **infrastruktura zálohování**. Zvolte **konfigurace** v **infrastruktura zálohování** okno.
3. Najít **název** a **datum dokončení** zálohy v **dostupnými zálohami** seznamu.
4. Ověřte, **stavu** je **proběhlo úspěšně**.

## <a name="next-steps"></a>Další postup

Další informace o pracovním postupu pro obnovení z před událostí ztráty. Zobrazit [obnovit ze ztráty dat](azure-stack-backup-recover-data.md).
