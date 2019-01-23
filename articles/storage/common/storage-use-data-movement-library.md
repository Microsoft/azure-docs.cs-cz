---
title: Přenos dat pomocí knihovna pro přesun dat úložiště Microsoft Azure | Dokumentace Microsoftu
description: Knihovna pro přesun dat pomocí Pokud chcete přesunout nebo kopírovat data do nebo z objektu blob a souboru obsahu. Kopírování dat do služby Azure Storage z místních souborů nebo kopírování dat v rámci nebo mezi účty úložiště. Snadno migrate data do služby Azure Storage.
services: storage
author: seguler
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 09/27/2017
ms.author: seguler
ms.component: common
ms.openlocfilehash: 809010727f0caf2e340bb62fff24e5ffff52f88a
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54464676"
---
# <a name="transfer-data-with-the-microsoft-azure-storage-data-movement-library"></a>Přenos dat pomocí knihovna pro přesun dat úložiště Microsoft Azure

## <a name="overview"></a>Přehled
Knihovna pro přesun dat aplikace Microsoft Azure Storage je multiplatformní opensourcové knihovny, který je určený pro vysokovýkonné nahrávání, stahování a kopírování objektů BLOB Azure Storage a souborů. Tato knihovna je základní platformě pro přesun dat, která je základem [AzCopy](../storage-use-azcopy.md). Knihovna pro přesun dat poskytuje vhodné metody, které nejsou dostupné v našich tradičních [Klientská knihovna Azure Storage .NET](../blobs/storage-dotnet-how-to-use-blobs.md). To zahrnuje možnost nastavit počet paralelních operací, sledování průběhu přenosu, snadno obnovit zrušené přenos a spoustu dalších věcí.

Tato knihovna také používá .NET Core, což znamená, že se použije při sestavování aplikací .NET pro Windows, Linux a macOS. Další informace o .NET Core, najdete v tématu [dokumentace k .NET Core](https://dotnet.github.io/). Tato knihovna funguje i pro tradiční aplikace rozhraní .NET Framework pro Windows.

Tento dokument ukazuje, jak vytvořit konzolovou aplikaci .NET Core, který běží na Windows, Linux a macOS a provádí následující scénáře:

- Nahrávání souborů a adresářů do úložiště objektů Blob.
- Při přenosu dat, definujte počet paralelních operací.
- Sledování průběhu přenosu dat.
- Přenos dat obnovení bylo zrušeno.
- Zkopírujte soubor z adresy URL do úložiště objektů Blob.
- Zkopírujte z úložiště objektů Blob do služby Blob Storage.

**Co budete potřebovat:**

* [Visual Studio Code](https://code.visualstudio.com/)
* [Účet úložiště Azure](storage-quickstart-create-account.md)

> [!NOTE]
> Tato příručka předpokládá, že jste už obeznámení s [služby Azure Storage](https://azure.microsoft.com/services/storage/). Pokud ne, čtení [Úvod do služby Azure Storage](storage-introduction.md) dokumentace je užitečné. Co je nejdůležitější, budete muset [vytvořit účet úložiště](storage-quickstart-create-account.md) chcete začít používat knihovna pro přesun dat.
>
>

## <a name="setup"></a>Nastavení

1. Přejděte [Průvodce instalací rozhraní .NET Core](https://www.microsoft.com/net/core) instalace .NET Core. Při výběru prostředí, zvolte možnost příkazového řádku.
2. Z příkazového řádku vytvořte adresář pro váš projekt. Přejděte do adresáře, zadejte `dotnet new console -o <sample-project-name>` vytvořit projekt konzoly C#.
3. Otevřete tento adresář v aplikaci Visual Studio Code. Tento krok lze rychle provést prostřednictvím příkazového řádku zadáním `code .` ve Windows.
4. Nainstalujte [rozšíření jazyka C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) z webu Visual Studio Code Marketplace. Restartujte Visual Studio Code.
5. V tomto okamžiku byste měli vidět dvě výzvy. Jedna je pro přidání "požadované prostředky pro sestavení a ladění." Klikněte na tlačítko "Ano". Další výzva je určena pro obnovení nevyřešené závislosti. Klikněte na tlačítko "obnovit".
6. Upravit `launch.json` pod `.vscode` používat externím terminálu jako konzola. Toto nastavení by měl čtení jako ` "console": "externalTerminal"`
7. Visual Studio Code umožňuje ladit aplikace .NET Core. Spuštění `F5` ke spuštění vaší aplikace a ověřte, zda je funkční vašeho nastavení. Měli byste vidět "Hello World!" Tisk do konzoly.

## <a name="add-data-movement-library-to-your-project"></a>Přidání knihovna pro přesun dat do projektu

1. Přidejte nejnovější verzi knihovna pro přesun dat do `dependencies` část vaší `<project-name>.csproj` souboru. V době psaní bude tato verze `"Microsoft.Azure.Storage.DataMovement": "0.6.2"`
2. By se zobrazit výzva k obnovení vašeho projektu. Klikněte na tlačítko "obnovit". Můžete také obnovit váš projekt z příkazového řádku zadáním příkazu `dotnet restore` v kořenovém adresáři projektu.

Modify `<project-name>.csproj`:

    <Project Sdk="Microsoft.NET.Sdk">

        <PropertyGroup>
            <OutputType>Exe</OutputType>
            <TargetFramework>netcoreapp2.0</TargetFramework>
        </PropertyGroup>
        <ItemGroup>
            <PackageReference Include="Microsoft.Azure.Storage.DataMovement" Version="0.6.2" />
            </ItemGroup>
        </Project>

## <a name="set-up-the-skeleton-of-your-application"></a>Nastavit kostra aplikace
První věc, kterou uděláme je nastavený kód "kostra" naší aplikace. Tento kód nám vyzve k zadání účet názvu a klíče účtu úložiště a použije tyto přihlašovací údaje k vytvoření `CloudStorageAccount` objektu. Tento objekt se používá k interakci s náš účet úložiště ve všech scénářích přenosu. Kód také vyžaduje, abychom mohli zvolit typ operace přenosu, kterou jsme chtěli provést.

Modify `Program.cs`:

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using System.Diagnostics;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.DataMovement;

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

## <a name="transfer-local-file-to-azure-blob"></a>Přesunout místní soubor do objektu Blob Azure
Přidejte metody `GetSourcePath` a `GetBlob` k `Program.cs`:

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

Upravit `TransferLocalFileToAzureBlob` metody:

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

Tento kód zobrazí výzvu nám pro cestu do místního souboru, název nové nebo existující kontejneru a názvem nový objekt blob. `TransferManager.UploadAsync` Metoda provádí samotné nahrávání pomocí těchto informací.

Spuštění `F5` ke spuštění aplikace. Můžete ověřit, že nahrávání došlo k účtu úložiště se zobrazením [Microsoft Azure Storage Explorer](http://storageexplorer.com/).

## <a name="set-number-of-parallel-operations"></a>Stanovený počet paralelních operací
Skvělé funkce nabízené knihovna pro přesun dat je možnost nastavit počet paralelních operací, pokud chcete zvýšit propustnost dat přenos. Ve výchozím nastavení, knihovna pro přesun dat nastaví počet paralelních operací na 8 * počet jader na svém počítači.

Mějte na paměti, že mnoho paralelních operací v prostředí s malou šířkou pásma může zahlcovat připojení k síti a ve skutečnosti zabránit operace plně dokončit. Bude potřeba experimentovat s tohoto nastavení můžete určit, co funguje nejlépe na základě na vaši dostupnou šířku pásma sítě.

Přidejme nějaký kód, který umožňuje nastavit počet paralelních operací. Můžeme také přidejte kód, který vyprší, jak dlouho trvá přenos dokončí.

Přidat `SetNumberOfParallelOperations` metodu `Program.cs`:

```csharp
public static void SetNumberOfParallelOperations()
{
    Console.WriteLine("\nHow many parallel operations would you like to use?");
    string parallelOperations = Console.ReadLine();
    TransferManager.Configurations.ParallelOperations = int.Parse(parallelOperations);
}
```

Upravit `ExecuteChoice` metodu použít `SetNumberOfParallelOperations`:

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

Upravit `TransferLocalFileToAzureBlob` metoda pomocí časovače:

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
Znalost, jak dlouho trvalo pro naše data pro přenos je skvělé. Ale nebudou moct zobrazit průběh naše přenos *během* operace přenosu by to ještě lepší. K dosažení tohoto scénáře, je třeba vytvořit `TransferContext` objektu. `TransferContext` Objektu je k dispozici ve dvou formách: `SingleTransferContext` a `DirectoryTransferContext`. Je pro přenos jednoho souboru (což je, co děláme My teď) a je pro přenos adresář souborů (které přidáváme později).

Přidejte metody `GetSingleTransferContext` a `GetDirectoryTransferContext` k `Program.cs`:

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

Upravit `TransferLocalFileToAzureBlob` metodu použít `GetSingleTransferContext`:

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

## <a name="resume-a-canceled-transfer"></a>Pokračování stornované přenosu
Další vhodné funkcí nabízených knihovna pro přesun dat je schopnost pokračování stornované přenosu. Přidejme nějaký kód, která umožňuje dočasně zrušit převod zadáním `c`a poté obnovit přenos 3 sekund později.

Modify `TransferLocalFileToAzureBlob`:

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

Až dosud naše `checkpoint` hodnota je vždy nastavená na `null`. Nyní Pokud jsme zrušit převod, jsme načíst posledního kontrolního bodu naše přenosu, pak použít tento nový kontrolní bod v našich kontext přenosu.

## <a name="transfer-local-directory-to-azure-blob-directory"></a>Převod místní adresář do adresáře objektů Blob v Azure
Bylo by neuspokojivé, pokud knihovna pro přesun dat může přenášet jenom jeden soubor současně. Naštěstí to není případ. Knihovna pro přesun dat umožňuje přenos souborů a všech jeho podadresářích adresáře. Přidejme nějaký kód, který umožňuje nám to udělat přesně takhle.

Nejprve přidejte metodu `GetBlobDirectory` k `Program.cs`:

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

Existuje několik rozdílů mezi touto metodou a metodou pro nahrání jednoho souboru. Teď používáme `TransferManager.UploadDirectoryAsync` a `getDirectoryTransferContext` metoda jsme vytvořili dříve. Kromě toho teď poskytujeme `options` hodnotu naše operace nahrávání, což umožňuje nám to znamenat, že chceme zahrnout do našich nahrávání podadresáře.

## <a name="copy-file-from-url-to-azure-blob"></a>Zkopírujte soubor z adresy URL do objektů Blob v Azure
Nyní Pojďme přidat kód, který umožňuje zkopírovat soubor z adresy URL do objektu Blob Azure.

Modify `TransferUrlToAzureBlob`:

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

Jeden případ použití důležité pro tuto funkci je, když budete chtít přesunout data z jiné cloudové služby (třeba AWS) do Azure. Za předpokladu, budete mít adresu URL, která poskytuje přístup k prostředku, můžete tento prostředek snadno přesouvat do objektů BLOB v Azure pomocí `TransferManager.CopyAsync` metody. Tato metoda také zavádí nový parametr logické hodnoty. Nastavení tohoto parametru na `true` označuje, že chceme provádět asynchronní kopírování na straně serveru. Nastavení tohoto parametru na `false` označuje synchronní kopie – to znamená prostředek nejprve stáhne do našich místního počítače a pak nahrát do objektů Blob v Azure. Však synchronní kopie je nyní k dispozici pouze pro kopírování z jednoho prostředku Azure Storage.

## <a name="transfer-azure-blob-to-azure-blob"></a>Přenos objektů Blob v Azure do objektů Blob v Azure
Další funkcí, které jednoznačně poskytuje knihovna pro přesun dat je schopnost kopírování z jednoho prostředku Azure Storage do jiného.

Modify `TransferAzureBlobToAzureBlob`:

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

V tomto příkladu jsme nastavili parametr logické hodnoty `TransferManager.CopyAsync` k `false` k označení, že chceme provést synchronní kopie. To znamená, že je prostředek nejprve stáhne do našich místního počítače a pak nahrát do objektů Blob v Azure. Možnost synchronní kopie je skvělý způsob, jak zajistit, že v operaci kopírování konzistentní rychlost. Naproti tomu je závislá na dostupné šířky pásma na serveru, který může kolísat rychlost asynchronní kopírování na straně serveru. Synchronní kopie však může způsobit další výchozí přenos dat nákladů ve srovnání s asynchronní kopírování. Doporučený postup je ve Virtuálním počítači Azure, který je ve stejné oblasti jako váš účet úložiště zdroje, aby se zabránilo náklady na výchozí přenos dat pomocí synchronní kopie.

## <a name="conclusion"></a>Závěr
Naše aplikace pro přesun dat je nyní dokončena. [Úplný příklad je k dispozici na Githubu](https://github.com/azure-samples/storage-dotnet-data-movement-library-app).

## <a name="next-steps"></a>Další postup
V této příručce Začínáme, jsme vytvořili aplikaci, která komunikuje se službou Azure Storage a běží na Windows, Linux a macOS. Jak začít, zaměřuje na úložiště objektů Blob. Tyto stejné znalosti však můžete použít pro File Storage. Další informace, podívejte se na [knihovna pro přesun dat úložiště Azure v dokumentaci](https://azure.github.io/azure-storage-net-data-movement).

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]
