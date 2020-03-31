---
title: 'Úvodní příručka: Knihovna úložiště objektů blob Azure v12 – .NET'
description: V tomto rychlém startu se dozvíte, jak pomocí knihovny klienta úložiště objektů blob Azure verze 12 pro rozhraní .NET vytvořit kontejner a objekt blob v úložišti objektů blob (objekt). Dále se dozvíte, jak stáhnout objekt blob do místního počítače a jak zobrazit seznam všech objektů blob, které jsou v kontejneru.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/05/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 5cfb0430bc94d347afd75bc01170a71a7ad53565
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240503"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-net"></a>Úvodní příručka: Klientská knihovna úložiště objektů blob Azure v12 pro rozhraní .NET

Začínáme s klientskou knihovnou úložiště objektů blob Azure v12 pro rozhraní .NET. Azure Blob Storage je řešení úložiště objektů Microsoftu pro cloud. Postupujte podle pokynů k instalaci balíčku a vyzkoušejte ukázkový kód pro základní úkoly. Úložiště objektů blob je optimalizované pro ukládání velkých objemů nestrukturovaných dat.

> [!NOTE]
> Pokud chcete začít s předchozí verzí sady SDK, přečtěte si [úvodní příručku: Klientská knihovna úložiště objektů Blob Azure pro rozhraní .NET](storage-quickstart-blobs-dotnet-legacy.md).

Pomocí klientské knihovny úložiště objektů blob Azure v12 pro rozhraní .NET:

* Vytvoření kontejneru
* Nahrání objektu blob do Azure Storage
* Vypsat všechny objekty BLOB v kontejneru
* Stažení objektu blob do místního počítače
* Odstranění kontejneru

[Referenční dokumentace](/dotnet/api/azure.storage.blobs) | rozhraní[Knihovna ukázky zdrojového kódu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs) | [knihovny (NuGet)](https://www.nuget.org/packages/Azure.Storage.Blobs) | [Ukázky](https://docs.microsoft.com/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
* Účet úložiště Azure – [vytvoření účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Aktuální [sada .NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) pro váš operační systém. Ujistěte se, že získat SDK a ne runtime.

## <a name="setting-up"></a>Nastavení

Tato část vás provede přípravou projektu pro práci s klientskou knihovnou úložiště objektů Blob Azure v12 pro rozhraní .NET.

### <a name="create-the-project"></a>Vytvoření projektu

Vytvořte aplikaci .NET Core s názvem *BlobQuickstartV12*.

1. V okně konzoly (například cmd, PowerShell `dotnet new` nebo Bash) vytvořte pomocí příkazu novou konzolovou aplikaci s názvem *BlobQuickstartV12*. Tento příkaz vytvoří jednoduchý projekt C# "Hello World" s jedním zdrojovým souborem: *Program.cs*.

   ```console
   dotnet new console -n BlobQuickstartV12
   ```

1. Přepněte do nově vytvořeného adresáře *BlobQuickstartV12.*

   ```console
   cd BlobQuickstartV12
   ```

1. Na straně adresáře *BlobQuickstartV12* vytvořte jiný adresář s názvem *data*. Toto je místo, kde budou vytvořeny a uloženy datové soubory objektů blob.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Instalace balíčku

Zatímco ještě v adresáři aplikace, nainstalujte klientskou knihovnu úložiště `dotnet add package` Azure Blob pro balíček .NET pomocí příkazu.

```console
dotnet add package Azure.Storage.Blobs
```

### <a name="set-up-the-app-framework"></a>Nastavení architektury aplikace

Z adresáře projektu:

1. Otevření *souboru Program.cs* v editoru
1. Odebrat `Console.WriteLine("Hello World!");` příkaz
1. Přidání `using` směrnic
1. Aktualizace `Main` deklarace metody pro podporu asynchronního kódu

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

Úložiště objektů blob Azure je optimalizované pro ukládání obrovského množství nestrukturovaných dat. Jde o data, která nevyhovují konkrétnímu datovému modelu nebo definici, například textová nebo binární data. Úložiště objektů blob nabízí tři typy prostředků:

* Účet úložiště
* Kontejner v účtu úložiště
* Objekt blob v kontejneru

Na následujícím diagramu jsou vztahy těchto prostředků.

![Diagram architektury úložiště objektů blob](./media/storage-blobs-introduction/blob1.png)

K interakci s těmito prostředky použijte následující třídy .NET:

* [BlobServiceClient:](/dotnet/api/azure.storage.blobs.blobserviceclient) `BlobServiceClient` Třída umožňuje manipulovat s prostředky úložiště Azure a kontejnery objektů blob.
* [BlobContainerClient:](/dotnet/api/azure.storage.blobs.blobcontainerclient) `BlobContainerClient` Třída umožňuje manipulovat s kontejnery úložiště Azure a jejich objekty BLOB.
* [Objekt BlobClient:](/dotnet/api/azure.storage.blobs.blobclient)Třída `BlobClient` umožňuje manipulovat s objekty BLOB úložiště Azure.
* [BlobDownloadInfo](/dotnet/api/azure.storage.blobs.models.blobdownloadinfo): `BlobDownloadInfo` Třída představuje vlastnosti a obsah vrácený ze stahování objektu blob.

## <a name="code-examples"></a>Příklady kódu

Tyto ukázkové fragmenty kódu ukazují, jak provést následující akce v klientské knihovně úložiště Objektů blob Azure pro rozhraní .NET:

* [Získání připojovacího řetězce](#get-the-connection-string)
* [Vytvoření kontejneru](#create-a-container)
* [Nahrání objektů BLOB do kontejneru](#upload-blobs-to-a-container)
* [Zobrazí seznam objektů blob v kontejneru](#list-the-blobs-in-a-container)
* [Stáhnout objekty blob](#download-blobs)
* [Odstranění kontejneru](#delete-a-container)

### <a name="get-the-connection-string"></a>Získání připojovacího řetězce

Níže uvedený kód načte připojovací řetězec pro účet úložiště z proměnné prostředí vytvořené v části [Konfigurovat připojovací řetězec úložiště.](#configure-your-storage-connection-string)

Přidejte tento `Main` kód uvnitř metody:

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

Rozhodněte o názvu nového kontejneru. Níže uvedený kód připojí hodnotu GUID k názvu kontejneru, aby bylo zajištěno, že je jedinečný.

> [!IMPORTANT]
> Názvy kontejnerů musí být malými písmeny. Další informace o pojmenování kontejnerů a objektů blob najdete v tématu [Názvy kontejnerů, objektů blob a metadat a odkazování na ně](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Vytvořte instanci třídy [BlobServiceClient.](/dotnet/api/azure.storage.blobs.blobserviceclient) Potom zavolejte [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync) metoda k vytvoření kontejneru v účtu úložiště.

Přidejte tento kód na `Main` konec metody:

```csharp
// Create a BlobServiceClient object which will be used to create a container client
BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

//Create a unique name for the container
string containerName = "quickstartblobs" + Guid.NewGuid().ToString();

// Create the container and return a container client object
BlobContainerClient containerClient = await blobServiceClient.CreateBlobContainerAsync(containerName);
```

### <a name="upload-blobs-to-a-container"></a>Nahrání objektů BLOB do kontejneru

Následující fragment kódu:

1. Vytvoří textový soubor v místním *datovém* adresáři.
1. Získá odkaz na objekt [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) voláním [GetBlobClient](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobclient) metoda v kontejneru z [vytvořit kontejner](#create-a-container) u části.
1. Odešle místní textový soubor do objektu blob voláním metody [UploadAsync.](/dotnet/api/azure.storage.blobs.blobclient.uploadasync#Azure_Storage_Blobs_BlobClient_UploadAsync_System_IO_Stream_System_Boolean_System_Threading_CancellationToken_) Tato metoda vytvoří objekt blob, pokud ještě neexistuje, a přepíše ho, pokud už existoval.

Přidejte tento kód na `Main` konec metody:

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

Seznam objektů BLOB v kontejneru voláním [GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync) metoda. V tomto případě byl do kontejneru přidán pouze jeden objekt blob, takže operace výpisu vrátí pouze jeden objekt blob.

Přidejte tento kód na `Main` konec metody:

```csharp
Console.WriteLine("Listing blobs...");

// List all blobs in the container
await foreach (BlobItem blobItem in containerClient.GetBlobsAsync())
{
    Console.WriteLine("\t" + blobItem.Name);
}
```

### <a name="download-blobs"></a>Stáhnout objekty blob

Stáhněte si dříve vytvořený objekt blob voláním metody [DownloadAsync.](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadasync) Ukázkový kód přidá k názvu souboru příponu "STAŽENO", takže oba soubory můžete zobrazit v místním systému souborů.

Přidejte tento kód na `Main` konec metody:

```csharp
// Download the blob to a local file
// Append the string "DOWNLOAD" before the .txt extension 
// so you can compare the files in the data directory
string downloadFilePath = localFilePath.Replace(".txt", "DOWNLOAD.txt");

Console.WriteLine("\nDownloading blob to\n\t{0}\n", downloadFilePath);

// Download the blob's contents and save it to a file
BlobDownloadInfo download = await blobClient.DownloadAsync();

using (FileStream downloadFileStream = File.OpenWrite(downloadFilePath))
{
    await download.Content.CopyToAsync(downloadFileStream);
    downloadFileStream.Close();
}
```

### <a name="delete-a-container"></a>Odstranění kontejneru

Následující kód vyčistí prostředky, které aplikace vytvořila odstraněním celého kontejneru pomocí [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync). Odstraní také místní soubory vytvořené aplikací.

Aplikace pozastaví vstup uživatele `Console.ReadLine` voláním před odstraněním objektu blob, kontejneru a místních souborů. Toto je dobrá šance ověřit, že prostředky byly skutečně vytvořeny správně, před jejich odstraněním.

Přidejte tento kód na `Main` konec metody:

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

Tato aplikace vytvoří testovací soubor v místní *datové* složce a nahraje ho do úložiště objektů Blob. V příkladu jsou uvedeny objekty BLOB v kontejneru a stáhne soubor s novým názvem, abyste mohli porovnat staré a nové soubory.

Přejděte do adresáře aplikace a pak ji sestavte a spusťte.

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

Než začnete proces čištění, zkontrolujte, zda ve složce *dat* neobsahuje dva soubory. Můžete je otevřít a podívat se, že jsou identické.

Po ověření souborů odstraňte testovací soubory stisknutím klávesy **Enter** a dokončete ukázku.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak nahrávat, stahovat a vypisovat objekty blob pomocí .NET.

Pokud chcete zobrazit ukázkové aplikace úložiště objektů Blob, pokračujte v:

> [!div class="nextstepaction"]
> [Ukázky sady Azure Blob Storage SDK v12 .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples)

* Kurzy, ukázky, rychlé spuštění a další dokumentaci najdete na webu [Azure for .NET a .NET Core developers](/dotnet/azure/).
* Další informace o .NET Core najdete v tématu [Začínáme s .NET během 10 minut](https://www.microsoft.com/net/learn/get-started/).
