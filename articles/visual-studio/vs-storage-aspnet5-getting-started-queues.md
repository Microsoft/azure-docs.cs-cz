---
title: Začínáme s úložištěm front a připojenými službami sady Visual Studio (ASP.NET Core) | Microsoft Docs
description: Jak začít používat službu Azure Queue Storage v ASP.NET Core projektu v aplikaci Visual Studio
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
ms.openlocfilehash: d8e370c6f7c59da8522bb4fb1403b6107a9c9c41
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510977"
---
# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-core"></a>Začínáme s úložištěm front a připojenými službami sady Visual Studio (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

Tento článek popisuje, jak začít používat úložiště Azure Queue v aplikaci Visual Studio po vytvoření nebo odkazování účtu služby Azure Storage v ASP.NET Core projektu pomocí funkce **připojené služby** sady Visual Studio. Operace **připojené služby** nainstaluje příslušné balíčky NuGet pro přístup k úložišti Azure v projektu a přidá připojovací řetězec pro účet úložiště do konfiguračních souborů projektu. (Obecné informace o Azure Storage najdete v [dokumentaci k úložišti](https://azure.microsoft.com/documentation/services/storage/) .)

Azure Queue Storage je služba pro ukládání velkého počtu zpráv, ke kterým se dá dostat odkudkoli na světě prostřednictvím ověřených volání přes protokol HTTP nebo HTTPS. Jedna zpráva fronty může mít velikost až 64 kilobajtů (KB) a fronta může obsahovat miliony zpráv až do celkového limitu kapacity účtu úložiště. Podrobné informace o tom, jak programově manipulovat s frontami, najdete v tématu [Začínáme s úložištěm Azure Queue pomocí rozhraní .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) .

Začněte tím, že ve svém účtu úložiště nejdřív vytvoříte frontu Azure. V tomto článku se dozvíte, jak vytvořit frontu v C# nástroji a jak provádět základní operace front, jako je přidání, úprava, čtení a odebrání zpráv fronty.  Kód používá klientskou knihovnu Azure Storage pro .NET. Další informace o ASP.NET najdete v tématu [ASP.NET](https://www.asp.net).

Některá rozhraní API Azure Storage jsou asynchronní a kód v tomto článku předpokládá použití asynchronních metod. Další informace najdete v tématu [asynchronní programování](https://docs.microsoft.com/dotnet/csharp/async) .

## <a name="access-queues-in-code"></a>Přístup k frontám v kódu

Pokud chcete získat přístup k frontám v ASP.NET Core projektech, zahrňte C# do libovolného zdrojového souboru, který přistupuje k úložišti front Azure, následující položky. Tento kód použijte před kódem v následujících oddílech.

1. Přidejte potřebné `using` příkazy:
    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Queue;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. `CloudStorageAccount` Získejte objekt, který představuje informace o vašem účtu úložiště. K získání připojovacího řetězce a informací o účtu úložiště z konfigurace služby Azure použijte následující kód:

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. `CloudQueueClient` Získat objekt, který bude odkazovat na objekty fronty v účtu úložiště:

    ```cs
    // Create the CloudQueueClient object for the storage account.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. `CloudQueue` Získat objekt pro odkaz na konkrétní frontu:

    ```cs
    // Get a reference to the CloudQueue named "messagequeue"
    CloudQueue messageQueue = queueClient.GetQueueReference("messagequeue");
    ```

### <a name="create-a-queue-in-code"></a>Vytvoření fronty v kódu

Pokud chcete vytvořit frontu Azure v kódu, `CreateIfNotExistsAsync`zavolejte:

```cs
// Create the CloudQueue if it does not exist.
await messageQueue.CreateIfNotExistsAsync();
```

## <a name="add-a-message-to-a-queue"></a>Přidat zprávu do fronty

Chcete-li vložit zprávu do existující fronty, vytvořte nový `CloudQueueMessage` objekt a potom `AddMessageAsync` zavolejte metodu. `CloudQueueMessage` Objekt lze vytvořit buď z řetězce (ve formátu UTF-8), nebo pole bajtů.

```cs
// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await messageQueue.AddMessageAsync(message);
```

## <a name="read-a-message-in-a-queue"></a>Čtení zprávy ve frontě

Můžete prohlížet zprávy před frontou, aniž byste je museli odebírat z fronty voláním `PeekMessageAsync` metody:

```cs
// Peek the next message in the queue.
CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();
```

## <a name="read-and-remove-a-message-in-a-queue"></a>Čtení a odebrání zprávy ve frontě

Váš kód může z fronty odebrat (odřadit z fronty) zprávu ve dvou krocích.

1. Voláním `GetMessageAsync` získáte další zprávu ve frontě. Zpráva vrácená z `GetMessageAsync` se bude neviditelná pro jakýkoliv jiný kód, který čte zprávy z této fronty. Ve výchozím nastavení tato zpráva zůstává neviditelná po dobu 30 sekund.
1. Chcete-li dokončit odebrání zprávy z fronty, zavolejte `DeleteMessageAsync`.

Tento dvoukrokový proces odebrání zprávy zaručuje, aby v případě, že se vašemu kódu nepodaří zprávu zpracovat z důvodu selhání hardwaru nebo softwaru, mohla stejnou zprávu získat jiná instance vašeho kódu a bylo možné to zkusit znovu. Následující kód volá `DeleteMessageAsync` hned po zpracování zprávy:

```cs
// Get the next message in the queue.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

// Process the message in less than 30 seconds.

// Then delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
```

## <a name="additional-options-for-dequeuing-messages"></a>Další možnosti pro vyřazování zpráv do fronty

Existují dva způsoby, jak přizpůsobit načítání zpráv z fronty. Za prvé si můžete načíst dávku zpráv (až 32). Za druhé si můžete nastavit delší nebo kratší časový limit neviditelnosti, aby měl váš kód více nebo méně času na úplné zpracování jednotlivých zpráv. Následující příklad kódu používá `GetMessages` metodu k získání 20 zpráv v jednom volání. Pak každou zprávu zpracuje pomocí `foreach` smyčky. Také se pro každou zprávu nastaví časový limit neviditelnosti 5 minut. Všimněte si, že časovač pět minut začíná u všech zpráv současně, takže po pěti minutách budou všechny zprávy, které nebyly odstraněny, opět viditelné.

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

Podle potřeby můžete získat odhadovaný počet zpráv ve frontě. `FetchAttributes` Metoda požádá službu front o načtení atributů fronty, včetně počtu zpráv. Vlastnost vrátí poslední hodnotu získanou `FetchAttributes` metodou bez volání služby fronty. `ApproximateMethodCount`

```cs
// Fetch the queue attributes.
messageQueue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = messageQueue.ApproximateMessageCount;

// Display the number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>Použití vzoru Async-await s běžnými rozhraními API fronty

Tento příklad ukazuje použití vzoru Async-await s běžnými rozhraními API front končícím `Async`na. Při použití asynchronní metody vzorek Async-await pozastaví místní spuštění, dokud se volání nedokončí. Toto chování umožňuje aktuálnímu vláknu provádět další práci, která pomáhá vyhnout se kritickým bodům výkonu a zlepšuje celkovou odezvu vaší aplikace.

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

Pokud chcete odstranit frontu a všechny zprávy, které jsou v ní obsažené `Delete` , zavolejte metodu u objektu Queue:

```cs
// Delete the queue.
messageQueue.Delete();
```

## <a name="next-steps"></a>Další postup

[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
