---
title: Vytvoření účtu, který podporuje klíče spravované zákazníkem pro tabulky a fronty
titleSuffix: Azure Storage
description: Naučte se, jak vytvořit účet úložiště, který podporuje konfiguraci klíčů spravovaných zákazníkem pro tabulky a fronty. Pomocí Azure CLI nebo šablony Azure Resource Manager vytvořte účet úložiště, který spoléhá na šifrovací klíč účtu pro šifrování Azure Storage. Pak můžete nakonfigurovat klíče spravované zákazníkem pro tento účet.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/10/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 39568d65978592b360512db2e556cfa6b59760b6
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/13/2020
ms.locfileid: "75921210"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>Vytvoření účtu, který podporuje klíče spravované zákazníkem pro tabulky a fronty

Azure Storage šifruje všechna data v účtu úložiště v klidovém umístění. Ve výchozím nastavení používá úložiště fronty a úložiště tabulek klíč, který je vymezen na službu a je spravován společností Microsoft. K šifrování dat front nebo tabulek můžete také použít klíče spravované zákazníkem. Pokud chcete používat klíče spravované zákazníkem s frontami a tabulkami, musíte nejdřív vytvořit účet úložiště, který používá šifrovací klíč, který je vymezen na účet, a ne na službu. Po vytvoření účtu, který používá šifrovací klíč účtu pro data front a tabulek, můžete nakonfigurovat klíče spravované zákazníky pomocí Azure Key Vault pro tento účet úložiště.

Tento článek popisuje, jak vytvořit účet úložiště, který spoléhá na klíč, který je vymezený pro daný účet. Při prvním vytvoření účtu používá společnost Microsoft klíč účtu k šifrování dat v účtu a tento klíč spravuje Microsoft. Následně můžete nakonfigurovat klíče spravované zákazníkem pro účet, abyste mohli využít výhod těchto výhod, včetně možnosti poskytovat vlastní klíče, aktualizovat verzi klíče, otáčet klíče a odvolávat řízení přístupu.

## <a name="about-the-feature"></a>O funkci

Pokud chcete vytvořit účet úložiště, který spoléhá na šifrovací klíč účtu pro frontu a úložiště tabulek, musíte se nejdřív zaregistrovat, aby se tato funkce používala v Azure. V důsledku omezené kapacity si uvědomte, že může trvat několik měsíců, než se schválí žádosti o přístup.

Můžete vytvořit účet úložiště, který spoléhá na šifrovací klíč účtu pro frontu a úložiště tabulek v následujících oblastech:

- Východní USA
- Středojižní USA
- Západní USA 2  

### <a name="register-to-use-the-account-encryption-key"></a>Registrace pro použití šifrovacího klíče účtu

Pokud se chcete zaregistrovat v Azure CLI, zavolejte příkaz [AZ Feature Register](/cli/azure/feature#az-feature-register) .

Registrace pro použití šifrovacího klíče účtu s úložištěm Queue:

```azurecli
az feature register --namespace Microsoft.Storage --name AllowAccountEncryptionKeyForQueues
```

Pokud se chcete zaregistrovat k používání šifrovacího klíče účtu s úložištěm tabulek:

```azurecli
az feature register --namespace Microsoft.Storage --name AllowAccountEncryptionKeyForTables
```

### <a name="check-the-status-of-your-registration"></a>Ověření stavu registrace

Postup kontroly stavu registrace pro úložiště Queue:

```azurecli
az feature show --namespace Microsoft.Storage --name AllowAccountEncryptionKeyForQueues
```

Postup kontroly stavu registrace pro úložiště tabulek:

```azurecli
az feature show --namespace Microsoft.Storage --name AllowAccountEncryptionKeyForTables
```

### <a name="re-register-the-azure-storage-resource-provider"></a>Znovu zaregistrovat poskytovatele prostředků Azure Storage

Po schválení registrace je potřeba znovu zaregistrovat poskytovatele prostředků Azure Storage. Zavolejte do příkazu [AZ Provider Register](/cli/azure/provider#az-provider-register) :

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>Vytvoření účtu, který používá šifrovací klíč účtu

Musíte nakonfigurovat nový účet úložiště, aby používal šifrovací klíč účtu pro fronty a tabulky v době, kdy vytvoříte účet úložiště. Po vytvoření účtu nelze změnit obor šifrovacího klíče.

Účet úložiště musí být typu pro obecné účely v2. Můžete vytvořit účet úložiště a nakonfigurovat ho tak, aby se spoléhal na šifrovací klíč účtu pomocí Azure CLI nebo šablony Azure Resource Manager.

> [!NOTE]
> V případě vytvoření účtu úložiště může být volitelně nakonfigurována pouze fronta a úložiště tabulek pro šifrování dat pomocí šifrovacího klíče účtu. Úložiště objektů BLOB a soubory Azure vždycky používají šifrovací klíč účtu k šifrování dat.

### <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete pomocí Azure CLI vytvořit účet úložiště, který spoléhá na šifrovací klíč účtu, ujistěte se, že máte nainstalovanou verzi Azure CLI 2.0.80 nebo novější. Další informace najdete v tématu [instalace rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli).

Pak vytvořte účet úložiště pro obecné účely v2 voláním příkazu [AZ Storage Account Create](/cli/azure/storage/account#az-storage-account-create) s příslušnými parametry:

- Zahrňte možnost `--encryption-key-type-for-queue` a nastavte její hodnotu na `Account`, aby používala šifrovací klíč účtu k šifrování dat ve frontovém úložišti.
- Zahrňte možnost `--encryption-key-type-for-table` a nastavte její hodnotu na `Account`, aby používala šifrovací klíč účtu k šifrování dat v úložišti tabulek.

Následující příklad ukazuje, jak vytvořit účet úložiště pro obecné účely v2, který je nakonfigurovaný pro LRS a který používá šifrovací klíč účtu k šifrování dat pro frontu i úložiště tabulek. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_LRS \
    --kind StorageV2 \
    --encryption-key-type-for-table Account \
    --encryption-key-type-for-queue Account
```

### <a name="templatetabtemplate"></a>[Šablona](#tab/template)

Následující příklad JSON vytvoří účet úložiště pro obecné účely v2, který je nakonfigurovaný pro LRS a který používá šifrovací klíč účtu k šifrování dat pro frontu i úložiště tabulek. Nezapomeňte nahradit hodnoty zástupných symbolů v lomených závorkách vlastními hodnotami:

```json
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "name": "[parameters('<storage-account>')]",
        "location": "[parameters('<location>')]",
        "dependsOn": [],
        "tags": {},
        "sku": {
            "name": "[parameters('Standard_LRS')]"
        },
        "kind": "[parameters('StorageV2')]",
        "properties": {
            "accessTier": "[parameters('<accessTier>')]",
            "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
            "largeFileSharesState": "[parameters('<largeFileSharesState>')]",
            "encryption": {
                "services": {
                    "queue": {
                        "keyType": "Account"
                    },
                    "table": {
                        "keyType": "Account"
                    }
                },
                "keySource": "Microsoft.Storage"
            }
        }
    }
],
```

---

Po vytvoření účtu, který spoléhá na šifrovací klíč účtu, si v jednom z následujících článků nakonfigurujte klíče spravované zákazníky pomocí Azure Key Vault:

- [Konfigurace klíčů spravovaných zákazníkem pomocí Azure Key Vault pomocí Azure Portal](storage-encryption-keys-portal.md)
- [Konfigurace klíčů spravovaných zákazníkem pomocí Azure Key Vault s využitím PowerShellu](storage-encryption-keys-powershell.md)
- [Konfigurace klíčů spravovaných zákazníkem pomocí Azure Key Vault pomocí Azure CLI](storage-encryption-keys-cli.md)

## <a name="verify-the-account-encryption-key"></a>Ověření šifrovacího klíče účtu

Pokud chcete ověřit, že nový účet úložiště používá šifrovací klíč účtu, zavolejte příkaz Azure CLI [AZ Storage Account](/cli/azure/storage/account#az-storage-account-show) . Tento příkaz vrátí seznam vlastností účtu úložiště a jejich hodnot. Vyhledejte vlastnost `keyType` a ověřte, zda je nastavena na hodnotu `Account`.

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

## <a name="next-steps"></a>Další kroky

- [Azure Storage šifrování dat v klidovém umístění](storage-service-encryption.md) 
- [Co je Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
