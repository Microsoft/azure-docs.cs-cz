---
title: Povolení obnovitelného odstranění pro objekty blob
titleSuffix: Azure Storage
description: Povolte obnovitelné odstranění pro objekty blob pro ochranu dat objektů BLOB před náhodným odstraněním nebo přepsáním.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/27/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli
ms.openlocfilehash: 11323f2aec05935b9dc45187ed54597e61af924d
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554109"
---
# <a name="enable-soft-delete-for-blobs"></a>Povolení obnovitelného odstranění pro objekty blob

Měkké odstranění objektu BLOB chrání jednotlivé objekty BLOB a jejich verze, snímky a metadata před náhodným odstraněním nebo přepsáním tím, že v systému v zadaném časovém období udržuje Odstraněná data. Během doby uchování můžete objekt BLOB obnovit do jeho stavu při odstranění. Po vypršení doby uchování se objekt BLOB trvale odstraní. Další informace o obnovitelném odstranění objektů BLOB najdete v tématu [obnovitelné odstranění pro objekty blob](soft-delete-blob-overview.md).

Obnovitelné odstranění objektu BLOB je součástí komplexní strategie ochrany dat pro data objektů BLOB. Další informace o doporučeních Microsoftu pro ochranu dat najdete v tématu [Přehled ochrany dat](data-protection-overview.md).

## <a name="enable-blob-soft-delete"></a>Povolit obnovitelné odstranění objektu BLOB

Obnovitelné odstranění objektu BLOB je ve výchozím nastavení pro nový účet úložiště zakázané. Obnovitelné odstranění účtu úložiště můžete kdykoli povolit nebo zakázat pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Pokud chcete pro svůj účet úložiště povolit obnovitelné odstranění objektů BLOB pomocí Azure Portal, postupujte takto:

1. Na webu [Azure Portal](https://portal.azure.com/) přejděte ke svému účtu úložiště.
1. V části **BLOB Service** Najděte možnost **Ochrana dat** .
1. V části **obnovení** vyberte **zapnout obnovitelné odstranění pro objekty blob**.
1. Zadejte dobu uchování mezi 1 a 365 dny. Microsoft doporučuje minimální dobu uchování po dobu sedmi dnů.
1. Uložte provedené změny.

:::image type="content" source="media/soft-delete-blob-enable/blob-soft-delete-configuration-portal.png" alt-text="Snímek obrazovky ukazující, jak povolit obnovitelné odstranění v Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud chcete v PowerShellu povolit obnovitelné odstranění objektů blob, zavolejte příkaz [Enable-AzStorageBlobDeleteRetentionPolicy](/powershell/module/az.storage/enable-azstorageblobdeleteretentionpolicy) , který určí dobu uchování ve dnech.

Následující příklad povoluje dočasné odstranění objektů BLOB a nastavuje dobu uchování na sedm dní. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami:

```azurepowershell
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -RetentionDays 7
```

Pokud chcete ověřit aktuální nastavení pro obnovitelné odstranění objektu blob, zavolejte příkaz [Get-AzStorageBlobServiceProperty](/powershell/module/az.storage/get-azstorageblobserviceproperty) :

```azurepowershell
$properties = Get-AzStorageBlobServiceProperty -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$properties.DeleteRetentionPolicy.Enabled
$properties.DeleteRetentionPolicy.Days
```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/azure-CLI)

Pokud chcete povolit obnovitelné odstranění objektů BLOB pomocí rozhraní příkazového řádku Azure, zavolejte příkaz [AZ Storage Account BLOB-Service-Properties Update](/cli/azure/ext/storage-blob-preview/storage/account/blob-service-properties#ext_storage_blob_preview_az_storage_account_blob_service_properties_update) a určete dobu uchování ve dnech.

Následující příklad povoluje dočasné odstranění objektů BLOB a nastavuje dobu uchování na sedm dní. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami:

```azurecli-interactive
az storage account blob-service-properties update --account-name <storage-account> \
    --resource-group <resource-group> \
    --enable-delete-retention true \
    --delete-retention-days 7
```

Chcete-li zjistit aktuální nastavení pro obnovitelné odstranění objektu blob, zavolejte příkaz [AZ Storage Account BLOB-Service-Properties show](/cli/azure/ext/storage-blob-preview/storage/account/blob-service-properties#ext_storage_blob_preview_az_storage_account_blob_service_properties_show) :

```azurecli-interactive
az storage account blob-service-properties show --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>Další kroky

- [Obnovitelné odstranění pro objekty blob](soft-delete-blob-overview.md)
- [Správa a obnovení objektů BLOB s odstraněnou přípouštějící](soft-delete-blob-manage.md)
