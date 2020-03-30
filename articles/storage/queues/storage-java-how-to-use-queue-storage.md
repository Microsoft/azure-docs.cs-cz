---
title: Jak používat úložiště front z Javy – Azure Storage
description: Naučte se používat úložiště front k vytváření a odstraňování front a vkládání, přijím a odstraňování zpráv pomocí klientské knihovny Azure Storage pro Jazyk Java.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/08/2016
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: 7658b8541e7a79a5e547a6649b35681446e34b0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067132"
---
# <a name="how-to-use-queue-storage-from-java"></a>Používání úložiště Queue z Javy

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

Tato příručka vám ukáže, jak provádět běžné scénáře pomocí služby úložiště Fronty Azure. Ukázky jsou napsané v Javě a využívají [sadu SDK služby Azure Storage pro Javu][Azure Storage SDK for Java]. Zahrnuté scénáře zahrnují **vkládání**, **prohlížení**, **získávání**a **odstranění** zpráv fronty a **také vytváření** a **mazání** front. Další informace o frontách naleznete v části [Další kroky.](#next-steps)

> [!NOTE]
> Sada SDK je k dispozici pro vývojáře používající službu Azure Storage na zařízeních s Androidem. Další informace najdete v tématu [Sada SDK služby Azure Storage pro Android][Azure Storage SDK for Android].

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Vytvoření aplikace Java

V této příručce budete používat funkce úložiště, které lze spustit v rámci aplikace Java místně nebo v kódu spuštěném ve webové aplikaci v Azure.

Chcete-li tak učinit, budete muset nainstalovat Java Development Kit (JDK) a vytvořit účet úložiště Azure v předplatném Azure. Jakmile tak učiníte, budete muset ověřit, že váš vývojový systém splňuje minimální požadavky a závislosti, které jsou uvedeny v [úložišti Azure Storage SDK pro Java][Azure Storage SDK for Java] na GitHubu. Pokud váš systém tyto požadavky splňuje, můžete postupovat podle pokynů pro stažení a instalaci knihoven úložišť Azure pro Javu do vašeho systému z tohoto úložiště. Po dokončení těchto úkolů budete moci vytvořit aplikaci Java, která používá příklady v tomto článku.

## <a name="configure-your-application-to-access-queue-storage"></a>Konfigurace aplikace pro přístup k úložišti fronty

Přidejte následující příkazy importu do horní části souboru Jazyka Java, kde chcete použít azure storage API pro přístup do fronty:

```java
// Include the following imports to use queue APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.queue.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Nastavení připojovacího řetězce úložiště Azure

Klient úložiště Azure používá připojovací řetězec úložiště k uložení koncových bodů a přihlašovacích údajů pro přístup ke službám správy dat. Při spuštění klientské aplikace musíte zadat připojovací řetězec úložiště v následujícím formátu a jako hodnoty *AccountName* a *AccountKey* použít název a primární přístupový klíč vašeho účtu úložiště uvedené na webu [Azure Portal](https://portal.azure.com). Tento příklad ukazuje deklaraci statického pole pro uložení připojovacího řetězce:

```java
// Define the connection-string with your values.
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

V aplikaci spuštěné v rámci role v Microsoft Azure může být tento řetězec uložen v konfiguračním souboru služby *ServiceConfiguration.cscfg*a lze k němu přistupovat pomocí volání metody **RoleEnvironment.getConfigurationSettings.** Tady je příklad získání připojovacího řetězce *StorageConnectionString* z elementu **Settings** (Nastavení) v konfiguračním souboru služby:

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

V následujících ukázkách se předpokládá, že jste pomocí některé z těchto dvou metod získali připojovací řetězec úložiště.

## <a name="how-to-create-a-queue"></a>Postup: Vytvoření fronty
Objekt **CloudQueueClient** umožňuje získat referenční objekty pro fronty. Následující kód vytvoří objekt **CloudQueueClient.** (Poznámka: Existují další způsoby, jak vytvořit objekty **CloudStorageAccount;** další informace naleznete v tématu **CloudStorageAccount** v [referenční příručce sktosadí klienta úložiště Azure].)

Pomocí objektu **CloudQueueClient** získáte odkaz na frontu, kterou chcete použít. Frontu můžete vytvořit, pokud neexistuje.

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

## <a name="how-to-add-a-message-to-a-queue"></a>Postup: Přidání zprávy do fronty
Pokud chcete vložit zprávu do existující fronty, vytvořte nejdříve novou třídu **CloudQueueMessage**. Dále zavolejte metodu **addMessage.** **CloudQueueMessage** je možné vytvořit buď z řetězce (ve formátu UTF-8), nebo z bajtového pole. Zde je kód, který vytvoří frontu (pokud neexistuje) a vloží zprávu "Hello, World".

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

## <a name="how-to-peek-at-the-next-message"></a>Postup: Náhled na další zprávu
Zprávu můžete nahlédnout před frontou, aniž byste ji odebrali z fronty voláním **peekMessage**.

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

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Postup: Změna obsahu zprávy ve frontě
Podle potřeby můžete změnit obsah zprávy přímo ve frontě. Pokud zpráva představuje pracovní úlohu, mohli byste tuto funkci použít k aktualizaci stavu pracovních úloh. Následující kód aktualizuje zprávy ve frontě o nový obsah a prodlouží časový limit viditelnosti na 60 sekund. Rozšíření časového limitu viditelnosti šetří stav práce spojené se zprávou a dává klientovi další minutu pokračovat v práci na zprávě. Tímto způsobem může sledovat vícekrokového pracovní postupy pro zprávy ve frontě, aniž by bylo nutné v případě, že krok zpracování z důvodu selhání hardwaru nebo softwaru selže, začít znovu od začátku. Obvykle byste udržovali také hodnotu počtu opakování, a pokud by se pokus o zpracování zprávy opakoval více než *n*krát, odstranili byste ji. Je to ochrana proti tomu, aby zpráva při každém pokusu o zpracování nevyvolala chyby aplikace.

Následující ukázka kódu prohledává frontu zpráv, vyhledá první zprávu, která odpovídá obsahu "Hello, World", pak upraví obsah zprávy a ukončí.

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

Alternativně následující ukázkový kód aktualizuje pouze první viditelné zprávy ve frontě.

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

## <a name="how-to-get-the-queue-length"></a>Postup: Získání délky fronty
Podle potřeby můžete získat odhadovaný počet zpráv ve frontě. Metoda **downloadAttributes** požádá službu Queue o několik aktuálních hodnot, včetně počtu zpráv, které jsou ve frontě. Počet je pouze přibližný, protože zprávy mohou být přidány nebo odebrány poté, co služba Fronta odpoví na váš požadavek. Metoda **getApproximateMessageCount** vrátí poslední hodnotu načtenou voláním **downloadAttributes**bez volání služby Queue.

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

## <a name="how-to-dequeue-the-next-message"></a>Postup: Vyřazení další zprávy z fronty
Váš kód dequeues zprávu z fronty ve dvou krocích. Při volání **retrieveMessage**, zobrazí se další zpráva ve frontě. Zpráva vrácená z **retrieveMessage** stane neviditelné pro jakýkoli jiný kód čtení zpráv z této fronty. Ve výchozím nastavení tato zpráva zůstává neviditelná po dobu 30 sekund. Chcete-li dokončit odebrání zprávy z fronty, musíte také zavolat **deleteMessage**. Tento dvoukrokový proces odebrání zprávy zaručuje, aby v případě, že se vašemu kódu nepodaří zprávu zpracovat z důvodu selhání hardwaru nebo softwaru, mohla stejnou zprávu získat jiná instance vašeho kódu a bylo možné to zkusit znovu. Váš kód volá **deleteMessage** hned po zpracování zprávy.

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

## <a name="additional-options-for-dequeuing-messages"></a>Další možnosti pro dequeuing zpráv
Načítání zpráv z fronty si můžete přizpůsobit dvěma způsoby. Za prvé si můžete načíst dávku zpráv (až 32). Za druhé si můžete nastavit delší nebo kratší časový limit neviditelnosti, aby měl váš kód více nebo méně času na úplné zpracování jednotlivých zpráv.

Následující příklad kódu používá **metodu retrieveMessages** k získání 20 zpráv v jednom volání. Pak zpracovává každou zprávu pomocí **smyčky for.** Také nastaví časový limit neviditelnosti na pět minut (300 sekund) pro každou zprávu. Pět minut spustit pro všechny zprávy ve stejnou dobu, takže po uplynutí pěti minut od volání **načístMessages**, všechny zprávy, které nebyly odstraněny se znovu zobrazí.

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

## <a name="how-to-list-the-queues"></a>Postup: Seznam front
Chcete-li získat seznam aktuálních front, zavolejte metodu **CloudQueueClient.listQueue(),** která vrátí kolekci objektů **CloudQueue.**

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

## <a name="how-to-delete-a-queue"></a>Postup: Odstranění fronty
Chcete-li odstranit frontu a všechny zprávy v ní obsažené, zavolejte metodu **deleteIfExists** na objektu **CloudQueue.**

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

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili základy úložiště front, postupujte podle těchto odkazů a získejte další informace o složitějších úlohách úložiště.

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
