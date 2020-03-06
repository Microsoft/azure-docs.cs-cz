---
title: 'Rychlý Start: knihovna úložiště objektů BLOB v Azure V12 – .NET'
description: V tomto rychlém startu se dozvíte, jak pomocí klientské knihovny Azure Blob Storage verze 12 pro .NET vytvořit kontejner a objekt BLOB v úložišti objektů BLOB (objekt). Dále se dozvíte, jak stáhnout objekt blob do místního počítače a jak zobrazit seznam všech objektů blob, které jsou v kontejneru.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/05/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: b8ddeda2c435a52efd14f06acb3c1a8bb94bc45e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78381847"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-net"></a>Rychlý Start: Klientská knihovna pro úložiště objektů BLOB v Azure V12 pro .NET

Začněte s klientskou knihovnou V12 Azure Blob Storage pro .NET. Azure Blob Storage je řešení úložiště objektů Microsoftu pro cloud. Postupujte podle kroků a nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úlohy. Úložiště objektů blob je optimalizované pro ukládání velkých objemů nestrukturovaných dat.

> [!NOTE]
> Informace o tom, jak začít s předchozí verzí sady SDK, najdete v tématu [rychlý Start: Klientská knihovna pro Azure Blob Storage pro .NET](storage-quickstart-blobs-dotnet-legacy.md).

Použijte klientskou knihovnu služby Azure Blob Storage V12 pro .NET pro:

* Vytvoření kontejneru
* Nahrání objektu blob do Azure Storage
* Výpis všech objektů BLOB v kontejneru
* Stažení objektu blob do místního počítače
* Odstranění kontejneru

[Referenční dokumentace k rozhraní API](/dotnet/api/azure.storage.blobs) | [ukázka | ukázky](https://docs.microsoft.com/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples) | balíčku [zdrojového kódu knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs) [(NuGet)](https://www.nuget.org/packages/Azure.Storage.Blobs)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* Účet úložiště Azure – [Vytvoření účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Aktuální [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) pro váš operační systém. Ujistěte se, že jste získali sadu SDK a ne modul runtime.

## <a name="setting-up"></a>Nastavení

Tato část vás provede přípravou projektu pro práci s klientskou knihovnou služby Azure Blob Storage V12 for .NET.

### <a name="create-the-project"></a>Vytvoření projektu

Vytvořte aplikaci .NET Core s názvem *BlobQuickstartV12*.

1. V okně konzoly (například cmd, PowerShell nebo bash) použijte příkaz `dotnet new` a vytvořte novou konzolovou aplikaci s názvem *BlobQuickstartV12*. Tento příkaz vytvoří jednoduchý projekt "Hello World" C# s jedním zdrojovým souborem: *program.cs*.

   ```console
   dotnet new console -n BlobQuickstartV12
   ```

1. Přepněte do nově vytvořeného adresáře *BlobQuickstartV12* .

   ```console
   cd BlobQuickstartV12
   ```

1. Na straně adresáře *BlobQuickstartV12* vytvořte další adresář s názvem *data*. V tomto umístění se vytvoří a uloží datové soubory objektů BLOB.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Instalace balíčku

Ještě pořád v adresáři aplikace nainstalujte klientskou knihovnu pro Azure Blob Storage pro balíček .NET pomocí příkazu `dotnet add package`.

```console
dotnet add package Azure.Storage.Blobs
```

### <a name="set-up-the-app-framework"></a>Nastavení aplikační architektury

Z adresáře projektu:

1. V editoru otevřete soubor *program.cs*
1. Odebrání příkazu `Console.WriteLine("Hello World!");`
1. Přidat direktivy `using`
1. Aktualizace deklarace `Main` metody pro podporu asynchronního kódu

Zde je kód:

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using System;
using System.IO;
using System.Threading.Tasks;

namespace BlobQuickstartV12
{
    class Program
    {
        static async Task Main()
        {
        }
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektový model

Úložiště objektů BLOB v Azure je optimalizované pro ukládání obrovských objemů nestrukturovaných dat. Nestrukturovaná data jsou data, která nevyhovují konkrétnímu datovému modelu nebo definici, jako jsou textová nebo binární data. Úložiště objektů BLOB nabízí tři typy prostředků:

* Účet úložiště
* Kontejner v účtu úložiště
* Objekt BLOB v kontejneru

Na následujícím diagramu jsou vztahy těchto prostředků.

![Diagram architektury služby Blob Storage](./media/storage-blobs-introduction/blob1.png)

Pro interakci s těmito prostředky použijte následující třídy .NET:

* [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient): třída `BlobServiceClient` umožňuje manipulovat s prostředky Azure Storage a kontejnery objektů BLOB.
* [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient): třída `BlobContainerClient` umožňuje manipulovat s kontejnery Azure Storage a jejich objekty blob.
* [BlobClient](/dotnet/api/azure.storage.blobs.blobclient): třída `BlobClient` umožňuje manipulovat s objekty blob Azure Storage.
* [BlobDownloadInfo](/dotnet/api/azure.storage.blobs.models.blobdownloadinfo): třída `BlobDownloadInfo` představuje vlastnosti a obsah vrácený stažením objektu BLOB.

## <a name="code-examples"></a>Příklady kódu

Tyto ukázkové fragmenty kódu ukazují, jak provést následující akce s klientskou knihovnou služby Azure Blob Storage pro .NET:

* [Získání připojovacího řetězce](#get-the-connection-string)
* [Vytvoření kontejneru](#create-a-container)
* [Nahrání objektů blob do kontejneru](#upload-blobs-to-a-container)
* [Výpis objektů BLOB v kontejneru](#list-the-blobs-in-a-container)
* [Stáhnout objekty blob](#download-blobs)
* [Odstranění kontejneru](#delete-a-container)

### <a name="get-the-connection-string"></a>Získání připojovacího řetězce

Následující kód načte připojovací řetězec pro účet úložiště z proměnné prostředí vytvořené v oddílu [konfigurace vašeho připojovacího řetězce úložiště](#configure-your-storage-connection-string) .

Přidejte tento kód do metody `Main`:

```csharp
Console.WriteLine("Azure Blob storage v12 - .NET quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
string connectionString = Environment.GetEnvironmentVariable("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-container"></a>Vytvoření kontejneru

Určete název nového kontejneru. Následující kód připojí hodnotu identifikátoru GUID k názvu kontejneru, aby bylo zajištěno, že je jedinečný.

> [!IMPORTANT]
> Názvy kontejnerů musí obsahovat jen malá písmena. Další informace o pojmenování kontejnerů a objektů blob najdete v tématu [Názvy kontejnerů, objektů blob a metadat a odkazování na ně](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Vytvořte instanci třídy [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient) . Pak zavolejte metodu [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync) a vytvořte kontejner v účtu úložiště.

Přidejte tento kód na konec metody `Main`:

```csharp
// Create a BlobServiceClient object which will be used to create a container client
BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

//Create a unique name for the container
string containerName = "quickstartblobs" + Guid.NewGuid().ToString();

// Create the container and return a container client object
BlobContainerClient containerClient = await blobServiceClient.CreateBlobContainerAsync(containerName);
```

### <a name="upload-blobs-to-a-container"></a>Nahrání objektů blob do kontejneru

Následující fragment kódu:

1. Vytvoří textový soubor v místním *datovém* adresáři.
1. Získá odkaz na objekt [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) voláním metody [GetBlobClient](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobclient) na kontejneru z oddílu [vytvoření kontejneru](#create-a-container) .
1. Nahraje místní textový soubor do objektu BLOB voláním metody [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync#Azure_Storage_Blobs_BlobClient_UploadAsync_System_IO_Stream_System_Boolean_System_Threading_CancellationToken_) . Tato metoda vytvoří objekt blob, pokud ještě neexistuje, a přepíše ho, pokud už existoval.

Přidejte tento kód na konec metody `Main`:

```csharp
// Create a local file in the ./data/ directory for uploading and downloading
string localPath = "./data/";
string fileName = "quickstart" + Guid.NewGuid().ToString() + ".txt";
string localFilePath = Path.Combine(localPath, fileName);

// Write text to the file
await File.WriteAllTextAsync(localFilePath, "Hello, World!");

// Get a reference to a blob
BlobClient blobClient = containerClient.GetBlobClient(fileName);

Console.WriteLine("Uploading to Blob storage as blob:\n\t {0}\n", blobClient.Uri);

// Open the file and upload its data
using FileStream uploadFileStream = File.OpenRead(localFilePath);
await blobClient.UploadAsync(uploadFileStream, true);
uploadFileStream.Close();
```

### <a name="list-the-blobs-in-a-container"></a>Zobrazí seznam objektů blob v kontejneru

Seznam objektů BLOB v kontejneru zavoláním metody [GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync) . V tomto případě byl do kontejneru přidán pouze jeden objekt blob, takže operace výpisu vrátí pouze jeden objekt BLOB.

Přidejte tento kód na konec metody `Main`:

```csharp
Console.WriteLine("Listing blobs...");

// List all blobs in the container
await foreach (BlobItem blobItem in containerClient.GetBlobsAsync())
{
    Console.WriteLine("\t" + blobItem.Name);
}
```

### <a name="download-blobs"></a>Stáhnout objekty blob

Stáhněte dřív vytvořený objekt BLOB voláním metody [DownloadAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadasync) . Vzorový kód přidá příponu "stažený" do názvu souboru, aby bylo možné zobrazit oba soubory v místním systému souborů.

Přidejte tento kód na konec metody `Main`:

```csharp
// Download the blob to a local file
// Append the string "DOWNLOAD" before the .txt extension 
// so you can compare the files in the data directory
string downloadFilePath = localFilePath.Replace(".txt", "DOWNLOAD.txt");

Console.WriteLine("\nDownloading blob to\n\t{0}\n", downloadFilePath);

// Download the blob's contents and save it to a file
BlobDownloadInfo download = await blobClient.DownloadAsync();

using FileStream downloadFileStream = File.OpenWrite(downloadFilePath);
await download.Content.CopyToAsync(downloadFileStream);
downloadFileStream.Close();
```

### <a name="delete-a-container"></a>Odstranění kontejneru

Následující kód vyčistí prostředky, které aplikace vytvořila, odstraněním celého kontejneru pomocí [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync). Odstraní také místní soubory vytvořené aplikací.

Aplikace pozastaví vstup uživatele voláním `Console.ReadLine` předtím, než odstraní objekt blob, kontejner a místní soubory. Tato možnost je vhodná pro ověření, že prostředky byly ve skutečnosti vytvořeny správně, než byly odstraněny.

Přidejte tento kód na konec metody `Main`:

```csharp
// Clean up
Console.Write("Press any key to begin clean up");
Console.ReadLine();

Console.WriteLine("Deleting blob container...");
await containerClient.DeleteAsync();

Console.WriteLine("Deleting the local source and downloaded files...");
File.Delete(localFilePath);
File.Delete(downloadFilePath);

Console.WriteLine("Done");
```

## <a name="run-the-code"></a>Spuštění kódu

Tato aplikace vytvoří testovací soubor ve složce místních *dat* a nahraje ho do úložiště objektů BLOB. Příklad zobrazí seznam objektů BLOB v kontejneru a stáhne soubor s novým názvem, abyste mohli porovnat staré a nové soubory.

Přejděte do adresáře aplikace a pak Sestavte a spusťte aplikaci.

```console
dotnet build
```

```console
dotnet run
```

Výstup aplikace je podobný následujícímu příkladu:

```output
Azure Blob storage v12 - .NET quickstart sample

Uploading to Blob storage as blob:
         https://mystorageacct.blob.core.windows.net/quickstartblobs60c70d78-8d93-43ae-954d-8322058cfd64/quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31.txt

Listing blobs...
        quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31.txt

Downloading blob to
        ./data/quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31DOWNLOADED.txt

Press any key to begin clean up
Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Než zahájíte proces vyčištění, vyhledejte tyto dva soubory ve složce *dat* . Můžete je otevřít a podívat se, že jsou identické.

Po ověření souborů stiskněte klávesu **ENTER** , aby se odstranily soubory testu, a dokončete ukázku.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak nahrávat, stahovat a vypisovat objekty blob pomocí .NET.

Pokud chcete zobrazit ukázkové aplikace služby Blob Storage, pokračujte:

> [!div class="nextstepaction"]
> [Ukázky rozhraní .NET V12 pro Azure Blob Storage SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples)

* Kurzy, ukázky, rychlé starty a další dokumentace najdete na webu [Azure pro vývojáře na platformě .NET a .NET Core](/dotnet/azure/).
* Další informace o .NET Core najdete v tématu [Začínáme s .NET během 10 minut](https://www.microsoft.com/net/learn/get-started/).
