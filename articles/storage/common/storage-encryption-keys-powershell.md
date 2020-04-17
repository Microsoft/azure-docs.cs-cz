---
title: Konfigurace klíčů spravovaných zákazníkem pomocí PowerShellu
titleSuffix: Azure Storage
description: Přečtěte si, jak pomocí PowerShellu nakonfigurovat klíče spravované zákazníky pro šifrování Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: bfc2e256396904456a7ee0fd8b6173c00a5f53d7
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456392"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-powershell"></a>Konfigurace klíčů spravovaných zákazníkem pomocí služby Azure Key Vault pomocí PowerShellu

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Tento článek ukazuje, jak nakonfigurovat Azure Key Vault s klíči spravované zákazníky pomocí PowerShellu. Informace o tom, jak vytvořit trezor klíčů pomocí příkazového příkazu k řešení Azure, najdete v [tématu Úvodní příručka: Nastavení a načtení tajného klíče z azure key vaultu pomocí PowerShellu](../../key-vault/secrets/quick-create-powershell.md).

## <a name="assign-an-identity-to-the-storage-account"></a>Přiřazení identity k účtu úložiště

Chcete-li povolit klíče spravované zákazníkem pro váš účet úložiště, nejprve přiřaďte účtu úložiště spravovanou spravanou identitu přiřazenou systémem. Tuto spravovanou identitu použijete k udělení oprávnění účtu úložiště pro přístup k trezoru klíčů.

Chcete-li přiřadit spravovanou identitu pomocí prostředí PowerShell, zavolejte [set-azstorageaccount](/powershell/module/az.storage/set-azstorageaccount). Nezapomeňte nahradit zástupné hodnoty v závorkách vlastními hodnotami.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Další informace o konfiguraci spravovaných identit přiřazených systémem pomocí PowerShellu najdete [v tématu Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí PowerShellu](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Vytvoření nového trezoru klíčů

Chcete-li vytvořit nový trezor klíčů pomocí prostředí PowerShell, zavolejte [new-azkeyvault](/powershell/module/az.keyvault/new-azkeyvault). Trezor klíčů, který používáte k ukládání klíčů spravovaných zákazníky pro šifrování Azure Storage, musí mít povolená dvě nastavení ochrany klíčů, **obnovitelné odstranění** a **neodstraňovat**.

Nezapomeňte nahradit zástupné hodnoty v závorkách vlastními hodnotami.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

Informace o povolení **funkce Obnovitelné odstranění** a **nečistit** v existujícím trezoru klíčů pomocí prostředí PowerShell naleznete v částech s názvem **Povolení obnovitelného odstranění** a Povolení ochrany proti **vymazání** v [tématu Použití obnovitelného odstranění s prostředím PowerShell](../../key-vault/general/soft-delete-powershell.md).

## <a name="configure-the-key-vault-access-policy"></a>Konfigurace zásad přístupu k trezoru klíčů

Dále nakonfigurujte zásady přístupu pro trezor klíčů tak, aby účet úložiště měl oprávnění k přístupu k němu. V tomto kroku použijete spravovanou identitu, kterou jste dříve přiřadili k účtu úložiště.

Chcete-li nastavit zásady přístupu pro trezor klíčů, zavolejte [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Nezapomeňte nahradit zástupné hodnoty v závorkách vlastními hodnotami a použít proměnné definované v předchozích příkladech.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

## <a name="create-a-new-key"></a>Vytvoření nového klíče

Dále vytvořte nový klíč v trezoru klíčů. Chcete-li vytvořit nový klíč, zavolejte [add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Nezapomeňte nahradit zástupné hodnoty v závorkách vlastními hodnotami a použít proměnné definované v předchozích příkladech.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

Šifrováním azure storage jsou podporované jenom 2048bitové klíče RSA a RSA-HSM. Další informace o klíčích najdete v **tématu Klíče trezoru klíčů** v [tématu O klíčích, tajných klíčích a certifikátech trezoru klíčů Azure](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurace šifrování pomocí klíčů spravovaných zákazníkem

Ve výchozím nastavení používá šifrování Azure Storage klíče spravované microsoftem. V tomto kroku nakonfigurujte účet Úložiště Azure tak, aby používal klíče spravované zákazníkem, a zadejte klíč, který chcete přidružit k účtu úložiště.

Volání [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) aktualizovat nastavení šifrování účtu úložiště, jak je znázorněno v následujícím příkladu. Zahrnout **-KeyvaultEncryption** možnost povolit klientem spravované klíče pro účet úložiště. Nezapomeňte nahradit zástupné hodnoty v závorkách vlastními hodnotami a použít proměnné definované v předchozích příkladech.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>Aktualizace verze klíče

Když vytvoříte novou verzi klíče, budete muset aktualizovat účet úložiště, aby se používala nová verze. Nejprve zavolejte [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) získat nejnovější verzi klíče. Potom volání [MA-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) aktualizovat nastavení šifrování účtu úložiště použít novou verzi klíče, jak je znázorněno v předchozí části.

## <a name="use-a-different-key"></a>Použití jiného klíče

Chcete-li změnit klíč používaný pro šifrování úložiště Azure, zavolejte [Set-AzStorageAccount,](/powershell/module/az.storage/set-azstorageaccount) jak je znázorněno v [tématu Konfigurace šifrování pomocí klíčů spravovaných zákazníkem,](#configure-encryption-with-customer-managed-keys) a zadejte nový název a verzi klíče. Pokud je nový klíč v jiném trezoru klíčů, aktualizujte také identifikátor URI trezoru klíčů.

## <a name="revoke-customer-managed-keys"></a>Odvolat klíče spravované zákazníkem

Pokud se domníváte, že klíč mohl být ohrožen, můžete odvolat klíče spravované zákazníkem odebráním zásad přístupu k trezoru klíčů. Chcete-li odvolat klíč spravovaný zákazníkem, zavolejte příkaz [Remove-AzKeyVaultAccessPolicy,](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) jak je znázorněno v následujícím příkladu. Nezapomeňte nahradit zástupné hodnoty v závorkách vlastními hodnotami a použít proměnné definované v předchozích příkladech.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

## <a name="disable-customer-managed-keys"></a>Zakázání klíčů spravovaných zákazníkem

Když zakážete klíče spravované zákazníky, váš účet úložiště se opět zašifruje pomocí klíčů spravovaných společností Microsoft. Chcete-li zakázat klíče spravované zákazníkem, zavolejte [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) s `-StorageEncryption` možností, jak je znázorněno v následujícím příkladu. Nezapomeňte nahradit zástupné hodnoty v závorkách vlastními hodnotami a použít proměnné definované v předchozích příkladech.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

## <a name="next-steps"></a>Další kroky

- [Šifrování Azure Storage pro data v klidovém stavu](storage-service-encryption.md)
- [Co je Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
