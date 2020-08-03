---
title: Použití rozhraní příkazového řádku Azure ke konfiguraci klíčů spravovaných zákazníkem
titleSuffix: Azure Storage
description: Naučte se používat rozhraní příkazového řádku Azure ke konfiguraci klíčů spravovaných zákazníkem Azure Key Vault pro Azure Storage šifrování.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/13/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 351fe5acd8d607b5b60817c235161ac09e530e99
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2020
ms.locfileid: "87495008"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-azure-cli"></a>Konfigurace klíčů spravovaných zákazníkem pomocí Azure Key Vault pomocí Azure CLI

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

V tomto článku se dozvíte, jak nakonfigurovat Azure Key Vault s použitím klíčů spravovaných zákazníkem pomocí Azure CLI. Informace o tom, jak vytvořit Trezor klíčů pomocí Azure CLI, najdete v tématu [rychlý Start: nastavení a načtení tajného klíče z Azure Key Vault pomocí Azure CLI](../../key-vault/secrets/quick-create-cli.md).

## <a name="assign-an-identity-to-the-storage-account"></a>Přiřazení identity k účtu úložiště

Pokud chcete pro svůj účet úložiště povolit klíče spravované zákazníkem, nejdřív přiřaďte k účtu úložiště spravovanou identitu přiřazenou systémem. Pomocí této spravované identity udělíte účtu úložiště oprávnění k přístupu k trezoru klíčů.

K přiřazení spravované identity pomocí Azure CLI volejte volání [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update). Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami.

```azurecli-interactive
az login
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Další informace o konfiguraci spravovaných identit přiřazených systémem pomocí Azure CLI najdete v tématu [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Vytvořit nový trezor klíčů

Trezor klíčů, který použijete k uložení klíčů spravovaných zákazníkem pro Azure Storage šifrování, musí mít povolené dvě nastavení ochrany klíčů, **obnovitelné odstranění** a **nemazatelné**. Pokud chcete vytvořit nový trezor klíčů pomocí PowerShellu nebo Azure CLI s povoleným nastavením, spusťte následující příkazy. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami.

Chcete-li vytvořit nový trezor klíčů pomocí rozhraní příkazového řádku Azure, zavolejte volání [AZ datatrezor Create](/cli/azure/keyvault#az-keyvault-create). Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami.

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

Informace o tom, jak povolit **obnovitelné odstranění** a **Nemazat** existující Trezor klíčů pomocí Azure CLI, najdete v částech s názvem **Povolení obnovitelného odstranění** a **Povolení ochrany vyprázdnit** v tématu [Použití obnovitelného odstranění s](../../key-vault/general/soft-delete-cli.md)rozhraním příkazového řádku.

## <a name="configure-the-key-vault-access-policy"></a>Konfigurace zásad přístupu trezoru klíčů

Dále nakonfigurujte zásady přístupu pro Trezor klíčů, aby měl účet úložiště oprávnění k přístupu. V tomto kroku použijete spravovanou identitu, kterou jste dříve přiřadili k účtu úložiště.

Pokud chcete nastavit zásady přístupu pro Trezor klíčů, volejte volání [AZ webtrezor set-Policy](/cli/azure/keyvault#az-keyvault-set-policy). Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami.

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
    --key-permissions get unwrapKey wrapKey
```

## <a name="create-a-new-key"></a>Vytvořit nový klíč

V dalším kroku vytvořte klíč v trezoru klíčů. Chcete-li vytvořit klíč, zavolejte volání [AZ klíčů trezor Key Create](/cli/azure/keyvault/key#az-keyvault-key-create). Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami.

```azurecli-interactive
az keyvault key create \
    --name <key> \
    --vault-name <key-vault>
```

Šifrování úložiště Azure podporuje klíče RSA a RSA-HSM velikostí 2048, 3072 a 4096. Další informace o klíčích najdete v tématu **Key Vault Keys** v tématu [informace o Azure Key Vaultch klíčích, tajných klíčích a certifikátech](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurace šifrování pomocí klíčů spravovaných zákazníkem

Ve výchozím nastavení používá Azure Storage šifrování klíče spravované společností Microsoft. V tomto kroku nakonfigurujte Azure Storage účet tak, aby používal klíče spravované zákazníkem s Azure Key Vault, a pak zadejte klíč, který chcete přidružit k účtu úložiště.

Když konfigurujete šifrování s použitím klíčů spravovaných zákazníkem, můžete při změně verze v přidruženém trezoru klíčů automaticky otočit klíč používaný k šifrování. Alternativně můžete explicitně zadat verzi klíče, která se má použít pro šifrování, dokud se verze klíče ručně neaktualizuje.

### <a name="configure-encryption-for-automatic-rotation-of-customer-managed-keys"></a>Konfigurace šifrování pro automatické otočení klíčů spravovaných zákazníkem

Pokud chcete nakonfigurovat šifrování pro automatické rotaci klíčů spravovaných zákazníkem, nainstalujte [Azure CLI verze 2.4.0](/cli/azure/release-notes-azure-cli#april-21-2020) nebo novější. Další informace najdete v tématu [instalace rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Pokud chcete automaticky otáčet klíče spravované zákazníkem, vynechejte verzi klíče při konfiguraci klíčů spravovaných zákazníkem pro účet úložiště. Chcete-li aktualizovat nastavení šifrování účtu úložiště, použijte příkaz [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) , jak je znázorněno v následujícím příkladu. Zahrňte `--encryption-key-source` parametr a nastavte ho tak, aby `Microsoft.Keyvault` umožňoval pro tento účet klíče spravované zákazníkem. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

### <a name="configure-encryption-for-manual-rotation-of-key-versions"></a>Konfigurace šifrování pro ruční otočení klíčových verzí

Pokud chcete explicitně zadat verzi klíče, která se má použít pro šifrování, poskytněte klíčovou verzi při konfiguraci šifrování pomocí klíčů spravovaných zákazníkem pro účet úložiště. Chcete-li aktualizovat nastavení šifrování účtu úložiště, použijte příkaz [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) , jak je znázorněno v následujícím příkladu. Zahrňte `--encryption-key-source` parametr a nastavte ho tak, aby `Microsoft.Keyvault` umožňoval pro tento účet klíče spravované zákazníkem. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami.

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

Při ručním otočení této verze klíče budete muset aktualizovat nastavení šifrování účtu úložiště, aby používala novou verzi. Nejdřív dotaz na identifikátor URI trezoru klíčů zavoláním [AZ klíčů show](/cli/azure/keyvault#az-keyvault-show)a pro verzi klíče zavoláním [AZ Key trezor Key list-versionss](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Pak zavolejte [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) a aktualizujte nastavení šifrování účtu úložiště tak, aby používala novou verzi klíče, jak je znázorněno v předchozím příkladu.

## <a name="use-a-different-key"></a>Použít jiný klíč

Pokud chcete změnit klíč, který se používá pro Azure Storage šifrování, zavolejte [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) , jak je znázorněno v části [Konfigurace šifrování pomocí klíčů spravovaných zákazníkem](#configure-encryption-with-customer-managed-keys) , a zadejte nový název a verzi klíče. Pokud je nový klíč v jiném trezoru klíčů, aktualizujte také identifikátor URI trezoru klíčů.

## <a name="revoke-customer-managed-keys"></a>Odvolání klíčů spravovaných zákazníkem

Klíče spravované zákazníkem můžete odvolat odebráním zásad přístupu trezoru klíčů. Pokud chcete odvolat klíč spravovaný zákazníkem, zavolejte příkaz [AZ Key trezor Delete-Policy](/cli/azure/keyvault#az-keyvault-delete-policy) , jak je znázorněno v následujícím příkladu. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami a použít proměnné definované v předchozích příkladech.

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

## <a name="disable-customer-managed-keys"></a>Zakázat klíče spravované zákazníkem

Když zakážete klíče spravované zákazníkem, váš účet úložiště se znovu zašifruje pomocí klíčů spravovaných Microsoftem. Pokud chcete zakázat klíče spravované zákazníkem, zavolejte [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) a nastavte `--encryption-key-source parameter` na `Microsoft.Storage` , jak je znázorněno v následujícím příkladu. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami a použít proměnné definované v předchozích příkladech.

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

## <a name="next-steps"></a>Další kroky

- [Šifrování služby Azure Storage pro neaktivní uložená data](storage-service-encryption.md) 
- [Co je Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
