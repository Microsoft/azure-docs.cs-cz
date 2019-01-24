---
title: Jak používat fronty služby Azure Service Bus pomocí Javy | Dokumentace Microsoftu
description: Naučte se používat fronty Service Bus v Azure. Ukázky kódu napsané v jazyce Java.
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
ms.topic: article
ms.date: 09/13/2018
ms.author: aschhab
ms.openlocfilehash: 9a26a08e7b1fb313e25a7dc8d9f5e58e843fca76
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54849614"
---
# <a name="how-to-use-service-bus-queues-with-java"></a>Jak používat fronty služby Service Bus pomocí Javy
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Tento článek popisuje, jak používat fronty Service Bus. Ukázky jsou napsané v jazyce Java a použití [sady Azure SDK pro Javu][Azure SDK for Java]. Mezi popsané scénáře patří **vytváření front**, **odesílání a přijímání zpráv**, a **odstranění front**.

> [!NOTE]
> Ukázky v Javě můžete najít na Githubu v [úložiště azure-service-bus](https://github.com/Azure/azure-service-bus/tree/master/samples/Java).

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="create-a-service-bus-namespace"></a>Vytvoření oboru názvů Service Bus
[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-a-service-bus-queue"></a>Vytvořit frontu služby Service Bus
[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurace aplikace pro použití služby Service Bus
Ujistěte se, že jste nainstalovali [sady Azure SDK pro Javu] [ Azure SDK for Java] před vytvořením této ukázky. Pokud používáte Eclipse, můžete nainstalovat [sady Azure Toolkit pro Eclipse] [ Azure Toolkit for Eclipse] , který obsahuje sadu Azure SDK pro Javu. Poté můžete přidat **knihovny Microsoft Azure Libraries for Java** do projektu:

![](./media/service-bus-java-how-to-use-queues/eclipselibs.png)

Přidejte následující `import` příkazy do horní části souboru Java:

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
Pro odesílání zpráv do fronty Service Bus, vaše aplikace vytvoří instanci **QueueClient** objektu a odesílá zprávy asynchronně. Následující kód ukazuje, jak odeslat zprávu fronty, který byl vytvořen prostřednictvím portálu.

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

Zprávy odeslané a přijaté ze služby Service Bus jsou fronty instance [zpráva](/java/api/com.microsoft.azure.servicebus.message?view=azure-java-stable) třídy. Zpráva objekty mají sadu standardních vlastností (třeba popisek a hodnota TimeToLive), slovník používaný pro udržení vlastních vlastností specifické pro aplikace a tělo s libovolnými aplikačními daty. Aplikace může tělo zprávy nastavit tak předá jakýkoli serializovatelný objekt do konstruktoru zprávy a příslušný serializátor pak bude sloužit k serializaci objektu. Alternativně můžete zadat **java. VSTUPNĚ-VÝSTUPNÍCH OPERACÍ. InputStream** objektu.


Fronty Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Počet zpráv držených ve frontě není omezený, ale celková velikost zpráv držených ve frontě omezená je. Velikost fronty se definuje při vytvoření, maximální limit je 5 GB.

## <a name="receive-messages-from-a-queue"></a>Příjem zpráv z fronty
Hlavní způsob, jak pro příjem zpráv z fronty je použití **ServiceBusContract** objektu. Přijaté zprávy můžete pracovat ve dvou různých režimech: **ReceiveAndDelete** a **PeekLock**.

Při použití **ReceiveAndDelete** režimu přijímat je jednorázová operace – to znamená, když Service Bus přijme požadavek čtení zprávy ve frontě, označí zprávu jako spotřebovávanou a vrátí ji do aplikace. **ReceiveAndDelete** režimu (což je výchozí režim) je nejjednodušší model a funguje nejlépe pro scénáře, ve kterých aplikace může tolerovat možnost, zprávy v případě selhání. Pro lepší vysvětlení si představte scénář, ve kterém spotřebitel vyšle požadavek na přijetí, ale než ji může zpracovat, dojde v něm k chybě a ukončí se.
Vzhledem k tomu, že Service Bus označil zprávu jako spotřebovávanou, pak když aplikace znovu spustí a začne znovu přijímat zprávy, byl vynechán zprávu, která se spotřebovala před pádem vynechá.

V **PeekLock** režimu, zobrazí se stane dvoufázového operaci, která umožňuje podporuje aplikace, které nemůžou tolerovat vynechání zpráv. Když Service Bus přijme požadavek, najde zprávu, která je na řadě ke spotřebování, uzamkne ji proti spotřebování jinými spotřebiteli a vrátí ji do aplikace. Poté, co aplikace dokončí zpracování zprávy (nebo spolehlivě uloží pro pozdější zpracování), dokončení druhé fáze přijetí voláním **odstranit** na přijatou zprávu. Když Service Bus uvidí **odstranit** volání, označí zprávu jako spotřebovávanou a odebere ji z fronty.

Následující příklad ukazuje, jak lze přijímat zprávy a zpracovaná pomocí **PeekLock** režimu (není výchozím režimu). Následující příklad provede nekonečnou smyčku a zpracovávat zprávy, když dorazí do našich `TestQueue`:

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
Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud přijímající aplikace nedokáže zpracovat zprávu z nějakého důvodu, pak může volat **unlockMessage** metoda na přijatou zprávu (místo **deleteMessage** metoda). To způsobí, že Service Bus zprávu odemkne ve frontě a zpřístupní ji pro další přijetí, buďto stejnou spotřebitelskou aplikací nebo jinou spotřebitelskou aplikací.

K dispozici je také vypršení časového limitu přidružené zpráva uzamčená ve frontě, a pokud aplikace zprávu nezpracuje zámku vyprší časový limit (například pokud aplikace spadne), Service Bus zprávu automaticky odemkne a díky tomu k dispozici pro další přijetí.

V případě, že aplikace spadne po zpracování zprávy, ale předtím, než **deleteMessage** požadavku a pak je víckrát do aplikace při restartování. Tomu se často říká *Zpracování nejméně jednou* – to znamená, že každá zpráva se zpracuje alespoň jednou, ale v některých situacích se může doručit víckrát. Pokud daný scénář nemůže tolerovat zpracování víc než jednou, vývojáři aplikace by měli přidat další logiku navíc pro zpracování víckrát doručené zprávy. To se často opírá **getMessageId** metoda zprávy, která zůstává konstantní pokusu o doručení.

## <a name="next-steps"></a>Další kroky
Teď, když jste se seznámili se základy front Service Bus, najdete v článku [fronty, témata a odběry] [ Queues, topics, and subscriptions] Další informace.

Další informace naleznete ve [Středisku pro vývojáře Java](https://azure.microsoft.com/develop/java/).

[Azure SDK for Java]: http://azure.microsoft.com/develop/java/
[Azure Toolkit for Eclipse]: https://msdn.microsoft.com/library/azure/hh694271.aspx
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
