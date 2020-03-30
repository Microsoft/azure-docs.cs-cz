---
title: Zachování výstupních dat ve službě Azure Storage pomocí knihovny Konvencí souborů .NET – Azure Batch
description: Zjistěte, jak používat knihovnu Azure Batch File Conventions pro rozhraní .NET k uchování dávkové úlohy & výstupu úlohy do Úložiště Azure a zobrazit tento výstup na webu Azure Portal.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/14/2018
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 693017e529f2869c16d94c30cdf48ec228df3276
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022864"
---
# <a name="persist-job-and-task-data-to-azure-storage-with-the-batch-file-conventions-library-for-net"></a>Zachování dat úloh a úloh ve službě Azure S knihovnou Konvencí dávkových souborů pro rozhraní .NET

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Jedním ze způsobů, jak zachovat data úloh, je použití [knihovny Azure Batch FileConventions pro rozhraní .NET][nuget_package]. Knihovna File Conventions zjednodušuje proces ukládání výstupních dat úloh do úložiště Azure a jejich načítání. Knihovnu File Conventions můžete použít v &mdash; kódu úlohy i klienta v kódu úlohy pro uchování souborů a v kódu klienta je můžete vypsat a načíst. Kód úkolu můžete také použít knihovnu k načtení výstupu upstream úkoly, například ve [scénáři závislosti na úkolu.](batch-task-dependencies.md)

Chcete-li načíst výstupní soubory pomocí knihovny Konvencí souborů, můžete soubory pro danou úlohu nebo úkol vyhledat tak, že je uvedete podle ID a účelu. Nemusíte znát názvy nebo umístění souborů. Knihovnu Konvencí souborů můžete například použít k zobrazení seznamu všech zprostředkujících souborů pro daný úkol nebo k získání souboru náhledu pro danou úlohu.

> [!TIP]
> Počínaje verzí 2017-05-01 podporuje rozhraní API služby Batch trvalé výstupní data do Úložiště Azure pro úlohy a úlohy správce úloh, které běží ve fondech vytvořených pomocí konfigurace virtuálního počítače. Rozhraní API dávkové služby poskytuje jednoduchý způsob, jak zachovat výstup z kódu, který vytvoří úlohu a slouží jako alternativa ke knihovně File Conventions. Aplikace batch klienta můžete upravit tak, aby uchovávaly výstup, aniž byste museli aktualizovat aplikaci, kterou je spuštěna úloha. Další informace najdete [v tématu Zachování dat úloh ve službě Azure Storage pomocí rozhraní API služby Batch](batch-task-output-files.md).

## <a name="when-do-i-use-the-file-conventions-library-to-persist-task-output"></a>Kdy mám k zachování výstupu úloh použít knihovnu File Conventions?

Azure Batch poskytuje více než jeden způsob, jak zachovat výstup úlohy. Konvence souborů se nejlépe hodí pro tyto scénáře:

- Můžete snadno upravit kód aplikace, která je spuštěna, aby uchovávaly soubory pomocí knihovny File Conventions.
- Chcete streamovat data do Azure Storage, když je úloha pořád spuštěná.
- Chcete zachovat data z fondů vytvořených pomocí konfigurace cloudové služby nebo konfigurace virtuálního počítače.
- Klientská aplikace nebo jiné úkoly v úloze musí vyhledat a stáhnout výstupní soubory úloh podle ID nebo podle účelu.
- Chcete zobrazit výstup úlohy na webu Azure Portal.

Pokud se váš scénář liší od výše uvedených, budete muset zvážit jiný přístup. Další informace o dalších možnostech pro trvalý výstup úlohy najdete [v tématu Persist job and task output to Azure Storage](batch-task-output.md).

## <a name="what-is-the-batch-file-conventions-standard"></a>Co je standard konvence dávkových souborů?

[Standard Konvence dávkových souborů](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files) poskytuje schéma pojmenování pro cílové kontejnery a cesty objektů blob, do kterých jsou zapsány výstupní soubory. Soubory trvalé do úložiště Azure, které dodržují standard Konvence souborů jsou automaticky k dispozici pro zobrazení na webu Azure Portal. Portál si je vědoma konvence pojmenování a proto může zobrazit soubory, které ji dodržují.

Knihovna File Conventions for .NET automaticky pojmenovává kontejnery úložiště a výstupní soubory úloh podle standardu File Conventions. Knihovna File Conventions také poskytuje metody pro dotazování výstupních souborů ve službě Azure Storage podle ID úlohy, ID úlohy nebo účelu.

Pokud vyvíjíte s jiným jazykem než .NET, můžete implementovat soubor konvencí standard sami ve vaší aplikaci. Další informace naleznete [v tématu Implementovat standard Konvence dávkových souborů](batch-task-output.md#implement-the-batch-file-conventions-standard).

## <a name="link-an-azure-storage-account-to-your-batch-account"></a>Propojení účtu Azure Storage s dávkovým účtem

Chcete-li zachovat výstupní data do úložiště Azure pomocí knihovny File Conventions, musíte nejprve propojit účet Úložiště Azure s vaším dávkovým účtem. Pokud jste tak ještě neučinili, propojte účet úložiště se svým dávkovým účtem pomocí [portálu Azure](https://portal.azure.com):

1. Na webu Azure Portal přejděte ke svému účtu Batch.
1. V části **Nastavení**vyberte **Účet úložiště**.
1. Pokud ještě nemáte účet úložiště přidružený k účtu Batch, klikněte na **Účet úložiště (Žádný).**
1. Vyberte účet úložiště ze seznamu pro vaše předplatné. Pro dosažení nejlepšího výkonu použijte účet Azure Storage, který je ve stejné oblasti jako účet Batch, kde jsou spuštěny vaše úlohy.

## <a name="persist-output-data"></a>Zachovat výstupní data

Chcete-li zachovat data úloh a výstupu úloh v knihovně File Conventions, vytvořte kontejner ve službě Azure Storage a uložte výstup do kontejneru. K nahrání výstupu úlohy do kontejneru použijte [klientskou knihovnu Azure Storage pro rozhraní .NET](https://www.nuget.org/packages/WindowsAzure.Storage) v kódu úlohy.

Další informace o práci s kontejnery a objekty BLOB ve službě Azure Storage najdete v [tématu Začínáme s úložištěm objektů blob Azure pomocí rozhraní .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

> [!WARNING]
> Všechny výstupy úloh a úloh trvalé s knihovnou Konvence souborů jsou uloženy ve stejném kontejneru. Pokud velký počet úloh se pokusí uchovat soubory ve stejnou dobu, může být vynuceno omezení úložiště Azure. Další informace o omezení omezení, najdete v [tématu výkon a škálovatelnost kontrolní seznam pro úložiště objektů blob](../storage/blobs/storage-performance-checklist.md).

### <a name="create-storage-container"></a>Vytvoření kontejneru úložiště

Chcete-li zachovat výstup úlohy do služby Azure Storage, nejprve vytvořte kontejner voláním [CloudJob][net_cloudjob]. [PrepareOutputStorageAsync][net_prepareoutputasync]. Tato metoda rozšíření přebírá objekt [CloudStorageAccount][net_cloudstorageaccount] jako parametr. Vytvoří kontejner pojmenovaný podle standardu File Conventions, takže jeho obsah je zjistitelný portálem Azure a metodami načítání popsanými dále v článku.

Kód obvykle umístíte k vytvoření kontejneru &mdash; v klientské aplikaci, která vytvoří vaše fondy, úlohy a úkoly.

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

Teď, když jste připravili kontejner ve službě Azure Storage, úlohy můžete uložit výstup do kontejneru pomocí [TaskOutputStorage][net_taskoutputstorage] třídy nalezené v knihovně File Conventions.

V kódu úlohy nejprve vytvořte objekt [TaskOutputStorage][net_taskoutputstorage] a poté, co úloha dokončí svou práci, zavolejte [taskoutputstorage][net_taskoutputstorage]. [SaveAsync][net_saveasync] metoda uložit jeho výstup do Úložiště Azure.

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

Parametr `kind` [TaskOutputStorage](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage). [SaveAsync](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync#overloads) metoda kategorizuje trvalé soubory. Existují čtyři předdefinované [TaskOutputKind][net_taskoutputkind] `TaskOutput` `TaskPreview`typy: , , `TaskLog`a `TaskIntermediate.` Můžete také definovat vlastní kategorie výstupu.

Tyto typy výstupů umožňují určit, jaký typ výstupů chcete vypsat při pozdějším dotazu Batch pro trvalé výstupy daného úkolu. Jinými slovy, když zobrazíte seznam výstupů pro úkol, můžete filtrovat seznam na jednom z typů výstupů. Například "Dejte mi výstup *náhledu* pro úkol *109*." Další informace o výpisu a načítání výstupů se zobrazí v načíst výstup dále v článku.

> [!TIP]
> Typ výstupu také určuje, kde se na portálu Azure zobrazí určitý soubor: TaskOutput-categoribované soubory se zobrazí v části **Výstupní soubory úloh**a *TaskLog* soubory se zobrazí v **protokolech úloh**. *TaskOutput*

### <a name="store-job-outputs"></a>Výstupy úloh úložiště

Kromě ukládání výstupů úloh můžete uložit výstupy přidružené k celé úloze. Například při sloučení úlohy vykreslování filmu můžete zachovat plně vykreslený film jako výstup úlohy. Po dokončení úlohy může klientská aplikace vypsat a načíst výstupy pro úlohu a nemusí se dotazovat na jednotlivé úkoly.

Uložit výstup úlohy voláním [JobOutputStorage][net_joboutputstorage]. [SaveAsync][net_joboutputstorage_saveasync] a zadejte [JobOutputKind][net_joboutputkind] a název souboru:

```csharp
CloudJob job = new JobOutputStorage(acct, jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

Stejně jako u **taskoutputkind** typ pro výstupy úkolů, použijte [JobOutputKind][net_joboutputkind] typ kategorizovat úlohy trvalé soubory. Tento parametr umožňuje pozdější dotaz na (seznam) konkrétní typ výstupu. Typ **JobOutputKind** zahrnuje výstupní i náhledové kategorie a podporuje vytváření vlastních kategorií.

### <a name="store-task-logs"></a>Ukládání protokolů úloh

Kromě uchování souboru do trvalého úložiště po dokončení úlohy nebo úlohy může být nutné &mdash; zachovat soubory, které jsou aktualizovány během provádění souborů protokolu úloh nebo `stdout.txt` například. `stderr.txt` Za tímto účelem knihovna Azure Batch File Conventions poskytuje [TaskOutputStorage][net_taskoutputstorage]. [SaveTrackedAsync][net_savetrackedasync] metoda. S [SaveTrackedAsync][net_savetrackedasync]můžete sledovat aktualizace souboru v uzlu (v intervalu, který zadáte) a zachovat tyto aktualizace azure storage.

V následujícím fragmentu kódu používáme [SaveTrackedAsync][net_savetrackedasync] `stdout.txt` k aktualizaci ve službě Azure Každých 15 sekund během provádění úlohy:

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

Komentářová část `Code to process data and produce output file(s)` je zástupný symbol kódu, který by váš úkol normálně provedl. Můžete mít například kód, který stahuje data z Azure Storage a provádí transformace nebo výpočtu na něm. Důležitou součástí tohoto fragmentu je demonstrovat, jak můžete `using` zabalit takový kód v bloku pravidelně aktualizovat soubor s [SaveTrackedAsync][net_savetrackedasync].

Agent uzlu je program, který běží na každém uzlu ve fondu a poskytuje rozhraní příkazu a řízení mezi uzlem a službou Batch. Volání `Task.Delay` je nutné na konci `using` tohoto bloku zajistit, že agent uzlu má čas vyprázdnění obsahu standard u souboru stdout.txt v uzlu. Bez tohoto zpoždění je možné vynechat posledních několik sekund výstupu. Toto zpoždění nemusí být vyžadováno pro všechny soubory.

> [!NOTE]
> Když povolíte sledování souborů pomocí **SaveTrackedAsync**, zůstanou do Služby Azure Storage *zachovány* jenom připojení k sledovanému souboru. Tuto metodu použijte pouze pro sledování nerotujících souborů protokolu nebo jiných souborů, které jsou zapsány pomocí operací připojení na konec souboru.

## <a name="retrieve-output-data"></a>Načtení výstupních dat

Když načtete trvalý výstup pomocí knihovny Azure Batch File Conventions, uděláte to způsobem zaměřeným na úlohy a úlohy. Můžete požádat o výstup pro danou úlohu nebo úlohu, aniž byste museli znát jeho cestu ve službě Azure Storage nebo dokonce název souboru. Místo toho můžete požádat o výstupní soubory podle ID úlohy nebo úlohy.

Následující fragment kódu iterates prostřednictvím úlohy úlohy, vytiskne některé informace o výstupnísoubory pro úlohu a potom stáhne soubory z úložiště.

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

## <a name="view-output-files-in-the-azure-portal"></a>Zobrazení výstupních souborů na webu Azure Portal

Portál Azure zobrazuje výstupní soubory úloh a protokoly, které jsou trvalé na propojeném účtu úložiště Azure pomocí [standardu Batch FileConventions](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions). Tyto konvence můžete implementovat sami v jazyce podle vašeho výběru nebo můžete použít knihovnu File Conventions ve vašich aplikacích .NET.

Chcete-li povolit zobrazení výstupních souborů na portálu, musíte splňovat následující požadavky:

1. Propojte účet Azure Storage s dávkovým účtem.
1. Při trvalém výstupu dodržujte předdefinované konvence pojmenování pro kontejnery úložiště a soubory. Definici těchto konvencí najdete v knihovně File Conventions [README][github_file_conventions_readme]. Pokud použijete knihovnu [Azure Batch FileConventions][nuget_package] k zachování výstupu, vaše soubory se udály podle standardu File Conventions.

Chcete-li zobrazit výstupní soubory úloh a protokoly na webu Azure Portal, přejděte na úlohu, jejíž výstup vás zajímá, a potom klikněte na **Uložené výstupní soubory** nebo Uložené **protokoly**. Tento obrázek **znázorňuje uložené výstupní soubory** pro úlohu s ID "007":

![Okno Výstupy úloh na webu Azure Portal][2]

## <a name="code-sample"></a>Ukázka kódu

Ukázkový projekt [PersistOutputs][github_persistoutputs] je jedním z [ukázek kódu Azure Batch][github_samples] na GitHubu. Toto řešení sady Visual Studio ukazuje, jak používat knihovnu Azure Batch FileConventions k zachování výstupu úloh y do trvalého úložiště. Chcete-li spustit ukázku, postupujte takto:

1. Otevřete projekt v **Sadě Visual Studio 2019**.
2. Přidejte přihlašovací **údaje účtu** batch a úložiště do **účtuSettings.settings** v projektu Microsoft.Azure.Batch.Samples.Common.
3. **Sestavení** (ale nespouštějte) řešení. Pokud se zobrazí výzva, obnovte všechny balíčky NuGet.
4. Na portálu Azure nahrajte [balíček aplikace](batch-application-packages.md) **persistoutputstask**. Zahrnout `PersistOutputsTask.exe` a jeho závislá sestavení v balíčku .zip, nastavte ID aplikace na "PersistOutputsTask" a verze balíčku aplikace na "1.0".
5. **Spustit** (spustit) **persistoutputs** projektu.
6. Po zobrazení výzvy k výběru technologie trvalosti, která se má použít pro spuštění ukázky, zadejte **hodnotu 1** a spusťte ukázku pomocí knihovny Konvencí souborů, abyste událi výstup úlohy. 

## <a name="next-steps"></a>Další kroky

### <a name="get-the-batch-file-conventions-library-for-net"></a>Získání knihovny Konvencí dávkových souborů pro rozhraní .NET

Knihovna dávkových konvencí souborů pro rozhraní .NET je k dispozici v [nugetu][nuget_package]. Knihovna rozšiřuje třídy [CloudJob][net_cloudjob] a [CloudTask][net_cloudtask] o nové metody. Viz také [referenční dokumentaci](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files) pro knihovnu File Conventions.

[Zdrojový kód][github_file_conventions] pro knihovnu File Conventions je k dispozici na GitHubu v microsoft azure sdk pro úložiště .NET. 

### <a name="explore-other-approaches-for-persisting-output-data"></a>Prozkoumejte další přístupy k uchování výstupních dat

- Přehled trvalých dat úloh [a úloh v článku Persist job and task output to Azure Storage.](batch-task-output.md)
- Informace o tom, jak pomocí rozhraní API služby Batch používat rozhraní API služby Batch k uchování výstupních dat, najdete v [tématu Persist task data to Azure Storage with the Batch service API](batch-task-output-files.md) the Learn how to use the Batch service API to persist output data.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudstorageaccount]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage._cloud_storage_account
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files/README.md
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
[storage_explorer]: https://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "Uložené výstupní soubory a voliči uložených protokolů na portálu"
[2]: ./media/batch-task-output/task-output-02.png "Okno Výstupy úloh na webu Azure Portal"
