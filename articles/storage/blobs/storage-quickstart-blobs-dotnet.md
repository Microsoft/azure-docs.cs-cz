---
title: "Rychlý start Azure – Nahrávání, stahování a výpis objektů blob v Azure Storage pomocí .NET | Microsoft Docs"
description: "V tomto rychlém startu vytvoříte účet úložiště a kontejner. Pak použijete klientskou knihovnu pro úložiště pro .NET k nahrání objektu blob do služby Azure Storage, stažení objektu blob a výpisu objektů blob v kontejneru."
services: storage
author: tamram
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 02/22/2018
ms.author: tamram
ms.openlocfilehash: 265691ff189c628156f234083645a4b2ca4b637b
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-net"></a>Rychlý start: Nahrávání, stahování a výpis objektů blob pomocí .NET

V tomto rychlém startu zjistíte, jak pomocí .NET nahrávat, stahovat a vypisovat objekty blob bloku v kontejneru v úložišti objektů blob v Azure.

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu Rychlý start je potřeba:

* instalace .NET core 2.0 pro [Linux](/dotnet/core/linux-prerequisites?tabs=netcore2x) nebo [Windows](/dotnet/core/windows-prerequisites?tabs=netcore2x)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Stažení ukázkové aplikace

Ukázková aplikace použitá v tomto rychlém startu je základní konzolová aplikace. 

Pomocí [gitu](https://git-scm.com/) stáhněte kopii aplikace do svého vývojového prostředí. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart.git
```

Tento příkaz naklonuje úložiště do vaší místní složky gitu. Pokud chcete otevřít řešení sady Visual Studio, vyhledejte složku storage-blobs-dotnet-quickstart, otevřete ji a dvakrát klikněte na soubor storage-blobs-dotnet-quickstart.sln. 

## <a name="configure-your-storage-connection-string"></a>Konfigurace připojovacího řetězce úložiště

V aplikaci je potřeba zadat připojovací řetězec pro váš účet úložiště. Doporučujeme uložit tento připojovací řetězec do proměnné prostředí v místním počítači, na kterém aplikaci spouštíte. V závislosti na operačním systém vytvořte proměnnou prostředí pomocí jednoho z následujících příkladů.  Nahraďte \<yourconnectionstring\> vaším připojovacím řetězcem.

### <a name="linux"></a>Linux

```bash
export storageconnectionstring=<yourconnectionstring>
```
### <a name="windows"></a>Windows

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

## <a name="run-the-sample"></a>Spuštění ukázky

Tato ukázka vytvoří testovací soubor ve složce Dokumenty, nahraje ho do úložiště objektů blob, vypíše objekty blob v kontejneru a pak soubor stáhne s novým názvem, abyste mohli porovnat starý a nový soubor. 

Přejděte do adresáře aplikace a spusťte aplikaci příkazem `dotnet run`.

```
dotnet run
```

Výstup se podobá následujícímu příkladu:

```
Azure Blob storage quick start sample
Temp file = /home/admin/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt
Uploading to Blob storage as blob 'QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt'
List blobs in container.
https://mystorageaccount.blob.core.windows.net/quickstartblobs/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt
Downloading blob to /home/admin/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374_DOWNLOADED.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.
```

Když budete pokračovat stisknutím libovolné klávesy, odstraní se kontejner úložiště i soubory. Než budete pokračovat, zkontrolujte, jestli složka Dokumenty obsahuje příslušné dva soubory. Můžete je otevřít a podívat se, že jsou identické. Obsah souboru v úložišti objektů blob můžete zobrazit zkopírováním adresy URL objektu blob z okna konzoly a jejím vložením do prohlížeče.

K zobrazení souborů v úložišti objektů blob můžete použít také nástroj, jako je [Průzkumník služby Azure Storage](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Průzkumník služby Azure Storage je bezplatný nástroj pro více platforem, který umožňuje přístup k informacím o účtu úložiště. 

Po ověření souborů stiskněte libovolnou klávesu a dokončete ukázku a odstraňte testovací soubory. Když teď víte, co ukázka dělá, otevřete soubor Program.cs a prohlédněte si kód. 

## <a name="understand-the-sample-code"></a>Vysvětlení vzorového kódu

Dále si projdeme vzorový kód, abyste pochopili, jak funguje.

### <a name="get-references-to-the-storage-objects"></a>Získání odkazů na objekty úložiště

První věc, kterou je potřeba udělat, je vytvořit odkazy na objekty sloužící k přístupu k úložišti objektů blob a jeho správě. Tyto objekty se vzájemně využívají a každý z nich je využívaný dalším objektem v seznamu.

* Vytvořte instanci objektu [CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet), která odkazuje na účet úložiště.

* Vytvořte instanci objektu [CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet), která odkazuje na službu Blob ve vašem účtu úložiště.

* Vytvořte instanci objektu [CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet), která představuje kontejner, ke kterému přistupujete. Kontejnery slouží k uspořádání objektů blob podobně jako složky na počítači k uspořádání souborů.

Jakmile budete mít [CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet), můžete vytvořit instanci objektu [CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet), která odkazuje na konkrétní objekt blob, který vás zajímá, a provést operaci nahrání, stažení, kopírování atd.

> [!IMPORTANT]
> Názvy kontejnerů musí být malými písmeny. Další informace o pojmenování kontejnerů a objektů blob najdete v tématu [Názvy kontejnerů, objektů blob a metadat a odkazování na ně](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

V této části vytvoříte instance objektů, vytvoříte nový kontejner a pak nastavíte oprávnění ke kontejneru tak, aby objekty blob byly veřejné a přístupné přes pouhou adresu URL. Kontejner má název **quickstartblobs**.

Tento příklad používá [CreateIfNotExists](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.createifnotexists?view=azure-dotnet), protože chceme při každém spuštění ukázky vytvořit nový kontejner. V produkčním prostředí, kde používáte stejný kontejner v celé aplikaci, je lepší volat **CreateIfNotExists** pouze jednou. Případně můžete kontejner vytvořit předem, abyste ho nemuseli vytvářet v kódu.

```csharp
// Load the connection string for use with the application. The storage connection string is stored
// in an environment variable on the machine running the application called storageconnectionstring.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring");
if (storageConnectionString == null)
{
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add a environment variable name 'storageconnectionstring' with the actual storage " +
        "connection string as a value.");
}
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);

// Create the CloudBlobClient that is used to call the Blob Service for that storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs'. 
cloudBlobContainer = cloudBlobClient.GetContainerReference("quickstartblobs" + Guid.NewGuid().ToString());
await cloudBlobContainer.CreateIfNotExistsAsync();

// Set the permissions so the blobs are public. 
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-the-container"></a>Nahrání objektů blob do kontejneru

Úložiště objektů blob podporuje objekty blob bloku, doplňovací objekty blob a objekty blob stránky. Nejčastěji používané jsou objekty blob bloku, které se používají také v tomto rychlém startu.

Pokud chcete do objektu blob nahrát soubor, získejte odkaz na objekt blob v cílovém kontejneru. Jakmile budete mít tento odkaz na objekt blob, můžete do něj nahrát data pomocí [CloudBlockBlob.UploadFromFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromfileasync). Tato operace vytvoří objekt blob, pokud ještě neexistuje, nebo ho přepíše, pokud už existuje.

Vzorový kód vytvoří místní soubor, který se použije k nahrání a stažení. Soubor k nahrání uloží do proměnné **fileAndPath** a název objektu blob do proměnné **localFileName**. Následující příklad nahraje soubor do kontejneru **quickstartblobs**.

```csharp
// Create a file in MyDocuments to test the upload and download.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
sourceFile = Path.Combine(localPath, localFileName);
// Write text to the file.
File.WriteAllText(sourceFile, "Hello, World!");

Console.WriteLine("Temp file = {0}", sourceFile);
Console.WriteLine("Uploading to Blob storage as blob '{0}'", localFileName);

// Get a reference to the location where the blob is going to go, then upload the file.
// Upload the file you created, use localFileName for the blob name.
CloudBlockBlob cloudBlockBlob = cloudBlobContainer.GetBlockBlobReference(localFileName);
await cloudBlockBlob.UploadFromFileAsync(sourceFile);
```

V případě úložiště objektů blob můžete k nahrání použít několik metod. Například pokud máte paměťový proud, můžete místo UploadFromFileAsync použít metodu UploadFromStreamAsync.

Objekty blob bloku můžou být jakýmkoli typem textového nebo binárního souboru. Objekty blob stránky se používají hlavně pro soubory VHD využívané virtuálními počítači IaaS. Doplňovací objekty blob se používají k protokolování, například když chcete zapisovat do souboru a pak přidávat další informace. Většina objektů blob ukládaných do úložiště jsou typu blok.

### <a name="list-the-blobs-in-a-container"></a>Zobrazí seznam objektů blob v kontejneru

Seznam souborů v kontejneru můžete získat pomocí [CloudBlobContainer.ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync). Následující kód načte seznam objektů blob, pak je ve smyčce projde a zobrazí identifikátory URI nalezených objektů blob. Soubor můžete zobrazit zkopírováním identifikátoru URI z příkazového okna a jeho vložením do prohlížeče.

Pokud máte v kontejneru 5 000 nebo méně objektů blob, všechny názvy objektů blob se načtou jedním zavoláním ListBlobsSegmentedAsync. Pokud máte v kontejneru více než 5 000 objektů blob, služba načítá seznam ve skupinách po 5 000, dokud se nenačtou názvy všech objektů blob. Takže při prvním volání rozhraní API se vrátí názvy prvních 5 000 objektů blob a token pro pokračování. Při druhém volání zadáte token a služba načte další sadu názvů objektů blob a tak dále, dokud token pro pokračování nemá hodnotu null, která indikuje, že se už načetly všechny názvy objektů blob.

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    blobContinuationToken = results.ContinuationToken;
    foreach (IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
    blobContinuationToken = results.ContinuationToken;
} while (blobContinuationToken != null);

```

### <a name="download-blobs"></a>Stáhnout objekty blob

Stáhněte všechny objekty blob na místní disk pomocí [CloudBlob.DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync).

Následující kód stáhne objekt blob nahraný v předchozí části a k názvu objektu blob přidá příponu „_DOWNLOADED“, takže na místním disku uvidíte oba soubory.

```csharp
// Download blob. In most cases, you would have to retrieve the reference
//   to cloudBlockBlob here. However, we created that reference earlier, and 
//   haven't changed the blob we're interested in, so we can reuse it. 
// First, add a _DOWNLOADED before the .txt so you can see both files in MyDocuments.
destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);  
```

### <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete objekty blob nahrané v rámci tohoto rychlého startu, můžete celý kontejner odstranit pomocí [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteasync). Odstraňte také vytvořené soubory, pokud je už nepotřebujete.

```csharp
Console.WriteLine("Press the 'Enter' key to delete the sample files, example container, and exit the application.");
Console.ReadLine();
// Clean up resources. This includes the container and the two temp files.
Console.WriteLine("Deleting the container");
if (cloudBlobContainer != null)
{
    await cloudBlobContainer.DeleteIfExistsAsync();
}
Console.WriteLine("Deleting the source, and downloaded files");
File.Delete(sourceFile);
File.Delete(destinationFile);
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak přenášet soubory mezi místním diskem a úložištěm objektů blob v Azure pomocí .NET. Další informace o práci s úložištěm objektů blob najdete v postupech pro úložiště objektů blob.

> [!div class="nextstepaction"]
> [Operace s úložištěm objektů blob – postupy](storage-dotnet-how-to-use-blobs.md)

Další vzorové kódy pro Azure Storage, které si můžete stáhnout a použít, najdete v seznamu [ukázek pro Azure Storage s použitím .NET](../common/storage-samples-dotnet.md).

Další informace o Průzkumníku služby Storage a objektech blob najdete v tématu [Správa prostředků úložiště objektů blob v Azure pomocí Průzkumníka služby Storage](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
