---
title: Obnovení klíče trezoru klíč a tajný klíč pro šifrované virtuální počítače pomocí služby Azure Backup
description: Zjistěte, jak obnovit klíč trezoru klíč a tajný klíč v Azure Backup pomocí prostředí PowerShell
services: backup
author: JPallavi
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 08/28/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b703b4511f9fefb48546b23feaa33ca7da34da1f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606099"
---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Obnovení klíče trezoru klíč a tajný klíč pro šifrované virtuální počítače pomocí služby Azure Backup
V tomto článku bude zmíněn pomocí zálohování virtuálních počítačů Azure k provedení obnovení šifrovaných virtuálních počítačích Azure, pokud klíč a tajný klíč neexistují v trezoru klíčů. Tyto kroky můžete také použijí, pokud chcete zachovat samostatnou kopii klíč (klíč šifrovacího klíče) a tajný klíč (klíč šifrování nástrojem BitLocker) pro obnovený virtuální počítač.

## <a name="prerequisites"></a>Požadavky
* **Šifrované zálohování virtuálních počítačů** – šifrované virtuální počítače Azure byly zálohovány pomocí služby Azure Backup. Najdete v článku [Správa zálohování a obnovení virtuálních počítačů Azure pomocí prostředí PowerShell](backup-azure-vms-automation.md) podrobnosti o tom, jak zálohovat šifrované virtuálních počítačích Azure.
* **Azure Key Vault konfigurovat** – Ujistěte se, že trezor klíčů, ke kterému klíče a tajné klíče je nutné obnovit již existuje. Najdete v článku [Začínáme s Azure Key Vault](../key-vault/key-vault-get-started.md) podrobnosti o správě trezoru klíčů.
* **Obnovení disku** – Ujistěte se, že máte aktivuje úlohy obnovení pro obnovení disků pro šifrované virtuální počítač pomocí [kroky PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm). Je to proto, že tato úloha generuje soubor JSON v účtu úložiště, který obsahuje klíče a tajné klíče pro šifrovaná virtuální počítač pro obnovení.

## <a name="get-key-and-secret-from-azure-backup"></a>Získat klíč a tajný klíč z Azure Backup

> [!NOTE]
> Jakmile disku byla obnovena pro šifrované virtuálních počítačů, zajistěte, aby:
> 1. $details naplněný podrobnosti úlohy obnovení disku, jak je uvedeno v [prostředí PowerShell kroky obnovení části disky](backup-azure-vms-automation.md#restore-an-azure-vm)
> 2. Virtuální počítač by měl být vytvořen z obnovené disků pouze **po obnovení klíče a tajné klíče trezoru**.
>
>

Dotaz na vlastnosti obnovené disku podrobnosti úlohy.

```
PS C:\> $properties = $details.properties
PS C:\> $storageAccountName = $properties["Target Storage Account Name"]
PS C:\> $containerName = $properties["Config Blob Container Name"]
PS C:\> $encryptedBlobName = $properties["Encryption Info Blob Name"]
```

Nastavit kontext úložiště Azure a obnovte konfigurační soubor JSON obsahující klíče a tajné údaje pro šifrované virtuální počítač.

```
PS C:\> Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName '<rg-name>'
PS C:\> $destination_path = 'C:\vmencryption_config.json'
PS C:\> Get-AzureStorageBlobContent -Blob $encryptedBlobName -Container $containerName -Destination $destination_path
PS C:\> $encryptionObject = Get-Content -Path $destination_path  | ConvertFrom-Json
```

## <a name="restore-key"></a>Obnovit klíč
Po vygenerování souboru JSON v cílové cestě zmíněné generování klíče objektu blob souboru z formátu JSON a kanálu ho k obnovení klíče rutiny uvést klíč (KEK) zpět do trezoru klíčů.

```
PS C:\> $keyDestination = 'C:\keyDetails.blob'
PS C:\> [io.file]::WriteAllBytes($keyDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyBackupData))
PS C:\> Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile $keyDestination
```

## <a name="restore-secret"></a>Obnovit tajný klíč
K nastavení tajný rutiny uvést tajný klíč (BEK) zpět do trezoru klíčů použijte soubor JSON generované výše získat tajný název a hodnotu a jeho kanálu. **Tyto rutiny použijte, pokud virtuální počítač je zašifrovaný pomocí BEK a KEK.**

```
PS C:\> $secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
PS C:\> $secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
PS C:\> Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

Pokud je virtuální počítač **šifrovat jenom pomocí BEK**, vygenerujte soubor tajný blob z JSON a kanál ho obnovit tajný rutiny uvést tajný klíč (BEK) zpět do trezoru klíčů.

```
PS C:\> $secretDestination = 'C:\secret.blob'
PS C:\> [io.file]::WriteAllBytes($secretDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyVaultSecretBackupData))
PS C:\> Restore-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -InputFile $secretDestination -Verbose
```

> [!NOTE]
> 1. Hodnota $secretname je možné získat odkaz na výstup $encryptionObject.OsDiskKeyAndSecretDetails.SecretUrl a pomocí textu po tajných klíčů nebo například výstup tajný URL https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 a tajný název je B3284AAA-DAAA-4AAA-B393-60CAA848AAAA
> 2. Hodnota značky DiskEncryptionKeyFileName je stejný jako tajný název.
>
>

## <a name="create-virtual-machine-from-restored-disk"></a>Vytvoření virtuálního počítače z obnovené disku
Pokud jste zálohovali šifrované virtuálního počítače pomocí zálohování virtuálních počítačů Azure, rutin prostředí PowerShell nápovědy zmíněné, obnovit klíče a tajné zpět do trezoru klíčů. Po obnovení je, najdete v článku [Správa zálohování a obnovení virtuálních počítačů Azure pomocí prostředí PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) vytvořit šifrovaný virtuální počítače z obnovené disku, klíče a tajný klíč.

## <a name="legacy-approach"></a>Starší verze přístup
Přístup zmíněné by fungovat pro všechny body obnovení. Starší způsob získání klíče a tajné informace z bodu obnovení, by však být platný pro body obnovení, které jsou starší než 11 července 2017 zašifrovaný pomocí BEK a KEK virtuálních počítačů. Po dokončení pro šifrované virtuální počítač pomocí úlohy obnovení disku [kroky PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm), ujistěte se, že je $rp naplněný platnou hodnotu.

### <a name="restore-key"></a>Obnovit klíč
Následující rutiny můžete použijte k získání informací o klíči (KEK) z bodu obnovení a kanál ho k obnovení klíče rutiny vrátit zpět v trezoru klíčů.

```
PS C:\> $rp1 = Get-AzureRmRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation 'C:\Users\downloads'
PS C:\> Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile 'C:\Users\downloads'
```

### <a name="restore-secret"></a>Obnovit tajný klíč
Následující rutiny můžete použijte k získání tajné informace (BEK) z bodu obnovení a kanál ho nastavit tajný rutiny vrátit zpět v trezoru klíčů.

```
PS C:\> $secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
PS C:\> $secretdata = $rp1.KeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://mykeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
PS C:\> Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  'Wrapped BEK'
```

> [!NOTE]
> 1. Hodnota pro $secretname je možné získat odkaz na výstup $rp1. KeyAndSecretDetails.SecretUrl a pomocí textu po tajných klíčů / například výstup tajný klíč je adresa URL https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 a tajný název je B3284AAA-DAAA-4AAA-B393-60CAA848AAAA
> 2. Hodnota značky DiskEncryptionKeyFileName je stejný jako tajný název.
> 3. Hodnota pro DiskEncryptionKeyEncryptionKeyURL můžete získat z trezoru klíčů po obnovení zpět klíče a použití [Get-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868053.aspx) rutiny
>
>

## <a name="next-steps"></a>Další postup
Po obnovení klíče a tajné zpět do trezoru klíčů, najdete v článku [Správa zálohování a obnovení virtuálních počítačů Azure pomocí prostředí PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) vytvořit šifrovaný virtuální počítače z obnovené disku, klíče a tajný klíč.
