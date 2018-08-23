---
title: Obnovení služby Key Vault klíč a tajný klíč pro šifrované virtuální počítače pomocí služby Azure Backup
description: Zjistěte, jak obnovit služby Key Vault klíč a tajný klíč ve službě Azure Backup pomocí Powershellu
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 08/28/2017
ms.author: sogup
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6ac3c3d8f2a5ae37f1d32f9781f0cdbec0b293e8
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/11/2018
ms.locfileid: "42054005"
---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Obnovení služby Key Vault klíč a tajný klíč pro šifrované virtuální počítače pomocí služby Azure Backup
Tento článek pojednává o pomocí zálohování virtuálních počítačů Azure k provedení obnovení šifrovaných virtuálních počítačů Azure, pokud klíč a tajný klíč neexistují v trezoru klíčů. Tyto kroky je možné také pokud chcete udržovat samostatnou kopii klíč (šifrovací klíč klíče) a tajný klíč (šifrovací klíč nástroje BitLocker) pro obnovený virtuální počítač.

## <a name="prerequisites"></a>Požadavky
* **Zálohování šifrovaných virtuálních počítačů** – šifrované virtuální počítače Azure byly zálohovány pomocí služby Azure Backup. Přečtěte si článek [Správa zálohování a obnovení virtuálních počítačů Azure pomocí Powershellu](backup-azure-vms-automation.md) podrobnosti o tom, jak zálohovat šifrované virtuální počítače Azure.
* **Konfigurace služby Azure Key Vault** – Ujistěte se, do které klíče a tajné klíče je nutné obnovit služby key vault už existuje. Přečtěte si článek [Začínáme s Azure Key Vault](../key-vault/key-vault-get-started.md) podrobné informace o správě služby key vault.
* **Obnovení disku** – Ujistěte se, právě jste aktivovali úlohy obnovení pro obnovení disků pro šifrované virtuální počítač pomocí [kroky PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm). Je to proto, že tato úloha generuje soubor JSON ve vašem účtu úložiště, který obsahuje klíče a tajné kódy pro šifrovaný virtuální počítač pro obnovení.

## <a name="get-key-and-secret-from-azure-backup"></a>Získejte klíč a tajný klíč z Azure Backup

> [!NOTE]
> Jakmile pro šifrovaný virtuální počítač se obnovila disku, ujistěte se, že:
> 1. $details se vyplní podrobnosti úlohy obnovení disku, jak je uvedeno v [Powershellu kroky obnovení disků části](backup-azure-vms-automation.md#restore-an-azure-vm)
> 2. Virtuální počítač by měl být vytvořen z obnovených disků pouze **po obnovení klíče a tajného klíče do trezoru klíčů**.
>
>

Dotaz na vlastnosti obnoveného disku pro podrobnosti o úloze.

```
PS C:\> $properties = $details.properties
PS C:\> $storageAccountName = $properties["Target Storage Account Name"]
PS C:\> $containerName = $properties["Config Blob Container Name"]
PS C:\> $encryptedBlobName = $properties["Encryption Info Blob Name"]
```

Nastavit kontext služby Azure storage a obnovte konfigurační soubor JSON obsahující klíče a tajného kódu podrobnosti pro šifrovaný virtuální počítač.

```
PS C:\> Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName '<rg-name>'
PS C:\> $destination_path = 'C:\vmencryption_config.json'
PS C:\> Get-AzureStorageBlobContent -Blob $encryptedBlobName -Container $containerName -Destination $destination_path
PS C:\> $encryptionObject = Get-Content -Path $destination_path  | ConvertFrom-Json
```

## <a name="restore-key"></a>Obnovit klíč
Po vygenerování souboru JSON v cílové cestě uvedené výše vygenerovat soubor blob klíče z JSON a kanál na obnovení klíče rutiny vložte klíč (KEK) zpět ve službě key vault.

```
PS C:\> $keyDestination = 'C:\keyDetails.blob'
PS C:\> [io.file]::WriteAllBytes($keyDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyBackupData))
PS C:\> Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile $keyDestination
```

## <a name="restore-secret"></a>Obnovit tajný klíč
Pomocí souboru JSON generované nad něj a získat název tajného kódu a hodnotu můžete nastavit tajných kódů rutiny umístění tajný kód (klíče BEK) zpět do služby key vault. **Tyto rutiny použijte, pokud je váš virtuální počítač šifrovaný pomocí klíče BEK a KEK.**

```
PS C:\> $secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
PS C:\> $secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
PS C:\> Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

Pokud je váš virtuální počítač **šifrované pomocí klíče BEK pouze**, generovat soubor tajného kódu objektu blob z JSON a něj obnovení tajných kódů rutiny umístění tajný klíč (klíče BEK) zpět do služby key vault.

```
PS C:\> $secretDestination = 'C:\secret.blob'
PS C:\> [io.file]::WriteAllBytes($secretDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyVaultSecretBackupData))
PS C:\> Restore-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -InputFile $secretDestination -Verbose
```

> [!NOTE]
> 1. Hodnota $secretname lze získat odkaz na výstup $encryptionObject.OsDiskKeyAndSecretDetails.SecretUrl a použitím textu po tajných kódů / například výstup adresa URL tajného kódu je https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 a název tajného kódu je B3284AAA-DAAA-4AAA-B393-60CAA848AAAA
> 2. Hodnota značky DiskEncryptionKeyFileName je stejná jako název tajného kódu.
>
>

## <a name="create-virtual-machine-from-restored-disk"></a>Vytvoření virtuálního počítače z obnoveného disku
Pokud jste zálohovali šifrovaných virtuálních počítačů pomocí Azure Backup pro virtuální počítače, rutin prostředí PowerShell uvedených výše nápovědy obnovit klíče a tajného kódu zpět do služby key vault. Po obnovení je, přečtěte si článek [Správa zálohování a obnovení virtuálních počítačů Azure pomocí Powershellu](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) vytvoření šifrované virtuální počítače z obnoveného disku, klíč a tajný klíč.

## <a name="legacy-approach"></a>Starší verze přístup
Přístup uvedených výše by fungovalo pro všechny body obnovení. Platné pro body obnovení starší než 11. července 2017 pro virtuální počítače jsou šifrované pomocí klíče BEK a KEK by však starším přístupem získání klíče a tajné informace z bodu obnovení. Po dokončení pro šifrované virtuální počítač pomocí úlohy obnovení disku [kroky PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm), ujistěte se, že se načtou $rp platnou hodnotu.

### <a name="restore-key"></a>Obnovit klíč
Pomocí následujících rutin pro získání klíčů (KEK) informace z bodu obnovení a kanál na obnovení klíče rutiny pro její umístění zpět do služby key vault.

```
PS C:\> $rp1 = Get-AzureRmRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation 'C:\Users\downloads'
PS C:\> Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile 'C:\Users\downloads'
```

### <a name="restore-secret"></a>Obnovit tajný klíč
Pomocí následujících rutin pro získání tajných informací (klíče BEK) z bodu obnovení a něj nastavit tajných kódů rutiny pro její umístění zpět do služby key vault.

```
PS C:\> $secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
PS C:\> $secretdata = $rp1.KeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://mykeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
PS C:\> Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  'Wrapped BEK'
```

> [!NOTE]
> 1. Hodnota pro $secretname je možné získat odkaz na výstup $rp1. KeyAndSecretDetails.SecretUrl a pomocí textu po tajných klíčů / například výstup tajný kód je adresa URL https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 a název tajného kódu je B3284AAA-DAAA-4AAA-B393-60CAA848AAAA
> 2. Hodnota značky DiskEncryptionKeyFileName je stejná jako název tajného kódu.
> 3. Hodnota pro DiskEncryptionKeyEncryptionKeyURL lze získat z trezoru klíčů po obnovení zpět klíče a pomocí [Get-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultkey) rutiny
>
>

## <a name="next-steps"></a>Další postup
Po obnovení klíče a tajného kódu zpět do služby key vault, najdete v článku [Správa zálohování a obnovení virtuálních počítačů Azure pomocí Powershellu](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) vytvoření šifrované virtuální počítače z obnoveného disku, klíče a tajného kódu.
