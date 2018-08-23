---
title: Začínáme s queue storage a Visual Studio připojené služby (cloudové služby) | Dokumentace Microsoftu
description: Jak začít používat Azure Queue storage v projektu cloudové služby v sadě Visual Studio po připojení k účtu úložiště pomocí sady Visual Studio připojené služby
services: storage
author: ghogen
manager: douge
ms.assetid: da587aac-5e64-4e9a-8405-44cc1924881d
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: eb924bcfe3e2545cf6666a19bbb3494c11bc3a48
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2018
ms.locfileid: "42055050"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Začínáme s Azure Queue storage a Visual Studio připojené služby (projekty cloudových služeb)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Přehled
Tento článek popisuje, jak začít používat Azure Queue storage v sadě Visual Studio po odkazovat účtu služby Azure storage v projektu cloudové služby pomocí sady Visual Studio nebo vytvořili **přidání připojené služby** dialogového okna.

Vám ukážeme, jak vytvořit frontu v kódu. Vám také ukážeme, jak provádět základní fronty operací, jako je například přidání, úprava, čtení a odstranění fronty zpráv. Ukázky jsou napsané v kódu jazyka C# a použití [Microsoft Azure Storage Client Library pro .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

**Přidání připojené služby** operace nainstaluje příslušné balíčky NuGet pro přístup k Azure storage ve vašem projektu a přidá připojovací řetězec pro účet úložiště pro konfigurační soubory projektu.

* Zobrazit [Začínáme s Azure Queue storage pomocí .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) Další informace o zpracování fronty v kódu.
* Zobrazit [dokumentace ke službě Storage](https://azure.microsoft.com/documentation/services/storage/) obecné informace o službě Azure Storage.
* Zobrazit [dokumentace ke službě Cloud Services](https://azure.microsoft.com/documentation/services/cloud-services/) obecné informace o cloudových službách Azure.
* Zobrazit [ASP.NET](http://www.asp.net) Další informace o programování aplikací ASP.NET.

Azure Queue Storage je služba pro ukládání velkého počtu zpráv, ke které můžete získat přístup z jakéhokoli místa na světě prostřednictvím ověřených volání s využitím protokolu HTTP nebo HTTPS. Zpráva s jednou frontou může mít velikost až 64 kB a jedna fronta můžete obsahovat miliony zpráv, až do dosažení celkové kapacity účtu úložiště.

## <a name="access-queues-in-code"></a>Přístup k frontám v kódu
Pro přístup k frontám v projektech Visual Studio Cloud Services, budete muset zahrnují následující položky do libovolného zdrojového souboru jazyka C#, které přistupují k Azure Queue storage.

1. Ujistěte se, že ty pak uvádějte deklarace oboru názvů v horní části souboru jazyka C# **pomocí** příkazy.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;
2. Získání **CloudStorageAccount** objekt reprezentující informace o vašem účtu úložiště. Chcete-li získat pomocí následujícího kódu připojovací řetězec úložiště a informace o účtu úložiště z konfigurace služby Azure.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
3. Získání **CloudQueueClient** objekt neodkazovali na objekty fronty ve vašem účtu úložiště.  
   
        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
4. Získání **CloudQueue** objektu, který chcete odkazovat na konkrétní fronty.
   
        // Get a reference to a queue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");

**Poznámka:** používat všechny výše uvedený kód před kód v následujících ukázkách.

## <a name="create-a-queue-in-code"></a>Vytvořit frontu v kódu
Vytvořte frontu v kódu, stačí přidat volání **CreateIfNotExists**.

    // Create the CloudQueue if it does not exist
    messageQueue.CreateIfNotExists();

## <a name="add-a-message-to-a-queue"></a>Přidání zprávy do fronty
Chcete-li vložit zprávu do existující fronty, vytvořte nový **CloudQueueMessage** objekt a potom voláním **AddMessage** metody.

A **CloudQueueMessage** objekt můžete vytvořit z řetězce (ve formátu UTF-8) nebo s polem bajtů.

Tady je příklad, který vloží zprávu "Hello, World".

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    messageQueue.AddMessage(message);

## <a name="read-a-message-in-a-queue"></a>Přečtěte si zprávu ve frontě
Pomocí volání metody **PeekMessage** můžete prohlížet zprávy ve frontě, aniž byste je z fronty odebrali.

    // Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

## <a name="read-and-remove-a-message-in-a-queue"></a>Čtení a odebrání zprávy ve frontě
Kód můžete odebrat (frontě) zprávy z fronty ve dvou krocích.

1. Volání **GetMessage** zobrazíte další zprávu ve frontě. Zpráva vrácená metodou **GetMessage** se stane neviditelnou pro jakýkoli jiný kód, který čte zprávy z této fronty. Ve výchozím nastavení tato zpráva zůstává neviditelná po dobu 30 sekund.
2. Chcete-li dokončit odebere zprávu z fronty, zavolejte **DeleteMessage**.

Tento dvoukrokový proces odebrání zprávy zaručuje, aby v případě, že se vašemu kódu nepodaří zprávu zpracovat z důvodu selhání hardwaru nebo softwaru, mohla stejnou zprávu získat jiná instance vašeho kódu a bylo možné to zkusit znovu. Následující kód volá **DeleteMessage** hned po zpracování zprávy.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

    // Process the message in less than 30 seconds

    // Then delete the message.
    await messageQueue.DeleteMessage(retrievedMessage);


## <a name="use-additional-options-to-process-and-remove-queue-messages"></a>Použít další možnosti pro zpracování a odstranění fronty zpráv
Načítání zpráv z fronty si můžete přizpůsobit dvěma způsoby.

* Můžete načíst dávku zpráv (až 32).
* Můžete nastavit časový limit neviditelnosti delší nebo kratší, umožňuje váš kód více nebo méně času na úplné zpracování jednotlivých zpráv. V následujícím příkladu kódu se pomocí metody **GetMessages** získá 20 zpráv v jednom volání. Následně se každá zpráva zpracuje pomocí smyčky **foreach**. Také se pro každou zprávu nastaví časový limit neviditelnosti 5 minut. Pozor, 5minutový časový limit začíná pro všechny zprávy najednou, takže po uplynutí 5 minut od volání metody **GetMessages** pak budou všechny zprávy, které nebyly odstraněny, opět viditelné.

Tady je příklad:

    foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.

        // Then delete the message after processing
        messageQueue.DeleteMessage(message);

    }

## <a name="get-the-queue-length"></a>Získání délky fronty
Podle potřeby můžete získat odhadovaný počet zpráv ve frontě. Metoda **FetchAttributes** požádá Službu front o načtení atributů fronty, včetně počtu zpráv. **ApproximateMethodCount** vlastnost vrací poslední hodnotu načtenou **FetchAttributes** metody, bez volání služby front.

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="use-the-async-await-pattern-with-common-azure-queue-apis"></a>Použití vzoru Async-Await s společné rozhraní API Azure Queue
Tento příklad ukazuje způsob použití vzoru Async-Await s common fronty rozhraními API Azure. Ukázka volá asynchronní verzi každé z daných metod, to můžete vidět **asynchronní** po opravě každé metody. Při použití asynchronní metody async-await vzor místní pozastaví provádění kódu až do dokončení volání. Toto chování umožňuje aktuálnímu vláknu provádět další činnosti, což pomáhá předejít kritické body výkonu a zlepšuje celkovou rychlost reakce vaší aplikace. Další podrobnosti o použití vzoru Async-Await v rozhraní .NET najdete v tématu [Async a Await (C# a Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx).

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

## <a name="next-steps"></a>Další postup
[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]

