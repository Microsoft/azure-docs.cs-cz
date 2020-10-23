---
title: Používání úložiště Queue z Azure Storage Java
description: Naučte se používat úložiště Queue k vytváření a odstraňování front. Naučte se vkládat, prohlížet, získávat a odstraňovat zprávy pomocí Azure Storage klientské knihovny pro Java.
author: mhopkins-msft
ms.custom: devx-track-java
ms.author: mhopkins
ms.date: 08/19/2020
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.openlocfilehash: 2f61fef58485a905b96bdada32b915106e60d1a8
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92425125"
---
# <a name="how-to-use-queue-storage-from-java"></a>Používání úložiště Queue z Javy

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Přehled

V této příručce se dozvíte, jak pomocí služby Azure Queue Storage používat kód pro běžné scénáře. Ukázky jsou napsané v Javě a využívají [sadu SDK služby Azure Storage pro Javu][Azure Storage SDK for Java]. Mezi scénáře patří **vkládání**, **prohlížení**, **získávání**a **odstraňování** zpráv fronty. Kód pro **vytváření** a **odstraňování** front je také pokrytý. Další informace o frontách najdete v části [Další kroky](#next-steps) .

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Vytvoření aplikace Java

# <a name="java-v12"></a>[Java V12](#tab/java)

Nejdřív ověřte, že váš vývojový systém splňuje požadavky uvedené v [klientské knihovně Azure Queue Storage pro Java V12](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue).

Vytvoření aplikace Java s názvem *Queues-to-V12*:

1. V okně konzoly (například cmd, PowerShell nebo bash) použijte Maven k vytvoření nové konzolové aplikace s názvem *Queues-to-V12*. Zadáním následujícího příkazu **MVN** vytvořte "Hello World!" Projekt Java.

   ```bash
    mvn archetype:generate \
        --define interactiveMode=n \
        --define groupId=com.queues.howto \
        --define artifactId=queues-howto-v12 \
        --define archetypeArtifactId=maven-archetype-quickstart \
        --define archetypeVersion=1.4
   ```

   ```powershell
    mvn archetype:generate `
        --define interactiveMode=n `
        --define groupId=com.queues.howto `
        --define artifactId=queues-howto-v12 `
        --define archetypeArtifactId=maven-archetype-quickstart `
        --define archetypeVersion=1.4
    ```

1. Výstup z generování projektu by měl vypadat přibližně takto:

    ```console
    [INFO] Scanning for projects...
    [INFO]
    [INFO] ------------------< org.apache.maven:standalone-pom >-------------------
    [INFO] Building Maven Stub Project (No POM) 1
    [INFO] --------------------------------[ pom ]---------------------------------
    [INFO]
    [INFO] >>> maven-archetype-plugin:3.1.2:generate (default-cli) > generate-sources @ standalone-pom >>>
    [INFO]
    [INFO] <<< maven-archetype-plugin:3.1.2:generate (default-cli) < generate-sources @ standalone-pom <<<
    [INFO]
    [INFO]
    [INFO] --- maven-archetype-plugin:3.1.2:generate (default-cli) @ standalone-pom ---
    [INFO] Generating project in Batch mode
    [INFO] ----------------------------------------------------------------------------
    [INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
    [INFO] ----------------------------------------------------------------------------
    [INFO] Parameter: groupId, Value: com.queues.howto
    [INFO] Parameter: artifactId, Value: queues-howto-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.howto
    [INFO] Parameter: packageInPathFormat, Value: com/queues/howto
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.howto
    [INFO] Parameter: groupId, Value: com.queues.howto
    [INFO] Parameter: artifactId, Value: queues-howto-v12
    [INFO] Project created from Archetype in dir: C:\queues\queues-howto-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  6.775 s
    [INFO] Finished at: 2020-08-17T15:27:31-07:00
    [INFO] ------------------------------------------------------------------------
        ```

1. Switch to the newly created *queues-howto-v12* directory.

   ```console
   cd queues-howto-v12
   ```

### <a name="install-the-package"></a>Instalace balíčku

Otevřete *pom.xml* soubor v textovém editoru. Přidejte následující prvek závislosti do skupiny závislostí.

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-queue</artifactId>
  <version>12.6.0</version>
</dependency>
```

# <a name="java-v8"></a>[Java V8](#tab/java8)

Nejdřív ověřte, že váš vývojový systém splňuje požadavky uvedené v sadě [Azure Storage SDK pro Java V8](https://github.com/azure/azure-storage-java). Postupujte podle pokynů ke stažení a instalaci knihoven Azure Storage pro jazyk Java. Pak můžete vytvořit aplikaci Java pomocí příkladů v tomto článku.

---

## <a name="configure-your-application-to-access-queue-storage"></a>Konfigurace aplikace pro přístup do úložiště Queue

Přidejte následující příkazy pro import do horní části souboru Java, ve kterém chcete používat rozhraní API služby Azure Storage pro přístup k frontám:

# <a name="java-v12"></a>[Java V12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ImportStatements":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

```java
// Include the following imports to use queue APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.queue.*;
```

---

## <a name="set-up-an-azure-storage-connection-string"></a>Nastavení připojovacího řetězce služby Azure Storage

Klient Azure Storage používá připojovací řetězec úložiště pro přístup ke službám pro správu dat. Získejte název a primární přístupový klíč pro váš účet úložiště uvedený v [Azure Portal](https://portal.azure.com). Použijte je jako hodnoty *účtu* a *AccountKey* v připojovacím řetězci. Tento příklad ukazuje deklaraci statického pole pro uložení připojovacího řetězce:

# <a name="java-v12"></a>[Java V12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ConnectionString":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

```java
// Define the connection-string with your values.
final String storageConnectionString =
    "DefaultEndpointsProtocol=https;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```
Tento řetězec můžete uložit do konfiguračního souboru služby s názvem *ServiceConfiguration. cscfg*. Pro aplikaci spuštěnou v rámci Microsoft Azure role přistoupit k připojovacímu řetězci voláním **RoleEnvironment. getConfigurationSettings**. Tady je příklad získání připojovacího řetězce z elementu **Nastavení** s názvem *StorageConnectionString*:

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

---

Následující ukázky předpokládají, že máte objekt **String** , který obsahuje připojovací řetězec úložiště.

## <a name="how-to-create-a-queue"></a>Postupy: vytvoření fronty

# <a name="java-v12"></a>[Java V12](#tab/java)

Objekt **QueueClient** obsahuje operace pro interakci s frontou. Následující kód vytvoří objekt **QueueClient** . Pomocí objektu **QueueClient** vytvořte frontu, kterou chcete použít.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_CreateQueue":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

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

---

## <a name="how-to-add-a-message-to-a-queue"></a>Postupy: Přidání zprávy do fronty

# <a name="java-v12"></a>[Java V12](#tab/java)

Chcete-li vložit zprávu do existující fronty, zavolejte metodu **SendMessage** . Zpráva může být buď řetězec (ve formátu UTF-8), nebo pole bajtů. Zde je kód, který pošle řetězcovou zprávu do fronty.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_AddMessage":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

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

---

## <a name="how-to-peek-at-the-next-message"></a>Postupy: prohlížení další zprávy

Můžete prohlížet zprávy před frontou, aniž byste je museli odebírat z fronty voláním **peekMessage**.

# <a name="java-v12"></a>[Java V12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_PeekMessage":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

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

---

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Postupy: Změna obsahu zprávy ve frontě

Podle potřeby můžete změnit obsah zprávy přímo ve frontě. Pokud zpráva představuje pracovní úlohu, můžete tuto funkci použít k aktualizaci stavu. Následující kód aktualizuje zprávu fronty o nový obsah a nastaví časový limit viditelnosti na prodloužení dalších 30 sekund. Prodloužení časového limitu viditelnosti zadává klientovi další 30 sekund, než bude pokračovat ve zpracování zprávy. Můžete také zachovat počet opakování. Pokud se zpráva znovu pokusí o více než *n* krát, byste ji odstranili. Tento scénář je chráněn proti zprávě, která při každém zpracování vyvolá chybu aplikace.

# <a name="java-v12"></a>[Java V12](#tab/java)

Následující ukázka kódu prohledá frontu zpráv, vyhledá první obsah zprávy, který odpovídá hledanému řetězci, upraví obsah zprávy a ukončí.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_UpdateSearchMessage":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

Následující ukázka kódu prohledá frontu zpráv, vyhledá první obsah zprávy, který odpovídá "Hello, World", upraví obsah zprávy a ukončí.

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

---

Následující ukázka kódu aktualizuje pouze první viditelnou zprávu ve frontě.

# <a name="java-v12"></a>[Java V12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_UpdateFirstMessage":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

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

---

## <a name="how-to-get-the-queue-length"></a>Postupy: získání délky fronty

Podle potřeby můžete získat odhadovaný počet zpráv ve frontě.

# <a name="java-v12"></a>[Java V12](#tab/java)

Metoda **GetProperties** požádá služba front o několik aktuálních hodnot. Jedna z hodnot je počet zpráv ve frontě. Počet je jenom přibližný, protože po vaší žádosti je možné přidat nebo odebrat zprávy. Metoda **getApproximateMessageCount** vrací poslední hodnotu získanou voláním metody **GetProperties**bez volání služba front.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_GetQueueLength":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

Metoda **downloadAttributes** požádá o služba front pro několik aktuálních hodnot. Jedna z hodnot je počet zpráv ve frontě. Počet je jenom přibližný, protože po vaší žádosti je možné přidat nebo odebrat zprávy. Metoda **getApproximateMessageCount** vrací poslední hodnotu získanou voláním **downloadAttributes**, bez volání služba front.

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

---

## <a name="how-to-dequeue-the-next-message"></a>Postupy: vyřazení další zprávy z fronty

# <a name="java-v12"></a>[Java V12](#tab/java)

Váš kód vyřadí zprávu z fronty ve dvou krocích. Když zavoláte **receiveMessage**, dostanete další zprávu ve frontě. Zpráva vrácená z **receiveMessage** bude neviditelná pro jakýkoliv jiný kód, který čte zprávy z této fronty. Ve výchozím nastavení tato zpráva zůstává neviditelná po dobu 30 sekund. Chcete-li dokončit odebrání zprávy z fronty, je nutné také volat **deleteMessage**. Pokud kód nedokáže zpracovat zprávu, tento proces se dvěma kroky zajistí, že můžete získat stejnou zprávu a zkusit to znovu. Váš kód volá **deleteMessage** hned po zpracování zprávy.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DequeueMessage":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

Váš kód vyřadí zprávu z fronty ve dvou krocích. Když zavoláte **retrieveMessage**, dostanete další zprávu ve frontě. Zpráva vrácená z **retrieveMessage** bude neviditelná pro jakýkoliv jiný kód, který čte zprávy z této fronty. Ve výchozím nastavení tato zpráva zůstává neviditelná po dobu 30 sekund. Chcete-li dokončit odebrání zprávy z fronty, je nutné také volat **deleteMessage**. Pokud kód nedokáže zpracovat zprávu, tento proces se dvěma kroky zajistí, že můžete získat stejnou zprávu a zkusit to znovu. Váš kód volá **deleteMessage** hned po zpracování zprávy.

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

---

## <a name="additional-options-for-dequeuing-messages"></a>Další možnosti pro vyřazování zpráv do fronty

Existují dva způsoby, jak přizpůsobit načítání zpráv z fronty. Nejdřív Získejte dávku zpráv (až 32). Za druhé nastavte delší nebo kratší časový limit neviditelnosti a umožněte, aby váš kód měl více nebo méně času na úplné zpracování každé zprávy.

# <a name="java-v12"></a>[Java V12](#tab/java)

Následující příklad kódu používá metodu **receiveMessages** k získání 20 zpráv v jednom volání. Potom zpracuje každou zprávu pomocí smyčky **for** . Nastaví také časový limit neviditelnosti na pět minut (300 sekund) pro každou zprávu. Časový limit začíná pro všechny zprávy ve stejnou dobu. Po pěti minutách od volání **receiveMessages**se všechny zprávy neodstraní znovu.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DequeueMessages":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

Následující příklad kódu používá metodu **retrieveMessages** k získání 20 zpráv v jednom volání. Potom zpracuje každou zprávu pomocí smyčky **for** . Nastaví také časový limit neviditelnosti na pět minut (300 sekund) pro každou zprávu. Časový limit začíná pro všechny zprávy ve stejnou dobu. Po pěti minutách od volání **retrieveMessages**se všechny zprávy neodstraní znovu.

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

---

## <a name="how-to-list-the-queues"></a>Postupy: výpis front

# <a name="java-v12"></a>[Java V12](#tab/java)

Chcete-li získat seznam aktuálních front, zavolejte metodu **QueueServiceClient. listQueues ()** , která vrátí kolekci objektů **QueueItem** .

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ListQueues":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

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

---

## <a name="how-to-delete-a-queue"></a>Postupy: odstranění fronty

# <a name="java-v12"></a>[Java V12](#tab/java)

Pokud chcete odstranit frontu a všechny zprávy, které jsou v ní obsažené, zavolejte metodu **Delete** v objektu **QueueClient** .

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DeleteMessageQueue":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

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

---

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili základní informace o službě Queue Storage, získáte další informace o složitějších úlohách úložiště pomocí těchto odkazů.

* [Sada SDK služby Azure Storage pro Javu][Azure Storage SDK for Java]
* [Referenční informace ke klientské sadě SDK služby Azure Storage][Azure Storage Client SDK Reference]
* [REST API služby Azure Storage][Azure Storage Services REST API]
* [Blog týmu Azure Storage][Azure Storage Team Blog]

[Azure SDK for Java]: https://github.com/azure/azure-sdk-for-java
[Azure Storage SDK for Java]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage
[Referenční informace ke klientské sadě SDK služby Azure Storage]: https://azure.github.io/azure-sdk-for-java/storage.html
[Azure Storage Services REST API]: /rest/api/storageservices/
[Azure Storage Team Blog]: https://techcommunity.microsoft.com/t5/azure-storage/bg-p/AzureStorageBlog
