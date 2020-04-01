---
title: PowerShell Script – hledání účtu úložiště pro úložiště
description: Přečtěte si, jak pomocí skriptu Azure PowerShellnajít trezor služeb pro obnovení, kde je registrovaný účet úložiště.
ms.topic: sample
ms.date: 1/28/2020
ms.openlocfilehash: 786420ec8cef6516f7261c71b40641693efece07
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76775358"
---
# <a name="powershell-script-to-find-the-recovery-services-vault-where-a-storage-account-is-registered"></a>Powershellscript pro nalezení trezoru služby Recovery Services, kde je účet úložiště registrován

Tento skript vám pomůže najít trezor služeb pro obnovení, kde je váš účet úložiště registrován.

## <a name="sample-script"></a>Ukázkový skript

```powershell
Param(
        [Parameter(Mandatory=$True)][System.String] $ResourceGroupName,
        [Parameter(Mandatory=$True)][System.String] $StorageAccountName,
        [Parameter(Mandatory=$True)][System.String] $SubscriptionId
    )

Connect-AzAccount
Select-AzSubscription -Subscription $SubscriptionId
$vaults = Get-AzRecoveryServicesVault
$found = $false
foreach($vault in $vaults)
{
  Write-Verbose "Checking vault: $($vault.Id)" -Verbose
  
  $containers = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -FriendlyName $StorageAccountName -ResourceGroupName $ResourceGroupName -VaultId $vault.Id -Status Registered
  
  if($containers -ne $null)
  {
    $found = $True
    Write-Information "Found Storage account $StorageAccountName registered in vault: $($vault.Id)" -InformationAction Continue
    break;
  }
}

if(!$found)
{
     Write-Information "Storage account: $StorageAccountName is not registered in any vault of this subscription" -InformationAction Continue
}
```

## <a name="how-to-execute-the-script"></a>Jak spustit skript

1. Uložte výše uvedený skript do počítače s názvem dle vašeho výběru. V tomto příkladu jsme jej uložili jako *FindRegisteredStorageAccount.ps1*.
2. Spusťte skript poskytnutím následujících parametrů:

    * **-ResourceGroupName** - Skupina prostředků účtu úložiště
    * **-StorageAccountName** – název účtu úložiště
    * **-SubscriptionID** - ID předplatného, kde je k dispozici účet úložiště.

Následující příklad se pokusí najít trezor služeb obnovení, kde je registrován účet úložiště *afsaccount:*

```powershell
.\FindRegisteredStorageAccount.ps1 -ResourceGroupName AzureFiles -StorageAccountName afsaccount -SubscriptionId ef4ad5a7-c2c0-4304-af80-af49f49af3d1
```

## <a name="output"></a>Výstup

Na výstupu se zobrazí úplná cesta trezoru služeb obnovení, kde je účet úložiště registrován. Zde je ukázkový výstup:

```output
Found Storage account afsaccount registered in vault: /subscriptions/ ef4ad5a7-c2c0-4304-af80-af49f49af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault123
```

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [zálohovat sdílené složky Azure z webu Azure Portal](https://docs.microsoft.com/azure/backup/backup-afs)
