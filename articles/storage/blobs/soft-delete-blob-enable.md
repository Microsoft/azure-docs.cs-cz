---
title: Povolení a Správa obnovitelného odstranění pro objekty blob
titleSuffix: Azure Storage
description: Povolit obnovitelné odstranění objektů blob, aby bylo možné snadněji obnovit data v případě, že se omylem změnila nebo odstranila.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/15/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: a74860b7adf4dade5aedc71a4960595cbe55eaf0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95995297"
---
# <a name="enable-and-manage-soft-delete-for-blobs"></a>Povolení a Správa obnovitelného odstranění pro objekty blob

Částečný odstranění objektu BLOB chrání vaše data před náhodným nebo omylem úpravou nebo odstraněním. Když je pro účet úložiště povolené obnovitelné odstranění objektů blob, můžou se objekty blob, verze objektů BLOB a snímky v tomto účtu úložiště po jejich odstranění obnovit v rámci doby uchování, kterou zadáte.

Pokud existuje možnost, že by vaše data mohla být omylem upravována nebo odstraněna aplikací nebo jiným uživatelem účtu úložiště, společnost Microsoft doporučuje zapnout funkci BLOB Soft DELETE. V tomto článku se dozvíte, jak povolit obnovitelné odstranění pro objekty blob. Další podrobnosti o obnovitelném odstranění objektů BLOB najdete v tématu [obnovitelné odstranění pro objekty blob](soft-delete-blob-overview.md).

Další informace o tom, jak povolit obnovitelné odstranění kontejnerů, najdete v tématu [povolení a Správa obnovitelného odstranění kontejnerů](soft-delete-container-enable.md).

## <a name="enable-blob-soft-delete"></a>Povolit obnovitelné odstranění objektu BLOB

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Povolit obnovitelné odstranění pro objekty BLOB v účtu úložiště pomocí Azure Portal:

1. Na webu [Azure Portal](https://portal.azure.com/) přejděte ke svému účtu úložiště.
1. V části **BLOB Service** Najděte možnost **Ochrana dat** .
1. Nastavte vlastnost **měkkého odstranění objektu BLOB** na *povolenou*.
1. V části **zásady uchovávání informací** určete, jak dlouho se mají dočasně odstraněné objekty blob uchovávat pomocí Azure Storage.
1. Uložte provedené změny.

![Snímek obrazovky webu Azure Portal se zvolenou službou data Protection BLOB](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-configuration.png)

Chcete-li zobrazit obnovitelné odstraněné objekty blob, zaškrtněte políčko **Zobrazit odstraněné objekty blob** .

![Snímek stránky služby BLOB data Protection s zvýrazněnou možností zobrazit odstraněné objekty blob](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-view-soft-deleted.png)

Pokud chcete pro daný objekt BLOB zobrazit obnovitelné odstraněné snímky, vyberte objekt BLOB a pak klikněte na **Zobrazit snímky**.

![Snímek stránky služby BLOB data Protection se zvýrazněnou možností zobrazení snímků](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Ujistěte se, že je zaškrtnuté políčko **Zobrazit odstraněné snímky** .

![Snímek stránky zobrazení snímků se zvýrazněnou možností zobrazit odstraněné objekty blob](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Když kliknete na měkký odstraněný objekt BLOB nebo snímek, Všimněte si, že se zobrazí nové vlastnosti objektu BLOB. Označují, že se objekt odstranil, a kolik dní zbývá, dokud platnost snímku objektu BLOB nebo objektu BLOB neproběhne trvale. Pokud neměkký odstraněný objekt není snímkem, budete mít také možnost ho zrušit.

![Snímek obrazovky podrobností nepodmíněného odstraněného objektu.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-properties.png)

Pamatujte, že zrušení odstranění objektu BLOB také zruší odstranění všech přidružených snímků. Chcete-li obnovit odstraněné obnovitelné snímky pro aktivní objekt blob, klikněte na objekt BLOB a vyberte možnost zrušit **odstranění všech snímků**.

![Snímek obrazovky s podrobnostmi nepodmíněného odstraněného objektu BLOB.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

Po obnovení snímků objektu blob můžete kliknutím na **zvýšit úroveň** zkopírovat snímek přes kořenový objekt blob, čímž se obnoví objekt blob do snímku.

![Snímek stránky zobrazení snímků se zvýrazněnou možností zvýšení úrovně](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-promote-snapshot.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pokud chcete povolit obnovitelné odstranění, aktualizujte vlastnosti služby klienta objektů BLOB. Následující příklad povoluje obnovitelné odstranění pro podmnožinu účtů v rámci předplatného:

```powershell
Set-AzContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzStorageDeleteRetentionPolicy -RetentionDays 7
```

Pomocí následujícího příkazu můžete ověřit, zda bylo toto obnovitelné odstranění zapnuté:

```powershell
$MatchingAccounts | $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context | Select-Object -ExpandProperty DeleteRetentionPolicy
```

Chcete-li obnovit objekty blob, které byly omylem odstraněny, zavolejte u těchto objektů BLOB příkaz **Undelete BLOB** . Nezapomeňte, že volání **Undelete BLOB**, jak u aktivních, tak i u objektů BLOB s odstraněnými objekty, obnoví všechny přidružené obnovitelné snímky odstraněné jako aktivní. Následující příklad volá odstraněné objekty **BLOB** u všech provizorních odstraněných a aktivních objektů BLOB v kontejneru:

```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
Chcete-li najít aktuální zásady uchovávání informací o tichém odstranění, použijte následující příkaz:

```azurepowershell-interactive
   $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context
```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/azure-CLI)

Pokud chcete povolit obnovitelné odstranění, aktualizujte vlastnosti služby klienta objektů BLOB:

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

Chcete-li ověřit, zda je funkce obnovitelné odstranění zapnutá, použijte následující příkaz: 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

# <a name="python"></a>[Python](#tab/python)

Pokud chcete povolit obnovitelné odstranění, aktualizujte vlastnosti služby klienta objektů BLOB:

```python
# Make the requisite imports
from azure.storage.blob import BlockBlobService
from azure.storage.common.models import DeleteRetentionPolicy

# Initialize a block blob service
block_blob_service = BlockBlobService(
    account_name='<enter your storage account name>', account_key='<enter your storage account key>')

# Set the blob client's service property settings to enable soft delete
block_blob_service.set_blob_service_properties(
    delete_retention_policy=DeleteRetentionPolicy(enabled=True, days=7))
```

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Pokud chcete povolit obnovitelné odstranění, aktualizujte vlastnosti služby klienta objektů BLOB:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_EnableSoftDelete":::

Chcete-li obnovit objekty blob, které byly omylem odstraněny, zavolejte u těchto objektů BLOB příkaz redelete. Nezapomeňte, že volání **Undelete**, jak u aktivních, tak i u tichých odstraněných objektů BLOB obnoví všechny přidružené obnovitelné snímky odstraněné jako aktivní. Následující příklad volá Undelete u všech provizorních odstraněných a aktivních objektů BLOB v kontejneru:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverDeletedBlobs":::

Chcete-li provést obnovení na konkrétní verzi objektu blob, nejdříve zavolejte příkaz Undelete u objektu BLOB a potom zkopírujte požadovaný snímek do objektu BLOB. Následující příklad obnoví objekt blob bloku na jeho naposledy vygenerovaný snímek:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverSpecificBlobVersion":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Pokud chcete povolit obnovitelné odstranění, aktualizujte vlastnosti služby klienta objektů BLOB:

```csharp
// Get the blob client's service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client's service property settings
blobClient.SetServiceProperties(serviceProperties);
```

Chcete-li obnovit objekty blob, které byly omylem odstraněny, zavolejte u těchto objektů BLOB příkaz **Undelete BLOB** . Nezapomeňte, že volání **Undelete BLOB**, jak u aktivních, tak i u objektů BLOB s odstraněnými objekty, obnoví všechny přidružené obnovitelné snímky odstraněné jako aktivní. Následující příklad volá odstraněné objekty **BLOB** pro všechny obnovitelné a aktivní objekty BLOB v kontejneru:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Pokud chcete provést obnovení na konkrétní verzi objektu blob, nejdřív zavolejte operaci **obnovení objektu BLOB** a potom zkopírujte požadovaný snímek přes objekt BLOB. Následující příklad obnoví objekt blob bloku na jeho naposledy vygenerovaný snímek:

```csharp
// Undelete
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container prefixed by the blob name
IEnumerable<IListBlobItem> allBlobVersions = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Restore the most recently generated snapshot to the active blob
CloudBlockBlob copySource = allBlobVersions.First(version => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)version).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```  

---

## <a name="next-steps"></a>Další kroky

- [Obnovitelné odstranění pro úložiště objektů BLOB](./soft-delete-blob-overview.md)
- [Správa verzí objektů BLOB](versioning-overview.md)