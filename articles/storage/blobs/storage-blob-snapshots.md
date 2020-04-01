---
title: Vytvoření a správa snímku objektu blob v rozhraní .NET – Azure Storage
description: Zjistěte, jak vytvořit snímek objektu blob jen pro čtení pro zálohování dat objektů blob v daném okamžiku v čase.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/06/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 9bf5eea55002814f461d375b3db43a37fe4f7aa9
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474098"
---
# <a name="create-and-manage-a-blob-snapshot-in-net"></a>Vytvoření a správa snímku objektu blob v rozhraní .NET

Snímek je verze objektu blob jen pro čtení, který je přijata v určitém okamžiku. Snímky jsou užitečné pro zálohování objektů BLOB. Tento článek ukazuje, jak vytvořit a spravovat snímky objektů blob pomocí [klientské knihovny Azure Storage pro .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="about-blob-snapshots"></a>O snímcích objektů blob

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

Snímek objektu blob je identický s jeho základní objekt blob, s tím rozdílem, že objekt URI objektu blob má **DateTime** hodnotu připojenou k objektu URI objektu blob k označení času, kdy byl pořízen snímek. Pokud je `http://storagesample.core.blob.windows.net/mydrives/myvhd`například identifikátor URI objektu BLOB `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`stránky , identifikátor URI snímku je podobný .

> [!NOTE]
> Všechny snímky sdílejí identifikátor URI základního objektu BLOB. Jediným rozdílem mezi základním objektem blob a snímek je **připojendatetime** hodnotu.
>

Objekt blob může mít libovolný počet snímků. Snímky přetrvávají, dokud nejsou explicitně odstraněny, což znamená, že snímek nemůže přežít jeho základní objekt blob. Můžete vytvořit výčet snímků přidružených k základní objekt blob sledovat aktuální snímky.

Když vytvoříte snímek objektu blob, vlastnosti systému objektu blob jsou zkopírovány do snímku se stejnými hodnotami. Metadata základního objektu blob se také zkopírují do snímku, pokud nezadáte samostatná metadata pro snímek při jeho vytvoření. Po vytvoření snímku jej můžete číst, kopírovat nebo odstraňovat, ale nelze jej změnit.

Všechny zapůjčení přidružené k základní objekt blob nemají vliv na snímek. Nelze získat zapůjčení na snímek.

Soubor VHD se používá k ukládání aktuálních informací a stavu pro disk virtuálního počítače. Můžete odpojit disk z virtuálního počítače nebo vypnout virtuální ho a potom pořídit snímek jeho souboru VHD. Tento soubor snímku můžete později použít k načtení souboru VHD v tomto okamžiku a znovu vytvořit virtuální ho.

## <a name="create-a-snapshot"></a>Vytvoření snímku

Chcete-li vytvořit snímek objektu blob bloku, použijte jednu z následujících metod:

- [Vytvořit snímek](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshot)
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

## <a name="delete-snapshots"></a>Odstranění snímků

Chcete-li odstranit objekt blob, musíte nejprve odstranit všechny snímky tohoto objektu blob. Snímek můžete odstranit jednotlivě nebo určit, že všechny snímky budou odstraněny při odstranění zdrojového objektu blob. Pokud se pokusíte odstranit objekt blob, který stále má snímky, výsledky chyby.

Chcete-li odstranit snímky objektů blob, použijte jednu z následujících metod odstranění objektů blob a zahrňte výčet [DeleteSnapshotsOption.](/dotnet/api/microsoft.azure.storage.blob.deletesnapshotsoption)

- [Odstranit](/dotnet/api/microsoft.azure.storage.blob.cloudblob.delete)
- [OdstranitAsynchronizace](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteasync)
- [OdstranitIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexists)
- [OdstranitIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexistsasync)

Následující příklad kódu ukazuje, jak odstranit objekt blob a `blockBlob` jeho snímky v rozhraní .NET, kde je objekt typu [CloudBlockBlob][dotnet_CloudBlockBlob]:

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

## <a name="return-the-absolute-uri-to-a-snapshot"></a>Vrátit absolutní identifikátor URI na snímek

Následující příklad kódu vytvoří snímek a zapíše absolutní identifikátor URI pro primární umístění.

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

## <a name="understand-how-snapshots-accrue-charges"></a>Pochopit, jak snímky časově rozrůstají poplatky

Vytvoření snímku, který je jen pro čtení kopie objektu blob, může mít za následek další poplatky za úložiště dat do vašeho účtu. Při navrhování aplikace je důležité si uvědomit, jak tyto poplatky mohou narůstat, abyste mohli minimalizovat náklady.

### <a name="important-billing-considerations"></a>Důležité informace o fakturaci

Následující seznam obsahuje klíčové body, které je třeba vzít v úvahu při vytváření snímku.

- Účet úložiště účtuje poplatky za jedinečné bloky nebo stránky, ať už jsou v objektu blob nebo ve snímku. Váš účet neúčtuje další poplatky za snímky spojené s objektem blob, dokud aktualizovat objekt blob, na kterém jsou založeny. Po aktualizaci základní hoblob, se liší od jeho snímky. V takovém případě se vám budou účtovat jedinečné bloky nebo stránky v každém objektu blob nebo snímku.
- Když nahradíte blok v objektu blob bloku, tento blok se následně nabije jako jedinečný blok. To platí i v případě, že blok má stejné ID bloku a stejná data jako ve snímku. Poté, co je blok znovu potvrzen, liší se od svého protějšku v libovolném snímku a bude vám účtována za jeho data. Totéž platí pro stránku v objektu blob stránky, který je aktualizován s identickými daty.
- Nahrazení objektu blob bloku voláním [Metody UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream]nebo [UploadFromByteArray][dotnet_UploadFromByteArray] nahradí všechny bloky v objektu blob. Pokud máte snímek přidružený k tomuto objektu blob, všechny bloky v základním objektu blob a snímek se nyní rozcházejí a bude se vám účtovat za všechny bloky v obou objektech blob. To platí i v případě, že data v základní objekt blob a snímek zůstávají identické.
- Služba Azure Blob nemá prostředky k určení, zda dva bloky obsahují stejná data. Každý blok, který je odeslán a potvrzen, je považován za jedinečný, i když má stejná data a stejné ID bloku. Vzhledem k tomu, že poplatky narůstají za jedinečné bloky, je důležité vzít v úvahu, že aktualizace objektu blob, který má snímek, má za následek další jedinečné bloky a další poplatky.

### <a name="minimize-cost-with-snapshot-management"></a>Minimalizace nákladů díky správě snímků

Doporučujeme pečlivě spravovat snímky, abyste se vyhnuli dalším poplatkům. Podle těchto doporučených postupů můžete minimalizovat náklady vzniklé v souvislosti s úložištěm snímků:

- Odstraňte a znovu vytvořte snímky přidružené k objektu blob při každé aktualizaci objektu blob, i když aktualizujete s identickými daty, pokud návrh aplikace nevyžaduje, abyste udržovali snímky. Odstraněním a opětovným vytvořením snímků objektu blob můžete zajistit, aby se objekt blob a snímky nelišily.
- Pokud udržujete snímky pro objekt blob, vyhněte se volání [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream]nebo [UploadFromByteArray][dotnet_UploadFromByteArray] aktualizovat objekt blob. Tyto metody nahradit všechny bloky v objektu blob, způsobuje základní objekt blob a jeho snímky výrazně rozcházejí. Místo toho aktualizujte co nejmenší počet bloků pomocí [PutBlock][dotnet_PutBlock] a [PutBlockList][dotnet_PutBlockList] metody.

### <a name="snapshot-billing-scenarios"></a>Scénáře fakturace snímků

Následující scénáře ukazují, jak se účtuje poplatky za objekt blob bloku a jeho snímky.

#### <a name="scenario-1"></a>Scénář 1

Ve scénáři 1 základní objekt blob nebyl aktualizován po pořízení snímku, takže poplatky jsou účtovány pouze pro jedinečné bloky 1, 2 a 3.

![Prostředky Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>Scénář 2

Ve scénáři 2 byl aktualizován základní objekt blob, ale snímek nebyl. Blok 3 byl aktualizován a přestože obsahuje stejná data a stejné ID, není stejný jako blok 3 ve snímku. V důsledku toho je účet účtován za čtyři bloky.

![Prostředky Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>Scénář 3

Ve scénáři 3 byl aktualizován základní objekt blob, ale snímek nebyl. Blok 3 byl nahrazen blokem 4 v základním objektu blob, ale snímek stále odráží blok 3. V důsledku toho je účet účtován za čtyři bloky.

![Prostředky Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>Scénář 4

Ve scénáři 4 byl základní objekt blob kompletně aktualizován a neobsahuje žádný z původních bloků. V důsledku toho je účet účtován za všech osm jedinečných bloků. K tomuto scénáři může dojít, pokud používáte metodu aktualizace, jako je [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream]nebo [UploadFromByteArray][dotnet_UploadFromByteArray], protože tyto metody nahradit veškerý obsah objektu blob.

![Prostředky Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Další kroky

- Další informace o práci se snímky disků virtuálních počítačů (VM) najdete v [části Zálohování nespravovaných disků virtuálních počítačů Azure s přírůstkovými snímky](../../virtual-machines/windows/incremental-snapshots.md)

- Další příklady kódu pomocí úložiště objektů Blob najdete v tématu [Ukázky kódu Azure](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob). Můžete si stáhnout ukázkovou aplikaci a spustit ji, nebo procházet kód na GitHubu.

[dotnet_AccessCondition]: https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.accesscondition
[dotnet_CloudBlockBlob]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob.cloudblockblob
[dotnet_CreateSnapshotAsync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.createsnapshotasync
[dotnet_HTTPStatusCode]: https://docs.microsoft.com/java/api/com.microsoft.store.partnercenter.network.httpstatuscode
[dotnet_PutBlockList]: /dotnet/api/microsoft.azure.storage.blob.cloudblockblob.putblocklist
[dotnet_PutBlock]: /dotnet/api/microsoft.azure.storage.blob.cloudblockblob.putblock
[dotnet_UploadFromByteArray]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob.cloudblob.uploadfrombytearray
[dotnet_UploadFromFile]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob.cloudblob.uploadfromfile
[dotnet_UploadFromStream]: /dotnet/api/microsoft.azure.storage.blob.cloudappendblob.uploadfromstream
[dotnet_UploadText]: /dotnet/api/microsoft.azure.storage.blob.cloudappendblob.uploadtext
