---
title: Vytvoření účtu, který podporuje klíče spravované zákazníkem pro tabulky a fronty
titleSuffix: Azure Storage
description: Naučte se, jak vytvořit účet úložiště, který podporuje konfiguraci klíčů spravovaných zákazníkem pro tabulky a fronty. Pomocí Azure CLI nebo šablony Azure Resource Manager vytvořte účet úložiště, který spoléhá na šifrovací klíč účtu pro šifrování Azure Storage. Pak můžete nakonfigurovat klíče spravované zákazníkem pro tento účet.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/05/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 8150375eff98374e21d200d98c04158b07f1c243
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92789688"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>Vytvoření účtu, který podporuje klíče spravované zákazníkem pro tabulky a fronty

Azure Storage šifruje všechna data v účtu úložiště v klidovém umístění. Ve výchozím nastavení používá úložiště fronty a úložiště tabulek klíč, který je vymezen na službu a je spravován společností Microsoft. K šifrování dat front nebo tabulek můžete také použít klíče spravované zákazníkem. Pokud chcete používat klíče spravované zákazníkem s frontami a tabulkami, musíte nejdřív vytvořit účet úložiště, který používá šifrovací klíč, který je vymezen na účet, a ne na službu. Po vytvoření účtu, který používá šifrovací klíč účtu pro data front a tabulek, můžete nakonfigurovat klíče spravované zákazníkem pro tento účet úložiště.

Tento článek popisuje, jak vytvořit účet úložiště, který spoléhá na klíč, který je vymezený pro daný účet. Při prvním vytvoření účtu používá společnost Microsoft klíč účtu k šifrování dat v účtu a tento klíč spravuje Microsoft. Následně můžete nakonfigurovat klíče spravované zákazníkem pro účet, abyste mohli využít výhod těchto výhod, včetně možnosti poskytovat vlastní klíče, aktualizovat verzi klíče, otáčet klíče a odvolávat řízení přístupu.

## <a name="about-the-feature"></a>O funkci

Pokud chcete vytvořit účet úložiště, který spoléhá na šifrovací klíč účtu pro frontu a úložiště tabulek, musíte se nejdřív zaregistrovat, aby se tato funkce používala v Azure. V důsledku omezené kapacity si uvědomte, že může trvat několik měsíců, než se schválí žádosti o přístup.

Můžete vytvořit účet úložiště, který spoléhá na šifrovací klíč účtu pro frontu a úložiště tabulek v následujících oblastech:

- East US
- Středojižní USA
- Západní USA 2  

### <a name="register-to-use-the-account-encryption-key"></a>Registrace pro použití šifrovacího klíče účtu

K registraci pro použití šifrovacího klíče účtu s frontou nebo úložištěm tabulek použijte PowerShell nebo Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud se chcete zaregistrovat v prostředí PowerShell, zavolejte příkaz [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) .

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud se chcete zaregistrovat v Azure CLI, zavolejte příkaz [AZ Feature Register](/cli/azure/feature#az-feature-register) .

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[Šablona](#tab/template)

–

---

### <a name="check-the-status-of-your-registration"></a>Ověření stavu registrace

Pokud chcete zjistit stav registrace pro frontu nebo úložiště tabulky, použijte PowerShell nebo Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud chcete zjistit stav vaší registrace pomocí PowerShellu, zavolejte příkaz [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) .

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete zjistit stav registrace pomocí Azure CLI, zavolejte příkaz [AZ Feature](/cli/azure/feature#az-feature-show) .

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[Šablona](#tab/template)

–

---

### <a name="re-register-the-azure-storage-resource-provider"></a>Znovu zaregistrovat poskytovatele prostředků Azure Storage

Po schválení registrace je potřeba znovu zaregistrovat poskytovatele prostředků Azure Storage. K opětovné registraci poskytovatele prostředků použijte PowerShell nebo Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Chcete-li znovu zaregistrovat poskytovatele prostředků v prostředí PowerShell, zavolejte příkaz [Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) .

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete poskytovatele prostředků v Azure CLI znovu zaregistrovat, zavolejte příkaz [AZ Provider Register](/cli/azure/provider#az-provider-register) .

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[Šablona](#tab/template)

–

---

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>Vytvoření účtu, který používá šifrovací klíč účtu

Musíte nakonfigurovat nový účet úložiště, aby používal šifrovací klíč účtu pro fronty a tabulky v době, kdy vytvoříte účet úložiště. Po vytvoření účtu nelze změnit obor šifrovacího klíče.

Účet úložiště musí být typu pro obecné účely v2. Můžete vytvořit účet úložiště a nakonfigurovat ho tak, aby se spoléhal na šifrovací klíč účtu pomocí Azure CLI nebo šablony Azure Resource Manager.

> [!NOTE]
> V případě vytvoření účtu úložiště může být volitelně nakonfigurována pouze fronta a úložiště tabulek pro šifrování dat pomocí šifrovacího klíče účtu. Úložiště objektů BLOB a soubory Azure vždycky používají šifrovací klíč účtu k šifrování dat.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud chcete pomocí PowerShellu vytvořit účet úložiště, který spoléhá na šifrovací klíč účtu, ujistěte se, že máte nainstalovaný modul Azure PowerShell verze 3.4.0 nebo novější. Další informace najdete v tématu [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps).

Pak vytvořte účet úložiště pro obecné účely v2 voláním příkazu [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) s příslušnými parametry:

- Zahrňte `-EncryptionKeyTypeForQueue` možnost a nastavte ji na hodnotu pro `Account` použití šifrovacího klíče účtu k šifrování dat v úložišti front.
- Zahrňte `-EncryptionKeyTypeForTable` možnost a nastavte ji na hodnotu pro `Account` použití šifrovacího klíče účtu k šifrování dat v úložišti tabulek.

Následující příklad ukazuje, jak vytvořit účet úložiště pro obecné účely v2, který je nakonfigurovaný pro geograficky redundantní úložiště s přístupem pro čtení (RA-GRS) a který používá šifrovací klíč účtu k šifrování dat pro frontu i úložiště tabulek. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -EncryptionKeyTypeForTable Account `
    -EncryptionKeyTypeForQueue Account
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete pomocí Azure CLI vytvořit účet úložiště, který spoléhá na šifrovací klíč účtu, ujistěte se, že máte nainstalovanou verzi Azure CLI 2.0.80 nebo novější. Další informace najdete v tématu [instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

Pak vytvořte účet úložiště pro obecné účely v2 voláním příkazu [AZ Storage Account Create](/cli/azure/storage/account#az-storage-account-create) s příslušnými parametry:

- Zahrňte `--encryption-key-type-for-queue` možnost a nastavte ji na hodnotu pro `Account` použití šifrovacího klíče účtu k šifrování dat v úložišti front.
- Zahrňte `--encryption-key-type-for-table` možnost a nastavte ji na hodnotu pro `Account` použití šifrovacího klíče účtu k šifrování dat v úložišti tabulek.

Následující příklad ukazuje, jak vytvořit účet úložiště pro obecné účely v2, který je nakonfigurovaný pro geograficky redundantní úložiště s přístupem pro čtení (RA-GRS) a který používá šifrovací klíč účtu k šifrování dat pro frontu i úložiště tabulek. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --encryption-key-type-for-table Account \
    --encryption-key-type-for-queue Account
```

# <a name="template"></a>[Šablona](#tab/template)

Následující příklad JSON vytvoří účet úložiště pro obecné účely v2, který je nakonfigurovaný pro geograficky redundantní úložiště s přístupem pro čtení (RA-GRS) a který používá šifrovací klíč účtu k šifrování dat pro frontu i úložiště tabulek. Nezapomeňte nahradit hodnoty zástupných symbolů v lomených závorkách vlastními hodnotami:

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
            "name": "[parameters('Standard_RAGRS')]"
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

Po vytvoření účtu, který spoléhá na šifrovací klíč účtu, můžete nakonfigurovat klíče spravované zákazníkem, které jsou uložené v Azure Key Vault nebo v Key Vault Managed hardware Security model (HSM) (Preview). Informace o tom, jak ukládat klíče spravované zákazníky do trezoru klíčů, najdete v tématu [Konfigurace šifrování pomocí klíčů spravovaných zákazníkem uložených v Azure Key Vault](customer-managed-keys-configure-key-vault.md). Informace o tom, jak ukládat klíče spravované zákazníkem ve spravovaném modulu HSM, najdete v tématu [Konfigurace šifrování pomocí klíčů spravovaných zákazníkem, které jsou uložené v Azure Key Vault spravované HSM (Preview)](customer-managed-keys-configure-key-vault-hsm.md).

## <a name="verify-the-account-encryption-key"></a>Ověření šifrovacího klíče účtu

Pokud chcete ověřit, že služba v účtu úložiště používá šifrovací klíč účtu, zavolejte příkaz Azure CLI [AZ Storage Account](/cli/azure/storage/account#az-storage-account-show) . Tento příkaz vrátí sadu vlastností účtu úložiště a jejich hodnoty. Vyhledejte `keyType` pole pro každou službu ve vlastnosti šifrování a ověřte, jestli je nastavená na `Account` .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud chcete ověřit, že služba v účtu úložiště používá šifrovací klíč účtu, zavolejte příkaz [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) . Tento příkaz vrátí sadu vlastností účtu úložiště a jejich hodnoty. Vyhledejte `KeyType` pole pro každou službu v rámci `Encryption` vlastnosti a ověřte, zda je nastavena na hodnotu `Account` .

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.Services.Queue
$account.Encryption.Services.Table
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete ověřit, že služba v účtu úložiště používá šifrovací klíč účtu, zavolejte příkaz [AZ Storage Account show](/cli/azure/storage/account#az-storage-account-show) . Tento příkaz vrátí sadu vlastností účtu úložiště a jejich hodnoty. Vyhledejte `keyType` pole pro každou službu ve vlastnosti šifrování a ověřte, jestli je nastavená na `Account` .

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Šablona](#tab/template)

–

---

## <a name="next-steps"></a>Další kroky

- [Šifrování služby Azure Storage pro neaktivní uložená data](storage-service-encryption.md)
- [Klíče spravované zákazníkem pro šifrování Azure Storage](customer-managed-keys-overview.md)
- [Co je Azure Key Vault](../../key-vault/general/overview.md)?