---
title: Přenos dat pomocí knihovny Pohyb dat pro rozhraní .NET
titleSuffix: Azure Storage
description: Knihovna Přesun dat slouží k přesouvání nebo kopírování dat do nebo z objektu blob a obsahu souborů. Zkopírujte data do Služby Azure Storage z místních souborů nebo zkopírujte data v rámci účtů úložiště nebo mezi nimi. Snadno migrujte data do Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 5b8654500fd697685b38e4f51ba1069e0cf6ccfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942907"
---
# <a name="transfer-data-with-the-data-movement-library"></a>Přenos dat s využitím knihovny pro přesun dat

Knihovna Azure Storage Data Movement je knihovna s otevřeným zdrojovým kódem pro různé platformy, která je navržená pro vysoce výkonné nahrávání, stahování a kopírování objektů BLOB a souborů. Knihovna Přesun dat poskytuje pohodlné metody, které nejsou k dispozici v klientské knihovně Úložiště Azure pro .NET. Tyto metody poskytují možnost nastavit počet paralelních operací, sledovat průběh přenosu, snadno obnovit zrušený přenos a mnoho dalšího.

Tato knihovna také používá .NET Core, což znamená, že ji můžete použít při vytváření aplikací .NET pro Windows, Linux a macOS. Další informace o jádru .NET naleznete v [dokumentaci .NET Core](https://dotnet.github.io/). Tato knihovna funguje také pro tradiční aplikace rozhraní .NET Framework pro Windows.

Tento dokument ukazuje, jak vytvořit konzolovou aplikaci .NET Core, která běží ve Windows, Linuxu a macOS a provede následující scénáře:

- Nahrajte soubory a adresáře do úložiště objektů Blob.
- Definujte počet paralelních operací při přenosu dat.
- Sledujte průběh přenosu dat.
- Pokračovat v zrušeném přenosu dat.
- Zkopírujte soubor z adresy URL do úložiště objektů Blob.
- Kopírování z úložiště objektů blob do úložiště objektů blob.

## <a name="prerequisites"></a>Požadavky

- [Kód visual studia](https://code.visualstudio.com/)
- [Účet úložiště Azure](storage-account-create.md)

## <a name="setup"></a>Nastavení

1. Chcete-li nainstalovat [rozhraní .NET Core,](https://www.microsoft.com/net/core) navštivte příručku k instalaci rozhraní .NET Core. Při výběru prostředí zvolte možnost příkazového řádku.
2. Z příkazového řádku vytvořte adresář pro projekt. Přejděte do tohoto `dotnet new console -o <sample-project-name>` adresáře a zadejte a vytvořte projekt konzoly Jazyka C#.
3. Otevřete tento adresář v kódu sady Visual Studio. Tento krok lze rychle provést prostřednictvím `code .` příkazového řádku zadáním v systému Windows.
4. Nainstalujte [rozšíření C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) z webu Visual Studio Code Marketplace. Restartujte kód sady Visual Studio.
5. V tomto okamžiku byste měli vidět dvě výzvy. Jedním z nich je pro přidání "požadované prostředky pro sestavení a ladění." Klikněte na "ano". Další výzva je pro obnovení nevyřešených závislostí. Klikněte na tlačítko Obnovit.
6. Upravit `launch.json` `.vscode` pod použít externí terminál jako konzolu. Toto nastavení by mělo být`"console": "externalTerminal"`
7. Visual Studio Code umožňuje ladit aplikace .NET Core. Chcete-li spustit aplikaci a ověřit, zda nastavení funguje, stiskněte tak `F5` to, že aplikace funguje. Měl bys vidět "Hello World!" vytištěny na konzoli.

## <a name="add-the-data-movement-library-to-your-project"></a>Přidání knihovny Přesun dat do projektu

1. Přidejte nejnovější verzi knihovny Přesun `dependencies` dat `<project-name>.csproj` do oddílu souboru. V době psaní tohoto článku by tato verze byla`"Microsoft.Azure.Storage.DataMovement": "0.6.2"`
2. K obnovení projektu by se měla zobrazit výzva. Klikněte na tlačítko "obnovit". Projekt můžete také obnovit z příkazového řádku `dotnet restore` zadáním příkazu do kořenového adresáře projektu.

Změnit `<project-name>.csproj`:

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

První věc, kterou uděláme, je nastavit "kostru" kód naší aplikace. Tento kód nás vyzve k zadání názvu účtu úložiště a klíče `CloudStorageAccount` účtu a použije tato pověření k vytvoření objektu. Tento objekt se používá k interakci s naším účtem úložiště ve všech scénářích přenosu. Kód nás také vyzve k výběru typu operace přenosu, kterou bychom chtěli provést.

Změnit `Program.cs`:

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

## <a name="upload-a-local-file-to-a-blob"></a>Nahrání místního souboru do objektu blob

Přidejte `GetSourcePath` metody `GetBlob` `Program.cs`a do :

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

Tento kód nás vyzve k cestě k místnímu souboru, název nového nebo existujícího kontejneru a název nového objektu blob. Metoda `TransferManager.UploadAsync` provádí nahrávání pomocí těchto informací.

Hitem `F5` spustíte aplikaci. K nahrání můžete ověřit zobrazením účtu úložiště v [Průzkumníku úložiště Microsoft Azure](https://storageexplorer.com/).

## <a name="set-the-number-of-parallel-operations"></a>Nastavení počtu paralelních operací

Jednou z funkcí, kterou nabízí knihovna Přesun dat, je možnost nastavit počet paralelních operací pro zvýšení propustnosti přenosu dat. Ve výchozím nastavení knihovna Přesun dat nastaví počet paralelních operací na 8 * počet jader v počítači.

Mějte na paměti, že mnoho paralelních operací v prostředí s malou šířkou pásma může zahltit síťové připojení a ve skutečnosti zabránit úplnému dokončení operací. Budete muset experimentovat s tímto nastavením, abyste zjistili, co nejlépe funguje na základě dostupné šířky pásma sítě.

Přidáme nějaký kód, který nám umožňuje nastavit počet paralelních operací. Přidáme také kód, který krát, jak dlouho trvá dokončení přenosu.

Přidejte `SetNumberOfParallelOperations` metodu do `Program.cs`:

```csharp
public static void SetNumberOfParallelOperations()
{
    Console.WriteLine("\nHow many parallel operations would you like to use?");
    string parallelOperations = Console.ReadLine();
    TransferManager.Configurations.ParallelOperations = int.Parse(parallelOperations);
}
```

Upravte `ExecuteChoice` metodu, která má být používána `SetNumberOfParallelOperations`:

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

Upravte `TransferLocalFileToAzureBlob` metodu tak, aby používala časovač:

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

## <a name="track-transfer-progress"></a>Sledování průběhu přenosu

Vědět, jak dlouho trvalo přenos dat, je užitečné. Možnost vidět průběh převodu *během* operace převodu by však byla ještě lepší. K dosažení tohoto scénáře, musíme vytvořit `TransferContext` objekt. Objekt `TransferContext` je dodáván ve `SingleTransferContext` `DirectoryTransferContext`dvou formách: a . První z nich je pro přenos jednoho souboru a druhý je pro přenos adresáře souborů.

Přidejte `GetSingleTransferContext` metody `GetDirectoryTransferContext` `Program.cs`a do :

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

Upravte `TransferLocalFileToAzureBlob` metodu, která má být používána `GetSingleTransferContext`:

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

## <a name="resume-a-canceled-transfer"></a>Obnovení zrušeného přenosu

Další praktickou funkcí, kterou knihovna Data Movement nabízí, je možnost pokračovat v zrušeném přenosu. Přidáme nějaký kód, který nám umožňuje dočasně zrušit `c`přenos zadáním a poté pokračovat v přenosu o 3 sekundy později.

Změnit `TransferLocalFileToAzureBlob`:

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

Až do teď `checkpoint` byla naše hodnota `null`vždy nastavena na . Pokud zrušíme převod, získáme poslední kontrolní bod našeho převodu a použijeme tento nový kontrolní bod v našem kontextu přenosu.

## <a name="transfer-a-local-directory-to-blob-storage"></a>Přenos místního adresáře do úložiště objektů Blob

Bylo by zklamáním, kdyby knihovna Přesun dat mohla přenášet pouze jeden soubor najednou. Naštěstí tomu tak není. Knihovna Přesun dat umožňuje přenášet adresář souborů a všechny jeho podadresáře. Pojďme přidat nějaký kód, který nám umožňuje udělat právě to.

Nejprve přidejte `GetBlobDirectory` `Program.cs`metodu do :

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

Potom upravte `TransferLocalDirectoryToAzureBlobDirectory`:

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

Existuje několik rozdílů mezi touto metodou a metodou pro nahrávání jednoho souboru. Nyní používáme `TransferManager.UploadDirectoryAsync` a `getDirectoryTransferContext` metodu, kterou jsme vytvořili dříve. Kromě toho nyní poskytujeme hodnotu `options` naší operace nahrávání, což nám umožňuje označit, že chceme zahrnout podadresáře do našeho nahrávání.

## <a name="copy-a-file-from-url-to-a-blob"></a>Kopírování souboru z adresy URL do objektu blob

Teď přidáme kód, který nám umožňuje zkopírovat soubor z adresy URL do objektu Blob Azure.

Změnit `TransferUrlToAzureBlob`:

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

Jeden důležitý případ použití pro tuto funkci je, když potřebujete přesunout data z jiné cloudové služby (např. AWS) do Azure. Pokud máte adresu URL, která vám poskytuje přístup k prostředku, můžete snadno přesunout `TransferManager.CopyAsync` tento prostředek do objektů Blob Azure pomocí metody. Tato metoda také zavádí nový logický parametr. Nastavení tohoto `true` parametru označuje, že chceme provést asynchronní kopii na straně serveru. Nastavení tohoto `false` parametru označuje synchronní kopii – což znamená, že prostředek se nejprve stáhne do místního počítače a pak se nahraje do objektu Blob Azure. Synchronní kopie je však aktuálně k dispozici pouze pro kopírování z jednoho prostředku úložiště Azure do jiného.

## <a name="copy-a-blob"></a>Kopírování objektu blob

Další funkcí, která je jedinečně poskytovaná knihovnou Přesun dat, je možnost kopírování z jednoho prostředku úložiště Azure do jiného.

Změnit `TransferAzureBlobToAzureBlob`:

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
        task = TransferManager.CopyAsync(sourceBlob, destinationBlob, true, null, context, cancellationSource.Token);
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

V tomto příkladu jsme nastavili `TransferManager.CopyAsync` `false` logický parametr v označující, že chceme provést synchronní kopii. To znamená, že prostředek se nejprve stáhne do našeho místního počítače a pak se nahraje do objektu Blob Azure. Možnost synchronní kopie je skvělý způsob, jak zajistit, že operace kopírování má konzistentní rychlost. Naproti tomu rychlost asynchronní kopie na straně serveru závisí na dostupné šířce pásma sítě na serveru, která může kolísat. Synchronní kopie však může generovat další náklady na odchozí přenos ve srovnání s asynchronní kopírování. Doporučeným přístupem je použití synchronní kopie ve virtuálním počítači Azure, který je ve stejné oblasti jako účet zdrojového úložiště, aby se zabránilo nákladům na odchozí přenos.

Aplikace pro přesun dat je nyní dokončena. [Ukázka celého kódu je k dispozici na GitHubu](https://github.com/azure-samples/storage-dotnet-data-movement-library-app).

## <a name="next-steps"></a>Další kroky

[Referenční dokumentace ke knihovně Azure Storage Data Movement](https://azure.github.io/azure-storage-net-data-movement).

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]
