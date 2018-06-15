---
title: Přenos dat pomocí knihovna pro přesun dat úložiště Microsoft Azure | Microsoft Docs
description: Knihovna pro přesun dat pomocí přesunutí nebo zkopírování dat do nebo z objektu blob a obsahu souborů. Kopírování dat do úložiště Azure z místních souborů, nebo zkopírujte data v rámci nebo mezi účty úložiště. Snadno migrujte data do úložiště Azure.
services: storage
documentationcenter: ''
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/27/2017
ms.author: seguler
ms.openlocfilehash: eb96f3697d5369ba96a1b0c491e3eacf09e7aac4
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2018
ms.locfileid: "27564735"
---
# <a name="transfer-data-with-the-microsoft-azure-storage-data-movement-library"></a>Přenos dat pomocí knihovna pro přesun dat úložiště Microsoft Azure

## <a name="overview"></a>Přehled
Knihovna pro přesun dat aplikace Microsoft Azure Storage je knihovna napříč platformami s otevřeným zdrojem určená pro vysoký výkon odesílání, stahování a kopírování souborů a objektů BLOB služby Azure Storage. Tato knihovna je základní framework přesun dat, která pohání [AzCopy](../storage-use-azcopy.md). Knihovna pro přesun dat poskytuje praktické metody, které nejsou k dispozici v našem tradiční [.NET Klientská knihovna pro úložiště Azure](../blobs/storage-dotnet-how-to-use-blobs.md). To zahrnuje možnost nastavit počet paralelních operací, sledovat průběh přenosu, snadno obnovit zrušené přenos a mnoho dalšího.  

Tuto knihovnu používá také .NET Core, což znamená, že při vytváření aplikace .NET pro Windows, Linux a systému macOS, můžete ji použít. Další informace o .NET Core, naleznete [.NET Core dokumentaci](https://dotnet.github.io/). Tato knihovna funguje i pro tradiční aplikace pro rozhraní .NET Framework pro systém Windows. 

Tento dokument ukazuje postup vytvoření aplikace konzoly .NET Core, který běží v systému Windows, Linux a systému macOS a provede následující scénáře:

- Nahrávání souborů a adresářů do úložiště objektů Blob.
- Při přenosu dat, zadejte počet paralelních operací.
- Probíhá přenos dat sledování.
- Přenos dat obnovení zrušeny. 
- Zkopírujte soubor do úložiště objektů Blob z adresy URL. 
- Zkopírujte z úložiště objektů Blob do úložiště objektů Blob.

**Co potřebujete:**

* [Visual Studio Code](https://code.visualstudio.com/)
* [Účet úložiště Azure](storage-create-storage-account.md#create-a-storage-account)

> [!NOTE]
> Tato příručka předpokládá, že jste již obeznámeni s [Azure Storage](https://azure.microsoft.com/services/storage/). Pokud ne, čtení [Úvod do Azure Storage](storage-introduction.md) dokumentace je užitečné. Co je nejdůležitější, budete muset [vytvořit účet úložiště](storage-create-storage-account.md#create-a-storage-account) začít používat knihovna pro přesun dat.
> 
> 

## <a name="setup"></a>Nastavení  

1. Přejděte [Průvodce instalací rozhraní .NET Core](https://www.microsoft.com/net/core) k instalaci .NET Core. Když vyberete prostředí, vyberte tuto možnost příkazového řádku. 
2. Z příkazového řádku vytvořte adresář pro váš projekt. Přejděte do tohoto adresáře, zadejte `dotnet new console -o <sample-project-name>` k vytvoření konzoly projektu C#.
3. Otevřete tento adresář v kódu Visual Studio. Tento krok lze rychle provést prostřednictvím příkazového řádku zadáním `code .` v systému Windows.  
4. Nainstalujte [C# rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) z Visual Studio Marketplace kódu. Restartujte Visual Studio Code. 
5. V tomto okamžiku byste měli vidět dvě výzvy. Jeden je pro přidání "požadované prostředky pro sestavení a ladění." Klikněte na tlačítko Ano. Další výzva je určena pro obnovení nerozpoznané závislosti. Klikněte na možnost "obnovit."
6. Upravit `launch.json` pod `.vscode` používat externí Terminálové jako konzola. Toto nastavení by měl čtení jako` "console": "externalTerminal"`
7. Visual Studio Code umožňuje ladit aplikace .NET Core. Stiskněte tlačítko `F5` spusťte aplikaci a ověřte, zda je funkční vašeho nastavení. Měli byste vidět "Hello, World!" vytištěny ke konzole. 

## <a name="add-data-movement-library-to-your-project"></a>Přidejte knihovna pro přesun dat do projektu

1. Přidat nejnovější verzi knihovna pro přesun dat do `dependencies` část vaší `<project-name>.csproj` souboru. V době psaní bude tato verze`"Microsoft.Azure.Storage.DataMovement": "0.6.2"` 
2. K obnovení projektu by měl zobrazovat výzvu. Klikněte na tlačítko "restore". Můžete také obnovit projekt z příkazového řádku zadáním příkazu `dotnet restore` v kořenovém adresáři projektu.

Upravit `<project-name>.csproj`:

    <Project Sdk="Microsoft.NET.Sdk">

        <PropertyGroup>
            <OutputType>Exe</OutputType>
            <TargetFramework>netcoreapp2.0</TargetFramework>
        </PropertyGroup>
        <ItemGroup>
            <PackageReference Include="Microsoft.Azure.Storage.DataMovement" Version="0.6.2" />
            </ItemGroup>
        </Project>

## <a name="set-up-the-skeleton-of-your-application"></a>Nastavit kostru aplikace
První věcí, kterou provedeme nastavení "kostra" kódu aplikace. Tento kód vyzve nám pro klíč účet a název účtu úložiště a použije tyto přihlašovací údaje pro vytvoření `CloudStorageAccount` objektu. Tento objekt se používá k interakci se naše účet úložiště ve všech scénářích přenosu. Kód výzvy také nám vyberte typ operace přenosu, kterou jsme chtěli provést. 

Upravit `Program.cs`:

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

## <a name="transfer-local-file-to-azure-blob"></a>Přenos místní soubor do objektu Blob Azure
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

Změnit `TransferLocalFileToAzureBlob` metoda:

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

Tento kód nám vyzve k zadání cesty do místního souboru, název nové nebo existující kontejneru a název nového objektu blob. `TransferManager.UploadAsync` Metoda provádí nahrávání na základě těchto informací. 

Stiskněte tlačítko `F5` spusťte aplikaci. Můžete ověřit, že došlo k nahrávání zobrazením účtu úložiště s [Microsoft Azure Storage Explorer](http://storageexplorer.com/).

## <a name="set-number-of-parallel-operations"></a>Sada počet paralelních operací
Skvělé funkce, které nabízí knihovna pro přesun dat je možnost nastavit počet paralelních operací, pokud chcete zvýšit propustnost dat přenosu. Knihovna pro přesun dat ve výchozím nastavení, nastaví počet paralelních operací na 8 * počet jader na váš počítač. 

Uvědomte si, že mnoho paralelních operací v prostředí s malou šířkou pásma může zahlcovat síťové připojení a ve skutečnosti zabránit v plně dokončení operace. Budete muset Vyzkoušejte toto nastavení k určení, co funguje nejlépe závislosti na vaší dostupnou šířku pásma sítě. 

Přidejme nějaký kód, který umožňuje nastavit počet paralelních operací. Můžeme také přidat kód, který krát, jak dlouho trvá pro přenos do dokončení.

Přidat `SetNumberOfParallelOperations` metodu `Program.cs`:

```csharp
public static void SetNumberOfParallelOperations()
{
    Console.WriteLine("\nHow many parallel operations would you like to use?");
    string parallelOperations = Console.ReadLine();
    TransferManager.Configurations.ParallelOperations = int.Parse(parallelOperations);
}
```

Změnit `ExecuteChoice` metodu použít `SetNumberOfParallelOperations`:

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

Změnit `TransferLocalFileToAzureBlob` metodu použít časovač:

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
Zároveň budete vědět, jak dlouho trvalo pro naše data k přenosu je velmi užitečná. Však bude možné sledovat průběh naše přenos *během* operace přenosu by být ještě lepší. K dosažení tohoto scénáře, je nutné vytvořit `TransferContext` objektu. `TransferContext` Objekt pochází ve dvou formách: `SingleTransferContext` a `DirectoryTransferContext`. První je při přenosu jednoho souboru (což je co jsme vaše změny teď) a je pro přenos adresář soubory (které přidáváme později).

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

Změnit `TransferLocalFileToAzureBlob` metodu použít `GetSingleTransferContext`:

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

## <a name="resume-a-canceled-transfer"></a>Obnovit zrušené přenosu
Další pohodlný funkcí, které nabízí knihovna pro přesun dat je možnost obnovit zrušené přenosu. Přidejme nějaký kód, který umožňuje dočasně zrušit přenos zadáním `c`a poté obnovit přenos 3 sekund později.

Upravit `TransferLocalFileToAzureBlob`:

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

Až se zatím naše `checkpoint` vždy byla nastavena hodnota `null`. Nyní pokud zrušení přenos, jsme načíst do posledního kontrolního bodu naše přenos a potom používat tento nový kontrolní bod v našem kontext přenosu. 

## <a name="transfer-local-directory-to-azure-blob-directory"></a>Přenos místního adresáře do Azure Blob adresáře
By být neuspokojivé, pokud knihovna pro přesun dat může přenášet jenom jeden soubor současně. Naštěstí to tak není. Knihovna pro přesun dat umožňuje přenos souborů a všech jeho podadresářích adresáře. Přidejme nějaký kód, který umožňuje nám takový postup.

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

Existuje několik rozdílů mezi touto metodou a metodu pro nahrávání jeden soubor. Teď používáme `TransferManager.UploadDirectoryAsync` a `getDirectoryTransferContext` jsme vytvořili předtím. Kromě toho teď poskytujeme `options` hodnotu naše operace nahrávání, která umožňuje znamenat, že chceme zahrnout do našich nahrávání podadresáře. 

## <a name="copy-file-from-url-to-azure-blob"></a>Zkopírujte soubor do objektu Blob Azure z adresy URL
Nyní Pojďme přidat kód, který umožňuje zkopírovat soubor z adresy URL do objektu Blob Azure. 

Upravit `TransferUrlToAzureBlob`:

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

Jeden případ použití důležité pro tuto funkci je, když potřebujete přesunout data z jiné cloudové služby (např. AWS) do Azure. Tak dlouho, dokud máte adresu URL, která umožňuje přístup k prostředku, můžete snadno přesunutí prostředku do objektů BLOB služby Azure pomocí `TransferManager.CopyAsync` metoda. Tato metoda také zavádí nový parametr typu boolean. Nastavení tohoto parametru na `true` označuje, že chceme provést asynchronní kopie na straně serveru. Nastavení tohoto parametru na `false` označuje synchronní kopie – tj. prostředek se nejprve stáhne do našich v místním počítači, pak je nahrán do objektu Blob Azure. Však synchronní kopie je nyní k dispozici pouze pro kopírování z jednoho prostředku Azure Storage. 

## <a name="transfer-azure-blob-to-azure-blob"></a>Přenos objektů Blob Azure do Azure Blob
Další funkcí, které jednoznačně poskytuje knihovna pro přesun dat je možnost Kopírovat z jednoho prostředku Azure Storage do jiného. 

Upravit `TransferAzureBlobToAzureBlob`:

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

V tomto příkladu jsme nastavený logického parametru `TransferManager.CopyAsync` k `false` indikující, že nám chcete synchronní kopie. To znamená, že je prostředek nejprve stáhne do našich v místním počítači a potom nahrán do objektu Blob Azure. Možnost synchronní kopie je skvělým způsobem, jak zajistěte, aby vaše operace kopírování byla konzistentní rychlost. Rychlost asynchronní kopie serverové spočívá v tom, závisí na dostupnou šířku pásma sítě na serveru, který můžete kolísá. Synchronní kopie však může generovat další odchozí nákladů ve srovnání s asynchronní kopírování. Doporučený postup je použít synchronní kopie ve virtuálním počítači Azure, který je ve stejné oblasti jako váš účet úložiště zdroj předejdete odchozí náklady.

## <a name="conclusion"></a>Závěr
Naše aplikace přesun dat je nyní dokončen. [Ukázka úplného kódu je dostupná na Githubu](https://github.com/azure-samples/storage-dotnet-data-movement-library-app). 

## <a name="next-steps"></a>Další postup
V této příručce Začínáme, jsme vytvořili aplikaci, která komunikuje s úložištěm Azure a běží na systému Windows, Linux a systému macOS. Toto úvodní zaměřuje na úložiště objektů Blob. Tato stejné znalosti však lze použít k úložišti souborů. Další informace, podívejte se na [knihovna pro přesun dat úložiště Azure referenční dokumentaci k nástroji](https://azure.github.io/azure-storage-net-data-movement).

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]




