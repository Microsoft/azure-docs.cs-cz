---
title: Konfigurace klíčů spravovaných zákazníky pomocí rozhraní příkazového příkazu Azure
titleSuffix: Azure Storage
description: Zjistěte, jak pomocí rozhraní příkazového příkazového nastavení Azure nakonfigurovat klíče spravované zákazníky pomocí šifrování Azure Key Vault for Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/31/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c20fcdf6a725da9d7b053f0ad98efec6aca88f81
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478208"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-azure-cli"></a>Konfigurace klíčů spravovaných zákazníky pomocí služby Azure Key Vault pomocí rozhraní Příkazového příkazu Azure

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Tento článek ukazuje, jak nakonfigurovat Azure Key Vault s klíči spravované zákazníky pomocí Rozhraní příkazového příkazového příkazu Azure. Informace o tom, jak vytvořit trezor klíčů pomocí příkazového příkazu k řešení Azure, najdete v [tématu Úvodní příručka: Nastavení a načtení tajného klíče z trezoru klíčů Azure pomocí azure CLI](../../key-vault/quick-create-cli.md).

## <a name="assign-an-identity-to-the-storage-account"></a>Přiřazení identity k účtu úložiště

Chcete-li povolit klíče spravované zákazníkem pro váš účet úložiště, nejprve přiřaďte účtu úložiště spravovanou spravanou identitu přiřazenou systémem. Tuto spravovanou identitu použijete k udělení oprávnění účtu úložiště pro přístup k trezoru klíčů.

Chcete-li přiřadit spravovanou identitu pomocí příkazového příkazu k příkazu Azure, zavolejte [aktualizaci účtu úložiště az](/cli/azure/storage/account#az-storage-account-update). Nezapomeňte nahradit zástupné hodnoty v závorkách vlastními hodnotami.

```azurecli-interactive
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Další informace o konfiguraci spravovaných identit přiřazených systémem pomocí rozhraní příkazového příkazu Azure najdete [v tématu Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí rozhraní příkazového příkazu Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Vytvoření nového trezoru klíčů

Trezor klíčů, který používáte k ukládání klíčů spravovaných zákazníky pro šifrování Azure Storage, musí mít povolená dvě nastavení ochrany klíčů, **obnovitelné odstranění** a **neodstraňovat**. Chcete-li vytvořit nový trezor klíčů pomocí Prostředí PowerShell nebo Azure CLI s povoleným nastavením, proveďte následující příkazy. Nezapomeňte nahradit zástupné hodnoty v závorkách vlastními hodnotami.

Chcete-li vytvořit nový trezor klíčů pomocí příkazového příkazu k onomu Azure, zavolejte [az keyvault create](/cli/azure/keyvault#az-keyvault-create). Nezapomeňte nahradit zástupné hodnoty v závorkách vlastními hodnotami.

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

Informace o povolení **funkce Obnovitelné odstranění** a **nečistit** v existujícím trezoru klíčů pomocí příkazového příkazu k použití příkazu Konstatování Azure najdete v části **povolení obnovitelného odstranění** a povolení ochrany proti **vymazání** v [tématu Jak používat obnovitelné odstranění pomocí příkazového příkazu příkazového příkazu](../../key-vault/key-vault-soft-delete-cli.md).

## <a name="configure-the-key-vault-access-policy"></a>Konfigurace zásad přístupu k trezoru klíčů

Dále nakonfigurujte zásady přístupu pro trezor klíčů tak, aby účet úložiště měl oprávnění k přístupu k němu. V tomto kroku použijete spravovanou identitu, kterou jste dříve přiřadili k účtu úložiště.

Chcete-li nastavit zásady přístupu pro trezor klíčů, zavolejte [zásadu sady klíčů az](/cli/azure/keyvault#az-keyvault-set-policy). Nezapomeňte nahradit zástupné hodnoty v závorkách vlastními hodnotami.

```azurecli-interactive
storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)
az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group>
    --object-id $storage_account_principal \
    --key-permissions get recover unwrapKey wrapKey
```

## <a name="create-a-new-key"></a>Vytvoření nového klíče

Dále vytvořte klíč v trezoru klíčů. Chcete-li vytvořit klíč, zavolejte [az keyvault klíč vytvořit](/cli/azure/keyvault/key#az-keyvault-key-create). Nezapomeňte nahradit zástupné hodnoty v závorkách vlastními hodnotami.

```azurecli-interactive
az keyvault key create
    --name <key> \
    --vault-name <key-vault>
```

Šifrováním azure storage jsou podporované jenom 2048bitové klíče RSA a RSA-HSM. Další informace o klíčích najdete v **tématu Klíče trezoru klíčů** v [tématu O klíčích, tajných klíčích a certifikátech trezoru klíčů Azure](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurace šifrování pomocí klíčů spravovaných zákazníkem

Ve výchozím nastavení používá šifrování Azure Storage klíče spravované microsoftem. Nakonfigurujte svůj účet Úložiště Azure pro klíče spravované zákazníkem a zadejte klíč, který chcete přidružit k účtu úložiště.

Chcete-li aktualizovat nastavení šifrování účtu úložiště, zavolejte [aktualizaci účtu úložiště az](/cli/azure/storage/account#az-storage-account-update), jak je znázorněno v následujícím příkladu. Zahrňte `--encryption-key-source` parametr `Microsoft.Keyvault` a nastavte jej tak, aby umožňoval klíče spravované zákazníkem pro účet úložiště. Příklad se také dotazuje na identifikátor URI trezoru klíčů a nejnovější verzi klíče, které jsou potřebné k přidružení klíče k účtu úložiště. Nezapomeňte nahradit zástupné hodnoty v závorkách vlastními hodnotami.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [-1].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

## <a name="update-the-key-version"></a>Aktualizace verze klíče

Když vytvoříte novou verzi klíče, budete muset aktualizovat účet úložiště, aby se používala nová verze. Nejprve se dotazujte na identifikátor URI trezoru klíčů voláním [az keyvault show](/cli/azure/keyvault#az-keyvault-show)a pro klíčovou verzi voláním [verzí seznamu klíčů az keyvault](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Potom zavolejte [aktualizaci účtu úložiště a](/cli/azure/storage/account#az-storage-account-update) aktualizujte nastavení šifrování účtu úložiště tak, aby používala novou verzi klíče, jak je znázorněno v předchozí části.

## <a name="use-a-different-key"></a>Použití jiného klíče

Chcete-li změnit klíč používaný pro šifrování azure storage, zavolejte [aktualizaci účtu úložiště az,](/cli/azure/storage/account#az-storage-account-update) jak je znázorněno v [tématu Konfigurace šifrování pomocí klíčů spravovaných zákazníkem,](#configure-encryption-with-customer-managed-keys) a zadejte nový název a verzi klíče. Pokud je nový klíč v jiném trezoru klíčů, aktualizujte také identifikátor URI trezoru klíčů.

## <a name="revoke-customer-managed-keys"></a>Odvolat klíče spravované zákazníkem

Pokud se domníváte, že klíč mohl být ohrožen, můžete odvolat klíče spravované zákazníkem odebráním zásad přístupu k trezoru klíčů. Chcete-li odvolat klíč spravovaný zákazníkem, zavolejte příkaz [zásad odstranění trezoru az,](/cli/azure/keyvault#az-keyvault-delete-policy) jak je znázorněno v následujícím příkladu. Nezapomeňte nahradit zástupné hodnoty v závorkách vlastními hodnotami a použít proměnné definované v předchozích příkladech.

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

## <a name="disable-customer-managed-keys"></a>Zakázání klíčů spravovaných zákazníkem

Když zakážete klíče spravované zákazníky, váš účet úložiště se opět zašifruje pomocí klíčů spravovaných společností Microsoft. Chcete-li zakázat klíče spravované zákazníkem, zavolejte `Microsoft.Storage` [aktualizaci účtu úložiště a](/cli/azure/storage/account#az-storage-account-update) nastavte `--encryption-key-source parameter` na , jak je znázorněno v následujícím příkladu. Nezapomeňte nahradit zástupné hodnoty v závorkách vlastními hodnotami a použít proměnné definované v předchozích příkladech.

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

## <a name="next-steps"></a>Další kroky

- [Šifrování Azure Storage pro data v klidovém stavu](storage-service-encryption.md) 
- [Co je Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
