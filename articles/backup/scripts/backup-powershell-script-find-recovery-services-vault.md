---
title: PowerShellový skript – najít trezor pro účet úložiště
description: Naučte se, jak pomocí skriptu Azure PowerShell najít trezor služby Recovery Services, ve kterém je váš účet úložiště zaregistrovaný.
ms.topic: sample
ms.date: 1/28/2020
ms.openlocfilehash: b343b2f93ed439188c5c0238bf108064f6e132c1
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86513247"
---
# <a name="powershell-script-to-find-the-recovery-services-vault-where-a-storage-account-is-registered"></a>Skript PowerShellu pro vyhledání trezoru Recovery Services, ve kterém je registrovaný účet úložiště

Tento skript vám pomůže najít trezor služby Recovery Services, ve kterém je váš účet úložiště zaregistrovaný.

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

## <a name="how-to-execute-the-script"></a>Postup spuštění skriptu

1. Uložte skript výše na svém počítači s názvem, který jste vybrali. V tomto příkladu jsme ho uložili jako *FindRegisteredStorageAccount.ps1*.
2. Spusťte skript zadáním následujících parametrů:

    * **-ResourceGroupName** -skupina prostředků účtu úložiště
    * **-StorageAccountName** – název účtu úložiště
    * **-SubscriptionId** – ID předplatného, kde se nachází účet úložiště.

Následující příklad se pokusí najít trezor služby Recovery Services, kde je zaregistrován účet úložiště *afsaccount* :

```powershell
.\FindRegisteredStorageAccount.ps1 -ResourceGroupName AzureFiles -StorageAccountName afsaccount -SubscriptionId ef4ad5a7-c2c0-4304-af80-af49f49af3d1
```

## <a name="output"></a>Výstup

Ve výstupu se zobrazí úplná cesta k trezoru služby Recovery Services, kde je zaregistrován účet úložiště. Zde je ukázkový výstup:

```output
Found Storage account afsaccount registered in vault: /subscriptions/ ef4ad5a7-c2c0-4304-af80-af49f49af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault123
```

## <a name="next-steps"></a>Další kroky

Informace o [Zálohování sdílených složek Azure z Azure Portal](../backup-afs.md)
