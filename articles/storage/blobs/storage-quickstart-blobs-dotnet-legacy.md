---
title: 'Rychlý Start: Klientská knihovna pro úložiště objektů BLOB v Azure pro .NET'
description: V tomto rychlém startu se dozvíte, jak pomocí klientské knihovny Azure Blob Storage pro .NET vytvořit kontejner a objekt BLOB v úložišti objektů BLOB (objekt). Dále se dozvíte, jak stáhnout objekt blob do místního počítače a jak zobrazit seznam všech objektů blob, které jsou v kontejneru.
author: twooley
ms.author: twooley
ms.date: 07/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 631b01dadacf81d66c3f42dc1401d2cf492316b8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869234"
---
# <a name="quickstart-azure-blob-storage-client-library-v11-for-net"></a>Rychlý Start: Klientská knihovna pro úložiště objektů BLOB v Azure V11 pro .NET

Začínáme s klientskou knihovnou V11 pro Azure Blob Storage pro .NET Azure Blob Storage je řešení úložiště objektů od Microsoftu pro Cloud. Postupujte podle kroků a nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úlohy. Služba Blob Storage je optimalizovaná pro ukládání velkých objemů nestrukturovaných dat.

> [!NOTE]
> V tomto rychlém startu se používá starší verze klientské knihovny pro úložiště objektů BLOB v Azure. Pokud chcete začít používat nejnovější verzi, přečtěte si téma [rychlý Start: Klientská knihovna Azure Blob Storage V12 pro .NET](storage-quickstart-blobs-dotnet.md).

Použijte klientskou knihovnu Azure Blob Storage pro .NET pro:

* Vytvoření kontejneru
* Nastavení oprávnění pro kontejner
* Vytvoření objektu BLOB v Azure Storage
* Stažení objektu blob do místního počítače
* Výpis všech objektů BLOB v kontejneru
* Odstranění kontejneru

Další prostředky:

* [Referenční dokumentace k rozhraní API](/dotnet/api/overview/azure/storage)
* [Zdrojový kód knihovny](https://github.com/Azure/azure-storage-net/tree/master/Blob)
* [Balíček (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)
* [ukázky](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=blob)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* Účet Azure Storage – [Vytvoření účtu úložiště](../common/storage-account-create.md)
* Aktuální [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) pro váš operační systém. Ujistěte se, že jste získali sadu SDK a ne modul runtime.

## <a name="setting-up"></a>Nastavení

Tato část vás provede přípravou projektu pro práci s klientskou knihovnou Azure Blob Storage pro .NET.

### <a name="create-the-project"></a>Vytvoření projektu

Nejdřív vytvořte aplikaci .NET Core s názvem *BLOB-Started*.

1. V okně konzoly (například cmd, PowerShell nebo bash) použijte `dotnet new` příkaz k vytvoření nové aplikace konzoly s názvem *BLOB – rychlý Start*. Tento příkaz vytvoří jednoduchý projekt C# "Hello World" s jedním zdrojovým souborem: *program. cs*.

   ```console
   dotnet new console -n blob-quickstart
   ```

2. Přepněte do nově vytvořené složky pro *rychlý Start objektů BLOB* a sestavte aplikaci, abyste ověřili, jestli je vše v dobrém.

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

I když v adresáři aplikace ještě pořád nainstalujete balíček klientské knihovny Azure Blob Storage pro .NET pomocí `dotnet add package` příkazu.

```console
dotnet add package Microsoft.Azure.Storage.Blob
```

### <a name="set-up-the-app-framework"></a>Nastavení aplikační architektury

Z adresáře projektu:

1. Otevřete soubor *program. cs* v editoru.
2. Odebrat `Console.WriteLine` příkaz
3. Přidat `using` direktivy
4. Vytvořte `ProcessAsync` metodu, kde bude umístěn hlavní kód pro příklad.
5. Asynchronní volání `ProcessAsync` metody z `Main`

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
        public static async Task Main()
        {
            Console.WriteLine("Azure Blob Storage - .NET quickstart sample\n");

            await ProcessAsync();

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

Když ukázková aplikace odešle požadavek na Azure Storage, musí být ověřena. K autorizaci žádosti přidejte do aplikace přihlašovací údaje účtu úložiště jako připojovací řetězec. Přihlašovací údaje účtu úložiště zobrazíte pomocí následujícího postupu:

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. Vyhledejte svůj účet úložiště.
3. V části **Nastavení** v přehledu účtu úložiště vyberte **Přístupové klíče**. Tady se zobrazí přístupové klíče vašeho účtu a úplný připojovací řetězec pro jednotlivé klíče.
4. V části **key1** vyhledejte hodnotu **Připojovací řetězec** a výběrem tlačítka **Kopírovat** zkopírujte připojovací řetězec. V dalším kroku přidáte hodnotu připojovacího řetězce do proměnné prostředí.

    ![Snímek obrazovky ukazující zkopírování připojovacího řetězce z webu Azure Portal](../../../includes/media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>Konfigurace připojovacího řetězce úložiště

Po zkopírování připojovacího řetězce ho zapište do nové proměnné prostředí na místním počítači, na kterém aplikaci spouštíte. Proměnnou prostředí nastavíte tak, že otevřete okno konzoly a budete postupovat podle pokynů pro váš operační systém. Nahraďte `<yourconnectionstring>` skutečným připojovacím řetězcem.

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

Úložiště objektů BLOB v Azure je optimalizované pro ukládání obrovských objemů nestrukturovaných dat. Jde o data, která nevyhovují konkrétnímu datovému modelu nebo definici, například textová nebo binární data. Úložiště objektů BLOB nabízí tři typy prostředků:

* Účet úložiště.
* Kontejner v účtu úložiště
* Objekt BLOB v kontejneru

Na následujícím diagramu jsou vztahy těchto prostředků.

![Diagram architektury služby Blob Storage](./media/storage-quickstart-blobs-dotnet/blob1.png)

Pro interakci s těmito prostředky použijte následující třídy .NET:

* [CloudStorageAccount](/dotnet/api/microsoft.azure.storage.cloudstorageaccount): `CloudStorageAccount` Třída představuje váš účet služby Azure Storage. Tato třída slouží k autorizaci přístupu k úložišti objektů BLOB pomocí přístupových klíčů k účtu.
* [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient): `CloudBlobClient` Třída poskytuje bod přístupu k BLOB Service ve vašem kódu.
* [CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer): `CloudBlobContainer` Třída představuje kontejner objektů BLOB ve vašem kódu.
* [CloudBlockBlob](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob): `CloudBlockBlob` objekt představuje objekt blob bloku ve vašem kódu. Objekty blob bloku se skládají z bloků dat, které můžete spravovat jednotlivě.

## <a name="code-examples"></a>Příklady kódu

Tyto ukázkové fragmenty kódu ukazují, jak provést následující akce s klientskou knihovnou služby Azure Blob Storage pro .NET:

   * [Ověření klienta](#authenticate-the-client)
   * [Vytvoření kontejneru](#create-a-container)
   * [Nastavení oprávnění pro kontejner](#set-permissions-on-a-container)
   * [Nahrání objektů blob do kontejneru](#upload-blobs-to-a-container)
   * [Seznam objektů blob v kontejneru](#list-the-blobs-in-a-container)
   * [Stáhnout objekty blob](#download-blobs)
   * [Odstranění kontejneru](#delete-a-container)

### <a name="authenticate-the-client"></a>Ověření klienta

Následující kód kontroluje, zda proměnná prostředí obsahuje připojovací řetězec, který lze analyzovat a vytvořit tak objekt [CloudStorageAccount](/dotnet/api/microsoft.azure.storage.cloudstorageaccount) odkazující na účet úložiště. Ke kontrole platnosti připojovacího řetězce použijte metodu [TryParse](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse). Pokud `TryParse` je úspěšná, inicializuje `storageAccount` proměnnou a vrátí `true` .

Přidejte tento kód do `ProcessAsync` metody:

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
> Chcete-li provést zbytek operací v tomto článku, nahraďte `// ADD OTHER OPERATIONS HERE` kód uvedený výše pomocí fragmentů kódu v následujících oddílech.

### <a name="create-a-container"></a>Vytvoření kontejneru

Pokud chcete vytvořit kontejner, nejprve vytvořte instanci objektu [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient), která odkazuje na úložiště objektů blob ve vašem účtu úložiště. Dále vytvořte instanci objektu [CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer) a pak vytvořte kontejner.

V tomto případě kód volá metodu [CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync) pro vytvoření kontejneru. K názvu kontejneru se připojí hodnota GUID, která zajistí jeho jedinečnost. V produkčním prostředí je často vhodnější použít metodu [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync) k vytvoření kontejneru pouze v případě, že ještě neexistuje.

> [!IMPORTANT]
> Názvy kontejnerů musí být malými písmeny. Další informace o pojmenování kontejnerů a objektů blob najdete v tématu [Názvy kontejnerů, objektů blob a metadat a odkazování na ně](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

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

Nastavte oprávnění ke kontejneru tak, aby všechny objekty BLOB v kontejneru byly veřejné. Když je objekt blob veřejný, může k němu anonymně přistupovat jakýkoli klient.

```csharp
// Set the permissions so the blobs are public.
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-a-container"></a>Nahrání objektů blob do kontejneru

Následující fragment kódu získá odkaz na `CloudBlockBlob` objekt voláním metody [GetBlockBlobReference](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getblockblobreference) v kontejneru vytvořeném v předchozí části. Pak nahraje vybraný místní soubor do objektu BLOB voláním metody [UploadFromFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromfileasync) . Tato metoda vytvoří objekt blob, pokud ještě neexistuje, a přepíše ho, pokud už existoval.

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

### <a name="list-the-blobs-in-a-container"></a>Seznam objektů blob v kontejneru

Vypíše objekty BLOB v kontejneru pomocí metody [ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync) . V tomto případě byl do kontejneru přidán pouze jeden objekt blob, takže operace výpisu vrátí pouze jeden objekt BLOB.

Pokud je příliš mnoho objektů blob, které se mají vrátit v jednom volání (ve výchozím nastavení je to více než 5000), pak `ListBlobsSegmentedAsync` Metoda vrátí segment celkové sady výsledků dotazu a token pro pokračování. K načtení dalšího segmentu objektů blob zadáte token pro pokračování vrácený z předchozího volání a tak dále, dokud token pro pokračování není nulový. Nulový token pro pokračování značí, že se načetly všechny objekty blob. Kód ukazuje, jak použít token pro pokračování v zájmu osvědčených postupů.

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

Pomocí metody [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync) Stáhněte objekt blob, který jste dříve vytvořili do svého místního systému souborů. Vzorový kód přidá příponu "_DOWNLOADED" do názvu objektu blob, aby bylo možné zobrazit oba soubory v místním systému souborů.

```csharp
// Download the blob to a local file, using the reference created earlier.
// Append the string "_DOWNLOADED" before the .txt extension so that you 
// can see both files in MyDocuments.
string destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);
```

### <a name="delete-a-container"></a>Odstranění kontejneru

Následující kód vyčistí prostředky, které aplikace vytvořila, odstraněním celého kontejneru pomocí [CloudBlobContainer. DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync). Pokud chcete, můžete odstranit také místní soubory.

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

Tato aplikace vytvoří testovací soubor v místní složce *dokumenty* a nahraje ho do úložiště objektů BLOB. Příklad zobrazí seznam objektů BLOB v kontejneru a stáhne soubor s novým názvem, abyste mohli porovnat staré a nové soubory.

Přejděte do adresáře aplikace a pak Sestavte a spusťte aplikaci.

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

Pokud se chcete dozvědět, jak vytvořit webovou aplikaci, která nahrává obrázek do úložiště objektů blob, pokračujte:

> [!div class="nextstepaction"]
> [Nahrání a zpracování obrázku](storage-upload-process-images.md)

* Další informace o .NET Core najdete v tématu [Začínáme s .NET během 10 minut](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/intro).
* Pokud chcete prozkoumat ukázkovou aplikaci, kterou můžete nasadit ze sady Visual Studio pro Windows, prohlédněte si [ukázku webové aplikace fotogalerie v .NET s využitím služby Azure Blob Storage](https://azure.microsoft.com/resources/samples/storage-blobs-dotnet-webapp/).