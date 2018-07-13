---
title: Zálohování Azure stacku | Dokumentace Microsoftu
description: Proveďte zálohu na vyžádání v Azure stacku pomocí služby backup na místě.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/08/2017
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: 2570423a3cae2a15f0cfd294f1d91e6730748f68
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972604"
---
# <a name="back-up-azure-stack"></a>Zálohování Azure stacku

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Proveďte zálohu na vyžádání v Azure stacku pomocí služby backup na místě. Pokyny ke konfiguraci prostředí PowerShell najdete v tématu [instalace Powershellu pro Azure Stack ](azure-stack-powershell-install.md). Přihlaste se ke službě Azure Stack, najdete v článku [nakonfigurovat prostředí operátor a přihlaste se ke službě Azure Stack](azure-stack-powershell-configure-admin.md).

## <a name="start-azure-stack-backup"></a>Spustit zálohování Azure stacku

Spustit nové zálohování s proměnnou - AsJob pro sledování průběhu pomocí Start AzSBackup. 

```powershell
    $backupjob = Start-AzsBackup -Force -AsJob
    "Start time: " + $backupjob.PSBeginTime;While($backupjob.State -eq "Running"){("Job is currently: " + $backupjob.State+" ;Duration: " + (New-TimeSpan -Start ($backupjob.PSBeginTime) -End (Get-Date)).Minutes);Start-Sleep -Seconds 30};$backupjob.Output
```

## <a name="confirm-backup-completed-via-powershell"></a>Potvrďte zálohování bylo dokončeno přes PowerShell

```powershell
    if($backupjob.State -eq "Completed"){Get-AzsBackup | where {$_.BackupId -eq $backupjob.Output.BackupId}}
```

- Výsledek by měl vypadat podobně jako následující výstup:

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

## <a name="confirm-backup-completed-in-the-administration-portal"></a>Potvrdit dokončení v portálu pro správu zálohování

1. Otevřít na portálu pro správu služby Azure Stack na [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).
2. Vyberte **další služby** > **infrastruktura zálohování**. Zvolte **konfigurace** v **infrastruktura zálohování** okno.
3. Najít **název** a **datum dokončení** zálohy v **dostupnými zálohami** seznamu.
4. Ověřte, **stavu** je **proběhlo úspěšně**.

## <a name="next-steps"></a>Další postup

- Další informace o pracovním postupu pro obnovení z před událostí ztráty. Zobrazit [obnovit ze ztráty dat](azure-stack-backup-recover-data.md).
