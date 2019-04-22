---
title: Obnovení služby Key Vault klíč a tajný klíč pro šifrované virtuální počítače pomocí služby Azure Backup
description: Zjistěte, jak obnovit služby Key Vault klíč a tajný klíč ve službě Azure Backup pomocí Powershellu
services: backup
author: geetha
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 08/28/2017
ms.author: geetha
ms.openlocfilehash: 13eb800cd64e0de736b1fdea308a03d8a8d0f046
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59358194"
---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Obnovení služby Key Vault klíč a tajný klíč pro šifrované virtuální počítače pomocí služby Azure Backup

Tento článek pojednává o pomocí zálohování virtuálních počítačů Azure k provedení obnovení šifrovaných virtuálních počítačů Azure, pokud klíč a tajný klíč neexistují v trezoru klíčů. Tyto kroky je možné také pokud chcete udržovat samostatnou kopii klíč (šifrovací klíč klíče) a tajný klíč (šifrovací klíč nástroje BitLocker) pro obnovený virtuální počítač.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

* **Zálohování šifrovaných virtuálních počítačů** – šifrované virtuální počítače Azure byly zálohovány pomocí služby Azure Backup. Přečtěte si článek [Správa zálohování a obnovení virtuálních počítačů Azure pomocí Powershellu](backup-azure-vms-automation.md) podrobnosti o tom, jak zálohovat šifrované virtuální počítače Azure.
* **Konfigurace služby Azure Key Vault** – Ujistěte se, do které klíče a tajné klíče je nutné obnovit služby key vault už existuje. Přečtěte si článek [Začínáme s Azure Key Vault](../key-vault/key-vault-get-started.md) podrobné informace o správě služby key vault.
* **Obnovení disku** – Ujistěte se, právě jste aktivovali úlohy obnovení pro obnovení disků pro šifrované virtuální počítač pomocí [kroky PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm). Je to proto, že tato úloha generuje soubor JSON ve vašem účtu úložiště, který obsahuje klíče a tajné kódy pro šifrovaný virtuální počítač pro obnovení.

## <a name="get-key-and-secret-from-azure-backup"></a>Získejte klíč a tajný klíč z Azure Backup

> [!NOTE]
> Jakmile pro šifrovaný virtuální počítač se obnovila disku, ujistěte se, že:
> * $details se vyplní podrobnosti úlohy obnovení disku, jak je uvedeno v [Powershellu kroky obnovení disků části](backup-azure-vms-automation.md#restore-an-azure-vm)
> * Virtuální počítač by měl být vytvořen z obnovených disků pouze **po obnovení klíče a tajného klíče do trezoru klíčů**.

Dotaz na vlastnosti obnoveného disku pro podrobnosti o úloze.

```powershell
$properties = $details.properties
$storageAccountName = $properties["Target Storage Account Name"]
$containerName = $properties["Config Blob Container Name"]
$encryptedBlobName = $properties["Encryption Info Blob Name"]
```

Nastavit kontext služby Azure storage a obnovte konfigurační soubor JSON obsahující klíče a tajného kódu podrobnosti pro šifrovaný virtuální počítač.

```powershell
Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName '<rg-name>'
$destination_path = 'C:\vmencryption_config.json'
Get-AzStorageBlobContent -Blob $encryptedBlobName -Container $containerName -Destination $destination_path
$encryptionObject = Get-Content -Path $destination_path  | ConvertFrom-Json
```

## <a name="restore-key"></a>Obnovit klíč

Po vygenerování souboru JSON v cílové cestě uvedené výše vygenerovat soubor blob klíče z JSON a kanál na obnovení klíče rutiny vložte klíč (KEK) zpět ve službě key vault.

```powershell
$keyDestination = 'C:\keyDetails.blob'
[io.file]::WriteAllBytes($keyDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyBackupData))
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile $keyDestination
```

## <a name="restore-secret"></a>Obnovit tajný klíč

Pomocí souboru JSON generované nad něj a získat název tajného kódu a hodnotu můžete nastavit tajných kódů rutiny umístění tajný kód (klíče BEK) zpět do služby key vault. Tyto rutiny použít, pokud vaše **virtuální počítač je zašifrovaný pomocí klíče BEK a KEK**.

**Tyto rutiny použijte, pokud je váš virtuální počítač Windows šifrovaný pomocí klíče BEK a KEK.**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

**Tyto rutiny použijte, pokud je šifrovaný pomocí klíče BEK a KEK virtuálního počítače s Linuxem.**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'LinuxPassPhraseFileName';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

Pomocí souboru JSON generované nad něj a získat název tajného kódu a hodnotu můžete nastavit tajných kódů rutiny umístění tajný kód (klíče BEK) zpět do služby key vault. Tyto rutiny použít, pokud vaše **virtuální počítač je zašifrovaný pomocí klíče BEK** pouze.

```powershell
$secretDestination = 'C:\secret.blob'
[io.file]::WriteAllBytes($secretDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyVaultSecretBackupData))
Restore-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -InputFile $secretDestination -Verbose
  ```

> [!NOTE]
> * Hodnota $secretname lze získat odkaz na výstup $encryptionObject.OsDiskKeyAndSecretDetails.SecretUrl a použitím textu po tajných kódů / například výstup adresa URL tajného kódu je https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 a název tajného kódu je B3284AAA-DAAA-4AAA-B393-60CAA848AAAA
> * Hodnota značky DiskEncryptionKeyFileName je stejná jako název tajného kódu.
>
>

## <a name="create-virtual-machine-from-restored-disk"></a>Vytvoření virtuálního počítače z obnoveného disku

Pokud jste zálohovali šifrovaných virtuálních počítačů pomocí Azure Backup pro virtuální počítače, rutin prostředí PowerShell uvedených výše nápovědy obnovit klíče a tajného kódu zpět do služby key vault. Po obnovení je, přečtěte si článek [Správa zálohování a obnovení virtuálních počítačů Azure pomocí Powershellu](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) vytvoření šifrované virtuální počítače z obnoveného disku, klíč a tajný klíč.

## <a name="legacy-approach"></a>Starší verze přístup

Přístup uvedených výše by fungovalo pro všechny body obnovení. Platné pro body obnovení starší než 11. července 2017 pro virtuální počítače jsou šifrované pomocí klíče BEK a KEK by však starším přístupem získání klíče a tajné informace z bodu obnovení. Po dokončení pro šifrované virtuální počítač pomocí úlohy obnovení disku [kroky PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm), ujistěte se, že se načtou $rp platnou hodnotu.

### <a name="restore-key"></a>Obnovit klíč

Pomocí následujících rutin pro získání klíčů (KEK) informace z bodu obnovení a kanál na obnovení klíče rutiny pro její umístění zpět do služby key vault.

```powershell
$rp1 = Get-AzRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation 'C:\Users\downloads'
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile 'C:\Users\downloads'
```

### <a name="restore-secret"></a>Obnovit tajný klíč

Pomocí následujících rutin pro získání tajných informací (klíče BEK) z bodu obnovení a něj nastavit tajných kódů rutiny pro její umístění zpět do služby key vault.

```powershell
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$secretdata = $rp1.KeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://mykeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  'Wrapped BEK'
```

> [!NOTE]
> * Hodnota pro $secretname je možné získat odkaz na výstup $rp1. KeyAndSecretDetails.SecretUrl a pomocí textu po tajných klíčů / například výstup tajný kód je adresa URL https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 a název tajného kódu je B3284AAA-DAAA-4AAA-B393-60CAA848AAAA
> * Hodnota značky DiskEncryptionKeyFileName je stejná jako název tajného kódu.
> * Hodnota pro DiskEncryptionKeyEncryptionKeyURL lze získat z trezoru klíčů po obnovení zpět klíče a pomocí [Get-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/get-azurekeyvaultkey) rutiny
>
>

## <a name="next-steps"></a>Další postup

Po obnovení klíče a tajného kódu zpět do služby key vault, najdete v článku [Správa zálohování a obnovení virtuálních počítačů Azure pomocí Powershellu](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) vytvoření šifrované virtuální počítače z obnoveného disku, klíče a tajného kódu.
