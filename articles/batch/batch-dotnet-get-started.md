---
title: Kurz – použití klientské knihovny Azure Batch pro .NET | Dokumentace Microsoftu
description: Informace o základních konceptech služby Azure Batch a vytvoření jednoduchého řešení pomocí rozhraní .NET
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 76cb9807-cbc1-405a-8136-d1e53e66e82b
ms.service: batch
ms.devlang: dotnet
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-compute
ms.date: 06/28/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: dbd96187ad73a9c7e27b28f137e25fe66e2944ad
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
ms.locfileid: "31514400"
---
# <a name="get-started-building-solutions-with-the-batch-client-library-for-net"></a>Začínáme sestavovat řešení pomocí klientské knihovny služby Batch pro .NET

> [!div class="op_single_selector"]
> * [.NET](batch-dotnet-get-started.md)
> * [Python](batch-python-tutorial.md)
> * [Node.js](batch-nodejs-get-started.md)
>
>

V tomto článku se seznámíte se základy [Azure Batch][azure_batch] a s knihovnou [Batch .NET][net_api] a společně si krok za krokem probereme ukázkovou aplikaci v jazyce C#. Podíváme se, jak tato ukázková aplikace využívá službu Batch ke zpracování paralelní úlohy v cloudu, a jak komunikuje se službou [Azure Storage](../storage/common/storage-introduction.md) při přípravě a načítání souborů. Seznámíte se s běžným pracovním postupem aplikací Batch a získáte základní přehled o součástech služby Batch, například o úlohách, úkolech, fondech a výpočetních uzlech.

![Pracovní postup řešení Batch (Basic)][11]<br/>

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že máte praktické znalosti jazyka C# a sady Visual Studio. Předpokládá také, že dokážete splnit požadavky na vytvoření účtů Azure, služby Batch a služby Storage, které jsou uvedeny níže.

### <a name="accounts"></a>Účty
* **Účet Azure**: Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet Azure][azure_free_account].
* **Účet Batch**: Po pořízení předplatného Azure si [vytvořte účet Azure Batch](batch-account-create-portal.md).
* **Účet úložiště:** Možnosti účtu úložiště ve službě Batch najdete v tématu [Přehled funkcí Batch](batch-api-basics.md#azure-storage-account).


### <a name="visual-studio"></a>Visual Studio
K vytvoření ukázkového projektu potřebujete sadu **Visual Studio 2015 nebo novější**. V [přehledu produktů Visual Studio][visual_studio] najdete bezplatné a zkušební verze sady Visual Studio.

### <a name="dotnettutorial-code-sample"></a>Ukázka kódu *DotNetTutorial*
Ukázka [DotNetTutorial][github_dotnettutorial] je jednou z mnoha ukázek kódu Batch, které najdete v úložišti na GitHubu [azure-batch-samples][github_samples]. Všechny ukázky můžete stáhnout kliknutím na **Klonovat nebo stáhnout > Stáhnout ZIP** na domovské stránce úložiště, nebo kliknutím na přímý odkaz ke stažení [azure-batch-samples-master.zip][github_samples_zip]. Po extrahování obsahu souboru ZIP najdete řešení v následující složce:

`\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial`

### <a name="batchlabs-optional"></a>BatchLabs (volitelné)
[BatchLabs][github_batchlabs] je bezplatný a samostatný klientský nástroj s bohatými funkcemi, který pomáhá vytvářet, ladit a monitorovat aplikace Azure Batch. Není sice k dokončení kurzu nutný, ale může být užitečný při vývoji a ladění vašich řešení Batch.

## <a name="dotnettutorial-sample-project-overview"></a>Přehled ukázkového projektu DotNetTutorial
Ukázka kódu *DotNetTutorial* je řešení sady Visual Studio, které se skládá ze dvou projektů: **DotNetTutorial** a **TaskApplication**.

* **DotNetTutorial** je klientská aplikace, která komunikuje se službou Batch a se službou Azure Storage při spouštění paralelní úlohy na výpočetních uzlech (virtuálních počítačích). DotNetTutorial se spouští na místní pracovní stanici.
* **TaskApplication** je program, který běží na výpočetních uzlech v Azure a provádí samotnou práci. V tomto příkladu `TaskApplication.exe` analyzuje text v souboru staženém ze služby Azure Storage (vstupní soubor). Potom vytvoří textový soubor (výstupní soubor), který obsahuje seznam nejčastějších tří slov, která se zobrazují ve vstupním souboru. Po vytvoření výstupního souboru TaskApplication odešle soubor do služby Azure Storage. Klientská aplikace ho tak bude mít k dispozici ke stažení. TaskApplication běží paralelně v několika výpočetních uzlech v rámci služby Batch.

Následující diagram znázorňuje primární operace, které provádí klientská aplikace *DotNetTutorial* a aplikace *TaskApplication*, kterou spouští úkoly. Tento základní pracovní postup je typický pro mnoho výpočetních řešení, která jsou vytvořená pomocí služby Batch. I když nepředvádí všechny funkce, které jsou ve službě Batch dostupné, téměř každý scénář Batch bude obsahovat části tohoto pracovního postupu.

![Ukázkový pracovní postup služby Batch][8]<br/>

[**Krok 1.**](#step-1-create-storage-containers) Ve službě Azure Blob Storage vytvořte **kontejnery** .<br/>
[**Krok 2.**](#step-2-upload-task-application-and-data-files) Odešlete do kontejneru aplikační soubory a vstupní soubory úkolu.<br/>
[**Krok 3.**](#step-3-create-batch-pool) Vytvořte **fond** Batch.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**3a.** Když se uzly připojí k fondu, fond **StartTask** stáhne binární soubory úkolů (TaskApplication).<br/>
[**Krok 4.**](#step-4-create-batch-job) Vytvořte **úlohu** Batch.<br/>
[**Krok 5.**](#step-5-add-tasks-to-job) Přidejte do úlohy **úkoly**.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**5a.** Úkoly jsou naplánované, aby se spustily na uzlech.<br/>
    &nbsp;&nbsp;&nbsp;&nbsp;**5b.** Každý úkol stáhne svoje vstupní data ze služby Azure Storage a potom zahájí spuštění.<br/>
[**Krok 6.**](#step-6-monitor-tasks) Sledujte úkoly.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**6a.** Úkoly při dokončení odesílají svoje výstupní data do služby Azure Storage.<br/>
[**Krok 7.**](#step-7-download-task-output) Stáhněte si výstup úkolu ze služby Storage.

Jak jsme už zmínili, ne každé řešení Batch provede právě tyto kroky a může jich obsahovat i mnohem víc, ale ukázková aplikace *DotNetTutorial* předvádí běžné procesy, které probíhají v řešení Batch.

## <a name="build-the-dotnettutorial-sample-project"></a>Vytvoření ukázkového projektu *DotNetTutorial*
Předtím, než ukázku úspěšně spustíte, musíte zadat přihlašovací údaje k účtu Batch i k účtu Storage do souboru `Program.cs` v projektu *DotNetTutorial*. Pokud jste to ještě neudělali, otevřete řešení v sadě Visual Studio dvojím kliknutím na soubor řešení `DotNetTutorial.sln`. Nebo ho otevřete v sadě Visual Studio pomocí nabídky **Soubor > Otevřít > Projekt nebo řešení**.

V projektu *DotNetTutorial* otevřete soubor `Program.cs`. Potom podle pokynů na začátku souboru zadejte svoje přihlašovací údaje:

```csharp
// Update the Batch and Storage account credential strings below with the values
// unique to your accounts. These are used when constructing connection strings
// for the Batch and Storage client objects.

// Batch account credentials
private const string BatchAccountName = "";
private const string BatchAccountKey  = "";
private const string BatchAccountUrl  = "";

// Storage account credentials
private const string StorageAccountName = "";
private const string StorageAccountKey  = "";
```

Přihlašovací údaje k účtu Batch a k účtu služby Storage najdete v okně účtu každé služby na webu [Azure Portal][azure_portal]:

![Přihlašovací údaje služby Batch na portálu][9]
![Přihlašovací údaje služby Storage na portálu][10]<br/>

Po aktualizaci projektu pomocí svých přihlašovacích údajů klikněte pravým tlačítkem v Průzkumníku řešení a potom klikněte na **Sestavit řešení**. Pokud se zobrazí výzva, potvrďte obnovení všech balíčků NuGet.

> [!TIP]
> Pokud se balíčky NuGet automaticky neobnoví, nebo když se zobrazí chyby týkající se neúspěšného obnovení balíčků, zkontrolujte, jestli máte nainstalovaného [Správce balíčků NuGet][nuget_packagemgr]. Potom povolte stažení chybějících balíčků. V článku [Povolení obnovy balíčků během sestavení][nuget_restore] najdete další informace o povolení stahování balíčků.
>
>

V následujících částech si ukázkovou aplikaci rozdělíme do kroků, které aplikace provádí při zpracování úloh ve službě Batch, a jednotlivé kroky si podrobně probereme. Doporučujeme, abyste při procházení zbývající části tohoto článku nahlíželi do řešení otevřeného v sadě Visual Studio, protože tady nezvládneme probrat každý jednotlivý řádek kódu.

V projektu *DotNetTutorial* v souboru `Program.cs` přejděte do horní části metody `MainAsync` a začněte s krokem 1. Každý níže uvedený krok zhruba následuje průběh volání metod v `MainAsync`.

## <a name="step-1-create-storage-containers"></a>Krok 1: Vytvoření kontejnerů služby Storage
![Vytvoření kontejnerů ve službě Azure Storage][1]
<br/>

Batch obsahuje vestavěnou podporu pro komunikaci se službou Azure Storage. Kontejnery v účtu Storage poskytnou soubory, které potřebují úkoly spuštěné v účtu Batch. Kontejnery také poskytují místo pro ukládání výstupních dat, která úkoly vytvářejí. Klientská aplikace *DotNetTutorial* nejdřív vytvoří tři kontejnery ve službě [Azure Blob Storage](../storage/common/storage-introduction.md):

* **application**: Do tohoto kontejneru se bude ukládat aplikace spuštěná úkoly a také veškeré její závislé položky, například knihovny DLL.
* **input**: Datové soubory ke zpracování budou úkoly stahovat z kontejneru *input*.
* **output**: Když úkoly dokončí zpracování vstupního souboru, odešlou výsledky do kontejneru *output*.

Za účelem práce s účtem služby Storage a vytvoření kontejnerů použijeme [klientskou knihovnu služby Azure Storage pro .NET][net_api_storage]. Referenci na účet vytvoříme pomocí [CloudStorageAccount][net_cloudstorageaccount] a z té vytvoříme [CloudBlobClient][net_cloudblobclient]:

```csharp
// Construct the Storage account connection string
string storageConnectionString = String.Format(
    "DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
    StorageAccountName,
    StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount =
    CloudStorageAccount.Parse(storageConnectionString);

// Create the blob client, for use in obtaining references to
// blob storage containers
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

Referenci `blobClient` používáme v celé aplikaci a předáváme jako parametr do několika metod. Příklad toho je v bloku kódu, který následuje výše uvedené, kde voláme `CreateContainerIfNotExistAsync`, abychom vytvořili kontejnery.

```csharp
// Use the blob client to create the containers in Azure Storage if they don't
// yet exist
const string appContainerName    = "application";
const string inputContainerName  = "input";
const string outputContainerName = "output";
await CreateContainerIfNotExistAsync(blobClient, appContainerName);
await CreateContainerIfNotExistAsync(blobClient, inputContainerName);
await CreateContainerIfNotExistAsync(blobClient, outputContainerName);
```

```csharp
private static async Task CreateContainerIfNotExistAsync(
    CloudBlobClient blobClient,
    string containerName)
{
        CloudBlobContainer container =
            blobClient.GetContainerReference(containerName);

        if (await container.CreateIfNotExistsAsync())
        {
                Console.WriteLine("Container [{0}] created.", containerName);
        }
        else
        {
                Console.WriteLine("Container [{0}] exists, skipping creation.",
                    containerName);
        }
}
```

Po vytvoření kontejnerů může aplikace začít odesílat soubory, které budou úkoly používat.

> [!TIP]
> Článek [Použití služby Blob Storage pomocí technologie .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) nabízí pěkný přehled o práci s kontejnery a objekty blob ve službě Azure Storage. Když začnete pracovat se službou Batch, je určitě na místě si ten článek přečíst.
>
>

## <a name="step-2-upload-task-application-and-data-files"></a>Krok 2: Nahrání aplikačních a datových souborů úkolů
![Odeslání aplikačních a vstupních (datových) souborů úkolů do kontejnerů][2]
<br/>

*DotNetTutorial* v rámci operace nahrávání souborů nejdřív definuje kolekce cest k **aplikačním** a **vstupním** souborům, které jsou v místním počítači. Potom tyto soubory odešle do kontejnerů, které jste vytvořili v předchozím kroku.

```csharp
// Paths to the executable and its dependencies that will be executed by the tasks
List<string> applicationFilePaths = new List<string>
{
    // The DotNetTutorial project includes a project reference to TaskApplication,
    // allowing us to determine the path of the task application binary dynamically
    typeof(TaskApplication.Program).Assembly.Location,
    "Microsoft.WindowsAzure.Storage.dll"
};

// The collection of data files that are to be processed by the tasks
List<string> inputFilePaths = new List<string>
{
    @"..\..\taskdata1.txt",
    @"..\..\taskdata2.txt",
    @"..\..\taskdata3.txt"
};

// Upload the application and its dependencies to Azure Storage. This is the
// application that will process the data files, and will be executed by each
// of the tasks on the compute nodes.
List<ResourceFile> applicationFiles = await UploadFilesToContainerAsync(
    blobClient,
    appContainerName,
    applicationFilePaths);

// Upload the data files. This is the data that will be processed by each of
// the tasks that are executed on the compute nodes within the pool.
List<ResourceFile> inputFiles = await UploadFilesToContainerAsync(
    blobClient,
    inputContainerName,
    inputFilePaths);
```

V souboru `Program.cs` existují dvě metody, které se účastní procesu nahrávání:

* `UploadFilesToContainerAsync`: Tato metoda vrátí kolekci objektů [ResourceFile][net_resourcefile] (viz následující popis) a interně volá `UploadFileToContainerAsync` kvůli nahrání každého souboru, který se předává v parametru *filePaths*.
* `UploadFileToContainerAsync`: Toto je metoda, která provádí samotné nahrávání souborů a vytváří objekty [ResourceFile][net_resourcefile]. Po nahrání souboru získá sdílený přístupový podpis (SAS) souboru a vrátí objekt ResourceFile, který ho zastupuje. Sdílené přístupové podpisy jsou také popsány níže.

```csharp
private static async Task<ResourceFile> UploadFileToContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string filePath)
{
        Console.WriteLine(
            "Uploading file {0} to container [{1}]...", filePath, containerName);

        string blobName = Path.GetFileName(filePath);

        CloudBlobContainer container = blobClient.GetContainerReference(containerName);
        CloudBlockBlob blobData = container.GetBlockBlobReference(blobName);
        await blobData.UploadFromFileAsync(filePath);

        // Set the expiry time and permissions for the blob shared access signature.
        // In this case, no start time is specified, so the shared access signature
        // becomes valid immediately
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
        {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
                Permissions = SharedAccessBlobPermissions.Read
        };

        // Construct the SAS URL for blob
        string sasBlobToken = blobData.GetSharedAccessSignature(sasConstraints);
        string blobSasUri = String.Format("{0}{1}", blobData.Uri, sasBlobToken);

        return new ResourceFile(blobSasUri, blobName);
}
```

### <a name="resourcefiles"></a>ResourceFiles
[ResourceFile][net_resourcefile] poskytuje úkolům v Batch adresu URL k souboru ve službě Azure Storage, který se před spuštěním úkolu stáhne do výpočetního uzlu. Vlastnost [ResourceFile.BlobSource][net_resourcefile_blobsource] určuje úplnou adresu URL souboru, protože existuje ve službě Azure Storage. Adresa URL může obsahovat také sdílený přístupový podpis (SAS), který zajišťuje zabezpečený přístup k souboru. Většina typů úkolů v rámci v Batch .NET obsahuje vlastnost *ResourceFiles* včetně:

* [CloudTask][net_task]
* [StartTask][net_pool_starttask]
* [JobPreparationTask][net_jobpreptask]
* [JobReleaseTask][net_jobreltask]

Ukázková aplikace DotNetTutorial nepoužívá typy úloh JobPreparationTask nebo JobReleaseTask, ale můžete si o nich přečíst v článku [Spouštění úkolů přípravy a dokončení úlohy na výpočetních uzlech Azure Batch](batch-job-prep-release.md).

### <a name="shared-access-signature-sas"></a>Sdílený přístupový podpis (SAS)
Sdílené přístupové podpisy jsou řetězce, které (když jsou součástí adresy URL) zajišťují zabezpečený přístup ke kontejnerům a objektům blob ve službě Azure Storage. Aplikace DotNetTutorial používá adresy URL se sdíleným přístupovým podpisem objektu blob i kontejneru a ukazuje, jak můžete tyto řetězce sdíleného přístupového podpisu získat ze služby Storage.

* **Sdílené přístupové podpisy objektů blob**: StartTask fondu v aplikaci DotNetTutorial používá sdílené přístupové podpisy objektů blob při stahování aplikačních binárních souborů a vstupních datových souborů ze služby Storage (viz krok 3 níže). Metoda `UploadFileToContainerAsync` v souboru `Program.cs` aplikace DotNetTutorial obsahuje kód, který získá sdílený přístupový podpis jednotlivých objektů blob. Dělá to tak, že volá [CloudBlob.GetSharedAccessSignature][net_sas_blob].
* **Sdílené přístupové podpisy kontejnerů**: Když každý úkol dokončí svojí práci ve výpočetním uzlu, odešle svůj výstupní soubor do kontejneru *output* ve službě Azure Storage. Aby to mohl provést, používá TaskApplication sdílený přístupový podpis kontejneru, který zajišťuje oprávnění k zápisu do kontejneru jako součást cesty při nahrávání souboru. Získání sdíleného přístupového podpisu kontejneru se provádí podobným způsobem jako získávání sdíleného přístupového podpisu objektu blob. V aplikaci DotNetTutorial zjistíte, že pomocná metoda `GetContainerSasUrl` za tímto účelem volá [CloudBlobContainer.GetSharedAccessSignature][net_sas_container]. Další informace o tom, jak TaskApplication používá sdílený přístupový podpis kontejneru, se dočtete v kroku 6: Sledování úkolů.

> [!TIP]
> Přečtěte si dvoudílný článek, který pojednává o sdíleném přístupovém podpisu: [Část 1: Vysvětlení modelu sdíleného přístupového podpisu (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md) a [Část 2: Vytvoření a používání sdíleného přístupového podpisu (SAS) se službou Blob Storage](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md). Dozvíte se další informace o zajišťování bezpečného přístupu k datům v účtu služby Storage.
>
>

## <a name="step-3-create-batch-pool"></a>Krok 3: Vytvoření fondu služby Batch
![Vytvořte fond Batch.][3]
<br/>

**Fond** Batch je kolekce výpočetních uzlů (virtuálních počítačů), na kterých služba Batch provádí úkoly z úlohy.

Po nahrání aplikačních a datových souborů do účtu úložiště pomocí rozhraní Azure Storage API zahájí *DotNetTutorial* volání služby Batch pomocí rozhraní API poskytovaných knihovnou Batch .NET. Kód nejprve vytvoří [BatchClient][net_batchclient]:

```csharp
BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials(
    BatchAccountUrl,
    BatchAccountName,
    BatchAccountKey);

using (BatchClient batchClient = BatchClient.Open(cred))
{
    ...
```

Na účtu Batch potom příklad pomocí volání `CreatePoolIfNotExistsAsync` vytvoří fond výpočetních uzlů. `CreatePoolIfNotExistsAsync` používá k vytvoření nového fondu ve službě Batch metodu [BatchClient.PoolOperations.CreatePool][net_pool_create]:

```csharp
private static async Task CreatePoolIfNotExistAsync(BatchClient batchClient, string poolId, IList<ResourceFile> resourceFiles)
{
    CloudPool pool = null;
    try
    {
        Console.WriteLine("Creating pool [{0}]...", poolId);

        // Create the unbound pool. Until we call CloudPool.Commit() or CommitAsync(), no pool is actually created in the
        // Batch service. This CloudPool instance is therefore considered "unbound," and we can modify its properties.
        pool = batchClient.PoolOperations.CreatePool(
            poolId: poolId,
            targetDedicatedComputeNodes: 3,                                             // 3 compute nodes
            virtualMachineSize: "small",                                                // single-vCPU, 1.75 GB memory, 225 GB disk
            cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));   // Windows Server 2012 R2

        // Create and assign the StartTask that will be executed when compute nodes join the pool.
        // In this case, we copy the StartTask's resource files (that will be automatically downloaded
        // to the node by the StartTask) into the shared directory that all tasks will have access to.
        pool.StartTask = new StartTask
        {
            // Specify a command line for the StartTask that copies the task application files to the
            // node's shared directory. Every compute node in a Batch pool is configured with a number
            // of pre-defined environment variables that can be referenced by commands or applications
            // run by tasks.

            // Since a successful execution of robocopy can return a non-zero exit code (e.g. 1 when one or
            // more files were successfully copied) we need to manually exit with a 0 for Batch to recognize
            // StartTask execution success.
            CommandLine = "cmd /c (robocopy %AZ_BATCH_TASK_WORKING_DIR% %AZ_BATCH_NODE_SHARED_DIR%) ^& IF %ERRORLEVEL% LEQ 1 exit 0",
            ResourceFiles = resourceFiles,
            WaitForSuccess = true
        };

        await pool.CommitAsync();
    }
    catch (BatchException be)
    {
        // Swallow the specific error code PoolExists since that is expected if the pool already exists
        if (be.RequestInformation?.BatchError != null && be.RequestInformation.BatchError.Code == BatchErrorCodeStrings.PoolExists)
        {
            Console.WriteLine("The pool {0} already existed when we tried to create it", poolId);
        }
        else
        {
            throw; // Any other exception is unexpected
        }
    }
}
```

Když vytváříte fond pomocí [CreatePool][net_pool_create], zadáváte několik parametrů, třeba počet výpočetních uzlů, [velikost uzlů](../cloud-services/cloud-services-sizes-specs.md) a operační systém uzlů. V aplikaci *DotNetTutorial* používáme [CloudServiceConfiguration][net_cloudserviceconfiguration], abychom ve službě [Cloud Services](../cloud-services/cloud-services-guestos-update-matrix.md) zadali systém Windows Server 2012 R2. 

Zadáním [VirtualMachineConfiguration][net_virtualmachineconfiguration] pro váš fond můžete také vytvořit fondy výpočetních uzlů, které jsou virtuálními počítači Azure. Fond výpočetních uzlů virtuálních počítačů můžete vytvořit z [image systému Linux](batch-linux-nodes.md) nebo Windows. Zdrojem vašich imagí virtuálních počítačů může být:

- Web [Azure Virtual Machines Marketplace][vm_marketplace] poskytující image systému Windows i Linux, které jsou připravené k použití. 
- Vlastní image, kterou připravíte a poskytnete. Další informace o vlastních imagích najdete v tématu [Vývoj rozsáhlých paralelních výpočetních řešení pomocí služby Batch](batch-api-basics.md#pool).

> [!IMPORTANT]
> Za výpočetní prostředky ve službě Batch vám budou účtované poplatky. Pokud chcete náklady minimalizovat, můžete před spuštěním ukázky snížit `targetDedicatedComputeNodes` na hodnotu 1.
>
>

Spolu s těmito fyzickými vlastnostmi uzlu můžete určit také vlastnost [StartTask][net_pool_starttask] fondu. StartTask se spustí na každém uzlu, když se takový uzel připojí k fondu, a taky pokaždé, když se uzel restartuje. StartTask je zvláště užitečná pro instalaci aplikací na výpočetní uzly před spuštěním úkolů. Pokud vaše úkoly například zpracovávají data pomocí skriptů Python, můžete StartTask použít k instalaci Pythonu na výpočetní uzly.

V této ukázkové aplikaci StartTask zkopíruje soubory, které stáhne ze služby Storage (které je určené vlastností [StartTask][net_starttask].[ResourceFiles][net_starttask_resourcefiles]) z pracovního adresáře StartTask do sdíleného adresáře, ke kterému mají přístup *všechny* úkoly spuštěné v takovém uzlu. V podstatě zkopíruje soubor `TaskApplication.exe` a jeho závislé položky do sdíleného adresáře v každém uzlu v okamžiku, kdy se uzel připojí k fondu, aby každý úkol spuštěný v uzlu měl k tomuto souboru přístup.

> [!TIP]
> Funkce **balíčků aplikací** v Azure Batch nabízí další způsob, jak dostat aplikaci na výpočetní uzly v rámci fondu. Podrobnosti najdete v tématu [Nasazení aplikací do výpočetních uzlů pomocí balíčků aplikací Batch](batch-application-packages.md).
>
>

Ve výše uvedeném fragmentu kódu je také zajímavé použití dvou proměnných prostředí ve vlastnosti *CommandLine* v StartTask: `%AZ_BATCH_TASK_WORKING_DIR%` a `%AZ_BATCH_NODE_SHARED_DIR%`. Každý výpočetní uzel v rámci fondu Batch je automaticky nakonfigurovaný pomocí řady proměnných prostředí, které se týkají služby Batch. Jakýkoli proces spuštěný úkolem má přístup k těmto proměnným prostředí.

> [!TIP]
> Další informace o proměnných prostředí, které jsou dostupné na výpočetní uzlech ve fondu Batch, a taky informace o pracovních adresářích úkolů najdete v částech [Nastavení prostředí pro úkoly](batch-api-basics.md#environment-settings-for-tasks) a [Soubory a adresáře](batch-api-basics.md#files-and-directories) v článku [Přehled funkcí služby Batch pro vývojáře](batch-api-basics.md).
>
>

## <a name="step-4-create-batch-job"></a>Krok 4: Vytvoření úlohy Batch
![Vytvoření úlohy Batch][4]<br/>

**Úloha** Batch je kolekcí úkolů a je přidružená k fondu výpočetních uzlů. Úkoly v úloze se spustit na přidružených výpočetních uzlech fondu.

Úlohu můžete použít nejen k uspořádání a sledování úkolů v souvisejících úlohách, ale také k nastavení určitých omezení – například maximálního runtime úlohy (a při rozšíření i pro její úkoly) a také priority úloh ve vztahu k dalším úlohám na účtu Batch. V tomto příkladu je úloha přidružená jenom k fondu, který byl vytvořen v kroku 3. Žádné další vlastnosti se nekonfigurují.

Všechny úlohy Batch jsou přidružené ke konkrétnímu fondu. Toto přidružení označuje uzly, na kterých se úkoly úlohy spustí. Toto určíte pomocí vlastnosti [CloudJob.PoolInformation][net_job_poolinfo], jak je ukázáno v následujícím fragmentu kódu.

```csharp
private static async Task CreateJobAsync(
    BatchClient batchClient,
    string jobId,
    string poolId)
{
    Console.WriteLine("Creating job [{0}]...", jobId);

    CloudJob job = batchClient.JobOperations.CreateJob();
    job.Id = jobId;
    job.PoolInformation = new PoolInformation { PoolId = poolId };

    await job.CommitAsync();
}
```

Po vytvoření úlohy budou přidány úkoly, které budou provádět práci.

## <a name="step-5-add-tasks-to-job"></a>Krok 5: Přidání úkolů do úlohy
![Přidání úkolů do úlohy][5]<br/>
*(1) Úkoly jsou přidány do úlohy, (2) úkoly jsou naplánovány ke spuštění na uzlech a (3) úkoly stahují datové soubory ke zpracování.*

**Úkoly** Batch jsou jednotlivé jednotky práce, které se spouští na výpočetních uzlech. Úkol má příkazový řádek a spouští skripty nebo spustitelné soubory, které jste v takovém příkazovém řádku určili.

Aby mohly skutečně provést nějakou práci, musí úkoly nejprve přidat do úlohy. Každý [CloudTask][net_task] je nakonfigurovaný pomocí vlastnosti příkazového řádku a [ResourceFiles][net_task_resourcefiles] (stejně jako u StartTask fondu), kterou si úkol stáhne do uzlu předtím, než se jeho příkazový řádek automaticky spustí. V ukázkovém projektu *DotNetTutorial* každý úkol zpracovává jenom jeden soubor. Proto jeho kolekce ResourceFiles obsahuje jen jeden prvek.

```csharp
private static async Task<List<CloudTask>> AddTasksAsync(
    BatchClient batchClient,
    string jobId,
    List<ResourceFile> inputFiles,
    string outputContainerSasUrl)
{
    Console.WriteLine("Adding {0} tasks to job [{1}]...", inputFiles.Count, jobId);

    // Create a collection to hold the tasks that we'll be adding to the job
    List<CloudTask> tasks = new List<CloudTask>();

    // Create each of the tasks. Because we copied the task application to the
    // node's shared directory with the pool's StartTask, we can access it via
    // the shared directory on the node that the task runs on.
    foreach (ResourceFile inputFile in inputFiles)
    {
        string taskId = "topNtask" + inputFiles.IndexOf(inputFile);
        string taskCommandLine = String.Format(
            "cmd /c %AZ_BATCH_NODE_SHARED_DIR%\\TaskApplication.exe {0} 3 \"{1}\"",
            inputFile.FilePath,
            outputContainerSasUrl);

        CloudTask task = new CloudTask(taskId, taskCommandLine);
        task.ResourceFiles = new List<ResourceFile> { inputFile };
        tasks.Add(task);
    }

    // Add the tasks as a collection, as opposed to issuing a separate AddTask call
    // for each. Bulk task submission helps to ensure efficient underlying API calls
    // to the Batch service.
    await batchClient.JobOperations.AddTaskAsync(jobId, tasks);

    return tasks;
}
```

> [!IMPORTANT]
> Když přistupují k proměnným prostředí, například k `%AZ_BATCH_NODE_SHARED_DIR%`, nebo když spouští aplikaci, která se nedá najít na `PATH` uzlu, musí příkazové řádky úkolu obsahovat předponu `cmd /c`. Tím se explicitně spustí překladač příkazů a dostane pokyn, aby se po provedení příkazu ukončil. Tento požadavek není nutný, pokud úkoly spouštějí jen aplikace nacházející se na `PATH` uzlu (například *robocopy.exe* nebo *powershell.exe*) a nepoužívají se žádné proměnné prostředí.
>
>

Ve smyčce `foreach` ve výše uvedeném fragmentu kódu můžete vidět, že příkazový řádek úkolu je vytvořený tak, aby se aplikaci *TaskApplication.exe* předávaly tři argumenty příkazového řádku:

1. **První argument** je cesta k souboru, který má být zpracován. Jedná se o místní cestu k souboru, protože soubor existuje na uzlu. Když byl objekt ResourceFile v `UploadFileToContainerAsync` v předchozí části vytvořen, použil se pro tuto vlastnost název souboru (jako parametr pro konstruktor ResourceFile). To znamená, že se soubor nachází ve stejném adresáři jako *TaskApplication.exe*.
2. **Druhý argument** určuje, že nejčastější slova v počtu *N* mají být zapsána do výstupního souboru. V ukázce je to pevně zakódované, aby se do výstupního souboru zapisovala tři nejčastější slova.
3. **Třetí argument** je sdílený přístupový podpis (SAS), který zajišťuje oprávnění k zápisu do kontejneru **output** ve službě Azure Storage. *TaskApplication.exe* používá tuto adresu URL se sdíleným přístupovým podpisem při nahrávání výstupního souboru do služby Azure Storage. Kód pro metodu `UploadFileToContainer` můžete najít v souboru `Program.cs` z projektu TaskApplication:

```csharp
// NOTE: From project TaskApplication Program.cs

private static void UploadFileToContainer(string filePath, string containerSas)
{
        string blobName = Path.GetFileName(filePath);

        // Obtain a reference to the container using the SAS URI.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(containerSas));

        // Upload the file (as a new blob) to the container
        try
        {
                CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
                blob.UploadFromFile(filePath);

                Console.WriteLine("Write operation succeeded for SAS URL " + containerSas);
                Console.WriteLine();
        }
        catch (StorageException e)
        {

                Console.WriteLine("Write operation failed for SAS URL " + containerSas);
                Console.WriteLine("Additional error information: " + e.Message);
                Console.WriteLine();

                // Indicate that a failure has occurred so that when the Batch service
                // sets the CloudTask.ExecutionInformation.ExitCode for the task that
                // executed this application, it properly indicates that there was a
                // problem with the task.
                Environment.ExitCode = -1;
        }
}
```

## <a name="step-6-monitor-tasks"></a>Krok 6: Sledování úkolů
![Sledujte úkoly.][6]<br/>
*Klientská aplikace (1) sleduje stav dokončení a úspěšnosti úkolů a (2) úkoly nahrávají výsledná data do služby Azure Storage.*

Pokud úkoly přidáte do úlohy, budou automaticky zařazeny do fronty a bude naplánováno jejich spuštění na výpočetních uzlech ve fondu, který je k úloze přidružený. Na základě vámi zadaných nastavení služba Batch zpracuje veškeré řazení úkolů do fronty, plánování úkolů, opakované spouštění a další povinnosti spojené se správou úkolů místo vás.

Ke sledování provádění úkolů existuje mnoho přístupů. DotNetTutorial ukazuje jednoduchý příklad, který hlásí jenom dokončení a stavy úspěchu/neúspěchu úkolu. V rámci metody `MonitorTasks` v souboru `Program.cs` z projektu DotNetTutorial existují tři koncepty Batch .NET, které je na místě prodiskutovat. Jsou uvedené níže v pořadí podle jejich výskytu:

1. **ODATADetailLevel**: Zadání [ODATADetailLevel][net_odatadetaillevel] v operaci vypsání seznamu (například získání seznamu úkolů úlohy) je důležité pro zajištění výkonu aplikace Batch. Pokud máte v úmyslu provádět jakékoli sledování stavu v aplikacích Batch, přidejte si do seznamu svých materiálů k prostudování článek [Efektivní dotazování na službu Azure Batch](batch-efficient-list-queries.md).
2. **TaskStateMonitor**: [TaskStateMonitor][net_taskstatemonitor] poskytuje aplikacím Batch .NET pomocné nástroje ke sledování stavů úkolů. V `MonitorTasks` aplikace *DotNetTutorial* počká, až všechny úkoly dosáhnou ve stanoveném časovém limitu stavu [TaskState.Completed][net_taskstate]. Potom úlohu ukončí.
3. **TerminateJobAsync**: Ukončení úlohy pomocí [JobOperations.TerminateJobAsync][net_joboperations_terminatejob] (nebo blokování JobOperations.TerminateJob) označí tuto úlohu jako dokončenou. To je velmi důležité provést, pokud vaše řešení Batch používá [JobReleaseTask][net_jobreltask]. Jedná se o zvláštní typ úkolu, který je popsaný v článku [Úkoly přípravy a dokončení úlohy](batch-job-prep-release.md).

Metodu `MonitorTasks` ze souboru `Program.cs` v aplikaci *DotNetTutorial* vidíte zde:

```csharp
private static async Task<bool> MonitorTasks(
    BatchClient batchClient,
    string jobId,
    TimeSpan timeout)
{
    bool allTasksSuccessful = true;
    const string successMessage = "All tasks reached state Completed.";
    const string failureMessage = "One or more tasks failed to reach the Completed state within the timeout period.";

    // Obtain the collection of tasks currently managed by the job. Note that we use
    // a detail level to  specify that only the "id" property of each task should be
    // populated. Using a detail level for all list operations helps to lower
    // response time from the Batch service.
    ODATADetailLevel detail = new ODATADetailLevel(selectClause: "id");
    List<CloudTask> tasks =
        await batchClient.JobOperations.ListTasks(JobId, detail).ToListAsync();

    Console.WriteLine("Awaiting task completion, timeout in {0}...",
        timeout.ToString());

    // We use a TaskStateMonitor to monitor the state of our tasks. In this case, we
    // will wait for all tasks to reach the Completed state.
    TaskStateMonitor taskStateMonitor
        = batchClient.Utilities.CreateTaskStateMonitor();

    try
    {
        await taskStateMonitor.WhenAll(tasks, TaskState.Completed, timeout);
    }
    catch (TimeoutException)
    {
        await batchClient.JobOperations.TerminateJobAsync(jobId, failureMessage);
        Console.WriteLine(failureMessage);
        return false;
    }

    await batchClient.JobOperations.TerminateJobAsync(jobId, successMessage);

    // All tasks have reached the "Completed" state, however, this does not
    // guarantee all tasks completed successfully. Here we further check each task's
    // ExecutionInfo property to ensure that it did not encounter a failure
    // or return a non-zero exit code.

    // Update the detail level to populate only the task id and executionInfo
    // properties. We refresh the tasks below, and need only this information for
    // each task.
    detail.SelectClause = "id, executionInfo";

    foreach (CloudTask task in tasks)
    {
        // Populate the task's properties with the latest info from the Batch service
        await task.RefreshAsync(detail);

        if (task.ExecutionInformation.Result == TaskExecutionResult.Failure)
        {
            // A task with failure information set indicates there was a problem with the task. It is important to note that
            // the task's state can be "Completed," yet still have encountered a failure.

            allTasksSuccessful = false;

            Console.WriteLine("WARNING: Task [{0}] encountered a failure: {1}", task.Id, task.ExecutionInformation.FailureInformation.Message);
            if (task.ExecutionInformation.ExitCode != 0)
            {
                // A non-zero exit code may indicate that the application executed by the task encountered an error
                // during execution. As not every application returns non-zero on failure by default (e.g. robocopy),
                // your implementation of error checking may differ from this example.

                Console.WriteLine("WARNING: Task [{0}] returned a non-zero exit code - this may indicate task execution or completion failure.", task.Id);
            }
        }
    }

    if (allTasksSuccessful)
    {
        Console.WriteLine("Success! All tasks completed successfully within the specified timeout period.");
    }

    return allTasksSuccessful;
}
```

## <a name="step-7-download-task-output"></a>Krok 7: Stažení výstupu úkolu
![Stažení výstupu úkolu ze služby Storage][7]<br/>

Po dokončení úlohy můžete ze služby Azure Storage stáhnout výstup úkolů. To provedete pomocí volání metody `DownloadBlobsFromContainerAsync` v souboru `Program.cs` z aplikace *DotNetTutorial*:

```csharp
private static async Task DownloadBlobsFromContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string directoryPath)
{
        Console.WriteLine("Downloading all files from container [{0}]...", containerName);

        // Retrieve a reference to a previously created container
        CloudBlobContainer container = blobClient.GetContainerReference(containerName);

        // Get a flat listing of all the block blobs in the specified container
        foreach (IListBlobItem item in container.ListBlobs(
                    prefix: null,
                    useFlatBlobListing: true))
        {
                // Retrieve reference to the current blob
                CloudBlob blob = (CloudBlob)item;

                // Save blob contents to a file in the specified folder
                string localOutputFile = Path.Combine(directoryPath, blob.Name);
                await blob.DownloadToFileAsync(localOutputFile, FileMode.Create);
        }

        Console.WriteLine("All files downloaded to {0}", directoryPath);
}
```

> [!NOTE]
> Volání `DownloadBlobsFromContainerAsync` v aplikaci *DotNetTutorial* určuje, že soubory se mají stahovat do složky `%TEMP%`. Umístění výstupu můžete podle libosti změnit.
>
>

## <a name="step-8-delete-containers"></a>Krok 8: Odstranění kontejnerů
Vzhledem k tomu, že musíte platit za data, která si necháváte ve službě Azure Storage, doporučujeme odebrat objekty blob, které už pro úlohy Batch nepotřebujete. V souboru `Program.cs` z aplikace DotNetTutorial se to provádí pomocí tří volání pomocné metody `DeleteContainerAsync`:

```csharp
// Clean up Storage resources
await DeleteContainerAsync(blobClient, appContainerName);
await DeleteContainerAsync(blobClient, inputContainerName);
await DeleteContainerAsync(blobClient, outputContainerName);
```

Metoda sama jenom získá referencí na kontejner a potom zavolá [CloudBlobContainer.DeleteIfExistsAsync][net_container_delete]:

```csharp
private static async Task DeleteContainerAsync(
    CloudBlobClient blobClient,
    string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    if (await container.DeleteIfExistsAsync())
    {
        Console.WriteLine("Container [{0}] deleted.", containerName);
    }
    else
    {
        Console.WriteLine("Container [{0}] does not exist, skipping deletion.",
            containerName);
    }
}
```

## <a name="step-9-delete-the-job-and-the-pool"></a>Krok 9: Odstranění úlohy a fondu
V posledním kroku budete vyzváni k odstranění úlohy a fondu, které vytvořila aplikace DotNetTutorial. I když se vám neúčtují poplatky za úlohy a úlohy samotné, *účtují* se vám poplatky za výpočetní uzly. Proto doporučujeme, abyste uzly přidělovali, jen když je to potřeba. Odstraňování nepoužívaných fondů by mělo být součástí vašeho standardního procesu údržby.

[JobOperations][net_joboperations] a [PoolOperations][net_pooloperations] z BatchClient mají odpovídající metody odstranění, které se volají, pokud uživatel potvrdí odstranění:

```csharp
// Clean up the resources we've created in the Batch account if the user so chooses
Console.WriteLine();
Console.WriteLine("Delete job? [yes] no");
string response = Console.ReadLine().ToLower();
if (response != "n" && response != "no")
{
    await batchClient.JobOperations.DeleteJobAsync(JobId);
}

Console.WriteLine("Delete pool? [yes] no");
response = Console.ReadLine();
if (response != "n" && response != "no")
{
    await batchClient.PoolOperations.DeletePoolAsync(PoolId);
}
```

> [!IMPORTANT]
> Pamatujte, že se vám účtují poplatky za výpočetní prostředky, takže odstranění nepoužívaných fondů vám ušetří náklady. Musíme ale upozornit, že odstraněním fondu odstraníte všechny výpočetní uzly v takovém fondu a veškerá data na uzlech budou po odstranění fondu ztracená.
>
>

## <a name="run-the-dotnettutorial-sample"></a>Spuštění ukázkové aplikace *DotNetTutorial*
Když spustíte ukázkovou aplikaci, bude výstup konzoly podobný následujícímu. Během provádění dojde k pozastavení při `Awaiting task completion, timeout in 00:30:00...` a mezitím se spustí výpočetní uzly fondu. Ke sledování fondu, výpočetních uzlů, úlohy a úkolů během a po spuštění použijte [Azure Portal][azure_portal]. K zobrazení prostředků služby Storage (kontejnerů a objektů blob), které vytvořila aplikace, použijte [Azure Portal][azure_portal] nebo [Azure Storage Explorer][storage_explorers].

Typická doba provádění je **přibližně 5 minut**, když aplikaci spouštíte v její výchozí konfiguraci.

```
Sample start: 1/8/2016 09:42:58 AM

Container [application] created.
Container [input] created.
Container [output] created.
Uploading file C:\repos\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial\bin\Debug\TaskApplication.exe to container [application]...
Uploading file Microsoft.WindowsAzure.Storage.dll to container [application]...
Uploading file ..\..\taskdata1.txt to container [input]...
Uploading file ..\..\taskdata2.txt to container [input]...
Uploading file ..\..\taskdata3.txt to container [input]...
Creating pool [DotNetTutorialPool]...
Creating job [DotNetTutorialJob]...
Adding 3 tasks to job [DotNetTutorialJob]...
Awaiting task completion, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Downloading all files from container [output]...
All files downloaded to C:\Users\USERNAME\AppData\Local\Temp
Container [application] deleted.
Container [input] deleted.
Container [output] deleted.

Sample end: 1/8/2016 09:47:47 AM
Elapsed time: 00:04:48.5358142

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>Další kroky
Nebojte se provádět v projektu *DotNetTutorial* a *TaskApplication* změny a experimentovat s různými výpočetními scénáři. Zkuste třeba do *TaskApplication* přidat prodlevu provádění, jaká je u [Thread.Sleep][net_thread_sleep], abyste mohli simulovat dlouhotrvající úlohy a sledovat je na portálu. Zkuste přidat další úkoly nebo upravit počet výpočetních uzlů. Přidejte logiku pro kontrolu a povolte použití existujícího fondu, abyste urychlili čas provádění (*tip*: podívejte se na soubor `ArticleHelpers.cs` v projektu [Microsoft.Azure.Batch.Samples.Common][github_samples_common] v [azure-batch-samples][github_samples]).

Teď, když jste se seznámili se základním pracovním postupem řešení Batch, je čas proniknout do dalších funkcí služby Batch.

* Přečtěte si článek [Přehled funkcí Azure Batch](batch-api-basics.md), který doporučujeme všem novým uživatelům služby.
* Začněte u dalších článků o vývoji pro Batch, které najdete v [Postupu výuky pro Batch][batch_learning_path] v části **Podrobný popis vývoje**.
* Podívejte se na různé implementace zpracování úlohy „N nejčastějších slov“ a použijte k tomu Batch v ukázce [TopNWords][github_topnwords].
* Zkontrolujte [poznámky k verzi](https://github.com/Azure/azure-sdk-for-net/blob/psSdkJson6/src/SDKs/Batch/DataPlane/changelog.md#azurebatch-release-notes) Batch .NET, kde najdete nejnovější změny v knihovně.

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[github_batchlabs]: https://azure.github.io/BatchLabs/
[github_dotnettutorial]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/DotNetTutorial
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_common]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/Common
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[net_api]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[net_api_storage]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudblobclient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobclient.aspx
[net_cloudblobcontainer]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudserviceconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudserviceconfiguration.aspx
[net_container_delete]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteifexistsasync.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_poolinfo]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.protocol.models.cloudjob.poolinformation.aspx
[net_joboperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.joboperations
[net_joboperations_terminatejob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_jobpreptask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_jobreltask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_odatadetaillevel]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_pooloperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.pooloperations
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_resourcefile_blobsource]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.blobsource.aspx
[net_sas_blob]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblob.getsharedaccesssignature.aspx
[net_sas_container]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblobcontainer.getsharedaccesssignature.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.resourcefiles.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_task_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.resourcefiles.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_taskstatemonitor]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskstatemonitor.aspx
[net_thread_sleep]: https://msdn.microsoft.com/library/274eh01d(v=vs.110).aspx
[net_virtualmachineconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.virtualmachineconfiguration.aspx
[nuget_packagemgr]: https://docs.nuget.org/consume/installing-nuget
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build
[storage_explorers]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/vs/
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

[1]: ./media/batch-dotnet-get-started/batch_workflow_01_sm.png "Vytvoření kontejnerů ve službě Azure Storage"
[2]: ./media/batch-dotnet-get-started/batch_workflow_02_sm.png "Odeslání aplikačních a vstupních (datových) souborů úkolů do kontejnerů"
[3]: ./media/batch-dotnet-get-started/batch_workflow_03_sm.png "Vytvoření fondu Batch"
[4]: ./media/batch-dotnet-get-started/batch_workflow_04_sm.png "Vytvoření úlohy Batch"
[5]: ./media/batch-dotnet-get-started/batch_workflow_05_sm.png "Přidání úkolů do úlohy"
[6]: ./media/batch-dotnet-get-started/batch_workflow_06_sm.png "Sledování úkolů"
[7]: ./media/batch-dotnet-get-started/batch_workflow_07_sm.png "Stažení výstupu úkolu ze služby Storage"
[8]: ./media/batch-dotnet-get-started/batch_workflow_sm.png "Pracovní postup řešení Batch (úplný diagram)"
[9]: ./media/batch-dotnet-get-started/credentials_batch_sm.png "Přihlašovací údaje Batch na portálu"
[10]: ./media/batch-dotnet-get-started/credentials_storage_sm.png "Přihlašovací údaje služby Storage na portálu"
[11]: ./media/batch-dotnet-get-started/batch_workflow_minimal_sm.png "Pracovní postup řešení Batch (minimální diagram)"
