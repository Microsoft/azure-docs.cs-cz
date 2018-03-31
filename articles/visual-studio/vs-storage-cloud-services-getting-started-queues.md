---
title: Začínáme s fronty úložiště a Visual Studio připojené služby (cloudové služby) | Microsoft Docs
description: Jak začít používat Azure Queue storage v projektu cloudové služby v sadě Visual Studio po připojení k účtu úložiště pomocí sady Visual Studio připojené služby
services: storage
documentationcenter: ''
author: ghogen
manager: douge
editor: ''
ms.assetid: da587aac-5e64-4e9a-8405-44cc1924881d
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: ee4228db093b840940152fcad6dfba793dbc4772
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Začínáme s Azure Queue storage a Visual Studio připojené služby (projekty cloudových služeb)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Přehled
Tento článek popisuje, jak začít používat Azure Queue storage v sadě Visual Studio, po vytvoření a účet úložiště Azure v projektu cloudové služby odkazuje pomocí sady Visual Studio **přidat připojení služby** dialogové okno.

Ukážeme vám postup vytvoření fronty v kódu. Jsme budete také ukazují, jak provést základní fronty operací, jako je přidání, úprava, čtení a odebrání zprávy do fronty. Ukázky jsou napsané v C# – kód a použít [Microsoft Azure Storage Client Library pro .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

**Přidat připojení služby** operaci nainstaluje příslušné balíčky NuGet pro přístup k úložišti Azure ve vašem projektu a přidá připojovací řetězec pro účet úložiště v konfiguračních souborech projektu.

* V tématu [Začínáme s Azure Queue storage pomocí rozhraní .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) Další informace o práci s front v kódu.
* V tématu [úložiště dokumentace](https://azure.microsoft.com/documentation/services/storage/) obecné informace o službě Azure Storage.
* V tématu [cloudové služby dokumentaci](https://azure.microsoft.com/documentation/services/cloud-services/) obecné informace o cloudových služeb Azure.
* V tématu [ASP.NET](http://www.asp.net) Další informace o programování aplikací ASP.NET.

Azure Queue Storage je služba pro ukládání velkého počtu zpráv, ke které můžete získat přístup z jakéhokoli místa na světě prostřednictvím ověřených volání s využitím protokolu HTTP nebo HTTPS. Zpráva s jednou frontou může mít velikost až 64 kB a jedna fronta můžete obsahovat miliony zpráv, až do dosažení celkové kapacity účtu úložiště.

## <a name="access-queues-in-code"></a>Přístup front v kódu
Chcete-li získat přístup k frontám v projektech Visual Studio cloudové služby, zahrnují následující položky pro všechny zdrojové soubory C#, která přistupují k Azure Queue storage.

1. Zajistěte, aby deklarace oboru názvů v horní části souboru C# zahrnují tyto **pomocí** příkazy.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;
2. Získání **CloudStorageAccount** objekt, který reprezentuje informace o účtu úložiště. Použít následující kód k získání připojovací řetězec úložiště a informace o účtu úložiště z konfigurace služby Azure.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
3. Získání **CloudQueueClient** objekt, který má odkazovat na objekty fronty ve vašem účtu úložiště.  
   
        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
4. Získání **CloudQueue** objekt, který má odkazovat na konkrétní fronty.
   
        // Get a reference to a queue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");

**Poznámka:** používat všechny výše uvedené kódu před kód v následující ukázky.

## <a name="create-a-queue-in-code"></a>Vytvoření fronty v kódu
Chcete-li vytvořit frontu v kódu, stačí přidat volání **CreateIfNotExists**.

    // Create the CloudQueue if it does not exist
    messageQueue.CreateIfNotExists();

## <a name="add-a-message-to-a-queue"></a>Přidání zprávy do fronty
Pokud chcete vložit zprávu do existující fronty, vytvořte novou **CloudQueueMessage** objekt a potom volání **AddMessage** metoda.

A **CloudQueueMessage** můžete vytvořit objekt z řetězce (ve formátu UTF-8) nebo pole bajtů.

Tady je příklad, který se vloží zprávu "Hello, World".

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    messageQueue.AddMessage(message);

## <a name="read-a-message-in-a-queue"></a>Čtení zprávy ve frontě
Pomocí volání metody **PeekMessage** můžete prohlížet zprávy ve frontě, aniž byste je z fronty odebrali.

    // Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

## <a name="read-and-remove-a-message-in-a-queue"></a>Přečtěte si a odebrat zprávu ve frontě
Můžete odebrat kódu (zrušte fronty) zprávu z fronty ve dvou krocích.

1. Volání **GetMessage** získat další zprávu ve frontě. Zpráva vrácená metodou **GetMessage** se stane neviditelnou pro jakýkoli jiný kód, který čte zprávy z této fronty. Ve výchozím nastavení tato zpráva zůstává neviditelná po dobu 30 sekund.
2. Chcete-li dokončit odebere ji z fronty, volejte **DeleteMessage**.

Tento dvoukrokový proces odebrání zprávy zaručuje, aby v případě, že se vašemu kódu nepodaří zprávu zpracovat z důvodu selhání hardwaru nebo softwaru, mohla stejnou zprávu získat jiná instance vašeho kódu a bylo možné to zkusit znovu. Následující kód volání **DeleteMessage** pravým po zpracování zprávy.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

    // Process the message in less than 30 seconds

    // Then delete the message.
    await messageQueue.DeleteMessage(retrievedMessage);


## <a name="use-additional-options-to-process-and-remove-queue-messages"></a>Použít další možnosti pro zpracování a odebrat zprávy fronty
Načítání zpráv z fronty si můžete přizpůsobit dvěma způsoby.

* Můžete načíst dávku zpráv (až 32).
* Můžete nastavit časový limit neviditelnosti delší nebo kratší, aby měl váš kód více nebo méně času na úplné zpracování jednotlivých zpráv. V následujícím příkladu kódu se pomocí metody **GetMessages** získá 20 zpráv v jednom volání. Následně se každá zpráva zpracuje pomocí smyčky **foreach**. Také se pro každou zprávu nastaví časový limit neviditelnosti 5 minut. Pozor, 5minutový časový limit začíná pro všechny zprávy najednou, takže po uplynutí 5 minut od volání metody **GetMessages** pak budou všechny zprávy, které nebyly odstraněny, opět viditelné.

Tady je příklad:

    foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.

        // Then delete the message after processing
        messageQueue.DeleteMessage(message);

    }

## <a name="get-the-queue-length"></a>Získání délky fronty
Podle potřeby můžete získat odhadovaný počet zpráv ve frontě. Metoda **FetchAttributes** požádá Službu front o načtení atributů fronty, včetně počtu zpráv. **ApproximateMethodCount** vlastnost vrací poslední hodnotu načtenou **FetchAttributes** metoda bez volání služby front.

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="use-the-async-await-pattern-with-common-azure-queue-apis"></a>Použití vzoru Async-Await s běžné fronty rozhraní API služby Azure
Tento příklad ukazuje způsob použití vzoru Async-Await s běžné fronty rozhraní API služby Azure. Ukázka volá asynchronní verzi každé z daných metod, to může zobrazit **asynchronní** po opravy jednotlivých metod. Při použití asynchronní metody slouží async-await vzor pozastaví místní spuštění, dokud se nedokončí volání. Toto chování umožňuje aktuálnímu vláknu provádět další činnosti, což přispívá k vyhnout se kritickým bodům výkonu a zlepšuje celkovou rychlost reakce aplikace. Další podrobnosti o použití vzoru Async-Await v rozhraní .NET najdete v tématu [Async a Await (C# a Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx).

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

