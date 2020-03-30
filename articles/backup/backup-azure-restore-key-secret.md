---
title: Obnovení klíče trezoru & tajný klíč pro šifrovaný virtuální virtuální ms
description: Zjistěte, jak obnovit klíč a tajný klíč trezoru klíčů v Azure Backup pomocí PowerShellu
ms.topic: conceptual
ms.date: 08/28/2017
ms.openlocfilehash: 35bcb919cadd46c603b1f2ad49742c5435f873d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75450051"
---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Obnovení klíče a tajného kódu Key Vault pro šifrované virtuální počítače pomocí Azure Backup

Tento článek popisuje použití zálohování virtuálních počítačích Azure k provedení obnovení šifrovaných virtuálních počítačích Azure, pokud váš klíč a tajný klíč neexistují v trezoru klíčů. Tyto kroky lze také použít, pokud chcete zachovat samostatnou kopii klíče (klíč šifrovací klíč) a tajný klíč (BitLocker encryption key) pro obnovený virtuální ho.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

* **Zálohované šifrované virtuální počítače** – šifrované virtuální počítače Azure byly zálohovány pomocí azure backup. V článku [Správa zálohování a obnovení virtuálních počítačů Azure pomocí PowerShellu](backup-azure-vms-automation.md) najdete podrobnosti o tom, jak zálohovat šifrované virtuální počítače Azure.
* **Konfigurace trezoru klíčů Azure** – ujistěte se, že trezor klíčů, do kterého je třeba obnovit klíče a tajné klíče, je již k dispozici. Podrobnosti o správě trezoru klíčů najdete v článku [Začínáme s trezorem klíčů Azure.](../key-vault/key-vault-get-started.md)
* **Obnovení disku** – ujistěte se, že jste spustili úlohu obnovení pro obnovení disků pro šifrovaný virtuální virtuální počítače pomocí [kroků prostředí PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm). Důvodem je, že tato úloha generuje soubor JSON ve vašem účtu úložiště obsahující klíče a tajné klíče pro šifrovaný virtuální počítač, který má být obnoven.

## <a name="get-key-and-secret-from-azure-backup"></a>Získání klíče a tajného klíče z Azure Backup

> [!NOTE]
> Po obnovení disku pro šifrovaný virtuální počítače zajistěte, aby:
>
> * $details je naplněn podrobnostmi o úloze obnovení disku, jak je uvedeno v [krocích prostředí PowerShell v části Obnovit disky.](backup-azure-vms-automation.md#restore-an-azure-vm)
> * Virtuální počítače by měl být vytvořen z obnovených disků pouze **po obnovení klíče a tajný klíč do trezoru klíčů**.

Dotaz na obnovené vlastnosti disku pro podrobnosti o úloze.

```powershell
$properties = $details.properties
$storageAccountName = $properties["Target Storage Account Name"]
$containerName = $properties["Config Blob Container Name"]
$encryptedBlobName = $properties["Encryption Info Blob Name"]
```

Nastavte kontext úložiště Azure a obnovte konfigurační soubor JSON obsahující klíč a tajné podrobnosti pro šifrovaný virtuální počítač.

```powershell
Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName '<rg-name>'
$destination_path = 'C:\vmencryption_config.json'
Get-AzStorageBlobContent -Blob $encryptedBlobName -Container $containerName -Destination $destination_path
$encryptionObject = Get-Content -Path $destination_path  | ConvertFrom-Json
```

## <a name="restore-key"></a>Obnovit klíč

Jakmile je soubor JSON vygenerován v cílové cestě uvedené výše, generovat soubor objektů blob klíče z JSON a krmit jej obnovit rutinu klíče, aby klíč (KEK) zpět do trezoru klíčů.

```powershell
$keyDestination = 'C:\keyDetails.blob'
[io.file]::WriteAllBytes($keyDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyBackupData))
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile $keyDestination
```

## <a name="restore-secret"></a>Obnovit tajný klíč

Pomocí souboru JSON generovaného výše získat tajný název a hodnotu a krmit ji nastavit tajnou rutinu, aby tajný klíč (BEK) zpět do trezoru klíčů.Tyto rutiny použijte, pokud je váš **virtuální počítač šifrovaný pomocí BEK a KEK**.

**Tyto rutiny použijte, pokud je virtuální počítač se systémem Windows šifrován pomocí BEK a KEK.**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

**Tyto rutiny použijte, pokud je váš virtuální počítač s Linuxem šifrován pomocí BEK a KEK.**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'LinuxPassPhraseFileName';'DiskEncryptionKeyEncryptionKeyURL' = <Key_url_of_newly_restored_key>;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

Pomocí souboru JSON generovaného výše získat tajný název a hodnotu a krmit ji nastavit tajnou rutinu, aby tajný klíč (BEK) zpět do trezoru klíčů.Tyto rutiny použijte, pokud **je váš virtuální počítač šifrovaný pouze pomocí BEK.**

```powershell
$secretDestination = 'C:\secret.blob'
[io.file]::WriteAllBytes($secretDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyVaultSecretBackupData))
Restore-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -InputFile $secretDestination -Verbose
  ```

> [!NOTE]
>
> * Hodnotu pro $secretname lze získat odkazem na výstup $encryptionObject.OsDiskKeyAndSecretDetails.SecretUrl a pomocí textu po tajemství/ např. https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163
> * Hodnota značky DiskEncryptionKeyFileName je stejná jako název tajného klíče.
>
>

## <a name="create-virtual-machine-from-restored-disk"></a>Vytvoření virtuálního počítače z obnoveného disku

Pokud jste zálohovali šifrovaný virtuální počítač pomocí zálohování virtuálních počítačích Azure, výše uvedené rutiny prostředí PowerShell vám pomůžou obnovit klíč a tajný klíč zpět do trezoru klíčů. Po jejich obnovení, odkazovat na článek [Správa zálohování a obnovení virtuálních počítačů Azure pomocí PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) u vytvoření šifrované virtuální počítače z obnoveného disku, klíče a tajných kódů.

## <a name="legacy-approach"></a>Starší přístup

Výše uvedený přístup by fungoval pro všechny body obnovy. Starší přístup k získání klíčových a tajných informací z bodu obnovení by však byl platný pro body obnovení starší než 11. Po dokončení úlohy obnovení disku pro šifrovaný virtuální počítače pomocí [kroků prostředí PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm)se ujistěte, že $rp je naplněn platnou hodnotou.

### <a name="restore-key"></a>Obnovit klíč

Pomocí následujících rutin získáte informace o klíči (KEK) z bodu obnovení a podejte je k obnovení rutiny klíče a vraťte ji zpět do trezoru klíčů.

```powershell
$rp1 = Get-AzRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation 'C:\Users\downloads'
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile 'C:\Users\downloads'
```

### <a name="restore-secret"></a>Obnovit tajný klíč

Pomocí následujících rutin získat tajné informace (BEK) z bodu obnovení a krmit nastavit tajné rutiny, aby ji zpět do trezoru klíčů.

```powershell
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$secretdata = $rp1.KeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://mykeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  'Wrapped BEK'
```

> [!NOTE]
>
> * Hodnotu pro $secretname lze získat odkazem na výstup $rp1. KeyAndSecretDetails.SecretUrl a pomocí textu po tajemství/ např. https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163
> * Hodnota značky DiskEncryptionKeyFileName je stejná jako název tajného klíče.
> * Hodnotu pro DiskEncryptionKeyEncryptionKeyURL lze získat z trezoru klíčů po obnovení klíče zpět a pomocí [Rutina Get-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/get-azurekeyvaultkey)
>
>

## <a name="next-steps"></a>Další kroky

Po obnovení klíče a tajného klíče zpět do trezoru klíčů, odkazovat na článek [Správa zálohování a obnovení virtuálních počítačů Azure pomocí PowerShellu](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) k vytvoření šifrované virtuální počítače z obnoveného disku, klíče a tajného klíče.
