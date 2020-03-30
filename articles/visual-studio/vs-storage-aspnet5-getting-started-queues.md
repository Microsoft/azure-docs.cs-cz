---
title: Začínáme s úložištěm front pomocí Sady Visual Studio (ASP.NET Core)
description: Jak začít používat úložiště front Azure v projektu ASP.NET Core v Sadě Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: 04977069-5b2d-4cba-84ae-9fb2f5eb1006
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.date: 11/14/2017
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5cdf6f2644788674df91b533c9444fc88ab30b09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72300024"
---
# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-core"></a>Začínáme s úložištěm front a připojenými službami Visual Studia (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

Tento článek popisuje, jak začít používat úložiště fronty Azure ve Visual Studiu po vytvoření nebo odkazování na účet úložiště Azure v projektu ASP.NET Core pomocí funkce Visual Studio **Connected Services.** Operace **Připojené služby** nainstaluje příslušné balíčky NuGet pro přístup k úložišti Azure ve vašem projektu a přidá připojovací řetězec pro účet úložiště do konfiguračních souborů projektu. (Obecné informace o Azure Storage najdete v [dokumentaci k](https://azure.microsoft.com/documentation/services/storage/) úložišti.)

Úložiště front Azure je služba pro ukládání velkého počtu zpráv, ke kterým lze přistupovat z libovolného místa na světě prostřednictvím ověřených volání pomocí protokolu HTTP nebo HTTPS. Jedna zpráva fronty může mít velikost až 64 kilobajtů (KB) a fronta může obsahovat miliony zpráv až do celkového limitu kapacity účtu úložiště. Taky najdete [v tématu Začínáme s úložištěm Fronty Azure pomocí rozhraní .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) podrobnosti o programové manipulaci s frontami.

Chcete-li začít, nejprve vytvořte frontu Azure ve vašem účtu úložiště. Tento článek pak ukazuje, jak vytvořit frontu v jazyce C# a jak provádět základní operace fronty, jako je například přidávání, úpravy, čtení a odebrání zpráv fronty.  Kód používá klientskou knihovnu úložiště Azure pro rozhraní .NET. Další informace o ASP.NET naleznete v [tématu ASP.NET](https://www.asp.net).

Některá api úložiště Azure jsou asynchronní a kód v tomto článku předpokládá, že se používají asynchronní metody. Další informace naleznete [v tématu Asynchronní programování.](https://docs.microsoft.com/dotnet/csharp/async)

## <a name="access-queues-in-code"></a>Přístup k frontám v kódu

Chcete-li získat přístup k frontám v ASP.NET projektů core, zahrňte následující položky do libovolného zdrojového souboru Jazyka C#, který přistupuje k úložišti front Azure. Použijte všechny tento kód před kód v následujících částech.

1. Přidejte `using` potřebné příkazy:
    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Queue;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. Získejte `CloudStorageAccount` objekt, který představuje informace o účtu úložiště. Pomocí následujícího kódu získáte informace o připojovacím řetězci úložiště a účtu úložiště z konfigurace služby Azure:

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Získejte `CloudQueueClient` objekt, který bude odkazovat na objekty fronty v účtu úložiště:

    ```cs
    // Create the CloudQueueClient object for the storage account.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Získejte `CloudQueue` objekt, který odkazuje na určitou frontu:

    ```cs
    // Get a reference to the CloudQueue named "messagequeue"
    CloudQueue messageQueue = queueClient.GetQueueReference("messagequeue");
    ```

### <a name="create-a-queue-in-code"></a>Vytvoření fronty v kódu

Chcete-li vytvořit frontu `CreateIfNotExistsAsync`Azure v kódu, volejte :

```cs
// Create the CloudQueue if it does not exist.
await messageQueue.CreateIfNotExistsAsync();
```

## <a name="add-a-message-to-a-queue"></a>Přidání zprávy do fronty

Chcete-li vložit zprávu do existující `CloudQueueMessage` fronty, vytvořte `AddMessageAsync` nový objekt a zavolejte metodu. Objekt `CloudQueueMessage` lze vytvořit buď z řetězce (ve formátu UTF-8) nebo bajtového pole.

```cs
// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await messageQueue.AddMessageAsync(message);
```

## <a name="read-a-message-in-a-queue"></a>Čtení zprávy ve frontě

Zprávu můžete nahlédnout před frontou, aniž byste ji odebrali `PeekMessageAsync` z fronty voláním metody:

```cs
// Peek the next message in the queue.
CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();
```

## <a name="read-and-remove-a-message-in-a-queue"></a>Čtení a odebrání zprávy ve frontě

Váš kód můžete odebrat (dequeue) zprávu z fronty ve dvou krocích.

1. Volání `GetMessageAsync` získat další zprávu ve frontě. Zpráva vrácená `GetMessageAsync` z stane neviditelné pro jakýkoli jiný kód čtení zpráv z této fronty. Ve výchozím nastavení tato zpráva zůstává neviditelná po dobu 30 sekund.
1. Chcete-li dokončit odebrání zprávy `DeleteMessageAsync`z fronty, zavolejte .

Tento dvoukrokový proces odebrání zprávy zaručuje, aby v případě, že se vašemu kódu nepodaří zprávu zpracovat z důvodu selhání hardwaru nebo softwaru, mohla stejnou zprávu získat jiná instance vašeho kódu a bylo možné to zkusit znovu. Následující kód `DeleteMessageAsync` volá ihned po zpracování zprávy:

```cs
// Get the next message in the queue.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

// Process the message in less than 30 seconds.

// Then delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
```

## <a name="additional-options-for-dequeuing-messages"></a>Další možnosti pro dequeuing zpráv

Existují dva způsoby, jak přizpůsobit načítání zpráv z fronty. Za prvé si můžete načíst dávku zpráv (až 32). Za druhé si můžete nastavit delší nebo kratší časový limit neviditelnosti, aby měl váš kód více nebo méně času na úplné zpracování jednotlivých zpráv. Následující příklad kódu `GetMessages` používá metodu k získání 20 zpráv v jednom volání. Pak zpracovává každou zprávu `foreach` pomocí smyčky. Také se pro každou zprávu nastaví časový limit neviditelnosti 5 minut. Všimněte si, že pět minut časovač spustí pro všechny zprávy ve stejnou dobu, takže po pěti minutách uplynulo, všechny zprávy, které nebyly odstraněny znovu zobrazit.

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

Podle potřeby můžete získat odhadovaný počet zpráv ve frontě. Metoda `FetchAttributes` požádá službu fronty k načtení atributů fronty, včetně počtu zpráv. Vlastnost `ApproximateMethodCount` vrátí poslední hodnotu načtenou `FetchAttributes` metodou bez volání služby fronty.

```cs
// Fetch the queue attributes.
messageQueue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = messageQueue.ApproximateMessageCount;

// Display the number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>Použití vzoru Async-Await s běžnými api fronty

Tento příklad ukazuje, jak používat vzor async-await s `Async`běžnými nastaveními API fronty končícími na . Při použití asynchronní metody async-await vzor pozastaví místní spuštění, dokud není dokončeno volání. Toto chování umožňuje aktuální vlákno dělat další práci, která pomáhá vyhnout se kritické body výkonu a zlepšuje celkovou odezvu vaší aplikace.

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

Chcete-li odstranit frontu a všechny zprávy `Delete` v ní obsažené, zavolejte metodu na objektu fronty:

```cs
// Delete the queue.
messageQueue.Delete();
```

## <a name="next-steps"></a>Další kroky

[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
