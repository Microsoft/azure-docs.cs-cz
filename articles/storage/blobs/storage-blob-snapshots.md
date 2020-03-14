---
title: Vytvoření a Správa snímku objektu BLOB v rozhraní .NET – Azure Storage
description: Naučte se vytvořit snímek objektu BLOB jen pro čtení pro zálohování dat objektů BLOB v daném časovém okamžiku.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/06/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 17cd57fbcf9b1c14fb275a070bdefdd1282c4d6e
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370521"
---
# <a name="create-and-manage-a-blob-snapshot-in-net"></a>Vytvoření a Správa snímku objektu BLOB v .NET

Snímek je verze objektu BLOB jen pro čtení, která je pořízena v určitém časovém okamžiku. Snímky jsou užitečné pro zálohování objektů BLOB. Tento článek popisuje, jak vytvořit a spravovat snímky objektů BLOB pomocí [klientské knihovny Azure Storage pro .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="about-blob-snapshots"></a>O snímcích objektů BLOB

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

Snímek objektu BLOB je stejný jako základní objekt BLOB s tím rozdílem, že identifikátor URI objektu BLOB má hodnotu **DateTime** připojenou k identifikátoru URI objektu blob, aby označoval čas, kdy se snímek povedl. Pokud je například identifikátor URI objektu blob stránky `http://storagesample.core.blob.windows.net/mydrives/myvhd`, je identifikátor URI snímku podobný `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`.

> [!NOTE]
> Všechny snímky sdílí identifikátor URI základního objektu BLOB. Jediným rozdílem mezi základním objektem BLOB a snímkem je připojená hodnota **DateTime** .
>

Objekt BLOB může mít libovolný počet snímků. Snímky zůstanou zachované, dokud se explicitně neodstraní, což znamená, že snímek nemůže být živý základní objekt BLOB. Můžete vytvořit výčet snímků přidružených k základnímu objektu BLOB a sledovat aktuální snímky.

Když vytvoříte snímek objektu blob, vlastnosti systému objektu BLOB se zkopírují do snímku se stejnými hodnotami. Metadata základního objektu BLOB se také zkopírují do snímku, pokud při vytváření snímku neurčíte samostatná metadata. Až snímek vytvoříte, můžete ho číst, kopírovat nebo odstranit, ale nemůžete ho upravovat.

Všechna zapůjčení přidružená k základnímu objektu BLOB neovlivňují snímek. U snímku nelze získat zapůjčení.

Soubor VHD se používá k uložení aktuálních informací a stavu disku virtuálního počítače. Disk můžete odpojit v rámci virtuálního počítače nebo ho vypnout a pak pořídit snímek jeho souboru VHD. Tento soubor snímku můžete použít později k načtení souboru VHD v daném časovém okamžiku a znovu vytvořit virtuální počítač.

## <a name="create-a-snapshot"></a>Vytvoření snímku

Chcete-li vytvořit snímek objektu blob bloku, použijte jednu z následujících metod:

- [CreateSnapshot](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshot)
- [CreateSnapshotAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshotasync)

Následující příklad kódu ukazuje, jak vytvořit snímek. Tento příklad určuje další metadata pro snímek při jeho vytvoření.

```csharp
private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
{
    // Create a new block blob in the container.
    CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

    // Add blob metadata.
    baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

    try
    {
        // Upload the blob to create it, with its metadata.
        await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
        await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="delete-snapshots"></a>Odstranit snímky

Pokud chcete odstranit objekt blob, musíte nejdřív odstranit všechny snímky tohoto objektu BLOB. Snímek můžete odstranit jednotlivě nebo můžete určit, že se při odstranění zdrojového objektu BLOB odstraní všechny snímky. Pokud se pokusíte odstranit objekt blob, který má stále snímky, dojde k chybě.

Pokud chcete odstranit snímky objektů blob, použijte jednu z následujících metod odstranění objektu BLOB a zahrňte do výčtu [DeleteSnapshotsOption](/dotnet/api/microsoft.azure.storage.blob.deletesnapshotsoption) .

- [Odstranění](/dotnet/api/microsoft.azure.storage.blob.cloudblob.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexistsasync)

Následující příklad kódu ukazuje, jak odstranit objekt BLOB a jeho snímky v rozhraní .NET, kde `blockBlob` je objekt typu [CloudBlockBlob][dotnet_CloudBlockBlob]:

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

## <a name="return-the-absolute-uri-to-a-snapshot"></a>Vrátí absolutní identifikátor URI snímku.

Následující příklad kódu vytvoří snímek a vypíše absolutní identifikátor URI pro primární umístění.

```csharp
//Create the blob service client object.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";

CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

//Get a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();

//Get a reference to a blob.
CloudBlockBlob blob = container.GetBlockBlobReference("sampleblob.txt");
blob.UploadText("This is a blob.");

//Create a snapshot of the blob and write out its primary URI.
CloudBlockBlob blobSnapshot = blob.CreateSnapshot();
Console.WriteLine(blobSnapshot.SnapshotQualifiedStorageUri.PrimaryUri);
```

## <a name="understand-how-snapshots-accrue-charges"></a>Vysvětlení způsobu nabíhání nákladů na snímky

Vytvoření snímku, který je kopií objektu BLOB jen pro čtení, může mít za následek další poplatky za ukládání dat do vašeho účtu. Při návrhu aplikace je důležité vědět, jak se tyto poplatky můžou snížit, abyste mohli minimalizovat náklady.

### <a name="important-billing-considerations"></a>Důležité informace o fakturaci

Následující seznam obsahuje klíčové body, které je potřeba vzít v úvahu při vytváření snímku.

- Váš účet úložiště se za jedinečné bloky nebo stránky účtuje bez ohledu na to, jestli jsou v objektu BLOB nebo ve snímku. U vašeho účtu se neúčtují další poplatky za snímky přidružené k objektu blob, dokud neaktualizujete objekt blob, na kterém jsou založené. Po aktualizaci základního objektu BLOB se odliší od jeho snímků. Pokud k tomu dojde, budou se vám účtovat jedinečné bloky nebo stránky v každém objektu BLOB nebo snímku.
- Když nahradíte blok v rámci objektu blob bloku, tento blok se následně účtuje jako jedinečný blok. To platí i v případě, že má blok stejné ID bloku a stejná data jako ve snímku. Po opětovném potvrzení bloku se odliší od jeho protějšku v jakémkoli snímku a za jeho data se vám budou účtovat poplatky. Totéž platí pro stránku v objektu blob stránky, který je aktualizovaný se stejnými daty.
- Nahrazení objektu blob bloku voláním metody [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream]nebo [UploadFromByteArray][dotnet_UploadFromByteArray] nahradí všechny bloky v objektu BLOB. Pokud máte snímek přidružený k tomuto objektu blob, všechny bloky v základním objektu BLOB a snímku se teď odchýlit a budou se vám účtovat všechny bloky v obou objektech blob. To platí i v případě, že data v základním objektu BLOB a snímku zůstanou stejná.
- Blob service Azure nemá žádný způsob, jak určit, jestli dva bloky obsahují identická data. Každý blok, který je nahraný a potvrzený, se považuje za jedinečný, a to i v případě, že má stejná data a stejné ID bloku. Vzhledem k tomu, že se poplatky za jedinečné bloky, je důležité vzít v úvahu, že aktualizace objektu blob, který má snímek, má za následek další jedinečné bloky a další poplatky.

### <a name="minimize-cost-with-snapshot-management"></a>Minimalizace nákladů pomocí správy snímků

Doporučujeme pečlivě spravovat vaše snímky, abyste se vyhnuli dodatečným poplatkům. Můžete dodržovat tyto osvědčené postupy, které vám pomohou minimalizovat náklady vzniklé úložištěm vašich snímků:

- Odstraňte a znovu vytvořte snímky přidružené k objektu BLOB při aktualizaci objektu blob, a to i v případě, že se aktualizujete se stejnými daty, pokud návrh aplikace nevyžaduje, abyste zachovali snímky. Odstraněním a opětovným vytvořením snímků objektu BLOB se můžete ujistit, že se objekty BLOB a snímky neodchylují.
- Pokud udržujete snímky pro objekt blob, vyhněte se voláním [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream]nebo [UploadFromByteArray][dotnet_UploadFromByteArray] k aktualizaci objektu BLOB. Tyto metody nahrazují všechny bloky v objektu blob, což způsobí, že základní objekt BLOB a jeho snímky se budou významně odchýlit. Místo toho aktualizujte nejmenší možný počet bloků pomocí metod [PutBlock][dotnet_PutBlock] a [PutBlockList][dotnet_PutBlockList] .

### <a name="snapshot-billing-scenarios"></a>Scénáře fakturace snímků

Následující scénáře ukazují, jak se účtují poplatky za objekt blob bloku a jeho snímky.

#### <a name="scenario-1"></a>Scénář 1

Ve scénáři 1 se základní objekt BLOB po pořízení snímku neaktualizoval, takže se poplatky účtují jenom pro jedinečné bloky 1, 2 a 3.

![Prostředky Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>Scénář 2

Ve scénáři 2 se základní objekt BLOB aktualizoval, ale snímek ne. Blok 3 byl aktualizován, a přestože obsahuje stejná data a stejné ID, není stejný jako blok 3 ve snímku. Výsledkem je, že se účtu účtuje čtyři bloky.

![Prostředky Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>Scénář 3

Ve scénáři 3 se základní objekt BLOB aktualizoval, ale snímek ne. Blok 3 byl nahrazen blokem 4 v základním objektu blob, ale snímek stále odráží blok 3. Výsledkem je, že se účtu účtuje čtyři bloky.

![Prostředky Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>Scénář 4

Ve scénáři 4 byl základní objekt BLOB zcela aktualizován a neobsahuje žádné původní bloky. V důsledku toho se účet účtuje za všechny osm jedinečných bloků. K tomuto scénáři může dojít, pokud používáte metodu aktualizace, jako je [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream]nebo [UploadFromByteArray][dotnet_UploadFromByteArray], protože tyto metody nahrazují celý obsah objektu BLOB.

![Prostředky Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Další kroky

- Další informace o práci s virtuálními počítači a snímky disků virtuálních počítačů najdete v [zálohování nespravovaných disků virtuálních počítačů Azure pomocí přírůstkových snímků](../../virtual-machines/windows/incremental-snapshots.md) .

- Další příklady kódu s využitím úložiště objektů BLOB najdete v tématu [ukázky kódu Azure](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob). Můžete si stáhnout ukázkovou aplikaci a spustit ji nebo procházet kód na GitHubu.

[dotnet_AccessCondition]: https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.accesscondition
[dotnet_CloudBlockBlob]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_block_blob
[dotnet_CreateSnapshotAsync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.createsnapshotasync
[dotnet_HTTPStatusCode]: https://docs.microsoft.com/java/api/com.microsoft.store.partnercenter.network.httpstatuscode
[dotnet_PutBlockList]: /dotnet/api/microsoft.azure.storage.blob.cloudblockblob.putblocklist
[dotnet_PutBlock]: /dotnet/api/microsoft.azure.storage.blob.cloudblockblob.putblock
[dotnet_UploadFromByteArray]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.uploadfrombytearray
[dotnet_UploadFromFile]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.uploadfromfile
[dotnet_UploadFromStream]: /dotnet/api/microsoft.azure.storage.blob.cloudappendblob.uploadfromstream
[dotnet_UploadText]: /dotnet/api/microsoft.azure.storage.blob.cloudappendblob.uploadtext
