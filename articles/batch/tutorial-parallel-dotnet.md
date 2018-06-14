---
title: Spuštění paralelní úlohy – Azure Batch .NET
description: Kurz – Paralelní překódování multimediálních souborů pomocí aplikace ffmpeg ve službě Azure Batch s využitím klientské knihovny Batch .NET
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 01/23/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 57fc70d5b47f18affa90e1153884e8af23d937ec
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
ms.locfileid: "31513998"
---
# <a name="tutorial-run-a-parallel-workload-with-azure-batch-using-the-net-api"></a>Kurz: Spuštění paralelní úlohy pomocí služby Azure Batch s využitím rozhraní .NET API

Azure Batch umožňuje efektivně spouštět v Azure rozsáhlé paralelní dávkové úlohy a úlohy vysokovýkonného výpočetního prostředí (HPC). Tento kurz vás provede příkladem spuštění paralelní úlohy pomocí služby Batch v jazyce C#. Seznámíte se s běžným pracovním postupem aplikace Batch a způsobem práce s prostředky služby Batch a Storage prostřednictvím kódu programu. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Přidání balíčku aplikace do účtu Batch
> * Ověření pomocí účtů Batch a Storage
> * Nahrání vstupních souborů do služby Storage
> * Vytvoření fondu výpočetních uzlů pro spouštění aplikace
> * Vytvoření úlohy a úkolů pro zpracování vstupních souborů
> * Monitorování provádění úkolů
> * Načtení výstupních souborů

V tomto kurzu pomocí open source nástroje [ffmpeg](http://ffmpeg.org/) paralelně převedete multimediální soubory MP4 do formátu MP3. 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* [Visual Studio IDE](https://www.visualstudio.com/vs) (Visual Studio 2015 nebo novější verze). 

* Účet Batch a propojený účet Azure Storage. Informace o vytvoření těchto účtů prostřednictvím [webu Azure Portal](quick-create-portal.md) nebo [rozhraní Azure CLI](quick-create-cli.md) najdete v rychlém startu služby Batch.

* [64bitová verze aplikace ffmpeg 3.4 pro Windows](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip) (.zip). Stáhněte soubor zip na místní počítač. Pro účely tohoto kurzu potřebujete pouze soubor zip. Soubor nemusíte rozbalovat ani ho místně instalovat. 

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).


## <a name="add-an-application-package"></a>Přidání balíčku aplikace

Pomocí webu Azure Portal přidejte do svého účtu Batch aplikaci ffmpeg jako [balíček aplikace](batch-application-packages.md). Balíčky aplikací pomáhají spravovat aplikace úkolů a jejich nasazení do výpočetních uzlů ve fondu. 

1. Na webu Azure Portal klikněte na **Další služby** > **Účty Batch** a pak klikněte na název vašeho účtu Batch.
3. Klikněte na **Aplikace** > **Přidat**.
4. Jako **ID aplikace** zadejte *ffmpeg* a jako verzi balíčku zadejte *3.4*. Vyberte soubor zip s aplikací ffmpeg, který jste stáhli dříve, a pak klikněte na **OK**. Balíček aplikace ffmpeg se přidá do vašeho účtu Batch.

![Přidání balíčku aplikace](./media/tutorial-parallel-dotnet/add-application.png)

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="download-and-run-the-sample"></a>Stažení a spuštění ukázky

### <a name="download-the-sample"></a>Stažení ukázky

[Stáhněte nebo naklonujte ukázkovou aplikaci](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) z GitHubu. K naklonování úložiště ukázkové aplikace pomocí klienta Git použijte následující příkaz:

```
git clone https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial.git
```

Přejděte do adresáře, který obsahuje soubor řešení sady Visual Studio `BatchDotNetFfmpegTutorial.sln`.

Otevřete soubor řešení v sadě Visual Studio a aktualizujte řetězce přihlašovacích údajů v souboru `program.cs` pomocí hodnot, které jste získali pro své účty. Příklad:

```csharp
// Batch account credentials
private const string BatchAccountName = "mybatchaccount";
private const string BatchAccountKey  = "xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ==";
private const string BatchAccountUrl  = "https://mybatchaccount.mybatchregion.batch.azure.com";

// Storage account credentials
private const string StorageAccountName = "mystorageaccount";
private const string StorageAccountKey  = "xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ==";
```

Ujistěte se také, že reference na balíček aplikace ffmpeg v řešení odpovídá ID a verzi balíčku ffmpeg, který jste nahráli do svého účtu Batch.

```csharp
const string appPackageId = "ffmpeg";
const string appPackageVersion = "3.4";
```
### <a name="build-and-run-the-sample-project"></a>Sestavení a spuštění ukázkového projektu

* Klikněte pravým tlačítkem na řešení v Průzkumníku řešení a pak klikněte na **Sestavit řešení**. 

* Pokud se zobrazí výzva, potvrďte obnovení všech balíčků NuGet. Pokud potřebujete stáhnout chybějící balíčky, ujistěte se, že máte nainstalovaného [Správce balíčků NuGet](https://docs.nuget.org/consume/installing-nuget).

Potom aplikaci spusťte. Po spuštění ukázkové aplikace vypadá výstup konzoly zhruba následovně. Během provádění dojde k pozastavení na řádku `Monitoring all tasks for 'Completed' state, timeout in 00:30:00...` a mezitím se spustí výpočetní uzly fondu. 

```
Sample start: 12/12/2017 3:20:21 PM

Container [input] created.
Container [output] created.
Uploading file LowPriVMs-1.mp4 to container [input]...
Uploading file LowPriVMs-2.mp4 to container [input]...
Uploading file LowPriVMs-3.mp4 to container [input]...
Uploading file LowPriVMs-4.mp4 to container [input]...
Uploading file LowPriVMs-5.mp4 to container [input]...
Creating pool [WinFFmpegPool]...
Creating job [WinFFmpegJob]...
Adding 5 tasks to job [WinFFmpegJob]...
Monitoring all tasks for 'Completed' state, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Deleting container [input]...

Sample end: 12/12/2017 3:29:36 PM
Elapsed time: 00:09:14.3418742
```


Fond, výpočetní uzly, úlohy a úkoly můžete sledovat ve svém účtu Batch na webu Azure Portal. Pokud například chcete zobrazit heat mapu výpočetních uzlů ve fondu, klikněte na **Fondy** > *WinFFmpegPool*.

Když jsou úkoly spuštěné, heat mapa vypadá přibližně takto:

![Heat mapa fondu](./media/tutorial-parallel-dotnet/pool.png)


Obvyklá doba provádění je přibližně **10 minut**, když aplikaci spouštíte v její výchozí konfiguraci. Vytvoření fondu trvá nejdéle.

[!INCLUDE [batch-common-tutorial-download](../../includes/batch-common-tutorial-download.md)]

## <a name="review-the-code"></a>Kontrola kódu

Následující části ukázkovou aplikaci rozdělují do kroků, které aplikace provádí při zpracování úloh ve službě Batch. Při čtení zbývajících částí tohoto článku nahlížejte do řešení otevřeného v sadě Visual Studio, protože tady nezvládneme probrat každý jednotlivý řádek kódu.

### <a name="authenticate-blob-and-batch-clients"></a>Ověřování klientů objektů blob a služby Batch

K interakci s propojeným účtem úložiště aplikace používá klientskou knihovnu služby Azure Storage pro .NET. Pomocí [CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) vytvoří referenci na účet a provede ověření pomocí sdíleného klíče. Potom vytvoří [CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient).

```csharp
// Construct the Storage account connection string
string storageConnectionString = String.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
StorageAccountName, StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);

CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

K vytváření a správě fondů, úloh a úkolů ve službě Batch aplikace vytvoří objekt [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient). Klient služby Batch v ukázce používá ověření pomocí sdíleného klíče. Pro účely ověřování jednotlivých uživatelů nebo bezobslužných aplikací služba Batch podporuje také ověřování prostřednictvím [Azure Active Directory](batch-aad-auth.md).

```csharp
BatchSharedKeyCredentials sharedKeyCredentials = new BatchSharedKeyCredentials(BatchAccountUrl, BatchAccountName, BatchAccountKey);

using (BatchClient batchClient = BatchClient.Open(sharedKeyCredentials))
...
```

### <a name="upload-input-files"></a>Nahrání vstupních souborů

Aplikace předá objekt `blobClient` do metody `CreateContainerIfNotExist`, která vytvoří kontejner úložiště pro vstupní soubory (ve formátu MP4) a kontejner pro výstup úkolů.

```csharp
  CreateContainerIfNotExist(blobClient, inputContainerName;
  CreateContainerIfNotExist(blobClient, outputContainerName);
```

Pak se do vstupního kontejneru nahrají soubory z místní složky `InputFiles`. Soubory v úložišti jsou definované jako objekty [ResourceFile](/dotnet/api/microsoft.azure.batch.resourcefile) služby Batch, které může služba Batch později stáhnout do výpočetních uzlů. 

Na nahrávání souborů se podílejí dvě metody v souboru `Program.cs`:

* `UploadResourceFilesToContainer`: Vrací kolekci objektů ResourceFile a interně volá metodu `UploadResourceFileToContainer`, která nahraje všechny soubory předané v parametru `filePaths`.
* `UploadResourceFileToContainer`: Nahraje jednotlivé soubory jako objekty blob do vstupního kontejneru. Po nahrání souboru získá sdílený přístupový podpis (SAS) objektu blob a vrátí objekt ResourceFile, který ho zastupuje. 

```csharp
  List<string> inputFilePaths = new List<string>(Directory.GetFileSystemEntries(@"..\..\InputFiles", "*.mp4",
      SearchOption.TopDirectoryOnly));

  List<ResourceFile> inputFiles = UploadResourceFilesToContainer(
    blobClient,
    inputContainerName,
    inputFilePaths);
```

Podrobnosti o nahrávání souborů jako objektů blob do účtu úložiště pomocí .NET najdete v tématu [Začínáme s úložištěm objektů blob v Azure s použitím .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

### <a name="create-a-pool-of-compute-nodes"></a>Vytvořte fond výpočetních uzlů.

Na účtu Batch potom příklad pomocí volání `CreatePoolIfNotExist` vytvoří fond výpočetních uzlů. Tato definovaná metoda používá metodu [BatchClient.PoolOperations.CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations.createpool), která nastavuje počet uzlů, velikost virtuálních počítačů a konfiguraci fondu. Tady objekt [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) určuje odkaz [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference) na image Windows Serveru publikovanou v Azure Marketplace. Batch podporuje širokou škálu imagí virtuálních počítačů v Azure Marketplace, ale i vlastní image virtuálních počítačů.

Počet uzlů a velikost virtuálních počítačů jsou definované konstanty. Batch podporuje vyhrazené uzly a [uzly s nízkou prioritou](batch-low-pri-vms.md) a ve svých fondech můžete použít oba typy. Vyhrazené uzly jsou rezervované pro váš fond. Uzly s nízkou prioritou pocházejí z přebytečné kapacity virtuálních počítačů v Azure a nabízejí se za nižší cenu. Pokud Azure nemá dostatek kapacity, uzly s nízkou prioritou budou nedostupné. Ukázka ve výchozím nastavení vytvoří fond obsahující pouze 5 uzlů s nízkou prioritou ve velikosti *Standard_A1_v2*. 

Aplikace ffmpeg se do výpočetních uzlů nasadí přidáním odkazu [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) do konfigurace fondu. 

Metoda [Commit](/dotnet/api/microsoft.azure.batch.cloudpool.commit) odešle fond do služby Batch.

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "MicrosoftWindowsServer",
    offer: "WindowsServer",
    sku: "2012-R2-Datacenter-smalldisk",
    version: "latest");

VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.windows amd64");

pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: DedicatedNodeCount,
    targetLowPriorityComputeNodes: LowPriorityNodeCount,
    virtualMachineSize: PoolVMSize,                                                
    virtualMachineConfiguration: virtualMachineConfiguration);

pool.ApplicationPackageReferences = new List<ApplicationPackageReference>
    {
    new ApplicationPackageReference {
    ApplicationId = appPackageId,
    Version = appPackageVersion}};

pool.Commit();  
```

### <a name="create-a-job"></a>Vytvoření úlohy

Úloha služby Batch určí fond, ve kterém se budou spouštět úkoly, a volitelná nastavení, jako je priorita a plán práce. Ukázka vytvoří úlohu zavoláním metody `CreateJobIfNotExist`. Tato definovaná metoda vytvoří úlohu ve vašem fondu pomocí metody [BatchClient.JobOperations.CreateJob](/dotnet/api/microsoft.azure.batch.joboperations.createjob). 

Metoda [Commit](/dotnet/api/microsoft.azure.batch.cloudjob.commit) odešle úlohu do služby Batch. Na začátku úloha neobsahuje žádné úkoly.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob();
    job.Id = JobId;
    job.PoolInformation = new PoolInformation { PoolId = PoolId };

job.Commit();        
```

### <a name="create-tasks"></a>Vytvoření úkolů

Ukázka vytvoří v úloze úkoly zavoláním metody `AddTasks`, která vytvoří seznam objektů [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask). Každý objekt `CloudTask` pomocí vlastnosti [CommandLine](/dotnet/api/microsoft.azure.batch.cloudtask.commandline) spouští aplikaci ffmpeg, která zpracuje vstupní objekt `ResourceFile`. Aplikace ffmpeg se na každý uzel nainstalovala dříve při vytváření fondu. Tady příkazový řádek spouští aplikaci ffmpeg kvůli převodu jednotlivých vstupních souborů MP4 (video) na soubory MP3 (zvuk).

Ukázka po spuštění příkazového řádku vytvoří pro soubor MP3 objekt [OutputFile](/dotnet/api/microsoft.azure.batch.outputfile). Výstupní soubory všech úkolů (v tomto případě jednoho) se pomocí vlastnosti [OutputFiles](/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles) nahrají do kontejneru v propojeném účtu úložiště.

Potom ukázka přidá úkoly do úlohy pomocí metody [AddTask](/dotnet/api/microsoft.azure.batch.joboperations.addtask) a ta je zařadí do fronty ke spuštění ve výpočetních uzlech. 

```csharp
for (int i = 0; i < inputFiles.Count; i++)
{
    string taskId = String.Format("Task{0}", i);

    // Define task command line to convert each input file.
    string appPath = String.Format("%AZ_BATCH_APP_PACKAGE_{0}#{1}%", appPackageId, appPackageVersion);
    string inputMediaFile = inputFiles[i].FilePath;
    string outputMediaFile = String.Format("{0}{1}",
        System.IO.Path.GetFileNameWithoutExtension(inputMediaFile),
        ".mp3");
    string taskCommandLine = String.Format("cmd /c {0}\\ffmpeg-3.4-win64-static\\bin\\ffmpeg.exe -i {1} {2}", appPath, inputMediaFile, outputMediaFile);

    // Create a cloud task (with the task ID and command line) 
    CloudTask task = new CloudTask(taskId, taskCommandLine);
    task.ResourceFiles = new List<ResourceFile> { inputFiles[i] };
   

    // Task output file
    List<OutputFile> outputFileList = new List<OutputFile>();
    OutputFileBlobContainerDestination outputContainer = new OutputFileBlobContainerDestination(outputContainerSasUrl);
    OutputFile outputFile = new OutputFile(outputMediaFile,
       new OutputFileDestination(outputContainer),
       new OutputFileUploadOptions(OutputFileUploadCondition.TaskSuccess));
    outputFileList.Add(outputFile);
    task.OutputFiles = outputFileList;
    tasks.Add(task);
}

// Add tasks as a collection
batchClient.JobOperations.AddTask(jobId, tasks);
```

### <a name="monitor-tasks"></a>Sledování úkolů

Když služba Batch přidá do úlohy úkoly, automaticky je zařadí do fronty a naplánuje jejich spuštění ve výpočetních uzlech v přidruženém fondu. Na základě vámi zadaných nastavení služba Batch zpracuje veškeré řazení úkolů do fronty, plánování úkolů, opakované spouštění a další povinnosti spojené se správou úkolů. 

Ke sledování provádění úkolů existuje mnoho přístupů. Tato ukázka definuje metodu `MonitorTasks`, která hlásí pouze dokončení, selhání úkolu nebo úspěšné stavy. Kód metody `MonitorTasks` určí [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel), aby se efektivně vybíralo pouze minimum informací o úkolech. Potom vytvoří [TaskStateMonitor](/dotnet/api/microsoft.azure.batch.taskstatemonitor) poskytující pomocné nástroje pro monitorování stavu úloh. V metodě `MonitorTasks` ukázka čeká na dosažení stavu `TaskState.Completed` u všech úkolů v časovém limitu. Potom úlohu ukončí a nahlásí všechny úkoly, které se dokončily, ale u kterých pravděpodobně došlo k chybě, například kvůli nenulovému ukončovacímu kódu.

```csharp
TaskStateMonitor taskStateMonitor = batchClient.Utilities.CreateTaskStateMonitor();
try
{
    batchClient.Utilities.CreateTaskStateMonitor().WaitAll(addedTasks, TaskState.Completed, timeout);
}
catch (TimeoutException)
{
    batchClient.JobOperations.TerminateJob(jobId, failureMessage);
    Console.WriteLine(failureMessage);
}
batchClient.JobOperations.TerminateJob(jobId, successMessage);
...

```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Aplikace po spuštění úkolů automaticky odstraní kontejner vstupního úložiště, který vytvořila, a dá vám možnost odstranit fond a úlohu služby Batch. Třídy [JobOperations](/dotnet/api/microsoft.azure.batch.batchclient.joboperations) a [PoolOperations](/dotnet/api/microsoft.azure.batch.batchclient.pooloperations) v BatchClient obsahují odpovídající metody pro odstranění, které se zavolají, pokud potvrdíte odstranění. I když se vám neúčtují poplatky za úlohy a úkoly jako takové, účtují se vám poplatky za výpočetní uzly. Proto doporučujeme, abyste fondy přidělovali, jen když je to potřeba. Při odstranění fondu se odstraní veškeré výstupy úkolů v uzlech. Vstupní a výstupní soubory však zůstanou v účtu úložiště.

Pokud už je nepotřebujete, odstraňte skupinu prostředků, účet Batch a účet úložiště. Na webu Azure Portal to provedete tak, že vyberete skupinu prostředků účtu Batch a kliknete na **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili tyto postupy:

> [!div class="checklist"]
> * Přidání balíčku aplikace do účtu Batch
> * Ověření pomocí účtů Batch a Storage
> * Nahrání vstupních souborů do služby Storage
> * Vytvoření fondu výpočetních uzlů pro spouštění aplikace
> * Vytvoření úlohy a úkolů pro zpracování vstupních souborů
> * Monitorování provádění úkolů
> * Načtení výstupních souborů

Další příklady použití rozhraní .NET API k plánování a zpracování úloh služby Batch najdete v ukázkách na GitHubu.

> [!div class="nextstepaction"]
> [Ukázky pro službu Batch v jazyce C#](https://github.com/Azure/azure-batch-samples/tree/master/CSharp)
