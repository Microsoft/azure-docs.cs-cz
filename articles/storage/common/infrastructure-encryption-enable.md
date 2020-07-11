---
title: Vytvoření účtu úložiště s povoleným šifrováním infrastruktury pro dvojité šifrování dat
titleSuffix: Azure Storage
description: Zákazníci, kteří vyžadují vyšší úroveň záruky, že jejich data jsou zabezpečená, můžou na úrovni infrastruktury Azure Storage povolit taky 256 šifrování AES. Když je povolené šifrování infrastruktury, data v účtu úložiště se zašifrují dvakrát pomocí dvou různých šifrovacích algoritmů a dvou různých klíčů.
services: storage
author: tamram
ms.service: storage
ms.date: 07/08/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: references_regions
ms.openlocfilehash: edeb184af1c1260a456ed3de7064805526629de8
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2020
ms.locfileid: "86225152"
---
# <a name="create-a-storage-account-with-infrastructure-encryption-enabled-for-double-encryption-of-data"></a>Vytvoření účtu úložiště s povoleným šifrováním infrastruktury pro dvojité šifrování dat

Azure Storage automaticky šifruje všechna data v účtu úložiště na úrovni služby pomocí 256 šifrování AES, jedno z nejsilnějších šifrovacích šifr, které jsou kompatibilní se standardem FIPS 140-2. Zákazníci, kteří vyžadují vyšší úroveň záruky, že jejich data jsou zabezpečená, můžou na úrovni infrastruktury Azure Storage povolit taky 256 šifrování AES. Když je povolené šifrování infrastruktury, data v účtu úložiště se &mdash; na úrovni služby šifrují dvakrát a jednou na úrovni infrastruktury &mdash; se dvěma různými šifrovacími algoritmy a dvěma různými klíči. Dvojité šifrování Azure Storage dat chrání před scénářem, kdy může dojít k ohrožení jednoho z šifrovacích algoritmů nebo klíčů. V tomto scénáři bude další vrstva šifrování nadále chránit vaše data.

Šifrování na úrovni služby podporuje použití klíčů spravovaných Microsoftem nebo klíčů spravovaných zákazníkem s Azure Key Vault. Šifrování na úrovni infrastruktury spoléhá na klíče spravované Microsoftem a vždycky používá samostatný klíč. Další informace o správě klíčů pomocí šifrování Azure Storage najdete v tématu [o správě šifrovacích klíčů](storage-service-encryption.md#about-encryption-key-management).

Pro zdvojnásobení šifrování dat musíte nejdřív vytvořit účet úložiště, který je nakonfigurovaný pro šifrování infrastruktury. Tento článek popisuje, jak vytvořit účet úložiště, který umožňuje šifrování infrastruktury.

## <a name="about-the-feature"></a>O funkci

Pokud chcete vytvořit účet úložiště s povoleným šifrováním infrastruktury, musíte se nejdřív zaregistrovat, aby se tato funkce používala v Azure. V důsledku omezené kapacity si uvědomte, že může trvat několik měsíců, než se schválí žádosti o přístup.

Můžete vytvořit účet úložiště s povoleným šifrováním infrastruktury v následujících oblastech:

- East US
- Středojižní USA
- Západní USA 2

### <a name="register-to-use-infrastructure-encryption"></a>Registrace pro použití šifrování infrastruktury

Pokud se chcete zaregistrovat pro použití šifrování infrastruktury s Azure Storage, použijte PowerShell nebo Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud se chcete zaregistrovat v prostředí PowerShell, zavolejte příkaz [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) .

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud se chcete zaregistrovat v Azure CLI, zavolejte příkaz [AZ Feature Register](/cli/azure/feature#az-feature-register) .

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

# <a name="template"></a>[Šablona](#tab/template)

Nelze použít

---

### <a name="check-the-status-of-your-registration"></a>Ověření stavu registrace

Pokud chcete zjistit stav registrace pro šifrování infrastruktury, použijte PowerShell nebo Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud chcete zjistit stav vaší registrace pomocí PowerShellu, zavolejte příkaz [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) .

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete zjistit stav registrace pomocí Azure CLI, zavolejte příkaz [AZ Feature](/cli/azure/feature#az-feature-show) .

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

# <a name="template"></a>[Šablona](#tab/template)

Nelze použít

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

Nelze použít

---

## <a name="create-an-account-with-infrastructure-encryption-enabled"></a>Vytvoření účtu s povoleným šifrováním infrastruktury

Účet úložiště musíte nakonfigurovat tak, aby používal šifrování infrastruktury v době, kdy vytváříte účet. Po vytvoření účtu nelze povolit nebo zakázat šifrování infrastruktury.

Účet úložiště musí být typu pro obecné účely v2. Můžete vytvořit účet úložiště a nakonfigurovat ho tak, aby povoloval šifrování infrastruktury pomocí PowerShellu, rozhraní příkazového řádku Azure nebo šablony Azure Resource Manager.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud chcete pomocí PowerShellu vytvořit účet úložiště s povoleným šifrováním infrastruktury, ujistěte se, že jste nainstalovali [modul příkaz AZ. Storage PowerShell](https://www.powershellgallery.com/packages/Az.Storage)verze 2.2.0 nebo novější. Další informace najdete v tématu [instalace Azure PowerShell](/powershell/azure/install-az-ps).

Pak vytvořte účet úložiště pro obecné účely v2 voláním příkazu [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) . Zahrňte `-RequireInfrastructureEncryption` možnost povolit šifrování infrastruktury.

Následující příklad ukazuje, jak vytvořit účet úložiště pro obecné účely v2, který je nakonfigurovaný pro geograficky redundantní úložiště s přístupem pro čtení (RA-GRS), a má šifrování infrastruktury s povoleným šifrováním dat. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete pomocí Azure CLI vytvořit účet úložiště s povoleným šifrováním infrastruktury, ujistěte se, že máte nainstalovanou verzi Azure CLI 2.8.0 nebo novější. Další informace najdete v tématu [instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

Pak vytvořte účet úložiště pro obecné účely v2 voláním příkazu [AZ Storage Account Create](/cli/azure/storage/account#az-storage-account-create) a zahrňte `--require-infrastructure-encryption option` do povolení šifrování infrastruktury.

Následující příklad ukazuje, jak vytvořit účet úložiště pro obecné účely v2, který je nakonfigurovaný pro geograficky redundantní úložiště s přístupem pro čtení (RA-GRS), a má šifrování infrastruktury s povoleným šifrováním dat. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami:

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --require-infrastructure-encryption
```

# <a name="template"></a>[Šablona](#tab/template)

Následující příklad JSON vytvoří účet úložiště pro obecné účely v2, který je nakonfigurovaný pro geograficky redundantní úložiště s přístupem pro čtení (RA-GRS), a má šifrování infrastruktury povolené pro dvojité šifrování dat. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami:

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
                "keySource": "Microsoft.Storage",
                "requireInfrastructureEncryption": true,
                "services": {
                    "blob": { "enabled": true },
                    "file": { "enabled": true }
              }
            }
        }
    }
],
```

---

## <a name="verify-that-infrastructure-encryption-is-enabled"></a>Ověřte, jestli je povolené šifrování infrastruktury.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud chcete ověřit, jestli je pro účet úložiště povolené šifrování infrastruktury, zavolejte příkaz [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) . Tento příkaz vrátí sadu vlastností účtu úložiště a jejich hodnoty. Načtěte `RequireInfrastructureEncryption` pole v rámci `Encryption` vlastnosti a ověřte, jestli je nastavené na `True` .

Následující příklad načte hodnotu `RequireInfrastructureEncryption` Vlastnosti. Nezapomeňte nahradit hodnoty zástupných symbolů v lomených závorkách vlastními hodnotami:

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete ověřit, jestli je pro účet úložiště povolené šifrování infrastruktury, zavolejte příkaz [AZ Storage Account show](/cli/azure/storage/account#az-storage-account-show) . Tento příkaz vrátí sadu vlastností účtu úložiště a jejich hodnoty. Vyhledejte `requireInfrastructureEncryption` pole v rámci `encryption` vlastnosti a ověřte, zda je nastaveno na `true` .

Následující příklad načte hodnotu `requireInfrastructureEncryption` Vlastnosti. Nezapomeňte nahradit hodnoty zástupných symbolů v lomených závorkách vlastními hodnotami:

```azurecli-interactive
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Šablona](#tab/template)

Nelze použít

---

## <a name="next-steps"></a>Další kroky

- [Šifrování služby Azure Storage pro neaktivní uložená data](storage-service-encryption.md)
- [Použití klíčů spravovaných zákazníkem se Azure Key Vault ke správě šifrování Azure Storage](encryption-customer-managed-keys.md)