---
title: Vytvoření účtu, který podporuje klíče spravované zákazníkem pro tabulky a fronty
titleSuffix: Azure Storage
description: Zjistěte, jak vytvořit účet úložiště, který podporuje konfiguraci klíčů spravovaných zákazníkem pro tabulky a fronty. Pomocí azure cli nebo šablony Azure Resource Manager vytvořit účet úložiště, který závisí na šifrovací klíč účtu pro šifrování Azure Storage. Potom můžete pro účet nakonfigurovat klíče spravované zákazníkem.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/05/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 09558a8d1e4e2dc68cefd2c870f54e008d10b97b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083566"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>Vytvoření účtu, který podporuje klíče spravované zákazníkem pro tabulky a fronty

Azure Storage šifruje všechna data v účtu úložiště v klidovém stavu. Ve výchozím nastavení používá úložiště front a úložiště tabulek klíč, který je vymezen pro službu a spravován společností Microsoft. Můžete se také rozhodnout použít klíče spravované zákazníkem k šifrování dat fronty nebo tabulky. Chcete-li používat klíče spravované zákazníkem s frontami a tabulkami, musíte nejprve vytvořit účet úložiště, který používá šifrovací klíč, který je vymezen pro účet, nikoli pro službu. Po vytvoření účtu, který používá šifrovací klíč účtu pro data fronty a tabulky, můžete pro tento účet úložiště nakonfigurovat klíče spravované zákazníkem pomocí trezoru klíčů Azure Key Vault.

Tento článek popisuje, jak vytvořit účet úložiště, který závisí na klíč, který je vymezen pro účet. Při prvním vytvoření účtu používá Společnost Microsoft klíč účtu k šifrování dat v účtu a Microsoft ho spravuje. Následně můžete nakonfigurovat klíče spravované zákazníkem pro účet využít těchto výhod, včetně možnosti poskytnout vlastní klíče, aktualizovat verzi klíče, otočit klíče a odvolat ovládací prvky přístupu.

## <a name="about-the-feature"></a>O funkci

Chcete-li vytvořit účet úložiště, který závisí na šifrovací klíč účtu pro fronty a table storage, musíte se nejprve zaregistrovat k použití této funkce s Azure. Vzhledem k omezené kapacitě, uvědomte si, že to může trvat několik měsíců, než žádosti o přístup jsou schváleny.

Můžete vytvořit účet úložiště, který závisí na šifrovací klíč účtu pro fronty a tabulka úložiště v následujících oblastech:

- USA – východ
- USA – středojih
- USA – západ 2  

### <a name="register-to-use-the-account-encryption-key"></a>Zaregistrujte se k použití šifrovacího klíče účtu

Pokud se chcete zaregistrovat k použití šifrovacího klíče účtu s úložištěm fronty nebo tabulky, použijte PowerShell nebo Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Chcete-li se zaregistrovat pomocí prostředí PowerShell, zavolejte příkaz [Get-AzProviderFeature.](/powershell/module/az.resources/get-azproviderfeature)

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li se zaregistrovat pomocí příkazového příkazu Azure CLI, zavolejte příkaz [registru funkcí az.](/cli/azure/feature#az-feature-register)

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[Šablony](#tab/template)

Není dostupné.

---

### <a name="check-the-status-of-your-registration"></a>Zkontrolujte stav vaší registrace

Chcete-li zkontrolovat stav registrace pro úložiště fronty nebo tabulky, použijte PowerShell nebo Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Chcete-li zkontrolovat stav registrace pomocí prostředí PowerShell, zavolejte příkaz [Get-AzProviderFeature.](/powershell/module/az.resources/get-azproviderfeature)

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li zkontrolovat stav registrace pomocí příkazového řádku Azure, zavolejte příkaz [funkce az.](/cli/azure/feature#az-feature-show)

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[Šablony](#tab/template)

Není dostupné.

---

### <a name="re-register-the-azure-storage-resource-provider"></a>Opětovná registrace zprostředkovatele prostředků úložiště Azure

Po schválení registrace je nutné znovu zaregistrovat poskytovatele prostředků úložiště Azure. Pomocí PowerShellu nebo Azure CLI znovu zaregistrovat poskytovatele prostředků.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Chcete-li znovu zaregistrovat zprostředkovatele prostředků pomocí prostředí PowerShell, zavolejte příkaz [Register-AzResourceProvider.](/powershell/module/az.resources/register-azresourceprovider)

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li znovu zaregistrovat poskytovatele prostředků pomocí příkazového příkazu k registraci Azure, zavolejte příkaz [registrace zprostředkovatele az.](/cli/azure/provider#az-provider-register)

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[Šablony](#tab/template)

Není dostupné.

---

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>Vytvoření účtu, který používá šifrovací klíč účtu

Je nutné nakonfigurovat nový účet úložiště pro použití šifrovacího klíče účtu pro fronty a tabulky při vytváření účtu úložiště. Obor šifrovacího klíče nelze po vytvoření účtu změnit.

Účet úložiště musí být typu pro obecné účely v2. Účet úložiště můžete vytvořit a nakonfigurovat tak, aby se spoléhal na šifrovací klíč účtu, a to buď pomocí azure cli nebo šablony Azure Resource Manager.

> [!NOTE]
> Při vytváření účtu úložiště lze volitelně nakonfigurovat pouze úložiště fronty a tabulky, aby šifrovalo data pomocí šifrovacího klíče účtu. Úložiště objektů blob a soubory Azure vždy používají šifrovací klíč účtu k šifrování dat.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud chcete pomocí PowerShellu vytvořit účet úložiště, který závisí na šifrovacím klíči účtu, ujistěte se, že jste nainstalovali modul Azure PowerShell verze 3.4.0 nebo novější. Další informace najdete [v tématu Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps).

Dále vytvořte účet úložiště pro obecné účely v2 voláním příkazu [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) s příslušnými parametry:

- Zahrňte `-EncryptionKeyTypeForQueue` možnost a `Account` nastavte její hodnotu tak, aby používala šifrovací klíč účtu k šifrování dat v úložišti fronty.
- Zahrňte `-EncryptionKeyTypeForTable` možnost a `Account` nastavte její hodnotu tak, aby používala šifrovací klíč účtu k šifrování dat v úložišti tabulky.

Následující příklad ukazuje, jak vytvořit účet úložiště pro obecné účely v2, který je nakonfigurován pro geograficky redundantní úložiště pro čtení (RA-GRS) a který používá šifrovací klíč účtu k šifrování dat pro úložiště fronty i tabulky. Nezapomeňte nahradit zástupné hodnoty v závorkách vlastními hodnotami:

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

Pokud chcete pomocí Azure CLI vytvořit účet úložiště, který závisí na šifrovacíklíč účtu, ujistěte se, že jste nainstalovali Azure CLI verze 2.0.80 nebo novější. Další informace najdete [v tématu Instalace příkazového příkazového příkazu k webu Azure](/cli/azure/install-azure-cli).

Dále vytvořte účet úložiště pro obecné účely v2 voláním příkazu [az storage create](/cli/azure/storage/account#az-storage-account-create) s příslušnými parametry:

- Zahrňte `--encryption-key-type-for-queue` možnost a `Account` nastavte její hodnotu tak, aby používala šifrovací klíč účtu k šifrování dat v úložišti fronty.
- Zahrňte `--encryption-key-type-for-table` možnost a `Account` nastavte její hodnotu tak, aby používala šifrovací klíč účtu k šifrování dat v úložišti tabulky.

Následující příklad ukazuje, jak vytvořit účet úložiště pro obecné účely v2, který je nakonfigurován pro geograficky redundantní úložiště pro čtení (RA-GRS) a který používá šifrovací klíč účtu k šifrování dat pro úložiště fronty i tabulky. Nezapomeňte nahradit zástupné hodnoty v závorkách vlastními hodnotami:

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

# <a name="template"></a>[Šablony](#tab/template)

Následující příklad JSON vytvoří účet úložiště pro obecné účely v2, který je nakonfigurován pro geograficky redundantní úložiště pro čtení (RA-GRS) a který používá šifrovací klíč účtu k šifrování dat pro úložiště fronty i tabulky. Nezapomeňte nahradit zástupné hodnoty v úhlových závorkách vlastními hodnotami:

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

Po vytvoření účtu, který závisí na šifrovací klíč účtu, najdete v jednom z následujících článků pro konfiguraci klíčů spravovaných zákazníky pomocí služby Azure Key Vault:

- [Konfigurace klíčů spravovaných zákazníky pomocí služby Azure Key Vault pomocí portálu Azure](storage-encryption-keys-portal.md)
- [Konfigurace klíčů spravovaných zákazníkem pomocí služby Azure Key Vault pomocí PowerShellu](storage-encryption-keys-powershell.md)
- [Konfigurace klíčů spravovaných zákazníky pomocí služby Azure Key Vault pomocí rozhraní Příkazového příkazu Azure](storage-encryption-keys-cli.md)

## <a name="verify-the-account-encryption-key"></a>Ověření šifrovacího klíče účtu

Chcete-li ověřit, že služba v účtu úložiště používá šifrovací klíč účtu, zavolejte příkaz [účtu az az](/cli/azure/storage/account#az-storage-account-show) Azure CLI. Tento příkaz vrátí sadu vlastností účtu úložiště a jejich hodnoty. Vyhledejte `keyType` pole pro každou službu v rámci vlastnosti `Account`šifrování a ověřte, zda je nastavena na .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Chcete-li ověřit, zda služba v účtu úložiště používá šifrovací klíč účtu, zavolejte příkaz [Get-AzStorageAccount.](/powershell/module/az.storage/get-azstorageaccount) Tento příkaz vrátí sadu vlastností účtu úložiště a jejich hodnoty. Vyhledejte `KeyType` pole pro každou `Encryption` službu v rámci vlastnosti a ověřte, zda je nastavena na `Account`.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.Services.Queue
$account.Encryption.Services.Table
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li ověřit, zda služba v účtu úložiště používá šifrovací klíč účtu, zavolejte příkaz [účtu úložiště az.](/cli/azure/storage/account#az-storage-account-show) Tento příkaz vrátí sadu vlastností účtu úložiště a jejich hodnoty. Vyhledejte `keyType` pole pro každou službu v rámci vlastnosti `Account`šifrování a ověřte, zda je nastavena na .

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Šablony](#tab/template)

Není dostupné.

---

## <a name="next-steps"></a>Další kroky

- [Šifrování Azure Storage pro data v klidovém stavu](storage-service-encryption.md) 
- [Co je Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
