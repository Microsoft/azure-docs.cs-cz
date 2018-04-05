---
title: Používání úložiště Queue z Javy | Microsoft Docs
description: Naučte se používat službu front Azure k vytváření a odstraňování front a vložit, získání a odstranění zprávy. Ukázky napsanou v jazyce Java.
services: storage
documentationcenter: java
author: roygara
manager: jeconnoc
editor: tysonn
ms.assetid: 68cecc8e-38c9-4a24-99e8-cb722bc63cf9
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 12/08/2016
ms.author: rogarana
ms.openlocfilehash: cd79a21fedca1c21c2e4a65394d424f1590f32c3
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="how-to-use-queue-storage-from-java"></a>Používání úložiště Queue z Javy
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="overview"></a>Přehled
Tento průvodce vám ukáže, jak provádět běžné scénáře s využitím služby Azure Queue storage. Ukázky jsou napsané v jazyce Java a použít [sada SDK úložiště Azure pro jazyk Java][Azure Storage SDK for Java]. Pokryté scénáře zahrnují **vkládání**, **prohlížení**, **získávání**, a **odstraňování** fronty zpráv, a také **vytváření** a **odstraňování** fronty. Další informace o frontách najdete v článku [další kroky](#Next-Steps) části.

Poznámka: Sada SDK je k dispozici pro vývojáře, kteří na zařízení se systémem Android používají Azure Storage. Další informace najdete v tématu [sada SDK úložiště Azure pro Android][Azure Storage SDK for Android].

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Vytvoření aplikace Java
V této příručce bude používat funkce úložiště, které lze spustit v rámci aplikace Java místně nebo v kódu běžící v rámci webovou roli nebo role pracovního procesu v Azure.

V takovém případě budete muset nainstalovat Java Development Kit (JDK) a vytvořit účet úložiště Azure ve vašem předplatném Azure. Jakmile provedete, budete muset ověřit, jestli váš vývojový systém splňuje minimální požadavky a závislosti, které jsou uvedeny v [sada SDK úložiště Azure pro jazyk Java] [ Azure Storage SDK for Java] úložišti na Githubu. Pokud váš systém splňuje tyto požadavky, můžete podle pokynů ke stažení a instalaci knihovny úložiště Azure pro jazyk Java systému z tohoto úložiště. Po dokončení těchto úloh, bude moci vytvořit aplikaci Java, která používá v příkladech v tomto článku.

## <a name="configure-your-application-to-access-queue-storage"></a>Konfigurace aplikace pro přístup k úložišti fronty
Přidejte následující příkazy pro import do horní části souboru Java, ve které chcete používat úložiště Azure rozhraní API pro přístup k fronty:

```java
// Include the following imports to use queue APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.queue.*;
```

## <a name="setup-an-azure-storage-connection-string"></a>Instalační program připojovací řetězec úložiště Azure
Klienta Azure storage používá připojovací řetězec úložiště k ukládání koncových bodů a pověření pro přístup ke službám dat správy. Když spustíte v aplikaci klienta, je nutné zadat připojovací řetězec úložiště v následujícím formátu, pomocí názvu účtu úložiště a primární přístupový klíč pro účet úložiště uvedené v [portálu Azure](https://portal.azure.com) pro *AccountName* a *AccountKey* hodnoty. Tento příklad ukazuje, jak můžou deklarovat statické pole pro uložení připojovací řetězec:

```java
// Define the connection-string with your values.
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

Ve spuštěné aplikace v rámci role ve službě Microsoft Azure, můžete tento řetězec uložené v konfiguračním souboru služby *souboru ServiceConfiguration.cscfg*a je přístupný pomocí volání **RoleEnvironment.getConfigurationSettings** metoda. Tady je příklad získávání připojovacího řetězce z **nastavení** element s názvem *StorageConnectionString* v konfiguračním souboru služby:

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

Následující ukázky předpokládejme, že používáte jednu z těchto dvou metod k získání připojovacího řetězce úložiště.

## <a name="how-to-create-a-queue"></a>Postupy: vytvoření fronty
A **CloudQueueClient** objektu umožňuje získat odkaz na objekty pro fronty. Následující kód vytvoří **CloudQueueClient** objektu. (Poznámka: Chcete-li vytvořit další způsoby **CloudStorageAccount** objekty; Další informace najdete v tématu **CloudStorageAccount** v [odkaz SDK klienta úložiště Azure].)

Použít **CloudQueueClient** objekt, který chcete získat odkaz na frontu, kterou chcete použít. Můžete vytvořit frontu, pokud neexistuje.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

   // Create the queue client.
   CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

   // Retrieve a reference to a queue.
   CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Create the queue if it doesn't already exist.
   queue.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-add-a-message-to-a-queue"></a>Postupy: Přidání zprávy do fronty
Pokud chcete vložit zprávu do existující fronty, vytvořte nejdříve novou třídu **CloudQueueMessage**. Pak zavolejte **addMessage** metoda. A **CloudQueueMessage** lze vytvořit z řetězce (ve formátu UTF-8) nebo pole bajtů. Tady je kód, který vytvoří frontu (pokud neexistuje) a vloží zprávu "Hello, World".

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.createIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.addMessage(message);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-peek-at-the-next-message"></a>Postupy: zobrazení náhledu další zprávy
Můžete prohlížet zprávy ve frontě bez odebere ji z fronty voláním **peekMessage**.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Peek at the next message.
    CloudQueueMessage peekedMessage = queue.peekMessage();

    // Output the message value.
    if (peekedMessage != null)
    {
      System.out.println(peekedMessage.getMessageContentAsString());
   }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Postupy: Změna obsahu zpráv zařazených ve frontě
Podle potřeby můžete změnit obsah zprávy přímo ve frontě. Pokud zpráva představuje pracovní úlohu, mohli byste tuto funkci použít k aktualizaci stavu pracovních úloh. Následující kód aktualizuje zprávy ve frontě o nový obsah a prodlouží časový limit viditelnosti na 60 sekund. Uloží se tím stav práce spojený se zprávou a klient získá další minutu, aby mohl pokračovat ve zpracování zprávy. Tímto způsobem může sledovat vícekrokového pracovní postupy pro zprávy ve frontě, aniž by bylo nutné v případě, že krok zpracování z důvodu selhání hardwaru nebo softwaru selže, začít znovu od začátku. Obvykle byste udržovali také hodnotu počtu opakování, a pokud by se pokus o zpracování zprávy opakoval více než *n*krát, odstranili byste ji. Je to ochrana proti tomu, aby zpráva při každém pokusu o zpracování nevyvolala chyby aplikace.

Následující ukázka hledání kódu prostřednictvím fronty zpráv, vyhledá první zprávu, která odpovídá "Hello, World" pro obsah, pak upraví obsahu zprávy a ukončí.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // The maximum number of messages that can be retrieved is 32.
    final int MAX_NUMBER_OF_MESSAGES_TO_PEEK = 32;

    // Loop through the messages in the queue.
    for (CloudQueueMessage message : queue.retrieveMessages(MAX_NUMBER_OF_MESSAGES_TO_PEEK,1,null,null))
    {
        // Check for a specific string.
        if (message.getMessageContentAsString().equals("Hello, World"))
        {
            // Modify the content of the first matching message.
            message.setMessageContent("Updated contents.");
            // Set it to be visible in 30 seconds.
            EnumSet<MessageUpdateFields> updateFields =
                EnumSet.of(MessageUpdateFields.CONTENT,
                MessageUpdateFields.VISIBILITY);
            // Update the message.
            queue.updateMessage(message, 30, updateFields, null, null);
            break;
        }
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

Následující ukázka kódu Alternativně aktualizuje pouze první viditelné zprávu ve frontě.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage message = queue.retrieveMessage();

    if (message != null)
    {
        // Modify the message content.
        message.setMessageContent("Updated contents.");
        // Set it to be visible in 60 seconds.
        EnumSet<MessageUpdateFields> updateFields =
            EnumSet.of(MessageUpdateFields.CONTENT,
            MessageUpdateFields.VISIBILITY);
        // Update the message.
        queue.updateMessage(message, 60, updateFields, null, null);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-get-the-queue-length"></a>Postupy: získání délky fronty
Podle potřeby můžete získat odhadovaný počet zpráv ve frontě. **DownloadAttributes** metoda požádá službu front několik aktuální hodnoty, včetně počet jsou počet zpráv ve frontě. Počet je pouze přibližné, protože zprávy můžete přidat nebo odebrat po služby front odpoví na žádost. **GetApproximateMessageCount** metoda vrátí poslední hodnotu načtenou volání **downloadAttributes**, bez volání služby front.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Download the approximate message count from the server.
    queue.downloadAttributes();

    // Retrieve the newly cached approximate message count.
    long cachedMessageCount = queue.getApproximateMessageCount();

    // Display the queue length.
    System.out.println(String.format("Queue length: %d", cachedMessageCount));
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-dequeue-the-next-message"></a>Postupy: vyřazení další zprávy z fronty
Váš kód dequeues zprávu z fronty ve dvou krocích. Při volání **retrieveMessage**, získáte další zprávu ve frontě. Zpráva vrácená metodou **retrieveMessage** stane neviditelnou pro jakýkoli jiný kód čtení zpráv z této fronty. Ve výchozím nastavení tato zpráva zůstává neviditelná po dobu 30 sekund. K dokončení odebrání zprávy z fronty, musíte také zavolat **deleteMessage**. Tento dvoukrokový proces odebrání zprávy zaručuje, aby v případě, že se vašemu kódu nepodaří zprávu zpracovat z důvodu selhání hardwaru nebo softwaru, mohla stejnou zprávu získat jiná instance vašeho kódu a bylo možné to zkusit znovu. Váš kód zavolá metodu **deleteMessage** pravým po zpracování zprávy.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage retrievedMessage = queue.retrieveMessage();

    if (retrievedMessage != null)
    {
        // Process the message in less than 30 seconds, and then delete the message.
        queue.deleteMessage(retrievedMessage);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="additional-options-for-dequeuing-messages"></a>Další možnosti pro vyřazení zprávy
Načítání zpráv z fronty si můžete přizpůsobit dvěma způsoby. Za prvé si můžete načíst dávku zpráv (až 32). Za druhé si můžete nastavit delší nebo kratší časový limit neviditelnosti, aby měl váš kód více nebo méně času na úplné zpracování jednotlivých zpráv.

Následující příklad kódu používá **retrieveMessages** metoda získá 20 zpráv v jednom volání. Pak se každá zpráva zpracuje pomocí **pro** smyčky. Také nastaví časový limit neviditelnosti 5 minut (300 sekund) pro každou zprávu. Všimněte si, že za pět minut spustí pro všechny zprávy ve stejnou dobu, takže když pět minut předané od volání **retrieveMessages**, všechny zprávy, které nebyly odstraněny, opět viditelné.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
    for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
        // Do processing for all messages in less than 5 minutes,
        // deleting each message after processing.
        queue.deleteMessage(message);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-list-the-queues"></a>Postupy: seznam fronty
Chcete-li získat seznam aktuální front, zavolejte **CloudQueueClient.listQueues()** metodu, která vrátí kolekci **CloudQueue** objekty.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient =
        storageAccount.createCloudQueueClient();

    // Loop through the collection of queues.
    for (CloudQueue queue : queueClient.listQueues())
    {
        // Output each queue name.
        System.out.println(queue.getName());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-delete-a-queue"></a>Postupy: odstranění fronty
Chcete-li odstranit frontu se všemi zprávami, které v ní, zavolejte **deleteIfExists** metodu **CloudQueue** objektu.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Delete the queue if it exists.
    queue.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="next-steps"></a>Další postup
Teď, když jste se naučili základy používání služby queue storage, postupujte podle následujících odkazech na další informace o složitějších úlohách úložiště.

* [Úložiště Azure SDK pro jazyk Java][Azure Storage SDK for Java]
* [Referenční informace sady SDK úložiště Azure klienta][odkaz SDK klienta úložiště Azure]
* [REST API služby Azure Storage][Azure Storage Services REST API]
* [Blog týmu Azure Storage][Azure Storage Team Blog]

[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[odkaz SDK klienta úložiště Azure]: http://dl.windowsazure.com/storage/javadoc/
[Azure Storage Services REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
