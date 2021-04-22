---
title: Povolení a správa verzí objektů BLOB
titleSuffix: Azure Storage
description: Naučte se povolit správu verzí objektů BLOB ve Azure Portal nebo pomocí Azure Resource Manager šablony.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/09/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: f6a1d315342ea98ccaf1382630eccca876ada3f1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870188"
---
# <a name="enable-and-manage-blob-versioning"></a>Povolení a správa verzí objektů BLOB

Můžete povolit správu verzí služby Blob Storage a automaticky tak zachovat předchozí verze objektů BLOB při jejich úpravě nebo odstranění. Pokud je povolená Správa verzí objektů blob, můžete obnovit předchozí verzi objektu blob, aby se data obnovila v případě, že se omylem změnila nebo odstranila.

Tento článek popisuje, jak povolit nebo zakázat správu verzí objektů BLOB pro účet úložiště pomocí šablony Azure Portal nebo Azure Resource Manager. Další informace o tom, jak se správou verzí objektů blob, najdete v tématu [Správa verzí objektů BLOB](versioning-overview.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="enable-blob-versioning"></a>Povolení správy verzí objektů blob

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Povolení správy verzí objektů BLOB pro účet úložiště v Azure Portal:

1. Na portálu přejděte na svůj účet úložiště.
1. V části **BLOB Service** vyberte **Ochrana dat**.
1. V části **Správa verzí** vyberte **povoleno**.

:::image type="content" source="media/versioning-enable/portal-enable-versioning.png" alt-text="Snímek obrazovky ukazující, jak povolit správu verzí objektů BLOB v Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud chcete povolit správu verzí objektů BLOB pro účet úložiště pomocí PowerShellu, nejdřív nainstalujte modul [AZ. Storage](https://www.powershellgallery.com/packages/Az.Storage) verze 2.3.0 nebo novější. Pak zavolejte příkaz [Update-AzStorageBlobServiceProperty](/powershell/module/az.storage/update-azstorageblobserviceproperty) pro povolení správy verzí, jak je znázorněno v následujícím příkladu. Nezapomeňte nahradit hodnoty v lomených závorkách vlastními hodnotami:

```powershell
# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable versioning.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -IsVersioningEnabled $true
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete povolit správu verzí objektů BLOB pro účet úložiště pomocí Azure CLI, nejdřív nainstalujte Azure CLI verze 2.2.0 nebo novější. Potom zavolejte příkaz [AZ Storage Account BLOB-Service-Properties Update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) , aby bylo možné povolit správu verzí, jak je znázorněno v následujícím příkladu. Nezapomeňte nahradit hodnoty v lomených závorkách vlastními hodnotami:

```azurecli
az storage account blob-service-properties update \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --enable-versioning true
```

# <a name="template"></a>[Šablona](#tab/template)

Chcete-li povolit správu verzí objektů BLOB pomocí šablony, vytvořte šablonu s vlastností **IsVersioningEnabled** na **hodnotu true**. Následující postup popisuje, jak vytvořit šablonu v Azure Portal.

1. V Azure Portal klikněte na možnost **vytvořit prostředek**.
1. V **části Hledat na Marketplace** zadejte **šablonu Deployment** a potom stiskněte **ENTER**.
1. Zvolte **template Deployment**, zvolte **vytvořit** a potom **v editoru zvolte sestavit vlastní šablonu**.
1. V editoru šablon vložte následující kód JSON. `<accountName>`Zástupný symbol nahraďte názvem vašeho účtu úložiště.
1. Uložte šablonu.
1. Zadejte skupinu prostředků účtu a pak kliknutím na tlačítko **koupit** nasaďte šablonu a povolte správu verzí objektů BLOB.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts/blobServices",
                "apiVersion": "2019-06-01",
                "name": "<accountName>/default",
                "properties": {
                    "IsVersioningEnabled": true
                }
            }
        ]
    }
    ```

Další informace o nasazení prostředků pomocí šablon v Azure Portal najdete v tématu [nasazení prostředků pomocí Azure Portal](../../azure-resource-manager/templates/deploy-portal.md).

---

## <a name="modify-a-blob-to-trigger-a-new-version"></a>Úprava objektu BLOB tak, aby aktivoval novou verzi

Následující příklad kódu ukazuje, jak aktivovat vytvoření nové verze pomocí klientské knihovny Azure Storage pro .NET, verze [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) nebo novější. Před spuštěním tohoto příkladu se ujistěte, že jste povolili správu verzí pro váš účet úložiště.

Tento příklad vytvoří objekt blob bloku a pak aktualizuje metadata objektu BLOB. Aktualizace metadat objektu BLOB aktivuje vytvoření nové verze. Příklad načte počáteční verzi a aktuální verzi a zobrazí, že metadata obsahují pouze aktuální verze.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_UpdateVersionedBlobMetadata":::

## <a name="list-blob-versions"></a>Vypsat verze objektů BLOB

Chcete-li vypsat verze nebo snímky objektů BLOB pomocí klientské knihovny .NET V12, zadejte parametr [BlobStates](/dotnet/api/azure.storage.blobs.models.blobstates) s polem **verze** .

Následující příklad kódu ukazuje, jak zobrazit výpis verze objektů BLOB pomocí klientské knihovny Azure Storage pro .NET, verze [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) nebo novější. Před spuštěním tohoto příkladu se ujistěte, že jste povolili správu verzí pro váš účet úložiště.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobVersions":::

## <a name="next-steps"></a>Další kroky

- [Správa verzí objektů BLOB](versioning-overview.md)
- [Obnovitelné odstranění objektů blob služby Azure Storage](./soft-delete-blob-overview.md)