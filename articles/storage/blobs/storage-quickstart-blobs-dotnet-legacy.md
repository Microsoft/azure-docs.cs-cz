---
title: 'Úvodní příručka: Klientská knihovna úložiště objektů blob Azure pro rozhraní .NET'
description: V tomto rychlém startu se dozvíte, jak pomocí klientské knihovny úložiště objektů blob Azure pro rozhraní .NET vytvořit kontejner a objekt blob v úložišti objektů blob .). Dále se dozvíte, jak stáhnout objekt blob do místního počítače a jak zobrazit seznam všech objektů blob, které jsou v kontejneru.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/20/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: b243d05619642e1dd3ad8dfe2bbe1d0a9661b773
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75351303"
---
# <a name="quickstart-azure-blob-storage-client-library-v11-for-net"></a>Úvodní příručka: Klientská knihovna úložiště objektů blob Azure v11 pro rozhraní .NET

Začínáme s klientskou knihovnou azure blob storage v11 pro rozhraní .NET. Azure Blob Storage je řešení úložiště objektů společnosti Microsoft pro cloud. Postupujte podle pokynů k instalaci balíčku a vyzkoušejte ukázkový kód pro základní úkoly. Úložiště objektů blob je optimalizované pro ukládání velkých objemů nestrukturovaných dat.

Pomocí klientské knihovny úložiště objektů blob Azure pro rozhraní .NET:

* Vytvoření kontejneru
* Nastavení oprávnění pro kontejner
* Vytvoření objektu blob ve službě Azure Storage
* Stažení objektu blob do místního počítače
* Vypsat všechny objekty BLOB v kontejneru
* Odstranění kontejneru

[Referenční dokumentace](https://docs.microsoft.com/dotnet/api/overview/azure/storage?view=azure-dotnet) | rozhraní[Knihovna ukázky zdrojového kódu](https://github.com/Azure/azure-storage-net/tree/master/Blob) | [knihovny (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/) | [Ukázky](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=blob)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
* Účet Azure Storage – [vytvoření účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Aktuální [sada .NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) pro váš operační systém. Ujistěte se, že získat SDK a ne runtime.

## <a name="setting-up"></a>Nastavení

Tato část vás provede přípravou projektu pro práci s klientskou knihovnou Azure Blob Storage pro rozhraní .NET.

### <a name="create-the-project"></a>Vytvoření projektu

Nejprve vytvořte aplikaci .NET Core s názvem *blob-quickstart*.

1. V okně konzoly (například cmd, PowerShell `dotnet new` nebo Bash) vytvořte pomocí příkazu novou konzolovou aplikaci s názvem *blob-quickstart*. Tento příkaz vytvoří jednoduchý projekt C# "Hello World" s jedním zdrojovým souborem: *Program.cs*.

   ```console
   dotnet new console -n blob-quickstart
   ```

2. Přepněte do nově vytvořené složky *rychlého spuštění objektu blob* a vytvořte aplikaci, abyste ověřili, že je vše v pořádku.

   ```console
   cd blob-quickstart
   ```

   ```console
   dotnet build
   ```

Očekávaný výstup z sestavení by měl vypadat nějak takto:

```output
C:\QuickStarts\blob-quickstart> dotnet build
Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 44.31 ms for C:\QuickStarts\blob-quickstart\blob-quickstart.csproj.
  blob-quickstart -> C:\QuickStarts\blob-quickstart\bin\Debug\netcoreapp2.1\blob-quickstart.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:03.08
```

### <a name="install-the-package"></a>Instalace balíčku

Zatímco ještě v adresáři aplikace, nainstalujte klientské knihovny Azure `dotnet add package` Blob Storage pro balíček .NET pomocí příkazu.

```console
dotnet add package Microsoft.Azure.Storage.Blob
```

### <a name="set-up-the-app-framework"></a>Nastavení architektury aplikace

Z adresáře projektu:

1. Otevření *souboru Program.cs* v editoru
2. Odebrat `Console.WriteLine` příkaz
3. Přidání `using` směrnic
4. Vytvořte `ProcessAsync` metodu, kde bude umístěn hlavní kód pro příklad
5. Asynchronně volat metodu `ProcessAsync` z`Main`

Zde je kód:

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;

namespace blob_quickstart
{
    class Program
    {
        public static void Main()
        {
            Console.WriteLine("Azure Blob Storage - .NET quickstart sample\n");

            // Run the examples asynchronously, wait for the results before proceeding
            ProcessAsync().GetAwaiter().GetResult();

            Console.WriteLine("Press any key to exit the sample application.");
            Console.ReadLine();
        }

        private static async Task ProcessAsync()
        {
        }
    }
}
```

### <a name="copy-your-credentials-from-the-azure-portal"></a>Zkopírování přihlašovacích údajů z webu Azure Portal

Když ukázková aplikace provede požadavek na Azure Storage, musí být autorizovaná. Chcete-li žádost autorizovat, přidejte do aplikace přihlašovací údaje účtu úložiště jako připojovací řetězec. Přihlašovací údaje účtu úložiště zobrazíte pomocí následujícího postupu:

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. Vyhledejte svůj účet úložiště.
3. V části **Nastavení** v přehledu účtu úložiště vyberte **Přístupové klíče**. Tady se zobrazí přístupové klíče vašeho účtu a úplný připojovací řetězec pro jednotlivé klíče.
4. V části **key1** vyhledejte hodnotu **Připojovací řetězec** a výběrem tlačítka **Kopírovat** zkopírujte připojovací řetězec. V dalším kroku přidáte hodnotu připojovacího řetězce do proměnné prostředí.

    ![Snímek obrazovky ukazující zkopírování připojovacího řetězce z webu Azure Portal](../../../includes/media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>Konfigurace připojovacího řetězce úložiště

Po zkopírování připojovacího řetězce ho zapište do nové proměnné prostředí na místním počítači, na kterém aplikaci spouštíte. Proměnnou prostředí nastavíte tak, že otevřete okno konzoly a budete postupovat podle pokynů pro váš operační systém. Nahraďte `<yourconnectionstring>` aktuálním připojovacím řetězcem.

#### <a name="windows"></a>Windows

```cmd
setx AZURE_STORAGE_CONNECTION_STRING "<yourconnectionstring>"
```

Po přidání proměnné prostředí v systému Windows je nutné spustit novou instanci příkazového okna.

#### <a name="linux"></a>Linux

```bash
export AZURE_STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

#### <a name="macos"></a>MacOS

```bash
export AZURE_STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

Po přidání proměnné prostředí restartujte všechny spuštěné programy, které budou muset číst proměnnou prostředí. Před pokračováním například restartujte vývojové prostředí nebo editor.

## <a name="object-model"></a>Objektový model

Úložiště objektů blob Azure je optimalizované pro ukládání obrovského množství nestrukturovaných dat. Jde o data, která nevyhovují konkrétnímu datovému modelu nebo definici, například textová nebo binární data. Úložiště objektů blob nabízí tři typy prostředků:

* Účet úložiště.
* Kontejner v účtu úložiště
* Objekt blob v kontejneru

Na následujícím diagramu jsou vztahy těchto prostředků.

![Diagram architektury úložiště objektů blob](./media/storage-quickstart-blobs-dotnet/blob1.png)

K interakci s těmito prostředky použijte následující třídy .NET:

* [CloudStorageAccount:](/dotnet/api/microsoft.azure.storage.cloudstorageaccount) `CloudStorageAccount` Třída představuje váš účet úložiště Azure. Pomocí této třídy můžete autorizovat přístup k úložišti objektů Blob pomocí přístupových klíčů vašeho účtu.
* [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient): `CloudBlobClient` Třída poskytuje bod přístupu ke službě objektů Blob ve vašem kódu.
* [CloudBlobContainer:](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer) `CloudBlobContainer` Třída představuje kontejner objektů blob ve vašem kódu.
* [CloudBlockBlob](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob): `CloudBlockBlob` Objekt představuje objekt blob bloku ve vašem kódu. Objekty blob bloku se skládají z bloků dat, které můžete spravovat jednotlivě.

## <a name="code-examples"></a>Příklady kódu

Tyto ukázkové fragmenty kódu ukazují, jak provést následující akce v klientské knihovně úložiště Objektů blob Azure pro rozhraní .NET:

   * [Ověření klienta](#authenticate-the-client)
   * [Vytvoření kontejneru](#create-a-container)
   * [Nastavení oprávnění pro kontejner](#set-permissions-on-a-container)
   * [Nahrání objektů BLOB do kontejneru](#upload-blobs-to-a-container)
   * [Zobrazí seznam objektů blob v kontejneru](#list-the-blobs-in-a-container)
   * [Stáhnout objekty blob](#download-blobs)
   * [Odstranění kontejneru](#delete-a-container)

### <a name="authenticate-the-client"></a>Ověření klienta

Níže uvedený kód zkontroluje, zda proměnná prostředí obsahuje připojovací řetězec, který lze analyzovat k vytvoření objektu [CloudStorageAccount](/dotnet/api/microsoft.azure.storage.cloudstorageaccount?view=azure-dotnet) směřujícího na účet úložiště. Ke kontrole platnosti připojovacího řetězce použijte metodu [TryParse](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse?view=azure-dotnet). Pokud `TryParse` je úspěšná, inicializuje `storageAccount` `true`proměnnou a vrátí .

Přidejte tento `ProcessAsync` kód uvnitř metody:

```csharp
// Retrieve the connection string for use with the application. The storage 
// connection string is stored in an environment variable on the machine 
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the 
// environment variable is created after the application is launched in a 
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("AZURE_STORAGE_CONNECTION_STRING");

// Check whether the connection string can be parsed.
CloudStorageAccount storageAccount;
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with operations against Blob
    // storage here.
    // ADD OTHER OPERATIONS HERE
}
else
{
    // Otherwise, let the user know that they need to define the environment variable.
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add an environment variable named 'AZURE_STORAGE_CONNECTION_STRING' with your storage " +
        "connection string as a value.");
    Console.WriteLine("Press any key to exit the application.");
    Console.ReadLine();
}
```

> [!NOTE]
> Chcete-li provést zbývající operace v `// ADD OTHER OPERATIONS HERE` tomto článku, nahraďte ve výše uvedeném kódu fragmenty kódu v následujících částech.

### <a name="create-a-container"></a>Vytvoření kontejneru

Pokud chcete vytvořit kontejner, nejprve vytvořte instanci objektu [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient), která odkazuje na úložiště objektů blob ve vašem účtu úložiště. Dále vytvořte instanci objektu [CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer) a pak vytvořte kontejner.

V tomto případě kód volá [CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync) metoda k vytvoření kontejneru. K názvu kontejneru se připojí hodnota GUID, která zajistí jeho jedinečnost. V produkčním prostředí je často vhodnější použít metodu [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync) k vytvoření kontejneru pouze v případě, že ještě neexistuje.

> [!IMPORTANT]
> Názvy kontejnerů musí být malými písmeny. Další informace o pojmenování kontejnerů a objektů blob najdete v tématu [Názvy kontejnerů, objektů blob a metadat a odkazování na ně](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

```csharp
// Create the CloudBlobClient that represents the 
// Blob storage endpoint for the storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs' and 
// append a GUID value to it to make the name unique.
CloudBlobContainer cloudBlobContainer = 
    cloudBlobClient.GetContainerReference("quickstartblobs" + 
        Guid.NewGuid().ToString());
await cloudBlobContainer.CreateAsync();
```

### <a name="set-permissions-on-a-container"></a>Nastavení oprávnění pro kontejner

Nastavte oprávnění pro kontejner tak, aby všechny objekty BLOB v kontejneru jsou veřejné. Když je objekt blob veřejný, může k němu anonymně přistupovat jakýkoli klient.

```csharp
// Set the permissions so the blobs are public.
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-a-container"></a>Nahrání objektů BLOB do kontejneru

Následující fragment kódu získá odkaz na `CloudBlockBlob` objekt voláním metody [GetBlockBlobReference](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getblockblobreference) na kontejneru vytvořeném v předchozí části. Potom odešle vybraný místní soubor do objektu blob voláním [Metody UploadFromFileAsync.](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromfileasync) Tato metoda vytvoří objekt blob, pokud ještě neexistuje, a přepíše ho, pokud už existoval.

```csharp
// Create a file in your local MyDocuments folder to upload to a blob.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
string sourceFile = Path.Combine(localPath, localFileName);
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

Seznam objektů BLOB v kontejneru pomocí [ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync) metoda. V tomto případě byl do kontejneru přidán pouze jeden objekt blob, takže operace výpisu vrátí pouze jeden objekt blob.

Pokud existuje příliš mnoho objektů BLOB vrátit v jednom volání (ve výchozím `ListBlobsSegmentedAsync` nastavení více než 5000), pak metoda vrátí segment celkové sady výsledků a token pokračování. K načtení dalšího segmentu objektů blob zadáte token pro pokračování vrácený z předchozího volání a tak dále, dokud token pro pokračování není nulový. Nulový token pro pokračování značí, že se načetly všechny objekty blob. Kód ukazuje, jak použít token pokračování v zájmu osvědčených postupů.

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

Stáhněte objekt blob vytvořený dříve do místního systému souborů pomocí metody [DownloadToFileAsync.](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync) Ukázkový kód přidá k názvu objektu blob příponu "_DOWNLOADED", takže oba soubory můžete zobrazit v místním systému souborů.

```csharp
// Download the blob to a local file, using the reference created earlier.
// Append the string "_DOWNLOADED" before the .txt extension so that you 
// can see both files in MyDocuments.
string destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);
```

### <a name="delete-a-container"></a>Odstranění kontejneru

Následující kód vyčistí prostředky, které aplikace vytvořila odstraněním celého kontejneru pomocí [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync). Pokud chcete, můžete odstranit také místní soubory.

```csharp
Console.WriteLine("Press the 'Enter' key to delete the example files, " +
    "example container, and exit the application.");
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

## <a name="run-the-code"></a>Spuštění kódu

Tato aplikace vytvoří testovací soubor v místní složce *MyDocuments* a nahraje jej do úložiště objektů Blob. V příkladu jsou uvedeny objekty BLOB v kontejneru a stáhne soubor s novým názvem, abyste mohli porovnat staré a nové soubory.

Přejděte do adresáře aplikace a pak ji sestavte a spusťte.

```console
dotnet build
```

```console
dotnet run
```

Výstup aplikace je podobný následujícímu příkladu:

```output
Azure Blob storage - .NET Quickstart example

Created container 'quickstartblobs33c90d2a-eabd-4236-958b-5cc5949e731f'

Temp file = C:\Users\myusername\Documents\QuickStart_c5e7f24f-a7f8-4926-a9da-96
97c748f4db.txt
Uploading to Blob storage as blob 'QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f
4db.txt'

Listing blobs in container.
https://storagesamples.blob.core.windows.net/quickstartblobs33c90d2a-eabd-4236-
958b-5cc5949e731f/QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db.txt

Downloading blob to C:\Users\myusername\Documents\QuickStart_c5e7f24f-a7f8-4926
-a9da-9697c748f4db_DOWNLOADED.txt

Press any key to delete the example files and example container.
```

Když stisknete klávesu **Enter**, aplikace odstraní kontejner úložiště i soubory. Před jejich odstraněním zkontrolujte, jestli složka *Dokumenty* obsahuje příslušné dva soubory. Můžete je otevřít a podívat se, že jsou identické. Obsah objektu blob můžete zobrazit zkopírováním adresy URL objektu blob z okna konzoly a jejím vložením do prohlížeče.

Po ověření souborů stiskněte libovolnou klávesu a dokončete ukázku a odstraňte testovací soubory.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak nahrávat, stahovat a vypisovat objekty blob pomocí .NET.

Chcete-li se dozvědět, jak vytvořit webovou aplikaci, která nahraje obrázek do úložiště objektů Blob, pokračujte v:

> [!div class="nextstepaction"]
> [Nahrání a zpracování obrázku](storage-upload-process-images.md)

* Další informace o .NET Core najdete v tématu [Začínáme s .NET během 10 minut](https://www.microsoft.com/net/learn/get-started/).
* Pokud chcete prozkoumat ukázkovou aplikaci, kterou můžete nasadit ze sady Visual Studio pro Windows, prohlédněte si [ukázku webové aplikace fotogalerie v .NET s využitím služby Azure Blob Storage](https://azure.microsoft.com/resources/samples/storage-blobs-dotnet-webapp/).
