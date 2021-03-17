---
title: Konfigurace šifrování pomocí klíčů spravovaných zákazníkem uložených v Azure Key Vault spravovaném HSM (Preview)
titleSuffix: Azure Storage
description: Naučte se konfigurovat Azure Storage šifrování pomocí klíčů spravovaných zákazníkem, které jsou uložené v Azure Key Vault spravované HSM (Preview) pomocí Azure CLI.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/05/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: f9be9272a898ad48f3553d4c5e48952e1fcdde81
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102218634"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault-managed-hsm-preview"></a>Konfigurace šifrování pomocí klíčů spravovaných zákazníkem uložených v Azure Key Vault spravovaném HSM (Preview)

Azure Storage šifruje všechna data v účtu úložiště v klidovém umístění. Ve výchozím nastavení se data šifrují pomocí klíčů spravovaných Microsoftem. Pro další kontrolu nad šifrovacími klíči můžete spravovat vlastní klíče. Klíče spravované zákazníkem musí být uložené ve Azure Key Vault nebo Key Vault spravovaném modelu hardwarového zabezpečení (HSM) (ve verzi Preview). Modul HARDWAROVÉho zabezpečení Azure Key Vault Managed má ověřený modul HARDWAROVÉho zabezpečení FIPS 140-2 úrovně 3.

V tomto článku se dozvíte, jak nakonfigurovat šifrování pomocí klíčů spravovaných zákazníkem, které jsou uložené ve spravovaném modulu HSM pomocí Azure CLI. Informace o tom, jak nakonfigurovat šifrování pomocí klíčů spravovaných zákazníkem, které jsou uložené v trezoru klíčů, najdete v tématu [Konfigurace šifrování pomocí klíčů spravovaných zákazníkem, které jsou uložené v Azure Key Vault](customer-managed-keys-configure-key-vault.md).

> [!IMPORTANT]
>
> Šifrování pomocí klíčů spravovaných zákazníkem, které jsou uložené v Azure Key Vault spravovaný modul HSM, je momentálně ve **verzi Preview**. Přečtěte si další [podmínky použití Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview pro právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti.
>
> Azure Key Vault a Azure Key Vault spravovaný modul HSM podporuje pro konfiguraci stejné rozhraní API a rozhraní pro správu.

## <a name="assign-an-identity-to-the-storage-account"></a>Přiřazení identity k účtu úložiště

Nejdřív k účtu úložiště přiřaďte spravovanou identitu přiřazenou systémem. Pomocí této spravované identity udělíte účtu úložiště oprávnění k přístupu ke spravovanému HSM. Další informace o spravovaných identitách přiřazených systémem najdete v tématu [co jsou spravované identity pro prostředky Azure?](../../active-directory/managed-identities-azure-resources/overview.md).

K přiřazení spravované identity pomocí Azure CLI volejte volání [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update). Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami:

```azurecli
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

## <a name="assign-a-role-to-the-storage-account-for-access-to-the-managed-hsm"></a>Přiřaďte účtu úložiště roli pro přístup ke spravovanému modulu HSM.

Pak přiřaďte spravované identitě **spravované šifrovací služby HSM** , aby měl účet úložiště oprávnění ke spravovanému modulu HSM. Společnost Microsoft doporučuje, abyste při přiřazování role do úrovně jednotlivých klíčů přidělili obor přiřazení, aby byla spravovaná identita udělena nejnižší možná oprávnění.

Pokud chcete vytvořit přiřazení role pro účet úložiště, zavolejte volání [AZ Key trezor role Create](/cli/azure/role/assignment#az_role_assignment_create). Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami.
  
```azurecli
storage_account_principal = $(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)

az keyvault role assignment create \
    --hsm-name <hsm-name> \
    --role "Managed HSM Crypto Service Encryption" \
    --assignee $storage_account_principal \
    --scope /keys/<key-name>
```

## <a name="configure-encryption-with-a-key-in-the-managed-hsm"></a>Konfigurace šifrování pomocí klíče ve spravovaném modulu HSM

Nakonec nakonfigurujte Azure Storage šifrování pomocí klíčů spravovaných zákazníkem tak, aby používaly klíč uložený ve spravovaném HSM. Mezi podporované typy klíčů patří klíče RSA-HSM velikostí 2048, 3072 a 4096. Nainstalujte rozhraní příkazového řádku Azure CLI 2.12.0 nebo novější, abyste mohli nakonfigurovat šifrování tak, aby používalo klíč spravovaný zákazníkem ve spravovaném modulu HSM. Další informace najdete v tématu [instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

Pokud chcete automatickou aktualizaci verze klíče pro klíč spravovaný zákazníkem, vynechejte verzi klíče při konfiguraci šifrování s použitím klíčů spravovaných zákazníkem pro účet úložiště. Chcete-li aktualizovat nastavení šifrování účtu úložiště, použijte příkaz [AZ Storage Account Update](/cli/azure/storage/account#az_storage_account_update) , jak je znázorněno v následujícím příkladu. Zahrňte `--encryption-key-source parameter` a nastavte ho tak, aby se `Microsoft.Keyvault` pro účet povolily klíče spravované zákazníkem. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami.

```azurecli
hsmurl = $(az keyvault show \
    --hsm-name <hsm-name> \
    --query properties.hsmUri \
    --output tsv)

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

Pokud chcete ručně aktualizovat verzi pro klíč spravovaný zákazníkem, zahrňte při konfiguraci šifrování pro účet úložiště klíčovou verzi:

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

Když ručně aktualizujete verzi klíče, budete muset aktualizovat nastavení šifrování účtu úložiště tak, aby používalo novou verzi. Nejdřív dotaz na identifikátor URI trezoru klíčů zavoláním [AZ klíčů show](/cli/azure/keyvault#az-keyvault-show)a pro verzi klíče zavoláním [AZ Key trezor Key list-versionss](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Pak zavolejte [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) a aktualizujte nastavení šifrování účtu úložiště tak, aby používala novou verzi klíče, jak je znázorněno v předchozím příkladu.

## <a name="next-steps"></a>Další kroky

- [Šifrování služby Azure Storage pro neaktivní uložená data](storage-service-encryption.md)
- [Klíče spravované zákazníkem pro šifrování Azure Storage](customer-managed-keys-overview.md)
