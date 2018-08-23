---
title: Začínáme s queue storage a Visual Studio připojené služby (ASP.NET Core) | Dokumentace Microsoftu
description: Jak začít pracovat pomocí Azure queue storage v projektu aplikace ASP.NET Core v sadě Visual Studio
services: storage
author: ghogen
manager: douge
ms.assetid: 04977069-5b2d-4cba-84ae-9fb2f5eb1006
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.date: 11/14/2017
ms.author: ghogen
ms.openlocfilehash: 237294c1bc603402c349f7a56f20c34ed8d210fe
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2018
ms.locfileid: "42054838"
---
# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-core"></a>Začínáme s queue storage a Visual Studio připojené služby (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

Tento článek popisuje, jak začít používat Azure Queue storage v sadě Visual Studio po odkazovat účtu služby Azure storage v projektu aplikace ASP.NET Core pomocí sady Visual Studio nebo vytvořili **připojené služby** funkce. **Připojené služby** operace nainstaluje příslušné balíčky NuGet pro přístup k Azure storage ve vašem projektu a přidá připojovací řetězec pro účet úložiště pro konfigurační soubory projektu. (Viz [dokumentace ke službě Storage](https://azure.microsoft.com/documentation/services/storage/) obecné informace o službě Azure Storage.)

Azure queue storage je služba pro ukládání velkého počtu zpráv, které můžete přistupovat odkudkoli na světě prostřednictvím ověřených volání přes protokol HTTP nebo HTTPS. Zpráva jednou frontou může mít velikost až 64 kilobajtů (KB) a jedna fronta můžete obsahovat miliony zpráv až do vyčerpání celkové kapacity účtu úložiště. Viz také [Začínáme s úložištěm Azure Queue pomocí rozhraní .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) podrobnosti o programově manipulace s fronty.

Abyste mohli začít, vytvoření fronty služby Azure ve vašem účtu úložiště. Tento článek popisuje pak v tom, jak vytvořit frontu v jazyce C# a jak provádět základní fronty operace, jako jsou přidání, úprava, čtení a odstranění fronty zpráv.  Tento kód použije klientskou knihovnu pro úložiště Azure pro .NET. Další informace o ASP.NET najdete v tématu [ASP.NET](http://www.asp.net).

Některé z rozhraní API služby Azure Storage jsou asynchronní a kódu v tomto článku se předpokládá, že se používají asynchronní metody. Zobrazit [asynchronní programování](https://docs.microsoft.com/dotnet/csharp/async) Další informace.

## <a name="access-queues-in-code"></a>Přístup k frontám v kódu

Pro přístup k frontám v projektech ASP.NET Core, zahrnují následující položky v libovolné zdrojový soubor C#, který přistupuje k úložišti front Azure. Použijte všechny tento kód před kód v následujících částech.

1. Přidat nezbytné `using` příkazy:
    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Queue;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. Získání `CloudStorageAccount` objekt reprezentující informace o vašem účtu úložiště. Chcete-li získat připojovací řetězec úložiště a informace o účtu úložiště z konfigurace služby Azure pomocí následujícího kódu:

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Získání `CloudQueueClient` objekt neodkazovali na objekty fronty ve vašem účtu úložiště:

    ```cs
    // Create the CloudQueueClient object for the storage account.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Získání `CloudQueue` objektu, který chcete odkazovat na konkrétní fronty:

    ```cs
    // Get a reference to the CloudQueue named "messagequeue"
    CloudQueue messageQueue = queueClient.GetQueueReference("messagequeue");
    ```

### <a name="create-a-queue-in-code"></a>Vytvořit frontu v kódu

Chcete-li vytvořit front Azure v kódu, zavolejte '' CreateIfNotExistsAsync ":

```cs
// Create the CloudQueue if it does not exist.
await messageQueue.CreateIfNotExistsAsync();
```

## <a name="add-a-message-to-a-queue"></a>Přidání zprávy do fronty

Chcete-li vložit zprávu do existující fronty, vytvořte nový `CloudQueueMessage` objekt a potom voláním `AddMessageAsync` metody. A `CloudQueueMessage` objekt můžete vytvořit z řetězce (ve formátu UTF-8) nebo s polem bajtů.

```cs
// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await messageQueue.AddMessageAsync(message);
```

## <a name="read-a-message-in-a-queue"></a>Přečtěte si zprávu ve frontě

Můžete prohlížet zprávy ve frontě bez odebrání z fronty pomocí volání `PeekMessageAsync` metody:

```cs
// Peek the next message in the queue.
CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();
```

## <a name="read-and-remove-a-message-in-a-queue"></a>Čtení a odebrání zprávy ve frontě

Kód můžete odebrat (odstranění z fronty) zprávy z fronty ve dvou krocích.

1. Volání `GetMessageAsync` zobrazíte další zprávu ve frontě. Zpráva vrácená metodou `GetMessageAsync` stane neviditelnou pro jakýkoli jiný kód přečte zprávy z této fronty. Ve výchozím nastavení tato zpráva zůstává neviditelná po dobu 30 sekund.
1. Chcete-li dokončit odebere zprávu z fronty, zavolejte `DeleteMessageAsync`.

Tento dvoukrokový proces odebrání zprávy zaručuje, aby v případě, že se vašemu kódu nepodaří zprávu zpracovat z důvodu selhání hardwaru nebo softwaru, mohla stejnou zprávu získat jiná instance vašeho kódu a bylo možné to zkusit znovu. Následující kód volá `DeleteMessageAsync` hned po zpracování zprávy:

```cs
// Get the next message in the queue.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

// Process the message in less than 30 seconds.

// Then delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
```

## <a name="additional-options-for-dequeuing-messages"></a>Další možnosti pro zrušení fronty zpráv

Existují dva způsoby, jak přizpůsobit načtení zprávy z fronty. Za prvé si můžete načíst dávku zpráv (až 32). Za druhé si můžete nastavit delší nebo kratší časový limit neviditelnosti, aby měl váš kód více nebo méně času na úplné zpracování jednotlivých zpráv. Následující příklad kódu používá `GetMessages` metodu k získání 20 zpráv v jednom volání. Pak se každá zpráva zpracuje pomocí `foreach` smyčky. Také se pro každou zprávu nastaví časový limit neviditelnosti 5 minut. Všimněte si, že 5minutovém spuštění časovače pro všechny zprávy ve stejnou dobu, takže po uplynutí 5 minut všechny zprávy, které nebyly odstraněny opět viditelné.

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

Podle potřeby můžete získat odhadovaný počet zpráv ve frontě. `FetchAttributes` Metoda požádá službu front o načtení atributů fronty, včetně počtu zpráv. `ApproximateMethodCount` Vlastnost vrací poslední hodnotu načtenou `FetchAttributes` metody, bez volání služby front.

```cs
// Fetch the queue attributes.
messageQueue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = messageQueue.ApproximateMessageCount;

// Display the number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>Použití vzoru Async-Await s obecné frontě rozhraní API

Tento příklad ukazuje způsob použití async-await vzor s běžnými fronty rozhraní API končí `Async`. Při použití asynchronní metody, async-await vzor místní pozastaví provádění kódu až do dokončení volání. Toto chování umožňuje aktuálnímu vláknu provádět další práci, která pomáhá zabránit kritické body výkonu a zlepšuje celkovou rychlost reakce vaší aplikace.

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

Chcete-li odstranit frontu se všemi zprávami, které v ní, zavolejte `Delete` metodu na objekt fronty:

```cs
// Delete the queue.
messageQueue.Delete();
```

## <a name="next-steps"></a>Další postup

[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
