---
title: Začínáme s úložištěm Queue pomocí sady Visual Studio (projekty WebJob)
description: Jak začít používat úložiště Azure Queue v projektu webové úlohy po připojení k účtu úložiště pomocí připojených služeb sady Visual Studio.
services: storage
author: ghogen
manager: jillfra
ms.assetid: 5c3ef267-2a67-44e9-ab4a-1edd7015034f
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ffba203bafaf3837cd2d7fc1a6fd962a6926b186
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298746"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-webjob-projects"></a>Začínáme s Azure Queue Storage a připojenými službami sady Visual Studio (projekty WebJob)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Přehled
Tento článek popisuje, jak začít používat Azure Queue Storage v projektu webové úlohy Azure Visual Studio po vytvoření nebo odkazu na účet služby Azure Storage pomocí dialogového okna **Přidat připojené služby** v aplikaci Visual Studio. Když přidáte účet úložiště do projektu webové úlohy pomocí dialogového okna **Přidat připojené služby** sady Visual Studio, budou nainstalovány příslušné balíčky NuGet Azure Storage, do projektu jsou přidány příslušné odkazy .NET a připojovací řetězce pro účet úložiště se aktualizuje v souboru App. config.  

Tento článek obsahuje C# ukázky kódu, které ukazují, jak používat sadu Azure WebJobs SDK verze 1. x ve službě Azure Queue Storage.

Azure Queue Storage je služba pro ukládání velkého počtu zpráv, ke které můžete získat přístup z jakéhokoli místa na světě prostřednictvím ověřených volání s využitím protokolu HTTP nebo HTTPS. Zpráva s jednou frontou může mít velikost až 64 kB a jedna fronta můžete obsahovat miliony zpráv, až do dosažení celkové kapacity účtu úložiště. Další informace najdete v tématu Začínáme [s Azure Queue Storage pomocí rozhraní .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) . Další informace o ASP.NET najdete v tématu [ASP.NET](https://www.asp.net).

## <a name="how-to-trigger-a-function-when-a-queue-message-is-received"></a>Jak aktivovat funkci při přijetí zprávy fronty
Chcete-li napsat funkci, kterou Sada WebJobs SDK volá při přijetí zprávy fronty, použijte atribut **QueueTrigger** . Konstruktor atributu přebírá parametr řetězce, který určuje název fronty, která se má dotazovat. Pokud chcete zjistit, jak dynamicky nastavit název fronty, podívejte [se na postup nastavení možností konfigurace](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Zprávy ve frontě řetězců
V následujícím příkladu fronta obsahuje zprávu o řetězci, takže **QueueTrigger** se použije na řetězcový parametr s názvem **LogMessage –** , který obsahuje obsah zprávy fronty. Funkce [zapíše zprávu protokolu na řídicí panel](#how-to-write-logs).

```csharp
public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    logger.WriteLine(logMessage);
}
```

Kromě **řetězce**parametr může být bajtové pole, objekt **CloudQueueMessage** nebo POCO, který definujete.

### <a name="poco-plain-old-clr-objecthttpsenwikipediaorgwikiplain_old_clr_object-queue-messages"></a>Zprávy fronty POCO [(obyčejný starý objekt CLR](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object))
V následujícím příkladu zpráva ve frontě obsahuje JSON pro objekt **BlobInformation** , který obsahuje vlastnost **BLOB** . Sada SDK automaticky deserializace objekt.

```csharp
public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
{
    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
}
```

Sada SDK používá [balíček NuGet Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json) k serializaci a deserializaci zpráv. Pokud vytvoříte zprávy ve frontě v programu, který nepoužívá sadu WebJobs SDK, můžete napsat kód jako v následujícím příkladu, a vytvořit tak zprávu fronty POCO, kterou může sada SDK analyzovat.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

### <a name="async-functions"></a>Asynchronní funkce
Následující asynchronní funkce [zapíše protokol do řídicího panelu](#how-to-write-logs).

```csharp
public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    await logger.WriteLineAsync(logMessage);
}
```

Asynchronní funkce mohou mít [token zrušení](https://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken), jak je znázorněno v následujícím příkladu, který kopíruje objekt BLOB. (Vysvětlení zástupného textu **queueTrigger** naleznete v části [objekty blob](#how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message) .)

```csharp
public async static Task ProcessQueueMessageAsyncCancellationToken(
    [QueueTrigger("blobcopyqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
    CancellationToken token)
{
    await blobInput.CopyToAsync(blobOutput, 4096, token);
}
```

## <a name="types-the-queuetrigger-attribute-works-with"></a>Typy atributů QueueTrigger fungují s
**QueueTrigger** můžete použít s následujícími typy:

* **řetezce**
* Typ POCO serializovaný jako JSON
* **Byte []**
* **CloudQueueMessage**

## <a name="polling-algorithm"></a>Algoritmus cyklického dotazování
Sada SDK implementuje náhodný exponenciální algoritmus pro snížení účinku nečinnosti při cyklickém dotazování na poplatky za transakce úložiště.  Když se najde zpráva, sada SDK počká dvě sekundy a pak vyhledá jinou zprávu. Pokud se nenalezne žádná zpráva, počká přibližně čtyři sekundy, než to zkusí znovu. Po následném neúspěšném pokusu o získání zprávy fronty se doba čekání zvětšuje, dokud nedosáhne maximální čekací doby, která je ve výchozím nastavení jedna minuta. [Maximální doba čekání je konfigurovatelná](#how-to-set-configuration-options).

## <a name="multiple-instances"></a>Několik instancí
Pokud webová aplikace běží na více instancích, spouští se v každém počítači průběžné webové úlohy a každý počítač bude čekat na triggery a pokusí se spustit funkce. V některých scénářích to může vést k tomu, že některé funkce zpracovávají stejná data dvakrát, takže funkce by měly být idempotentní (zapsány tak, aby opakovaně volaly se stejnými vstupními daty, nevytvářejí duplicitní výsledky).  

## <a name="parallel-execution"></a>Paralelní provádění
Pokud máte více funkcí naslouchajících v různých frontách, sada SDK je při současném přijetí zpráv paralelně zavolá paralelně.

Totéž platí při přijetí více zpráv pro jednu frontu. Sada SDK ve výchozím nastavení získá dávku 16 zpráv fronty v čase a spustí funkci, která je zpracovává paralelně. [Velikost dávky je konfigurovatelná](#how-to-set-configuration-options). Když se zpracovávané číslo dostane do poloviny velikosti dávky, sada SDK získá další dávku a začne tyto zprávy zpracovávat. Proto je maximální počet souběžných zpráv zpracovávaných na funkci jedna a poloviční velikost dávky. Toto omezení se vztahuje odděleně na každou funkci s atributem **QueueTrigger** . Pokud nechcete paralelní spouštění pro zprávy přijaté v jedné frontě, nastavte velikost dávky na 1.

## <a name="get-queue-or-queue-message-metadata"></a>Získat metadata zprávy fronty nebo fronty
Následující vlastnosti zprávy můžete získat přidáním parametrů do signatury metody:

* ExpirationTime **DateTimeOffset**
* InsertionTime **DateTimeOffset**
* NextVisibleTime **DateTimeOffset**
* **String** queueTrigger (obsahuje text zprávy)
* ID **řetězce**
* popReceipt **řetězce**
* dequeueCount **int**

Pokud chcete pracovat přímo s rozhraním API služby Azure Storage, můžete také přidat parametr **CloudStorageAccount** .

V následujícím příkladu jsou všechna tato metadata zapsána do protokolu informační aplikace. V příkladu obě LogMessage – a queueTrigger obsahují obsah zprávy fronty.

```csharp
public static void WriteLog([QueueTrigger("logqueue")] string logMessage,
    DateTimeOffset expirationTime,
    DateTimeOffset insertionTime,
    DateTimeOffset nextVisibleTime,
    string id,
    string popReceipt,
    int dequeueCount,
    string queueTrigger,
    CloudStorageAccount cloudStorageAccount,
    TextWriter logger)
{
    logger.WriteLine(
        "logMessage={0}\n" +
        "expirationTime={1}\ninsertionTime={2}\n" +
        "nextVisibleTime={3}\n" +
        "id={4}\npopReceipt={5}\ndequeueCount={6}\n" +
        "queue endpoint={7} queueTrigger={8}",
        logMessage, expirationTime,
        insertionTime,
        nextVisibleTime, id,
        popReceipt, dequeueCount,
        cloudStorageAccount.QueueEndpoint,
        queueTrigger);
}
```

Tady je ukázkový protokol zapsaný vzorovým kódem:

        logMessage=Hello world!
        expirationTime=10/14/2014 10:31:04 PM +00:00
        insertionTime=10/7/2014 10:31:04 PM +00:00
        nextVisibleTime=10/7/2014 10:41:23 PM +00:00
        id=262e49cd-26d3-4303-ae88-33baf8796d91
        popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
        dequeueCount=1
        queue endpoint=https://contosoads.queue.core.windows.net/
        queueTrigger=Hello world!

## <a name="graceful-shutdown"></a>Řádné vypnutí
Funkce, která běží v průběžné úloze WebJob, může přijmout parametr **CancellationToken** , který umožňuje operačnímu systému upozornit funkci, když bude webová úloha ukončena. Toto oznámení můžete použít k tomu, abyste se ujistili, že funkce nekončí nečekaně způsobem, který opustí data v nekonzistentním stavu.

Následující příklad ukazuje, jak kontrolovat chystané ukončení úlohy WebJob ve funkci.

```csharp
public static void GracefulShutdownDemo(
            [QueueTrigger("inputqueue")] string inputText,
            TextWriter logger,
            CancellationToken token)
{
    for (int i = 0; i < 100; i++)
    {
        if (token.IsCancellationRequested)
        {
            logger.WriteLine("Function was cancelled at iteration {0}", i);
            break;
        }
        Thread.Sleep(1000);
        logger.WriteLine("Normal processing for queue message={0}", inputText);
    }
}
```

**Poznámka:** Řídicí panel nemusí správně zobrazovat stav a výstup funkcí, které byly vypnuty.

Další informace najdete v tématu [řádné vypnutí služby WebJobs](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR).   

## <a name="how-to-create-a-queue-message-while-processing-a-queue-message"></a>Vytvoření zprávy ve frontě při zpracování zprávy ve frontě
Chcete-li napsat funkci, která vytvoří novou zprávu fronty, použijte atribut **Queue** . Podobně jako **QueueTrigger**, předáváte název fronty jako řetězec, nebo můžete [název fronty nastavit dynamicky](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Zprávy ve frontě řetězců
Následující neasynchronní ukázka kódu vytvoří novou zprávu fronty ve frontě s názvem "funkce outputqueue" se stejným obsahem, jako je zpráva fronty přijatá ve frontě s názvem "InputQueue". (Pro asynchronní funkce použijte **IAsyncCollector @ no__t-1T >** , jak je uvedeno dále v této části.)

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    [Queue("outputqueue")] out string outputQueueMessage )
{
    outputQueueMessage = queueMessage;
}
```

### <a name="poco-plain-old-clr-objecthttpsenwikipediaorgwikiplain_old_clr_object-queue-messages"></a>Zprávy fronty POCO [(obyčejný starý objekt CLR](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object))
Chcete-li vytvořit zprávu fronty obsahující POCO místo řetězce, předejte typ POCO jako výstupní parametr do konstruktoru atributu **Queue** .

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
{
    blobInfoOutput = blobInfoInput;
}
```

Sada SDK automaticky serializace objektu do formátu JSON. Zpráva fronty je vždy vytvořena, a to i v případě, že objekt má hodnotu null.

### <a name="create-multiple-messages-or-in-async-functions"></a>Vytvoření více zpráv nebo v asynchronních funkcích
Chcete-li vytvořit více zpráv, nastavte typ parametru pro výstupní frontu **ICollector @ no__t-1T >** nebo **IAsyncCollector @ no__t-3T >** , jak je znázorněno v následujícím příkladu.

```csharp
public static void CreateQueueMessages(
    [QueueTrigger("inputqueue")] string queueMessage,
    [Queue("outputqueue")] ICollector<string> outputQueueMessage,
    TextWriter logger)
{
    logger.WriteLine("Creating 2 messages in outputqueue");
    outputQueueMessage.Add(queueMessage + "1");
    outputQueueMessage.Add(queueMessage + "2");
}
```

Každá zpráva fronty je vytvořena ihned při volání metody **Add** .

### <a name="types-that-the-queue-attribute-works-with"></a>Typy, ve kterých pracuje atribut Queue
Atribut **Queue** můžete použít u následujících typů parametrů:

* **výstupní řetězec** (vytvoří zprávu fronty, pokud hodnota parametru končí funkcí)
* **out Byte []** (funguje jako **řetězec**)
* **out CloudQueueMessage** (funguje jako **řetězec**)
* **out POCO** (serializovatelný typ vytvoří zprávu s objektem s hodnotou null, pokud je parametr null, když funkce skončí)
* **ICollector**
* **IAsyncCollector**
* **CloudQueue** (ruční vytváření zpráv pomocí rozhraní API Azure Storage přímo)

### <a name="use-webjobs-sdk-attributes-in-the-body-of-a-function"></a>Použití atributů sady WebJobs SDK v těle funkce
Pokud potřebujete před použitím atributu sady WebJobs SDK, jako je například **Queue**, **BLOB**nebo **Table**, udělat nějakou práci, můžete použít rozhraní **IBinder** .

Následující příklad přebírá zprávu vstupní fronty a vytvoří novou zprávu se stejným obsahem ve výstupní frontě. Název výstupní fronty je nastaven podle kódu v těle funkce.

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
}
```

Rozhraní **IBinder** lze také použít s atributy **Table** a **BLOB** .

## <a name="how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message"></a>Čtení a zápis objektů BLOB a tabulek během zpracování zprávy ve frontě
Atributy **objektu BLOB** a **tabulky** umožňují čtení a zápis objektů BLOB a tabulek. Ukázky v této části se vztahují na objekty blob. Ukázky kódu, které ukazují, jak aktivovat procesy při vytváření nebo aktualizaci objektů blob, najdete v tématu [Jak používat úložiště objektů BLOB v Azure se sadou WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).
<!-- , and for code samples that read and write tables, see [How to use Azure table storage with the WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-storage-tables-how-to.md). -->

### <a name="string-queue-messages-triggering-blob-operations"></a>Zprávy fronty řetězců spouštějící operace objektů BLOB
U zprávy ve frontě, která obsahuje řetězec, **queueTrigger** je zástupný symbol, který můžete použít v parametru **Blobpath cestou** atributu **objektu BLOB** , který obsahuje obsah zprávy.

Následující příklad používá objekty **Stream** ke čtení a zápisu objektů BLOB. Zpráva fronty je název objektu BLOB umístěný v kontejneru textblobs. Kopie objektu BLOB s názvem-New připojeného k názvu se vytvoří ve stejném kontejneru.

```csharp
public static void ProcessQueueMessage(
    [QueueTrigger("blobcopyqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

Konstruktor atributu **BLOB** přebírá parametr **blobpath cestou** , který určuje kontejner a název objektu BLOB. Další informace o tomto zástupném symbolu najdete v tématu [Jak používat úložiště objektů BLOB v Azure se sadou WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

Když atribut upraví objekt **datového proudu** , parametr jiného konstruktoru určuje režim **FileAccess** jako čtení, zápis nebo čtení a zápis.

Následující příklad používá objekt **CloudBlockBlob** k odstranění objektu BLOB. Zpráva fronty je název objektu BLOB.

```csharp
public static void DeleteBlob(
    [QueueTrigger("deleteblobqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
{
    blobToDelete.Delete();
}
```

### <a name="poco-plain-old-clr-objecthttpsenwikipediaorgwikiplain_old_clr_object-queue-messages"></a>Zprávy fronty POCO [(obyčejný starý objekt CLR](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object))
Pro POCO uložený jako JSON ve zprávě fronty můžete použít zástupné symboly, které vlastnosti názvu objektu v parametru **blobpath cestou** atributu **Queue** . Můžete také použít názvy vlastností Queue metadata jako zástupné symboly. Viz [získat metadata zprávy fronty nebo fronty](#get-queue-or-queue-message-metadata).

Následující příklad zkopíruje objekt blob do nového objektu BLOB s jinou příponou. Zpráva fronty je objekt **BlobInformation** , který obsahuje vlastnosti **BLOB** a **BlobNameWithoutExtension** . Názvy vlastností se používají jako zástupné symboly v cestě objektů BLOB pro atributy **objektu BLOB** .

```csharp
public static void CopyBlobPOCO(
    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

Sada SDK používá [balíček NuGet Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json) k serializaci a deserializaci zpráv. Pokud vytvoříte zprávy ve frontě v programu, který nepoužívá sadu WebJobs SDK, můžete napsat kód jako v následujícím příkladu, a vytvořit tak zprávu fronty POCO, kterou může sada SDK analyzovat.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

Pokud potřebujete před navázáním objektu BLOB na objekt udělat nějakou práci, můžete použít atribut v těle funkce, jak je znázorněno v části [použití atributů sady SDK WebJobs v těle funkce](#use-webjobs-sdk-attributes-in-the-body-of-a-function).

### <a name="types-you-can-use-the-blob-attribute-with"></a>Typy, ve kterých můžete použít atribut objektu BLOB
Atribut **objektu BLOB** lze použít s následujícími typy:

* **Stream** (čtení nebo zápis, zadaný pomocí parametru konstruktoru FileAccess)
* **Elementu**
* **TextWriter**
* **řetězec** (čtení)
* **výstupní řetězec** (zápis; vytvoří objekt BLOB pouze v případě, že parametr řetězce má hodnotu null, když se funkce vrátí)
* POCO (čtení)
* out POCO (zápis; vždy vytvoří objekt blob, vytvoří se jako objekt s hodnotou null, pokud parametr POCO má hodnotu null, když se funkce vrátí)
* **CloudBlobStream** (zápis)
* **ICloudBlob** (čtení nebo zápis)
* **CloudBlockBlob** (čtení nebo zápis)
* **CloudPageBlob** (čtení nebo zápis)

## <a name="how-to-handle-poison-messages"></a>Postup zpracování poškozených zpráv
Zprávy, jejichž obsah způsobuje selhání funkce, se nazývají *poškozené zprávy*. Když dojde k selhání této funkce, zpráva fronty se neodstraní a nakonec se znovu vybere, což způsobí, že se cyklus opakuje. Sada SDK může cykly automaticky přerušit po omezeném počtu iterací, nebo můžete to provést ručně.

### <a name="automatic-poison-message-handling"></a>Automatické zpracování nepoškozených zpráv
Sada SDK zavolá funkci až pětkrát, aby zpracovala zprávu fronty. Pokud se pátý pokus nezdařil, zpráva bude přesunuta do fronty nepoškozeného. Můžete si prohlédnout, jak nakonfigurovat maximální počet opakování při [nastavování možností konfigurace](#how-to-set-configuration-options).

Fronta poškození má název *{originalqueuename}* – jed. Můžete napsat funkci pro zpracování zpráv z fronty poškození tím, že je přihlásíte nebo posíláte oznámení, že je potřeba ruční pozornost.

V následujícím příkladu se funkce **CopyBlob** nezdaří, pokud zpráva fronty obsahuje název objektu blob, který neexistuje. Pokud k tomu dojde, bude zpráva přesunuta z fronty copyblobqueue do fronty copyblobqueue-otrav. **ProcessPoisonMessage** pak zaznamená poškozenou zprávu.

```csharp
public static void CopyBlob(
    [QueueTrigger("copyblobqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}

public static void ProcessPoisonMessage(
    [QueueTrigger("copyblobqueue-poison")] string blobName, TextWriter logger)
{
    logger.WriteLine("Failed to copy blob, name=" + blobName);
}
```

Následující ilustrace znázorňuje výstup z těchto funkcí v případě, že je zpracována nezpracovatelná zpráva.

![Výstup konzoly pro zpracování nezpracovatelných zpráv](./media/vs-storage-webjobs-getting-started-queues/poison.png)

### <a name="manual-poison-message-handling"></a>Manipulace s ručními nepoškozenými zprávami
Počet vyzvednutí zprávy pro zpracování můžete získat tak, že do své funkce přidáte parametr **int** s názvem **dequeueCount** . Pak můžete zjistit počet vyřazení z fronty v kódu funkce a provést vlastní zpracování nezpracovatelných zpráv, když číslo překročí prahovou hodnotu, jak je znázorněno v následujícím příkladu.

```csharp
public static void CopyBlob(
    [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput,
    TextWriter logger)
{
    if (dequeueCount > 3)
    {
        logger.WriteLine("Failed to copy blob, name=" + blobName);
    }
    else
    {
        blobInput.CopyTo(blobOutput, 4096);
    }
}
```

## <a name="how-to-set-configuration-options"></a>Nastavení možností konfigurace
Pomocí typu **JobHostConfiguration** můžete nastavit následující možnosti konfigurace:

* Nastavte připojovací řetězce sady SDK v kódu.
* Nakonfigurujte nastavení **QueueTrigger** , jako je třeba maximální počet vyřazování z fronty.
* Získá názvy front z konfigurace.

### <a name="set-sdk-connection-strings-in-code"></a>Nastavení připojovacích řetězců sady SDK v kódu
Nastavení připojovacích řetězců sady SDK v kódu umožňuje používat vlastní názvy připojovacích řetězců v konfiguračních souborech nebo proměnných prostředí, jak je znázorněno v následujícím příkladu.

```csharp
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    var _dashboardConn = ConfigurationManager
        .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    var _serviceBusConn = ConfigurationManager
        .ConnectionStrings["MyServiceBusConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    config.DashboardConnectionString = _dashboardConn;
    config.ServiceBusConnectionString = _serviceBusConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="configure-queuetrigger--settings"></a>Konfigurace nastavení QueueTrigger
Můžete nakonfigurovat následující nastavení, která se vztahují na zpracování zprávy ve frontě:

* Maximální počet zpráv ve frontě, které jsou současně spouštěny souběžně (výchozí hodnota je 16).
* Maximální počet opakovaných pokusů, než se zpráva fronty pošle do fronty poškození (výchozí hodnota je 5).
* Maximální doba čekání před opětovným dotazem, když je fronta prázdná (výchozí hodnota je 1 minuta).

Následující příklad ukazuje, jak nakonfigurovat tato nastavení:

```csharp
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.Queues.BatchSize = 8;
    config.Queues.MaxDequeueCount = 4;
    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="set-values-for-webjobs-sdk-constructor-parameters-in-code"></a>Nastavení hodnot pro parametry konstruktoru sady WebJobs SDK v kódu
Někdy je třeba zadat název fronty, název objektu BLOB nebo kontejner nebo název tabulky v kódu, nikoli jako pevný kód. Například můžete chtít zadat název fronty pro **QueueTrigger** v konfiguračním souboru nebo v proměnné prostředí.

To lze provést předáním objektu **NameResolver** do typu **JobHostConfiguration** . Zahrnete speciální zástupné symboly, které jsou obklopeny procenty (%) v části parametry konstruktoru atributu WebJobs SDK a váš **NameResolver** kód určuje skutečné hodnoty, které budou použity místo těchto zástupných symbolů.

Předpokládejme například, že chcete použít frontu s názvem logqueuetest v testovacím prostředí a jednu s názvem logqueueprod v produkčním prostředí. Místo pevně zakódovaného názvu fronty chcete zadat název položky v kolekci **appSettings** , která by měla mít skutečný název fronty. Pokud je klíč **appSettings** logqueue, může funkce vypadat jako v následujícím příkladu.

```csharp
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Vaše třída **NameResolver** pak může získat název fronty z **appSettings** , jak je znázorněno v následujícím příkladu:

```csharp
public class QueueNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

Třídu **NameResolver** předáte do objektu **JobHost** , jak je znázorněno v následujícím příkladu.

```csharp
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new QueueNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

**Poznámka:** Názvy front, tabulek a objektů BLOB jsou vyřešeny pokaždé, když je zavolána funkce, ale názvy kontejnerů objektů BLOB jsou vyřešeny pouze při spuštění aplikace. Když je úloha spuštěná, nemůžete změnit název kontejneru objektů BLOB.

## <a name="how-to-trigger-a-function-manually"></a>Ruční aktivace funkce
Chcete-li funkci aktivovat ručně, použijte metodu **Call** nebo **CallAsync** objektu **JobHost** a atribut **NoAutomaticTrigger** pro funkci, jak je znázorněno v následujícím příkladu.

```csharp
public class Program
{
    static void Main(string[] args)
    {
        JobHost host = new JobHost();
        host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
    }

    [NoAutomaticTrigger]
    public static void CreateQueueMessage(
        TextWriter logger,
        string value,
        [Queue("outputqueue")] out string message)
    {
        message = value;
        logger.WriteLine("Creating queue message: ", message);
    }
}
```

## <a name="how-to-write-logs"></a>Zápis protokolů
Řídicí panel zobrazuje protokoly na dvou místech: na stránce webové úlohy a na stránce pro konkrétní volání webové úlohy.

![Stránka přihlášení na webové stránce](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

![Stránka pro vyvolání funkce log](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

Výstup z metod konzoly, které voláte ve funkci nebo v metodě **Main ()** , se zobrazí na stránce řídicí panel pro webovou úlohu, nikoli na stránce pro konkrétní volání metody. Výstup z objektu TextWriter, který získáte z parametru v podpisu metody, se zobrazí na stránce řídicího panelu pro vyvolání metody.

Výstup konzoly nelze propojit s konkrétním voláním metody, protože konzola je tvořena jedním vláknem, zatímco mnoho pracovních funkcí může být spuštěno ve stejnou dobu. Proto sada SDK poskytuje jednotlivá volání funkcí s vlastním jedinečným objektem zapisovače protokolů.

Chcete-li zapisovat [protokoly trasování aplikace](../app-service/troubleshoot-dotnet-visual-studio.md#logsoverview), použijte **konzolu. out** (vytvoří protokoly označené jako info) a **Console. Error** (vytvoří protokoly označené jako chyba). Alternativou je použití [Trace nebo TraceSource](https://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx), které kromě informací a chyb poskytuje podrobné, varovné a kritické úrovně. Protokoly trasování aplikace se zobrazí v souborech protokolu webové aplikace, v tabulkách Azure nebo v objektech blob Azure v závislosti na konfiguraci webové aplikace Azure. Vzhledem k tomu, že se jedná o všechny výstupy konzoly, se na stránce řídicí panel pro webovou úlohu zobrazí také nejnovější protokoly aplikací 100, nikoli stránka pro vyvolání funkce.

Výstup konzoly se zobrazí na řídicím panelu jenom v případě, že je program spuštěný ve webové úloze Azure, a ne v případě, že je program spuštěný místně nebo v některém jiném prostředí.

Protokolování můžete zakázat nastavením připojovacího řetězce řídicího panelu na hodnotu null. Další informace najdete v tématu [Nastavení možností konfigurace](#how-to-set-configuration-options).

Následující příklad ukazuje několik způsobů, jak zapisovat protokoly:

```csharp
public static void WriteLog(
    [QueueTrigger("logqueue")] string logMessage,
    TextWriter logger)
{
    Console.WriteLine("Console.Write - " + logMessage);
    Console.Out.WriteLine("Console.Out - " + logMessage);
    Console.Error.WriteLine("Console.Error - " + logMessage);
    logger.WriteLine("TextWriter - " + logMessage);
}
```

V řídicím panelu sady WebJobs se zobrazí výstup objektu **TextWriter** , když přejdete na stránku pro konkrétní vyvolání funkce a vyberete **přepínač výstup**:

![Odkaz na vyvolání](./media/vs-storage-webjobs-getting-started-queues/dashboardinvocations.png)

![Stránka pro vyvolání funkce log](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

V řídicím panelu SDK WebJobs se při přechodu na stránku webové úlohy (ne pro vyvolání funkce) zobrazí poslední 100 řádky výstupu konzoly. Vyberte možnost **přepnout výstup**.

![Přepnout výstup](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

V nepřetržité úloze WebJob se protokoly aplikací zobrazí v/data/Jobs/Continuous/ *{webjobname}* /job_log.txt v systému souborů webové aplikace.

        [09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
        [09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
        [09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

V objektech blob v Azure budou protokoly aplikací vypadat takto: 2014-09-26T21:01:13, informace, contosoadsnew, 491e54, 635473620738373502, 0, 17404, 17, Console. Write-Hello World!, 2014-09-26T21:01:13, Error, contosoadsnew, 491e54, 635473620738373502, 0, 17404, 19, Console. Error-Hello World!, 2014-09-26T21:01:13, informace, contosoadsnew, 491e54, 635473620738529920, 0, 17404, 17, Console. out-Hello World!,

A v tabulce Azure **konzole. out** a **Console. Errors** vypadají takto:

![Informační log v tabulce](./media/vs-storage-webjobs-getting-started-queues/tableinfo.png)

![Protokol chyb v tabulce](./media/vs-storage-webjobs-getting-started-queues/tableerror.png)

## <a name="next-steps"></a>Další kroky
V tomto článku jsou uvedené ukázky kódu, které ukazují, jak zpracovávat běžné scénáře pro práci s frontami Azure. Další informace o tom, jak používat Azure WebJobs a sadu WebJobs SDK, najdete v [dokumentaci k prostředkům Azure WebJobs](https://go.microsoft.com/fwlink/?linkid=390226).

