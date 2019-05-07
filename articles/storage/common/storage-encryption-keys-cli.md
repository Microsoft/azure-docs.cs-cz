---
title: Konfigurovat klíče spravované zákazníkem pro šifrování Azure Storage z příkazového řádku Azure
description: Zjistěte, jak nakonfigurovat spravované zákazníkem klíče pro šifrování Azure Storage pomocí Azure CLI. Klíče spravované zákazníkem umožňují vytvořit, otáčení, zakázat a odvolat přístup k ovládacím prvkům.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ca2cfc9369fd6fb001b2a2dc401c33e5ddfd38c8
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65142911"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-azure-cli"></a>Konfigurovat klíče spravované zákazníkem pro šifrování Azure Storage z příkazového řádku Azure

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Tento článek ukazuje, jak konfigurovat službu key vault pomocí klíčů spravovaných zákazníkem pomocí Azure CLI.

## <a name="assign-an-identity-to-the-storage-account"></a>Přiřaďte identitu do účtu úložiště

Pokud chcete povolit klíče spravované zákazníkem pro váš účet úložiště, mu nejdřív přiřadíte systém přiřadil spravovanou identitu do účtu úložiště. Tuto spravovanou identitu budete používat k udělení oprávnění účtu úložiště pro přístup k trezoru klíčů.

Chcete-li přiřadit spravovanou identitu pomocí Azure CLI, zavolejte [aktualizace účtu úložiště az](/cli/azure/storage/account#az-storage-account-update). Nezapomeňte nahradit zástupné hodnoty v závorkách vlastními hodnotami.

```azurecli-interactive
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Další informace o konfiguraci systém přiřadil spravovaných identit pomocí Azure CLI najdete v tématu [konfigurace spravovaných identit pro prostředky Azure na Virtuálním počítači Azure pomocí Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Vytvoření nové služby key vault

Trezor klíčů, který použijete k uložení klíče spravované zákazníkem pro šifrování Azure Storage, musí mít dvě nastavení ochrany klíčů povolená, **obnovitelné odstranění** a **proveďte není vyprázdnit**. Chcete-li vytvořit nový trezor klíčů pomocí Powershellu nebo rozhraní příkazového řádku Azure povolena tato nastavení, spusťte následující příkazy. Nezapomeňte nahradit zástupné hodnoty v závorkách vlastními hodnotami. 

Chcete-li vytvořit nový trezor klíčů pomocí Azure CLI, zavolejte [az keyvault vytvořit](/cli/azure/keyvault#az-keyvault-create). Nezapomeňte nahradit zástupné hodnoty v závorkách vlastními hodnotami.

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

## <a name="configure-the-key-vault-access-policy"></a>Konfigurace zásady přístupu trezoru klíčů

V dalším kroku nakonfigurujte zásady přístupu pro trezor klíčů tak, aby účet úložiště má oprávnění k přístupu. V tomto kroku budete pomocí spravované identity, který jste dříve přiřadili k účtu úložiště.

Chcete-li nastavit zásady přístupu pro trezor klíčů, zavolejte [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy). Nezapomeňte nahradit zástupné hodnoty v závorkách vlastními hodnotami.

```azurecli-interactive
storage_account_principal=$(az storage account show \
    -name <storage-account> \
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

V dalším kroku vytvoření klíče v trezoru klíčů. Chcete-li vytvořit klíč, zavolejte [az keyvault key vytvořit](/cli/azure/keyvault/key#az-keyvault-key-create). Nezapomeňte nahradit zástupné hodnoty v závorkách vlastními hodnotami.

```azurecli-interactive
az keyvault key create
    --name <key> \
    --vault-name <key-vault>
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurace šifrování pomocí klíčů spravovaných zákazníkem

Ve výchozím nastavení používá šifrování služby Azure Storage klíčů spravovaných microsoftem. Konfigurace účtu služby Azure Storage pro klíče spravované zákazníkem a zadejte klíč, který chcete přidružit k účtu úložiště.

Chcete-li aktualizovat nastavení šifrování účtu úložiště, zavolejte [aktualizace účtu úložiště az](/cli/azure/storage/account#az-storage-account-update). Tento příklad také dotazy pro identifikátor URI služby key vault a verzi klíče, obě hodnoty, které jsou potřeba k přidružení klíč účtu úložiště. Nezapomeňte nahradit zástupné hodnoty v závorkách vlastními hodnotami.

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

Když vytvoříte novou verzi klíče, musíte aktualizovat účet úložiště na použití nové verze. Nejprve dotazování pro službu key vault URI voláním [az keyvault show](/cli/azure/keyvault#az-keyvault-show)a pro verzi klíče voláním [az keyvault key seznam verzí](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Poté zavolejte [aktualizace účtu úložiště az](/cli/azure/storage/account#az-storage-account-update) aktualizace nastavení šifrování účtu úložiště používat novou verzi klíče, jak je znázorněno v předchozí části.

## <a name="next-steps"></a>Další postup

- [Šifrování služby Azure Storage pro neaktivní uložená data](storage-service-encryption.md) 
- [Co je Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?
