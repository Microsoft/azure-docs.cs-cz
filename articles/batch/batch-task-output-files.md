---
title: Trvalý výstup úloh a ke službě Azure Storage pomocí služby Batch API – Azure Batch | Dokumentace Microsoftu
description: Další informace o použití rozhraní API služby Batch pro trvalý výstup úloh a úlohy služby Batch do služby Azure Storage.
services: batch
author: laurenhughes
manager: jeconnoc
editor: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/14/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 685f034dfa3b312c461f7313c0f85a256838a8d2
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53539547"
---
# <a name="persist-task-data-to-azure-storage-with-the-batch-service-api"></a>Zachovat data úloh do služby Azure Storage pomocí rozhraní API služby Batch

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Rozhraní API služby Batch podporuje zachování výstupní data do služby Azure Storage pro úkoly a úkolech Správce úloh, které běží u fondů s vlastností konfigurace virtuálního počítače. Při přidání úlohy, můžete zadat kontejner ve službě Azure Storage jako cíl pro výstup úkolu. Služba Batch pak zapíše veškerá výstupní data do tohoto kontejneru, když je úloha dokončena.

Výhodou použití rozhraní API služby Batch se zachovat výstup úkolu je, že není potřeba upravovat aplikace, který je spuštěn úkol. Místo toho je možné zachovat výstup úkolu z v rámci stejný kód, který vytvoří úkol několik změn do klientské aplikace.

## <a name="when-do-i-use-the-batch-service-api-to-persist-task-output"></a>Kdy použít rozhraní API služby Batch se zachovat výstup úlohy?

Azure Batch poskytuje více než jeden způsob, jak zachovat výstup úlohy. Pomocí rozhraní API služby Batch je pohodlný přístup, který je nejvhodnější pro tyto scénáře:

- Chcete napsat kód, který trvalý výstup úloh z v rámci klientské aplikace beze změny aplikací, na kterém běží vaše úlohy.
- Chcete zachovat výstup z úlohy služby Batch a úkolech Správce úloh v fondy vytvořené s konfigurací virtuálního počítače.
- Chcete zachovat výstup do kontejneru služby Azure Storage s libovolný název.
- Chcete zachovat výstup do kontejneru služby Azure Storage s názvem podle [Batch File Conventions standard](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions). 

> [!NOTE]
> Rozhraní API služby Batch nepodporuje zachovávání dat. z úloh spuštěných v fondy vytvořené s konfigurací cloudové služby. Informace o zachování úloh, výstup z fondů spuštění konfigurace služby cloud services najdete v tématu [uchování úloh a dat do služby Azure Storage pomocí knihovny Batch File Conventions pro .NET k uchování ](batch-task-output-file-conventions.md).

Pokud váš scénář se liší od těch uvedené výše, budete muset vzít v úvahu jiný přístup. Například rozhraní API služby Batch nepodporuje aktuálně streamování výstup do služby Azure Storage je spuštěn úkol. Chcete-li výstupní datový proud stream, zvažte použití knihovny Batch File Conventions dostupné pro .NET. Pro jiné jazyky musíte implementovat vlastní řešení. Další informace o dalších možnostech pro zachování výstup úlohy najdete v tématu [trvalý výstup úloh a ke službě Azure Storage](batch-task-output.md).

## <a name="create-a-container-in-azure-storage"></a>Vytvořit kontejner ve službě Azure Storage

Chcete-li trvalý výstup úloh do služby Azure Storage, budete muset vytvořit kontejner, který slouží jako cíl pro výstupní soubory. Před spuštěním úlohy, přednostně před odesláním úlohy, vytvořte kontejner. Pokud chcete vytvořit kontejner, použijte příslušnou knihovnu klienta služby Azure Storage nebo sady SDK. Další informace o rozhraní API služby Azure Storage, najdete v článku [dokumentace ke službě Azure Storage](https://docs.microsoft.com/azure/storage/).

Například pokud píšete aplikaci v jazyce C#, použijte [Klientská knihovna Azure Storage pro .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). Následující příklad ukazuje, jak vytvořit kontejner:

```csharp
CloudBlobContainer container = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
await conainer.CreateIfNotExists();
```

## <a name="get-a-shared-access-signature-for-the-container"></a>Získání sdíleného přístupového podpisu kontejneru

Po vytvoření kontejneru získáte sdílený přístupový podpis (SAS) s oprávněním k zápisu do kontejneru. SAS poskytuje Delegovaný přístup ke kontejneru. SAS uděluje přístup s konkrétní sadou oprávnění a v zadaném časovém intervalu. Služba Batch musí SAS s oprávněním pro zápis zapisovat výstup úlohy do kontejneru. Další informace o SAS najdete v tématu [použití sdílených přístupových podpisů \(SAS\) ve službě Azure Storage](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

Když dostanete SAS pomocí rozhraní API služby Azure Storage, rozhraní API vrátí řetězec tokenu SAS. Tento token řetězec obsahuje všechny parametry SAS, včetně oprávnění a interval, přes které SAS. Pokud chcete použít SAS pro přístup ke kontejneru ve službě Azure Storage, budete muset připojit řetězec tokenu SAS URI prostředku. Identifikátor URI, společně s připojený token SAS, poskytuje ověřený přístup ke službě Azure Storage.

Následující příklad ukazuje, jak získat řetězec tokenu SAS jen pro zápis pro kontejner a pak sdíleného přístupového podpisu připojí k identifikátoru URI kontejneru:

```csharp
string containerSasToken = container.GetSharedAccessSignature(new SharedAccessBlobPolicy()
{
    SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddDays(1),
    Permissions = SharedAccessBlobPermissions.Write
});

string containerSasUrl = container.Uri.AbsoluteUri + containerSasToken;
```

## <a name="specify-output-files-for-task-output"></a>Zadejte výstupní soubory pro výstup úlohy

K určení výstupních souborů pro úlohu, vytvoření kolekce [OutputFile](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile) objekty a přiřaďte ho [CloudTask.OutputFiles](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles#Microsoft_Azure_Batch_CloudTask_OutputFiles) vlastnost při vytvoření úkolu.

Následující C# příklad kódu vytvoří úlohu, která zapíše náhodná čísla do souboru s názvem `output.txt`. Příklad vytvoří výstupní soubor pro `output.txt` k zápisu do kontejneru. Tento příklad také vytvoří výstupní soubory pro všechny soubory protokolů, které odpovídají vzoru souboru `std*.txt` (_například_, `stdout.txt` a `stderr.txt`). Adresa URL kontejneru SAS, který byl vytvořen dříve vyžaduje pro kontejner. Služba Batch používá SAS k ověření přístupu ke kontejneru:

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

### <a name="specify-a-file-pattern-for-matching"></a>Zadejte vzorek souborů pro porovnávání

Když zadáte výstupní soubor, můžete použít [OutputFile.FilePattern](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile.filepattern#Microsoft_Azure_Batch_OutputFile_FilePattern) vlastnosti a určit soubor vzor k porovnání. Vzor souboru může shodovat s nulovou soubory, jeden soubor nebo sady souborů, které jsou vytvořené úlohou.

**FilePattern** vlastnost podporuje zástupné znaky standardní systém souborů, jako `*` (pro nerekurzivní odpovídá) a `**` (pro rekurzivní odpovídá). Například ukázkovém kódu výše Určuje vzor souborů tak, aby odpovídaly `std*.txt` nerekurzivně:

`filePattern: @"..\std*.txt"`

Pokud chcete nahrát jeden soubor, zadejte vzorek souborů bez zástupných znaků. Například ukázkovém kódu výše Určuje vzor souborů tak, aby odpovídaly `output.txt`:

`filePattern: @"output.txt"`

### <a name="specify-an-upload-condition"></a>Zadejte podmínku nahrávání

[OutputFileUploadOptions.UploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileuploadoptions.uploadcondition#Microsoft_Azure_Batch_OutputFileUploadOptions_UploadCondition) vlastnost povoluje podmíněné odesílání výstupních souborů. Běžný scénář, kdy se má nahrát jednu sadu souborů, pokud je úloha úspěšná a jinou sadu souborů, pokud se nezdaří. Můžete například nahrát souborů podrobného protokolování, pouze pokud úloha selže a ukončí se nenulový ukončovací kód. Podobně můžete nahrát soubory s výsledky pouze v případě, že je úloha úspěšná, protože tyto soubory mohou být chybějící či neúplné, pokud se úloha nezdaří.

Ukázka kódu nad sad **UploadCondition** vlastnost **TaskCompletion**. Toto nastavení určuje, zda je soubor k nahrání, jakmile úkoly dokončí, bez ohledu na hodnotu ukončovacího kódu.

`uploadCondition: OutputFileUploadCondition.TaskCompletion`

Další nastavení, najdete v článku [OutputFileUploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.outputfileuploadcondition) výčtu.

### <a name="disambiguate-files-with-the-same-name"></a>Rozlišit soubory se stejným názvem

Úkoly v úloze může vytvořit soubory, které mají stejný název. Například `stdout.txt` a `stderr.txt` jsou vytvořeny pro každý úkol, který běží v rámci úlohy. Protože každý úkol spustí ve vlastním kontextu, tyto soubory nejsou ve vzájemném konfliktu v systému souborů uzlu. Ale při nahrávání souborů z více úloh do sdíleného kontejneru, bude nutné k rozlišení soubory se stejným názvem.

[OutputFileBlobContainerDestination.Path](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination.path#Microsoft_Azure_Batch_OutputFileBlobContainerDestination_Path) vlastnost určuje cílový objekt blob nebo virtuální adresář pro výstupní soubory. Můžete použít **cesta** nastavte na název objektu blob nebo virtuálního adresáře tak, že výstupní soubory se stejným názvem jsou pojmenovány jednoznačně ve službě Azure Storage. Pomocí ID úkolu v cestě je dobrým způsobem, jak zajistit jedinečné názvy a snadno identifikovat soubory.

Pokud **FilePattern** je nastavena na výraz se zástupnými znaky, pak všechny soubory, které odpovídají vzoru se nahrají do virtuálního adresáře určené **cesta** vlastnost. Například, pokud je kontejner `mycontainer`, úlohy je ID `mytask`, a je vzor souborů `..\std*.txt`, pak bude vypadat podobně jako absolutní URI pro výstupní soubory ve službě Azure Storage:

```
https://myaccount.blob.core.windows.net/mycontainer/mytask/stderr.txt
https://myaccount.blob.core.windows.net/mycontainer/mytask/stdout.txt
```

Pokud **FilePattern** vlastnost je nastavena tak, aby odpovídaly jeden název souboru, což znamená, neobsahuje žádné zástupné znaky, pak hodnota **cesta** vlastnost určuje název objektu blob plně kvalifikovaný. Pokud očekáváte, že ke konfliktu názvů s jeden soubor z více úloh, pak zahrnete název virtuálního adresáře jako součást názvu souboru k rozlišení těchto souborů. Například nastavit **cesta** vlastnost ID úkolu, oddělovací znak (obvykle dopředné lomítko) a název souboru:

`path: taskId + @"/output.txt"`

Absolutní URI do výstupních souborů pro sadu úloh, bude vypadat podobně jako:

```
https://myaccount.blob.core.windows.net/mycontainer/task1/output.txt
https://myaccount.blob.core.windows.net/mycontainer/task2/output.txt
```

Další informace o virtuálních adresářů ve službě Azure Storage najdete v tématu [uvádět seznamy blobů v kontejneru](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container).

## <a name="diagnose-file-upload-errors"></a>Diagnostika chyb nahrávání souboru

Pokud odesílání výstupních souborů do služby Azure Storage se nezdaří, pak úloha přejde **dokončeno** stavu a [TaskExecutionInformation.FailureInformation](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskexecutioninformation.failureinformation#Microsoft_Azure_Batch_TaskExecutionInformation_FailureInformation) je nastavena. Zkontrolujte **FailureInformation** a určí, co k chybě. Tady je například Chyba, ke které dochází na nahrávání souborů, pokud nelze najít kontejner:

```
Category: UserError
Code: FileUploadContainerNotFound
Message: One of the specified Azure container(s) was not found while attempting to upload an output file
```

Na každé nahrání souboru zapíše dva soubory protokolu na výpočetním uzlu, Batch `fileuploadout.txt` a `fileuploaderr.txt`. Můžete prozkoumat tyto soubory protokolu na další informace o konkrétní chybě. V případech, kde samotné nahrávání souborů byl nikdy proveden pokus o, třeba protože se nepovedlo spustit vlastní úloha pak tyto soubory protokolu nebude existovat.

## <a name="diagnose-file-upload-performance"></a>Diagnostika výkonu nahrání souboru

`fileuploadout.txt` Protokoly průběhu nahrávání souboru. Tento soubor na další informace o tom, jak dlouho trvá vaše nahrávání souborů můžete zkontrolovat. Pamatujte, že existuje celá řada faktorů přispívajících k nahrání výkonu, včetně velikost uzlu, druhé aktivity na uzlu v okamžiku odeslání, zda cílový kontejner je ve stejné oblasti jako fondu služby Batch, kolik uzlů nahráváte do stora ge účet ve stejnou dobu a tak dále.

## <a name="use-the-batch-service-api-with-the-batch-file-conventions-standard"></a>Rozhraní API služby Batch pomocí standardu Batch File Conventions

Když je trvalý výstup úloh pomocí rozhraní API služby Batch, můžete pojmenovat cílový kontejner a objekty BLOB, ale chcete. Můžete také pojmenovat je podle [Batch File Conventions standard](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions). Standardní File Conventions určuje názvy cílový kontejner a objektů blob ve službě Azure Storage pro danou výstupní soubor podle názvu úlohy a úkolů. Pokud použijete standardní soubor konvence pro pojmenování výstupních souborů, pak výstupní soubory jsou k dispozici pro zobrazení v [webu Azure portal](https://portal.azure.com).

Pokud vyvíjíte v jazyce C#, můžete použít metody součástí [Batch File Conventions library pro .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files). Tato knihovna vytvoří správně pojmenovaných kontejnerů a cesty k objektům blob. Například můžete volat rozhraní API, chcete-li získat správný název kontejneru, na základě názvu úlohy:

```csharp
string containerName = job.OutputStorageContainerName();
```

Můžete použít [CloudJobExtensions.GetOutputStorageContainerUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files.cloudjobextensions.getoutputstoragecontainerurl) metoda vrátí adresu URL sdíleného přístupového podpisu (SAS), který se používá k zápisu do kontejneru. Můžete předat tento SAS [OutputFileBlobContainerDestination](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination) konstruktoru.

Pokud vyvíjíte v jiném jazyce než v jazyce C#, je potřeba implementovat standard File Conventions sami.

## <a name="code-sample"></a>Ukázka kódu

[PersistOutputs] [ github_persistoutputs] ukázkový projekt je jedním z [ukázky kódu Azure Batch] [ github_samples] na Githubu. Toto řešení sady Visual Studio ukazuje, jak používat klientskou knihovnu služby Batch pro .NET k uchování výstup úlohy do trvalého úložiště. Ke spuštění ukázky, postupujte podle těchto kroků:

1. Otevřete projekt v **Visual Studio 2017**.
2. Přidání služby Batch a Storage **přihlašovací údaje účtu** k **AccountSettings.settings** Microsoft.Azure.Batch.Samples.Common projektu.
3. **Sestavení** (ale nespouštějte) řešení. Obnovení všech balíčků NuGet, pokud se zobrazí výzva.
4. Pomocí webu Azure portal k odeslání [balíčku aplikace](batch-application-packages.md) pro **PersistOutputsTask**. Zahrnout `PersistOutputsTask.exe` a jeho závislých sestavení v balíčku .zip, nastavte na "PersistOutputsTask" ID aplikace a verze balíčku aplikace má "1.0".
5. **Spustit** (spustit) **PersistOutputs** projektu.
6. Po zobrazení výzvy k výběru technologie trvalosti pro používání vzorku, zadejte **2** ke spuštění ukázky trvalý výstup úloh pomocí rozhraní API služby Batch.
7. V případě potřeby spusťte ukázku znovu, zadávání **3** trvalý výstup pomocí rozhraní API služby Batch a také k pojmenování cílovou cestu kontejneru a objektu blob podle standardu File Conventions.

## <a name="next-steps"></a>Další postup

- Další informace o zachování úlohy výstup pomocí knihovny File Conventions pro rozhraní .NET najdete v tématu [uchování úloh a dat do služby Azure Storage pomocí knihovny File Conventions služby Batch pro .NET](batch-task-output-file-conventions.md).
- Informace o jiných přístupech pro zachování výstupní data ve službě Azure Batch najdete v tématu [trvalý výstup úloh a ke službě Azure Storage](batch-task-output.md).

[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
