---
title: Uchování výstupních dat do úložiště Azure s rozhraním API dávkové služby – Azure Batch
description: Zjistěte, jak pomocí rozhraní API služby Batch zachovat data dávkových úloh a výstupů úloh do Azure Storage.
services: batch
author: LauraBrenner
manager: evansma
editor: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 03/05/2019
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 11bd8bc427dd3da35ec5aa0f728f6b04b7d4527d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022847"
---
# <a name="persist-task-data-to-azure-storage-with-the-batch-service-api"></a>Zachování dat úloh ve službě Azure Storage pomocí rozhraní API dávkové služby

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Rozhraní API dávkové služby podporuje uchování výstupních dat do Úložiště Azure pro úlohy a úlohy správce úloh, které běží ve fondech s konfigurací virtuálního počítače. Když přidáte úkol, můžete zadat kontejner v Azure Storage jako cíl pro výstup úlohy. Služba Batch pak zapíše všechna výstupní data do tohoto kontejneru po dokončení úlohy.

Výhodou použití rozhraní API služby Batch k uchování výstupu úloh je, že není nutné upravovat aplikaci, která je spuštěna úlohu. Místo toho s několika úpravami klientské aplikace můžete zachovat výstup úlohy ze stejného kódu, který vytvoří úlohu.

## <a name="when-do-i-use-the-batch-service-api-to-persist-task-output"></a>Kdy mám použít rozhraní API služby Batch k uchování výstupu úloh?

Azure Batch poskytuje více než jeden způsob, jak zachovat výstup úlohy. Použití rozhraní API služby Batch je pohodlný přístup, který je nejvhodnější pro tyto scénáře:

- Chcete napsat kód pro zachování výstupu úlohy z klientské aplikace, aniž byste změnili aplikaci, kterou je spuštěna vaše úloha.
- Chcete zachovat výstup z dávkových úloh a úloh správce úloh ve fondech vytvořených pomocí konfigurace virtuálního počítače.
- Chcete zachovat výstup do kontejneru úložiště Azure s libovolným názvem.
- Chcete zachovat výstup do kontejneru úložiště Azure pojmenované podle [standardu dávkových souborů](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions). 

Pokud se váš scénář liší od výše uvedených, budete muset zvážit jiný přístup. Například rozhraní API služby batch aktuálně nepodporuje streamování výstupu do služby Azure Storage, když je úloha spuštěna. Chcete-li streamovat výstup, zvažte použití knihovny Dávkových konvencí souborů, která je k dispozici pro rozhraní .NET. Pro ostatní jazyky budete muset implementovat vlastní řešení. Další informace o dalších možnostech pro trvalý výstup úlohy najdete [v tématu Persist job and task output to Azure Storage](batch-task-output.md).

## <a name="create-a-container-in-azure-storage"></a>Vytvoření kontejneru ve službě Azure Storage

Chcete-li zachovat výstup úlohy do Služby Azure Storage, budete muset vytvořit kontejner, který slouží jako cíl pro vaše výstupní soubory. Vytvořte kontejner před spuštěním úlohy, nejlépe před odesláním úlohy. Chcete-li vytvořit kontejner, použijte příslušnou klientskou knihovnu Azure Storage nebo sadu SDK. Další informace o azure storage api najdete v [dokumentaci k úložišti Azure](https://docs.microsoft.com/azure/storage/).

Pokud například píšete aplikaci v c#, použijte [klientskou knihovnu Azure Storage pro rozhraní .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). Následující příklad ukazuje, jak vytvořit kontejner:

```csharp
CloudBlobContainer container = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
await container.CreateIfNotExists();
```

## <a name="get-a-shared-access-signature-for-the-container"></a>Získání sdíleného přístupového podpisu pro kontejner

Po vytvoření kontejneru získáte sdílený přístupový podpis (SAS) s přístupem pro zápis do kontejneru. SAS poskytuje delegovaný přístup ke kontejneru. SAS uděluje přístup se zadanou sadou oprávnění a přes zadaný časový interval. Služba Batch potřebuje SAS s oprávněními k zápisu do kontejneru. Další informace o SAS najdete [v \(tématu Používání sdílených přístupových podpisů SAS\) ve službě Azure Storage](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

Když získáte SAS pomocí rozhraní API úložiště Azure, rozhraní API vrátí řetězec tokenu SAS. Tento řetězec tokenu obsahuje všechny parametry SAS, včetně oprávnění a interval, přes který je platný SAS. Chcete-li použít SAS pro přístup ke kontejneru ve službě Azure Storage, musíte připojit řetězec tokenu SAS k uri prostředku. Identifikátor URI prostředku spolu s připojeným tokenem SAS poskytuje ověřený přístup k Úložišti Azure.

Následující příklad ukazuje, jak získat řetězec tokenu SAS pouze pro zápis pro kontejner, pak připojí SAS k uri kontejneru:

```csharp
string containerSasToken = container.GetSharedAccessSignature(new SharedAccessBlobPolicy()
{
    SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddDays(1),
    Permissions = SharedAccessBlobPermissions.Write
});

string containerSasUrl = container.Uri.AbsoluteUri + containerSasToken;
```

## <a name="specify-output-files-for-task-output"></a>Určení výstupních souborů pro výstup úlohy

Chcete-li určit výstupní soubory pro úlohu, vytvořte kolekci objektů [OutputFile](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile) a přiřaďte ji vlastnosti [CloudTask.OutputFiles](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles#Microsoft_Azure_Batch_CloudTask_OutputFiles) při vytváření úlohy.

Následující příklad kódu jazyka C# vytvoří úlohu, `output.txt`která zapisuje náhodná čísla do souboru s názvem . Příklad vytvoří výstupní soubor `output.txt` pro zápis do kontejneru. Příklad také vytvoří výstupní soubory pro všechny soubory `std*.txt` protokolu, které odpovídají `stderr.txt`vzoru souboru _(např._ `stdout.txt` a ). Adresa URL kontejneru vyžaduje SAS, který byl vytvořen dříve pro kontejner. Služba Batch používá SAS k ověření přístupu ke kontejneru:

```csharp
new CloudTask(taskId, "cmd /v:ON /c \"echo off && set && (FOR /L %i IN (1,1,100000) DO (ECHO !RANDOM!)) > output.txt\"")
{
    OutputFiles = new List<OutputFile>
    {
        new OutputFile(
            filePattern: @"..\std*.txt",
            destination: new OutputFileDestination(
         new OutputFileBlobContainerDestination(
                    containerUrl: containerSasUrl,
                    path: taskId)),
            uploadOptions: new OutputFileUploadOptions(
            uploadCondition: OutputFileUploadCondition.TaskCompletion)),
        new OutputFile(
            filePattern: @"output.txt",
            destination: 
         new OutputFileDestination(new OutputFileBlobContainerDestination(
                    containerUrl: containerSasUrl,
                    path: taskId + @"\output.txt")),
            uploadOptions: new OutputFileUploadOptions(
            uploadCondition: OutputFileUploadCondition.TaskCompletion)),
}
```

### <a name="specify-a-file-pattern-for-matching"></a>Určení vzorku souboru pro porovnávání

Když zadáte výstupní soubor, můžete použít vlastnost [OutputFile.FilePattern](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile.filepattern#Microsoft_Azure_Batch_OutputFile_FilePattern) k určení vzoru souboru pro porovnávání. Vzorek souboru může odpovídat nule souborů, jeden soubor nebo sadu souborů, které jsou vytvořeny úlohou.

Vlastnost **FilePattern** podporuje standardní zástupné `*` znaky souborového systému, například `**` (pro nerekurzivní shody) a (pro rekurzivní shody). Například výše uvedená ukázka kódu určuje `std*.txt` vzorek souboru tak, aby nerekurzivním způsobem odpovídal:

`filePattern: @"..\std*.txt"`

Chcete-li nahrát jeden soubor, zadejte vzorek souboru bez zástupných znaků. Například výše uvedená ukázka kódu určuje `output.txt`vzorek souboru tak, aby odpovídal :

`filePattern: @"output.txt"`

### <a name="specify-an-upload-condition"></a>Určení podmínky nahrávání

[Vlastnost OutputFileUploadOptions.UploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileuploadoptions.uploadcondition#Microsoft_Azure_Batch_OutputFileUploadOptions_UploadCondition) umožňuje podmíněné nahrávání výstupních souborů. Běžným scénářem je nahrát jednu sadu souborů, pokud je úloha úspěšná, a jinou sadu souborů, pokud se nezdaří. Můžete například nahrát podrobné soubory protokolu pouze v případě, že úloha selže a ukončí se s nenulovým ukončovacím kódem. Podobně můžete chtít odeslat soubory výsledků pouze v případě, že úkol proběhne úspěšně, protože tyto soubory mohou chybět nebo neúplné, pokud se úloha nezdaří.

Výše uvedená ukázka kódu nastaví vlastnost **UploadCondition** na **dokončení úkolu**. Toto nastavení určuje, že soubor má být odeslán po dokončení úkolů, bez ohledu na hodnotu ukončovacího kódu.

`uploadCondition: OutputFileUploadCondition.TaskCompletion`

Další nastavení naleznete v výčtu [OutputFileUploadCondition.](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.outputfileuploadcondition)

### <a name="disambiguate-files-with-the-same-name"></a>Disambiguate soubory se stejným názvem

Úkoly v úloze mohou vytvářet soubory, které mají stejný název. Například `stdout.txt` a `stderr.txt` jsou vytvořeny pro každou úlohu, která běží v úloze. Vzhledem k tomu, že každá úloha je spuštěna ve vlastním kontextu, nejsou tyto soubory v systému souborů uzlu v konfliktu. Když však nahrajete soubory z více úkolů do sdíleného kontejneru, budete muset rozepnout soubory se stejným názvem.

Vlastnost [OutputFileBlobContainerDestination.Path](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination.path#Microsoft_Azure_Batch_OutputFileBlobContainerDestination_Path) určuje cílový objekt blob nebo virtuální adresář pro výstupní soubory. Vlastnost **Path** můžete použít k pojmenování objektu blob nebo virtuálního adresáře takovým způsobem, že výstupní soubory se stejným názvem jsou jedinečně pojmenované v Azure Storage. Použití ID úlohy v cestě je dobrý způsob, jak zajistit jedinečné názvy a snadno identifikovat soubory.

Pokud je vlastnost **FilePattern** nastavena na zástupný výraz, budou všechny soubory, které odpovídají vzoru, odeslány do virtuálního adresáře určeného vlastností **Path.** Pokud je `mycontainer`například kontejner , ID `mytask`úlohy je `..\std*.txt`a vzorek souboru je , budou absolutní identifikátory URI výstupních souborů ve službě Azure Storage podobné:

```
https://myaccount.blob.core.windows.net/mycontainer/mytask/stderr.txt
https://myaccount.blob.core.windows.net/mycontainer/mytask/stdout.txt
```

Pokud je vlastnost **FilePattern** nastavena tak, aby odpovídala názvu jednoho souboru, což znamená, že neobsahuje žádné zástupné znaky, pak hodnota **vlastnosti Path** určuje plně kvalifikovaný název objektu blob. Pokud očekáváte, že pojmenování je v konfliktu s jedním souborem z více úkolů, zahrňte název virtuálního adresáře jako součást názvu souboru, abyste tyto soubory rozčlenili. Například nastavte vlastnost **Path** tak, aby zahrnovala ID úkolu, znak oddělovače (obvykle lomítko) a název souboru:

`path: taskId + @"/output.txt"`

Absolutní identifikátory URI pro výstupní soubory pro sadu úkolů budou podobné:

```
https://myaccount.blob.core.windows.net/mycontainer/task1/output.txt
https://myaccount.blob.core.windows.net/mycontainer/task2/output.txt
```

Další informace o virtuálních adresářích ve službě Azure Storage najdete [v tématu Seznam objektů BLOB v kontejneru](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container).

## <a name="diagnose-file-upload-errors"></a>Diagnostika chyb při nahrávání souborů

Pokud se nahrávání výstupních souborů do Služby Azure Storage nezdaří, úloha se přesune do stavu **Dokončeno** a je nastavena vlastnost [TaskExecutionInformation.FailureInformation.](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskexecutioninformation.failureinformation#Microsoft_Azure_Batch_TaskExecutionInformation_FailureInformation) Zkontrolujte **Vlastnost FailureInformation** k určení, k jaké chybě došlo. Například zde je chyba, ke které dochází při nahrávání souboru, pokud kontejner nelze najít:

```
Category: UserError
Code: FileUploadContainerNotFound
Message: One of the specified Azure container(s) was not found while attempting to upload an output file
```

Při každém nahrání souboru Batch zapíše dva `fileuploadout.txt` `fileuploaderr.txt`soubory protokolu do výpočetního uzlu a . Můžete prozkoumat tyto soubory protokolu další informace o konkrétní selhání. V případech, kdy se o nahrání souboru nikdy nepokusili, například proto, že samotná úloha nemohla být spuštěna, tyto soubory protokolu nebudou existovat.

## <a name="diagnose-file-upload-performance"></a>Diagnostikovat výkon nahrávání souborů

Protokoly `fileuploadout.txt` souborů průběh odesílání. Můžete zkontrolovat tento soubor další informace o tom, jak dlouho vaše soubory nahrávání trvá. Mějte na paměti, že existuje mnoho faktorů, které přispívají k nahrávání výkonu, včetně velikosti uzlu, další aktivity na uzlu v době nahrávání, zda je cílový kontejner ve stejné oblasti jako fond Batch, kolik uzlů se nahrává do úložiště a tak dále.

## <a name="use-the-batch-service-api-with-the-batch-file-conventions-standard"></a>Použití rozhraní API dávkové služby se standardem Konvence dávkových souborů

Při zachování výstupu úlohs s rozhraním API služby Batch, můžete pojmenovat cílový kontejner a objekty BLOB, jak se vám líbí. Můžete je také pojmenovat podle [standardu Konvence dávkových souborů](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions). Standard File Conventions určuje názvy cílového kontejneru a objektu blob ve službě Azure Storage pro daný výstupní soubor na základě názvů úlohy a úlohy. Pokud používáte standard File Conventions pro pojmenování výstupních souborů, jsou vaše výstupní soubory k dispozici pro zobrazení na [webu Azure Portal](https://portal.azure.com).

Pokud vyvíjíte v c#, můžete použít metody integrované do [knihovny dávkových souborů konvence pro .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files). Tato knihovna vytvoří správně pojmenované kontejnery a cesty objektů blob pro vás. Můžete například volat rozhraní API získat správný název pro kontejner, na základě názvu úlohy:

```csharp
string containerName = job.OutputStorageContainerName();
```

Metodu [CloudJobExtensions.GetOutputStorageContainerUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files.cloudjobextensions.getoutputstoragecontainerurl) můžete použít k vrácení adresy URL sdíleného přístupového podpisu (SAS), která se používá k zápisu do kontejneru. Potom můžete předat tento SAS [outputFileBlobContainerDestination](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination) konstruktoru.

Pokud vyvíjíte v jiném jazyce než C#, budete muset implementovat soubor konvencí standard sami.

## <a name="code-sample"></a>Ukázka kódu

Ukázkový projekt [PersistOutputs][github_persistoutputs] je jedním z [ukázek kódu Azure Batch][github_samples] na GitHubu. Toto řešení sady Visual Studio ukazuje, jak použít knihovnu klienta Batch pro rozhraní .NET k zachování výstupu úlohy do trvalého úložiště. Chcete-li spustit ukázku, postupujte takto:

1. Otevřete projekt v **Sadě Visual Studio 2019**.
2. Přidejte přihlašovací **údaje účtu** batch a úložiště do **účtuSettings.settings** v projektu Microsoft.Azure.Batch.Samples.Common.
3. **Sestavení** (ale nespouštějte) řešení. Pokud se zobrazí výzva, obnovte všechny balíčky NuGet.
4. Na portálu Azure nahrajte [balíček aplikace](batch-application-packages.md) **persistoutputstask**. Zahrnout `PersistOutputsTask.exe` a jeho závislá sestavení v balíčku .zip, nastavte ID aplikace na "PersistOutputsTask" a verze balíčku aplikace na "1.0".
5. **Spustit** (spustit) **persistoutputs** projektu.
6. Po zobrazení výzvy k výběru technologie trvalosti, která se má použít pro spuštění ukázky, zadejte **hodnotu 2** a spusťte ukázku pomocí rozhraní API služby Batch, chcete-li zachovat výstup úlohy.
7. V případě potřeby spusťte ukázku znovu, zadáním **3** pro zachování výstupu s rozhraním API služby Batch a také pojmenujte cílový kontejner a cestu objektu blob podle standardu File Conventions.

## <a name="next-steps"></a>Další kroky

- Další informace o trvalém výstupu úloh v knihovně Konvencí souborů pro rozhraní .NET najdete v [tématu Persist job and task data to Azure Storage with the Batch File Conventions library for .NET](batch-task-output-file-conventions.md).
- Informace o dalších přístupech k uchování výstupních dat v Azure Batch najdete v [tématu Persist job and task output to Azure Storage](batch-task-output.md).

[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
