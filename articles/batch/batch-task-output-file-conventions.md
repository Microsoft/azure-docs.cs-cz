---
title: Zachovat úloh a úkolů výstup do služby Azure Storage s knihovnou souboru konvence pro platformu .NET – Azure Batch | Microsoft Docs
description: Další informace o použití knihovny konvence souboru Azure Batch pro .NET pro zachování výstup úlohy a úlohy Batch do služby Azure Storage a zobrazení trvalou výstup na portálu Azure.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 06/16/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bbfb40b3740f9ea43df327a01ba6f4cf52d80457
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2018
ms.locfileid: "30314142"
---
# <a name="persist-job-and-task-data-to-azure-storage-with-the-batch-file-conventions-library-for-net-to-persist"></a>Zachovat úloh a úkolů data do služby Azure Storage pomocí knihovny Batch souboru konvence pro .NET k uchování 

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Je možné zachovat data úloh používat [konvence souboru Azure Batch library pro .NET][nuget_package]. Soubor konvence knihovny zjednodušuje proces ukládání úkolů výstupní data do služby Azure Storage a jejich načtení. Můžete použít knihovnu názvů souboru v kódu úlohy a klienta &mdash; v kódu úlohy pro zachování soubory a kód klienta a seznamu je znovu načíst. Kód úloh můžete také použít knihovnu načíst výstup nadřazeného úlohy, jako třeba v [závislosti úkolů](batch-task-dependencies.md) scénář. 

Načíst výstupní soubory s knihovnou názvů souborů, můžete vyhledat soubory pro daný úlohy nebo úkolu je vypsáním podle ID a účel. Nemusíte znát názvy nebo umístění souborů. Můžete například použít soubor konvence knihovny k zobrazení seznamu všech zprostředkující soubory pro danou úlohu nebo získat soubor preview pro danou úlohu.

> [!TIP]
> Počínaje verzí 2017-05-01, rozhraní API služby Batch podporuje zachování výstupní data do úložiště Azure pro úlohy a úkolech Správce úloh, které běží na fondy, které jsou vytvořené pomocí konfigurace virtuálního počítače. Rozhraní API služby Batch poskytuje jednoduchý způsob, jak zachovat výstup z kódu, který vytvoří úlohu a slouží jako alternativu ke knihovně názvů souborů. Můžete upravit klienta aplikace Batch udržení výstup aniž by museli aktualizovat aplikaci spuštěnou úlohu. Další informace najdete v tématu [zachovat data úloh do služby Azure Storage službou Batch služby API](batch-task-output-files.md).
> 
> 

## <a name="when-do-i-use-the-file-conventions-library-to-persist-task-output"></a>Kdy použít soubor konvence knihovny se zachovat výstup úlohy?

Azure Batch poskytuje více než jeden způsob, jak zachovat výstup úlohy. Konvence souboru je nejvhodnější pro tyto scénáře:

- Můžete snadno upravit kód pro aplikaci, která vaše úloha pracuje se zachovat soubory pomocí souboru konvence knihovny.
- Chcete data datového proudu do služby Azure Storage při běhu úlohy.
- Chcete zachovat data z fondů vytvořené pomocí konfigurace cloudové služby nebo konfigurace virtuálního počítače.
- Klientské aplikace nebo jiné úkoly v úloze musí vyhledat a stáhnout úloh výstupní soubory podle ID nebo podle účelu. 
- Chcete zobrazit výstup úlohy na portálu Azure.

Pokud váš scénář se liší od výše uvedené, musíte vzít v úvahu jiný přístup. Další informace o dalších možnostech pro zachování výstup úlohy najdete v tématu [zachovat výstup úlohy a úkolů do služby Azure Storage](batch-task-output.md). 

## <a name="what-is-the-batch-file-conventions-standard"></a>Co je soubor konvence Batch, které jsou standardní?

[Dávkového souboru konvence standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) poskytuje schéma pojmenování s pro cílový kontejnerů a objektů blob cesty, na které jsou zapsány výstupní soubory. Trvalé soubory do úložiště Azure, které dodržovat konvence souboru standard jsou automaticky dostupné pro zobrazení na portálu Azure. Portál si je vědoma zásady vytváření názvů a proto můžete zobrazit soubory, které ho splňovat.

Soubor konvence knihovna pro .NET automaticky názvy kontejnerů úložiště a úloh výstupní soubory podle standardní soubor konvence. Soubor konvence knihovny také poskytuje metody pro dotaz výstupní soubory ve službě Azure Storage podle ID úlohy, ID úlohy nebo účel.   

Pokud vyvíjíte s jiném jazyce než v rozhraní .NET, můžete implementovat standard souboru konvence sami ve vaší aplikaci. Další informace najdete v tématu [o služba Batch souboru konvence standard](batch-task-output.md#about-the-batch-file-conventions-standard).

## <a name="link-an-azure-storage-account-to-your-batch-account"></a>Propojení účtu Azure Storage k účtu Batch

Udržení výstupní data do služby Azure Storage pomocí souboru konvence knihovny, je nutné nejprve propojit účtu Azure Storage k účtu Batch. Pokud jste tak ještě neučinili, propojení účtu úložiště k účtu Batch pomocí [portál Azure](https://portal.azure.com):

1. Na webu Azure Portal přejděte ke svému účtu Batch. 
2. V části **nastavení**, vyberte **účet úložiště**.
3. Pokud již nemáte účet úložiště spojené s vaším účtem Batch, klikněte na tlačítko **účet úložiště (None)**.
4. Vyberte účet úložiště, ze seznamu pro vaše předplatné. Pro nejlepší výkon použijte účet Azure Storage, který je ve stejné oblasti jako účet Batch, kde běží vaše úkoly.

## <a name="persist-output-data"></a>Zachovat výstupní data

Zachovat výstupních dat úloh a úkolů s knihovnou konvence soubor, vytvořit kontejner ve službě Azure Storage a potom uložte si výstup do kontejneru. Použití [Klientská knihovna pro úložiště Azure pro .NET](https://www.nuget.org/packages/WindowsAzure.Storage) ve vašem kódu úloh odesílat výstup úlohy do kontejneru. 

Další informace o práci s kontejnery a objekty BLOB ve službě Azure Storage najdete v tématu [Začínáme s Azure Blob storage pomocí rozhraní .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

> [!WARNING]
> Všechny výstupy úlohy a úkolů jako trvalý, s konvence soubor knihovny jsou uložené ve stejném kontejneru. Pokud velkého počtu úkolů zkuste zachovat soubory ve stejnou dobu, [úložiště omezení](../storage/common/storage-performance-checklist.md#blobs) může být požadováno.
> 
> 

### <a name="create-storage-container"></a>Vytvoření kontejneru úložiště

Udržení výstup úlohy do služby Azure Storage, nejprve vytvořte kontejner voláním [CloudJob][net_cloudjob].[ PrepareOutputStorageAsync][net_prepareoutputasync]. Tato metoda rozšíření přijímá [CloudStorageAccount] [ net_cloudstorageaccount] objekt jako parametr. Vytvoří kontejner s názvem podle konvence souboru standard, tak, aby jeho obsah je zjistitelný na portálu Azure a načtení metody popsané v článku později.

Kód k vytvoření kontejneru v klientské aplikace obvykle umístíte &mdash; aplikaci, která vytváří vaše fondy, úlohy a úkoly.

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

### <a name="store-task-outputs"></a>Výstupy úložiště úlohy

Teď, když jste připravili kontejner ve službě Azure Storage, úlohy můžete uložit výstup do kontejneru [TaskOutputStorage] [ net_taskoutputstorage] nalezena třída v souboru konvence knihovny.

Ve vašem kódu úloha nejprve vytvořit [TaskOutputStorage] [ net_taskoutputstorage] objekt a potom po dokončení práce úlohy volání [TaskOutputStorage][net_taskoutputstorage].[ SaveAsync] [ net_saveasync] metody uložte svůj výstup do služby Azure Storage.

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

`kind` Parametr [TaskOutputStorage](https://msdn.microsoft.com/library/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx).[ SaveAsync](https://msdn.microsoft.com/library/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx) metoda rozděluje trvalé soubory. Existují čtyři předdefinované [TaskOutputKind] [ net_taskoutputkind] typy: `TaskOutput`, `TaskPreview`, `TaskLog`, a `TaskIntermediate.` je možné definovat také vlastní kategorie výstupu.

Tyto typy výstup umožňují určit typ výstupy do seznamu, když dotazujete později Batch pro trvalou výstupy danou úlohu. Jinými slovy Pokud uvedete výstupy pro úlohu, můžete filtrovat v seznamu na jeden z typů výstup. Například "mě *preview* výstup pro úlohu *109*." Další informace o výpis a načítání výstupy se zobrazí v [načíst výstup](#retrieve-output) dále v článku.

> [!TIP]
> Typ výstupu také určuje, kde na portálu Azure určitého souboru se zobrazí: *TaskOutput*-seřazený podle kategorií soubory se zobrazí v části **úloh výstupní soubory**, a *TaskLog* soubory se zobrazí v části **úkolů protokoly**.
> 
> 

### <a name="store-job-outputs"></a>Výstupy úlohy úložiště

Kromě ukládání výstupy úlohy, můžete uložit výstupy přidružené k celé úlohy. Například v úloze sloučení film vykreslování úlohy můžete zachovat plně vykreslené film jako výstup úlohy. Po dokončení úlohy můžete klientskou aplikaci seznamu a načíst výstupy úlohy a nemusí dotazovat jednotlivých úkolů.

Ukládání výstupu úlohy voláním [JobOutputStorage][net_joboutputstorage].[ SaveAsync] [ net_joboutputstorage_saveasync] metoda a zadejte [JobOutputKind] [ net_joboutputkind] a název souboru:

```csharp
CloudJob job = new JobOutputStorage(acct, jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

Stejně jako u **TaskOutputKind** typ pro výstupy úlohy, můžete použít [JobOutputKind] [ net_joboutputkind] typu zařadit do kategorií úlohy je trvalé soubory. Tento parametr umožňuje novější dotazu pro určitý typ výstupu (list). **JobOutputKind** typ obsahuje výstup a preview kategorie a podporuje vytváření vlastních kategorií.

### <a name="store-task-logs"></a>Úloha protokoly úložiště

Kromě uložením souboru do trvalého úložiště po dokončení úlohy nebo úkolu, budete muset zachovat soubory, které jsou aktualizovány při provádění úlohy &mdash; soubory protokolu nebo `stdout.txt` a `stderr.txt`, např. Pro tento účel, poskytuje knihovna konvence souboru Azure Batch [TaskOutputStorage][net_taskoutputstorage].[ SaveTrackedAsync] [ net_savetrackedasync] metoda. S [SaveTrackedAsync][net_savetrackedasync], můžete sledovat aktualizace do souboru na uzel (v intervalu, který zadáte) a zachovat tyto aktualizace do úložiště Azure.

V následující fragment kódu, použijeme [SaveTrackedAsync] [ net_savetrackedasync] aktualizace `stdout.txt` ve službě Azure Storage každých 15 sekund během provádění úlohy:

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

V komentáři části `Code to process data and produce output file(s)` je zástupný symbol pro kód, který by za normálních okolností provedl úlohu. Například můžete mít kód, který stahuje data ze služby Azure Storage a provede transformaci nebo výpočet jeho. Důležitou součástí tento fragment kódu je ukázka, jak může obtékat takový kód v `using` bloku pravidelně aktualizovat soubor s [SaveTrackedAsync][net_savetrackedasync].

Agent uzlu je program, který běží na každém uzlu ve fondu a poskytuje rozhraní příkazu a řízení mezi uzlu a služby Batch. `Task.Delay` Volání je na konci tohoto třeba `using` blok k zajistěte, aby agent uzlu čas vyprázdnění obsah standardní na více systémů do souboru stdout.txt na uzlu. Bez této zpoždění je možné neproběhly poslední několik sekund výstupu. Tato prodleva nemusí být požadovány pro všechny soubory.

> [!NOTE]
> Když povolíte sledování pomocí souboru **SaveTrackedAsync**, pouze *připojí* k souboru sledovaných zůstávají do služby Azure Storage. Tuto metodu lze použijte pouze pro sledování-rotaci souborů protokolu nebo jiné soubory, které se zapisují do s doplňovací operace na konec souboru.
> 
> 

## <a name="retrieve-output-data"></a>Načíst výstupní data

Můžete načíst trvalou výstupu pomocí Azure Batch souboru konvence knihovny, můžete udělat úlohy a úlohy-zaměřená na způsobem. Můžete požádat výstup pro danou úlohu nebo úlohu, aniž by museli znát jeho cestu do úložiště Azure, nebo i její název souboru. Místo toho můžete požádat výstupní soubory úlohy nebo úlohy ID.

Následující fragment kódu iteruje v rámci úlohy, vytiskne některé informace o výstupní soubory pro úlohu a potom stáhne jeho soubory z úložiště.

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

## <a name="view-output-files-in-the-azure-portal"></a>Výstupní soubory zobrazení na portálu Azure

Portál Azure zobrazí úloh výstupní soubory a protokoly, které jsou nastavené jako trvalé k propojené službě Azure Storage účet pomocí [dávkového souboru konvence standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). Můžete implementovat tyto konvence sami v jazyk své volby, nebo můžete použít knihovnu názvů souboru v aplikacích .NET.

Chcete-li zobrazení vaší výstupní soubory na portálu, musí splňovat následující požadavky:

1. [Propojení účtu Azure Storage](#requirement-linked-storage-account) k účtu Batch.
2. Předdefinované zásady vytváření názvů pro kontejnery úložiště a soubory splňovat při zachování výstupy. Definice těchto konvence můžete najít v souboru konvence knihovny [README][github_file_conventions_readme]. Pokud použijete [Azure Batch souboru konvence] [ nuget_package] knihovny se zachovat výstupu, vaše soubory jsou nastavené jako trvalé podle standardu názvů souborů.

Chcete-li zobrazit úlohy výstupní soubory a protokoly na portálu Azure, přejděte na úlohu, jejíž výstup vás zajímá, pak klikněte buď **uložená výstupní soubory** nebo **uložené protokoly**. Tento obrázek ukazuje **uložená výstupní soubory** pro úlohu s ID "007":

![Okno portálu Azure výstupy úlohy][2]

## <a name="code-sample"></a>Ukázka kódu

[PersistOutputs] [ github_persistoutputs] ukázkový projekt je jedním z [ukázky kódu Azure Batch] [ github_samples] na Githubu. Toto řešení sady Visual Studio ukazuje, jak pomocí Azure Batch souboru konvence knihovny lze zachovat výstup úlohy do odolné úložiště. Pokud chcete spustit ukázku, postupujte takto:

1. Otevřete projekt ve **Visual Studio 2015 nebo novější**.
2. Přidat Batch a Storage **přihlašovací údaje účtu** k **AccountSettings.settings** v Microsoft.Azure.Batch.Samples.Common projektu.
3. **Sestavení** (ale není spuštěný) řešení. Pokud se zobrazí výzva, obnovení všech balíčků NuGet.
4. Použít portál Azure k nahrání [balíčku aplikace](batch-application-packages.md) pro **PersistOutputsTask**. Zahrnout `PersistOutputsTask.exe` a jeho závislá sestavení v balíčku .zip, nastavte na "PersistOutputsTask" ID aplikace a verze balíčku aplikace do "1.0".
5. **Spustit** (spustit) **PersistOutputs** projektu.
6. Po zobrazení výzvy vyberte technologie trvalost použít ke spuštění ukázky, zadejte **1** spustit ukázku pomocí souboru konvence knihovny se zachovat výstup úlohy. 

## <a name="next-steps"></a>Další postup

### <a name="get-the-batch-file-conventions-library-for-net"></a>Získání knihovny Batch souboru konvence pro rozhraní .NET

Konvence souboru Batch library pro .NET je k dispozici na [NuGet][nuget_package]. Rozšiřuje knihovny [CloudJob] [ net_cloudjob] a [CloudTask] [ net_cloudtask] tříd pomocí nové metody. Viz také [referenční dokumentace](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files) souboru konvence knihovny.

[Zdrojový kód] [ github_file_conventions] pro soubor konvence knihovny k dispozici na Githubu v Microsoft Azure SDK for .NET úložiště. 

### <a name="explore-other-approaches-for-persisting-output-data"></a>Prozkoumejte jiné postupy zachování výstupních dat

- V tématu [zachovat výstup úlohy a úkolů do služby Azure Storage](batch-task-output.md) přehled zachování dat úlohy a úlohy.
- V tématu [zachovat data úloh do služby Azure Storage službou Batch služby API](batch-task-output-files.md) se dozvíte, jak používat rozhraní API služby Batch udržení výstupní data.

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

[1]: ./media/batch-task-output/task-output-01.png "Uložená výstupní soubory a uložené protokoly selektory portálu"
[2]: ./media/batch-task-output/task-output-02.png "Okno portálu Azure výstupy úlohy"
