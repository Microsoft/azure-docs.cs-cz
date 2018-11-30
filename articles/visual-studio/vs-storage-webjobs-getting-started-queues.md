---
title: Začínáme se službou queue storage a Visual Studio připojené služby (webové úlohy projekty) | Dokumentace Microsoftu
description: Jak začít používat Azure Queue storage v projektu úlohy WebJob po připojení k účtu úložiště pomocí sady Visual Studio připojené služby.
services: storage
author: ghogen
manager: douge
ms.assetid: 5c3ef267-2a67-44e9-ab4a-1edd7015034f
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 899792be583f3b2e2a16e42472fcdf87bf751893
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52635488"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-webjob-projects"></a>Začínáme s Azure Queue storage a Visual Studio připojené služby (webové úlohy projektů)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Přehled
Tento článek popisuje, jak začít používat Azure Queue storage v projektu Visual Studio Azure WebJob po odkazovat účtu služby Azure storage s použitím sady Visual Studio nebo vytvořili **přidání připojené služby** dialogové okno. Po přidání účtu úložiště do projektu úlohy WebJob pomocí sady Visual Studio **přidání připojené služby** dialogového okna, jsou nainstalovány odpovídající balíčky NuGet pro Azure Storage, do projektu, jsou přidány příslušné odkazy na rozhraní .NET a připojovací řetězce pro účet úložiště se aktualizují v souboru App.config.  

Tento článek obsahuje ukázky kódu C#, které ukazují, jak pomocí sady Azure WebJobs SDK verze 1.x službou Azure Queue storage.

Azure Queue Storage je služba pro ukládání velkého počtu zpráv, ke které můžete získat přístup z jakéhokoli místa na světě prostřednictvím ověřených volání s využitím protokolu HTTP nebo HTTPS. Zpráva s jednou frontou může mít velikost až 64 kB a jedna fronta můžete obsahovat miliony zpráv, až do dosažení celkové kapacity účtu úložiště. Zobrazit [Začínáme s Azure Queue Storage pomocí .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) Další informace. Další informace o ASP.NET najdete v tématu [ASP.NET](http://www.asp.net).

## <a name="how-to-trigger-a-function-when-a-queue-message-is-received"></a>Jak aktivovat funkci při doručení zprávy do fronty
Chcete-li vytvořit funkci, která volá sada WebJobs SDK, při doručení zprávy do fronty, použijte **QueueTrigger** atribut. Konstruktor atributu má řetězcový parametr, který určuje název fronty posílat do služby. Chcete-li zjistit, jak dynamicky nastavte název fronty, přečtěte si [nastavení konfigurace možností](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Řetězec zprávy fronty
V následujícím příkladu, tato fronta obsahuje zprávu řetězce, proto **QueueTrigger** je použít pro parametr řetězce s názvem **logMessage** obsahující obsah zprávy fronty. Funkce [zapíše zprávu protokolu na řídicí panel](#how-to-write-logs).

```csharp
public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    logger.WriteLine(logMessage);
}
```

Kromě **řetězec**, hodnota parametru může být bajtové pole, **CloudQueueMessage** objektu nebo POCO, který určíte.

### <a name="poco-plain-old-clr-objecthttpenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO [(Plain původního objektu CLR](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) fronty zpráv
V následujícím příkladu zpráva fronty obsahuje JSON pro **BlobInformation** objekt, který zahrnuje **BlobName** vlastnost. Sada SDK automaticky deserializuje objekt.

```csharp
public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
{
    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
}
```

Sada SDK používá [balíček Newtonsoft.Json NuGet](http://www.nuget.org/packages/Newtonsoft.Json) k serializaci a deserializaci zprávy. Pokud vytvoříte frontu zpráv v aplikaci, která nepodporuje použití sady WebJobs SDK, můžete napsat kód jako v následujícím příkladu, k vytvoření zprávy fronty POCO, které mohou analyzovat sady SDK.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

### <a name="async-functions"></a>Asynchronní funkce
Následující asynchronní funkci [zapíše protokol do řídicího panelu](#how-to-write-logs).

```csharp
public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    await logger.WriteLineAsync(logMessage);
}
```

Asynchronní funkce může trvat [token zrušení](http://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken), jak je znázorněno v následujícím příkladu, který zkopíruje objekt blob. (Vysvětlení **queueTrigger** zástupného symbolu, najdete v článku [objekty BLOB](#how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message) části.)

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

## <a name="types-the-queuetrigger-attribute-works-with"></a>Typy atributů QueueTrigger funguje s
Můžete použít **QueueTrigger** s následujícími typy:

* **řetězec**
* Typ POCO serializovanou jako JSON
* **Byte]**
* **CloudQueueMessage**

## <a name="polling-algorithm"></a>Algoritmus cyklického dotazování
Sada SDK implementuje exponenciální regresní algoritmu náhodných aby se snížil dopad nečinné fronty dotazovat se na náklady za transakce úložiště.  Když je nalezena zpráva, sady SDK čeká dvou sekund a pak vyhledá další zprávy. Když je nalezena žádná zpráva čeká před dalším pokusem o čtyři sekundy. Po následujících neúspěšných pokusech o získání zpráv fronty dobu čekání i nadále zvyšovat, dokud nedosáhne maximální doba čekání, výchozí nastavení je jedna minuta. [Maximální doba čekání je možné konfigurovat](#how-to-set-configuration-options).

## <a name="multiple-instances"></a>Více instancí
Pokud vaše webová aplikace běží na více instancí, průběžné WebJobs běží na každém počítači, a každý počítač bude čekat aktivační události a pokusí se spouštět službu functions. V některých případech to může vést k některé funkce zpracování stejných dat dvakrát takže funkce by měly být idempotentní (napsána tak, aby opakovaně volání se stejnými vstupními daty nevytvoří duplicitních výsledků).  

## <a name="parallel-execution"></a>Paralelní provádění
Pokud máte více funkcí naslouchá na různých front, sady SDK budou je volat paralelně při jsou zprávy přijímány současně.

Totéž platí při přijetí více zpráv pro jednu frontu. Ve výchozím nastavení sady SDK získá dávku 16 fronty zpráv najednou a spustí funkci, která zpracovává paralelně. [Velikost dávky je možné konfigurovat](#how-to-set-configuration-options). Při zpracování číslo na polovinu velikost dávky, sady SDK získá další dávku a spustí zpracování zprávy. Maximální počet souběžných za funkce zpracování zprávy je proto jeden a půl krát velikost dávky. Toto omezení platí zvlášť pro každou funkci, která má **QueueTrigger** atribut. Pokud nechcete, aby paralelní provádění pro jednu frontu zpráv, nastavte na 1 velikost dávky.

## <a name="get-queue-or-queue-message-metadata"></a>Získat fronty nebo fronty zpráv metadat
Přidávání parametrů k podpisu metody, můžete získat následující vlastnosti zprávy:

* **DateTimeOffset** expirationTime
* **DateTimeOffset** insertionTime
* **DateTimeOffset** nextVisibleTime
* **řetězec** queueTrigger (obsahuje text zprávy)
* **řetězec** id
* **řetězec** vlastností popReceipt
* **int** dequeueCount

Pokud budete chtít pracovat přímo s úložištěm Azure API, můžete také přidat **CloudStorageAccount** parametru.

Následující příklad zapíše všechna z těchto metadat do protokolu informace o aplikaci. V tomto příkladu logMessage i queueTrigger obsahovat obsah zprávy fronty.

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

Tady je ukázkový protokol napsané ve vzorovém kódu:

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
Funkce, která běží v nepřetržitá webová úloha může přijmout **CancellationToken** parametr, který umožňuje operačnímu systému upozornit funkci webové úlohy je ukončeno. Toto oznámení můžete použít k Ujistěte se, že funkce nebude dojde k neočekávanému ukončení tak, aby data ponechá v nekonzistentním stavu.

Následující příklad ukazuje, jak vyhledat brzké ukončení úlohy WebJob ve funkci.

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

**Poznámka:** řídicí panel nemusí správně zobrazovat stav a výstupu funkcí, které byla ukončena.

Další informace najdete v tématu [řádné vypnutí WebJobs](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR).   

## <a name="how-to-create-a-queue-message-while-processing-a-queue-message"></a>Vytvoření zprávy fronty při zpracování zprávy fronty
Chcete-li vytvořit funkci, která vytvoří nové zprávy fronty, použijte **fronty** atribut. Stejně jako **QueueTrigger**, předejte jako řetězec názvu fronty nebo můžete [dynamické nastavení názvu fronty](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Řetězec zprávy fronty
Následující ukázka kódu bez asynchronní vytvoří nové zprávy fronty ve frontě s názvem "outputqueue" se stejným obsahem jako fronty zprávy přijaté do fronty s názvem "inputqueue". (Pro asynchronní funkce používají **IAsyncCollector<T>**  jak je uvedeno dále v této části.)

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    [Queue("outputqueue")] out string outputQueueMessage )
{
    outputQueueMessage = queueMessage;
}
```

### <a name="poco-plain-old-clr-objecthttpenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO [(Plain původního objektu CLR](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) fronty zpráv
K vytvoření zprávy fronty, která obsahuje POCO, spíše než řetězec, předejte typ POCO jako výstupní parametr pro **fronty** konstruktor atributu.

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
{
    blobInfoOutput = blobInfoInput;
}
```

Sada SDK automaticky serializuje objekt do formátu JSON. Zpráva fronty je vytvořen vždy, i v případě, objekt má hodnotu null.

### <a name="create-multiple-messages-or-in-async-functions"></a>Vytvoření více zpráv nebo v asynchronních funkcí
Chcete-li vytvořit více zpráv, ujistěte se, typ parametru pro výstupní fronty **ICollector<T>**  nebo **IAsyncCollector<T>**, jak je znázorněno v následujícím příkladu.

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

Každá zpráva fronty je vytvořena ihned po **přidat** metoda je volána.

### <a name="types-that-the-queue-attribute-works-with"></a>Typy, které atribut fronta pracuje s
Můžete použít **fronty** atribut na následující typy parametrů:

* **mimo řetězec** (Pokud hodnota parametru je jiná než null při ukončení funkce vytvoří zprávu fronty)
* **navýšení kapacity byte []** (funguje jako **řetězec**)
* **navýšení kapacity CloudQueueMessage** (funguje jako **řetězec**)
* **navýšení kapacity POCO** (serializovatelný typ., vytvoří zprávu s objekt null Pokud má parametr hodnotu null při ukončení funkce)
* **ICollector**
* **IAsyncCollector**
* **CloudQueue** (pro vytváření zpráv ručně přímo pomocí rozhraní API služby Azure Storage)

### <a name="use-webjobs-sdk-attributes-in-the-body-of-a-function"></a>Použití sady WebJobs SDK atributů v těle funkce
Pokud potřebujete udělat nějakou práci ve své funkci před použitím atributu sada WebJobs SDK, jako **fronty**, **Blob**, nebo **tabulky**, můžete použít **IBinder**rozhraní.

V následujícím příkladu přebírá zprávu vstupní fronty a vytvoří novou zprávu s stejný obsah ve výstupní frontě. Název fronty výstupu nastavený prostřednictvím kódu v těle funkce.

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

**IBinder** rozhraní lze také s **tabulky** a **Blob** atributy.

## <a name="how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message"></a>Čtení a zápisu objektů BLOB a tabulek při zpracování zprávy fronty
**Blob** a **tabulky** atributy umožňují čtení a zápisu objektů BLOB a tabulek. Příklady v této části se vztahují na objekty BLOB. Ukázky kódu, které ukazují, jak aktivovat procesy, když jsou objekty BLOB vytvoření nebo aktualizaci, najdete v části [používání úložiště objektů blob v Azure se sadou WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).
<!-- , and for code samples that read and write tables, see [How to use Azure table storage with the WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-storage-tables-how-to.md). -->

### <a name="string-queue-messages-triggering-blob-operations"></a>Řetězec zprávy fronty aktivací operace objektů blob
Fronty zprávy, která obsahuje řetězce **queueTrigger** je zástupný symbol můžete použít v **Blob** atributu **blobPath** parametr, který obsahuje obsah zpráva.

Následující příklad používá **Stream** objekty ke čtení a zápisu objektů BLOB. Zpráva fronty je název objektu blob v kontejneru textblobs. Kopírování objektu blob s "-nové" připojenou k názvu se vytvoří ve stejném kontejneru.

```csharp
public static void ProcessQueueMessage(
    [QueueTrigger("blobcopyqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

**Blob** atribut přebírá konstruktor **blobPath** parametr, který určuje název kontejneru a objektu blob. Další informace o tento zástupný text, naleznete v tématu [používání úložiště objektů blob v Azure se sadou WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

Pokud atribut upraví **Stream** objektu, určuje jiný parametr konstruktoru **FileAccess** režimu pro čtení, zápisu nebo čtení a zápisu.

Následující příklad používá **CloudBlockBlob** objektu, který chcete odstranit objekt blob. Zpráva fronty je název objektu blob.

```csharp
public static void DeleteBlob(
    [QueueTrigger("deleteblobqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
{
    blobToDelete.Delete();
}
```

### <a name="poco-plain-old-clr-objecthttpenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO [(Plain původního objektu CLR](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) fronty zpráv
Pro POCO uloží jako dokumenty JSON ve zprávě fronty, můžete použít zástupné znaky, které název vlastnosti v objektu **fronty** atributu **blobPath** parametru. Názvy vlastností fronty metadat také slouží jako zástupné symboly. Zobrazit [získat fronty nebo fronty zpráv metadata](#get-queue-or-queue-message-metadata).

Následující příklad zkopíruje objekt blob do nového objektu blob s jinou příponou. Fronta zpráv je **BlobInformation** objekt, který zahrnuje **BlobName** a **BlobNameWithoutExtension** vlastnosti. Názvy vlastností, které se používají jako zástupné symboly v cestě k objektu blob pro **Blob** atributy.

```csharp
public static void CopyBlobPOCO(
    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

Sada SDK používá [balíček Newtonsoft.Json NuGet](http://www.nuget.org/packages/Newtonsoft.Json) k serializaci a deserializaci zprávy. Pokud vytvoříte frontu zpráv v aplikaci, která nepodporuje použití sady WebJobs SDK, můžete napsat kód jako v následujícím příkladu, k vytvoření zprávy fronty POCO, které mohou analyzovat sady SDK.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

Pokud potřebujete udělat nějakou práci ve své funkci před vazby objektu blob na objekt, můžete použít atribut v těle funkce, jak je znázorněno v [pomocí WebJobs SDK atributy v těle funkce](#use-webjobs-sdk-attributes-in-the-body-of-a-function).

### <a name="types-you-can-use-the-blob-attribute-with"></a>Typy, které můžete použít atribut objektů Blob s
**Blob** atribut lze použít s následujícími typy:

* **Stream** (čtení nebo zápisu zadán parametr konstruktoru FileAccess)
* **TextReader**
* **TextWriter**
* **řetězec** (čtení)
* **mimo řetězec** (zápis; vytvoří objekt blob pouze v případě, že parametr řetězce je jiná než null, pokud funkce vrátí)
* POCO (čtení)
* navýšení kapacity POCO (zápis; vždy vytvoří objekt blob, vytvoří jako objekt s hodnotou null, pokud parametr POCO má hodnotu null, pokud funkce vrátí)
* **CloudBlobStream** (write)
* **ICloudBlob** (čtení a zápis)
* **CloudBlockBlob** (čtení a zápis)
* **CloudPageBlob** (čtení a zápis)

## <a name="how-to-handle-poison-messages"></a>Způsob zpracování nezpracovatelných zpráv
Zprávy, jejíž obsah způsobí, že funkce, která se nezdaří se nazývají *nezpracovatelné zprávy*. Pokud funkce selže, zpráva fronty není odstraněn a nakonec je převzata, způsobuje cyklus se opakuje. Sady SDK můžete automaticky přerušení cyklu po omezený počet iterací, nebo je možné provést ručně.

### <a name="automatic-poison-message-handling"></a>Automatické manipulaci s nezpracovatelnými zprávami
Sada SDK bude volání funkce až 5krát ke zpracování zpráv fronty. Pokud selže páté zkuste zpráva bude přesunuta do fronty poškozené. Můžete zjistit, jak nakonfigurovat maximální počet opakovaných pokusů v [nastavení konfigurace možností](#how-to-set-configuration-options).

Počet poškozených fronta je s názvem *{originalqueuename}*-poškozené. Můžete napsat, že je potřeba funkci zpracování zpráv z fronty nezpracovatelných podle jejich protokolování nebo odeslání oznámení této ruční pozornost.

V následujícím příkladu **CopyBlob** funkce se nezdaří, pokud zpráva fronty obsahuje název objektu blob, který neexistuje. Pokud k tomu dojde, se přesune zprávy z fronty copyblobqueue copyblobqueue poison fronty. **ProcessPoisonMessage** pak protokoly nezpracovatelných zpráv.

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

Následující obrázek znázorňuje výstup konzoly z těchto funkcí při zpracování nezpracovatelných zpráv.

![Výstup na konzole pro manipulaci s nezpracovatelnými zprávami](./media/vs-storage-webjobs-getting-started-queues/poison.png)

### <a name="manual-poison-message-handling"></a>Ruční manipulaci s nezpracovatelnými zprávami
Počet, kolikrát má vyzvednuty zprávu pro zpracování můžete získat tak, že přidáte **int** parametr s názvem **dequeueCount** vaší funkce. Potom můžete zkontrolovat počet odstranění z fronty v kódu funkce a provádět vlastní nezpracovatelná zpráva byla zpracování při počet překročení mezních hodnot, jak je znázorněno v následujícím příkladu.

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
Můžete použít **JobHostConfiguration** typ a nastavte následující možnosti konfigurace:

* Nastavení sady SDK připojovacích řetězců v kódu.
* Konfigurace **QueueTrigger** nastavení, jako je maximální počet odstranění z fronty.
* Názvy front získá z konfigurace.

### <a name="set-sdk-connection-strings-in-code"></a>Sada SDK připojovacích řetězců v kódu
Nastavení sady SDK připojovacích řetězců v kódu můžete použít vlastní názvy řetězec připojení v konfiguračních souborů nebo proměnné prostředí, jak je znázorněno v následujícím příkladu.

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
Můžete nakonfigurovat následující nastavení, které platí pro zpracování zpráv fronty:

* Maximální počet zpráv fronty, které jsou současně neexistoval, který se spustí paralelně (výchozí je 16).
* Maximální počet opakování, než se do poškozené fronty pošle zprávu fronty (výchozí hodnota je 5).
* Maximální doba před cyklickým dotazováním znovu, když do fronty je prázdný (výchozí hodnota je 1 minuta).

Následující příklad ukazuje postup při konfiguraci těchto nastavení:

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

### <a name="set-values-for-webjobs-sdk-constructor-parameters-in-code"></a>Nastavení hodnot pro WebJobs SDK parametry konstruktoru v kódu
Někdy chcete zadat název fronty, název objektu blob nebo kontejneru, nebo tabulka název v kódu namísto pevně zakódovat. Například můžete chtít určit název fronty **QueueTrigger** v konfigurační soubor nebo prostředí proměnnou.

Můžete to udělat předáním **NameResolver** objektu **JobHostConfiguration** typu. Zahrnout speciální zástupné symboly ohraničen symboly procent (%) v parametry konstruktoru atributu sada WebJobs SDK a vaše **NameResolver** kód určuje skutečné hodnoty, které mají být zastoupen tyto zástupné symboly.

Předpokládejme například, že chcete použít frontu s názvem logqueuetest v testovacím prostředí a jednu s názvem logqueueprod v produkčním prostředí. Místo názvu pevně zakódované fronty, kterou chcete zadat název položky v **appSettings** kolekce, která bude mít na skutečný název. Pokud **appSettings** logqueue je klíč, funkce by měl vypadat jako v následujícím příkladu.

```csharp
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Vaše **NameResolver** třída může potom získá název fronty z **appSettings** jak je znázorněno v následujícím příkladu:

```csharp
public class QueueNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

Můžete předat **NameResolver** třídu v **JobHost** jak je znázorněno v následujícím příkladu.

```csharp
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new QueueNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

**Poznámka:** fronty, tabulky a názvy objektů blob jsou vyřešeny pokaždé, když je volána funkce, ale názvy kontejneru objektů blob jsou vyřešeny pouze při spuštění aplikace. Nelze změnit název kontejneru objektů blob, zatímco úloha běží.

## <a name="how-to-trigger-a-function-manually"></a>Jak aktivovat funkci ručně
Chcete-li aktivovat funkce ručně, použijte **volání** nebo **CallAsync** metodu **JobHost** objektu a **NoAutomaticTrigger** atribut na funkci, jak je znázorněno v následujícím příkladu.

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

## <a name="how-to-write-logs"></a>Jak napsat protokoly
Na řídicím panelu zobrazuje protokoly na dvou místech: stránka pro webové úlohy a na stránce pro konkrétní úlohu WebJob vyvolání.

![Protokoly na stránce webové úlohy](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

![Protokoly na stránce funkce volání](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

Výstup z konzoly metody, které volají ve funkci nebo v **Main()** metoda se zobrazí v této webové stránce řídicího panelu, ne na stránku pro volání konkrétní metody. Na stránce řídicího panelu pro volání metody se zobrazí výstup z objekt TextWriter, který získáte z parametru v podpisu metody.

Výstup na konzole, nelze ho propojit s volání konkrétní metody, protože konzole je s jedním vláknem, ale mnoho funkcí úloha může běžet ve stejnou dobu. To je důvod, proč sada SDK poskytuje každé volání funkce s objekt zapisovače svůj vlastní jedinečný protokolu.

Zapsat [protokoly trasování aplikací](../app-service/web-sites-dotnet-troubleshoot-visual-studio.md#logsoverview), použijte **Console.Out** (vytvářejí protokoly, které jsou označeny jako informace o) a **Console.Error** (vytvářejí protokoly, které jsou označeny jako chyba). Další možností je použít [trasování nebo TraceSource](https://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx), které poskytuje podrobné, upozornění, a kritická úrovních kromě informací a chyba. Protokoly trasování aplikace se zobrazí v souborech protokolů webové aplikace, tabulky Azure, nebo za objekty BLOB Azure v závislosti na tom, jak nakonfigurovat svou webovou aplikaci Azure. Platí pro všechny výstup na konzole, protokoly posledních 100 aplikací také se zobrazí na stránce řídicího panelu pro webové úlohy, ne stránku pro volání funkce.

Výstup konzoly se zobrazí na řídicím panelu pouze v případě, že je aplikace spuštěna ve webové úloze Azure, není-li program spuštěn místně nebo v nějaké jiné prostředí.

Zakázat protokolování tak, že nastavíte řídicí panel připojovací řetězec na hodnotu null. Další informace najdete v tématu [nastavení konfigurace možností](#how-to-set-configuration-options).

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

V panelu WebJobs SDK na výstup z **TextWriter** zobrazuje když přejdete na stránku pro konkrétní funkci volání a výběr objektů **přepnout výstup**:

![Vyvolání odkazu](./media/vs-storage-webjobs-getting-started-queues/dashboardinvocations.png)

![Protokoly na stránce funkce volání](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

Na řídicím panelu WebJobs SDK, posledních 100 řádků konzoly výstupu zobrazit až při přejděte na stránku pro webové úlohy (není pro volání funkce) a vyberte **přepnout výstup**.

![Přepnout výstupu](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

V probíhající WebJob, protokoly aplikací zobrazí v/data/úlohy/průběžné/*{webjobname}*/job_log.txt v systému souborů webové aplikace.

        [09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
        [09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
        [09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

V Azure blob protokolů vzhledu aplikace následujícím způsobem: 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world! 2014 -09-contosoadsnew 26T21:01:13, chyby, 491e54, 635473620738373502,0,17404,19,Console.Error - Hello world!, 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

A v tabulce Azure **Console.Out** a **Console.Error** protokoly vypadat nějak takto:

![Informace o protokolu v tabulce](./media/vs-storage-webjobs-getting-started-queues/tableinfo.png)

![Protokol chyb v tabulce](./media/vs-storage-webjobs-getting-started-queues/tableerror.png)

## <a name="next-steps"></a>Další postup
Tento článek poskytuje ukázek kódu, které ukazují, jak zvládnout běžné scénáře pro práci s frontami Azure. Další informace o tom, jak používat Azure WebJobs a sada WebJobs SDK najdete v tématu [prostředků dokumentace Azure WebJobs](https://go.microsoft.com/fwlink/?linkid=390226).

