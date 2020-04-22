---
title: Použití front Azure Service Bus s Javou
description: V tomto kurzu se dozvíte, jak vytvořit java aplikace pro odesílání zpráv a přijímání zpráv z fronty Azure Service Bus.
services: service-bus-messaging
documentationcenter: java
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f701439c-553e-402c-94a7-64400f997d59
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aschhab
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 184ffd39281ea27d8596bc37a9f89fd22acfb1ba
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732173"
---
# <a name="quickstart-use-azure-service-bus-queues-with-java-to-send-and-receive-messages"></a>Úvodní příručka: K odesílání a přijímání zpráv pomocí front Azure Service Bus s Javou

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]
V tomto kurzu se dozvíte, jak vytvořit java aplikace pro odesílání zpráv a přijímání zpráv z fronty Azure Service Bus. 

> [!NOTE]
> Ukázky Jazyka Java najdete na GitHubu v [úložišti azure-service-bus](https://github.com/Azure/azure-service-bus/tree/master/samples/Java).

## <a name="prerequisites"></a>Požadavky
1. Předplatné Azure. K dokončení tohoto kurzu potřebujete mít účet Azure. Můžete aktivovat [výhody předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Pokud nemáte frontu pro práci s, postupujte podle kroků na [portálu Azure k vytvoření](service-bus-quickstart-portal.md) článku fronty Service Bus k vytvoření fronty.
    1. Přečtěte si stručný **přehled** **front služby**Service Bus . 
    2. Vytvořte **obor názvů**service bus . 
    3. Získejte **připojovací řetězec**.
    4. Vytvořte **frontu služby**Service Bus .
3. Nainstalujte [Azure SDK pro Javu][Azure SDK for Java]. 


## <a name="configure-your-application-to-use-service-bus"></a>Konfigurace aplikace pro použití služby Service Bus
Ujistěte se, že jste nainstalovali [Azure SDK pro Java][Azure SDK for Java] před sestavením této ukázky. 

Pokud používáte Eclipse, můžete nainstalovat [sadu nástrojů Azure Toolkit pro Eclipse,][Azure Toolkit for Eclipse] která zahrnuje sadu Azure SDK pro Javu. Potom můžete do projektu přidat **knihovny Microsoft Azure pro Jazyk Java.** Pokud používáte IntelliJ, [přečtěte si informace o instalaci sady Nástrojů Azure pro IntelliJ](/azure/developer/java/toolkit-for-intellij/installation). 

![Přidání knihoven Microsoft Azure pro Jazyk Java do projektu Eclipse](./media/service-bus-java-how-to-use-queues/eclipse-azure-libraries-java.png)


Do horní `import` části souboru Java přidejte následující příkazy:

```java
// Include the following imports to use Service Bus APIs
import com.google.gson.reflect.TypeToken;
import com.microsoft.azure.servicebus.*;
import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import com.google.gson.Gson;

import static java.nio.charset.StandardCharsets.*;

import java.time.Duration;
import java.util.*;
import java.util.concurrent.*;

import org.apache.commons.cli.*;

```

## <a name="send-messages-to-a-queue"></a>Zasílání zpráv do fronty
Chcete-li odeslat zprávy do fronty služby Service Bus, aplikace instanciobjektu **QueueClient** a odesílá zprávy asynchronně. Následující kód ukazuje, jak odeslat zprávu pro frontu, která byla vytvořena prostřednictvím portálu.

```java
public void run() throws Exception {
    // Create a QueueClient instance and then asynchronously send messages.
    // Close the sender once the send operation is complete.
    QueueClient sendClient = new QueueClient(new ConnectionStringBuilder(ConnectionString, QueueName), ReceiveMode.PEEKLOCK);
    this.sendMessageAsync(sendClient).thenRunAsync(() -> sendClient.closeAsync());

    sendClient.close();
}

    CompletableFuture<Void> sendMessagesAsync(QueueClient sendClient) {
        List<HashMap<String, String>> data =
                GSON.fromJson(
                        "[" +
                                "{'name' = 'Einstein', 'firstName' = 'Albert'}," +
                                "{'name' = 'Heisenberg', 'firstName' = 'Werner'}," +
                                "{'name' = 'Curie', 'firstName' = 'Marie'}," +
                                "{'name' = 'Hawking', 'firstName' = 'Steven'}," +
                                "{'name' = 'Newton', 'firstName' = 'Isaac'}," +
                                "{'name' = 'Bohr', 'firstName' = 'Niels'}," +
                                "{'name' = 'Faraday', 'firstName' = 'Michael'}," +
                                "{'name' = 'Galilei', 'firstName' = 'Galileo'}," +
                                "{'name' = 'Kepler', 'firstName' = 'Johannes'}," +
                                "{'name' = 'Kopernikus', 'firstName' = 'Nikolaus'}" +
                                "]",
                        new TypeToken<List<HashMap<String, String>>>() {}.getType());

        List<CompletableFuture> tasks = new ArrayList<>();
        for (int i = 0; i < data.size(); i++) {
            final String messageId = Integer.toString(i);
            Message message = new Message(GSON.toJson(data.get(i), Map.class).getBytes(UTF_8));
            message.setContentType("application/json");
            message.setLabel("Scientist");
            message.setMessageId(messageId);
            message.setTimeToLive(Duration.ofMinutes(2));
            System.out.printf("\nMessage sending: Id = %s", message.getMessageId());
            tasks.add(
                    sendClient.sendAsync(message).thenRunAsync(() -> {
                        System.out.printf("\n\tMessage acknowledged: Id = %s", message.getMessageId());
                    }));
        }
        return CompletableFuture.allOf(tasks.toArray(new CompletableFuture<?>[tasks.size()]));
    }

```

Zprávy odeslané a přijaté z front služby Service Bus jsou instancetřídy [Message.](/java/api/com.microsoft.azure.servicebus.message?view=azure-java-stable) Message objekty mají sadu standardnívlastnosti (například Label a TimeToLive), slovník, který se používá k uložení vlastní vlastnosti specifické pro aplikaci a tělo libovolných dat aplikace. Aplikace můžete nastavit tělo zprávy předáním libovolný serializovatelný objekt do konstruktormessage a příslušný serializátor pak bude použit k serializaci objektu. Případně můžete poskytnout **java. Io. InputStream** objektu.


Fronty Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Počet zpráv držených ve frontě není omezený, ale celková velikost zpráv držených ve frontě omezená je. Velikost fronty se definuje při vytvoření, maximální limit je 5 GB.

## <a name="receive-messages-from-a-queue"></a>Příjem zpráv z fronty
Primární způsob, jak přijímat zprávy z fronty je použití **Objektu ServiceBusContract.** Přijaté zprávy mohou pracovat ve dvou různých režimech: **ReceiveAndDelete** a **PeekLock**.

Při použití **receiveAndDelete** režimu receive, receive je operace s jedním výstřelem - to znamená, když Service Bus obdrží požadavek na čtení zprávy ve frontě, označí zprávu jako spotřebované a vrátí ji do aplikace. **ReceiveAndDelete** režim (což je výchozí režim) je nejjednodušší model a funguje nejlépe pro scénáře, ve kterých aplikace může tolerovat není zpracování zprávy v případě selhání. Pro lepší vysvětlení si představte scénář, ve kterém spotřebitel vyšle požadavek na přijetí, ale než ji může zpracovat, dojde v něm k chybě a ukončí se.
Vzhledem k tomu, že service bus označil zprávu jako spotřebované, pak při restartování aplikace a začne spotřebovávat zprávy znovu, má zmeškané zprávy, která byla spotřebována před selhání.

V režimu **PeekLock,** příjem se stane dvoustupňovou operaci, která umožňuje podporovat aplikace, které nemohou tolerovat chybějící zprávy. Když Service Bus přijme požadavek, najde zprávu, která je na řadě ke spotřebování, uzamkne ji proti spotřebování jinými spotřebiteli a vrátí ji do aplikace. Po dokončení aplikace zpracování zprávy (nebo ukládá spolehlivě pro budoucí zpracování), dokončí druhou fázi procesu příjmu voláním **complete()** na přijaté zprávy. Když service bus vidí **volání complete(),** označí zprávu jako spotřebované a odebere ji z fronty. 

Následující příklad ukazuje, jak mohou být zprávy přijímány a zpracovávány pomocí režimu **PeekLock** (nikoli ve výchozím režimu). Níže uvedený příklad používá model zpětného volání s registrovanou obslužnou rutinou zprávy a zpracovává zprávy při jejich doručení do našeho `TestQueue`. Tento režim volá **complete()** automaticky jako zpětné volání vrátí normálně a volání **abandon(),** pokud zpětné volání vyvolá výjimku. 

```java
    public void run() throws Exception {
        // Create a QueueClient instance for receiving using the connection string builder
        // We set the receive mode to "PeekLock", meaning the message is delivered
        // under a lock and must be acknowledged ("completed") to be removed from the queue
        QueueClient receiveClient = new QueueClient(new ConnectionStringBuilder(ConnectionString, QueueName), ReceiveMode.PEEKLOCK);
        this.registerReceiver(receiveClient);

        // shut down receiver to close the receive loop
        receiveClient.close();
    }
    void registerReceiver(QueueClient queueClient) throws Exception {
        // register the RegisterMessageHandler callback
        queueClient.registerMessageHandler(new IMessageHandler() {
        // callback invoked when the message handler loop has obtained a message
            public CompletableFuture<Void> onMessageAsync(IMessage message) {
            // receives message is passed to callback
                if (message.getLabel() != null &&
                    message.getContentType() != null &&
                    message.getLabel().contentEquals("Scientist") &&
                    message.getContentType().contentEquals("application/json")) {

                        byte[] body = message.getBody();
                        Map scientist = GSON.fromJson(new String(body, UTF_8), Map.class);

                        System.out.printf(
                            "\n\t\t\t\tMessage received: \n\t\t\t\t\t\tMessageId = %s, \n\t\t\t\t\t\tSequenceNumber = %s, \n\t\t\t\t\t\tEnqueuedTimeUtc = %s," +
                            "\n\t\t\t\t\t\tExpiresAtUtc = %s, \n\t\t\t\t\t\tContentType = \"%s\",  \n\t\t\t\t\t\tContent: [ firstName = %s, name = %s ]\n",
                            message.getMessageId(),
                            message.getSequenceNumber(),
                            message.getEnqueuedTimeUtc(),
                            message.getExpiresAtUtc(),
                            message.getContentType(),
                            scientist != null ? scientist.get("firstName") : "",
                            scientist != null ? scientist.get("name") : "");
                    }
                    return CompletableFuture.completedFuture(null);
                }

                // callback invoked when the message handler has an exception to report
                public void notifyException(Throwable throwable, ExceptionPhase exceptionPhase) {
                    System.out.printf(exceptionPhase + "-" + throwable.getMessage());
                }
        },
        // 1 concurrent call, messages are auto-completed, auto-renew duration
        new MessageHandlerOptions(1, true, Duration.ofMinutes(1)));
    }

```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Zpracování pádů aplikace a nečitelných zpráv
Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud aplikace příjemce není schopen zpracovat zprávu z nějakého důvodu, pak může volat **abandon()** metoda na objektu klienta s tokenem zámku přijaté zprávy získané prostřednictvím **getLockToken()**. To způsobí, že Service Bus zprávu odemkne ve frontě a zpřístupní ji pro další přijetí, buďto stejnou spotřebitelskou aplikací nebo jinou spotřebitelskou aplikací.

K dispozici je také časový limit přidružený ke zprávě zamčené ve frontě a pokud aplikace nepodaří zpracovat zprávu před vypršením časového limitu uzamčení (například pokud dojde k chybě aplikace), pak Service Bus automaticky odemkne zprávu a zpřístupní ji k přijetí znovu.

V případě, že aplikace dojde k chybě po zpracování zprávy, ale před **complete()** požadavek je vydán, pak zpráva je znovu doručena do aplikace při restartování. Tomu se často říká *Zpracování nejméně jednou* – to znamená, že každá zpráva se zpracuje alespoň jednou, ale v některých situacích se může doručit víckrát. Pokud daný scénář nemůže tolerovat zpracování víc než jednou, vývojáři aplikace by měli přidat další logiku navíc pro zpracování víckrát doručené zprávy. Toho je často dosaženo pomocí **getMessageId** metodu zprávy, která zůstává konstantní napříč pokusy o doručení.

> [!NOTE]
> Prostředky služby Service Bus můžete spravovat pomocí [aplikace Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Průzkumník služby Service Bus umožňuje uživatelům připojit se k oboru názvů service bus a snadno spravovat entity zasílání zpráv. Nástroj poskytuje pokročilé funkce, jako je funkce importu a exportu nebo možnost testovat téma, fronty, předplatná, přenosové služby, centra oznámení a centra událostí. 

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili základy front service bus, najdete [v tématu Fronty, témata a odběry][Queues, topics, and subscriptions] další informace.

Další informace naleznete ve [Středisku pro vývojáře Java](https://azure.microsoft.com/develop/java/).

[Azure SDK for Java]: /azure/developer/java/sdk/java-sdk-azure-get-started
[Azure Toolkit for Eclipse]: https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
