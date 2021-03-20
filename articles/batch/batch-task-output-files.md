---
title: Zachování výstupních dat pro Azure Storage s využitím rozhraní API služby Batch
description: Naučte se používat rozhraní API služby Batch k trvalému zachovávání výstupních dat úlohy a úloh Batch a Azure Storage.
ms.topic: how-to
ms.date: 07/30/2020
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 720c064c6b382bc62565c0828422181c761df8e8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88936924"
---
# <a name="persist-task-data-to-azure-storage-with-the-batch-service-api"></a>Zachování dat úkolu Azure Storage pomocí rozhraní API služby Batch

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Rozhraní API služby Batch podporuje ukládání výstupních dat do Azure Storage pro úlohy a úlohy Správce úloh spouštěné ve fondech s konfigurací virtuálního počítače. Když přidáte úkol, můžete zadat kontejner v Azure Storage jako cíl pro výstup úlohy. Služba Batch po dokončení úkolu zapíše do kontejneru veškerá výstupní data.

Výhodou použití rozhraní API služby Batch k zachování výstupu úlohy je, že nemusíte měnit aplikaci, kterou úloha spouští. Místo toho s několika úpravami klientské aplikace můžete zachovat výstup úlohy ze stejného kódu, který úlohu vytvoří.

> [!IMPORTANT]
> Uchování dat úkolu pro Azure Storage pomocí rozhraní API služby Batch nefunguje s fondy vytvořenými před [1. února 2018](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md#1204).

## <a name="when-do-i-use-the-batch-service-api-to-persist-task-output"></a>Kdy se používá rozhraní API služby Batch k uchování výstupu úlohy?

Azure Batch poskytuje více než jeden způsob, jak uchovat výstup úlohy. Použití rozhraní API služby Batch je pohodlný přístup, který nejlépe vyhovuje těmto scénářům:

- Chcete napsat kód pro uchování výstupu úlohy z klientské aplikace bez změny aplikace, kterou váš úkol spouští.
- Chcete zachovat výstup z úloh služby Batch a úloh Správce úloh ve fondech vytvořených s konfigurací virtuálního počítače.
- Chcete zachovat výstup do kontejneru Azure Storage s libovolným názvem.
- Chcete zachovat výstup do kontejneru Azure Storage s názvem podle [standardu pro dávkové soubory](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files).

Pokud se váš scénář liší od výše uvedených výše, možná budete muset zvážit jiný přístup. Rozhraní API služby Batch například v současné době nepodporuje výstup streamování do Azure Storage, zatímco je úloha spuštěná. Pokud chcete streamovat výstup, zvažte použití knihovny konvence souborů Batch, která je k dispozici pro .NET. V ostatních jazycích budete muset implementovat vlastní řešení. Informace o dalších možnostech zachování výstupu úlohy najdete v tématu [trvalé uložení úlohy a výstupu úlohy do Azure Storage](batch-task-output.md).

## <a name="create-a-container-in-azure-storage"></a>Vytvoření kontejneru v Azure Storage

Chcete-li zachovat výstup úlohy do Azure Storage, budete muset vytvořit kontejner, který slouží jako cíl pro výstupní soubory. Vytvořte kontejner před spuštěním úlohy, nejlépe předtím, než odešlete úlohu. Chcete-li vytvořit kontejner, použijte příslušnou klientskou knihovnu Azure Storage nebo sadu SDK. Další informace o rozhraních API Azure Storage najdete v [dokumentaci k Azure Storage](../storage/index.yml).

Například při psaní aplikace v jazyce C# použijte [Azure Storage klientskou knihovnu pro .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). Následující příklad ukazuje, jak vytvořit kontejner:

```csharp
CloudBlobContainer container = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
await container.CreateIfNotExists();
```

## <a name="get-a-shared-access-signature-for-the-container"></a>Získání sdíleného přístupového podpisu pro kontejner

Po vytvoření kontejneru Získejte sdílený přístupový podpis (SAS) s přístupem pro zápis do kontejneru. SAS poskytuje delegovaný přístup ke kontejneru. SAS udělí přístup se zadanou sadou oprávnění a v zadaném časovém intervalu. Služba Batch potřebuje k zápisu výstupu úlohy do kontejneru SAS s oprávněním k zápisu. Další informace o SAS najdete v tématu [použití SAS sdílených přístupových podpisů \( \) v Azure Storage](../storage/common/storage-sas-overview.md).

Když obdržíte SAS pomocí Azure Storage rozhraní API, rozhraní API vrátí řetězec tokenu SAS. Tento řetězec tokenu zahrnuje všechny parametry SAS, včetně oprávnění a intervalu, za který je SAS platný. Chcete-li použít SAS pro přístup ke kontejneru v Azure Storage, je nutné připojit řetězec tokenu SAS k identifikátoru URI prostředku. Identifikátor URI prostředku, společně s připojením tokenu SAS, poskytuje ověřený přístup k Azure Storage.

Následující příklad ukazuje, jak získat řetězec tokenu SAS jen pro zápis pro kontejner a pak připojit SAS k identifikátoru URI kontejneru:

```csharp
string containerSasToken = container.GetSharedAccessSignature(new SharedAccessBlobPolicy()
{
    SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddDays(1),
    Permissions = SharedAccessBlobPermissions.Write
});

string containerSasUrl = container.Uri.AbsoluteUri + containerSasToken;
```

## <a name="specify-output-files-for-task-output"></a>Zadat výstupní soubory pro výstup úlohy

Chcete-li určit výstupní soubory pro úlohu, vytvořte kolekci objektů typu [Výstupní_soubor](/dotnet/api/microsoft.azure.batch.outputfile) a přiřaďte ji k vlastnosti [CloudTask. OutputFiles](/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles#Microsoft_Azure_Batch_CloudTask_OutputFiles) při vytváření úlohy.

Následující příklad kódu jazyka C# vytvoří úlohu, která zapíše náhodná čísla do souboru s názvem `output.txt` . Příklad vytvoří výstupní soubor pro `output.txt` zápis do kontejneru. V tomto příkladu se vytvoří také výstupní soubory pro všechny soubory protokolů, které odpovídají vzoru souboru `std*.txt` (_např._ `stdout.txt` a `stderr.txt` ). Adresa URL kontejneru vyžaduje SAS, který byl vytvořen dříve pro kontejner. Služba Batch používá k ověření přístupu ke kontejneru SAS:

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

> [!NOTE]
> Pokud používáte tento příklad se systémem Linux, nezapomeňte změnit zpětná lomítka na lomítka.

### <a name="specify-a-file-pattern-for-matching"></a>Zadat vzor souboru pro porovnávání

Při zadání výstupního souboru můžete použít vlastnost [Výstupní_soubor. File Pattern](/dotnet/api/microsoft.azure.batch.outputfile.filepattern#Microsoft_Azure_Batch_OutputFile_FilePattern) k určení vzoru souboru pro porovnávání. Vzor souboru se může shodovat s nulovými soubory, s jedním souborem nebo sadou souborů, které jsou vytvořeny úlohou.

Vlastnost **Pattern vzor** podporuje standardní zástupné znaky systému souborů, například `*` (pro nerekurzivní shody) a `**` (pro rekurzivní shody). Například vzor kódu výše určuje vzor souboru, který se bude shodovat `std*.txt` bez rekurzivní:

`filePattern: @"..\std*.txt"`

Pokud chcete nahrát jeden soubor, určete vzorek souboru bez zástupných znaků. Například vzor kódu výše určuje vzor souboru, který se má shodovat `output.txt` :

`filePattern: @"output.txt"`

### <a name="specify-an-upload-condition"></a>Zadat podmínku odeslání

Vlastnost [OutputFileUploadOptions. UploadCondition](/dotnet/api/microsoft.azure.batch.outputfileuploadoptions.uploadcondition#Microsoft_Azure_Batch_OutputFileUploadOptions_UploadCondition) povoluje podmíněné nahrávání výstupních souborů. Běžným scénářem je odeslat jednu sadu souborů, pokud je úloha úspěšná, a jinou sadu souborů, pokud se nezdaří. Například můžete chtít odeslat podrobné soubory protokolu pouze v případě, že se úloha nezdařila a ukončí s nenulovým ukončovacím kódem. Podobně můžete chtít odeslat soubory výsledků, jenom pokud je úloha úspěšná, protože tyto soubory můžou chybět nebo být neúplné, pokud se úloha nezdaří.

Výše uvedený vzor kódu nastaví vlastnost **UploadCondition** na hodnotu **TaskCompletion**. Toto nastavení určuje, že se má soubor nahrát po dokončení úkolů, bez ohledu na hodnotu ukončovacího kódu.

`uploadCondition: OutputFileUploadCondition.TaskCompletion`

Další nastavení naleznete v tématu [OutputFileUploadCondition](/dotnet/api/microsoft.azure.batch.common.outputfileuploadcondition) Enum.

### <a name="disambiguate-files-with-the-same-name"></a>Nejednoznačnost souborů se stejným názvem

Úlohy v úloze můžou vytvořit soubory, které mají stejný název. Například `stdout.txt` a `stderr.txt` jsou vytvořeny pro každý úkol, který běží v rámci úlohy. Vzhledem k tomu, že každý úkol běží ve vlastním kontextu, tyto soubory nejsou v konfliktu v systému souborů uzlu. Když ale nahráváte soubory z několika úloh do sdíleného kontejneru, budete muset soubory se stejným názvem jednoznačně určit.

Vlastnost [OutputFileBlobContainerDestination. Path](/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination.path#Microsoft_Azure_Batch_OutputFileBlobContainerDestination_Path) Určuje cílový objekt BLOB nebo virtuální adresář pro výstupní soubory. Vlastnost **path** můžete použít k pojmenování objektu BLOB nebo virtuálního adresáře takovým způsobem, že výstupní soubory se stejným názvem jsou jednoznačně pojmenované v Azure Storage. Použití ID úlohy v cestě je dobrý způsob, jak zajistit jedinečné názvy a snadno identifikovat soubory.

Pokud je vlastnost **Pattern vzoru** nastavená na zástupný znak, všechny soubory, které se shodují se vzorem, se nahrají do virtuálního adresáře zadaného vlastností **path** . Například pokud je kontejner `mycontainer` , ID úkolu je `mytask` a vzor souboru je `..\std*.txt` , pak absolutní identifikátory URI na výstupní soubory v Azure Storage budou vypadat podobně jako:

```
https://myaccount.blob.core.windows.net/mycontainer/mytask/stderr.txt
https://myaccount.blob.core.windows.net/mycontainer/mytask/stdout.txt
```

Pokud je vlastnost **Pattern vzoru** nastavená tak, aby odpovídala jednomu názvu souboru, což znamená, že neobsahuje žádné zástupné znaky, pak hodnota vlastnosti **path** Určuje plně kvalifikovaný název objektu BLOB. Pokud očekáváte, že se pojmenování v konfliktu s jedním souborem z více úloh, pak jako součást názvu souboru přidejte název virtuálního adresáře, aby bylo možné tyto soubory odlišit. Například nastavte vlastnost **cesta** tak, aby obsahovala ID úlohy, znak oddělovače (obvykle lomítko) a název souboru:

`path: taskId + @"/output.txt"`

Absolutní identifikátory URI pro výstupní soubory pro sadu úloh budou vypadat podobně jako:

```
https://myaccount.blob.core.windows.net/mycontainer/task1/output.txt
https://myaccount.blob.core.windows.net/mycontainer/task2/output.txt
```

Další informace o virtuálních adresářích v Azure Storage najdete v tématu [seznam objektů BLOB v kontejneru](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container).

## <a name="diagnose-file-upload-errors"></a>Diagnostika chyb při nahrávání souborů

Pokud se nahrává výstupní soubory do Azure Storage dojde k chybě, úloha se přesune do stavu **dokončeno** a vlastnost [TaskExecutionInformation. FailureInformation](/dotnet/api/microsoft.azure.batch.taskexecutioninformation.failureinformation#Microsoft_Azure_Batch_TaskExecutionInformation_FailureInformation) je nastavena. Zkontrolujte vlastnost **FailureInformation** a určete, k jakou chybě došlo. Tady je například chyba, ke které dojde při nahrávání souboru, pokud se kontejner nedá najít:

```
Category: UserError
Code: FileUploadContainerNotFound
Message: One of the specified Azure container(s) was not found while attempting to upload an output file
```

Při každém nahrání souboru Batch zapisuje dva soubory protokolu do výpočetního uzlu `fileuploadout.txt` a `fileuploaderr.txt` . Můžete si prohlédnout tyto soubory protokolu a získat další informace o konkrétní chybě. V případech, kdy se odeslání souboru nikdy nepokoušelo, například kvůli tomu, že se nezdařilo spustit samotný úkol, tyto soubory protokolu nebudou existovat.

## <a name="diagnose-file-upload-performance"></a>Diagnostika výkonu nahrávání souborů

V `fileuploadout.txt` protokolech souborů probíhá nahrávání. V tomto souboru můžete zjistit, jak dlouho trvá nahrávání souborů. Mějte na paměti, že existuje mnoho přispívajících faktorů pro nahrávání výkonu, včetně velikosti uzlu, jiné aktivity na uzlu v době nahrávání, zda je cílový kontejner ve stejné oblasti jako fond služby Batch, kolik uzlů se nahrává do účtu úložiště ve stejnou dobu a tak dále.

## <a name="use-the-batch-service-api-with-the-batch-file-conventions-standard"></a>Použití rozhraní API služby Batch společně se standardními konvencemi pro dávkové soubory

Když zachová výstup úlohy pomocí rozhraní API služby Batch, můžete svůj cílový kontejner a objekty blob pojmenovat, ale budete chtít. Můžete také zvolit jejich pojmenování podle [standardu pro dávkové soubory](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files). Standardní konvence souborů určuje názvy cílového kontejneru a objektu BLOB v Azure Storage pro daný výstupní soubor na základě názvů úlohy a úlohy. Pokud pro pojmenování výstupních souborů použijete standardní konvence souborů, jsou vaše výstupní soubory k dispozici pro zobrazení v [Azure Portal](https://portal.azure.com).

Pokud vyvíjíte v jazyce C#, můžete použít metody integrované do [knihovny konvence pro dávkové soubory pro .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files). Tato knihovna vytvoří správně pojmenované kontejnery a cesty objektů BLOB. Například můžete volat rozhraní API a získat tak správný název kontejneru na základě názvu úlohy:

```csharp
string containerName = job.OutputStorageContainerName();
```

K vrácení adresy URL sdíleného přístupového podpisu (SAS), která se používá k zápisu do kontejneru, můžete použít metodu [CloudJobExtensions. GetOutputStorageContainerUrl](/dotnet/api/microsoft.azure.batch.conventions.files.cloudjobextensions.getoutputstoragecontainerurl) . Pak můžete toto SAS předat konstruktoru [OutputFileBlobContainerDestination](/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination) .

Pokud vyvíjíte v jiném jazyce než C#, bude nutné implementovat standardní konvence souborů sami.

## <a name="code-sample"></a>Ukázka kódu

Vzorový projekt [PersistOutputs](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs) je jednou z [Azure Batch ukázek kódu](https://github.com/Azure/azure-batch-samples) na GitHubu. Toto řešení sady Visual Studio ukazuje, jak pomocí klientské knihovny Batch pro .NET uchovat výstup úlohy do trvalého úložiště. Chcete-li spustit ukázku, postupujte podle následujících kroků:

1. Otevřete projekt v **aplikaci Visual Studio 2019**.
2. Přidejte **přihlašovací údaje** služby Batch a účtu úložiště do **AccountSettings. settings** v projektu Microsoft.Azure.Batch. Samples. Common.
3. **Sestavte** (ale nespouštějte) řešení. Pokud se zobrazí výzva, obnovte případné balíčky NuGet.
4. Pomocí Azure Portal nahrajte [balíček aplikace](batch-application-packages.md) pro **PersistOutputsTask**. Zahrňte `PersistOutputsTask.exe` a jeho závislá sestavení v balíčku. zip, nastavte ID aplikace na "PersistOutputsTask" a verzi balíčku aplikace na "1,0".
5. **Spusťte** (spusťte) projekt **PersistOutputs** .
6. Po zobrazení výzvy k výběru technologie trvalosti, která se má použít ke spuštění ukázky, zadejte **2** pro spuštění ukázky pomocí rozhraní API služby Batch pro zachování výstupu úlohy.
7. V případě potřeby spusťte ukázku **znovu a** zachovejte výstup pomocí rozhraní API služby Batch a také pojmenujte cílový kontejner a cestu objektu BLOB podle standardu souborů.

## <a name="next-steps"></a>Další kroky

- Další informace o zachování výstupu úlohy pomocí knihovny pro konvence souborů pro .NET najdete v tématu [zachování úloh a dat úloh pro Azure Storage s knihovnou konvence souborů Batch pro .NET](batch-task-output-file-conventions.md).
- Další informace o dalších možnostech, jak zachovat výstupní data v Azure Batch, najdete v tématu [trvalé uložení úlohy a úlohy do Azure Storage](batch-task-output.md).
