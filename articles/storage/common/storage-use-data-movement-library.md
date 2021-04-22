---
title: Přenos dat pomocí knihovny pro přesun dat pro .NET
titleSuffix: Azure Storage
description: Pomocí knihovny pro přesun dat můžete přesunout nebo zkopírovat data z objektu BLOB a souboru. Kopírování dat do Azure Storage z místních souborů nebo kopírování dat v rámci nebo mezi účty úložiště. Snadná migrace dat do Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: f87379f48f82757916aef0fa0d358835f48cb9a5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875930"
---
# <a name="transfer-data-with-the-data-movement-library"></a>Přenos dat s využitím knihovny pro přesun dat

Knihovna pro přesun dat Azure Storage je open source knihovna pro různé platformy, která je určená pro vysoce výkonné nahrávání, stahování a kopírování objektů BLOB a souborů. Knihovna pro přesun dat poskytuje pohodlný způsob, který není dostupný v klientské knihovně Azure Storage pro .NET. Tyto metody poskytují možnost nastavit počet paralelních operací, sledovat průběh přenosu, snadno obnovit zrušený přenos a mnohem víc.

Tato knihovna také využívá .NET Core, což znamená, že ji můžete použít při sestavování aplikací .NET pro Windows, Linux a macOS. Další informace o .NET Core najdete v [dokumentaci k .NET Core](https://dotnet.github.io/). Tato knihovna funguje také pro tradiční aplikace .NET Framework pro Windows.

Tento dokument ukazuje, jak vytvořit konzolovou aplikaci .NET Core, která běží na systémech Windows, Linux a macOS a provádí následující scénáře:

- Nahrajte soubory a adresáře do Blob Storage.
- Definujte počet paralelních operací při přenosu dat.
- Sledujte průběh přenosu dat.
- Obnovit zrušený přenos dat
- Kopírování souboru z adresy URL do Blob Storage.
- Kopírovat z Blob Storage do Blob Storage

## <a name="prerequisites"></a>Požadavky

- [Visual Studio Code](https://code.visualstudio.com/)
- [Účet úložiště Azure](storage-account-create.md)

## <a name="setup"></a>Nastavení

1. Instalaci .NET Core SDK najdete v [Průvodci instalací .NET Core](https://dotnet.microsoft.com/download) . Při výběru prostředí zvolte možnost příkazového řádku.
2. Z příkazového řádku vytvořte adresář pro váš projekt. Přejděte do tohoto adresáře a pak zadejte `dotnet new console -o <sample-project-name>` a vytvořte projekt konzoly v jazyce C#.
3. Otevřete tento adresář v Visual Studio Code. Tento krok můžete rychle provést pomocí příkazového řádku zadáním `code .` v systému Windows.
4. Nainstalujte [rozšíření C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) z webu Visual Studio Code Marketplace. Restartujte Visual Studio Code.
5. V tomto okamžiku byste měli vidět dvě výzvy. Jedna je určena k přidání "požadovaných assetů pro sestavení a ladění". Klikněte na tlačítko Ano. Další výzvou pro obnovení nevyřešených závislostí. Klikněte na tlačítko obnovit.
6. Upravte `launch.json` v části `.vscode` na použít externí terminál jako konzolu. Toto nastavení by se mělo číst jako `"console": "externalTerminal"`
7. Visual Studio Code umožňuje ladit aplikace .NET Core. Stiskněte `F5` ke spuštění aplikace a ověřte, že instalace funguje. Měl by se zobrazit "Hello World!" vytištěno do konzoly.

## <a name="add-the-data-movement-library-to-your-project"></a>Přidání knihovny pro přesun dat do projektu

1. Přidejte nejnovější verzi knihovny pro přesun dat do `dependencies` části `<project-name>.csproj` souboru. V době psaní by tato verze byla `"Microsoft.Azure.Storage.DataMovement": "0.6.2"`
2. Zobrazí se výzva k obnovení projektu. Klikněte na tlačítko obnovit. Svůj projekt můžete také obnovit z příkazového řádku zadáním příkazu `dotnet restore` v kořenu adresáře projektu.

Upravit `<project-name>.csproj` :

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Storage.DataMovement" Version="0.6.2" />
        </ItemGroup>
    </Project>
```

## <a name="set-up-the-skeleton-of-your-application"></a>Nastavení kostry aplikace

První věc, kterou jsme provedli, je nastavení "kostry" naší aplikace. Tento kód vás vyzve k zadání názvu účtu úložiště a klíče účtu a použije tyto přihlašovací údaje k vytvoření `CloudStorageAccount` objektu. Tento objekt se používá k interakci s naším účtem úložiště ve všech scénářích přenosu. Kód také vyzve k výběru typu operace přenosu, kterou bychom chtěli spustit.

Upravit `Program.cs` :

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using System.Diagnostics;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;
using Microsoft.Azure.Storage.DataMovement;

namespace DMLibSample
{
    public class Program
    {
        public static void Main()
        {
            Console.WriteLine("Enter Storage account name:");
            string accountName = Console.ReadLine();

            Console.WriteLine("\nEnter Storage account key:");
            string accountKey = Console.ReadLine();

            string storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=" + accountName + ";AccountKey=" + accountKey;
            CloudStorageAccount account = CloudStorageAccount.Parse(storageConnectionString);

            ExecuteChoice(account);
        }

        public static void ExecuteChoice(CloudStorageAccount account)
        {
            Console.WriteLine("\nWhat type of transfer would you like to execute?\n1. Local file --> Azure Blob\n2. Local directory --> Azure Blob directory\n3. URL (e.g. Amazon S3 file) --> Azure Blob\n4. Azure Blob --> Azure Blob");
            int choice = int.Parse(Console.ReadLine());

            if(choice == 1)
            {
                TransferLocalFileToAzureBlob(account).Wait();
            }
            else if(choice == 2)
            {
                TransferLocalDirectoryToAzureBlobDirectory(account).Wait();
            }
            else if(choice == 3)
            {
                TransferUrlToAzureBlob(account).Wait();
            }
            else if(choice == 4)
            {
                TransferAzureBlobToAzureBlob(account).Wait();
            }
        }

        public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
        {

        }

        public static async Task TransferLocalDirectoryToAzureBlobDirectory(CloudStorageAccount account)
        {

        }

        public static async Task TransferUrlToAzureBlob(CloudStorageAccount account)
        {

        }

        public static async Task TransferAzureBlobToAzureBlob(CloudStorageAccount account)
        {

        }
    }
}
```

## <a name="upload-a-local-file-to-a-blob"></a>Nahrání místního souboru do objektu BLOB

Přidejte metody `GetSourcePath` a `GetBlob` pro `Program.cs` :

```csharp
public static string GetSourcePath()
{
    Console.WriteLine("\nProvide path for source:");
    string sourcePath = Console.ReadLine();

    return sourcePath;
}

public static CloudBlockBlob GetBlob(CloudStorageAccount account)
{
    CloudBlobClient blobClient = account.CreateCloudBlobClient();

    Console.WriteLine("\nProvide name of Blob container:");
    string containerName = Console.ReadLine();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExistsAsync().Wait();

    Console.WriteLine("\nProvide name of new Blob:");
    string blobName = Console.ReadLine();
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    return blob;
}
```

Upravte `TransferLocalFileToAzureBlob` metodu:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    Console.WriteLine("\nTransfer started...");
    await TransferManager.UploadAsync(localFilePath, blob);
    Console.WriteLine("\nTransfer operation complete.");
    ExecuteChoice(account);
}
```

Tento kód vás vyzve pro cestu k místnímu souboru, název nového nebo existujícího kontejneru a název nového objektu BLOB. `TransferManager.UploadAsync`Metoda provede odeslání pomocí těchto informací.

Stiskněte `F5` ke spuštění aplikace. Můžete ověřit, že k nahrávání došlo, zobrazením účtu úložiště pomocí [Průzkumník služby Microsoft Azure Storage](https://storageexplorer.com/).

## <a name="set-the-number-of-parallel-operations"></a>Nastavení počtu paralelních operací

Jednou funkcí nabízená knihovnou přesunu dat je schopnost nastavit počet paralelních operací, aby se zvýšila propustnost přenosu dat. Knihovna pro přesun dat ve výchozím nastavení nastavuje počet paralelních operací na 8 * počet jader v počítači.

Mějte na paměti, že mnoho paralelních operací v prostředí s nízkou šířkou pásma může být zahlcené síťovým připojením a ve skutečnosti zabránit kompletnímu dokončení operací. Abyste zjistili, co nejlépe funguje na základě dostupné šířky pásma sítě, budete muset experimentovat s tímto nastavením.

Pojďme přidat nějaký kód, který nám umožní nastavit počet paralelních operací. Pojďme také přidat kód, který krát, jak dlouho trvá dokončení přenosu.

Přidejte `SetNumberOfParallelOperations` metodu do `Program.cs` :

```csharp
public static void SetNumberOfParallelOperations()
{
    Console.WriteLine("\nHow many parallel operations would you like to use?");
    string parallelOperations = Console.ReadLine();
    TransferManager.Configurations.ParallelOperations = int.Parse(parallelOperations);
}
```

Upravte `ExecuteChoice` metodu, která se má použít `SetNumberOfParallelOperations` :

```csharp
public static void ExecuteChoice(CloudStorageAccount account)
{
    Console.WriteLine("\nWhat type of transfer would you like to execute?\n1. Local file --> Azure Blob\n2. Local directory --> Azure Blob directory\n3. URL (e.g. Amazon S3 file) --> Azure Blob\n4. Azure Blob --> Azure Blob");
    int choice = int.Parse(Console.ReadLine());

    SetNumberOfParallelOperations();

    if(choice == 1)
    {
        TransferLocalFileToAzureBlob(account).Wait();
    }
    else if(choice == 2)
    {
        TransferLocalDirectoryToAzureBlobDirectory(account).Wait();
    }
    else if(choice == 3)
    {
        TransferUrlToAzureBlob(account).Wait();
    }
    else if(choice == 4)
    {
        TransferAzureBlobToAzureBlob(account).Wait();
    }
}
```

Upravte `TransferLocalFileToAzureBlob` metodu pro použití časovače:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    Console.WriteLine("\nTransfer started...");
    Stopwatch stopWatch = Stopwatch.StartNew();
    await TransferManager.UploadAsync(localFilePath, blob);
    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

## <a name="track-transfer-progress"></a>Průběh přenosu sledovat

Je užitečné vědět, jak dlouho trvalo přenosu dat. Nicméně schopnost zobrazit průběh přenosu *během* operace přenosu by byla ještě lepší. Pro dosažení tohoto scénáře musíme vytvořit `TransferContext` objekt. `TransferContext`Objekt se nachází ve dvou formách: `SingleTransferContext` a `DirectoryTransferContext` . Předchozí je pro přenos jednoho souboru a druhý pro přenos adresáře souborů.

Přidejte metody `GetSingleTransferContext` a `GetDirectoryTransferContext` pro `Program.cs` :

```csharp
public static SingleTransferContext GetSingleTransferContext(TransferCheckpoint checkpoint)
{
    SingleTransferContext context = new SingleTransferContext(checkpoint);

    context.ProgressHandler = new Progress<TransferStatus>((progress) =>
    {
        Console.Write("\rBytes transferred: {0}", progress.BytesTransferred );
    });

    return context;
}

public static DirectoryTransferContext GetDirectoryTransferContext(TransferCheckpoint checkpoint)
{
    DirectoryTransferContext context = new DirectoryTransferContext(checkpoint);

    context.ProgressHandler = new Progress<TransferStatus>((progress) =>
    {
        Console.Write("\rBytes transferred: {0}", progress.BytesTransferred );
    });

    return context;
}
```

Upravte `TransferLocalFileToAzureBlob` metodu, která se má použít `GetSingleTransferContext` :

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    Console.WriteLine("\nTransfer started...\n");
    Stopwatch stopWatch = Stopwatch.StartNew();
    await TransferManager.UploadAsync(localFilePath, blob, null, context);
    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

## <a name="resume-a-canceled-transfer"></a>Obnovit zrušený přenos

Další pohodlnější funkce nabízená knihovnou přesunu dat je schopnost obnovit zrušený přenos. Pojďme přidat kód, který nám umožní dočasně zrušit přenos zadáním `c` a potom pokračovat v přenosu 3 sekundy později.

Upravit `TransferLocalFileToAzureBlob` :

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.UploadAsync(localFilePath, blob, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.UploadAsync(localFilePath, blob, null, context);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

V současné době je naše `checkpoint` hodnota vždycky nastavená na `null` . Když teď tento přenos zrušíte, načteme poslední kontrolní bod pro náš přenos a pak tento nový kontrolní bod použijete v našem kontextu přenosu.

## <a name="transfer-a-local-directory-to-blob-storage"></a>Přenos místního adresáře do úložiště objektů BLOB

By se disappointing, pokud by knihovna pro přesun dat mohla najednou přenést jenom jeden soubor. Donovanovo, nejedná se o případ. Knihovna pro přesun dat poskytuje možnost přenosu adresáře souborů a všech jeho podadresářů. Pojďme přidat kód, který nám umožní to udělat jenom to.

Nejprve přidejte metodu `GetBlobDirectory` do `Program.cs` :

```csharp
public static CloudBlobDirectory GetBlobDirectory(CloudStorageAccount account)
{
    CloudBlobClient blobClient = account.CreateCloudBlobClient();

    Console.WriteLine("\nProvide name of Blob container. This can be a new or existing Blob container:");
    string containerName = Console.ReadLine();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExistsAsync().Wait();

    CloudBlobDirectory blobDirectory = container.GetDirectoryReference("");

    return blobDirectory;
}
```

Pak změňte `TransferLocalDirectoryToAzureBlobDirectory` :

```csharp
public static async Task TransferLocalDirectoryToAzureBlobDirectory(CloudStorageAccount account)
{
    string localDirectoryPath = GetSourcePath();
    CloudBlobDirectory blobDirectory = GetBlobDirectory(account);
    TransferCheckpoint checkpoint = null;
    DirectoryTransferContext context = GetDirectoryTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    UploadDirectoryOptions options = new UploadDirectoryOptions()
    {
        Recursive = true
    };

    try
    {
        task = TransferManager.UploadDirectoryAsync(localDirectoryPath, blobDirectory, options, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetDirectoryTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.UploadDirectoryAsync(localDirectoryPath, blobDirectory, options, context);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Mezi touto metodou a metodou pro nahrání jednoho souboru existuje několik rozdílů. Nyní používáme `TransferManager.UploadDirectoryAsync` a metodu, kterou `getDirectoryTransferContext` jsme vytvořili dříve. Nyní teď poskytujeme `options` hodnotu pro naši operaci nahrávání, což nám umožňuje indikovat, že chceme do nahrávání zahrnout podadresáře.

## <a name="copy-a-file-from-url-to-a-blob"></a>Kopírování souboru z adresy URL do objektu BLOB

Teď přidáme kód, který nám umožní zkopírovat soubor z adresy URL do objektu blob Azure.

Upravit `TransferUrlToAzureBlob` :

```csharp
public static async Task TransferUrlToAzureBlob(CloudStorageAccount account)
{
    Uri uri = new Uri(GetSourcePath());
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.CopyAsync(uri, blob, true, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.CopyAsync(uri, blob, true, null, context, cancellationSource.Token);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Jedním z důležitých případů použití této funkce je situace, kdy potřebujete přesunout data z jiné cloudové služby (např. AWS) do Azure. Pokud máte adresu URL, která vám poskytne přístup k prostředku, můžete tento prostředek snadno přesunout do objektů blob Azure pomocí `TransferManager.CopyAsync` metody. Tato metoda také zavádí nový logický parametr. Nastavením tohoto parametru na `true` označuje, že chceme provést asynchronní kopírování na straně serveru. Nastavením tohoto parametru na `false` indikujeme synchronní kopii – což znamená, že se prostředek stáhne do našeho místního počítače a pak se nahraje do objektu blob Azure. Synchronní kopie je však nyní k dispozici pouze pro kopírování z jednoho prostředku Azure Storage do jiného.

## <a name="copy-a-blob"></a>Kopírování objektu BLOB

Jiná funkce, která je jednoznačně poskytována knihovnou přesunu dat, je možnost kopírování z jednoho prostředku Azure Storage do jiného.

Upravit `TransferAzureBlobToAzureBlob` :

```csharp
public static async Task TransferAzureBlobToAzureBlob(CloudStorageAccount account)
{
    CloudBlockBlob sourceBlob = GetBlob(account);
    CloudBlockBlob destinationBlob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.CopyAsync(sourceBlob, destinationBlob, CopyMethod.ServiceSideAsyncCopy, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.CopyAsync(sourceBlob, destinationBlob, false, null, context, cancellationSource.Token);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

V tomto příkladu nastavíme logický parametr v pro, `TransferManager.CopyAsync` `false` aby označoval, že chceme provést synchronní kopii. To znamená, že se prostředek stáhne nejdřív do našeho místního počítače a pak se nahraje do objektu blob Azure. Možnost synchronní kopírování představuje skvělý způsob, jak zajistit, aby operace kopírování měla konzistentní rychlost. Naproti tomu rychlost asynchronního kopírování na straně serveru závisí na dostupné šířce pásma sítě na serveru, která může kolísat. Synchronní kopírování ale může v porovnání s asynchronní kopírováním způsobit další náklady na výstup. Doporučený postup je použít synchronní kopii na virtuálním počítači Azure, který je ve stejné oblasti jako zdrojový účet úložiště, abyste předešli nákladům na výstup.

Aplikace pro přesun dat je teď dokončená. [Kompletní ukázka kódu je k dispozici na GitHubu](https://github.com/azure-samples/storage-dotnet-data-movement-library-app).

## <a name="next-steps"></a>Další kroky

[Azure Storage referenční dokumentaci knihovny pro přesun dat](https://azure.github.io/azure-storage-net-data-movement).

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]
