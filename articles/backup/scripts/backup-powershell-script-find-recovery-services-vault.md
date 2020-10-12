---
title: PowerShellový skript – najít trezor pro účet úložiště
description: Naučte se používat skript Azure PowerShell k vyhledání trezoru Recovery Services, ve kterém je váš účet úložiště zaregistrovaný.
ms.topic: sample
ms.date: 1/28/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 40859c1ea05210d27fcdcf33ba9d4f961965ea22
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89075694"
---
# <a name="powershell-script-to-find-the-recovery-services-vault-where-a-storage-account-is-registered"></a>Skript PowerShellu pro vyhledání trezoru Recovery Services, ve kterém je registrovaný účet úložiště

Tento skript vám pomůže najít Recovery Services trezor, ve kterém je váš účet úložiště zaregistrovaný.

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

Následující příklad se pokusí najít Recovery Services trezoru, kde je zaregistrován účet úložiště *afsaccount* :

```powershell
.\FindRegisteredStorageAccount.ps1 -ResourceGroupName AzureFiles -StorageAccountName afsaccount -SubscriptionId ef4ad5a7-c2c0-4304-af80-af49f49af3d1
```

## <a name="output"></a>Výstup

Ve výstupu se zobrazí úplná cesta k trezoru Recovery Services, kde je zaregistrován účet úložiště. Zde je ukázkový výstup:

```output
Found Storage account afsaccount registered in vault: /subscriptions/ ef4ad5a7-c2c0-4304-af80-af49f49af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault123
```

## <a name="next-steps"></a>Další kroky

Informace o [Zálohování sdílených složek Azure z Azure Portal](../backup-afs.md)
