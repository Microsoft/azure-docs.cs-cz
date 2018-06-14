---
title: Zachovat výstup úlohy a úkolů do služby Azure Storage pomocí rozhraní API služby Azure Batch | Microsoft Docs
description: Naučte se používat rozhraní API služby Batch udržení výstup úlohy a úlohy Batch do služby Azure Storage.
services: batch
author: dlepow
manager: jeconnoc
editor: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 06/16/2017
ms.author: danlep
ms.openlocfilehash: ee8622525adcc698bf920b0c3379cc3065798a19
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2018
ms.locfileid: "30315588"
---
# <a name="persist-task-data-to-azure-storage-with-the-batch-service-api"></a>Zachovat data úloh do služby Azure Storage pomocí rozhraní API služby Batch

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Počínaje verzí 2017-05-01, rozhraní API služby Batch podporuje zachování výstupní data do úložiště Azure pro úlohy a úkolech Správce úloh, které běží na fondy s danou konfiguraci virtuálního počítače. Přidáte-li úlohu, můžete zadat kontejner ve službě Azure Storage jako cíl pro výstup úkolu. Služba Batch pak zapíše veškerá výstupní data do tohoto kontejneru, po dokončení úlohy.

Výhodou pomocí rozhraní API služby Batch udržení výstup úlohy je, že není potřeba upravovat aplikace, který je spuštěn úkol. Místo toho se několik jednoduchých úprav klientskou aplikaci, můžete zachovat výstup úkolu v rámci kód, který vytvoří úlohu.   

## <a name="when-do-i-use-the-batch-service-api-to-persist-task-output"></a>Když pomocí rozhraní API služby Batch lze zachovat výstup úlohy?

Azure Batch poskytuje více než jeden způsob, jak zachovat výstup úlohy. Pomocí rozhraní API služby Batch je vhodné přístup, který je nejvhodnější pro tyto scénáře:

- Chcete napsat kód pro zachování výstup úlohy z v rámci klientské aplikace beze změny aplikace, která vaše úloha spuštěna.
- Chcete zachovat výstup z úlohy Batch a úkolech Správce úloh ve fondech vytvořené pomocí konfigurace virtuálního počítače.
- Chcete zachovat výstup ke kontejneru úložiště Azure libovolný název.
- Chcete zachovat výstup do Azure Storage kontejner s názvem podle [dávkového souboru konvence standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). 

Pokud váš scénář se liší od výše uvedené, musíte vzít v úvahu jiný přístup. Například rozhraní API služby Batch nepodporuje aktuálně streamování výstup do úložiště Azure. když je spuštěn úkol. Do výstupního datového proudu, zvažte použití dávkového souboru konvence knihovny, k dispozici pro rozhraní .NET. Pro jiné jazyky musíte implementovat vlastní řešení. Další informace o dalších možnostech pro zachování výstup úlohy najdete v tématu [zachovat výstup úlohy a úkolů do služby Azure Storage](batch-task-output.md). 

## <a name="create-a-container-in-azure-storage"></a>Vytvořit kontejner ve službě Azure Storage

Udržení výstup úlohy do úložiště Azure, budete muset vytvořit kontejner, který slouží jako cíl pro výstupní soubory. Vytvoření kontejneru před spuštěním úkolu, pokud možno před odesláním úlohu. Pokud chcete vytvořit kontejner, použijte odpovídající Klientská knihovna pro úložiště Azure nebo sady SDK. Další informace o rozhraní API úložiště Azure najdete v tématu [dokumentaci pro Azure Storage](https://docs.microsoft.com/azure/storage/).

Například pokud píšete vaší aplikace v jazyce C#, použijte [Klientská knihovna pro úložiště Azure pro .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). Následující příklad ukazuje, jak vytvořit kontejner:

```csharp
CloudBlobContainer container = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
await conainer.CreateIfNotExists();
```

## <a name="get-a-shared-access-signature-for-the-container"></a>Získání sdíleného přístupového podpisu kontejneru

Po vytvoření kontejneru získáte sdílený přístupový podpis (SAS) s přístupem k zápisu do kontejneru. SAS poskytuje Delegovaný přístup ke kontejneru. SAS uděluje přístup pomocí zadané sady oprávnění a v zadaném časovém intervalu. Služba Batch musí SAS s oprávněními k zápisu pro zápis výstupu úlohy do kontejneru. Další informace o tokenu SAS naleznete v tématu [pomocí sdílené přístupové podpisy \(SAS\) ve službě Azure Storage](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

Když získáte SAS pomocí rozhraní API úložiště Azure, rozhraní API vrátí řetězec tokenu SAS. Tento token řetězec obsahuje všechny parametry SAS, včetně oprávnění a interval, za které SAS platný. Použití sdíleného přístupového podpisu pro přístup k kontejner ve službě Azure Storage, budete muset připojit řetězec tokenu SAS k identifikátoru URI prostředku. Identifikátor URI, společně s připojením tokenu SAS, poskytuje ověřený přístup do služby Azure Storage.

Následující příklad ukazuje, jak získat řetězec tokenu SAS jen pro zápis pro kontejner, a poté připojí ke kontejneru identifikátor URI SAS:

```csharp
string containerSasToken = container.GetSharedAccessSignature(new SharedAccessBlobPolicy()
{
    SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddDays(1),
    Permissions = SharedAccessBlobPermissions.Write
});

string containerSasUrl = container.Uri.AbsoluteUri + containerSasToken; 
```

## <a name="specify-output-files-for-task-output"></a>Zadejte výstupní soubory pro výstup úlohy

K určení výstupní soubory pro úlohu, vytvořit kolekci [výstupní soubor](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile) objekty a přiřadit ji ke [CloudTask.OutputFiles](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles#Microsoft_Azure_Batch_CloudTask_OutputFiles) vlastnost, když vytvoříte úlohu. 

Následující příklad kódu .NET vytvoří úlohu, která zapisuje náhodná čísla do souboru s názvem `output.txt`. V příkladu se vytváří výstupní soubor pro `output.txt` k zápisu do kontejneru. V příkladu se vytváří také výstupní soubory pro všechny soubory protokolů, které se shodují se vzorem souboru `std*.txt` (_například_, `stdout.txt` a `stderr.txt`). URL adresa kontejneru vyžaduje SAS, který byl vytvořen dříve kontejneru. Služba Batch používá SAS k ověření přístupu ke kontejneru: 

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

### <a name="specify-a-file-pattern-for-matching"></a>Zadejte odpovídající vzor souboru

Když zadáte výstupního souboru, můžete použít [OutputFile.FilePattern](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile.filepattern#Microsoft_Azure_Batch_OutputFile_FilePattern) vlastnosti a určit tak odpovídající vzor souboru. Vzor souboru může odpovídat nulové soubory, jeden soubor nebo u sady souborů, které jsou vytvořené úlohou.

**FilePattern** vlastnost podporuje zástupné znaky standardní systém souborů, jako `*` (pro tohoto nerekurzivního odpovídá) a `**` (pro rekurzivní odpovídá). Například výše uvedený příklad určuje vzor souborů tak, aby odpovídaly `std*.txt` bez rekurzivně: 

`filePattern: @"..\std*.txt"`

Chcete-li nahrát jeden soubor, zadejte vzor souborů s žádné zástupné znaky. Například výše uvedený příklad určuje vzor souborů tak, aby odpovídaly `output.txt`:

`filePattern: @"output.txt"`

### <a name="specify-an-upload-condition"></a>Zadejte podmínku nahrávání

[OutputFileUploadOptions.UploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileuploadoptions.uploadcondition#Microsoft_Azure_Batch_OutputFileUploadOptions_UploadCondition) vlastnost umožňuje podmíněného odesílání výstupní soubory. Obvyklým scénářem je nahrávat jednu sadu souborů, pokud je úloha úspěšná a jinou sadu souborů, pokud se nezdaří. Můžete například nahrát podrobné soubory protokolů, jenom v případě, že úloha selže a ukončí se nenulový ukončovací kód. Podobně můžete nahrát výsledek soubory pouze v případě, že je úloha úspěšná, jak tyto soubory mohou být chybí nebo není úplná, pokud se úloha nezdaří.

Ukázka kódu výše nastaví **UploadCondition** vlastnost **TaskCompletion**. Toto nastavení určuje, zda je soubor k odeslání, jakmile úkoly dokončí, bez ohledu na hodnotu v něm ukončovací kód. 

`uploadCondition: OutputFileUploadCondition.TaskCompletion`

Další nastavení, najdete v článku [OutputFileUploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.outputfileuploadcondition) výčtu.

### <a name="disambiguate-files-with-the-same-name"></a>Odstranit nejednoznačnost soubory se stejným názvem

Úkoly v úloze může vytvořit soubory, které mají stejný název. Například `stdout.txt` a `stderr.txt` jsou vytvořeny pro každý úkol, který běží v rámci úlohy. Protože každá úloha se spustí v jeho vlastní kontextu, nemusíte tyto soubory dojít ke konfliktu v systému souborů uzlu. Ale při nahrávání souborů z více úloh na kontejner sdílené, musíte odstranit nejednoznačnost soubory se stejným názvem.

[OutputFileBlobContainerDestination.Path](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination.path#Microsoft_Azure_Batch_OutputFileBlobContainerDestination_Path) vlastnost určuje cílový objekt blob nebo virtuální adresář pro výstupní soubory. Můžete použít **cesta** vlastnost na název objektu blob nebo virtuální adresář tak, že jsou ve službě Azure Storage jednoznačně pojmenované výstupní soubory se stejným názvem. Pomocí ID úkolu v cestě je vhodný způsob k zajištění jedinečné názvy a snadno identifikovat soubory.

Pokud **FilePattern** je nastavena na výraz se zástupnými znaky, pak všechny soubory, které se shodují se vzorem se odešlou do virtuálního adresáře určeného **cesta** vlastnost. Například, pokud je kontejner `mycontainer`, úlohu ID je `mytask`, a je vzor souborů `..\std*.txt`, pak bude vypadat podobně jako absolutní identifikátory URI pro výstupní soubory ve službě Azure Storage:

```
https://myaccount.blob.core.windows.net/mycontainer/mytask/stderr.txt
https://myaccount.blob.core.windows.net/mycontainer/mytask/stdout.txt
```

Pokud **FilePattern** je nastavena na shodovat s názvem jeden soubor, což znamená, neobsahuje žádné zástupné znaky a potom hodnotu **cesta** vlastnost určuje název plně kvalifikovaný objektu blob. Pokud očekáváte, že ke konfliktu názvů s jeden soubor z více úloh, zadejte název virtuálního adresáře v rámci názvu souboru, který chcete odstranit nejednoznačnost těchto souborů. Například nastavit **cesta** vlastnost ID úkolu, oddělovací znak (obvykle dopředné lomítko) a název souboru:

`path: taskId + @"/output.txt"`

Absolutní identifikátory URI pro výstupní soubory pro sadu úloh bude vypadat podobně jako:

```
https://myaccount.blob.core.windows.net/mycontainer/task1/output.txt
https://myaccount.blob.core.windows.net/mycontainer/task2/output.txt
```

Další informace o virtuálních adresářů v Azure Storage najdete v tématu [seznam objektů BLOB v kontejneru](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container).


## <a name="diagnose-file-upload-errors"></a>Diagnostikovat chyby odesílání souborů

Pokud chcete nahrát výstupní soubory do úložiště Azure se nezdaří, pak úlohu přesune do **dokončeno** stavu a [TaskExecutionInformation.FailureInformation](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskexecutioninformation.failureinformation#Microsoft_Azure_Batch_TaskExecutionInformation_FailureInformation) je nastavena. Zkontrolujte **FailureInformation** vlastnosti k určení, co došlo k chybě. Zde je ukázka, k chybě, která proběhne nahrávání souborů, pokud nelze nalézt kontejner: 

```
Category: UserError
Code: FileUploadContainerNotFound
Message: One of the specified Azure container(s) was not found while attempting to upload an output file
```

Na každých nahrávání souborů Batch zapíše dva soubory protokolu ve výpočetním uzlu `fileuploadout.txt` a `fileuploaderr.txt`. Můžete zkontrolovat tyto soubory protokolu Další informace o konkrétní chyby. V případech, kde nahrávání souborů byla nikdy pokus, např. protože vlastní úloha nebylo možné spustit pak tyto soubory protokolu neexistují.

## <a name="diagnose-file-upload-performance"></a>Diagnostika výkon nahrávání souborů

`fileuploadout.txt` Protokoly průběhu odesílání souboru. Tento soubor Další informace o tom, jak dlouho trvá vaší nahrávání souborů můžete zkontrolovat. Mějte na paměti, že existuje celá řada přispívajících faktorů nahrát výkonu, včetně velikosti uzlu dalších aktivit na uzlu v době nahrávání, jestli je cílový kontejner ve stejné oblasti jako fondu Batch, kolik uzly nahráváte do stora ge účet ve stejnou dobu a tak dále.

## <a name="use-the-batch-service-api-with-the-batch-file-conventions-standard"></a>Rozhraní API služby Batch pomocí standardní konvence dávkového souboru

Můžete zachovat výstup úlohy s rozhraním API služby Batch, můžete pojmenovat cílový kontejner a objekty BLOB, ale chcete. Můžete také název je podle požadavků [dávkového souboru konvence standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). Standardní konvence souboru určuje názvy cílový kontejner a objektů blob v Azure Storage pro danou výstupní soubor založený na názvy úloh a úloh. Pokud používáte standard souboru konvence pro pojmenování výstupní soubory, pak jsou k dispozici pro zobrazení v výstupní soubory [portál Azure](https://portal.azure.com).

Pokud vyvíjíte v jazyce C#, můžete použít metody, které jsou součástí [konvence souboru Batch library pro .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files). V této knihovně pro vás vytvoří správně pojmenované kontejnerů a objektů blob cesty. Například můžete volat rozhraní API získat správný název kontejneru, založené na název úlohy:

```csharp
string containerName = job.OutputStorageContainerName();
```

Můžete použít [CloudJobExtensions.GetOutputStorageContainerUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files.cloudjobextensions.getoutputstoragecontainerurl) metoda vrátí adresu URL sdílený přístupový podpis (SAS), která se používá k zápisu do kontejneru. Potom můžete předat tuto SAS k [OutputFileBlobContainerDestination](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination) konstruktor.

Pokud vyvíjíte v jiném jazyce než v C#, musíte implementovat standard souboru konvence sami.

## <a name="code-sample"></a>Ukázka kódu

[PersistOutputs] [ github_persistoutputs] ukázkový projekt je jedním z [ukázky kódu Azure Batch] [ github_samples] na Githubu. Toto řešení sady Visual Studio ukazuje, jak pomocí klientské knihovny Batch pro .NET lze zachovat výstup úlohy do odolné úložiště. Pokud chcete spustit ukázku, postupujte takto:

1. Otevřete projekt ve **Visual Studio 2015 nebo novější**.
2. Přidat Batch a Storage **přihlašovací údaje účtu** k **AccountSettings.settings** v Microsoft.Azure.Batch.Samples.Common projektu.
3. **Sestavení** (ale není spuštěný) řešení. Pokud se zobrazí výzva, obnovení všech balíčků NuGet.
4. Použít portál Azure k nahrání [balíčku aplikace](batch-application-packages.md) pro **PersistOutputsTask**. Zahrnout `PersistOutputsTask.exe` a jeho závislá sestavení v balíčku .zip, nastavte na "PersistOutputsTask" ID aplikace a verze balíčku aplikace do "1.0".
5. **Spustit** (spustit) **PersistOutputs** projektu.
6. Po zobrazení výzvy vyberte technologie trvalost použít ke spuštění ukázky, zadejte **2** spustit ukázku pomocí rozhraní API služby Batch udržení výstup úlohy.
7. V případě potřeby ukázku spustit znovu, zadávání **3** udržení výstup s rozhraním API služby Batch a také název cílová cesta kontejnerů a objektů blob podle standardu názvů souborů.

## <a name="next-steps"></a>Další postup

- Další informace o zachování výstup úlohy s knihovnou souboru konvence pro rozhraní .NET najdete v tématu [zachovat úloh a úkolů data do služby Azure Storage pomocí knihovny Batch souboru konvence pro technologii .NET zachovat ](batch-task-output-file-conventions.md).
- Informace o dalších přístupy k zachování výstupní data ve službě Azure Batch, najdete v části [zachovat výstup úlohy a úkolů do služby Azure Storage](batch-task-output.md).

[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
