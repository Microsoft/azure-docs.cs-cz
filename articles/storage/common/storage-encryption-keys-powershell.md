---
title: Konfigurovat klíče spravované zákazníkem pro šifrování Azure Storage z prostředí PowerShell
description: Další informace o použití Powershellu ke konfiguraci klíče spravované zákazníkem pro šifrování Azure Storage. Klíče spravované zákazníkem umožňují vytvořit, otáčení, zakázat a odvolat přístup k ovládacím prvkům.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: be876b370cd476bee2af7d90a9f0433fd80de3b4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233692"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-powershell"></a>Konfigurovat klíče spravované zákazníkem pro šifrování Azure Storage z prostředí PowerShell

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Tento článek ukazuje, jak konfigurovat službu key vault pomocí klíčů spravovaných zákazníkem pomocí Powershellu.

## <a name="assign-an-identity-to-the-storage-account"></a>Přiřaďte identitu do účtu úložiště

Pokud chcete povolit klíče spravované zákazníkem pro váš účet úložiště, mu nejdřív přiřadíte systém přiřadil spravovanou identitu do účtu úložiště. Tuto spravovanou identitu budete používat k udělení oprávnění účtu úložiště pro přístup k trezoru klíčů.

Chcete-li přiřadit spravovanou identitu pomocí prostředí PowerShell, zavolejte [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). Nezapomeňte nahradit zástupné hodnoty v závorkách vlastními hodnotami.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Další informace o konfiguraci systému uživatelsky přiřazené identity spravované pomocí prostředí PowerShell najdete v tématu [konfigurace spravovaných identit pro prostředky Azure na Virtuálním počítači Azure pomocí Powershellu](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Vytvoření nové služby key vault

Chcete-li vytvořit nový trezor klíčů pomocí Powershellu, zavolejte [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Trezor klíčů, který použijete k uložení klíče spravované zákazníkem pro šifrování Azure Storage, musí mít dvě nastavení ochrany klíčů povolená, **obnovitelné odstranění** a **proveďte není vyprázdnit**. 

Nezapomeňte nahradit zástupné hodnoty v závorkách vlastními hodnotami. 

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>Konfigurace zásady přístupu trezoru klíčů

V dalším kroku nakonfigurujte zásady přístupu pro trezor klíčů tak, aby účet úložiště má oprávnění k přístupu. V tomto kroku budete pomocí spravované identity, který jste dříve přiřadili k účtu úložiště.

Chcete-li nastavit zásady přístupu pro trezor klíčů, zavolejte [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Mějte na paměti a zástupné hodnoty v závorkách nahraďte vlastními hodnotami pomocí proměnné definované v předchozích příkladech.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Vytvořit nový klíč

Dále vytvořte nový klíč v trezoru klíčů. Chcete-li vytvořit nový klíč, zavolejte [přidat AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Mějte na paměti a zástupné hodnoty v závorkách nahraďte vlastními hodnotami pomocí proměnné definované v předchozích příkladech.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurace šifrování pomocí klíčů spravovaných zákazníkem

Ve výchozím nastavení používá šifrování služby Azure Storage klíčů spravovaných microsoftem. V tomto kroku nakonfigurujte svůj účet úložiště Azure používat klíče spravované zákazníkem a zadejte klíč, který chcete přidružit k účtu úložiště.

Volání [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) aktualizace nastavení šifrování účtu úložiště. Mějte na paměti a zástupné hodnoty v závorkách nahraďte vlastními hodnotami pomocí proměnné definované v předchozích příkladech.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>Aktualizace verze klíče

Když vytvoříte novou verzi klíče, musíte aktualizovat účet úložiště na použití nové verze. Nejprve volat [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) získat nejnovější verzi klíče. Poté zavolejte [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) aktualizace nastavení šifrování účtu úložiště používat novou verzi klíče, jak je znázorněno v předchozí části.

## <a name="next-steps"></a>Další postup

- [Šifrování služby Azure Storage pro neaktivní uložená data](storage-service-encryption.md) 
- [Co je Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?
