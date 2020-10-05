---
title: Použití Azure Service Bus front s Java
description: V tomto kurzu se naučíte vytvářet aplikace v jazyce Java pro posílání zpráv a příjem zpráv z fronty Azure Service Bus.
ms.devlang: Java
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 5447bea686db48157c721978f5510cd80e0924c6
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "88065790"
---
# <a name="quickstart-use-azure-service-bus-queues-with-java-to-send-and-receive-messages"></a>Rychlý Start: použití Azure Service Bus front s jazykem Java k posílání a přijímání zpráv

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]
V tomto kurzu se naučíte vytvářet aplikace v jazyce Java pro posílání zpráv a příjem zpráv z fronty Azure Service Bus. 

> [!NOTE]
> Ukázky Java můžete najít na GitHubu v [úložišti Azure-Service-Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/Java).

## <a name="prerequisites"></a>Předpoklady
1. Předplatné Azure. K dokončení tohoto kurzu potřebujete mít účet Azure. Můžete aktivovat výhody pro [předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Pokud nemáte frontu, ve které byste mohli pracovat, postupujte podle kroků v tématu [použití Azure Portal k vytvoření fronty Service Bus](service-bus-quickstart-portal.md) .
    1. Přečtěte si rychlý **přehled** Service Busch **front**. 
    2. Vytvořte **obor názvů**Service Bus. 
    3. Získá **připojovací řetězec**.
    4. Vytvořte **frontu**Service Bus.
3. Nainstalujte [sadu Azure SDK pro jazyk Java][Azure SDK for Java]. 


## <a name="configure-your-application-to-use-service-bus"></a>Konfigurace aplikace pro použití Service Bus
Před vytvořením této ukázky se ujistěte, že máte nainstalovanou [sadu Azure SDK pro jazyk Java][Azure SDK for Java] . 

Pokud používáte zatmění, můžete nainstalovat [Azure Toolkit for Eclipse][Azure Toolkit for Eclipse] , který obsahuje sadu Azure SDK pro jazyk Java. Pak můžete přidat **knihovny Microsoft Azure pro jazyk Java** do projektu. Pokud používáte IntelliJ, přečtěte si téma [instalace Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation). 

![Přidání knihoven Microsoft Azure pro Java do projektu zatmění](./media/service-bus-java-how-to-use-queues/eclipse-azure-libraries-java.png)


`import`Do horní části souboru Java přidejte následující příkazy:

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
Chcete-li odesílat zprávy do fronty Service Bus, aplikace vytvoří instanci objektu **QueueClient** a asynchronně odesílá zprávy. Následující kód ukazuje, jak odeslat zprávu pro frontu, která byla vytvořena prostřednictvím portálu.

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

Zprávy odeslané do a přijaté z fronty Service Bus jsou instancemi třídy [Message](/java/api/com.microsoft.azure.servicebus.message?view=azure-java-stable) . Objekty zpráv mají sadu standardních vlastností (například Label a TimeToLive), slovník, který slouží k uložení vlastních vlastností specifických pro aplikaci a tělo libovolných aplikačních dat. Aplikace může tělo zprávy nastavit tak, že do konstruktoru zprávy předáte libovolný serializovatelný objekt a příslušný serializátor pak bude použit k serializaci objektu. Případně můžete zadat **jazyk Java. Operace. Objekt InputStream**


Fronty Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Počet zpráv držených ve frontě není omezený, ale celková velikost zpráv držených ve frontě omezená je. Velikost fronty se definuje při vytvoření, maximální limit je 5 GB.

## <a name="receive-messages-from-a-queue"></a>Přijímání zpráv z fronty
Hlavním způsobem, jak přijímat zprávy z fronty, je použít objekt **ServiceBusContract** . Přijaté zprávy mohou fungovat ve dvou různých režimech: **ReceiveAndDelete** a **PeekLock**.

Při použití režimu **ReceiveAndDelete** je přijetí jednorázová operace – to znamená, že když Service Bus přijme požadavek na čtení zprávy ve frontě, označí zprávu jako spotřebou a vrátí ji do aplikace. **ReceiveAndDelete** režim (což je výchozí režim) je nejjednodušší model a funguje nejlépe ve scénářích, ve kterých aplikace může tolerovat nezpracovávání zprávy v případě selhání. Pro lepší vysvětlení si představte scénář, ve kterém spotřebitel vyšle požadavek na přijetí, ale než ji může zpracovat, dojde v něm k chybě a ukončí se.
Vzhledem k tomu, že Service Bus označila zprávu jako spotřebovaná, pak když se aplikace znovu spustí a začne znovu přijímat zprávy, vynechala zprávu, která byla spotřebována před selháním.

V režimu **PeekLock** se přijme operace se dvěma fázemi, která umožňuje podporovat aplikace, které nemůžou tolerovat chybějící zprávy. Když Service Bus přijme požadavek, najde zprávu, která je na řadě ke spotřebování, uzamkne ji proti spotřebování jinými spotřebiteli a vrátí ji do aplikace. Poté, co aplikace dokončí zpracování zprávy (nebo je uloží spolehlivě pro budoucí zpracování), dokončí druhou fázi procesu příjmu voláním metody **Complete ()** na přijatou zprávu. Když Service Bus uvidí volání **Complete ()** , označí zprávu jako spotřebou a odebere ji z fronty. 

Následující příklad ukazuje, jak lze přijímat a zpracovávat zprávy pomocí režimu **PeekLock** (nikoli ve výchozím režimu). Následující příklad používá model zpětného volání s registrovanou obslužnou rutinou zprávy a zpracovává zprávy při jejich doručení do naší `TestQueue` . Tento režim volá funkci **Complete ()** automaticky, protože zpětné volání vrátí hodnotu Standard a zavolá **Abandon ()** , pokud zpětné volání vyvolá výjimku. 

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
Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud aplikace příjemce z nějakého důvodu nemůže zprávu zpracovat, může zavolat metodu **Abandon ()** na objekt klienta s tokenem zámku přijaté zprávy získaným prostřednictvím **getLockToken ()**. To způsobí, že Service Bus zprávu odemkne ve frontě a zpřístupní ji pro další přijetí, buďto stejnou spotřebitelskou aplikací nebo jinou spotřebitelskou aplikací.

Je také časový limit přidružený ke zprávě uzamčený ve frontě a pokud aplikace nedokáže zpracovat zprávu před vypršením časového limitu zámku (například pokud dojde k selhání aplikace), Service Bus automaticky odemkne zprávu a zpřístupní ji, aby byla k dispozici.

V případě, že dojde k chybě aplikace po zpracování zprávy, ale před vydáním žádosti o **dokončení ()** , je zpráva po restartování znovu doručena do aplikace. Tomu se často říká *Zpracování nejméně jednou* – to znamená, že každá zpráva se zpracuje alespoň jednou, ale v některých situacích se může doručit víckrát. Pokud daný scénář nemůže tolerovat zpracování víc než jednou, vývojáři aplikace by měli přidat další logiku navíc pro zpracování víckrát doručené zprávy. To se často dosahuje pomocí metody **getMessageId** zprávy, která zůstává konstantní při pokusůch o doručení.

> [!NOTE]
> Prostředky Service Bus můžete spravovat pomocí [Service Bus Exploreru](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer umožňuje uživatelům připojit se k oboru názvů Service Bus a snadno spravovat entity zasílání zpráv. Tento nástroj poskytuje pokročilé funkce, jako jsou funkce importu a exportu, nebo možnost testovat témata, fronty, odběry, služby Relay, centra oznámení a centra událostí. 

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili základy Service Busch front, najdete další informace v tématu [fronty, témata a předplatná][Queues, topics, and subscriptions] .

Další informace naleznete ve [Středisku pro vývojáře Java](https://azure.microsoft.com/develop/java/).

[Azure SDK for Java]: /azure/developer/java/sdk/java-sdk-azure-get-started
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage