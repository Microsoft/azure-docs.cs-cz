---
title: Správa a obnovení objektů BLOB s odstraněnou přípouštějící
titleSuffix: Azure Storage
description: Spravujte a obnovte obnovitelné odstraněné objekty BLOB a snímky pomocí Azure Portal nebo pomocí Azure Storage klientských knihoven.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/27/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 9d5ef85d947ae999fd94ba5a6e9cdb00baec9786
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556104"
---
# <a name="manage-and-restore-soft-deleted-blobs"></a>Správa a obnovení objektů BLOB s odstraněnou přípouštějící

Měkké odstranění objektu BLOB chrání jednotlivé objekty BLOB a jejich verze, snímky a metadata před náhodným odstraněním nebo přepsáním tím, že v systému v zadaném časovém období udržuje Odstraněná data. Během doby uchování můžete objekt BLOB obnovit do jeho stavu při odstranění. Po vypršení doby uchování se objekt BLOB trvale odstraní. Další informace o obnovitelném odstranění objektů BLOB najdete v tématu [obnovitelné odstranění pro objekty blob](soft-delete-blob-overview.md).

Obnovitelné odstranění objektu BLOB je součástí komplexní strategie ochrany dat pro data objektů BLOB. Další informace o doporučeních Microsoftu pro ochranu dat najdete v tématu [Přehled ochrany dat](data-protection-overview.md).

## <a name="manage-soft-deleted-blobs-with-the-azure-portal"></a>Správa objektů BLOB s jemnou odstraněnou pomocí Azure Portal

Pomocí Azure Portal můžete zobrazit a obnovit místně odstraněné objekty BLOB a snímky.

### <a name="view-deleted-blobs"></a>Zobrazit odstraněné objekty blob

Když jsou objekty blob obnovitelné, nejsou ve výchozím nastavení viditelné Azure Portal. Chcete-li zobrazit dočasné odstraněné objekty blob, přejděte na stránku **Přehled** kontejneru a přepněte nastavení **Zobrazit odstraněné objekty blob** . Podmíněné odstraněné objekty BLOB se zobrazí se stavem **odstraněno**.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blobs-list-portal.png" alt-text="Snímek obrazovky znázorňující výpis objektů BLOB s příčárkou odstraněných v Azure Portal":::

V dalším kroku vyberte odstraněný objekt BLOB ze seznamu objektů blob, aby se zobrazily jeho vlastnosti. Na kartě **Přehled** si všimněte, že stav objektu BLOB je nastavený na hodnotu **odstraněno**. Na portálu se zobrazí také počet dní do trvalého odstranění objektu BLOB.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-properties-portal.png" alt-text="Snímek obrazovky s vlastnostmi neodstraněného objektu BLOB v Azure Portal":::

### <a name="view-deleted-snapshots"></a>Zobrazit odstraněné snímky

Odstranění objektu BLOB také odstraní všechny snímky přidružené k objektu BLOB. Pokud má objekt BLOB s odstraněnou podmnožinou snímků snímky, odstraněné snímky se taky dají zobrazit na portálu. Zobrazte vlastnosti neodstraněného objektu blob, přejděte na kartu **snímky** a přepněte **Zobrazit odstraněné snímky**.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-snapshots-portal.png" alt-text="Snímek obrazovky se zobrazením ":::

### <a name="restore-soft-deleted-objects-when-versioning-is-disabled"></a>Obnovit odstraněné objekty, když je zakázaná správa verzí

Pokud chcete obnovit odstraněné objekty BLOB v Azure Portal, když není povolená Správa verzí objektů blob, nejdřív zobrazte vlastnosti objektu BLOB a pak na kartě **Přehled** vyberte tlačítko zrušit **odstranění** . Obnovení objektu BLOB taky obnoví všechny snímky, které se odstranily během doby uchovávání obnovitelného odstranění.

:::image type="content" source="media/soft-delete-blob-manage/undelete-soft-deleted-blob-portal.png" alt-text="Snímek obrazovky s informacemi o tom, jak obnovit odstraněný objekt BLOB v Azure Portal":::

Pokud chcete povýšit odstraněný snímek na základní objekt blob, nejdřív se ujistěte, že se obnovily pracovní snímky objektu BLOB. Kliknutím na **tlačítko Obnovit** obnovíte obnovené snímky objektů blob, a to i v případě, že samotný základní objekt BLOB nebyl odstraněn. Potom vyberte snímek, který chcete propagovat, a pomocí tlačítka **zvýšit snímek** přepište základní objekt BLOB s obsahem snímku.

:::image type="content" source="media/soft-delete-blob-manage/promote-snapshot.png" alt-text="Snímek obrazovky ukazující, jak zvýšit úroveň snímku na základní objekt BLOB":::

### <a name="restore-soft-deleted-blobs-when-versioning-is-enabled"></a>Obnovování dočasně odstraněných objektů blob, když je povolená Správa verzí

Pokud chcete obnovit odstraněný objekt BLOB v Azure Portal, když je povolená Správa verzí, vyberte objekt BLOB s odstraněnými příčárkami, abyste zobrazili jeho vlastnosti, a pak vyberte kartu **verze** . Vyberte verzi, kterou chcete povýšit na aktuální verzi, a pak vyberte **nastavit aktuální verzi**.  

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-promote-version-portal.png" alt-text="Snímek obrazovky ukazující, jak zvýšit úroveň verze pro obnovení objektu BLOB v Azure Portal":::

Pokud chcete obnovit odstraněné verze nebo snímky, pokud je povolená Správa verzí, zobrazte vlastnosti objektu BLOB a na kartě **Přehled** vyberte tlačítko zrušit **odstranění** .

> [!NOTE]
> Když je povolená Správa verzí, při výběru tlačítka zrušit **odstranění** u odstraněného objektu BLOB se obnoví všechny odstraněné verze nebo snímky, ale neobnoví se základní objekt BLOB. Chcete-li obnovit základní objekt blob, je nutné zvýšit úroveň předchozí verze.

## <a name="manage-soft-deleted-blobs-with-code"></a>Správa objektů BLOB s jemnou odstraněnou pomocí kódu

Můžete použít klientské knihovny Azure Storage k obnovení odstraněných objektů BLOB nebo snímku. Následující příklady ukazují, jak používat klientskou knihovnu .NET.

### <a name="restore-soft-deleted-objects-when-versioning-is-disabled"></a>Obnovit odstraněné objekty, když je zakázaná správa verzí

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Pokud chcete obnovit odstraněné objekty blob, když není povolená Správa verzí, zavolejte na tyto objekty blob operaci [zrušit odstranění objektu BLOB](/rest/api/storageservices/undelete-blob) . Operace obnovení **objektu BLOB** obnoví obnovitelné odstraněné objekty BLOB a všechny odstraněné snímky přidružené k těmto objektům blob.

Volání metody **Undelete BLOB** u objektu blob, který nebyl odstraněn, nemá žádný vliv. Následující příklad volá příkaz zrušit **odstranění objektu BLOB** ve všech objektech blob v kontejneru a obnoví objekty BLOB s příčárkou a jejich snímky:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverDeletedBlobs":::

Pokud chcete obnovit konkrétní verzi, nejdřív zavolejte operaci **obnovení objektu BLOB** na základním objektu BLOB nebo verzi a pak zkopírujte požadovanou verzi přes základní objekt BLOB. Následující příklad obnoví objekt blob bloku na naposledy uloženou verzi:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverSpecificBlobSnapshot":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Pokud chcete obnovit odstraněné objekty blob, když není povolená Správa verzí, zavolejte na tyto objekty blob operaci [zrušit odstranění objektu BLOB](/rest/api/storageservices/undelete-blob) . Operace obnovení **objektu BLOB** obnoví obnovitelné odstraněné objekty BLOB a všechny odstraněné snímky přidružené k těmto objektům blob.

Volání metody **Undelete BLOB** u objektu blob, který nebyl odstraněn, nemá žádný vliv. Následující příklad volá příkaz zrušit **odstranění objektu BLOB** ve všech objektech blob v kontejneru a obnoví objekty BLOB s příčárkou a jejich snímky:

```csharp
// Restore all blobs in a container.
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Pro obnovení konkrétního snímku nejdříve zavolejte operaci **obnovení objektu BLOB** v základním objektu BLOB a potom zkopírujte požadovaný snímek přes základní objekt BLOB. Následující příklad obnoví objekt blob bloku na jeho naposledy vygenerovaný snímek:

```csharp
// Restore the block blob.
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container, prefixed by the blob name.
IEnumerable<IListBlobItem> allBlobSnapshots = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Copy the most recently generated snapshot to the base blob.
CloudBlockBlob copySource = allBlobSnapshots.First(snapshot => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)snapshot).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```  

---

### <a name="restore-soft-deleted-blobs-when-versioning-is-enabled"></a>Obnovování dočasně odstraněných objektů blob, když je povolená Správa verzí

Pokud chcete obnovit odstraněný objekt BLOB s příchodem, pokud je povolená Správa verzí, zkopírujte předchozí verzi přes základní objekt BLOB s objektem pro [kopírování](/rest/api/storageservices/copy-blob) nebo [objekt BLOB kopie z adresy URL](/rest/api/storageservices/copy-blob-from-url) .  

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RestorePreviousVersion":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Neužívá se. Správa verzí objektů BLOB je podporovaná jenom v klientských knihovnách Azure Storage verze 12. x a vyšší.

---

## <a name="next-steps"></a>Další kroky

- [Obnovitelné odstranění pro úložiště objektů BLOB](./soft-delete-blob-overview.md)
- [Povolení obnovitelného odstranění pro objekty blob](soft-delete-blob-enable.md)
- [Správa verzí objektů BLOB](versioning-overview.md)
