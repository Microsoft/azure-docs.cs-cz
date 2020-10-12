---
title: Vytvoření účtu úložiště s povoleným šifrováním infrastruktury pro dvojité šifrování dat
titleSuffix: Azure Storage
description: Zákazníci, kteří vyžadují vyšší úroveň záruky, že jejich data jsou zabezpečená, můžou na úrovni infrastruktury Azure Storage povolit taky 256 šifrování AES. Když je povolené šifrování infrastruktury, data v účtu úložiště se zašifrují dvakrát pomocí dvou různých šifrovacích algoritmů a dvou různých klíčů.
services: storage
author: tamram
ms.service: storage
ms.date: 09/17/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 3164de9c3e44001d58d46eab9f823041b440960b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90984164"
---
# <a name="create-a-storage-account-with-infrastructure-encryption-enabled-for-double-encryption-of-data"></a>Vytvoření účtu úložiště s povoleným šifrováním infrastruktury pro dvojité šifrování dat

Azure Storage automaticky šifruje všechna data v účtu úložiště na úrovni služby pomocí 256 šifrování AES, jedno z nejsilnějších šifrovacích šifr, které jsou kompatibilní se standardem FIPS 140-2. Zákazníci, kteří vyžadují vyšší úroveň záruky, že jejich data jsou zabezpečená, můžou na úrovni infrastruktury Azure Storage povolit taky 256 šifrování AES. Když je povolené šifrování infrastruktury, data v účtu úložiště se &mdash; na úrovni služby šifrují dvakrát a jednou na úrovni infrastruktury &mdash; se dvěma různými šifrovacími algoritmy a dvěma různými klíči. Dvojité šifrování Azure Storage dat chrání před scénářem, kdy může dojít k ohrožení jednoho z šifrovacích algoritmů nebo klíčů. V tomto scénáři bude další vrstva šifrování nadále chránit vaše data.

Šifrování na úrovni služby podporuje použití klíčů spravovaných společností Microsoft nebo klíčů spravovaných zákazníkem pomocí Azure Key Vault nebo Key Vault modelu HSM (Managed hardware Security) (verze Preview). Šifrování na úrovni infrastruktury spoléhá na klíče spravované Microsoftem a vždycky používá samostatný klíč. Další informace o správě klíčů pomocí šifrování Azure Storage najdete v tématu [o správě šifrovacích klíčů](storage-service-encryption.md#about-encryption-key-management).

Pro zdvojnásobení šifrování dat musíte nejdřív vytvořit účet úložiště, který je nakonfigurovaný pro šifrování infrastruktury. Tento článek popisuje, jak vytvořit účet úložiště, který umožňuje šifrování infrastruktury.

## <a name="register-to-use-infrastructure-encryption"></a>Registrace pro použití šifrování infrastruktury

Pokud chcete vytvořit účet úložiště s povoleným šifrováním infrastruktury, musíte se nejdřív zaregistrovat na používání této funkce v Azure pomocí PowerShellu nebo rozhraní příkazového řádku Azure CLI.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Není k dispozici

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud se chcete zaregistrovat v prostředí PowerShell, zavolejte příkaz [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) .

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

Pokud chcete zjistit stav vaší registrace pomocí PowerShellu, zavolejte příkaz [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) .

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

Po schválení registrace je potřeba znovu zaregistrovat poskytovatele prostředků Azure Storage. Chcete-li znovu zaregistrovat poskytovatele prostředků v prostředí PowerShell, zavolejte příkaz [Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) .

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud se chcete zaregistrovat v Azure CLI, zavolejte příkaz [AZ Feature Register](/cli/azure/feature#az-feature-register) .

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

Pokud chcete zjistit stav registrace pomocí Azure CLI, zavolejte příkaz [AZ Feature](/cli/azure/feature#az-feature-show) .

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

Po schválení registrace je potřeba znovu zaregistrovat poskytovatele prostředků Azure Storage. Pokud chcete poskytovatele prostředků v Azure CLI znovu zaregistrovat, zavolejte příkaz [AZ Provider Register](/cli/azure/provider#az-provider-register) .

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[Šablona](#tab/template)

Není k dispozici

---

## <a name="create-an-account-with-infrastructure-encryption-enabled"></a>Vytvoření účtu s povoleným šifrováním infrastruktury

Účet úložiště musíte nakonfigurovat tak, aby používal šifrování infrastruktury v době, kdy vytváříte účet. Účet úložiště musí být typu pro obecné účely v2.

Po vytvoření účtu nelze povolit nebo zakázat šifrování infrastruktury.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Pokud chcete k vytvoření účtu úložiště s povoleným šifrováním infrastruktury použít PowerShell, postupujte takto:

1. V Azure Portal přejděte na stránku **účty úložiště** .
1. Kliknutím na tlačítko **Přidat** přidejte nový účet úložiště pro obecné účely v2.
1. Na kartě **Upřesnit** vyhledejte možnost šifrování **infrastruktury** a vyberte **povoleno**.
1. Kliknutím na tlačítko **zkontrolovat + vytvořit** dokončete vytváření účtu úložiště.

    :::image type="content" source="media/infrastructure-encryption-enable/create-account-infrastructure-encryption-portal.png" alt-text="Snímek obrazovky ukazující, jak povolit šifrování infrastruktury při vytváření účtu":::

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

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Pokud chcete ověřit, jestli je pro účet úložiště s Azure Portal povolené šifrování infrastruktury, postupujte takto:

1. Na webu Azure Portal přejděte na svůj účet úložiště.
1. V části **Nastavení**vyberte **šifrování**.

    :::image type="content" source="media/infrastructure-encryption-enable/verify-infrastructure-encryption-portal.png" alt-text="Snímek obrazovky ukazující, jak povolit šifrování infrastruktury při vytváření účtu":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud chcete ověřit, jestli je pro účet úložiště s PowerShellem povolené šifrování infrastruktury, zavolejte příkaz [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) . Tento příkaz vrátí sadu vlastností účtu úložiště a jejich hodnoty. Načtěte `RequireInfrastructureEncryption` pole v rámci `Encryption` vlastnosti a ověřte, jestli je nastavené na `True` .

Následující příklad načte hodnotu `RequireInfrastructureEncryption` Vlastnosti. Nezapomeňte nahradit hodnoty zástupných symbolů v lomených závorkách vlastními hodnotami:

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete ověřit, jestli je povolené šifrování infrastruktury pro účet úložiště pomocí Azure CLI, zavolejte příkaz [AZ Storage Account show](/cli/azure/storage/account#az-storage-account-show) . Tento příkaz vrátí sadu vlastností účtu úložiště a jejich hodnoty. Vyhledejte `requireInfrastructureEncryption` pole v rámci `encryption` vlastnosti a ověřte, zda je nastaveno na `true` .

Následující příklad načte hodnotu `requireInfrastructureEncryption` Vlastnosti. Nezapomeňte nahradit hodnoty zástupných symbolů v lomených závorkách vlastními hodnotami:

```azurecli-interactive
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Šablona](#tab/template)

Není k dispozici

---

## <a name="next-steps"></a>Další kroky

- [Šifrování služby Azure Storage pro neaktivní uložená data](storage-service-encryption.md)
- [Klíče spravované zákazníkem pro šifrování Azure Storage](customer-managed-keys-overview.md)