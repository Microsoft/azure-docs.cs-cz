---
title: Konfigurace klíčů spravovaných zákazníkem pomocí Azure Key Vault pomocí Azure CLI – Azure Storage
description: Naučte se používat rozhraní příkazového řádku Azure ke konfiguraci klíčů spravovaných zákazníkem Azure Key Vault pro Azure Storage šifrování. Klíče spravované zákazníkem umožňují vytvářet, otáčet, zakazovat a odvolávat řízení přístupu.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/20/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: df65267924cfbfdc856b81928c4b6d7029f61184
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665963"
---
# <a name="configure-customer-managed-keys-for-azure-storage-by-using-azure-cli"></a>Konfigurace klíčů spravovaných zákazníkem pro Azure Storage pomocí Azure CLI

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

V tomto článku se dozvíte, jak nakonfigurovat Azure Key Vault s použitím klíčů spravovaných zákazníkem pomocí Azure CLI. Informace o tom, jak vytvořit Trezor klíčů pomocí Azure CLI, najdete v tématu [rychlý Start: nastavení a načtení tajného klíče z Azure Key Vault pomocí Azure CLI](../../key-vault/quick-create-cli.md).

> [!IMPORTANT]
> Použití klíčů spravovaných zákazníkem s šifrováním Azure Storage vyžaduje, aby byly v trezoru klíčů nastaveny dvě vlastnosti, **obnovitelné odstranění** a **nemazatelné**. Tyto vlastnosti nejsou ve výchozím nastavení povolené. Pokud chcete tyto vlastnosti povolit, použijte PowerShell nebo rozhraní příkazového řádku Azure CLI.
> Podporují se jenom klíče RSA a velikost klíče 2048.

## <a name="assign-an-identity-to-the-storage-account"></a>Přiřazení identity k účtu úložiště

Pokud chcete pro svůj účet úložiště povolit klíče spravované zákazníkem, nejdřív přiřaďte k účtu úložiště spravovanou identitu přiřazenou systémem. Pomocí této spravované identity udělíte účtu úložiště oprávnění k přístupu k trezoru klíčů.

K přiřazení spravované identity pomocí Azure CLI volejte volání [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update). Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami.

```azurecli-interactive
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
    --key-permissions get recover unwrapKey wrapKey
```

## <a name="create-a-new-key"></a>Vytvořit nový klíč

V dalším kroku vytvořte klíč v trezoru klíčů. Chcete-li vytvořit klíč, zavolejte volání [AZ klíčů trezor Key Create](/cli/azure/keyvault/key#az-keyvault-key-create). Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami.

```azurecli-interactive
az keyvault key create
    --name <key> \
    --vault-name <key-vault>
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurace šifrování pomocí klíčů spravovaných zákazníkem

Ve výchozím nastavení používá Azure Storage šifrování klíče spravované společností Microsoft. Nakonfigurujte účet Azure Storage pro klíče spravované zákazníkem a zadejte klíč, který chcete přidružit k účtu úložiště.

Pokud chcete aktualizovat nastavení šifrování účtu úložiště, zavolejte [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update). Tento příklad také dotazuje se na identifikátor URI a verzi klíče trezoru klíčů, které hodnoty jsou potřeba k přidružení klíče k účtu úložiště. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [].kid \
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

Když vytváříte novou verzi klíče, budete muset aktualizovat účet úložiště, aby používal novou verzi. Nejdřív dotaz na identifikátor URI trezoru klíčů zavoláním [AZ klíčů show](/cli/azure/keyvault#az-keyvault-show)a pro verzi klíče zavoláním [AZ Key trezor Key list-versionss](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Pak zavolejte [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) a aktualizujte nastavení šifrování účtu úložiště tak, aby používala novou verzi klíče, jak je znázorněno v předchozí části.

## <a name="next-steps"></a>Další kroky

- [Azure Storage šifrování dat v klidovém umístění](storage-service-encryption.md) 
- [Co je Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
