---
title: Vytvořit snímek jen pro čtení objektu blob ve službě Azure Storage | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit snímek objektu blob k zálohování dat objektů blob v daném okamžiku v čase. Zjistěte, jak se účtují snímky a jak pomocí nich můžete minimalizovat náklady na kapacitu.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/06/2018
ms.author: tamram
ms.component: blobs
ms.openlocfilehash: 6fa223ffcbc70b2f17649645df3daed22746edd0
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2018
ms.locfileid: "39264027"
---
# <a name="create-a-blob-snapshot"></a>Vytvoření snímku objektu blob

Snímek je verze jen pro čtení objektu blob, která je provedena v bodě v čase. Snímky jsou užitečné pro zálohování objekty BLOB. Po vytvoření snímku, může číst, zkopírujte nebo odstraňte ji, ale už ho nelze změnit.

Snímek objektu blob je stejný jako jeho základní objekt blob, s tím rozdílem, že má identifikátor URI objektu blob **data a času** hodnotu připojí k identifikátoru URI označuje čas pořízení snímku objektu blob. Například, pokud identifikátor URI objektu blob stránky je `http://storagesample.core.blob.windows.net/mydrives/myvhd`, snímku identifikátoru URI je podobný `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`.

> [!NOTE]
> Přístup ke všem snímkům sdílet identifikátor URI základního objektu blob. Jediný rozdíl mezi základní objekt blob a snímek je připojený **data a času** hodnotu.
>

Objekt blob může mít libovolný počet snímků. Snímky budou zachovány, dokud sami výslovně neodstraníte. Snímek nemůže něj jeho základní objekt blob. Můžete zobrazit výčet snímků přidružených k základní objekt blob ke sledování aktuálního snímky.

Při vytváření snímku objektu blob, systémové vlastnosti objektu blob se zkopírují do snímku se stejnými hodnotami. Metadata základního objektu blob je také zkopírován do snímku, pokud neurčíte samostatné metadat pro snímek po jeho vytvoření.

Žádné zapůjčení přidružené k základní objekt blob nemají vliv na snímku. Nelze získat zapůjčení na snímek.

Soubor virtuálního pevného disku se používá k ukládání aktuální informace a stav disku virtuálního počítače. Odpojení disku od virtuálního nebo vypnout virtuální počítač a potom pořídit snímek jeho soubor virtuálního pevného disku. Tento soubor snímku můžete použít později k načtení souboru virtuálního pevného disku v tomto okamžiku v čase a znovu vytvořte virtuální počítač.

## <a name="create-a-snapshot"></a>Vytvoření snímku
Následující příklad kódu ukazuje, jak vytvořit snímek pomocí [Klientská knihovna Azure Storage pro .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). Tento příklad určuje další metadata pro snímek při jeho vytvoření.

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
        // Specify metadata at the time that the snapshot is created to specify unique metadata for the snapshot.
        // If no metadata is specified when the snapshot is created, the base blob's metadata is copied to the snapshot.
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

## <a name="copy-snapshots"></a>Kopie snímků
Operace kopírování týkajících se snímky objektů BLOB a postupovat podle těchto pravidel:

* Můžete zkopírovat snímek nad jeho základní objekt blob. Zvyšuje se úroveň snímku na pozici základní objekt blob, můžete obnovit starší verzi objektu blob. Snímek zůstane, ale základní objekt blob je přepsat zapisovatelné kopie snímku.
* Můžete zkopírovat snímek do cílového objektu blob s jiným názvem. Výsledný cílový objekt blob je zapisovatelný objekt blob a ne snímku.
* Když se zkopíruje zdrojový objekt blob, všechny snímky zdrojový objekt blob nejsou zkopírovat do cíle. Když kopie se přepsat cílový objekt blob, všechny snímky přidružené k původní cílový objekt blob zůstanou beze změny.
* Při vytváření snímku objektu blob bloku, objektu blob potvrzené blokovaných webů. je také zkopírován do snímku. Všechny nepotvrzené bloků nejsou zkopírovány.

## <a name="specify-an-access-condition"></a>Zadejte podmínku přístup
Při volání [CreateSnapshotAsync][dotnet_CreateSnapshotAsync], určíte podmínku přístup tak, aby snímku se vytvoří pouze v případě, že je některá podmínka splněná. Chcete-li určit podmínku přístup, použijte [AccessCondition] [ dotnet_AccessCondition] parametru. Pokud se zadaná podmínka není splněna, není vytvořen snímek a službu Blob service vrátí stavový kód [HTTPStatusCode][dotnet_HTTPStatusCode]. PreconditionFailed.

## <a name="delete-snapshots"></a>Odstranit snímky
Nelze odstranit objekt blob se snímky, pokud snímky budou také odstraněny. Můžete odstranit snímek jednotlivě nebo určit, že všechny snímky se odstraní při odstranění zdrojového objektu blob. Při pokusu odstranit objekt blob, který má snímky stále, bude výsledkem chyba.

Následující příklad kódu ukazuje, jak odstranit objekt blob a jeho snímky v .NET, ve kterém `blockBlob` je objekt typu [CloudBlockBlob][dotnet_CloudBlockBlob]:

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

## <a name="snapshots-with-azure-premium-storage"></a>Snímky se službou Azure Premium Storage
Při použití snímků díky službě Premium Storage, platí následující pravidla:

* Maximální počet snímků na objekt blob stránky v účtu služby premium storage je 100. Pokud je tento limit překročen, operace vytvoření snímku objektu Blob vrátí kód chyby: 409 (`SnapshotCountExceeded`).
* Udělat snímek objektu blob stránky v účtu služby premium storage každých 10 minut. Pokud dojde k překročení tohoto kurzu, operace vytvoření snímku objektu Blob vrátí kód chyby: 409 (`SnapshotOperationRateExceeded`).
* Další snímek, můžete operaci objekt Blob kopírování ke zkopírování snímku do jiného objektů blob stránky v účtu. Cílový objekt blob pro operaci kopírování nesmí mít všechny existující snímky. Pokud cílový objekt blob má snímky, pak objekt Blob kopírování operace vrátí kód chyby: 409 (`SnapshotsPresent`).

## <a name="return-the-absolute-uri-to-a-snapshot"></a>Vrátí absolutní identifikátor URI na snímek
Tento příklad kódu jazyka C# vytvoří snímek a zapíše absolutní identifikátor URI pro primární umístění.

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

## <a name="understand-how-snapshots-accrue-charges"></a>Vysvětlení, jak snímky nechat nabíhat poplatky
Vytváří se snímek, který je jen pro čtení kopírování objektu blob, může způsobit poplatky za úložiště další data k vašemu účtu. Při návrhu aplikace, je důležité vědět, jak může nabíhají tyto poplatky, tak, aby měli minimalizovat náklady.

### <a name="important-billing-considerations"></a>Důležité informace o fakturaci
Následující seznam obsahuje klíčové body, které je třeba zvážit při vytváření snímku.

* Účtu úložiště se neúčtují poplatky za jedinečné bloky a stránky, ať už jsou v objektu blob nebo ve snímku. Váš účet není účtovat další poplatky za snímky přidružené tomuto objektu blob až do aktualizace objektů blob, na kterém jsou založeny. Po aktualizaci základní objekt blob diverges z jeho snímků. Pokud k tomu dojde, bude vám účtována jedinečné bloky nebo stránky v jednotlivých objektů blob nebo snímku.
* Po nahrazení blok v rámci objektu blob bloku, účtuje se jako jedinečný blok následně tohoto bloku. To platí i v případě, že blok má stejné ID bloku a stejná data, je na něm ve snímku. Po bloku usiluje o opět ho diverges z jeho protějšek v libovolný snímek a vám bude účtovat svá data. To samé platí pro stránku v objekt blob stránky, která se aktualizuje s stejná data.
* Nahrazení objektu blob bloku voláním [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream] [ dotnet_UploadFromStream], nebo [UploadFromByteArray] [ dotnet_UploadFromByteArray] metoda nahradí všechny bloky v objektu blob. Pokud máte snímek přidružené tohoto objektu blob, všechny bloky v základní objekt blob a snímek nyní odchýlit a vám budou účtovat všechny bloky v obou objekty BLOB. To platí i v případě, že data na základní objekt blob a snímek zůstávají stejné.
* Služba Azure Blob service nemá žádné prostředky k určení, zda dva bloky obsahují stejné údaje. Každý blok, který je nahraný a potvrzené je považován za jedinečný, i když má stejná data a stejné ID bloku. Poplatky se účtují za jedinečné bloky, proto je důležité vzít v úvahu informující o probíhající aktualizaci objektu blob, který má výsledky snímku v další jedinečné bloky a další poplatky.

### <a name="minimize-cost-with-snapshot-management"></a>Minimalizace nákladů s Správa snímku

Doporučujeme vám Správa snímků pečlivě vyhnout dalším poplatkům. Můžete postupovat podle těchto osvědčených postupů a tím pomáhá minimalizovat náklady na úložiště vašeho snímky:

* Odstranit a znovu vytvořit snímky přidružené tomuto objektu blob pokaždé, když se aktualizovat objekt blob, i v případě, že aktualizujete daty shodné, není-li návrhu aplikace vyžaduje, že udržujete snímky. Tím odstraníte a znovu vytvoříte snímky objektu blob, můžete zajistit, že není odchýlit objektů blob a snímky.
* Pokud udržujete snímky pro objekt blob, vyhněte se volání [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [ UploadFromStream][dotnet_UploadFromStream], nebo [UploadFromByteArray] [ dotnet_UploadFromByteArray] aktualizovat objekt blob. Tyto metody nahradí všechny bloky v objektu blob, způsobil základní objekt blob a jeho snímky, chcete-li odchýlit výrazně. Místo toho aktualizovat pomocí nejmenšího počtu bloků [PutBlock] [ dotnet_PutBlock] a [PutBlockList] [ dotnet_PutBlockList] metody.

### <a name="snapshot-billing-scenarios"></a>Snímek fakturace scénáře
Následující scénáře ukazují, jak nabíhají poplatky za objekt blob bloku a její snímky.

**Scénář 1**

Ve scénáři 1 základní objekt blob se neaktualizoval po pořízení snímku, takže pouze pro jedinečné bloky 1, 2 a 3 budou účtovat poplatky.

![Prostředky Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

**Scénář 2**

Ve scénáři 2 základní objekt blob se aktualizovala, ale snímku nevypršela. Blok 3 byla aktualizována a přestože obsahuje stejná data a stejné ID, není stejný jako blokovat 3 ve snímku. V důsledku toho účet se budou účtovat čtyři bloky.

![Prostředky Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

**Scénář 3**

Ve scénáři 3 základní objekt blob se aktualizovala, ale snímku nevypršela. Blok 3 byla nahrazena bloku 4 v základní objekt blob, ale snímek odráží bloku 3. V důsledku toho účet se budou účtovat čtyři bloky.

![Prostředky Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

**Scénář 4**

V případě 4 základní objekt blob se úplně aktualizovala a obsahuje jeden z jeho původní bloků. V důsledku toho účet se budou účtovat všechny bloky osm jedinečný. Tato situace může nastat, pokud používáte metodu aktualizace, jako [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [ UploadFromStream][dotnet_UploadFromStream], nebo [UploadFromByteArray][dotnet_UploadFromByteArray], protože tyto metody nahradí veškerý obsah objektu blob.

![Prostředky Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Další postup

* Další informace o práci se snímky disků virtuálních počítačů (VM) v [zálohování Azure nespravované disky virtuálních počítačů s přírůstkovými snímky](../../virtual-machines/windows/incremental-snapshots.md)

* Další příklady použití Blob storage, najdete v části [vzorových kódů Azure](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob). Stažení ukázkové aplikace a spusťte ho nebo procházení kódu na Githubu.

[dotnet_AccessCondition]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.accesscondition.aspx
[dotnet_CloudBlockBlob]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.aspx
[dotnet_CreateSnapshotAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.createsnapshotasync.aspx
[dotnet_HTTPStatusCode]: https://msdn.microsoft.com/library/system.net.httpstatuscode(v=vs.110).aspx
[dotnet_PutBlockList]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.putblocklist.aspx
[dotnet_PutBlock]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.putblock.aspx
[dotnet_UploadFromByteArray]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfrombytearray.aspx
[dotnet_UploadFromFile]: https://msdn.microsoft.com/library/azure/mt705654.aspx
[dotnet_UploadFromStream]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstream.aspx
[dotnet_UploadText]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.uploadtext.aspx