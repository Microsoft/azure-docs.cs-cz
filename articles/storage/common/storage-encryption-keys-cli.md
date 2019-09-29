---
title: Konfigurace klíčů spravovaných zákazníkem pro Azure Storage šifrování z Azure CLI
description: Naučte se používat rozhraní příkazového řádku Azure ke konfiguraci klíčů spravovaných zákazníkem pro Azure Storage šifrování. Klíče spravované zákazníkem umožňují vytvářet, otáčet, zakazovat a odvolávat řízení přístupu.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 036322e73470ad4aa25de03e95c506e9f04496d8
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2019
ms.locfileid: "71670984"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-azure-cli"></a>Konfigurace klíčů spravovaných zákazníkem pro Azure Storage šifrování z Azure CLI

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

V tomto článku se dozvíte, jak nakonfigurovat Trezor klíčů pomocí klíčů spravovaných zákazníkem pomocí Azure CLI.

> [!IMPORTANT]
> Použití klíčů spravovaných zákazníkem se šifrováním Azure Storage vyžaduje, aby Trezor klíčů měl nakonfigurované dvě požadované vlastnosti, **obnovitelné odstranění** a **Nemazat**. Tyto vlastnosti jsou ve výchozím nastavení povolené, když v Azure Portal vytvoříte nový trezor klíčů. Pokud ale potřebujete tyto vlastnosti v existujícím trezoru klíčů povolit, musíte použít buď PowerShell, nebo rozhraní příkazového řádku Azure CLI.
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
