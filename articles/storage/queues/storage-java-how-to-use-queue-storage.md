---
title: Používání úložiště Queue z Azure Storage Java
description: Naučte se používat úložiště Queue k vytváření a odstraňování front a vkládání, získávání a odstraňování zpráv pomocí Azure Storage klientské knihovny pro Java.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/08/2016
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: cbrooks
ms.openlocfilehash: 8360d78c8c33f99fa34cfe78e8b9882ee55a5c48
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/10/2020
ms.locfileid: "84655602"
---
# <a name="how-to-use-queue-storage-from-java"></a>Používání úložiště Queue z Javy

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

Tato příručka vám ukáže, jak provádět běžné scénáře pomocí služby Azure Queue Storage. Ukázky jsou napsané v Javě a využívají [sadu SDK služby Azure Storage pro Javu][Azure Storage SDK for Java]. Mezi zahrnuté scénáře patří **vkládání**, **prohlížení**, **získávání**a **odstraňování** zpráv fronty a **vytváření** a **odstraňování** front. Další informace o frontách najdete v části [Další kroky](#next-steps) .

> [!IMPORTANT]
> Tento článek odkazuje na starší verzi Azure Storage klientské knihovny pro jazyk Java. Pokud chcete začít používat nejnovější verzi, přečtěte si [rychlý Start: Klientská knihovna Azure Queue Storage pro Java.](storage-quickstart-queues-java.md)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Vytvoření aplikace Java

V této příručce budete používat funkce úložiště, které se dají spouštět v aplikaci Java lokálně, nebo v kódu běžícím v rámci webové aplikace v Azure.

Pokud to chcete udělat, budete muset nainstalovat Java Development Kit (JDK) a vytvořit účet Azure Storage ve svém předplatném Azure. Až to uděláte, budete muset ověřit, že váš vývojový systém splňuje minimální požadavky a závislosti uvedené v sadě [Azure Storage SDK pro Java][Azure Storage SDK for Java] úložiště na GitHubu. Pokud váš systém splňuje tyto požadavky, můžete postupovat podle pokynů pro stažení a instalaci knihoven Azure Storage pro jazyk Java v systému z tohoto úložiště. Až tyto úlohy dokončíte, budete moct vytvořit aplikaci Java, která používá příklady v tomto článku.

## <a name="configure-your-application-to-access-queue-storage"></a>Konfigurace aplikace pro přístup do úložiště Queue

Přidejte následující příkazy pro import do horní části souboru Java, ve kterém chcete používat rozhraní API služby Azure Storage pro přístup k frontám:

```java
// Include the following imports to use queue APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.queue.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Nastavení připojovacího řetězce služby Azure Storage

Klient úložiště Azure používá připojovací řetězec úložiště k uložení koncových bodů a přihlašovacích údajů pro přístup ke službám správy dat. Při spuštění klientské aplikace musíte zadat připojovací řetězec úložiště v následujícím formátu a jako hodnoty *AccountName* a *AccountKey* použít název a primární přístupový klíč vašeho účtu úložiště uvedené na webu [Azure Portal](https://portal.azure.com). Tento příklad ukazuje deklaraci statického pole pro uložení připojovacího řetězce:

```java
// Define the connection-string with your values.
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

V aplikaci spuštěné v rámci role v Microsoft Azure lze tento řetězec Uložit do konfiguračního souboru služby *ServiceConfiguration. cscfg*a lze k němu přidružit volání metody **RoleEnvironment. getConfigurationSettings** . Tady je příklad získání připojovacího řetězce *StorageConnectionString* z elementu **Settings** (Nastavení) v konfiguračním souboru služby:

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

V následujících ukázkách se předpokládá, že jste pomocí některé z těchto dvou metod získali připojovací řetězec úložiště.

## <a name="how-to-create-a-queue"></a>Postupy: vytvoření fronty
Objekt **CloudQueueClient** umožňuje získat referenční objekty pro fronty. Následující kód vytvoří objekt **CloudQueueClient** . (Poznámka: existují další způsoby, jak vytvořit objekty **CloudStorageAccount** ; Další informace naleznete v tématu **CloudStorageAccount** v referenčních informacích k [sadě SDK Azure Storage klienta].)

Pomocí objektu **CloudQueueClient** Získejte odkaz na frontu, kterou chcete použít. Tuto frontu můžete vytvořit, pokud neexistuje.

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
Pokud chcete vložit zprávu do existující fronty, vytvořte nejdříve novou třídu **CloudQueueMessage**. Dále zavolejte metodu **addMessage** . **CloudQueueMessage** je možné vytvořit buď z řetězce (ve formátu UTF-8), nebo z bajtového pole. Tady je kód, který vytvoří frontu (Pokud neexistuje) a vloží zprávu "Hello, World".

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

## <a name="how-to-peek-at-the-next-message"></a>Postupy: prohlížení další zprávy
Můžete prohlížet zprávy před frontou, aniž byste je museli odebírat z fronty voláním **peekMessage**.

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

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Postupy: Změna obsahu zprávy ve frontě
Podle potřeby můžete změnit obsah zprávy přímo ve frontě. Pokud zpráva představuje pracovní úlohu, mohli byste tuto funkci použít k aktualizaci stavu pracovních úloh. Následující kód aktualizuje zprávy ve frontě o nový obsah a prodlouží časový limit viditelnosti na 60 sekund. Rozšíření časového limitu viditelnosti uloží stav práce přidružené ke zprávě a poskytne klientovi další minutu, aby pokračoval ve zpracování zprávy. Tímto způsobem může sledovat vícekrokového pracovní postupy pro zprávy ve frontě, aniž by bylo nutné v případě, že krok zpracování z důvodu selhání hardwaru nebo softwaru selže, začít znovu od začátku. Obvykle byste udržovali také hodnotu počtu opakování, a pokud by se pokus o zpracování zprávy opakoval více než *n*krát, odstranili byste ji. Je to ochrana proti tomu, aby zpráva při každém pokusu o zpracování nevyvolala chyby aplikace.

Následující ukázka kódu prohledá frontu zpráv, vyhledá první zprávu, která odpovídá "Hello, World" pro obsah, a pak upraví obsah zprávy a ukončí se.

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

Alternativně následující ukázka kódu aktualizuje pouze první viditelnou zprávu ve frontě.

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
Podle potřeby můžete získat odhadovaný počet zpráv ve frontě. Metoda **downloadAttributes** požádá služba front o několik aktuálních hodnot, včetně počtu zpráv ve frontě. Počet je přibližný pouze vzhledem k tomu, že je možné přidat nebo odebrat zprávy poté, co Služba front odpoví na vaši žádost. Metoda **getApproximateMessageCount** vrací poslední hodnotu získanou voláním **downloadAttributes**, bez volání služba front.

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
Váš kód vyřadí zprávu z fronty ve dvou krocích. Když zavoláte **retrieveMessage**, dostanete další zprávu ve frontě. Zpráva vrácená z **retrieveMessage** bude neviditelná pro jakýkoliv jiný kód, který čte zprávy z této fronty. Ve výchozím nastavení tato zpráva zůstává neviditelná po dobu 30 sekund. Chcete-li dokončit odebrání zprávy z fronty, je nutné také volat **deleteMessage**. Tento dvoukrokový proces odebrání zprávy zaručuje, aby v případě, že se vašemu kódu nepodaří zprávu zpracovat z důvodu selhání hardwaru nebo softwaru, mohla stejnou zprávu získat jiná instance vašeho kódu a bylo možné to zkusit znovu. Váš kód volá **deleteMessage** hned po zpracování zprávy.

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

## <a name="additional-options-for-dequeuing-messages"></a>Další možnosti pro vyřazování zpráv do fronty
Načítání zpráv z fronty si můžete přizpůsobit dvěma způsoby. Za prvé si můžete načíst dávku zpráv (až 32). Za druhé si můžete nastavit delší nebo kratší časový limit neviditelnosti, aby měl váš kód více nebo méně času na úplné zpracování jednotlivých zpráv.

Následující příklad kódu používá metodu **retrieveMessages** k získání 20 zpráv v jednom volání. Potom zpracuje každou zprávu pomocí smyčky **for** . Nastaví také časový limit neviditelnosti na pět minut (300 sekund) pro každou zprávu. Pět minut začne u všech zpráv současně, takže až pět minut uplynulo od volání **retrieveMessages**, všechny zprávy, které nebyly odstraněny, budou opět viditelné.

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

## <a name="how-to-list-the-queues"></a>Postupy: výpis front
Chcete-li získat seznam aktuálních front, zavolejte metodu **CloudQueueClient. listQueues ()** , která vrátí kolekci objektů **CloudQueue** .

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
Pokud chcete odstranit frontu a všechny zprávy, které jsou v ní obsažené, zavolejte metodu **deleteIfExists** pro objekt **CloudQueue** .

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

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

> [!NOTE]
> Sada SDK je k dispozici pro vývojáře používající službu Azure Storage na zařízeních s Androidem. Další informace najdete v tématu [Sada SDK služby Azure Storage pro Android][Azure Storage SDK for Android].

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili základní informace o službě Queue Storage, získáte další informace o složitějších úlohách úložiště pomocí těchto odkazů.

* [Sada SDK služby Azure Storage pro Javu][Azure Storage SDK for Java]
* [Referenční informace ke klientské sadě SDK služby Azure Storage][Azure Storage Client SDK Reference]
* [REST API služby Azure Storage][Azure Storage Services REST API]
* [Blog týmu Azure Storage][Azure Storage Team Blog]

[Azure SDK for Java]: https://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Referenční informace ke klientské sadě SDK služby Azure Storage]: https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html
[Azure Storage Services REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
