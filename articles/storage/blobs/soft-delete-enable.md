---
title: Povolení a Správa obnovitelného odstranění pro objekty blob
titleSuffix: Azure Storage
description: Povolit obnovitelné odstranění objektů BLOB pro snazší obnovení dat, když se omylem upraví nebo odstraní.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/15/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 970a6dfc167a6bef7984598c60e7ce89c6e4b34c
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2020
ms.locfileid: "84463718"
---
# <a name="enable-and-manage-soft-delete-for-blobs"></a>Povolení a Správa obnovitelného odstranění pro objekty blob

Obnovitelné odstranění: Ochrana dat objektů BLOB je náhodná nebo nesprávně upravená nebo Odstraněná. Když je pro účet úložiště povolené obnovitelné odstranění, objekty blob, verze objektů BLOB (Preview) a snímky v tomto účtu úložiště se po jejich odstranění můžou obnovit v době uchování, kterou zadáte.

Pokud existuje možnost, že by vaše data mohla být omylem upravována nebo odstraněna aplikací nebo jiným uživatelem účtu úložiště, společnost Microsoft doporučuje zapnout obnovitelné odstranění.

V tomto článku se dozvíte, jak začít s obnovitelné odstraněním.

## <a name="enable-soft-delete"></a>Povolení obnovitelného odstranění

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Povolit obnovitelné odstranění pro objekty BLOB v účtu úložiště pomocí Azure Portal:

1. V [Azure Portal](https://portal.azure.com/)vyberte svůj účet úložiště. 

2. Přejděte do možnosti **Ochrana dat** v části **BLOB Service**.

3. Klikněte na **Povolit** v části **BLOB – obnovitelné odstranění** .

4. Zadejte počet dní, po které chcete *zachovat* v části **zásady uchovávání informací** .

5. Kliknutím na tlačítko **Uložit** potvrďte nastavení ochrany dat.

![](media/soft-delete-enable/storage-blob-soft-delete-portal-configuration.png)

Chcete-li zobrazit obnovitelné odstraněné objekty blob, zaškrtněte políčko **Zobrazit odstraněné objekty blob** .

![](media/soft-delete-enable/storage-blob-soft-delete-portal-view-soft-deleted.png)

Pokud chcete pro daný objekt BLOB zobrazit obnovitelné odstraněné snímky, vyberte objekt BLOB a pak klikněte na **Zobrazit snímky**.

![](media/soft-delete-enable/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Ujistěte se, že je zaškrtnuté políčko **Zobrazit odstraněné snímky** .

![](media/soft-delete-enable/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Když kliknete na měkký odstraněný objekt BLOB nebo snímek, Všimněte si, že se zobrazí nové vlastnosti objektu BLOB. Označují, že se objekt odstranil, a kolik dní zbývá, dokud platnost snímku objektu BLOB nebo objektu BLOB neproběhne trvale. Pokud neměkký odstraněný objekt není snímkem, budete mít také možnost ho zrušit.

![](media/soft-delete-enable/storage-blob-soft-delete-portal-properties.png)

Pamatujte, že zrušení odstranění objektu BLOB také zruší odstranění všech přidružených snímků. Chcete-li obnovit odstraněné obnovitelné snímky pro aktivní objekt blob, klikněte na objekt BLOB a vyberte možnost zrušit **odstranění všech snímků**.

![](media/soft-delete-enable/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

Po obnovení snímků objektu blob můžete kliknutím na **zvýšit úroveň** zkopírovat snímek přes kořenový objekt blob, čímž se obnoví objekt blob do snímku.

![](media/soft-delete-enable/storage-blob-soft-delete-portal-promote-snapshot.png)

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

Chcete-li obnovit objekty blob, které byly omylem odstraněny, zavolejte u těchto objektů BLOB příkaz redelete. Nezapomeňte, že volání **Undelete BLOB**, jak u aktivních, tak i u objektů BLOB s odstraněnými objekty, obnoví všechny přidružené obnovitelné snímky odstraněné jako aktivní. Následující příklad volá Undelete u všech provizorních odstraněných a aktivních objektů BLOB v kontejneru:

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

# <a name="net-v12-sdk"></a>[Sada .NET V12 SDK](#tab/dotnet)

Pokud chcete povolit obnovitelné odstranění, aktualizujte vlastnosti služby klienta objektů BLOB:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_EnableSoftDelete":::

Chcete-li obnovit objekty blob, které byly omylem odstraněny, zavolejte u těchto objektů BLOB příkaz redelete. Nezapomeňte, že volání **Undelete**, jak u aktivních, tak i u tichých odstraněných objektů BLOB obnoví všechny přidružené obnovitelné snímky odstraněné jako aktivní. Následující příklad volá Undelete u všech provizorních odstraněných a aktivních objektů BLOB v kontejneru:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverDeletedBlobs":::

Chcete-li provést obnovení na konkrétní verzi objektu blob, nejdříve zavolejte příkaz Undelete u objektu BLOB a potom zkopírujte požadovaný snímek do objektu BLOB. Následující příklad obnoví objekt blob bloku na jeho naposledy vygenerovaný snímek:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverSpecificBlobVersion":::

# <a name="net-v11-sdk"></a>[Sada .NET V11 SDK](#tab/dotnet11)

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

Chcete-li obnovit objekty blob, které byly omylem odstraněny, zavolejte u těchto objektů BLOB příkaz redelete. Nezapomeňte, že volání **Undelete**, jak u aktivních, tak i u tichých odstraněných objektů BLOB obnoví všechny přidružené obnovitelné snímky odstraněné jako aktivní. Následující příklad volá Undelete u všech provizorních odstraněných a aktivních objektů BLOB v kontejneru:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Chcete-li provést obnovení na konkrétní verzi objektu blob, nejdříve zavolejte příkaz Undelete u objektu BLOB a potom zkopírujte požadovaný snímek do objektu BLOB. Následující příklad obnoví objekt blob bloku na jeho naposledy vygenerovaný snímek:

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

- [Obnovitelné odstranění pro úložiště objektů BLOB](soft-delete-overview.md)
- [Správa verzí objektů BLOB (Preview)](versioning-overview.md)