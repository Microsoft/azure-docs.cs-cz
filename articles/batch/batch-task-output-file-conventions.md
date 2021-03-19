---
title: Zachovat výstupní data Azure Storage pomocí knihovny konvence souborů .NET
description: Naučte se, jak pomocí Azure Batch knihovny konvencí souborů pro .NET uchovat výstup úlohy & dávkové úlohy do Azure Storage a zobrazit tento výstup v Azure Portal.
ms.topic: how-to
ms.date: 11/14/2018
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 1a45eed421dd8d734fcef0dd452df1d4a65fd053
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88936958"
---
# <a name="persist-job-and-task-data-to-azure-storage-with-the-batch-file-conventions-library-for-net"></a>Trvalá data úloh a úloh pro Azure Storage s knihovnou konvence souborů Batch pro .NET

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Jedním ze způsobů, jak zachovat data úloh, je použít [knihovnu Azure Batchch konvencí souborů pro .NET][nuget_package]. Knihovna konvence souborů zjednodušuje proces ukládání výstupních dat úlohy do Azure Storage a jejich načítání. Můžete použít knihovnu File Conventions v kódu úlohy i klienta &mdash; v kódu úlohy pro trvalé soubory a v kódu klienta k vypsání a načtení. Kód úlohy může také pomocí knihovny načíst výstup nadřazeného úkolu, jako je například ve scénáři [závislosti úkolů](batch-task-dependencies.md) .

Chcete-li načíst výstupní soubory pomocí knihovny konvence souborů, můžete vyhledat soubory pro danou úlohu nebo úlohu jejich výpisem podle ID a účelu. Nemusíte znát názvy ani umístění souborů. Například můžete použít knihovnu přípon souborů k vypsání všech zprostředkujících souborů pro danou úlohu nebo získat soubor ve verzi Preview pro danou úlohu.

> [!TIP]
> Od verze 2017-05-01 poskytuje rozhraní API služby Batch trvalé ukládání výstupních dat Azure Storage pro úlohy a úlohy Správce úloh, které se spouštějí ve fondech vytvořených pomocí konfigurace virtuálního počítače. Rozhraní API služby Batch poskytuje jednoduchý způsob, jak zachovat výstup z kódu, který vytváří úlohu, a slouží jako alternativa k knihovně souborových konvencí. Můžete upravit klientské aplikace Batch tak, aby zachovaly výstup bez nutnosti aktualizovat aplikaci, kterou váš úkol spouští. Další informace najdete v tématu [trvalá data o úkolech pro Azure Storage pomocí rozhraní API služby Batch](batch-task-output-files.md).

## <a name="when-do-i-use-the-file-conventions-library-to-persist-task-output"></a>Kdy se používá knihovna konvence souborů k uložení výstupu úlohy?

Azure Batch poskytuje více než jeden způsob, jak uchovat výstup úlohy. K těmto scénářům nejlépe vyhovuje konvence souborů:

- Můžete snadno upravit kód aplikace, na které je úloha spuštěná, aby se soubory zachovaly pomocí knihovny konvence souborů.
- Chcete streamovat data, která se Azure Storage, když je úloha pořád spuštěná.
- Chcete zachovat data z fondů vytvořených s konfigurací cloudové služby nebo s konfigurací virtuálního počítače.
- Klientská aplikace nebo jiné úkoly v úloze potřebují vyhledat a stáhnout výstupní soubory úlohy podle ID nebo podle účelu.
- Chcete zobrazit výstup úlohy v Azure Portal.

Pokud se váš scénář liší od výše uvedených výše, možná budete muset zvážit jiný přístup. Další informace o dalších možnostech pro zachování výstupu úlohy najdete v tématu [trvalé uložení úloh a úloh do Azure Storage](batch-task-output.md).

## <a name="what-is-the-batch-file-conventions-standard"></a>Jaké jsou standardní konvence souborů pro dávkové soubory?

[Standardní zásady pro soubory služby Batch](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files) poskytují schéma pojmenování pro cílové kontejnery a cesty objektů blob, na které se zapisují vaše výstupní soubory. Soubory trvalé pro Azure Storage, které vyhovují standardům souborů, jsou automaticky k dispozici pro zobrazení v Azure Portal. Portál ví o konvenci vytváření názvů a může tak zobrazit soubory, které s ním vyhovují.

Knihovna konvence souborů pro .NET automaticky pojmenuje kontejnery úložiště a výstupní soubory úloh podle standardu souborů. Knihovna konvence souborů také poskytuje metody pro dotazování na výstupní soubory v Azure Storage podle ID úlohy, ID úlohy nebo účelu.

Pokud vyvíjíte v jiném jazyce než .NET, můžete implementovat vlastní konvence souborů sami ve své aplikaci. Další informace najdete v tématu [implementace standardu souborů služby Batch](batch-task-output.md#implement-the-batch-file-conventions-standard).

## <a name="link-an-azure-storage-account-to-your-batch-account"></a>Propojit účet Azure Storage k účtu Batch

Pokud chcete zachovat výstupní data Azure Storage pomocí knihovny konvence souborů, musíte nejdřív propojit účet Azure Storage s účtem Batch. Pokud jste to ještě neudělali, propojte účet úložiště s účtem Batch pomocí [Azure Portal](https://portal.azure.com):

1. Na webu Azure Portal přejděte ke svému účtu Batch.
1. V části **Nastavení** vyberte **účet úložiště**.
1. Pokud ještě nemáte účet úložiště přidružený k účtu Batch, klikněte na **účet úložiště (žádný)**.
1. Vyberte účet úložiště ze seznamu pro vaše předplatné. Nejlepšího výkonu dosáhnete, když použijete účet Azure Storage, který je ve stejné oblasti jako účet Batch, ve kterém jsou vaše úlohy spuštěné.

## <a name="persist-output-data"></a>Zachovat výstupní data

Pokud chcete zachovat výstupní data úlohy a úlohy pomocí knihovny konvence souborů, vytvořte kontejner v Azure Storage a pak výstup uložte do kontejneru. Pomocí [klientské knihovny Azure Storage pro .NET](https://www.nuget.org/packages/WindowsAzure.Storage) v kódu úkolu nahrajte výstup úlohy do kontejneru.

Další informace o práci s kontejnery a objekty BLOB v Azure Storage najdete v tématu Začínáme [s úložištěm objektů BLOB v Azure pomocí .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md).

> [!WARNING]
> Všechny výstupy úlohy a úlohy trvale s knihovnou konvence souborů jsou uložené ve stejném kontejneru. Pokud se velký počet úloh pokusí uchovat soubory současně, můžou se vyhovět Azure Storage omezení omezování. Další informace o omezeních omezování najdete v tématu [Kontrolní seznam pro výkon a škálovatelnost pro úložiště objektů BLOB](../storage/blobs/storage-performance-checklist.md).

### <a name="create-storage-container"></a>Vytvoření kontejneru úložiště

Chcete-li zachovat výstup úlohy do Azure Storage, nejprve vytvořte kontejner voláním [vlastnosti cloudjob][net_cloudjob]. [PrepareOutputStorageAsync][net_prepareoutputasync]. Tato metoda rozšíření přebírá objekt [CloudStorageAccount][net_cloudstorageaccount] jako parametr. Vytvoří kontejner s názvem podle standardu souboru, aby jeho obsah byl zjistitelný Azure Portal a metody načítání popsané dále v článku.

Obvykle umístíte kód k vytvoření kontejneru v klientské aplikaci &mdash; aplikace, která vytváří vaše fondy, úlohy a úlohy.

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

### <a name="store-task-outputs"></a>Ukládání výstupů úloh

Teď, když jste připravili kontejner v Azure Storage, můžou úlohy ukládat výstup do kontejneru pomocí třídy [TaskOutputStorage][net_taskoutputstorage] , která se nachází v knihovně konvence souborů.

V kódu úlohy nejprve vytvořte objekt [TaskOutputStorage][net_taskoutputstorage] , poté, co úloha dokončí svou práci, zavolejte na [TaskOutputStorage][net_taskoutputstorage]. Metoda [SaveAsync][net_saveasync] , která uloží svůj výstup do Azure Storage.

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

`kind`Parametr [TaskOutputStorage](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage).[ Metoda SaveAsync](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync#overloads) zařadí trvalé soubory do kategorií. Existují čtyři předdefinované typy [TaskOutputKind][net_taskoutputkind] : `TaskOutput` , `TaskPreview` , `TaskLog` a `TaskIntermediate.` můžete také definovat vlastní kategorie výstupu.

Tyto typy výstupu umožňují určit, který typ výstupů se má zobrazit, když později budete dotazovat Batch na trvalé výstupy daného úkolu. Jinými slovy, při výpisu výstupů pro úlohu můžete filtrovat seznam na jednom z výstupních typů. Například "dát mi výstup *verze Preview* pro úlohu *109*." Další informace o výpisu a načítání výstupů se zobrazí v části načtení výstupu později v článku.

> [!TIP]
> Typ výstupu také určuje, kde se v Azure Portal zobrazí konkrétní *soubor: soubory* zařazené do kategorií se zobrazí v části **výstupní soubory úlohy** a v části **protokoly úloh** se zobrazí soubory *TaskLog* .

### <a name="store-job-outputs"></a>Ukládání výstupů úloh

Kromě ukládání výstupů úkolů můžete ukládat výstupy přidružené k celé úloze. Například v úloze sloučení úlohy vykreslování videa můžete zachovat plně vykreslený film jako výstup úlohy. Po dokončení úlohy může klientská aplikace vypsat a načíst výstupy pro úlohu a nepotřebuje dotazovat se na jednotlivé úlohy.

Uložte výstup úlohy voláním [JobOutputStorage][net_joboutputstorage]. [SaveAsync][net_joboutputstorage_saveasync] metoda a zadejte [JobOutputKind][net_joboutputkind] a filename:

```csharp
CloudJob job = new JobOutputStorage(acct, jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

Stejně jako u výstupů úloh s typem **TaskOutputKind** můžete použít typ [JobOutputKind][net_joboutputkind] k kategorizaci trvalých souborů úlohy. Tento parametr umožňuje dotazování pro (seznam) konkrétního typu výstupu. Typ **JobOutputKind** zahrnuje kategorie Output i Preview a podporuje vytváření vlastních kategorií.

### <a name="store-task-logs"></a>Ukládat protokoly úloh

Kromě uchování souboru do odolného úložiště po dokončení úlohy nebo úlohy může být nutné zachovat soubory, které byly aktualizovány během provádění &mdash; souborů protokolu úloh, nebo `stdout.txt` `stderr.txt` například. Pro účely tohoto účelu poskytuje knihovna pro Azure Batch souborů konvence [TaskOutputStorage][net_taskoutputstorage]. Metoda [SaveTrackedAsync][net_savetrackedasync] Pomocí [SaveTrackedAsync][net_savetrackedasync]můžete sledovat aktualizace souboru na uzlu (v intervalu, který zadáte) a zachovat tyto aktualizace Azure Storage.

V následujícím fragmentu kódu používáme [SaveTrackedAsync][net_savetrackedasync] k aktualizaci `stdout.txt` v Azure Storage každých 15 sekund během provádění úlohy:

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

Oddíl s komentářem `Code to process data and produce output file(s)` je zástupný symbol pro kód, který by úkol normálně prováděl. Například můžete mít kód, který stáhne data z Azure Storage a provede transformaci nebo výpočet. Důležitou součástí tohoto fragmentu kódu je demonstrace, jak můžete tento kód v bloku zabalit `using` , aby se soubor pravidelně aktualizoval pomocí [SaveTrackedAsync][net_savetrackedasync].

Agent uzlu je program, který běží na všech uzlech ve fondu a poskytuje rozhraní příkazového a řídicího prostředí mezi uzlem a službou Batch. `Task.Delay`Volání je vyžadováno na konci tohoto `using` bloku, aby bylo zajištěno, že agent Node má čas vyprázdnit obsah standardu na stdout.txt souboru na uzlu. Bez této prodlevy je možné vymezit několik posledních sekund výstupu. Toto zpoždění nemusí být vyžadováno pro všechny soubory.

> [!NOTE]
> Když povolíte sledování souborů pomocí **SaveTrackedAsync**, budou trvale *připojeny* jenom ke sledovanému souboru, aby se Azure Storage. Tuto metodu použijte pouze pro sledování nerotujících souborů protokolu nebo jiných souborů, které jsou zapsány do nástroje pomocí operací Append na konec souboru.

## <a name="retrieve-output-data"></a>Načtení výstupních dat

Po načtení trvalého výstupu pomocí knihovny Azure Batchch konvencí souborů můžete tak učinit způsobem orientovaným na úlohy a úlohy. Můžete požadovat výstup pro daný úkol nebo úlohu, aniž byste potřebovali znát jeho cestu v Azure Storage, nebo dokonce i jeho název souboru. Místo toho můžete požadovat výstupní soubory podle ID úkolu nebo úlohy.

Následující fragment kódu projde úkoly úlohy, vytiskne některé informace o výstupních souborech pro daný úkol a pak stáhne jeho soubory z úložiště.

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

## <a name="view-output-files-in-the-azure-portal"></a>Zobrazit výstupní soubory v Azure Portal

Azure Portal zobrazí výstupní soubory úlohy a protokoly, které jsou trvalé na propojený Azure Storage účet pomocí [standardu pro dávkové soubory](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files). Tyto konvence můžete implementovat sami v jazyce, podle vašeho výběru, nebo můžete použít knihovnu pro konvence souborů v aplikacích .NET.

Chcete-li povolit zobrazení výstupních souborů na portálu, je nutné splnit následující požadavky:

1. Propojte účet Azure Storage k účtu Batch.
1. Dodržovat předdefinované zásady vytváření názvů kontejnerů a souborů pro úložiště při zachování výstupů. Definici těchto konvencí najdete v souboru [Readme][github_file_conventions_readme]knihovny konvence souborů. Použijete-li knihovnu [Azure Batchch přípon souborů][nuget_package] k uchování vašeho výstupu, budou soubory uchovány podle standardu souborů.

Pokud chcete zobrazit výstupní soubory úlohy a protokoly v Azure Portal, přejděte na úlohu, jejíž výstup vás zajímá, a potom klikněte buď na **uložené výstupní soubory** , nebo na **uložené protokoly**. Tento obrázek ukazuje **uložené výstupní soubory** pro úlohu s ID "007":

![Okno výstupy úkolů v Azure Portal][2]

## <a name="code-sample"></a>Ukázka kódu

Vzorový projekt [PersistOutputs][github_persistoutputs] je jednou z [Azure Batch ukázek kódu][github_samples] na GitHubu. Toto řešení sady Visual Studio ukazuje, jak použít knihovnu Azure Batchch konvencí souborů k trvalému uložení výstupu úlohy do trvalého úložiště. Chcete-li spustit ukázku, postupujte podle následujících kroků:

1. Otevřete projekt v **aplikaci Visual Studio 2019**.
2. Přidejte **přihlašovací údaje** služby Batch a účtu úložiště do **AccountSettings. settings** v projektu Microsoft.Azure.Batch. Samples. Common.
3. **Sestavte** (ale nespouštějte) řešení. Pokud se zobrazí výzva, obnovte případné balíčky NuGet.
4. Pomocí Azure Portal nahrajte [balíček aplikace](batch-application-packages.md) pro **PersistOutputsTask**. Zahrňte `PersistOutputsTask.exe` a jeho závislá sestavení v balíčku. zip, nastavte ID aplikace na "PersistOutputsTask" a verzi balíčku aplikace na "1,0".
5. **Spusťte** (spusťte) projekt **PersistOutputs** .
6. Po zobrazení výzvy k výběru technologie trvalosti, která se má použít ke spuštění ukázky, zadejte **1** pro spuštění ukázky pomocí knihovny konvence souborů pro zachování výstupu úlohy. 

## <a name="next-steps"></a>Další kroky

### <a name="get-the-batch-file-conventions-library-for-net"></a>Získání knihovny pro konvence souborů Batch pro .NET

Knihovna konvence souborů služby Batch pro .NET je dostupná na [NuGet][nuget_package]. Knihovna rozšiřuje třídy [vlastnosti cloudjob][net_cloudjob] a [CloudTask][net_cloudtask] novými metodami. Podívejte se také na [referenční dokumentaci](/dotnet/api/microsoft.azure.batch.conventions.files) knihovny pro konvence souborů.

[Zdrojový kód][github_file_conventions] knihovny pro konvence souborů je k dispozici na GitHubu v úložišti Microsoft Azure SDK pro .NET. 

### <a name="explore-other-approaches-for-persisting-output-data"></a>Prozkoumat další přístupy k uchování výstupních dat

- Přehled trvalého zpracování dat úloh a úloh najdete v tématu [trvalé zpracování úloh a úloh na Azure Storage](batch-task-output.md) .
- Informace o tom, jak používat rozhraní API služby Batch k uchovávání výstupních dat, najdete v tématu [zachování dat úkolu pro Azure Storage s rozhraním API služby Batch](batch-task-output-files.md) .

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: /dotnet/api/microsoft.azure.batch.batchclient
[net_cloudjob]: /dotnet/api/microsoft.azure.batch.cloudjob
[net_cloudstorageaccount]: /java/api/com.microsoft.azure.storage.cloudstorageaccount
[net_cloudtask]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files/README.md
[net_joboutputkind]: /dotnet/api/microsoft.azure.batch.conventions.files.joboutputkind
[net_joboutputstorage]: /dotnet/api/microsoft.azure.batch.conventions.files.joboutputstorage
[net_joboutputstorage_saveasync]: /dotnet/api/microsoft.azure.batch.conventions.files.joboutputstorage.saveasync
[net_msdn]: /dotnet/api/microsoft.azure.batch
[net_prepareoutputasync]: /dotnet/api/microsoft.azure.batch.conventions.files.cloudjobextensions.prepareoutputstorageasync
[net_saveasync]: /dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync
[net_savetrackedasync]: /dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage.savetrackedasync
[net_taskoutputkind]: /dotnet/api/microsoft.azure.batch.conventions.files.taskoutputkind
[net_taskoutputstorage]: /dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage
[nuget_manager]: https://docs.nuget.org/consume/installing-nuget
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: https://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "Uložené výstupní soubory a selektory uložených protokolů na portálu"
[2]: ./media/batch-task-output/task-output-02.png "Okno výstupy úkolů v Azure Portal"
