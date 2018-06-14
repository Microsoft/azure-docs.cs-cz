---
title: Vytvoření snímku jen pro čtení objektu blob ve službě Azure Storage | Microsoft Docs
description: Naučte se vytvořit snímek objektů blob k zálohování dat objektů blob v daném okamžiku v čase. Pochopit, jak se účtují snímky a jak pomocí nich můžete minimalizovat náklady na kapacitu.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/06/2018
ms.author: tamram
ms.openlocfilehash: 1a27dfd61850d9dfa1f232eacf7f09d66202cafe
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
ms.locfileid: "29852927"
---
# <a name="create-a-blob-snapshot"></a>Vytvoření snímku objektu blob

Snímek je jen pro čtení verze objektu blob, který se pořídí na bod v čase. Snímky jsou užitečné pro zálohování objekty BLOB. Po vytvoření snímku, číst, kopírovat nebo odstranit, ale nelze je změnit.

Snímek objektu blob je stejný jako jeho základní objekt blob, s tím rozdílem, že je identifikátor URI objektu blob **data a času** hodnota připojí k objektu blob identifikátor URI, který označuje datum a čas, kdy pořízení snímku. Například, pokud na stránce blob identifikátor URI je `http://storagesample.core.blob.windows.net/mydrives/myvhd`, snímku identifikátor URI je podobná `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`.

> [!NOTE]
> Všechny snímky sdílet identifikátor URI základní objektu blob. Mezi základní objekt blob a snímku jenom rozdíl je připojených **data a času** hodnotu.
>

Objekt blob může mít libovolný počet snímků. Snímky uchová, dokud explicitně odstranit. Snímek nemůže outlive jeho základní objekt blob. Můžete vytvořit výčet snímků přidružených základní objekt blob sledovat vaše aktuální snímky.

Při vytváření snímku objektu blob vlastnosti systému objektu blob se zkopírují do snímku se stejnými hodnotami. Metadata základní objektu blob je také zkopírován do snímku, pokud nezadáte samostatné metadata pro snímek při jeho vytvoření.

Všechny zapůjčení přidružené základní objekt blob neovlivňují snímku. Nelze získat zapůjčení na snímku.

Soubor virtuálního pevného disku se používá k uložení aktuální informace a stav pro disk virtuálního počítače. Můžete odpojit disk z virtuálního počítače nebo vypněte virtuální počítač a potom pořízení snímku jeho souboru virtuálního pevného disku. Tento soubor snímku můžete použít později k načtení souboru virtuálního pevného disku v tomto bodě v čase a znovu vytvořte virtuální počítač.

## <a name="create-a-snapshot"></a>Vytvoření snímku
Následující příklad kódu ukazuje, jak vytvořit snímek pomocí [Klientská knihovna pro úložiště Azure pro .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). Tento příklad určuje další metadata pro snímek, když je vytvořeno.

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
Operace kopírování zahrnující objekty BLOB a snímky postupujte podle těchto pravidel:

* Snímek můžete zkopírovat přes jeho základní objekt blob. Povýšení snímku na pozici základní objekt blob, můžete obnovit dřívější verzi objektu blob. Snímek zůstane, ale základní dojde k přepsání objektů blob s možností zápisu kopie snímků.
* Snímek můžete zkopírovat do cílového objektu blob s jiným názvem. Výsledný cílový objekt blob je s možností zápisu objektů blob a není snímek.
* Po zkopírování zdrojový objekt blob nebudou zkopírovány všechny snímky zdrojový objekt blob do cílového umístění. Pokud cílový objekt blob je přepsat kopie, všechny snímky přidružené k původní cílový objekt blob zůstanou beze změn.
* Při vytváření snímku objekt blob bloku potvrdit blokovaných objektu blob je také zkopírován do snímku. Všechny nepotvrzené bloky nebudou zkopírovány.

## <a name="specify-an-access-condition"></a>Zadejte podmínku přístup
Při volání [CreateSnapshotAsync][dotnet_CreateSnapshotAsync], můžete zadat podmínku přístup, tak, aby snímku se vytvoří pouze v případě, že je splněna podmínka. Pokud chcete zadat podmínku přístup, použijte [AccessCondition] [ dotnet_AccessCondition] parametr. Pokud není splněna zadaná podmínka, není-li vytvořit snímek a služby objektů Blob vrátí stavový kód [HTTPStatusCode][dotnet_HTTPStatusCode]. PreconditionFailed.

## <a name="delete-snapshots"></a>Odstraňte snímky
Objekt blob se snímky nelze odstranit, pokud budou odstraněny také tyto snímky. Lze odstranit snímek jednotlivě, nebo určit, že všechny snímky odstranit, pokud je zdrojový objekt blob se odstraní. Pokud se pokusíte odstranit objekt blob, který má stále snímky, bude výsledkem chyba.

Následující příklad kódu ukazuje, jak odstranit objekt blob a jeho snímky v rozhraní .NET, kde `blockBlob` je objekt typu [CloudBlockBlob][dotnet_CloudBlockBlob]:

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

## <a name="snapshots-with-azure-premium-storage"></a>Snímky s Azure Premium Storage
Při použití snímků Storage úrovně Premium, platí následující pravidla:

* Maximální počet snímků za objekt blob stránky v účtu úložiště premium je 100. Pokud je tento limit překročen, operace snímku Blob vrátí kód chyby 409 (`SnapshotCountExceeded`).
* Snímek objekt blob stránky může trvat v účtu úložiště premium každých 10 minut. Pokud dojde k překročení tohoto kurzu, objektů Blob snímku operaci vrátí kód chyby 409 (`SnapshotOperationRateExceeded`).
* Číst snímku, můžete kopírovat objekt Blob operaci kopírování snímku na jiný objekt blob stránky v účtu. Cílový objekt blob pro kopírování nesmí mít všechny existující snímky. Pokud cílový objekt blob má snímky a pak operaci kopírování objektu Blob vrátí kód chyby 409 (`SnapshotsPresent`).

## <a name="return-the-absolute-uri-to-a-snapshot"></a>Vrátí absolutní identifikátor URI na snímek
Tento příklad kódu C# vytvoří snímek a zapisuje na absolutní identifikátor URI pro primární umístění.

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

## <a name="understand-how-snapshots-accrue-charges"></a>Pochopit, jak snímky nabíhat poplatky
Vytvoření snímku, která je jen pro čtení kopie objektu blob, může způsobit další data poplatky za úložiště k vašemu účtu. Při navrhování vaší aplikace, je důležité si uvědomit o tom, jak může tyto poplatky nabíhat tak, aby můžete minimalizovat náklady.

### <a name="important-billing-considerations"></a>Důležité aspekty fakturace
Následující seznam obsahuje klíčové body, které je třeba zvážit při vytváření snímku.

* Váš účet úložiště způsobuje poplatky za jedinečný bloky nebo stránky, ať už jsou v objektu blob nebo ve snímku. Váš účet není způsobí nárůst nákladů pro snímky přidružený objekt blob, dokud neaktualizujete objektů blob, na kterém jsou založena. Po dokončení aktualizace základní objekt blob se diverges z jeho snímků. V takovém případě vám budou účtovat jedinečný bloky nebo stránky v jednotlivých objektů blob nebo snímek.
* Když nahradíte blok v rámci objekt blob bloku, tomto bloku je následně účtovat jako blok jedinečný. To platí i v případě, že bloku má stejné ID bloku a stejná data, protože má ve snímku. Po blok je potvrzená znovu ji diverges od jeho protějšku v jakékoli snímek a vám bude účtována pro svoje data. To samé platí pro stránku v objekt blob stránky, který se aktualizuje identické daty.
* Nahrazení objekt blob bloku voláním [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream], nebo [UploadFromByteArray] [ dotnet_UploadFromByteArray] metoda nahrazuje všechny bloky v objektu blob. Pokud máte snímek přidružené k tomuto objektu blob, teď odchýlit v základní objekt blob a snímek všech bloků a vám bude účtována pro všechny bloky v obou objekty BLOB. To platí i v případě, že data v základní objekt blob a snímku zůstávají stejné.
* Služba objektů Blob v Azure nemá prostředek k určení, zda dva bloky obsahují stejné údaje. Každý blok, který je odeslán a potvrzené považuje jako jedinečné, i když má stejná data a stejné ID bloku. Protože pro jedinečný bloky nabíhat poplatky, je důležité vzít v úvahu, aktualizován objekt blob, který má snímku výsledky v další jedinečné bloky a další poplatky.

### <a name="minimize-cost-with-snapshot-management"></a>Minimalizovat náklady s Správa snímků

Doporučujeme vám, správě vaší snímků pečlivě, aby se zabránilo další poplatky. Můžete postupovat podle těchto osvědčené postupy minimalizovat náklady na úložiště vaše snímky:

* Odstranit a znovu vytvořit snímky, které jsou přidružené k objektu blob při každé aktualizaci objektu blob, i když jsou aktualizace s stejné údaje, pokud váš návrh aplikace vyžaduje udržet si snímky. Odstranit a znovu vytváření snímků objektu blob, můžete zajistit, že není odchýlit objektů blob a snímky.
* Pokud udržujete snímků pro objekt blob, vyhněte se volání [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream], nebo [UploadFromByteArray] [ dotnet_UploadFromByteArray] k aktualizaci objektu blob. Tyto metody nahraďte všechny bloky v objektu blob, které způsobuje objektu blob služby základní a jeho snímky o odchylce výrazně. Místo toho aktualizovat pomocí nejmenšího počtu bloků [PutBlock] [ dotnet_PutBlock] a [PutBlockList] [ dotnet_PutBlockList] metody.

### <a name="snapshot-billing-scenarios"></a>Snímek fakturace scénáře
Následující scénáře ukazují, jak nabíhat poplatky pro objekt blob bloku a jeho snímků.

**Scénář 1**

Ve scénáři 1 základní objekt blob nebyla aktualizována po pořízení snímku, takže poplatky se vám neúčtují pouze pro jedinečný bloky 1, 2 a 3.

![Prostředky Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

**Scénář 2**

Ve scénáři 2 základní objekt blob se aktualizovalo, ale má snímku není. Blok 3 byla aktualizována, a to i v případě, že obsahuje stejná data a stejným ID, není stejný jako blokovat 3 ve snímku. Účet je v důsledku toho účtovat čtyři bloky.

![Prostředky Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

**Scénář 3**

Ve scénáři 3 základní objekt blob se aktualizovalo, ale má snímku není. Blok 3 byla nahrazena blok 4 v základní objektu blob, ale snímku odráží bloku 3. Účet je v důsledku toho účtovat čtyři bloky.

![Prostředky Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

**Scénář 4**

V případě 4 základní objekt blob se aktualizovala kompletně a obsahuje žádný z jeho původní bloky. Účet je v důsledku toho účtovat všechny bloky osm jedinečný. Tato situace může nastat, pokud používáte metodu aktualizace, jako [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream], nebo [UploadFromByteArray][dotnet_UploadFromByteArray], protože tyto metody nahradit veškerý obsah objektu blob.

![Prostředky Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Další postup

* Můžete najít další informace o práci s snímků disku virtuálního počítače (VM) v [zálohování Azure nespravované disky virtuálních počítačů s přírůstkové snímky](../../virtual-machines/windows/incremental-snapshots.md)

* Další příklady kódu pomocí úložiště objektů Blob, najdete v části [ukázky kódu Azure](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob). Můžete stáhnout ukázkovou aplikaci a potom ho spusťte nebo procházet kód na Githubu.

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