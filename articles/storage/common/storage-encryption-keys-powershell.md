---
title: Použití PowerShellu ke konfiguraci klíčů spravovaných zákazníkem
titleSuffix: Azure Storage
description: Naučte se používat PowerShell ke konfiguraci klíčů spravovaných zákazníkem pro Azure Storage šifrování.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 6b2983bbaf22ae1b9e09ff3362a4bc06e6658b33
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85506190"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-powershell"></a>Konfigurace klíčů spravovaných zákazníkem pomocí Azure Key Vault s využitím PowerShellu

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

V tomto článku se dozvíte, jak nakonfigurovat Azure Key Vault s použitím klíčů spravovaných zákazníkem pomocí PowerShellu. Informace o tom, jak vytvořit Trezor klíčů pomocí Azure CLI, najdete v tématu [rychlý Start: nastavení a načtení tajného klíče z Azure Key Vault pomocí prostředí PowerShell](../../key-vault/secrets/quick-create-powershell.md).

## <a name="assign-an-identity-to-the-storage-account"></a>Přiřazení identity k účtu úložiště

Pokud chcete pro svůj účet úložiště povolit klíče spravované zákazníkem, nejdřív přiřaďte k účtu úložiště spravovanou identitu přiřazenou systémem. Pomocí této spravované identity udělíte účtu úložiště oprávnění k přístupu k trezoru klíčů.

Chcete-li přiřadit spravovanou identitu pomocí prostředí PowerShell, zavolejte rutinu [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Další informace o konfiguraci spravovaných identit přiřazených systémem pomocí PowerShellu najdete v tématu [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí PowerShellu](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Vytvořit nový trezor klíčů

Pokud chcete vytvořit nový trezor klíčů pomocí PowerShellu, volejte rutinu [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Trezor klíčů, který použijete k uložení klíčů spravovaných zákazníkem pro Azure Storage šifrování, musí mít povolené dvě nastavení ochrany klíčů, **obnovitelné odstranění** a **nemazatelné**.

Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

Informace o tom, jak povolit **obnovitelné odstranění** a **Nemazat** existující Trezor klíčů pomocí PowerShellu, najdete v částech s názvem **Povolení obnovitelného odstranění** a **Povolení ochrany vyprázdnit** v tématu [Použití obnovitelného odstranění pomocí prostředí PowerShell](../../key-vault/general/soft-delete-powershell.md).

## <a name="configure-the-key-vault-access-policy"></a>Konfigurace zásad přístupu trezoru klíčů

Dále nakonfigurujte zásady přístupu pro Trezor klíčů, aby měl účet úložiště oprávnění k přístupu. V tomto kroku použijete spravovanou identitu, kterou jste dříve přiřadili k účtu úložiště.

Pro nastavení zásad přístupu pro Trezor klíčů volejte [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami a použít proměnné definované v předchozích příkladech.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

## <a name="create-a-new-key"></a>Vytvořit nový klíč

V dalším kroku vytvořte nový klíč v trezoru klíčů. Chcete-li vytvořit nový klíč, zavolejte [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami a použít proměnné definované v předchozích příkladech.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

Šifrování úložiště Azure podporuje klíče RSA a RSA-HSM velikostí 2048, 3072 a 4096. Další informace o klíčích najdete v tématu **Key Vault Keys** v tématu [informace o Azure Key Vaultch klíčích, tajných klíčích a certifikátech](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurace šifrování pomocí klíčů spravovaných zákazníkem

Ve výchozím nastavení používá Azure Storage šifrování klíče spravované společností Microsoft. V tomto kroku nakonfigurujte Azure Storage účet pro použití klíčů spravovaných zákazníkem a zadejte klíč, který chcete přidružit k účtu úložiště.

Voláním [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) aktualizujte nastavení šifrování účtu úložiště, jak je znázorněno v následujícím příkladu. Zahrňte možnost **-KeyvaultEncryption** , která povolí klíče spravované zákazníkem pro účet úložiště. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami a použít proměnné definované v předchozích příkladech.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>Aktualizace verze klíče

Když vytváříte novou verzi klíče, budete muset aktualizovat účet úložiště, aby používal novou verzi. Nejdřív zavolejte [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) , abyste získali nejnovější verzi klíče. Pak zavolejte [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) a aktualizujte nastavení šifrování účtu úložiště tak, aby používala novou verzi klíče, jak je znázorněno v předchozí části.

## <a name="use-a-different-key"></a>Použít jiný klíč

Pokud chcete změnit klíč, který se používá pro Azure Storage šifrování, zavolejte [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) , jak je znázorněno v části [Konfigurace šifrování pomocí klíčů spravovaných zákazníkem](#configure-encryption-with-customer-managed-keys) , a zadejte nový název a verzi klíče. Pokud je nový klíč v jiném trezoru klíčů, aktualizujte také identifikátor URI trezoru klíčů.

## <a name="revoke-customer-managed-keys"></a>Odvolání klíčů spravovaných zákazníkem

Pokud se domníváte, že došlo k ohrožení bezpečnosti klíče, můžete odvolat klíče spravované zákazníkem odebráním zásad přístupu trezoru klíčů. Chcete-li odvolat klíč spravovaný zákazníkem, zavolejte příkaz [Remove-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) , jak je znázorněno v následujícím příkladu. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami a použít proměnné definované v předchozích příkladech.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

## <a name="disable-customer-managed-keys"></a>Zakázat klíče spravované zákazníkem

Když zakážete klíče spravované zákazníkem, váš účet úložiště se znovu zašifruje pomocí klíčů spravovaných Microsoftem. Chcete-li zakázat klíče spravované zákazníkem, zavolejte [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) s `-StorageEncryption` možností, jak je znázorněno v následujícím příkladu. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami a použít proměnné definované v předchozích příkladech.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

## <a name="next-steps"></a>Další kroky

- [Šifrování služby Azure Storage pro neaktivní uložená data](storage-service-encryption.md)
- [Co je Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
