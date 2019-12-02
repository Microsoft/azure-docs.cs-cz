---
title: Konfigurace klíčů spravovaných zákazníkem pomocí Azure Key Vault pomocí prostředí PowerShell – Azure Storage
description: Naučte se používat PowerShell ke konfiguraci klíčů spravovaných zákazníkem pro Azure Storage šifrování. Klíče spravované zákazníkem umožňují vytvářet, otáčet, zakazovat a odvolávat řízení přístupu.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/20/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: bd723787d9cea2d3b9d81ae9db63c70a21190854
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666218"
---
# <a name="configure-customer-managed-keys-for-azure-storage-by-using-powershell"></a>Konfigurace klíčů spravovaných zákazníkem pro Azure Storage pomocí prostředí PowerShell

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

V tomto článku se dozvíte, jak nakonfigurovat Azure Key Vault s použitím klíčů spravovaných zákazníkem pomocí PowerShellu. Informace o tom, jak vytvořit Trezor klíčů pomocí Azure CLI, najdete v tématu [rychlý Start: nastavení a načtení tajného klíče z Azure Key Vault pomocí prostředí PowerShell](../../key-vault/quick-create-powershell.md).

> [!IMPORTANT]
> Použití klíčů spravovaných zákazníkem s šifrováním Azure Storage vyžaduje, aby byly v trezoru klíčů nastaveny dvě vlastnosti, **obnovitelné odstranění** a **nemazatelné**. Tyto vlastnosti nejsou ve výchozím nastavení povolené. Pokud chcete tyto vlastnosti povolit, použijte PowerShell nebo rozhraní příkazového řádku Azure CLI.
> Podporují se jenom klíče RSA a velikost klíče 2048.

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

## <a name="configure-the-key-vault-access-policy"></a>Konfigurace zásad přístupu trezoru klíčů

Dále nakonfigurujte zásady přístupu pro Trezor klíčů, aby měl účet úložiště oprávnění k přístupu. V tomto kroku použijete spravovanou identitu, kterou jste dříve přiřadili k účtu úložiště.

Pro nastavení zásad přístupu pro Trezor klíčů volejte [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami a použít proměnné definované v předchozích příkladech.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Vytvořit nový klíč

V dalším kroku vytvořte nový klíč v trezoru klíčů. Chcete-li vytvořit nový klíč, zavolejte [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami a použít proměnné definované v předchozích příkladech.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurace šifrování pomocí klíčů spravovaných zákazníkem

Ve výchozím nastavení používá Azure Storage šifrování klíče spravované společností Microsoft. V tomto kroku nakonfigurujte Azure Storage účet pro použití klíčů spravovaných zákazníkem a zadejte klíč, který chcete přidružit k účtu úložiště.

Voláním [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) aktualizujte nastavení šifrování účtu úložiště. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami a použít proměnné definované v předchozích příkladech.

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

## <a name="next-steps"></a>Další kroky

- [Azure Storage šifrování dat v klidovém umístění](storage-service-encryption.md)
- [Co je Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
