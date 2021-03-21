---
title: Začínáme s úložištěm Queue pomocí sady Visual Studio (cloudové služby)
description: Jak začít používat úložiště Azure Queue v projektu cloudové služby v aplikaci Visual Studio po připojení k účtu úložiště pomocí připojených služeb sady Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: da587aac-5e64-4e9a-8405-44cc1924881d
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure, devx-track-csharp
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 94f248edfebd6c6fedb78a54eee220c0ef38b4ab
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95545856"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Začínáme s Azure Queue Storage a připojenými službami sady Visual Studio (projekty cloudových služeb)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Přehled
Tento článek popisuje, jak začít používat úložiště Azure Queue v aplikaci Visual Studio po vytvoření nebo odkazování účtu úložiště Azure v projektu cloudové služby pomocí dialogového okna **Přidat připojené služby** sady Visual Studio.

Ukážeme vám, jak vytvořit frontu v kódu. Také vám ukážeme, jak provádět základní operace s frontami, jako je přidání, úprava, čtení a odebrání zpráv fronty. Ukázky jsou napsány v kódu jazyka C# a používají [Microsoft Azure Storage klientské knihovny pro .NET](/previous-versions/azure/dn261237(v=azure.100)).

Operace **Přidat připojené služby** nainstaluje příslušné balíčky NuGet pro přístup k úložišti Azure v projektu a přidá připojovací řetězec pro účet úložiště do konfiguračních souborů projektu.

* Další informace o manipulaci s frontami v kódu najdete v tématu Začínáme [s úložištěm Azure Queue pomocí rozhraní .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) .
* Obecné informace o Azure Storage najdete v [dokumentaci k úložišti](https://azure.microsoft.com/documentation/services/storage/) .
* Obecné informace o cloudových službách Azure najdete v [dokumentaci k Cloud Services](https://azure.microsoft.com/documentation/services/cloud-services/) .
* Další informace o programování aplikací ASP.NET naleznete v tématu [ASP.NET](https://www.asp.net) .

Azure Queue Storage je služba pro ukládání velkého počtu zpráv, ke které můžete získat přístup z jakéhokoli místa na světě prostřednictvím ověřených volání s využitím protokolu HTTP nebo HTTPS. Zpráva s jednou frontou může mít velikost až 64 kB a jedna fronta můžete obsahovat miliony zpráv, až do dosažení celkové kapacity účtu úložiště.

## <a name="access-queues-in-code"></a>Přístup k frontám v kódu
Chcete-li získat přístup k frontám v projektech aplikace Visual Studio Cloud Services, je nutné zahrnout následující položky do libovolného zdrojového souboru jazyka C#, který přistupuje k úložišti front Azure.

1. Ujistěte se, že deklarace oboru názvů v horní části souboru jazyka C# obsahují tyto příkazy **using** .
   
    ```csharp
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Queue;
    ```
2. Získejte objekt **CloudStorageAccount** , který představuje informace o vašem účtu úložiště. K získání připojovacího řetězce a informací o účtu úložiště z konfigurace služby Azure použijte následující kód.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
3. Získejte objekt **CloudQueueClient** , který bude odkazovat na objekty fronty v účtu úložiště.  
   
    ```csharp
    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
4. Získá objekt **CloudQueue** , který odkazuje na konkrétní frontu.
   
    ```csharp
    // Get a reference to a queue named "messageQueue"
    CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");
    ```

**Poznámka:** Použijte veškerý výše uvedený kód před kódem v následujících ukázkách.

## <a name="create-a-queue-in-code"></a>Vytvoření fronty v kódu
Chcete-li vytvořit frontu v kódu, stačí přidat volání do **CreateIfNotExists**.

```csharp
// Create the CloudQueue if it does not exist
messageQueue.CreateIfNotExists();
```

## <a name="add-a-message-to-a-queue"></a>Přidat zprávu do fronty
Chcete-li vložit zprávu do existující fronty, vytvořte nový objekt **CloudQueueMessage** a zavolejte metodu **AddMessage** .

Objekt **CloudQueueMessage** lze vytvořit buď z řetězce (ve formátu UTF-8), nebo pole bajtů.

Tady je příklad, který vkládá zprávu "Hello, World".

```csharp
// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
messageQueue.AddMessage(message);
```

## <a name="read-a-message-in-a-queue"></a>Čtení zprávy ve frontě
Pomocí volání metody **PeekMessage** můžete prohlížet zprávy ve frontě, aniž byste je z fronty odebrali.

```csharp
// Peek at the next message
CloudQueueMessage peekedMessage = messageQueue.PeekMessage();
```

## <a name="read-and-remove-a-message-in-a-queue"></a>Čtení a odebrání zprávy ve frontě
Kód může z fronty odebrat zprávu (z fronty) ve dvou krocích.

1. K získání další zprávy ve frontě volejte metodu **GetMessage** . Zpráva vrácená metodou **GetMessage** se stane neviditelnou pro jakýkoli jiný kód, který čte zprávy z této fronty. Ve výchozím nastavení tato zpráva zůstává neviditelná po dobu 30 sekund.
2. Chcete-li dokončit odebrání zprávy z fronty, zavolejte **DeleteMessage**.

Tento dvoukrokový proces odebrání zprávy zaručuje, aby v případě, že se vašemu kódu nepodaří zprávu zpracovat z důvodu selhání hardwaru nebo softwaru, mohla stejnou zprávu získat jiná instance vašeho kódu a bylo možné to zkusit znovu. Následující kód volá **DeleteMessage** hned po zpracování zprávy.

```csharp
// Get the next message in the queue.
CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

// Process the message in less than 30 seconds

// Then delete the message.
await messageQueue.DeleteMessage(retrievedMessage);
```


## <a name="use-additional-options-to-process-and-remove-queue-messages"></a>Použití dalších možností pro zpracování a odebrání zpráv fronty
Načítání zpráv z fronty si můžete přizpůsobit dvěma způsoby.

* Můžete získat dávku zpráv (až do 32).
* Můžete nastavit delší nebo kratší časový limit neviditelnosti, což umožňuje, aby váš kód měl více nebo méně času na úplné zpracování každé zprávy. V následujícím příkladu kódu se pomocí metody **GetMessages** získá 20 zpráv v jednom volání. Následně se každá zpráva zpracuje pomocí smyčky **foreach**. Také se pro každou zprávu nastaví časový limit neviditelnosti 5 minut. Pozor, 5minutový časový limit začíná pro všechny zprávy najednou, takže po uplynutí 5 minut od volání metody **GetMessages** pak budou všechny zprávy, které nebyly odstraněny, opět viditelné.

Tady je příklad:

```csharp
foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.

    // Then delete the message after processing
    messageQueue.DeleteMessage(message);

}
```

## <a name="get-the-queue-length"></a>Získání délky fronty
Podle potřeby můžete získat odhadovaný počet zpráv ve frontě. Metoda **FetchAttributes** požádá Službu front o načtení atributů fronty, včetně počtu zpráv. Vlastnost **ApproximateMethodCount** vrací poslední hodnotu získanou metodou **FetchAttributes** bez volání služba front.

```csharp
// Fetch the queue attributes.
messageQueue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = messageQueue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="use-the-async-await-pattern-with-common-azure-queue-apis"></a>Použití vzorů Async-Await s běžnými rozhraními API fronty Azure
Tento příklad ukazuje, jak použít vzor Async-Await s běžnými rozhraními API fronty Azure. Ukázka volá asynchronní verzi každé z těchto metod, kterou lze zobrazit **asynchronní** po opravě každé metody. Pokud je použita asynchronní metoda, vzorek Async-await pozastaví místní spuštění, dokud se volání nedokončí. Toto chování umožňuje aktuálnímu vláknu provádět další úkoly, které pomáhají zabránit vzniku kritických bodů výkonu a zvyšují celkovou odezvu aplikace. Další podrobnosti o použití vzoru Async-Await v rozhraní .NET najdete v tématu [Async a Await (C# a Visual Basic)](/previous-versions/hh191443(v=vs.140)).

```csharp
// Create a message to put in the queue
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Add the message asynchronously
await messageQueue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Delete the message asynchronously
await messageQueue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

## <a name="delete-a-queue"></a>Odstranění fronty
Pokud budete chtít odstranit frontu se všemi zprávami, které v ní jsou, zavolejte metodu **Delete** pro objekt fronty.

```csharp
// Delete the queue.
messageQueue.Delete();
```

## <a name="next-steps"></a>Další kroky
[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]