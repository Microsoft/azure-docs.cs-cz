---
title: Obnovit tajný klíč Key Vault Key & pro šifrovaný virtuální počítač
description: Naučte se obnovit Key Vault klíč a tajný kód v Azure Backup pomocí PowerShellu.
ms.topic: conceptual
ms.date: 08/28/2017
ms.openlocfilehash: 456ce18f253ffa02cd6b13826a7839f18beecba7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88827082"
---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Obnovení klíče a tajného kódu Key Vault pro šifrované virtuální počítače pomocí Azure Backup

Tento článek pojednává o použití zálohování virtuálních počítačů Azure k provedení obnovení šifrovaných virtuálních počítačů Azure, pokud klíč a tajný kód neexistují v trezoru klíčů. Tyto kroky se dají použít i v případě, že chcete zachovat samostatnou kopii klíče (šifrovací klíč klíče) a tajný klíč (šifrovací klíč BitLockeru) pro obnovený virtuální počítač.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

* **Zálohování šifrovaných virtuálních počítačů** – šifrované virtuální počítače Azure se zálohovali pomocí Azure Backup. Podrobnosti o tom, jak zálohovat šifrované virtuální počítače Azure, najdete v článku [Správa zálohování a obnovení virtuálních počítačů Azure pomocí PowerShellu](backup-azure-vms-automation.md) .
* **Konfigurace Azure Key Vault** – Ujistěte se, že je již přítomen Trezor klíčů, pro který jsou klíče a tajné kódy nutné obnovit. Podrobnosti o správě trezoru klíčů najdete v článku [Začínáme s Azure Key Vault](../key-vault/general/overview.md) .
* **Obnovit disk** – Ujistěte se, že jste aktivovali úlohu obnovení pro obnovení disků pro šifrovaný virtuální počítač pomocí [kroků PowerShellu](backup-azure-vms-automation.md#restore-an-azure-vm). Důvodem je to, že tato úloha v účtu úložiště vygeneruje soubor JSON obsahující klíče a tajné klíče, aby se obnovil zašifrovaný virtuální počítač.

## <a name="get-key-and-secret-from-azure-backup"></a>Získání klíče a tajného klíče z Azure Backup

> [!NOTE]
> Po obnovení disku pro šifrovaný virtuální počítač zajistěte, aby:
>
> * $details se naplní Podrobnosti úlohy obnovení disku, jak je uvedeno v [části kroky prostředí PowerShell v části obnovení disků](backup-azure-vms-automation.md#restore-an-azure-vm) .
> * Virtuální počítač by se měl vytvořit z obnovených disků jenom **po obnovení klíče a tajného klíče do trezoru klíčů**.

Dotaz na vlastnosti obnoveného disku pro podrobnosti úlohy.

```powershell
$properties = $details.properties
$storageAccountName = $properties["Target Storage Account Name"]
$containerName = $properties["Config Blob Container Name"]
$encryptedBlobName = $properties["Encryption Info Blob Name"]
```

Nastavte kontext služby Azure Storage a obnovte konfigurační soubor JSON obsahující informace o klíčovém a tajném klíči pro šifrovaný virtuální počítač.

```powershell
Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName '<rg-name>'
$destination_path = 'C:\vmencryption_config.json'
Get-AzStorageBlobContent -Blob $encryptedBlobName -Container $containerName -Destination $destination_path
$encryptionObject = Get-Content -Path $destination_path  | ConvertFrom-Json
```

## <a name="restore-key"></a>Obnovit klíč

Jakmile se soubor JSON vygeneruje v cílové cestě uvedené výše, vygenerujte soubor objektu BLOB klíče z JSON a zapněte ho k obnovení rutiny Key pro vložení klíče (KEK) zpátky do trezoru klíčů.

```powershell
$keyDestination = 'C:\keyDetails.blob'
[io.file]::WriteAllBytes($keyDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyBackupData))
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile $keyDestination
```

## <a name="restore-secret"></a>Obnovit tajný klíč

Pomocí souboru JSON, který jste vygenerovali nahoře, získáte název a hodnotu tajného klíče a zadáte ho k nastavení tajné rutiny pro vložení tajného klíče (klíče bek) zpátky do trezoru klíčů.Tyto rutiny použijte, pokud **je váš virtuální počítač zašifrovaný pomocí klíče bek a KEK**.

**Tyto rutiny použijte, pokud je váš virtuální počítač s Windows zašifrovaný pomocí klíče bek a KEK.**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

**Tyto rutiny použijte, pokud je váš virtuální počítač se systémem Linux zašifrovaný pomocí klíče bek a KEK.**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'LinuxPassPhraseFileName';'DiskEncryptionKeyEncryptionKeyURL' = <Key_url_of_newly_restored_key>;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

Pomocí souboru JSON, který jste vygenerovali nahoře, získáte název a hodnotu tajného klíče a zadáte ho k nastavení tajné rutiny pro vložení tajného klíče (klíče bek) zpátky do trezoru klíčů.Tyto rutiny použijte, pokud **je váš virtuální počítač zašifrovaný jenom pomocí klíče bek** .

```powershell
$secretDestination = 'C:\secret.blob'
[io.file]::WriteAllBytes($secretDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyVaultSecretBackupData))
Restore-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -InputFile $secretDestination -Verbose
  ```

> [!NOTE]
>
> * Hodnotu pro $secretname lze získat odkazem na výstup $encryptionObject. OsDiskKeyAndSecretDetails. SecretUrl a použitím textu po tajných klíčích, například adresa URL výstupního tajného kódu je `https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163` B3284AAA-DAAA-4AAA-B393-60CAA848AAAA
> * Hodnota tagu DiskEncryptionKeyFileName je stejná jako název tajného kódu.
>
>

## <a name="create-virtual-machine-from-restored-disk"></a>Vytvořit virtuální počítač z obnoveného disku

Pokud jste zálohovali zašifrovaný virtuální počítač pomocí služby zálohování virtuálních počítačů Azure, výše uvedené rutiny prostředí PowerShell vám pomůžou obnovit klíč a tajný kód zpátky do trezoru klíčů. Po obnovení najdete informace v článku [Správa zálohování a obnovení virtuálních počítačů Azure pomocí prostředí PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) k vytvoření šifrovaných virtuálních počítačů z obnoveného disku, klíče a tajného klíče.

## <a name="legacy-approach"></a>Starší verze přístupu

Výše zmíněný postup by fungoval pro všechny body obnovení. Starší přístup k získání klíčů a tajných informací z bodu obnovení však bude platný pro body obnovení starší než 11. července 2017 pro virtuální počítače zašifrované pomocí klíče bek a KEK. Po dokončení úlohy obnovení disku pro šifrovaný virtuální počítač pomocí [kroků PowerShellu](backup-azure-vms-automation.md#restore-an-azure-vm)zajistěte, aby $RP naplněna platnou hodnotou.

### <a name="restore-key-legacy-approach"></a>Obnovit klíč (starší přístup)

Pomocí následujících rutin můžete získat informace o klíči (KEK) z bodu obnovení a obsloužitte ho k obnovení rutiny klíče pro vrácení zpět do trezoru klíčů.

```powershell
$rp1 = Get-AzRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation 'C:\Users\downloads'
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile 'C:\Users\downloads'
```

### <a name="restore-secret-legacy-approach"></a>Obnovit tajný klíč (starší přístup)

Pomocí následujících rutin získáte informace o tajných klíčích (klíče bek) z bodu obnovení a obdržíte je k nastavení tajné rutiny tak, aby byla vrácena zpět do trezoru klíčů.

```powershell
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$secretdata = $rp1.KeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://mykeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  'Wrapped BEK'
```

> [!NOTE]
>
> * Hodnotu pro $secretname lze získat odkazem na výstup $rp 1. KeyAndSecretDetails. SecretUrl a používání textu po tajných klíčích/příkladech je adresa URL výstupního tajného kódu `https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163` a název tajného klíče je B3284AAA-DAAA-4AAA-B393-60CAA848AAAA
> * Hodnota DiskEncryptionKeyFileName značky je shodná s názvem tajného kódu.
> * Hodnota pro DiskEncryptionKeyEncryptionKeyURL se dá získat z trezoru klíčů po obnovení klíčů zpátky a pomocí rutiny [Get-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/get-azurekeyvaultkey) .
>
>

## <a name="next-steps"></a>Další kroky

Po obnovení klíče a tajného kódu zpátky do trezoru klíčů si přečtěte článek [Správa zálohování a obnovení virtuálních počítačů Azure pomocí PowerShellu](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) a vytvoření šifrovaných virtuálních počítačů z obnoveného disku, klíče a tajného klíče.
