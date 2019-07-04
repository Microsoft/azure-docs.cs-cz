---
title: 'Rychlý start: Azure Blob storage Klientská knihovna pro .NET'
description: V tomto rychlém startu se dozvíte, jak používat klientskou knihovnu pro úložiště objektů Blob v Azure pro .NET k vytvoření kontejneru a objektu blob v úložišti objektů Blob (objekt). Dále se dozvíte, jak stáhnout objekt blob do místního počítače a jak zobrazit seznam všech objektů blob, které jsou v kontejneru.
services: storage
author: mhopkins-msft
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 06/20/2019
ms.author: mhopkins
ms.subservice: blobs
ms.openlocfilehash: c5e9981c6854ff778775631f1d671189830e564b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67435756"
---
# <a name="quickstart-azure-blob-storage-client-library-for-net"></a>Rychlý start: Azure Blob storage Klientská knihovna pro .NET

Začínáme s klientskou knihovnu pro úložiště objektů Blob v Azure pro .NET. Azure Blob Storage je řešení úložiště objektů Microsoftu pro cloud. Postupujte podle kroků instalace balíčku a vyzkoušejte si ukázkový kód pro základní úlohy. Úložiště objektů blob je optimalizovaná pro ukládání velkých objemů nestrukturovaných dat.

Použijte klientskou knihovnu pro úložiště objektů Blob v Azure pro .NET:

* Vytvoření kontejneru
* Nastavení oprávnění pro kontejner
* Vytvoření objektu blob ve službě Azure Storage
* Stažení objektu blob na místní počítač
* Seznam všech objektů BLOB v kontejneru
* Odstranění kontejneru

[Referenční dokumentace rozhraní API](https://docs.microsoft.com/dotnet/api/overview/azure/storage?view=azure-dotnet) | [zdrojový kód knihovny](https://github.com/Azure/azure-storage-net/tree/master/Blob) | [balíčku (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/) | [ukázky](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=blob)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořit zdarma](https://azure.microsoft.com/free/)
* Účet služby Azure Storage – [vytvoření účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* [NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet-core), nebo novější pro váš operační systém

## <a name="setting-up"></a>Nastavení

Tato část vás provede přípravou projektu pro práci s klientskou knihovnu pro úložiště objektů Blob v Azure pro .NET.

### <a name="create-the-project"></a>Vytvoření projektu

Nejprve vytvořte aplikaci .NET Core s názvem **blob-quickstart**.

1. V okně konzoly (jako je cmd, PowerShell nebo Bash), použijte `dotnet new` příkaz pro vytvoření nových konzolovou aplikaci s názvem **blob-quickstart**. Tento příkaz vytvoří jednoduchý "Hello World" C# projekt s jeden zdrojový soubor: **Program.cs**.

   ```console
   dotnet new console -n blob-quickstart
   ```

2. Přepnout na nově vytvořený **blob-quickstart** složky a sestavení aplikace a zkontrolujte, že vše je dobře.

   ```console
   cd blob-quickstart
   ```

   ```console
   dotnet build
   ```

Očekávaný výstup sestavení by měl vypadat přibližně takto:

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

Zatímco jste pořád v adresáři aplikace nainstalujte s použitím klientskou knihovnu pro úložiště objektů Blob v Azure pro balíček .NET `dotnet add package` příkazu.

```console
dotnet add package Microsoft.Azure.Storage.Blob
```

### <a name="set-up-the-app-framework"></a>Nastavení rozhraní framework aplikace

Z adresáře projektu:

1. Otevřete soubor Program.cs v editoru
2. Odeberte **Console.WriteLine** – příkaz
3. Přidat **pomocí** direktivy
4. Vytvoření **ProcessAsync** kde se bude nacházet hlavní kód například – metoda
5. Asynchronně volat **ProcessAsync** metodu z **Main**

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

Ukázková aplikace potřebuje ověřit přístup k vašemu účtu úložiště. Ověření provedete tak, že do aplikace přidáte přihlašovací údaje svého účtu úložiště v podobě připojovacího řetězce. Přihlašovací údaje účtu úložiště zobrazíte pomocí následujícího postupu:

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. Vyhledejte svůj účet úložiště.
3. V části **Nastavení** v přehledu účtu úložiště vyberte **Přístupové klíče**. Tady se zobrazí přístupové klíče vašeho účtu a úplný připojovací řetězec pro jednotlivé klíče.
4. V části **key1** vyhledejte hodnotu **Připojovací řetězec** a výběrem tlačítka **Kopírovat** zkopírujte připojovací řetězec. V dalším kroku přidáte hodnotu připojovacího řetězce do proměnné prostředí.

    ![Snímek obrazovky ukazující zkopírování připojovacího řetězce z webu Azure Portal](../../../includes/media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>Konfigurace připojovacího řetězce úložiště

Po zkopírování připojovacího řetězce ho zapište do nové proměnné prostředí na místním počítači, na kterém aplikaci spouštíte. Proměnnou prostředí nastavíte tak, že otevřete okno konzoly a budete postupovat podle pokynů pro váš operační systém. Nahraďte `<yourconnectionstring>` skutečným připojovacím řetězcem.

Po přidání proměnné prostředí, budete muset restartovat všechny spuštěné programy, které budou potřebovat číst tuto proměnnou prostředí. Například pokud jako editor používáte Visual Studio, restartujte aplikaci Visual Studio před spuštěním v příkladu.

#### <a name="windows"></a>Windows

```cmd
setx STORAGE_CONNECTION_STRING "<yourconnectionstring>"
```

#### <a name="linux"></a>Linux

```bash
export STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

#### <a name="macos"></a>MacOS

```bash
export STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

## <a name="object-model"></a>Objektový model

Úložiště objektů Blob v Azure je optimalizovaná pro ukládání velkých objemů nestrukturovaných dat. Nestrukturovaných dat jsou data, která nedrží se konkrétního datového modelu nebo definice, jako jsou textová nebo binární data. Úložiště objektů BLOB nabízí tři typy prostředků:

* Účet úložiště.
* Kontejner v účtu úložiště
* Objekt blob v kontejneru

Na následujícím diagramu jsou vztahy těchto prostředků.

![Diagram architektury úložiště objektů Blob](./media/storage-quickstart-blobs-dotnet/blob1.png)

Použijte následující třídy .NET pro interakci s těmito prostředky:

* [CloudStorageAccount](/dotnet/api/microsoft.azure.storage.cloudstorageaccount): **CloudStorageAccount** třída reprezentuje svůj účet úložiště Azure. Tato třída slouží k autorizaci přístupu k Blob storage pomocí přístupové klíče vašeho účtu.
* [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient): **CloudBlobClient** třída poskytuje bod přístup ke službě objektů Blob ve vašem kódu.
* [CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer): **CloudBlobContainer** třída reprezentuje kontejner objektů blob ve vašem kódu.
* [CloudBlockBlob](//dotnet/api/microsoft.azure.storage.blob.cloudblockblob): **CloudBlockBlob** objekt představuje objekt blob bloku v kódu. Objekty blob bloku se skládají z bloků dat, které můžete spravovat jednotlivě.

## <a name="code-examples"></a>Příklady kódu

Tyto příklady fragmentů kódu ukazují, jak provést následující kroky se Klientská knihovna pro úložiště objektů Blob v Azure pro .NET:

   * [Ověření klienta](#authenticate-the-client)
   * [Vytvoření kontejneru](#create-a-container)
   * [Nastavení oprávnění pro kontejner](#set-permissions-on-a-container)
   * [Nahrání objektů BLOB do kontejneru](#upload-blobs-to-a-container)
   * [Výpis objektů BLOB v kontejneru](#list-the-blobs-in-a-container)
   * [Stažení objektů BLOB](#download-blobs)
   * [Odstranění kontejneru](#delete-a-container)

### <a name="authenticate-the-client"></a>Ověření klienta

Následující kód ověří, že proměnná prostředí obsahuje připojovací řetězec, který může být analyzován k vytvoření [CloudStorageAccount](/dotnet/api/microsoft.azure.storage.cloudstorageaccount?view=azure-dotnet) odkazuje na účet úložiště. Ke kontrole platnosti připojovacího řetězce použijte metodu [TryParse](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse?view=azure-dotnet). Pokud se metoda **TryParse** úspěšně provede, inicializuje proměnnou *storageAccount* a vrátí hodnotu **true**.

Přidejte tento kód **ProcessAsync** metody:

```csharp
// Retrieve the connection string for use with the application. The storage 
// connection string is stored in an environment variable on the machine 
// running the application called STORAGE_CONNECTION_STRING. If the 
// environment variable is created after the application is launched in a 
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("STORAGE_CONNECTION_STRING");

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
        "Add an environment variable named 'STORAGE_CONNECTION_STRING' with your storage " +
        "connection string as a value.");
    Console.WriteLine("Press any key to exit the application.");
    Console.ReadLine();
}
```

> [!NOTE]
> Chcete-li provést zbytek operace v tomto článku, nahraďte **/ / přidejte sem další operace** v kódu výše s fragmenty kódu v dalších částech.

### <a name="create-a-container"></a>Vytvoření kontejneru

Pokud chcete vytvořit kontejner, nejprve vytvořte instanci objektu [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient), která odkazuje na úložiště objektů blob ve vašem účtu úložiště. Dále vytvořte instanci objektu [CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer) a pak vytvořte kontejner.

V tomto případě kód volá [asynchronně](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync) metodu pro vytvoření kontejneru. K názvu kontejneru se připojí hodnota GUID, která zajistí jeho jedinečnost. V produkčním prostředí, je často vhodnější použít [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync) metodu pro vytvoření kontejneru, pouze pokud ještě neexistuje.

> [!IMPORTANT]
> Názvy kontejnerů musí obsahovat jen malá písmena. Další informace o pojmenování kontejnerů a objektů blob najdete v tématu [Názvy kontejnerů, objektů blob a metadat a odkazování na ně](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

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

Nastavíte oprávnění ke kontejneru tak, aby žádné objekty BLOB v kontejneru budou veřejné. Když je objekt blob veřejný, může k němu anonymně přistupovat jakýkoli klient.

```csharp
// Set the permissions so the blobs are public.
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-a-container"></a>Nahrání objektů BLOB do kontejneru

Následující fragment kódu získá odkaz na **CloudBlockBlob** objektu voláním [GetBlockBlobReference](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getblockblobreference) metoda kontejner vytvořený v předchozí části. Pak odešle vybraný místní soubor do objektu blob pomocí volání [UploadFromFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromfileasync) metody. Tato metoda vytvoří objekt blob, pokud ještě neexistuje, a přepíše ho, pokud už existoval.

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

Výpis objektů BLOB v kontejneru pomocí [ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync) metody. V takovém případě pouze jeden objekt blob se přidala do kontejneru, takže operace výpisu vrátí jenom tento jeden objekt blob.

Pokud je na vrácení v jednom volání příliš mnoho objektů blob (ve výchozím nastavení více než 5 000), pak metoda **ListBlobsSegmentedAsync** vrátí segment celkové sady výsledků dotazu a token pro pokračování. K načtení dalšího segmentu objektů blob zadáte token pro pokračování vrácený z předchozího volání a tak dále, dokud token pro pokračování není nulový. Nulový token pro pokračování značí, že se načetly všechny objekty blob. Kód ukazuje způsob použití pokračování token souladu s osvědčenými postupy.

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

Stažení objektu blob do vašeho místního systému souborů předtím vytvořili pomocí [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync) metody. Příklad kódu přidá příponu "_DOWNLOADED" k názvu objektu blob, takže se zobrazí oba soubory v místním systému souborů.

```csharp
// Download the blob to a local file, using the reference created earlier.
// Append the string "_DOWNLOADED" before the .txt extension so that you 
// can see both files in MyDocuments.
string destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);
```

### <a name="delete-a-container"></a>Odstranění kontejneru

Následující kód vyčistí prostředky aplikace vytvořila, odstraněním celého kontejneru pomocí [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync). Pokud chcete, můžete odstranit také místní soubory.

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

Tato aplikace vytvoří testovací soubor ve své místní **MyDocuments** složky a nahraje ho do úložiště objektů Blob. V příkladu se pak vypíše objekty BLOB v kontejneru a stáhne soubor s novým názvem, takže můžete porovnat starý a nový soubor.

Pokud jako editor používáte sadu Visual Studio, můžete ukázku spustit stisknutím **F5**.

V opačném případě přejděte do adresáře aplikace a sestavte a spusťte aplikaci.

```console
dotnet build
```

```console
dotnet run
```

Výstup ukázkové aplikaci je podobně jako v následujícím příkladu:

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

Když stisknete klávesu **Enter**, aplikace odstraní kontejner úložiště i soubory. Před jejich odstraněním zkontrolujte, jestli složka **Dokumenty** obsahuje příslušné dva soubory. Můžete je otevřít a podívat se, že jsou identické. Obsah objektu blob můžete zobrazit zkopírováním adresy URL objektu blob z okna konzoly a jejím vložením do prohlížeče.

Po ověření souborů stiskněte libovolnou klávesu a dokončete ukázku a odstraňte testovací soubory.

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste zjistili, jak nahrávat, stahovat a vypisovat objekty blob pomocí .NET.

Chcete-li zjistěte, jak vytvořit webovou aplikaci, která odešle obrázek do úložiště objektů Blob, pokračujte na:

> [!div class="nextstepaction"]
> [Nahrání a zpracování obrazu](storage-upload-process-images.md)

* Další informace o .NET Core najdete v tématu [Začínáme s .NET během 10 minut](https://www.microsoft.com/net/learn/get-started/).
* Pokud chcete prozkoumat ukázkovou aplikaci, kterou můžete nasadit ze sady Visual Studio pro Windows, prohlédněte si [ukázku webové aplikace fotogalerie v .NET s využitím služby Azure Blob Storage](https://azure.microsoft.com/resources/samples/storage-blobs-dotnet-webapp/).
