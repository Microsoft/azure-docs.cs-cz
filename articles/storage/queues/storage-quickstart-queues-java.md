---
title: 'Rychlý Start: knihovna Azure Queue Storage V12 – Java'
description: Naučte se používat knihovnu Azure V12 knihovny Java k vytvoření fronty a přidání zpráv do fronty. V dalším kroku se dozvíte, jak číst a odstraňovat zprávy z fronty. Naučíte se také, jak odstranit frontu.
author: mhopkins-msft
ms.custom: devx-track-java
ms.author: mhopkins
ms.date: 09/10/2020
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: b6be3c0f3b7ff8e44bd9dda089c803a27e883383
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92783432"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-java"></a>Rychlý Start: Klientská knihovna Azure Queue Storage V12 pro jazyk Java

Začněte s knihovnou klienta Azure Queue Storage verze 12 pro Java. Azure Queue Storage je služba pro ukládání velkého počtu zpráv pro pozdější načtení a zpracování. Postupujte podle těchto kroků a nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úlohy.

Použijte klientskou knihovnu Azure Queue Storage V12 pro jazyk Java:

* Vytvoření fronty
* Přidání zpráv do fronty
* Prohlížet zprávy ve frontě
* Aktualizace zprávy ve frontě
* Přijímání a odstraňování zpráv z fronty
* Odstranění fronty

Další prostředky:

* [Referenční dokumentace k rozhraní API](/java/api/overview/azure/storage-queue-readme)
* [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue)
* [Balíček (Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-queue)
* [Ukázky](../common/storage-samples-java.md?toc=%252fazure%252fstorage%252fqueues%252ftoc.json#queue-samples)

## <a name="prerequisites"></a>Předpoklady

* [Java Development Kit (JDK)](/java/azure/jdk/) verze 8 nebo vyšší
* [Apache Maven](https://maven.apache.org/download.cgi)
* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* Účet úložiště Azure – [Vytvoření účtu úložiště](../common/storage-account-create.md)

## <a name="setting-up"></a>Nastavení

Tato část vás provede přípravou projektu pro práci s klientskou knihovnou Azure Queue Storage V12 for Java.

### <a name="create-the-project"></a>Vytvoření projektu

Vytvořte aplikaci Java s názvem *Queues-Start-V12* .

1. V okně konzoly (například cmd, PowerShell nebo bash) použijte Maven k vytvoření nové konzolové aplikace s názvem *fronty – rychlý Start-V12* . Zadáním následujícího příkazu **MVN** vytvořte "Hello World!" Projekt Java.

   ```console
   mvn archetype:generate -DgroupId=com.queues.quickstart \
                          -DartifactId=queues-quickstart-v12 \
                          -DarchetypeArtifactId=maven-archetype-quickstart \
                          -DarchetypeVersion=1.4 \
                          -DinteractiveMode=false
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
    [INFO] Parameter: groupId, Value: com.queues.quickstart
    [INFO] Parameter: artifactId, Value: queues-quickstart-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.quickstart
    [INFO] Parameter: packageInPathFormat, Value: com/queues/quickstart
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.quickstart
    [INFO] Parameter: groupId, Value: com.queues.quickstart
    [INFO] Parameter: artifactId, Value: queues-quickstart-v12
    [INFO] Project created from Archetype in dir: C:\quickstarts\queues\queues-quickstart-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  6.394 s
    [INFO] Finished at: 2019-12-03T09:58:35-08:00
    [INFO] ------------------------------------------------------------------------
    ```

1. Přepněte na nově vytvořený adresář *s rychlým startem front-V12* .

   ```console
   cd queues-quickstart-v12
   ```

### <a name="install-the-package"></a>Instalace balíčku

Otevřete *pom.xml* soubor v textovém editoru. Přidejte následující prvek závislosti do skupiny závislostí.

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-queue</artifactId>
  <version>12.0.1</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Nastavení aplikační architektury

Z adresáře projektu:

1. Přejděte do adresáře */Src/Main/Java/com/Queues/Quickstart*
1. Otevřete soubor *App. Java* v editoru.
1. Odstranění `System.out.println("Hello world!");` příkazu
1. Přidat `import` direktivy

Zde je kód:

```java
package com.queues.quickstart;

/**
 * Azure queue storage v12 SDK quickstart
 */
import com.azure.storage.queue.*;
import com.azure.storage.queue.models.*;
import java.io.*;
import java.time.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektový model

Azure Queue Storage je služba pro ukládání velkých objemů zpráv. Zpráva fronty může mít velikost až 64 KB. Fronta může obsahovat miliony zpráv až do celkového limitu kapacity účtu úložiště. Fronty se běžně používají k vytváření nevyřízených položek pro asynchronní zpracování. Queue Storage nabízí tři typy prostředků:

* Účet úložiště
* Fronta v účtu úložiště
* Zprávy ve frontě

Na následujícím diagramu jsou vztahy těchto prostředků.

![Diagram architektury služby Queue Storage](./media/storage-queues-introduction/queue1.png)

K interakci s těmito prostředky použijte následující třídy Java:

* [QueueClientBuilder](/java/api/com.azure.storage.queue.queueclientbuilder): `QueueClientBuilder` Třída konfiguruje a vytvoří instanci `QueueClient` objektu.
* [QueueServiceClient](/java/api/com.azure.storage.queue.queueserviceclient): `QueueServiceClient` umožňuje spravovat všechny fronty v účtu úložiště.
* [QueueClient](/java/api/com.azure.storage.queue.queueclient): `QueueClient` Třída umožňuje spravovat a manipulovat s jednotlivou frontou a jejími zprávami.
* [QueueMessageItem](/java/api/com.azure.storage.queue.models.queuemessageitem): `QueueMessageItem` Třída představuje jednotlivé objekty vracené při volání [receiveMessages](/java/api/com.azure.storage.queue.queueclient.receivemessages) ve frontě.

## <a name="code-examples"></a>Příklady kódu

Tyto ukázkové fragmenty kódu ukazují, jak provést následující akce s klientskou knihovnou Azure Queue Storage pro Java:

* [Získání připojovacího řetězce](#get-the-connection-string)
* [Vytvoření fronty](#create-a-queue)
* [Přidání zpráv do fronty](#add-messages-to-a-queue)
* [Prohlížet zprávy ve frontě](#peek-at-messages-in-a-queue)
* [Aktualizace zprávy ve frontě](#update-a-message-in-a-queue)
* [Přijímání a odstraňování zpráv z fronty](#receive-and-delete-messages-from-a-queue)
* [Odstranění fronty](#delete-a-queue)

### <a name="get-the-connection-string"></a>Získání připojovacího řetězce

Následující kód načte připojovací řetězec pro účet úložiště. Připojovací řetězec ukládá proměnnou prostředí vytvořenou v části [Konfigurace připojovacího řetězce úložiště](#configure-your-storage-connection-string) .

Přidejte tento kód do `main` metody:

```java
System.out.println("Azure Queues storage v12 - Java quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable
// is created after the application is launched in a console or with
// Visual Studio, the shell or application needs to be closed and reloaded
// to take the environment variable into account.
String connectStr = System.getenv("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-queue"></a>Vytvoření fronty

Určete název nové fronty. Následující kód připojí hodnotu identifikátoru GUID k názvu fronty, aby bylo zajištěno, že je jedinečný.

> [!IMPORTANT]
> Názvy front můžou obsahovat jenom malá písmena, číslice a spojovníky a musí začínat písmenem nebo číslicí. Před i za každým spojovníkem musí být jiný znak než spojovník. Název musí mít také délku 3 až 63 znaků. Další informace o pojmenovávání front najdete v tématu [pojmenování front a metadat](/rest/api/storageservices/naming-queues-and-metadata).


Vytvořte instanci třídy [QueueClient](/java/api/com.azure.storage.queue.queueclient) . Pak zavolejte metodu [Create](/java/api/com.azure.storage.queue.queueclient.create) a vytvořte ve svém účtu úložiště frontu.

Přidejte tento kód na konec `main` metody:

```java
// Create a unique name for the queue
String queueName = "quickstartqueues-" + java.util.UUID.randomUUID();

System.out.println("Creating queue: " + queueName);

// Instantiate a QueueClient which will be
// used to create and manipulate the queue
QueueClient queueClient = new QueueClientBuilder()
                                .connectionString(connectStr)
                                .queueName(queueName)
                                .buildClient();

// Create the queue
queueClient.create();
```

### <a name="add-messages-to-a-queue"></a>Přidání zpráv do fronty

Následující fragment kódu přidá zprávy do fronty voláním metody [SendMessage](/java/api/com.azure.storage.queue.queueclient.sendmessage) . Také ukládá [SendMessageResult](/java/api/com.azure.storage.queue.models.sendmessageresult) vrácené `sendMessage` voláním. Výsledek se používá k aktualizaci zprávy později v programu.

Přidejte tento kód na konec `main` metody:

```java
System.out.println("\nAdding messages to the queue...");

// Send several messages to the queue
queueClient.sendMessage("First message");
queueClient.sendMessage("Second message");

// Save the result so we can update this message later
SendMessageResult result = queueClient.sendMessage("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>Prohlížet zprávy ve frontě

Podívejte se na zprávy ve frontě voláním metody [peekMessages](/java/api/com.azure.storage.queue.queueclient.peekmessages) . `peelkMessages`Metoda načítá jednu nebo více zpráv před frontou, ale nemění viditelnost zprávy.

Přidejte tento kód na konec `main` metody:

```java
System.out.println("\nPeek at the messages in the queue...");

// Peek at messages in the queue
queueClient.peekMessages(10, null, null).forEach(
    peekedMessage -> System.out.println("Message: " + peekedMessage.getMessageText()));
```

### <a name="update-a-message-in-a-queue"></a>Aktualizace zprávy ve frontě

Aktualizujte obsah zprávy voláním metody [updateMessage](/java/api/com.azure.storage.queue.queueclient.updatemessage) . `updateMessage`Metoda může změnit časový limit viditelnosti zprávy a její obsah. Obsah zprávy musí být řetězec kódovaný v kódování UTF-8, který má velikost až 64 KB. Spolu s novým obsahem zprávy předejte ID zprávy a příjemku POP pomocí `SendMessageResult` , který byl uložen dříve v kódu. ID zprávy a příjemku pop identifikují, která zpráva se má aktualizovat.

```java
System.out.println("\nUpdating the third message in the queue...");

// Update a message using the result that
// was saved when sending the message
queueClient.updateMessage(result.getMessageId(),
                          result.getPopReceipt(), 
                          "Third message has been updated",
                          Duration.ofSeconds(1));
```

### <a name="receive-and-delete-messages-from-a-queue"></a>Přijímání a odstraňování zpráv z fronty

Stáhněte dříve přidané zprávy voláním metody [receiveMessages](/java/api/com.azure.storage.queue.queueclient.receivemessages) . Vzorový kód také odstraní zprávy z fronty po jejich přijetí a zpracování. V tomto případě se zpracovává jenom zpráva v konzole nástroje.

Aplikace pozastaví vstup uživatele voláním `System.console().readLine();` , než přijme a odstraní zprávy. Před odstraněním ověřte ve svém [Azure Portal](https://portal.azure.com) , že se prostředky vytvořily správně. Všechny zprávy, které se explicitně neodstraní, se později stanou viditelnými ve frontě, aby se mohly zpracovat další šance na jejich zpracování.

Přidejte tento kód na konec `main` metody:

```java
System.out.println("\nPress Enter key to receive messages and delete them from the queue...");
System.console().readLine();

// Get messages from the queue
queueClient.receiveMessages(10).forEach(
    // "Process" the message
    receivedMessage -> {
        System.out.println("Message: " + receivedMessage.getMessageText());

        // Let the service know we're finished with
        // the message and it can be safely deleted.
        queueClient.deleteMessage(receivedMessage.getMessageId(), receivedMessage.getPopReceipt());
    }
);
```

### <a name="delete-a-queue"></a>Odstranění fronty

Následující kód vyčistí prostředky, které aplikace vytvořila, odstraněním fronty pomocí metody [Delete](/java/api/com.azure.storage.queue.queueclient.delete) .

Přidejte tento kód na konec `main` metody:

```java
System.out.println("\nPress Enter key to delete the queue...");
System.console().readLine();

// Clean up
System.out.println("Deleting queue: " + queueClient.getQueueName());
queueClient.delete();

System.out.println("Done");
```

## <a name="run-the-code"></a>Spuštění kódu

Tato aplikace vytvoří a přidá tři zprávy do fronty Azure. Kód vypíše zprávy ve frontě a pak je načte a odstraní před tím, než se nakonec odstraní fronta.

V okně konzoly přejděte do adresáře aplikace a pak Sestavte a spusťte aplikaci.

```console
mvn compile
```

Pak Sestavte balíček.

```console
mvn package
```

Spusťte následující `mvn` příkaz, který aplikaci spustí.

```console
mvn exec:java -Dexec.mainClass="com.queues.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

Výstup aplikace je podobný následujícímu příkladu:

```output
Azure Queues storage v12 - Java quickstart sample

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Press Enter key to receive messages and delete them from the queue...

Message: First message
Message: Second message
Message: Third message has been updated

Press Enter key to delete the queue...

Deleting queue: quickstartqueues-fbf58f33-4d5a-41ac-ac0e-1a05d01c7003
Done
```

Když se aplikace před přijetím zpráv pozastaví, ověřte si účet úložiště v [Azure Portal](https://portal.azure.com). Ověřte, zda jsou zprávy ve frontě.

Stiskněte klávesu **ENTER** pro příjem a odstranění zpráv. Po zobrazení výzvy znovu stiskněte klávesu **ENTER** , aby se fronta odstranila, a dokončete ukázku.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit frontu a přidat do ní zprávy pomocí kódu Java. Pak jste se naučili prohlížet, načítat a odstraňovat zprávy. Nakonec jste zjistili, jak odstranit frontu zpráv.

Výukové programy, ukázky, rychlé starty a další dokumentace najdete na webu:

> [!div class="nextstepaction"]
> [Azure pro cloudové vývojáře v Javě](/azure/developer/java/)

* Pokud chcete zobrazit více ukázkových aplikací Azure Queue Storage, pokračujte v [sadě SDK Azure Queue Storage SDK V12 na ukázky klientských knihoven Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue).