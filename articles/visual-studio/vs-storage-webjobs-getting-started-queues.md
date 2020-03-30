---
title: Začínáme s úložištěm front pomocí sady Visual Studio (projekty WebJob)
description: Jak začít používat úložiště Fronty Azure v projektu WebJob po připojení k účtu úložiště pomocí připojených služeb Visual Studia.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298746"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-webjob-projects"></a>Začínáme s úložištěm fronty Azure a připojenými službami Visual Studia (Projekty webových úloh)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Přehled
Tento článek popisuje, jak začít používat úložiště fronty Azure v projektu Visual Studio Azure WebJob po vytvoření nebo odkazování na účet úložiště Azure pomocí dialogového okna **Přidat připojené služby** Visual Studia. Když přidáte účet úložiště do projektu WebJob pomocí dialogového okna **Přidat připojené služby** sady Visual Studio, najdou se příslušné balíčky Azure Storage NuGet, do projektu se přidají příslušné odkazy .NET a v souboru App.config se aktualizují připojovací řetězce pro účet úložiště.  

Tento článek obsahuje ukázky kódu jazyka C#, které ukazují, jak používat Azure WebJobs SDK verze 1.x se službou úložiště Fronty Azure.

Azure Queue Storage je služba pro ukládání velkého počtu zpráv, ke které můžete získat přístup z jakéhokoli místa na světě prostřednictvím ověřených volání s využitím protokolu HTTP nebo HTTPS. Zpráva s jednou frontou může mít velikost až 64 kB a jedna fronta můžete obsahovat miliony zpráv, až do dosažení celkové kapacity účtu úložiště. Další informace [najdete v tématu Začínáme s azure queue storage pomocí rozhraní .NET.](../storage/queues/storage-dotnet-how-to-use-queues.md) Další informace o ASP.NET naleznete v [tématu ASP.NET](https://www.asp.net).

## <a name="how-to-trigger-a-function-when-a-queue-message-is-received"></a>Jak spustit funkci při přijetí zprávy fronty
Chcete-li napsat funkci, kterou sada WebJobs SDK volá při přijetí zprávy fronty, použijte atribut **QueueTrigger.** Konstruktor atributu přebírá parametr řetězce, který určuje název fronty k dotazování. Chcete-li zjistit, jak dynamicky nastavit název fronty, podívejte [se na informace o nastavení možností konfigurace](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Zprávy fronty řetězců
V následujícím příkladu fronta obsahuje zprávu řetězce, takže **QueueTrigger** se použije na parametr řetězce s názvem **logMessage,** který obsahuje obsah zprávy fronty. Funkce [zapíše zprávu protokolu na řídicí panel](#how-to-write-logs).

```csharp
public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    logger.WriteLine(logMessage);
}
```

Kromě **řetězce**může být parametr emitovat pole bajtů, objekt **CloudQueueMessage** nebo POCO, který definujete.

### <a name="poco-plain-old-clr-object-queue-messages"></a>Zprávy fronty POCO [(Prostý starý objekt CLR)](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)
V následujícím příkladu zpráva fronty obsahuje JSON pro **objekt BlobInformation,** který obsahuje vlastnost **BlobName.** Sada SDK objekt automaticky rekonstruuje.

```csharp
public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
{
    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
}
```

Sada SDK používá [newtonsoft.Json NuGet balíček](https://www.nuget.org/packages/Newtonsoft.Json) serializovat a rekonstruovat zprávy. Pokud vytvoříte zprávy fronty v programu, který nepoužívá webjobs SDK, můžete napsat kód jako v následujícím příkladu k vytvoření zprávy fronty POCO, kterou může sada SDK analyzovat.

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

Asynchronní funkce může mít [token zrušení](https://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken), jak je znázorněno v následujícím příkladu, který zkopíruje objekt blob. (Vysvětlení zástupného symbolu **fronttrigger** upozorňují na část [Objekty blob.)](#how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message)

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

## <a name="types-the-queuetrigger-attribute-works-with"></a>Zadá atribut QueueTrigger, se kterým pracuje.
**FrontTrigger** můžete použít s následujícími typy:

* **Řetězec**
* Typ POCO serializovaný jako JSON
* **bajt[]**
* **CloudQueueMessage**

## <a name="polling-algorithm"></a>Algoritmus dotazování
Sada SDK implementuje algoritmus náhodného exponenciálního back-off, aby se snížil vliv dotazování fronty nečinnosti na náklady na transakce úložiště.  Při nalezení zprávy sada SDK čeká dvě sekundy a potom zkontroluje další zprávu; Pokud není nalezena žádná zpráva, čeká asi čtyři sekundy, než se pokusí znovu. Po následných neúspěšných pokusech o získání zprávy fronty se čekací doba nadále zvyšuje, dokud nedosáhne maximální čekací doby, která je výchozí na jednu minutu. [Maximální čekací doba je konfigurovatelná](#how-to-set-configuration-options).

## <a name="multiple-instances"></a>Několik instancí
Pokud vaše webová aplikace běží na více instancích, kontinuální WebJobs běží na každém počítači a každý počítač bude čekat na aktivační události a pokus o spuštění funkcí. V některých případech to může vést k některé funkce zpracování stejných dat dvakrát, takže funkce by měly být idempotentní (napsaný tak, aby jejich opakované volání se stejnými vstupními daty nevytváří duplicitní výsledky).  

## <a name="parallel-execution"></a>Paralelní provádění
Pokud máte více funkcí naslouchání v různých frontách, sada SDK je bude volat paralelně při současném přijetí zpráv.

Totéž platí, pokud je přijato více zpráv pro jednu frontu. Ve výchozím nastavení sada SDK získá dávku zpráv fronty 16 najednou a spustí funkci, která je zpracovává paralelně. [Velikost dávky je konfigurovatelná](#how-to-set-configuration-options). Když číslo, které jsou zpracovávány, se zmenší na polovinu velikosti dávky, sada SDK získá další dávku a začne tyto zprávy zpracovávat. Proto je maximální počet souběžných zpráv zpracovávaných na funkci jeden a půl násobek velikosti dávky. Toto omezení platí samostatně pro každou funkci, která má atribut **QueueTrigger.** Pokud nechcete paralelní spuštění pro zprávy přijaté v jedné frontě, nastavte velikost dávky na 1.

## <a name="get-queue-or-queue-message-metadata"></a>Získat metadata zprávy fronty nebo fronty
Přidáním parametrů do podpisu metody můžete získat následující vlastnosti zprávy:

* **DateTimeOffset** expiraceČas
* **DatumTimeOffset** insertionTime
* **DateTimeOffset** nextVisibleTime
* **řetězec** queueTrigger (obsahuje text zprávy)
* id **řetězce**
* **řetězec** popReceipt
* **int** dequeueCount

Pokud chcete pracovat přímo s rozhraním API úložiště Azure, můžete také přidat parametr **CloudStorageAccount.**

Následující příklad zapíše všechna tato metadata do protokolu aplikace INFO. V příkladu logMessage a queueTrigger obsahují obsah zprávy fronty.

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

Zde je ukázkový protokol napsaný ukázkovým kódem:

        logMessage=Hello world!
        expirationTime=10/14/2014 10:31:04 PM +00:00
        insertionTime=10/7/2014 10:31:04 PM +00:00
        nextVisibleTime=10/7/2014 10:41:23 PM +00:00
        id=262e49cd-26d3-4303-ae88-33baf8796d91
        popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
        dequeueCount=1
        queue endpoint=https://contosoads.queue.core.windows.net/
        queueTrigger=Hello world!

## <a name="graceful-shutdown"></a>Bezproblémové vypnutí
Funkce, která běží v průběžné webjob může přijmout **CancellationToken** parametr, který umožňuje operačnímu systému upozornit na funkci, když WebJob se chystá být ukončena. Toto oznámení můžete použít k ujistěte se, že funkce neukončí neočekávaně způsobem, který ponechává data v nekonzistentním stavu.

Následující příklad ukazuje, jak zkontrolovat blížící se ukončení webjob ve funkci.

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

**Poznámka:** Řídicí panel nemusí správně zobrazit stav a výstup funkcí, které byly vypnuty.

Další informace naleznete v [tématu WebJobs Graceful Shutdown](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR).   

## <a name="how-to-create-a-queue-message-while-processing-a-queue-message"></a>Jak vytvořit zprávu fronty při zpracování zprávy fronty
Chcete-li napsat funkci, která vytvoří novou zprávu fronty, použijte atribut **Queue.** Podobně jako **QueueTrigger**předáte název fronty jako řetězec nebo můžete [nastavit název fronty dynamicky](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Zprávy fronty řetězců
Následující ukázka neasynchronního kódu vytvoří novou zprávu fronty ve frontě s názvem "outputqueue" se stejným obsahem jako zpráva fronty přijatá ve frontě s názvem "inputqueue". (Pro asynchronní funkce použijte **IAsyncCollector\<T>** jak je znázorněno dále v této části.)

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    [Queue("outputqueue")] out string outputQueueMessage )
{
    outputQueueMessage = queueMessage;
}
```

### <a name="poco-plain-old-clr-object-queue-messages"></a>Zprávy fronty POCO [(Prostý starý objekt CLR)](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)
Chcete-li vytvořit zprávu fronty, která obsahuje poco spíše než řetězec, předajte typ POCO jako výstupní parametr konstruktoru **atributu Queue.**

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
{
    blobInfoOutput = blobInfoInput;
}
```

Sada SDK automaticky serializuje objekt do aplikace JSON. Zpráva fronty je vždy vytvořena, i v případě, že objekt je null.

### <a name="create-multiple-messages-or-in-async-functions"></a>Vytvoření více zpráv nebo v asynchronních funkcích
Chcete-li vytvořit více zpráv, vytvořte typ parametru pro výstupní frontu **ICollector\<T>** nebo **IAsyncCollector\<T>**, jak je znázorněno v následujícím příkladu.

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

Každá zpráva fronty je vytvořena okamžitě **při** add metoda je volána.

### <a name="types-that-the-queue-attribute-works-with"></a>Typy, se kterými atribut Queue pracuje
Atribut **Queue** můžete použít u následujících typů parametrů:

* **out řetězec** (vytvoří zprávu fronty, pokud hodnota parametru není null, když funkce končí)
* **out byte[]** (funguje jako **řetězec**)
* **out CloudQueueMessage** (funguje jako **řetězec**)
* **out POCO** (serializovatelný typ, vytvoří zprávu s nulovým objektem, pokud je parametr nulový, když funkce končí)
* **ISběratel**
* **Kolekce IAsyncCollector**
* **CloudQueue** (pro ruční vytváření zpráv pomocí rozhraní AZURE Storage API přímo)

### <a name="use-webjobs-sdk-attributes-in-the-body-of-a-function"></a>Použití atributů sady WebJobs SDK v těle funkce
Pokud potřebujete provést nějakou práci ve funkci před použitím atributu WebJobs SDK, jako je **fronta**, **blob**nebo **tabulka**, můžete použít rozhraní **IBinder.**

Následující příklad přebírá zprávu vstupní fronty a vytvoří novou zprávu se stejným obsahem ve výstupní frontě. Název výstupní fronty je nastaven kódem v těle funkce.

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

**Rozhraní IBinder** lze také použít s **table** a **blob** atributy.

## <a name="how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message"></a>Jak číst a psát objekty BLOB a tabulky při zpracování zprávy fronty
Atributy **objektů Blob** a **tabulka** umožňují číst a zapisovat objekty BLOB a tabulky. Ukázky v této části platí pro objekty BLOB. Ukázky kódu, které ukazují, jak aktivovat procesy při vytváření nebo aktualizaci objektů BLOB, najdete v tématu [Jak používat úložiště objektů blob Azure s sadou WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).
<!-- , and for code samples that read and write tables, see [How to use Azure table storage with the WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-storage-tables-how-to.md). -->

### <a name="string-queue-messages-triggering-blob-operations"></a>Zprávy fronty řetězců spouštějící operace objektů blob
Pro zprávu fronty, která obsahuje řetězec, **queueTrigger** je zástupný symbol, který můžete použít v **objektu blob atributu blobPath** parametr, který obsahuje obsah zprávy. **Blob**

Následující příklad používá **Stream** objekty ke čtení a zápisu objektů BLOB. Zpráva fronty je název objektu blob umístěného v kontejneru textblobs. Kopie objektu blob s "-new" připojené k názvu je vytvořen ve stejném kontejneru.

```csharp
public static void ProcessQueueMessage(
    [QueueTrigger("blobcopyqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

Konstruktor atributu **Blob** přebírá parametr **blobPath,** který určuje název kontejneru a objektu blob. Další informace o tomto zástupném symbolu najdete [v tématu Jak používat úložiště objektů blob Azure s sadou WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

Když atribut zdobí **Stream** objekt, jiný parametr konstruktoru určuje režim **FileAccess** jako čtení, zápis nebo čtení/zápis.

Následující příklad používá **Objekt CloudBlockBlob** k odstranění objektu blob. Zpráva fronty je název objektu blob.

```csharp
public static void DeleteBlob(
    [QueueTrigger("deleteblobqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
{
    blobToDelete.Delete();
}
```

### <a name="poco-plain-old-clr-object-queue-messages"></a>Zprávy fronty POCO [(Prostý starý objekt CLR)](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)
Pro poco uložené jako JSON ve zprávě fronty, můžete použít zástupné symboly, které název vlastnosti objektu v **atributu Queue** **blobPath** parametr. Jako zástupné symboly můžete také použít názvy vlastností metadat fronty. Viz [Získání metadat zprávy fronty nebo fronty](#get-queue-or-queue-message-metadata).

Následující příklad zkopíruje objekt blob do nového objektu blob s jiným rozšířením. Zpráva fronty je objekt **BlobInformation,** který obsahuje **vlastnosti BlobName** a **BlobNameWithoutExtension.** Názvy vlastností se používají jako zástupné symboly v cestě objektu blob pro atributy **objektu Blob.**

```csharp
public static void CopyBlobPOCO(
    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

Sada SDK používá [newtonsoft.Json NuGet balíček](https://www.nuget.org/packages/Newtonsoft.Json) serializovat a rekonstruovat zprávy. Pokud vytvoříte zprávy fronty v programu, který nepoužívá webjobs SDK, můžete napsat kód jako v následujícím příkladu k vytvoření zprávy fronty POCO, kterou může sada SDK analyzovat.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

Pokud potřebujete provést nějakou práci ve funkci před vazbou objektu blob na objekt, můžete použít atribut v těle funkce, jak je znázorněno v [použití atributů WebJobs SDK v těle funkce](#use-webjobs-sdk-attributes-in-the-body-of-a-function).

### <a name="types-you-can-use-the-blob-attribute-with"></a>Typy, se kterými můžete použít atribut Objekt blob
Atribut **Blob** lze použít s následujícími typy:

* **Stream** (čtení nebo zápis, určené pomocí parametru konstruktoru FileAccess)
* **Textreader**
* **TextWriter**
* **řetězec** (čtení)
* **out řetězec** (zápis; vytvoří objekt blob pouze v případě, že parametr řetězce je non-null, když funkce vrátí)
* POCO (číst)
* out POCO (zápis; vždy vytvoří objekt blob, vytvoří jako nulový objekt, pokud parametr POCO je null, když funkce vrátí)
* **CloudBlobStream** (zápis)
* **ICloudBlob** (čtení nebo zápis)
* **CloudBlockBlob** (číst nebo psát)
* **CloudPageBlob** (čtení nebo zápis)

## <a name="how-to-handle-poison-messages"></a>Jak zacházet s otrávenými zprávami
Zprávy, jejichž obsah způsobí selhání funkce se nazývají *poškozená zprávy*. Pokud funkce selže, zpráva fronty není odstraněna a nakonec je znovu zvedl, což způsobuje opakování cyklu. Sada SDK může automaticky přerušit cyklus po omezeném počtu iterací, nebo to můžete udělat ručně.

### <a name="automatic-poison-message-handling"></a>Automatické zpracování poškozené zprávy
Sada SDK zavolá funkci až pětkrát ke zpracování zprávy fronty. Pokud pátý pokus selže, zpráva je přesunuta do fronty jedovatý. Jak nakonfigurovat maximální počet opakování v [tématu Jak nastavit možnosti konfigurace](#how-to-set-configuration-options).

Poškozená fronta má název *{originalqueuename}*-poison. Můžete napsat funkci pro zpracování zpráv z fronty jed jejich protokolováním nebo odesláním oznámení, že je nutná ruční pozornost.

V následujícím příkladu funkce **CopyBlob** selže, pokud zpráva fronty obsahuje název objektu blob, který neexistuje. V takovém případě je zpráva přesunuta z fronty copyblobqueue do fronty copyblobqueue poison. **ProcessPoisonMessage** pak zaznamená poškozenou zprávu.

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

Následující obrázek znázorňuje výstup konzoly z těchto funkcí při zpracování poškozená zpráva.

![Výstup konzoly pro zpracování poškozená zpráva](./media/vs-storage-webjobs-getting-started-queues/poison.png)

### <a name="manual-poison-message-handling"></a>Ruční zpracování poškozená zpráva
Můžete získat počet, kolikrát byla zpráva zvedl ke zpracování přidáním **int** parametr s názvem **dequeueCount** do vaší funkce. Potom můžete zkontrolovat počet vyřazení fronty v kódu funkce a provést vlastní zpracování poškozená zpráva, když číslo překročí prahovou hodnotu, jak je znázorněno v následujícím příkladu.

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

## <a name="how-to-set-configuration-options"></a>Jak nastavit možnosti konfigurace
Pomocí typu **JobHostConfiguration** můžete nastavit následující možnosti konfigurace:

* Nastavte připojovací řetězce sady SDK v kódu.
* Nakonfigurujte nastavení **triggeru fronty,** například maximální počet vyřazení z fronty.
* Získejte názvy front z konfigurace.

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

### <a name="configure-queuetrigger--settings"></a>Konfigurovat nastavení triggeru fronty
Můžete nakonfigurovat následující nastavení, která platí pro zpracování zpráv fronty:

* Maximální počet zpráv fronty, které jsou současně zachyceny, aby byly provedeny paralelně (výchozí hodnota je 16).
* Maximální počet opakování před odesláním zprávy fronty do fronty poison (výchozí hodnota je 5).
* Maximální čekací doba před dotazováníznovu, když je fronta prázdná (výchozí hodnota je 1 minuta).

Následující příklad ukazuje, jak konfigurovat tato nastavení:

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

### <a name="set-values-for-webjobs-sdk-constructor-parameters-in-code"></a>Nastavení hodnot parametrů konstruktoru sady WebJobs SDK v kódu
Někdy chcete zadat název fronty, název objektu blob nebo kontejner nebo název tabulky v kódu, nikoli pevný kód. Můžete například zadat název fronty pro **QueueTrigger** v konfiguračním souboru nebo proměnné prostředí.

Můžete to udělat předáním objektu **NameResolver** typu **JobHostConfiguration.** Zahrnete speciální zástupné symboly obklopené procenty (%) podepisuje v parametrech konstruktoru atributu WebJobs SDK a kód **NameResolver** určuje skutečné hodnoty, které mají být použity místo těchto zástupných symbolů.

Předpokládejme například, že chcete použít frontu s názvem logqueuetest v testovacím prostředí a jeden s názvem logqueueprod v produkčním prostředí. Namísto pevně zakódovaného názvu fronty chcete zadat název položky v **kolekci appSettings,** která by měla skutečný název fronty. Pokud je klíč **appSettings** logqueue, vaše funkce může vypadat jako v následujícím příkladu.

```csharp
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Vaše třída **NameResolver** by pak mohla získat název fronty z **appSettings,** jak je znázorněno v následujícím příkladu:

```csharp
public class QueueNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

Třídu **NameResolver** předáte objektu **JobHost,** jak je znázorněno v následujícím příkladu.

```csharp
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new QueueNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

**Poznámka:** Názvy front, tabulek a objektů blob jsou vyřešeny při každém volání funkce, ale názvy kontejnerů objektů blob jsou vyřešeny pouze při spuštění aplikace. Název kontejneru objektů blob nelze změnit, když je úloha spuštěna.

## <a name="how-to-trigger-a-function-manually"></a>Jak spustit funkci ručně
Chcete-li spustit funkci ručně, použijte metodu **Call** nebo **CallAsync** na objektu **JobHost** a atribut **NoAutomaticTrigger** na funkci, jak je znázorněno v následujícím příkladu.

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

## <a name="how-to-write-logs"></a>Jak psát protokoly
Řídicí panel zobrazuje protokoly na dvou místech: na stránce webjob a na stránce pro konkrétní vyvolání webjob.

![Protokoly na stránce WebJob](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

![Přihlašuje stránku vyvolání funkce](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

Výstup z Console metody, které voláte ve funkci nebo v **Main()** metoda se zobrazí na stránce Řídicí panel pro webjob, nikoli na stránce pro konkrétní vyvolání metody. Výstup z objektu TextWriter, který získáte z parametru v podpisu metody, se zobrazí na stránce Řídicí panel pro vyvolání metody.

Výstup konzoly nelze propojit s voláním určité metody, protože konzola je jednovláknová, zatímco mnoho funkcí úloh může být spuštěno současně. To je důvod, proč sada SDK poskytuje každé vyvolání funkce s vlastním jedinečným objektem zapisovače protokolu.

Chcete-li zapsat [protokoly trasování aplikací](../app-service/troubleshoot-dotnet-visual-studio.md#logsoverview), použijte **Console.Out** (vytvoří protokoly označené jako INFO) a **Console.Error** (vytvoří protokoly označené jako CHYBA). Alternativou je použití [trace nebo TraceSource](https://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx), který poskytuje verbose, upozornění a kritické úrovně kromě informace a chyby. Protokoly trasování aplikací se zobrazují v souborech protokolu webové aplikace, v tbulkách Azure nebo blobech Azure v závislosti na konfiguraci webové aplikace Azure. Stejně jako u všech výstupů konzoly se nejnovější protokoly 100 aplikací zobrazí také na stránce Řídicí panel pro webovou úlohu, nikoli na stránce vyvolání funkce.

Výstup konzoly se zobrazí v řídicím panelu pouze v případě, že program běží v Azure WebJob, ne v případě, že program běží místně nebo v jiném prostředí.

Protokolování můžete zakázat nastavením připojovacího řetězce řídicího panelu na hodnotu null. Další informace naleznete v tématu [Jak nastavit možnosti konfigurace](#how-to-set-configuration-options).

Následující příklad ukazuje několik způsobů zápisu protokolů:

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

V řídicím panelu Sady WebJobs SDK se výstup z objektu **TextWriter** zobrazí, když přejdete na stránku pro vyvolání určité funkce a vyberete **Přepnout výstup**:

![Odkaz vyvolání](./media/vs-storage-webjobs-getting-started-queues/dashboardinvocations.png)

![Přihlašuje stránku vyvolání funkce](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

Na řídicím panelu sady WebJobs SDK se zobrazí posledních 100 řádků výstupu konzoly, když přejdete na stránku pro webovou úlohu (nikoli pro vyvolání funkce) a vyberete **Přepnout výstup**.

![Přepnout výstup](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

V nepřetržité matné službě WebJob se v souborovém systému webové aplikace /data/jobs/continuous/*{webjobname}*/job_log.txt zobrazují protokoly aplikací.

        [09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
        [09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
        [09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

V objektu blob Azure protokoly aplikací vypadat takto: 2014-09-26T21:01:13,Informace,contosoadsnew,491e54,635473620738373502,0,17404,Console.Write - Hello world!, 2014-09-26T21:01:13,Chyba,contosoadsnew,491e54, 635473620738373502,0,17404,19,Console.Error - Hello world!, 2014-09-26T21:01:13,Informace,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Dobrý den, svět!,

A v tabulce Azure **console.out** a **console.error** protokoly vypadají takto:

![Informace přihlásit v tabulce](./media/vs-storage-webjobs-getting-started-queues/tableinfo.png)

![Protokolování chyb v tabulce](./media/vs-storage-webjobs-getting-started-queues/tableerror.png)

## <a name="next-steps"></a>Další kroky
Tento článek poskytl ukázky kódu, které ukazují, jak zpracovat běžné scénáře pro práci s frontami Azure. Další informace o tom, jak používat Azure WebJobs a WebJobs SDK, najdete v [tématu Materiály pro dokumentaci Azure WebJobs](https://go.microsoft.com/fwlink/?linkid=390226).

