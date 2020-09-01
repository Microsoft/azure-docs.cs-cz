---
title: Použití PowerShellu ke konfiguraci klíčů spravovaných zákazníkem
titleSuffix: Azure Storage
description: Naučte se používat PowerShell ke konfiguraci klíčů spravovaných zákazníkem pro Azure Storage šifrování.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/24/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2beaae3fd6aaae719ac84ef86d7dd7877c94f757
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2020
ms.locfileid: "89076119"
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

Pokud chcete vytvořit nový trezor klíčů pomocí PowerShellu, nainstalujte verzi 2.0.0 nebo novější z modulu PowerShellu [AZ. Key trezor](https://www.powershellgallery.com/packages/Az.KeyVault/2.0.0) . Pak zavolejte [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) a vytvořte nový trezor klíčů.

Trezor klíčů, který použijete k uložení klíčů spravovaných zákazníkem pro Azure Storage šifrování, musí mít povolené dvě nastavení ochrany klíčů, **obnovitelné odstranění** a **nemazatelné**. Při vytváření nového trezoru klíčů je ve výchozím nastavení ve výchozím nastavení ve verzi 2.0.0 a novějšího modulu příkazového trezoru AZ.

Následující příklad vytvoří nový trezor klíčů s povolenými vlastnostmi **obnovitelného odstranění** a **nevyprázdnění** . Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
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

Ve výchozím nastavení používá Azure Storage šifrování klíče spravované společností Microsoft. V tomto kroku nakonfigurujte Azure Storage účet tak, aby používal klíče spravované zákazníkem s Azure Key Vault, a pak zadejte klíč, který chcete přidružit k účtu úložiště.

Když konfigurujete šifrování s použitím klíčů spravovaných zákazníkem, můžete automaticky aktualizovat klíč používaný k šifrování při změně verze klíče v přidruženém trezoru klíčů. Alternativně můžete explicitně zadat verzi klíče, která se má použít pro šifrování, dokud se verze klíče ručně neaktualizuje.

> [!NOTE]
> Pokud chcete klíč otočit, vytvořte v Azure Key Vault novou verzi klíče. Azure Storage nezpracovává rotaci klíče v Azure Key Vault, takže budete muset klíč otočit ručně nebo vytvořit funkci, která ho otočí podle plánu.

### <a name="configure-encryption-to-automatically-update-the-key-version"></a>Konfigurace šifrování pro automatickou aktualizaci verze klíče

Pokud chcete nakonfigurovat šifrování pomocí klíčů spravovaných zákazníkem, aby se automaticky aktualizovala verze klíče, nainstalujte modul [AZ. Storage](https://www.powershellgallery.com/packages/Az.Storage) , verze 2.0.0 nebo novější.

Pokud chcete automatickou aktualizaci verze klíče pro klíč spravovaný zákazníkem, vynechejte verzi klíče při konfiguraci šifrování s použitím klíčů spravovaných zákazníkem pro účet úložiště. Voláním [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) aktualizujte nastavení šifrování účtu úložiště, jak je znázorněno v následujícím příkladu, a zahrňte možnost **-KeyvaultEncryption** pro povolení klíčů spravovaných zákazníkem pro účet úložiště. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami a použít proměnné definované v předchozích příkladech.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVaultUri $keyVault.VaultUri
```

### <a name="configure-encryption-for-manual-updating-of-key-versions"></a>Konfigurace šifrování pro ruční aktualizace verzí klíčů

Pokud chcete explicitně zadat verzi klíče, která se má použít pro šifrování, poskytněte klíčovou verzi při konfiguraci šifrování pomocí klíčů spravovaných zákazníkem pro účet úložiště. Voláním [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) aktualizujte nastavení šifrování účtu úložiště, jak je znázorněno v následujícím příkladu, a zahrňte možnost **-KeyvaultEncryption** pro povolení klíčů spravovaných zákazníkem pro účet úložiště. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami a použít proměnné definované v předchozích příkladech.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

Když ručně aktualizujete verzi klíče, budete muset aktualizovat nastavení šifrování účtu úložiště tak, aby používalo novou verzi. Nejdřív zavolejte [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) , abyste získali nejnovější verzi klíče. Pak zavolejte [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) a aktualizujte nastavení šifrování účtu úložiště tak, aby používala novou verzi klíče, jak je znázorněno v předchozím příkladu.

## <a name="use-a-different-key"></a>Použít jiný klíč

Pokud chcete změnit klíč, který se používá pro Azure Storage šifrování, zavolejte [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) , jak je znázorněno v části [Konfigurace šifrování pomocí klíčů spravovaných zákazníkem](#configure-encryption-with-customer-managed-keys) , a zadejte nový název a verzi klíče. Pokud je nový klíč v jiném trezoru klíčů, aktualizujte také identifikátor URI trezoru klíčů.

## <a name="revoke-customer-managed-keys"></a>Odvolání klíčů spravovaných zákazníkem

Klíče spravované zákazníkem můžete odvolat odebráním zásad přístupu trezoru klíčů. Chcete-li odvolat klíč spravovaný zákazníkem, zavolejte příkaz [Remove-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) , jak je znázorněno v následujícím příkladu. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami a použít proměnné definované v předchozích příkladech.

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
