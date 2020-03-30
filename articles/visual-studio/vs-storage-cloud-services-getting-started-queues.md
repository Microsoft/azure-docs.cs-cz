---
title: Začínáme s úložištěm front pomocí Sady Visual Studio (cloudové služby)
description: Jak začít používat úložiště Azure Queue v projektu cloudové služby v sadě Visual Studio po připojení k účtu úložiště pomocí připojených služeb Visual Studia
services: storage
author: ghogen
manager: jillfra
ms.assetid: da587aac-5e64-4e9a-8405-44cc1924881d
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 603bb2b9a862ad4ed2cbde63e2d82b9a82fbeaa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298779"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Začínáme s Azure Queue Storage a připojenými službami sady Visual Studio (projekty cloudových služeb)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Přehled
Tento článek popisuje, jak začít používat úložiště fronty Azure ve Visual Studiu po vytvoření nebo odkazování na účet úložiště Azure v projektu cloudových služeb pomocí dialogového okna **Přidat připojené služby** Visual Studia.

Ukážeme vám, jak vytvořit frontu v kódu. Ukážeme vám také, jak provádět základní operace fronty, jako je přidávání, úpravy, čtení a odebírání zpráv fronty. Ukázky jsou zapsány v kódu Jazyka C# a používají [klientskou knihovnu úložiště Microsoft Azure pro rozhraní .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Operace **Přidat připojené služby** nainstaluje příslušné balíčky NuGet pro přístup k úložišti Azure ve vašem projektu a přidá připojovací řetězec pro účet úložiště do konfiguračních souborů projektu.

* Další informace o manipulaci s frontami v kódu najdete v tématu [Začínáme s úložištěm Fronty Azure pomocí rozhraní .NET.](../storage/queues/storage-dotnet-how-to-use-queues.md)
* Obecné informace o Azure Storage najdete v [dokumentaci](https://azure.microsoft.com/documentation/services/storage/) k úložišti.
* Obecné informace o cloudových službách Azure najdete v dokumentaci ke [cloudovým službám.](https://azure.microsoft.com/documentation/services/cloud-services/)
* Další informace o programování aplikací ASP.NET naleznete [v ASP.NET.](https://www.asp.net)

Azure Queue Storage je služba pro ukládání velkého počtu zpráv, ke které můžete získat přístup z jakéhokoli místa na světě prostřednictvím ověřených volání s využitím protokolu HTTP nebo HTTPS. Zpráva s jednou frontou může mít velikost až 64 kB a jedna fronta můžete obsahovat miliony zpráv, až do dosažení celkové kapacity účtu úložiště.

## <a name="access-queues-in-code"></a>Přístup k frontám v kódu
Chcete-li získat přístup k frontám v projektech Visual Studio Cloud Services, musíte zahrnout následující položky do libovolného zdrojového souboru Jazyka C#, který přistupuje k úložišti Azure Queue.

1. Ujistěte se, že deklarace oboru názvů v horní části souboru C# obsahují tyto **příkazy pomocí.**
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;
2. Získejte objekt **CloudStorageAccount,** který představuje informace o vašem účtu úložiště. Pomocí následujícího kódu získáte informace o připojovacím řetězci úložiště a účtu úložiště z konfigurace služby Azure.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
3. Získejte objekt **CloudQueueClient,** který odkazuje na objekty fronty v účtu úložiště.  
   
        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
4. Získejte objekt **CloudQueue,** který odkazuje na konkrétní frontu.
   
        // Get a reference to a queue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");

**POZNÁMKA:** Použijte všechny výše uvedené kód před kódem v následujících ukázkách.

## <a name="create-a-queue-in-code"></a>Vytvoření fronty v kódu
Chcete-li vytvořit frontu v kódu, stačí přidat volání **CreateIfNotExists**.

    // Create the CloudQueue if it does not exist
    messageQueue.CreateIfNotExists();

## <a name="add-a-message-to-a-queue"></a>Přidání zprávy do fronty
Chcete-li vložit zprávu do existující fronty, vytvořte nový objekt **CloudQueueMessage** a pak zavolejte metodu **AddMessage.**

Objekt **CloudQueueMessage** lze vytvořit buď z řetězce (ve formátu UTF-8) nebo bajtového pole.

Zde je příklad, který vloží zprávu 'Hello, World'.

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    messageQueue.AddMessage(message);

## <a name="read-a-message-in-a-queue"></a>Čtení zprávy ve frontě
Pomocí volání metody **PeekMessage** můžete prohlížet zprávy ve frontě, aniž byste je z fronty odebrali.

    // Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

## <a name="read-and-remove-a-message-in-a-queue"></a>Čtení a odebrání zprávy ve frontě
Váš kód můžete odebrat (de-queue) zprávu z fronty ve dvou krocích.

1. Volání **GetMessage** získat další zprávu ve frontě. Zpráva vrácená metodou **GetMessage** se stane neviditelnou pro jakýkoli jiný kód, který čte zprávy z této fronty. Ve výchozím nastavení tato zpráva zůstává neviditelná po dobu 30 sekund.
2. Chcete-li dokončit odebrání zprávy z fronty, volejte **DeleteMessage**.

Tento dvoukrokový proces odebrání zprávy zaručuje, aby v případě, že se vašemu kódu nepodaří zprávu zpracovat z důvodu selhání hardwaru nebo softwaru, mohla stejnou zprávu získat jiná instance vašeho kódu a bylo možné to zkusit znovu. Následující kód volá **DeleteMessage** hned po zpracování zprávy.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

    // Process the message in less than 30 seconds

    // Then delete the message.
    await messageQueue.DeleteMessage(retrievedMessage);


## <a name="use-additional-options-to-process-and-remove-queue-messages"></a>Použití dalších možností zpracování a odebrání zpráv fronty
Načítání zpráv z fronty si můžete přizpůsobit dvěma způsoby.

* Můžete získat dávku zpráv (až 32).
* Můžete nastavit delší nebo kratší časový limit neviditelnosti, což umožňuje kódu více či méně času na úplné zpracování každé zprávy. V následujícím příkladu kódu se pomocí metody **GetMessages** získá 20 zpráv v jednom volání. Následně se každá zpráva zpracuje pomocí smyčky **foreach**. Také se pro každou zprávu nastaví časový limit neviditelnosti 5 minut. Pozor, 5minutový časový limit začíná pro všechny zprávy najednou, takže po uplynutí 5 minut od volání metody **GetMessages** pak budou všechny zprávy, které nebyly odstraněny, opět viditelné.

Tady je příklad:

    foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.

        // Then delete the message after processing
        messageQueue.DeleteMessage(message);

    }

## <a name="get-the-queue-length"></a>Získání délky fronty
Podle potřeby můžete získat odhadovaný počet zpráv ve frontě. Metoda **FetchAttributes** požádá Službu front o načtení atributů fronty, včetně počtu zpráv. Vlastnost **ApproximateMethodCount** vrátí poslední hodnotu načtenou metodou **FetchAttributes** bez volání služby Queue.

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="use-the-async-await-pattern-with-common-azure-queue-apis"></a>Použití vzoru Async-Await se společnými api fronty Azure
Tento příklad ukazuje, jak používat vzor Async-Await s běžnými virtuálními zařízeními fronty Azure. Ukázka volá asynchronní verzi každé z daných metod, to lze vidět **asynchronní** po opravě každé metody. Při použití asynchronní metody async-await vzor pozastaví místní spuštění, dokud volání nedokončí. Toto chování umožňuje aktuální vlákno dělat jinou práci, která pomáhá vyhnout se kritické body výkonu a zlepšuje celkovou odezvu vaší aplikace. Další podrobnosti o použití vzoru Async-Await v rozhraní .NET najdete v tématu [Async a Await (C# a Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx).

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

## <a name="delete-a-queue"></a>Odstranění fronty
Pokud budete chtít odstranit frontu se všemi zprávami, které v ní jsou, zavolejte metodu **Delete** pro objekt fronty.

    // Delete the queue.
    messageQueue.Delete();

## <a name="next-steps"></a>Další kroky
[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]

