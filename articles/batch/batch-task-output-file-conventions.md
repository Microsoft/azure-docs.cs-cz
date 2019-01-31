---
title: Trvalý výstup úloh a ke službě Azure Storage pomocí knihovny File Conventions pro .NET – Azure Batch | Dokumentace Microsoftu
description: Další informace o použití knihovny Azure Batch File Conventions pro .NET pro trvalý výstup úloh a úlohy služby Batch do služby Azure Storage a zobrazení trvalý výstup na webu Azure Portal.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/14/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 970cab2c782e71defbda828a42273f317dee80e4
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55472979"
---
# <a name="persist-job-and-task-data-to-azure-storage-with-the-batch-file-conventions-library-for-net"></a>Uchování úloh a dat do služby Azure Storage pomocí knihovny File Conventions služby Batch pro .NET

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Jedním ze způsobů k uchování dat. úloha je použít [knihovny Azure Batch File Conventions pro .NET][nuget_package]. Knihovny File Conventions zjednodušuje proces ukládání výstupních dat úloh do služby Azure Storage a jeho načítání. Můžete použít knihovny File Conventions v kódu úkolu a klient &mdash; v úkolu kódu pro zachování souborů a v klientském kódu do seznamu a je načíst. Váš kód úlohy můžete také použít knihovnu k načtení výstupu nadřazeného úkoly, jako například v [závislosti úkolů](batch-task-dependencies.md) scénář.

Načtení výstupních souborů pomocí knihovny File Conventions, najdou soubory pro dané úlohy nebo úkolu jejich uvedení v seznamu podle ID a účel. Není nutné znát názvy nebo umístění souborů. Můžete například pomocí knihovny File Conventions můžete zobrazit seznam všechny dočasné soubory pro danou úlohu nebo získat soubor ve verzi preview pro danou úlohu.

> [!TIP]
> Od verze 2017-05-01, rozhraní API služby Batch podporuje zachování výstupní data do služby Azure Storage pro úkoly a úkolech Správce úloh, které běží na fondy vytvořené s konfigurací virtuálního počítače. Rozhraní API služby Batch poskytuje jednoduchý způsob, jak zachovat výstup z v rámci kódu, který vytvoří úkol a slouží jako alternativu k knihovny File Conventions. Můžete upravit klientských aplikací služby Batch pro trvalý výstup bez nutnosti aktualizovat aplikaci, na kterém běží vaše úlohy. Další informace najdete v tématu [trvalého úkolů data do služby Azure Storage pomocí služby Batch služby API](batch-task-output-files.md).

## <a name="when-do-i-use-the-file-conventions-library-to-persist-task-output"></a>Když pomocí knihovny File Conventions zachovat výstup úlohy?

Azure Batch poskytuje více než jeden způsob, jak zachovat výstup úlohy. File Conventions je nejvhodnější pro tyto scénáře:

- Můžete snadno upravit kód pro aplikace, na kterém běží vaše úlohy k trvalému ukládání souborů pomocí knihovny File Conventions.
- Chcete Streamovat data do služby Azure Storage při běhu úlohy.
- Chcete zachovat data z fondy vytvořené s konfigurací cloudové služby nebo konfigurace virtuálního počítače.
- Klientská aplikace nebo další úkoly v úloze musí vyhledat a stáhnout výstupní soubory úloh podle ID nebo podle účelu.
- Chcete zobrazit výstup úlohy na webu Azure Portal.

Pokud váš scénář se liší od těch uvedené výše, budete muset vzít v úvahu jiný přístup. Další informace o dalších možnostech pro zachování výstup úlohy najdete v tématu [trvalý výstup úloh a ke službě Azure Storage](batch-task-output.md).

## <a name="what-is-the-batch-file-conventions-standard"></a>Co je Batch File Conventions standard?

[Batch File Conventions standard](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions) obsahuje schéma pojmenování pro určení kontejnery a cesty k objektům blob ke kterým jsou zapsány výstupní soubory. Soubory se ukládají do Azure Storage, který dodržovat standardní File Conventions jsou automaticky dostupné pro zobrazení na webu Azure Portal. Na portálu si je vědoma zásady vytváření názvů a proto můžete zobrazit soubory, které se řídí ji.

Knihovny File Conventions pro .NET automaticky názvy kontejnerů úložiště a výstupní soubory úloh podle standardní konvence souboru. Knihovny File Conventions dále poskytuje metody pro výstupní soubory ve službě Azure Storage podle ID úlohy, ID úlohy nebo účely dotazů.

Pokud vyvíjíte pomocí jiného jazyka než .NET, můžete implementovat standard File Conventions sami ve vaší aplikaci. Další informace najdete v tématu [implementovat standard Batch File Conventions](batch-task-output.md#implement-the-batch-file-conventions-standard).

## <a name="link-an-azure-storage-account-to-your-batch-account"></a>Propojení účtu služby Azure Storage s účtem Batch

Zachování výstupní data do služby Azure Storage pomocí knihovny File Conventions, je třeba nejprve propojit účet služby Azure Storage k účtu Batch. Pokud jste tak ještě neučinili, propojit účet úložiště do svého účtu Batch pomocí [webu Azure portal](https://portal.azure.com):

1. Na webu Azure Portal přejděte ke svému účtu Batch.
1. V části **nastavení**vyberte **účtu úložiště**.
1. Pokud ještě nemáte účet úložiště spojené s vaším účtem Batch, klikněte na tlačítko **účtu úložiště (žádný)**.
1. Vyberte ze seznamu pro vaše předplatné účet úložiště. Pro zajištění nejlepšího výkonu použijte účet služby Azure Storage, který je ve stejné oblasti jako účet Batch, kde běží vaše úlohy.

## <a name="persist-output-data"></a>Zachovat výstupní data

Uchování úloh a výstupních dat pomocí knihovny File Conventions, vytvořit kontejner ve službě Azure Storage a potom uložte výstup do kontejneru. Použití [Klientská knihovna Azure Storage pro .NET](https://www.nuget.org/packages/WindowsAzure.Storage) ve svém kódu úkol pro nahrání výstupu úkolu do kontejneru. 

Další informace o práci s kontejnery a objekty BLOB ve službě Azure Storage najdete v tématu [Začínáme s Azure Blob storage pomocí .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

> [!WARNING]
> Všechny výstupy úloh a trvalé s File Conventions knihovny jsou uložené ve stejném kontejneru. Pokud k trvalému ukládání souborů ve stejnou dobu, zkuste velký počet úkolů [úložiště limitů omezování](../storage/common/storage-performance-checklist.md#blobs) může být požadováno.

### <a name="create-storage-container"></a>Vytvoření kontejneru úložiště

Zachovat výstup úlohy do služby Azure Storage, nejprve vytvořte kontejner zavoláním [CloudJob][net_cloudjob].[ PrepareOutputStorageAsync][net_prepareoutputasync]. Tato metoda rozšíření přebírá [CloudStorageAccount] [ net_cloudstorageaccount] objektu jako parametr. Vytvoří kontejner s názvem podle standardu File Conventions tak, aby jeho obsah zjistitelné pomocí webu Azure portal a načtení metod popsaných dále v tomto článku.

Obvykle umístěte kód k vytvoření kontejneru v klientské aplikaci &mdash; aplikaci, která vytvoří fondy, úlohy a úkoly.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob(
    "myJob",
    new PoolInformation { PoolId = "myPool" });

// Create reference to the linked Azure Storage account
CloudStorageAccount linkedStorageAccount =
    new CloudStorageAccount(myCredentials, true);

// Create the blob storage container for the outputs
await job.PrepareOutputStorageAsync(linkedStorageAccount);
```

### <a name="store-task-outputs"></a>Výstupy úlohy Store

Teď, když budete připraveni kontejner ve službě Azure Storage, úlohy můžete uložit výstup do kontejneru [TaskOutputStorage] [ net_taskoutputstorage] třída součástí knihovny File Conventions.

Ve vašem kódu úloh vytvoření [TaskOutputStorage] [ net_taskoutputstorage] objekt a potom když se úloha dokončí svou práci, volání [TaskOutputStorage] [ net_taskoutputstorage]. [SaveAsync] [ net_saveasync] metody uložte svůj výstup do služby Azure Storage.

```csharp
CloudStorageAccount linkedStorageAccount = new CloudStorageAccount(myCredentials);
string jobId = Environment.GetEnvironmentVariable("AZ_BATCH_JOB_ID");
string taskId = Environment.GetEnvironmentVariable("AZ_BATCH_TASK_ID");

TaskOutputStorage taskOutputStorage = new TaskOutputStorage(
    linkedStorageAccount, jobId, taskId);

/* Code to process data and produce output file(s) */

await taskOutputStorage.SaveAsync(TaskOutputKind.TaskOutput, "frame_full_res.jpg");
await taskOutputStorage.SaveAsync(TaskOutputKind.TaskPreview, "frame_low_res.jpg");
```

`kind` Parametr [TaskOutputStorage](https://msdn.microsoft.com/library/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx).[ SaveAsync](https://msdn.microsoft.com/library/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx) metoda kategorizuje trvalé soubory. Existují čtyři předdefinované [TaskOutputKind] [ net_taskoutputkind] typy: `TaskOutput`, `TaskPreview`, `TaskLog`, a `TaskIntermediate.` můžete také definovat vlastní kategorie výstupu.

Tyto typy výstupu bylo možné určit typ výstupy do seznamu při později dotazování služby Batch pro trvalé výstupy dané úlohy. Jinými slovy zobrazením výstupy pro úkol můžete filtrovat seznam na jednom z formátů výstupu. Například "uvést *ve verzi preview* výstup pro úlohu *109*." Další informace o zobrazení a načítání výstupů se zobrazí v [načíst výstup](#retrieve-output) dále v tomto článku.

> [!TIP]
> Druh výstupu také určuje, kde na webu Azure Portal zobrazuje určitého souboru: *TaskOutput*-zařazená do kategorie soubory se zobrazí v části **výstupní soubory úloh**, a *TaskLog* soubory se zobrazí v části **úkolů protokoly**.

### <a name="store-job-outputs"></a>Store výstupy úlohy

Kromě ukládání výstupy úloh, můžete uložit výstupy přidružené celý projekt. Například v úloze sloučení úlohy vykreslování film, můžete zachovat plně vykreslené film jako výstup úlohy. Po dokončení úlohy může klientská aplikace seznamu nebo načítat výstupy úlohy a nemusí dotazovat jednotlivých úkolů.

Store výstup úlohy voláním [JobOutputStorage][net_joboutputstorage].[ SaveAsync] [ net_joboutputstorage_saveasync] metoda a určit, [JobOutputKind] [ net_joboutputkind] a název souboru:

```csharp
CloudJob job = new JobOutputStorage(acct, jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

Stejně jako u **TaskOutputKind** typ pro výstupy úloh, je použít [JobOutputKind] [ net_joboutputkind] typ ke kategorizaci úlohy je trvalé soubory. Tento parametr umožňuje novější dotaz pro konkrétní typ výstupu (seznam). **JobOutputKind** typ obsahuje kategorie, výstupu a ve verzi preview a podporuje vytváření vlastní kategorie.

### <a name="store-task-logs"></a>Protokoly úlohy Store

Kromě trvalém ukládání souboru do trvalého úložiště po dokončení úlohy nebo úlohy, budete muset zachovat soubory, které jsou aktualizovány při spuštění úlohy &mdash; soubory protokolu nebo `stdout.txt` a `stderr.txt`, např. K tomuto účelu poskytuje knihovny Azure Batch File Conventions [TaskOutputStorage][net_taskoutputstorage].[ SaveTrackedAsync] [ net_savetrackedasync] metody. S [SaveTrackedAsync][net_savetrackedasync], můžete sledovat aktualizace do souboru na uzel (v intervalu, který zadáte) a zachovat tyto aktualizace do služby Azure Storage.

V následujícím fragmentu kódu používáme [SaveTrackedAsync] [ net_savetrackedasync] aktualizovat `stdout.txt` ve službě Azure Storage během provádění úkolu každých 15 sekund:

```csharp
TimeSpan stdoutFlushDelay = TimeSpan.FromSeconds(3);
string logFilePath = Path.Combine(
    Environment.GetEnvironmentVariable("AZ_BATCH_TASK_DIR"), "stdout.txt");

// The primary task logic is wrapped in a using statement that sends updates to
// the stdout.txt blob in Storage every 15 seconds while the task code runs.
using (ITrackedSaveOperation stdout =
        await taskStorage.SaveTrackedAsync(
        TaskOutputKind.TaskLog,
        logFilePath,
        "stdout.txt",
        TimeSpan.FromSeconds(15)))
{
    /* Code to process data and produce output file(s) */

    // We are tracking the disk file to save our standard output, but the
    // node agent may take up to 3 seconds to flush the stdout stream to
    // disk. So give the file a moment to catch up.
     await Task.Delay(stdoutFlushDelay);
}
```

Části komentářem `Code to process data and produce output file(s)` je zástupný symbol pro kód, který bude obvykle provádět úlohy. Například může mít kód, který načte data ze služby Azure Storage a provede transformaci nebo výpočet ho. Důležitou součástí tento fragment kódu je ukázka, jak můžete zalomit takový kód v `using` blok se pravidelně aktualizují váš soubor se [SaveTrackedAsync][net_savetrackedasync].

Agenta uzlu je program, který se spustí na každém uzlu ve fondu a poskytuje rozhraní příkazu a řízení mezi uzlem a služby Batch. `Task.Delay` Volání je nutné na konci tohoto `using` blok k Ujistěte se, že má agent uzlu čas Vyprázdnit obsah standardní výstupní soubor stdout.txt na uzlu. Bez prodlevy je možné si ujít posledních několik sekund výstup. Toto zpoždění nemusí být požadovaný pro všechny soubory.

> [!NOTE]
> Když povolíte sledování pomocí souborů **SaveTrackedAsync**, pouze *připojí* sledované souboru se ukládají do Azure Storage. Tuto metodu lze používejte pouze pro sledování-rotaci souborů protokolu nebo jiné soubory, které jsou zapsány do s doplňovací operace na konec souboru.

## <a name="retrieve-output-data"></a>Načtení výstupních dat

Při načítání trvalý výstup pomocí knihovny Azure Batch File Conventions provedete úlohy a úlohy zaměřené na způsobem. Můžete požádat o výstup pro danou úlohu nebo úlohy, aniž by museli znát cestu ve službě Azure Storage nebo i její název souboru. Místo toho můžete požádat o výstupní soubory úlohy nebo úlohy ID.

Následující fragment kódu prochází z úlohy, vypíše některé informace o výstupních souborů pro úlohy a pak stáhne soubory ze služby Storage.

```csharp
foreach (CloudTask task in myJob.ListTasks())
{
    foreach (OutputFileReference output in
        task.OutputStorage(storageAccount).ListOutputs(
            TaskOutputKind.TaskOutput))
    {
        Console.WriteLine($"output file: {output.FilePath}");

        output.DownloadToFileAsync(
            $"{jobId}-{output.FilePath}",
            System.IO.FileMode.Create).Wait();
    }
}
```

## <a name="view-output-files-in-the-azure-portal"></a>Zobrazit výstupní soubory na webu Azure Portal

Na webu Azure portal zobrazuje výstupní soubory úlohy a protokoly, které jsou zachované v propojené služby Azure Storage account pomocí [Batch File Conventions standard](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions). Můžete implementovat tyto konvence sami v jazyce podle vašeho výběru, nebo můžete pomocí knihovny File Conventions v aplikacích .NET.

Chcete-li zobrazit výstupní soubory na portálu, musí splňovat následující požadavky:

1. [Propojit účet Azure Storage](#requirement-linked-storage-account) k účtu Batch.
1. Při trvalém ukládání výstupů proto zavázala dodržovat předdefinované zásady vytváření názvů pro kontejnery úložiště a soubory. Definice těchto konvence můžete najít v knihovny File Conventions [README][github_file_conventions_readme]. Pokud používáte [Azure Batch File Conventions] [ nuget_package] knihovny k uchování vašich výstupní soubory jsou trvalé podle standardu File Conventions.

Chcete-li zobrazit výstupní soubory úloh a protokolů na webu Azure Portal, přejděte na úlohu, jejíž výstup, který vás zajímá, pak klikněte na možnost **uložené výstupní soubory** nebo **uloženy protokoly**. Tento obrázek ukazuje, **uložené výstupní soubory** pro úlohu s ID "007":

![Okno výstupy úloh na webu Azure Portal][2]

## <a name="code-sample"></a>Ukázka kódu

[PersistOutputs] [ github_persistoutputs] ukázkový projekt je jedním z [ukázky kódu Azure Batch] [ github_samples] na Githubu. Toto řešení sady Visual Studio ukazuje, jak pomocí knihovny Azure Batch File Conventions trvalý výstup úloh do trvalého úložiště. Ke spuštění ukázky, postupujte podle těchto kroků:

1. Otevřete projekt v **Visual Studio 2017**.
2. Přidání služby Batch a Storage **přihlašovací údaje účtu** k **AccountSettings.settings** Microsoft.Azure.Batch.Samples.Common projektu.
3. **Sestavení** (ale nespouštějte) řešení. Obnovení všech balíčků NuGet, pokud se zobrazí výzva.
4. Pomocí webu Azure portal k odeslání [balíčku aplikace](batch-application-packages.md) pro **PersistOutputsTask**. Zahrnout `PersistOutputsTask.exe` a jeho závislých sestavení v balíčku .zip, nastavte na "PersistOutputsTask" ID aplikace a verze balíčku aplikace má "1.0".
5. **Spustit** (spustit) **PersistOutputs** projektu.
6. Po zobrazení výzvy k výběru technologie trvalosti pro používání vzorku, zadejte **1** ke spuštění ukázky pomocí knihovny File Conventions trvalý výstup úloh. 

## <a name="next-steps"></a>Další postup

### <a name="get-the-batch-file-conventions-library-for-net"></a>Získat knihovny File Conventions služby Batch pro .NET

Batch File Conventions library pro .NET je k dispozici na [NuGet][nuget_package]. Knihovny rozšiřuje [CloudJob] [ net_cloudjob] a [CloudTask] [ net_cloudtask] tříd pomocí nové metody. Viz také [referenční dokumentaci](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files) pro knihovny File Conventions.

[Zdrojový kód] [ github_file_conventions] konvence soubor knihovny je dostupný na Githubu v Microsoft Azure SDK for .NET do úložiště. 

### <a name="explore-other-approaches-for-persisting-output-data"></a>Prozkoumejte další přístupy k zachování výstupní data

- Zobrazit [trvalý výstup úloh a ke službě Azure Storage](batch-task-output.md) přehledné informace o zachování dat úloh a úloh.
- V tématu [trvalého úkolů data do služby Azure Storage pomocí služby Batch služby API](batch-task-output-files.md) se naučíte používat rozhraní API služby Batch k uchování výstupní data.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Batch/FileConventions
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[net_joboutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputkind.aspx
[net_joboutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.aspx
[net_joboutputstorage_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.saveasync.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_prepareoutputasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.cloudjobextensions.prepareoutputstorageasync.aspx
[net_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx
[net_savetrackedasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.savetrackedasync.aspx
[net_taskoutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputkind.aspx
[net_taskoutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx
[nuget_manager]: https://docs.nuget.org/consume/installing-nuget
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: http://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "Uložené výstupní soubory a protokoly selektory uložený na portálu"
[2]: ./media/batch-task-output/task-output-02.png "Okno výstupy úloh na webu Azure Portal"
