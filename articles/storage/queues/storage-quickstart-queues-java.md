---
title: 'Úvodní příručka: Knihovna úložiště fronty Azure v12 – Java'
description: Přečtěte si, jak pomocí knihovny Azure Queue Java v12 vytvořit frontu a přidat zprávy do fronty. Dále se dozvíte, jak číst a odstraňovat zprávy z fronty. Dozvíte se také, jak odstranit frontu.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/4/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: 013281db2821c99f6942edd1322f4978e100c144
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729857"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-java"></a>Úvodní příručka: Klientská knihovna úložiště fronty Azure v12 pro Javu

Začínáme s klientskou knihovnou úložiště Azure Queue verze 12 pro Javu. Azure Queue Storage je služba pro ukládání velkého počtu zpráv pro pozdější načítání a zpracování. Následujícím postupem nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úkoly.

Pomocí klientské knihovny úložiště fronty Azure v12 pro Javu:

* Vytvoření fronty
* Přidání zpráv do fronty
* Náhled na zprávy ve frontě
* Aktualizace zprávy ve frontě
* Příjem a odstraňování zpráv z fronty
* Odstranění fronty

[Referenční dokumentace](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/index.html) | rozhraní[Knihovna zdrojový kód](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue) | [Balíček (Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-queue) | [Ukázky](https://docs.microsoft.com/azure/storage/common/storage-samples-java?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Požadavky

* [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable) verze 8 nebo vyšší
* [Apache Maven](https://maven.apache.org/download.cgi)
* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
* Účet úložiště Azure – [vytvoření účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

## <a name="setting-up"></a>Nastavení

Tato část vás provede přípravou projektu pro práci s klientskou knihovnou úložiště fronty Azure v12 pro Javu.

### <a name="create-the-project"></a>Vytvoření projektu

Vytvořte aplikaci Java s názvem *queues-quickstart-v12*.

1. V okně konzoly (například cmd, PowerShell nebo Bash) použijte Maven k vytvoření nové konzolové aplikace s *názvovými frontami-quickstart-v12*. Zadejte následující příkaz **mvn** a vytvořte "Hello world!" Projekt Java.

   ```console
   mvn archetype:generate -DgroupId=com.queues.quickstart \
                          -DartifactId=queues-quickstart-v12 \
                          -DarchetypeArtifactId=maven-archetype-quickstart \
                          -DarchetypeVersion=1.4 \
                          -DinteractiveMode=false
   ```

1. Výstup z generování projektu by měl vypadat nějak takto:

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

1. Přepněte do nově vytvořeného *adresáře front-quickstart-v12.*

   ```console
   cd queues-quickstart-v12
   ```

### <a name="install-the-package"></a>Instalace balíčku

Otevřete soubor *pom.xml* v textovém editoru. Přidejte následující prvek závislosti do skupiny závislostí.

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-queue</artifactId>
  <version>12.0.1</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Nastavení architektury aplikace

Z adresáře projektu:

1. Přejděte do adresáře */src/main/java/com/queues/quickstart*
1. Otevření souboru *App.java* v editoru
1. Odstranit `System.out.println("Hello world!");` příkaz
1. Přidání `import` směrnic

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

Azure Queue Storage je služba pro ukládání velkých objemů zpráv. Zpráva fronty může mít velikost až 64 kB. Fronta může obsahovat miliony zpráv až do celkového limitu kapacity účtu úložiště. Fronty se běžně používají k vytvoření nevyřízených položek práce pro zpracování asynchronně. Úložiště front nabízí tři typy prostředků:

* Účet úložiště
* Fronta v účtu úložiště
* Zprávy ve frontě

Na následujícím diagramu jsou vztahy těchto prostředků.

![Diagram architektury úložiště fronty](./media/storage-queues-introduction/queue1.png)

K interakci s těmito prostředky použijte následující třídy Javy:

* [QueueClientBuilder](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClientBuilder.html): `QueueClientBuilder` Třída konfiguruje `QueueClient` a konkretuje objekt.
* [QueueServiceClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueServiceClient.html): `QueueServiceClient` Umožňuje spravovat všechny fronty v účtu úložiště.
* [QueueClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html): `QueueClient` Třída umožňuje spravovat a manipulovat s jednotlivou frontou a jejími zprávami.
* [QueueMessageItem](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/models/QueueMessageItem.html): `QueueMessageItem` Třída představuje jednotlivé objekty vrácené při volání [receiveMessages](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#receiveMessages-java.lang.Integer-) ve frontě.

## <a name="code-examples"></a>Příklady kódu

Tyto ukázkové fragmenty kódu ukazují, jak provést následující akce s klientskou knihovnou úložiště Azure Queue pro Jazyk Java:

* [Získání připojovacího řetězce](#get-the-connection-string)
* [Vytvoření fronty](#create-a-queue)
* [Přidání zpráv do fronty](#add-messages-to-a-queue)
* [Náhled na zprávy ve frontě](#peek-at-messages -in-a-queue)
* [Aktualizace zprávy ve frontě](#update-a-message-in-a-queue)
* [Příjem a odstraňování zpráv z fronty](#receive-and-delete-messages-from-a-queue)
* [Odstranění fronty](#delete-a-queue)

### <a name="get-the-connection-string"></a>Získání připojovacího řetězce

Níže uvedený kód načte připojovací řetězec pro účet úložiště. Připojovací řetězec je uložen proměnnou prostředí vytvořenou v části [Konfigurovat připojovací řetězec úložiště.](#configure-your-storage-connection-string)

Přidejte tento `main` kód uvnitř metody:

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

Rozhodněte o názvu nové fronty. Níže uvedený kód připojí hodnotu GUID k názvu fronty, aby bylo zajištěno, že je jedinečný.

> [!IMPORTANT]
> Názvy front mohou obsahovat pouze malá písmena, čísla a pomlčky a musí začínat písmenem nebo číslem. Před i za každým spojovníkem musí být jiný znak než spojovník. Název musí být také mezi 3 a 63 znaků dlouhé. Další informace o pojmenování front naleznete [v tématu Pojmenování front a metadat](/rest/api/storageservices/naming-queues-and-metadata).


Vytvořte instanci třídy [QueueClient.](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html) Potom zavolejte metodu [create](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#create--) a vytvořte frontu v účtu úložiště.

Přidejte tento kód na `main` konec metody:

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

Následující fragment kódu přidá zprávy do fronty voláním metody [sendMessage.](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#sendMessage-java.lang.String-) Také uloží [SendMessageResult](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/models/SendMessageResult.html) vrácena `sendMessage` z volání. Výsledek se používá k aktualizaci zprávy později v programu.

Přidejte tento kód na `main` konec metody:

```java
System.out.println("\nAdding messages to the queue...");

// Send several messages to the queue
queueClient.sendMessage("First message");
queueClient.sendMessage("Second message");

// Save the result so we can update this message later
SendMessageResult result = queueClient.sendMessage("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>Náhled na zprávy ve frontě

Prohlédněte si zprávy ve frontě voláním [peekMessages](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#peekMessages-java.lang.Integer-java.time.Duration-com.azure.core.util.Context-) metoda. Metoda `peelkMessages` načte jednu nebo více zpráv z přední části fronty, ale nezmění viditelnost zprávy.

Přidejte tento kód na `main` konec metody:

```java
System.out.println("\nPeek at the messages in the queue...");

// Peek at messages in the queue
queueClient.peekMessages(10, null, null).forEach(
    peekedMessage -> System.out.println("Message: " + peekedMessage.getMessageText()));
```

### <a name="update-a-message-in-a-queue"></a>Aktualizace zprávy ve frontě

Aktualizujte obsah zprávy voláním metody [updateMessage.](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#updateMessage-java.lang.String-java.lang.String-java.lang.String-java.time.Duration-) Metoda `updateMessage` může změnit časový limit viditelnosti zprávy a obsah. Obsah zprávy musí být kódovaný řetězec UTF-8, který má velikost až 64 kB. Spolu s novým obsahem zprávy předavěte ID zprávy `SendMessageResult` a potvrzení pomocí protokolu, který byl uložen dříve v kódu. ID zprávy a potvrzení o automaticky ohlášení identifikují, kterou zprávu chcete aktualizovat.

```java
System.out.println("\nUpdating the third message in the queue...");

// Update a message using the result that
// was saved when sending the message
queueClient.updateMessage(result.getMessageId(),
                          result.getPopReceipt(), 
                          "Third message has been updated",
                          Duration.ofSeconds(1));
```

### <a name="receive-and-delete-messages-from-a-queue"></a>Příjem a odstraňování zpráv z fronty

Stáhnout dříve přidané zprávy voláním [metody receiveMessages.](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#receiveMessages-java.lang.Integer-java.time.Duration-java.time.Duration-com.azure.core.util.Context-) Ukázkový kód také odstraní zprávy z fronty po jejich přijetí a zpracování. V tomto případě zpracování je pouze zobrazení zprávy na konzoli.

Aplikace pozastaví vstup uživatele `System.console().readLine();` voláním před tím, než obdrží a odstraní zprávy. Před odstraněním na [webu Azure Portal](https://portal.azure.com) ověřte, že prostředky byly vytvořeny správně. Všechny zprávy, které nejsou explicitně odstraněny, se nakonec znovu zobrazí ve frontě, aby byla možné je zpracovat.

Přidejte tento kód na `main` konec metody:

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

Následující kód vyčistí prostředky, které aplikace vytvořila odstraněním fronty pomocí metody [delete.](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#delete--)

Přidejte tento kód na `main` konec metody:

```java
System.out.println("\nPress Enter key to delete the queue...");
System.console().readLine();

// Clean up
System.out.println("Deleting queue: " + queueClient.getQueueName());
queueClient.delete();

System.out.println("Done");
```

## <a name="run-the-code"></a>Spuštění kódu

Tato aplikace vytvoří a přidá tři zprávy do fronty Azure. Kód uvádí zprávy ve frontě, pak načte a odstraní je, před konečnou odstranění fronty.

V okně konzoly přejděte do adresáře aplikace a potom aplikaci sestavte a spusťte.

```console
mvn compile
```

Potom vytvořte balíček.

```console
mvn package
```

Spusťte `mvn` následující příkaz pro spuštění aplikace.

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

Když se aplikace před příjemem zpráv pozastaví, zkontrolujte svůj účet úložiště na [webu Azure Portal](https://portal.azure.com). Ověřte, zda jsou zprávy ve frontě.

Stisknutím klávesy **Enter** přijměte a odstraňte zprávy. Po zobrazení výzvy znovu stisknete klávesu **Enter,** abyste odstranili frontu a dokončili ukázku.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak vytvořit frontu a přidat do ní zprávy pomocí kódu Java. Pak jste se naučili prohlížet, načítat a odstraňovat zprávy. Nakonec jste se naučili, jak odstranit frontu zpráv.

Kurzy, ukázky, rychlé spuštění a další dokumentaci naleznete na adrese:

> [!div class="nextstepaction"]
> [Azure pro cloudové vývojáře v Javě](https://docs.microsoft.com/azure/developer/java/)

* Chcete-li zobrazit další ukázkové aplikace úložiště fronty Azure, pokračujte na [ukázky knihovny klienta Azure Queue storage SDK v12 Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue).
