---
title: Jak pomocí protokolu AMQP 1.0 rozhraní Java API služby Service Bus | Dokumentace Microsoftu
description: Jak používat službu zpráva Java (JMS) pomocí služby Azure Service Bus a rozšířené řízení front zpráv protokolu (AMQP) 1.0.
services: service-bus-messaging
documentationcenter: java
author: axisc
manager: timlt
editor: spelluru
ms.assetid: be766f42-6fd1-410c-b275-8c400c811519
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 08/10/2018
ms.author: aschhab
ms.openlocfilehash: f0c3aac95b1d19dc3f217cc146532254d8740ca0
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54853285"
---
# <a name="how-to-use-the-java-message-service-jms-api-with-service-bus-and-amqp-10"></a>Jak Java Message Service (JMS) rozhraní API pomocí služby Service Bus a protokolu AMQP 1.0
Rozšířené řízení front zpráv protokolu (AMQP) 1.0 je efektivní a spolehlivý přenosový protokol zasílání zpráv, který vám pomůže vytvářet aplikace napříč platformami, robustní zasílání zpráv.

Podpora pro protokolu AMQP 1.0 ve službě Service Bus znamená, že můžete použít řazení do fronty a publikování/přihlášení k odběru funkce zprostředkovaného zasílání zpráv z celou řadu platforem s využitím efektivní binární protokol. Kromě toho můžete vytvářet aplikace skládá z komponenty sestavené pomocí kombinace jazyků, architektur a operační systémy.

Tento článek vysvětluje, jak pomocí služby Service Bus (fronty a témata publikace nebo odběru) funkce zasílání zpráv v aplikacích v Javě pomocí Oblíbené Java zprávy služby JMS () standardní rozhraní API. Je [doprovodný článek](service-bus-amqp-dotnet.md) , který vysvětluje, jak provést stejný pomocí rozhraní .NET API služby Service Bus. Tyto dvě příručky můžete použít společně se dozvíte o zasílání zpráv mezi platformami pomocí AMQP 1.0.

## <a name="get-started-with-service-bus"></a>Začínáme se službou Service Bus
Tento průvodce to předpokládá, že už máte obor názvů Service Bus, který obsahuje frontu s názvem **basicqueue**. Pokud ho nevidíte, pak můžete [vytvořit obor názvů a frontu](service-bus-create-namespace-portal.md) pomocí [webu Azure portal](https://portal.azure.com). Další informace o tom, jak vytvořit obory názvů služby Service Bus a fronty, naleznete v tématu [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Dělené fronty a témata podporují také AMQP. Další informace najdete v tématu [segmentované entity zasílání zpráv](service-bus-partitioning.md) a [podpory AMQP 1.0 pro službu Service Bus dělené fronty a témata](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>Stahuje se knihovna klienta protokolu AMQP 1.0 JMS
Informace o tom, kde chcete stáhnout nejnovější verzi klientské knihovny Apache Qpid JMS protokolu AMQP 1.0, najdete v článku [ https://qpid.apache.org/download.html ](https://qpid.apache.org/download.html).

Následující čtyři soubory JAR z archivní úrovně distribuce Apache Qpid JMS protokolu AMQP 1.0 je nutné přidat do cesta třídy Java při vytváření a spouštění aplikací JMS službou Service Bus:

* geronimo-jms\_1.1\_spec-1.0.jar
* qpid-jms-client-[version].jar

> ! [POZNÁMKA] JMS JAR názvy a verze se možná změnily Podrobnosti najdete v tématu [Qpid JMS - protokolu AMQP 1.0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).

## <a name="coding-java-applications"></a>Kódování aplikací v Javě
### <a name="java-naming-and-directory-interface-jndi"></a>Pojmenování Javy a rozhraní adresáře (JNDI)
K vytvoření oddělení mezi názvy logické a fyzické používá JMS Java pojmenování a Interface adresáře (JNDI). Dva typy objektů JMS se budou překládat pomocí JNDI: ConnectionFactory a cíl. JNDI používá zprostředkovatele modelu, do kterého můžete zařadit jiného adresáře služby, které zvládnou povinnosti název řešení. Apache Qpid JMS protokolu AMQP 1.0 knihovny obsahuje jednoduché vlastnosti formátování souborové JNDI zprostředkovatele, který je nakonfigurovaný pomocí vlastnosti souboru z následujících akcí:

```
# servicebus.properties - sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="setup-jndi-context-and-configure-the-connectionfactory"></a>Kontext JNDI nainstalovat a nakonfigurovat ConnectionFactory

**ConnectionString** odkazuje k dispozici "sdílené zásady přístupu' v jedné [webu Azure Portal](https://portal.azure.com) pod **primární připojovací řetězec**
```java
// The connection string builder is the only part of the azure-servicebus SDK library
// we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
// connection string. 
ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");

// Look up queue
Destination queue = (Destination) context.lookup("QUEUE");
```

#### <a name="configure-producer-and-consumer-destination-queues"></a>Konfigurace Producer and Consumer cílové fronty
Slouží k definování cíle v poskytovateli Qpid vlastnosti souboru JNDI položka je v následujícím formátu:

Chcete-li vytvořit cílovou frontu pro výrobce- 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Producer
MessageProducer producer = session.createProducer(queue);
```

Chcete-li vytvořit cílovou frontu pro spotřebitele- 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Consumer
MessageConsumer consumer = session.createConsumer(queue);
```

### <a name="write-the-jms-application"></a>Napíšeme aplikaci JMS
Neexistují žádné zvláštní rozhraní API nebo možnosti požaduje se při použití JMS službou Service Bus. Existují však několik omezení, které se věnujeme později. Stejně jako u jakékoli aplikace JMS nejprve thing požadované je konfigurace JNDI prostředí, které mají být schopen převést **ConnectionFactory** a cíle.

#### <a name="configure-the-jndi-initialcontext"></a>Konfigurace JNDI InitialContext
JNDI prostředí je nakonfigurované pomocí předání do konstruktoru třídy javax.naming.InitialContext zatřiďovací tabulku informace o konfiguraci. Dvě požadované prvky v zatřiďovací tabulky jsou název třídy objektu pro vytváření počáteční kontext a adresa URL zprostředkovatele. Následující kód ukazuje, jak nakonfigurovat prostředí JNDI používat Qpid vlastnosti souboru na základě JNDI poskytovatele pomocí vlastnosti souboru s názvem **servicebus.properties**.

```java
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + \
"?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Jednoduchou aplikaci JMS pomocí fronty Service Bus
Následující ukázkový program JMS TextMessages odešle do fronty služby Service Bus s JNDI logický název fronty a přijímá zprávy zpět.

Všechny dostanete všechny zdrojový kód a konfigurační informace z [služby Service Bus ukázky JMS fronty rychlý Start pro Azure](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart)

```java
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE file in the project root for full license information.

package com.microsoft.azure.servicebus.samples.jmsqueuequickstart;

import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import org.apache.commons.cli.*;
import org.apache.log4j.*;

import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.util.Hashtable;
import java.util.concurrent.atomic.AtomicInteger;
import java.util.function.Function;

/**
 * This sample demonstrates how to send messages from a JMS Queue producer into
 * an Azure Service Bus Queue, and receive them with a JMS message consumer.
 * JMS Queue. 
 */
public class JmsQueueQuickstart {

    // Number of messages to send
    private static int totalSend = 10;
    //Tracking counter for how many messages have been received; used as termination condition
    private static AtomicInteger totalReceived = new AtomicInteger(0);
    // log4j logger 
    private static Logger logger = Logger.getRootLogger();

    public void run(String connectionString) throws Exception {

        // The connection string builder is the only part of the azure-servicebus SDK library
        // we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
        // connection string. 
        ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
        // set up JNDI context
        Hashtable<String, String> hashtable = new Hashtable<>();
        hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
        hashtable.put("queue.QUEUE", "BasicQueue");
        hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
        Context context = new InitialContext(hashtable);
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        
        // Look up queue
        Destination queue = (Destination) context.lookup("QUEUE");

        // we create a scope here so we can use the same set of local variables cleanly 
        // again to show the receive side separately with minimal clutter
        {
            // Create Connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            // Create Session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

            // Create producer
            MessageProducer producer = session.createProducer(queue);

            // Send messages
            for (int i = 0; i < totalSend; i++) {
                BytesMessage message = session.createBytesMessage();
                message.writeBytes(String.valueOf(i).getBytes());
                producer.send(message);
                System.out.printf("Sent message %d.\n", i + 1);
            }

            producer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        {
            // Create Connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            connection.start();
            // Create Session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            // Create consumer
            MessageConsumer consumer = session.createConsumer(queue);
            // create a listener callback to receive the messages
            consumer.setMessageListener(message -> {
                try {
                    // receives message is passed to callback
                    System.out.printf("Received message %d with sq#: %s\n",
                            totalReceived.incrementAndGet(), // increments the tracking counter
                            message.getJMSMessageID());
                    message.acknowledge();
                } catch (Exception e) {
                    logger.error(e);
                }
            });

            // wait on the main thread until all sent messages have been received
            while (totalReceived.get() < totalSend) {
                Thread.sleep(1000);
            }
            consumer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        System.out.printf("Received all messages, exiting the sample.\n");
        System.out.printf("Closing queue client.\n");
    }

    public static void main(String[] args) {

        System.exit(runApp(args, (connectionString) -> {
            JmsQueueQuickstart app = new JmsQueueQuickstart();
            try {
                app.run(connectionString);
                return 0;
            } catch (Exception e) {
                System.out.printf("%s", e.toString());
                return 1;
            }
        }));
    }

    static final String SB_SAMPLES_CONNECTIONSTRING = "SB_SAMPLES_CONNECTIONSTRING";

    public static int runApp(String[] args, Function<String, Integer> run) {
        try {

            String connectionString = null;

            // parse connection string from command line
            Options options = new Options();
            options.addOption(new Option("c", true, "Connection string"));
            CommandLineParser clp = new DefaultParser();
            CommandLine cl = clp.parse(options, args);
            if (cl.getOptionValue("c") != null) {
                connectionString = cl.getOptionValue("c");
            }

            // get overrides from the environment
            String env = System.getenv(SB_SAMPLES_CONNECTIONSTRING);
            if (env != null) {
                connectionString = env;
            }

            if (connectionString == null) {
                HelpFormatter formatter = new HelpFormatter();
                formatter.printHelp("run jar with", "", options, "", true);
                return 2;
            }
            return run.apply(connectionString);
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
            return 3;
        }
    }
}
```

### <a name="run-the-application"></a>Spuštění aplikace
Předání **připojovací řetězec** ze sdílené zásady přístupu ke spuštění aplikace.
Níže je výstup ve formátu spuštěním aplikace:

```
> mvn clean package
>java -jar ./target/jmsqueuequickstart-1.0.0-jar-with-dependencies.jar -c "<CONNECTION_STRING>"

Sent message 1.
Sent message 2.
Sent message 3.
Sent message 4.
Sent message 5.
Sent message 6.
Sent message 7.
Sent message 8.
Sent message 9.
Sent message 10.
Received message 1 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-1
Received message 2 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-2
Received message 3 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-3
Received message 4 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-4
Received message 5 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-5
Received message 6 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-6
Received message 7 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-7
Received message 8 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-8
Received message 9 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-9
Received message 10 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-10
Received all messages, exiting the sample.
Closing queue client.

```

## <a name="amqp-disposition-and-service-bus-operation-mapping"></a>AMQP dispozice a mapování operace služby Service Bus
Zde je, jak se dispozice AMQP přeloží na operaci služby Service Bus:

```
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```


## <a name="unsupported-features-and-restrictions"></a>Nepodporované funkce a omezení
Při použití JMS prostřednictvím protokolu AMQP 1.0 se Service Bus, konkrétně existují následující omezení:

* Pouze jeden **MessageProducer** nebo **MessageConsumer** smí za **relace**. Pokud je potřeba vytvořit několik **MessageProducers** nebo **MessageConsumers** v aplikaci, vytvořte vyhrazený **relace** pro každý z nich.
* Odběry témat volatile se momentálně nepodporují.
* **MessageSelectors** se momentálně nepodporují.
* Počet relací a distribuované transakce nejsou podporované.

Kromě toho Azure Service Bus rozdělí rovina řízení v rovině dat a proto nepodporuje řadu funkcí pro JMS dynamické topologie:

| Nepodporovaná – metoda          | Nahradit hodnotou                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| createDurableSubscriber     | Vytvoří předplatné tématu přenos zprávy modulu pro výběr                                 |
| createDurableConsumer       | Vytvoří předplatné tématu přenos zprávy modulu pro výběr                                 |
| createSharedConsumer        | Témata služby Service Bus jsou vždy ke sdílení, najdete výše                                       |
| createSharedDurableConsumer | Témata služby Service Bus jsou vždy ke sdílení, najdete výše                                       |
| createTemporaryTopic        | Vytvoření tématu přes správu rozhraní API/tools/portál s *AutoDeleteOnIdle* nastavte období vypršení platnosti |
| createTopic                 | Vytvoření tématu přes správu rozhraní API/tools/portál                                           |
| odhlášení odběru                 | Odstraňte téma správy rozhraní API/tools/portál                                             |
| createBrowser               | Nepodporovaný. Použití funkce Peek() rozhraní API služby Service Bus                         |
| createQueue                 | Vytvořte frontu přes správu rozhraní API/tools/portál                                           | 
| createTemporaryQueue        | vytvořit frontu přes správu rozhraní API/tools/portál s *AutoDeleteOnIdle* nastavte období vypršení platnosti |

## <a name="summary"></a>Souhrn
Tato příručka vám ukázal, jak používat funkce Service Bus pro zprostředkované zasílání zpráv (fronty a témata publikace nebo odběru) z Javy pomocí oblíbených rozhraní API pro JMS a protokolu AMQP 1.0.

Služba Service Bus protokolu AMQP 1.0 můžete použít také z jiných jazyků, včetně .NET, C, Python a PHP. Komponenty sestavené pomocí těchto různých jazyků můžete spolehlivě výměnu zpráv a s plnou věrností pomocí protokolu AMQP 1.0 podporují ve službě Service Bus.

## <a name="next-steps"></a>Další postup
* [Podpora AMQP 1.0 ve službě Azure Service Bus](service-bus-amqp-overview.md)
* [Jak rozhraní .NET API služby Service Bus pomocí protokolu AMQP 1.0](service-bus-dotnet-advanced-message-queuing.md)
* [Service Bus AMQP 1.0 – Příručka vývojáře](service-bus-amqp-dotnet.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Středisko pro vývojáře Java](https://azure.microsoft.com/develop/java/)

