---
title: Rychlý start Azure – Vytvoření objektu blob v úložišti objektů pomocí .NET | Microsoft Docs
description: V tomto rychlém startu vytvoříte v úložišti objektů (blob) účet úložiště a kontejner. Pak použijete klientskou knihovnu pro úložiště pro .NET k nahrání objektu blob do služby Azure Storage, stažení objektu blob a výpisu objektů blob v kontejneru.
services: storage
author: tamram
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 04/09/2018
ms.author: tamram
ms.openlocfilehash: 7607643698cddb0cf5a113191ef638d36ab176bc
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-net"></a>Rychlý start: Nahrávání, stahování a výpis objektů blob pomocí .NET

V tomto rychlém startu zjistíte, jak pomocí .NET nahrávat, stahovat a vypisovat objekty blob bloku v kontejneru v úložišti objektů blob v Azure.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, nejprve na webu [Azure Portal](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM) vytvořte účet úložiště Azure. Nápovědu k vytvoření účtu najdete v tématu [Vytvoření účtu úložiště](../common/storage-quickstart-create-account.md).

Dále stáhněte a nainstalujte .NET Core 2.0 pro váš operační systém. Pokud používáte Windows, můžete nainstalovat sadu Visual Studio a použít .NET Framework, pokud chcete. Můžete také nainstalovat editor pro použití ve vašem operačním systému.

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

- Nainstalujte [.NET Core pro Windows](https://www.microsoft.com/net/download/windows) nebo [.NET Framework](https://www.microsoft.com/net/download/windows) (ten je součástí sady Visual Studio pro Windows).
- Nainstalujte sadu [Visual Studio pro Windows](https://www.visualstudio.com/). Pokud používáte .NET Core, instalace sady Visual Studio je volitelná.  

Informace o výběru mezi .NET Core a .NET Framework najdete v tématu [Výběr mezi .NET Core a .NET Framework pro serverové aplikace](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server).

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

- Nainstalujte [.NET Core pro Linux](https://www.microsoft.com/net/download/linux).
- Volitelně nainstalujte [Visual Studio Code](https://www.visualstudio.com/) a [rozšíření jazyka C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp&dotnetid=963890049.1518206068).

# <a name="macostabmacos"></a>[macOS](#tab/macos)

- Nainstalujte [.NET Core pro macOS](https://www.microsoft.com/net/download/macos).
- Volitelně nainstalujte sadu [Visual Studio pro Mac](https://www.visualstudio.com/vs/visual-studio-mac/).

---

## <a name="download-the-sample-application"></a>Stažení ukázkové aplikace

Ukázková aplikace použitá v tomto rychlém startu je základní konzolová aplikace. Ukázkovou aplikaci můžete prozkoumat na [GitHubu](https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart).

Pomocí [gitu](https://git-scm.com/) stáhněte kopii aplikace do svého vývojového prostředí. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart.git
```

Tento příkaz naklonuje úložiště do vaší místní složky gitu. Pokud chcete otevřít řešení sady Visual Studio, vyhledejte složku *storage-blobs-dotnet-quickstart*, otevřete ji a dvakrát klikněte na soubor *storage-blobs-dotnet-quickstart.sln*. 

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurace připojovacího řetězce úložiště

Pro spuštění aplikace je potřeba zadat připojovací řetězec pro váš účet úložiště. Ukázková aplikace načte připojovací řetězec z proměnné prostředí a použije ho k ověření požadavků ve službě Azure Storage.

Po zkopírování připojovacího řetězce ho zapište do nové proměnné prostředí na místním počítači, na kterém aplikaci spouštíte. Proměnnou prostředí nastavíte tak, že otevřete okno konzoly a budete postupovat podle pokynů pro váš operační systém. Nahraďte `<yourconnectionstring>` svým skutečným připojovacím řetězcem:

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

Po přidání proměnné prostředí možná bude nutné restartovat všechny spuštěné programy, které budou potřebovat číst tuto proměnnou prostředí, a to včetně okna konzoly. Pokud například jako editor používáte sadu Visual Studio, před spuštěním ukázky sadu Visual Studio restartujte. 

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export storageconnectionstring=<yourconnectionstring>
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source ~/.bashrc`, aby se změny projevily.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

Upravte svůj soubor .bash_profile a přidejte do něj proměnnou prostředí:

```bash
export STORAGE_CONNECTION_STRING=<yourconnectionstring>
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source .bash_profile`, aby se změny projevily.

---

## <a name="run-the-sample"></a>Spuštění ukázky

Tato ukázka vytvoří v místní složce **Dokumenty** testovací soubor a nahraje ho do úložiště objektů blob. Ukázka pak vypíše objekty blob v kontejneru a stáhne soubor s novým názvem, abyste mohli porovnat starý a nový soubor. 

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Pokud jako editor používáte sadu Visual Studio, můžete ukázku spustit stisknutím **F5**. 

Jinak, přejděte do adresáře aplikace a spusťte aplikaci příkazem `dotnet run`.

```
dotnet run
```

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Přejděte do adresáře aplikace a spusťte aplikaci příkazem `dotnet run`.

```
dotnet run
```

# <a name="macostabmacos"></a>[macOS](#tab/macos)

Přejděte do adresáře aplikace a spusťte aplikaci příkazem `dotnet run`.

```
dotnet run
```

---

Výstup ukázkové aplikace je podobný jako v následujícím příkladu:

```
Azure Blob storage - .NET Quickstart sample

Created container 'quickstartblobs33c90d2a-eabd-4236-958b-5cc5949e731f'

Temp file = C:\Users\myusername\Documents\QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db.txt
Uploading to Blob storage as blob 'QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db.txt'

Listing blobs in container.
https://storagesamples.blob.core.windows.net/quickstartblobs33c90d2a-eabd-4236-958b-5cc5949e731f/QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db.txt

Downloading blob to C:\Users\myusername\Documents\QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db_DOWNLOADED.txt

Press any key to delete the sample files and example container.
```

Když stisknete klávesu **Enter**, aplikace odstraní kontejner úložiště i soubory. Před jejich odstraněním zkontrolujte, jestli složka **Dokumenty** obsahuje příslušné dva soubory. Můžete je otevřít a podívat se, že jsou identické. Obsah objektu blob můžete zobrazit zkopírováním adresy URL objektu blob z okna konzoly a jejím vložením do prohlížeče.

Po ověření souborů stiskněte libovolnou klávesu a dokončete ukázku a odstraňte testovací soubory. Když teď víte, co ukázka dělá, otevřete soubor Program.cs a prohlédněte si kód. 

## <a name="understand-the-sample-code"></a>Vysvětlení vzorového kódu

Dále prozkoumejte vzorový kód, abyste pochopili, jak funguje.

### <a name="try-parsing-the-connection-string"></a>Zkouška parsování připojovacího řetězce

Ukázka jako první zkontroluje, že proměnná prostředí obsahuje připojovací řetězec, jehož parsováním je možné vytvořit objekt [CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) odkazující na účet úložiště. Ke kontrole platnosti připojovacího řetězce použijte metodu [TryParse](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.tryparse). Pokud se metoda **TryParse** úspěšně provede, inicializuje proměnnou *storageAccount* a vrátí hodnotu **true**.

```csharp
// Retrieve the connection string for use with the application. The storage connection string is stored
// in an environment variable on the machine running the application called storageconnectionstring.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell or application needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring");

// Check whether the connection string can be parsed.
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with operations against Blob storage here.
    ...
}
else
{
    // Otherwise, let the user know that they need to define the environment variable.
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add a environment variable named 'storageconnectionstring' with your storage " +
        "connection string as a value.");
    Console.WriteLine("Press any key to exit the sample application.");
    Console.ReadLine();
}
```

### <a name="create-the-container-and-set-permissions"></a>Vytvoření kontejneru a nastavení oprávnění

Dále ukázka vytvoří kontejner a nastaví jeho oprávnění tak, že všechny objekty blob v kontejneru budou veřejné. Když je objekt blob veřejný, může k němu anonymně přistupovat jakýkoli klient.

Pokud chcete vytvořit kontejner, nejprve vytvořte instanci objektu [CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient), která odkazuje na úložiště objektů blob ve vašem účtu úložiště. Dále vytvořte instanci objektu [CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer) a pak vytvořte kontejner. 

V tomto případě ukázka vytvoří kontejner zavoláním metody [CreateAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.createasync). K názvu kontejneru se připojí hodnota GUID, která zajistí jeho jedinečnost. V produkčním prostředí je často vhodnější použít metodu [CreateIfNotExistsAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.createifnotexistsasync), která předchází konfliktům názvů tím, že vytvoří kontejner, pouze pokud ještě neexistuje.

> [!IMPORTANT]
> Názvy kontejnerů musí obsahovat jen malá písmena. Další informace o pojmenování kontejnerů a objektů blob najdete v tématu [Názvy kontejnerů, objektů blob a metadat a odkazování na ně](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).


```csharp
// Create the CloudBlobClient that represents the Blob storage endpoint for the storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs' and append a GUID value to it to make the name unique. 
cloudBlobContainer = cloudBlobClient.GetContainerReference("quickstartblobs" + Guid.NewGuid().ToString());
await cloudBlobContainer.CreateAsync();

// Set the permissions so the blobs are public. 
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-the-container"></a>Nahrání objektů blob do kontejneru

Dále ukázka nahraje místní soubor do objektu blob bloku. Příklad kódu získá odkaz na objekt **CloudBlockBlob** zavoláním metody [GetBlockBlobReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.getblockblobreference) pro kontejner vytvořený v předchozí části. Pak do objektu blob nahraje vybraný soubor zavoláním metody [UploadFromFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromfileasync). Tato metoda vytvoří objekt blob, pokud ještě neexistuje, a přepíše ho, pokud už existoval. 

```csharp
// Create a file in your local MyDocuments folder to upload to a blob.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
sourceFile = Path.Combine(localPath, localFileName);
// Write text to the file.
File.WriteAllText(sourceFile, "Hello, World!");

Console.WriteLine("Temp file = {0}", sourceFile);
Console.WriteLine("Uploading to Blob storage as blob '{0}'", localFileName);

// Get a reference to the blob address, then upload the file to the blob.
// Use the value of localFileName for the blob name.
CloudBlockBlob cloudBlockBlob = cloudBlobContainer.GetBlockBlobReference(localFileName);
await cloudBlockBlob.UploadFromFileAsync(sourceFile);
```

### <a name="list-the-blobs-in-a-container"></a>Zobrazí seznam objektů blob v kontejneru

Ukázka vypíše objekty blob v kontejneru pomocí metody [ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync). V případě ukázky se do kontejneru přidal pouze jeden objekt blob, takže operace výpisu vrátí jenom tento jeden objekt blob.

Pokud je na vrácení v jednom volání příliš mnoho objektů blob (ve výchozím nastavení více než 5 000), pak metoda **ListBlobsSegmentedAsync** vrátí segment celkové sady výsledků dotazu a token pro pokračování. K načtení dalšího segmentu objektů blob zadáte token pro pokračování vrácený z předchozího volání a tak dále, dokud token pro pokračování není nulový. Nulový token pro pokračování značí, že se načetly všechny objekty blob. Vzorový kód ukazuje, jak použít token pro pokračování v souladu s osvědčenými postupy.

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    // Get the value of the continuation token returned by the listing call.
    blobContinuationToken = results.ContinuationToken;
    foreach (IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
} while (blobContinuationToken != null); // Loop while the continuation token is not null. 

```

### <a name="download-blobs"></a>Stáhnout objekty blob

Dále ukázka stáhne dříve vytvořený objekt blob do vašeho místního systému souborů pomocí metody [DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync). Vzorový kód k názvu objektu blob přidává příponu _DOWNLOADED, takže se v systému souborů zobrazí oba soubory.

```csharp
// Download the blob to a local file, using the reference created earlier. 
// Append the string "_DOWNLOADED" before the .txt extension so that you can see both files in MyDocuments.
destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);  
```

### <a name="clean-up-resources"></a>Vyčištění prostředků

Ukázka vyčistí prostředky, které vytvořila, odstraněním celého kontejneru pomocí metody [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteasync). Pokud chcete, můžete odstranit také místní soubory.

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

## <a name="resources-for-developing-net-applications-with-blobs"></a>Zdroje informací pro vývoj aplikací .NET s využitím objektů blob

Prohlédněte si tyto další zdroje informací o vývoji v .NET s využitím úložiště objektů blob:

### <a name="binaries-and-source-code"></a>Binární soubory a zdrojový kód

- Stáhněte si balíček NuGet s nejnovější verzí [klientské knihovny pro .NET](https://www.nuget.org/packages/WindowsAzure.Storage/) pro službu Azure Storage. 
- Prohlédněte si [zdrojový kód klientské knihovny pro .NET](https://github.com/Azure/azure-storage-net) na GitHubu.

### <a name="client-library-reference-and-samples"></a>Klientská knihovna – referenční informace a ukázky

- Další informace o klientské knihovně pro .NET najdete v [referenčních informacích k rozhraní .NET API](https://docs.microsoft.com/dotnet/api/overview/azure/storage).
- Prozkoumejte [ukázky pro úložiště objektů blob](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=blob) napsané s využitím klientské knihovny pro .NET.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak nahrávat, stahovat a vypisovat objekty blob pomocí .NET. 

Pokud chcete zjistit, jak vytvořit webovou aplikaci, která odešle obrázek do úložiště objektů blob, pokračujte k tématu [Odeslání dat obrázků do cloudu v Azure Storage](storage-upload-process-images.md).

> [!div class="nextstepaction"]
> [Operace s úložištěm objektů blob – postupy](storage-dotnet-how-to-use-blobs.md)

- Další informace o .NET Core najdete v tématu [Začínáme s .NET během 10 minut](https://www.microsoft.com/net/learn/get-started/).
- Pokud chcete prozkoumat ukázkovou aplikaci, kterou můžete nasadit ze sady Visual Studio pro Windows, prohlédněte si [ukázku webové aplikace fotogalerie v .NET s využitím služby Azure Blob Storage](https://azure.microsoft.com/resources/samples/storage-blobs-dotnet-webapp/).
 