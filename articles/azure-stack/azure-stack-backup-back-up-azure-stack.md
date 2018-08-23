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
ms.date: 08/01/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: 578bb864f56b788db77d1201533e73d3b9616669
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2018
ms.locfileid: "42055228"
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
Použití Start-AzSBackup novou zálohu začínat proměnnou - AsJob sledovat průběh úlohy zálohování.

```powershell
    $backupjob = Start-AzsBackup -Force -AsJob 
    "Start time: " + $backupjob.PSBeginTime;While($backupjob.State -eq "Running"){("Job is currently: " `
    + $backupjob.State+" ;Duration: " + (New-TimeSpan -Start ($backupjob.PSBeginTime) `
    -End (Get-Date)).Minutes);Start-Sleep -Seconds 30};$backupjob.Output

    if($backupjob.State -eq "Completed"){Get-AzsBackup | where {$_.BackupId -eq $backupjob.Output.BackupId}}
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
2. Vyberte **další služby** > **infrastruktura zálohování**. Zvolte **konfigurace** v **infrastruktura zálohování** okno.
3. Najít **název** a **datum dokončení** zálohy v **dostupnými zálohami** seznamu.
4. Ověřte, **stavu** je **proběhlo úspěšně**.

## <a name="next-steps"></a>Další postup

Další informace o pracovním postupu pro obnovení z před událostí ztráty. Zobrazit [obnovit ze ztráty dat](azure-stack-backup-recover-data.md).
