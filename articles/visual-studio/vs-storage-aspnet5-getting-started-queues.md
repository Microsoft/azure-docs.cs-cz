---
title: Začínáme s fronty úložiště a Visual Studio připojené služby (ASP.NET Core) | Microsoft Docs
description: Jak začít používat fronty Azure storage v projektu ASP.NET Core v sadě Visual Studio
services: storage
author: ghogen
manager: douge
ms.assetid: 04977069-5b2d-4cba-84ae-9fb2f5eb1006
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: article
ms.date: 11/14/2017
ms.author: ghogen
ms.openlocfilehash: e56f79334aa85d9a0c81bed4f00664fee5789676
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
ms.locfileid: "31793983"
---
# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-core"></a>Začínáme s fronty úložiště a Visual Studio připojené služby (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

Tento článek popisuje, jak začít používat Azure Queue storage v sadě Visual Studio, po vytvoření a odkazuje pomocí sady Visual Studio účet úložiště Azure v projektu ASP.NET Core **připojené služby** funkce. **Připojené služby** operaci nainstaluje příslušné balíčky NuGet pro přístup k úložišti Azure ve vašem projektu a přidá připojovací řetězec pro účet úložiště v konfiguračních souborech projektu. (Viz [úložiště dokumentace](https://azure.microsoft.com/documentation/services/storage/) obecné informace o službě Azure Storage.)

Azure queue storage je služba pro ukládání velkého počtu zpráv, které jsou přístupné odkudkoli na světě prostřednictvím ověřených volání s využitím protokolu HTTP nebo HTTPS. Zpráva s jednou frontou může být až 64 kilobajtů (KB) velikost a jedna fronta můžete obsahovat miliony zpráv, až do limitu celkové kapacity účtu úložiště. Viz také [Začínáme s Azure Queue storage pomocí rozhraní .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) podrobnosti o prostřednictvím kódu programu manipulace s fronty.

Chcete-li začít, nejdřív vytvořte fronty Azure ve vašem účtu úložiště. Pak se tento článek ukazuje, jak vytvořit frontu v jazyce C# a jak provádět základní fronty operací, jako je přidání, úprava, čtení a odebrání zprávy do fronty.  Kód používá Klientská knihovna pro úložiště Azure pro .NET. Další informace o technologii ASP.NET najdete v tématu [ASP.NET](http://www.asp.net).

Některé z rozhraní API úložiště Azure jsou asynchronní a kód v tomto článku předpokládá, že asynchronní metody jsou používány. V tématu [asynchronní programování](https://docs.microsoft.com/dotnet/csharp/async) Další informace.

## <a name="access-queues-in-code"></a>Přístup front v kódu

Pro přístup k frontám projektů ASP.NET Core, zahrnují následující položky v jakékoli C# zdrojový soubor, který používá fronty Azure storage. Použijte všechny tento kód před kód v následujících částech.

1. Přidat nezbytné `using` příkazy:
    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Queue;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. Získání `CloudStorageAccount` objekt, který reprezentuje informace o účtu úložiště. Chcete-li získat připojovací řetězec úložiště a informace o účtu úložiště z konfigurace služby Azure, použijte následující kód:

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Získání `CloudQueueClient` objekt, který má odkazovat na objekty fronty ve vašem účtu úložiště:

    ```cs
    // Create the CloudQueueClient object for the storage account.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Získání `CloudQueue` objekt, který má odkazovat na konkrétní fronty:

    ```cs
    // Get a reference to the CloudQueue named "messagequeue"
    CloudQueue messageQueue = queueClient.GetQueueReference("messagequeue");
    ```

### <a name="create-a-queue-in-code"></a>Vytvoření fronty v kódu

Chcete-li vytvořit frontu Azure v kódu, volejte '' CreateIfNotExistsAsync':

```cs
// Create the CloudQueue if it does not exist.
await messageQueue.CreateIfNotExistsAsync();
```

## <a name="add-a-message-to-a-queue"></a>Přidání zprávy do fronty

Pokud chcete vložit zprávu do existující fronty, vytvořte novou `CloudQueueMessage` objekt a potom volání `AddMessageAsync` metoda. A `CloudQueueMessage` můžete vytvořit objekt z řetězce (ve formátu UTF-8) nebo pole bajtů.

```cs
// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await messageQueue.AddMessageAsync(message);
```

## <a name="read-a-message-in-a-queue"></a>Čtení zprávy ve frontě

Můžete prohlížet zprávy ve frontě bez odebere ji z fronty voláním `PeekMessageAsync` metoda:

```cs
// Peek the next message in the queue.
CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();
```

## <a name="read-and-remove-a-message-in-a-queue"></a>Přečtěte si a odebrat zprávu ve frontě

Váš kód můžete odebrat (dequeue –) zprávu z fronty ve dvou krocích.

1. Volání `GetMessageAsync` získat další zprávu ve frontě. Zpráva vrácená metodou `GetMessageAsync` stane neviditelnou pro jakýkoli jiný kód čtení zpráv z této fronty. Ve výchozím nastavení tato zpráva zůstává neviditelná po dobu 30 sekund.
1. Chcete-li dokončit odebere ji z fronty, volejte `DeleteMessageAsync`.

Tento dvoukrokový proces odebrání zprávy zaručuje, aby v případě, že se vašemu kódu nepodaří zprávu zpracovat z důvodu selhání hardwaru nebo softwaru, mohla stejnou zprávu získat jiná instance vašeho kódu a bylo možné to zkusit znovu. Následující kód volání `DeleteMessageAsync` pravým po zpracování zprávy:

```cs
// Get the next message in the queue.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

// Process the message in less than 30 seconds.

// Then delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
```

## <a name="additional-options-for-dequeuing-messages"></a>Další možnosti pro vyřazení zprávy

Existují dva způsoby, jak přizpůsobit načítání zpráv z fronty. Za prvé si můžete načíst dávku zpráv (až 32). Za druhé si můžete nastavit delší nebo kratší časový limit neviditelnosti, aby měl váš kód více nebo méně času na úplné zpracování jednotlivých zpráv. Následující příklad kódu používá `GetMessages` metoda získá 20 zpráv v jednom volání. Pak se každá zpráva zpracuje pomocí `foreach` smyčky. Také se pro každou zprávu nastaví časový limit neviditelnosti 5 minut. Všimněte si, že pět minutu, které spustí časovač pro všechny zprávy ve stejnou dobu, takže po pěti minut předané, všechny zprávy, které nebyly odstraněny opět viditelné.

```cs
// Retrieve 20 messages at a time, keeping those messages invisible for 5 minutes, 
//   delete each message after processing.

foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

## <a name="get-the-queue-length"></a>Získání délky fronty

Podle potřeby můžete získat odhadovaný počet zpráv ve frontě. `FetchAttributes` Metoda požádá službu front o načtení atributů fronty, včetně počtu zpráv. `ApproximateMethodCount` Vlastnost vrací poslední hodnotu načtenou `FetchAttributes` metoda bez volání služby front.

```cs
// Fetch the queue attributes.
messageQueue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = messageQueue.ApproximateMessageCount;

// Display the number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>Použití vzoru Async-Await s obecné frontě rozhraní API

Tento příklad ukazuje, jak používat async-await vzor s běžné fronty rozhraní API s `Async`. Při použití asynchronní metody, async-await vzor pozastaví místní spuštění, dokud se nedokončí volání. Toto chování umožňuje aktuálnímu vláknu provádět další činnosti, která pomáhá zabránit kritická místa výkonu a zlepšuje celkovou rychlost reakce aplikace.

```cs
// Create a message to add to the queue.
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message.
await messageQueue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

## <a name="delete-a-queue"></a>Odstranění fronty

Chcete-li odstranit frontu se všemi zprávami, které v ní, zavolejte `Delete` metoda pro objekt fronty:

```cs
// Delete the queue.
messageQueue.Delete();
```

## <a name="next-steps"></a>Další postup

[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
