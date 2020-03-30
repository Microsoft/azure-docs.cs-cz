---
title: Použití rozhraní AMQP s rozhraním API služby Java Message Service & Azure Service Bus
description: How to use the Java Message Service (JMS) with Azure Service Bus and Advanced Message Queuing Protocol (AMQP) 1.0.
services: service-bus-messaging
documentationcenter: java
author: axisc
editor: spelluru
ms.assetid: be766f42-6fd1-410c-b275-8c400c811519
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/22/2019
ms.author: aschhab
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: cd06838abbb69af5684fdea18c42f6a8f95ffe2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77371251"
---
# <a name="use-the-java-message-service-jms-with-azure-service-bus-and-amqp-10"></a>Use the Java Message Service (JMS) with Azure Service Bus and AMQP 1.0
Tento článek vysvětluje, jak používat funkce zasílání zpráv Azure Service Bus (fronty a publikování/odběr témata) z aplikací Java pomocí populární java message service service (JMS) rozhraní API standard. Je [doprovodný článek,](service-bus-amqp-dotnet.md) který vysvětluje, jak provést totéž pomocí rozhraní AZURE Service Bus .NET API. Pomocí těchto dvou průvodců můžete společně získat informace o zasílání zpráv mezi platformami pomocí amqp 1.0.

Protokol AMQP (Advanced Message Queuing Protocol) 1.0 (Advanced Message Queuing Protocol) 1.0 je efektivní, spolehlivý protokol zasílání zpráv na úrovni drátu, který lze použít k vytváření robustních aplikací pro zasílání zpráv napříč platformami.

Podpora AMQP 1.0 v Azure Service Bus znamená, že můžete použít fronty a publikovat a odebírat zprostředkované funkce zasílání zpráv z řady platforem pomocí efektivníbinární protokol. Kromě toho můžete vytvářet aplikace složené z součástí vytvořených pomocí kombinace jazyků, architektur a operačních systémů.

## <a name="get-started-with-service-bus"></a>Začínáme se službou Service Bus
Tato příručka předpokládá, že již máte obor názvů `basicqueue`Service Bus obsahující frontu s názvem . Pokud tak nechcete, můžete [vytvořit obor názvů a fronty](service-bus-create-namespace-portal.md) pomocí [portálu Azure](https://portal.azure.com). Další informace o vytváření oborů názvů a front služby Service Bus naleznete v tématu [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> AMQP také podporují rozdělené fronty a témata. Další informace naleznete v [tématu Partitioned messaging entities](service-bus-partitioning.md) a [AMQP 1.0 support for Service Bus partitioned queues and topics](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>Stažení klientské knihovny AMQP 1.0 JMS
Informace o tom, kde stáhnout nejnovější verzi klientské knihovny Apache Qpid JMS AMQP 1.0, naleznete na adrese [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html).

Při vytváření a spouštění aplikací JMS se službou Service Bus je nutné přidat následující čtyři soubory JAR z distribučního archivu Apache Qpid JMS AMQP 1.0 do cesty Java CLASSPATH:

* geronimo-jms\_1.1\_spec-1.0.jar
* qpid-jms-client-[verze].jar

> [!NOTE]
> JMS JAR názvy a verze může změnit. Podrobnosti viz [Qpid JMS - AMQP 1.0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).

## <a name="coding-java-applications"></a>Kódování java aplikací
### <a name="java-naming-and-directory-interface-jndi"></a>Java pojmenování a adresář rozhraní (JNDI)
JMS používá Java Naming and Directory Interface (JNDI) k vytvoření oddělení logických názvů a fyzických názvů. Dva typy objektů JMS jsou vyřešeny pomocí JNDI: ConnectionFactory a Destination. JNDI používá model zprostředkovatele, do kterého můžete připojit různé adresářové služby pro zpracování povinností překladu názvů. Knihovna Apache Qpid JMS AMQP 1.0 je dodávána s jednoduchým poskytovatelem JNDI založeným na souboru vlastností, který je nakonfigurován pomocí souboru vlastností následujícího formátu:

```TEXT
# servicebus.properties - sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="setup-jndi-context-and-configure-the-connectionfactory"></a>Nastavení kontextu JNDI a Konfigurace technologie ConnectionFactory

**ConnectionString** odkazovaný v tom, který je k dispozici v "Zásady sdíleného přístupu" na [portálu Azure](https://portal.azure.com) v části **Primární připojovací řetězec**
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

#### <a name="configure-producer-and-consumer-destination-queues"></a>Konfigurace front určení výrobce a spotřebitele
Položka použitá k definování cíle v souboru Vlastností Qpid jndi zprostředkovatele je následující formát:

Vytvoření cílové fronty pro výrobce - 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Producer
MessageProducer producer = session.createProducer(queue);
```

Vytvoření cílové fronty pro příjemce - 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Consumer
MessageConsumer consumer = session.createConsumer(queue);
```

### <a name="write-the-jms-application"></a>Napsat aplikaci JMS
Při použití JMS se službou Service Bus nejsou vyžadována žádná speciální řešení API ani možnosti. Existuje však několik omezení, která budou pokryta později. Stejně jako u všech aplikací JMS, první věc, kterou je třeba, je konfigurace prostředí JNDI, aby bylo možné vyřešit **ConnectionFactory** a cíle.

#### <a name="configure-the-jndi-initialcontext"></a>Konfigurace kontextu INDI InitialContext
Prostředí JNDI je konfigurováno předáním hashtable informací o konfiguraci do konstruktoru třídy javax.naming.InitialContext. Dva požadované prvky v hashtable jsou název třídy počáteční context factory a adresa URL zprostředkovatele. Následující kód ukazuje, jak nakonfigurovat prostředí JNDI tak, aby používalo zprostředkovatele JNDI založeného na souboru vlastností Qpid se souborem vlastností s názvem **servicebus.properties**.

```java
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + \
"?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Jednoduchá aplikace JMS používající frontu služby Service Bus
Následující ukázkový program odesílá textové zprávy JMS do fronty sběrnice s logickým názvem JNDI FRONTY a přijímá zprávy zpět.

Všichni máte přístup ke všem zdrojový kód a informace o konfiguraci z [ukázky azure service bus JMS fronty rychlý start](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart)

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
Předajte **připojovací řetězec** ze zásad sdíleného přístupu ke spuštění aplikace.
Níže je výstup formuláře spuštěním aplikace:

```Output
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

## <a name="amqp-disposition-and-service-bus-operation-mapping"></a>Dispozice AMQP a mapování provozu sběrnice Service Bus
Zde je způsob, jakým dispozice AMQP překládá do operace service bus:

```Output
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```

## <a name="jms-topics-vs-service-bus-topics"></a>Témata JMS vs. Témata servisní ho servisu
Pomocí Azure Service Bus témata a předplatná prostřednictvím rozhraní API Java Message Service (JMS) poskytuje základní možnosti odesílání a přijímání. Je to vhodná volba při přenosu aplikací z jiných zprostředkovatelů zpráv s rozhraními API kompatibilními s JMS, i když se témata služby Service Bus liší od témat JMS a vyžadují několik úprav. 

Témata Azure Service Bus směrují zprávy do pojmenovaných, sdílených a trvalých předplatných, která se spravují prostřednictvím rozhraní Azure Resource Management, nástrojů příkazového řádku Azure nebo prostřednictvím portálu Azure. Každé předplatné umožňuje až 2000 pravidel výběru, z nichž každý může mít podmínku filtru a pro filtry SQL také akce transformace metadat. Každá shoda podmínky filtru vybere vstupní zprávu, která má být zkopírována do předplatného.  

Příjem zpráv z odběrů je identické příjem zpráv z front. Každé předplatné má přidruženou frontu nedoručených zpráv a možnost automaticky předávat zprávy do jiné fronty nebo témat. 

Témata JMS umožňují klientům dynamicky vytvářet trvalé a trvalé předplatitele, které volitelně umožňují filtrování zpráv pomocí voliče zpráv. Tyto nesdílené entity nejsou podporovány service bus. Syntaxe pravidla filtru SQL pro service bus je však podobná syntaxi voliče zpráv podporovanou JMS. 

Strana vydavatele tématu JMS je kompatibilní se službou Service Bus, jak je znázorněno v této ukázce, ale dynamické odběratele nejsou. Následující kódy JMS související s topologii nejsou podporovány službou Service Bus. 

## <a name="unsupported-features-and-restrictions"></a>Nepodporované funkce a omezení
Při použití JMS přes AMQP 1.0 se službou Service Bus existují následující omezení, a to:

* Pouze jeden **MessageProducer** nebo **MessageConsumer** je povoleno na **relaci**. Pokud potřebujete vytvořit více **MessageProducers** nebo **MessageConsumers** v aplikaci, **vytvořte** vyhrazenou relaci pro každou z nich.
* Odběry těkavých témat nejsou aktuálně podporovány.
* **Voliči zpráv** nejsou aktuálně podporovány.
* Distribuované transakce nejsou podporovány (ale jsou podporovány transakční relace).

Azure Service Bus navíc rozdělí rovinu ovládacího prvku od roviny dat a proto nepodporuje několik funkcí dynamické topologie JMS:

| Nepodporovaná metoda          | Nahradit hodnotou                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| createDurableSubscriber     | Vytvoření odběru tématu přenesené na volič zpráv                                 |
| createDurableConsumer       | Vytvoření odběru tématu přenesené na volič zpráv                                 |
| createSharedConsumer        | Témata služby Service Bus jsou vždy sdíletelná, viz výše                                       |
| createSharedDurableConsumer | Témata služby Service Bus jsou vždy sdíletelná, viz výše                                       |
| createTemporaryTopic        | vytvoření tématu pomocí rozhraní API/nástrojů/portálu pro správu s *nastavením automatického* odstranění onidle na dobu platnosti |
| createTopic                 | vytvoření tématu pomocí api pro správu/nástroje/portál                                           |
| Odhlásit                 | odstranění rozhraní API/nástrojů/portálu pro správu témat                                             |
| vytvořitprohlížeč               | Nepodporovaný. Použití funkce Peek() rozhraní API služby Service Bus                         |
| createQueue                 | vytvoření fronty pomocí rozhraní API pro správu/nástroje/portál                                           | 
| createTemporaryQueue        | vytvoření fronty pomocí rozhraní API/nástrojů/portálu pro správu s nastaveným na dobu platnosti automatického odstranění *onidle* |
| receiveNoWait               | použijte metodu receive() poskytovanou sadou Service Bus SDK a zadejte velmi nízký nebo nulový časový výběh |

## <a name="summary"></a>Souhrn
Tento návod ukázal, jak používat funkce service bus zprostředkované zasílání zpráv (fronty a publikovat / odebírat témata) z Javy pomocí populárníjms API a AMQP 1.0.

Service Bus AMQP 1.0 můžete také použít z jiných jazyků, včetně .NET, C, Python a PHP. Komponenty vytvořené pomocí těchto různých jazyků si mohou vyměňovat zprávy spolehlivě a v plné věrnosti pomocí podpory AMQP 1.0 v service bus.

## <a name="next-steps"></a>Další kroky
* [Podpora AMQP 1.0 ve službě Azure Service Bus](service-bus-amqp-overview.md)
* [Použití rozhraní AMQP 1.0 s rozhraním SERVICE BUS .NET API](service-bus-dotnet-advanced-message-queuing.md)
* [Průvodce pro vývojáře Service Bus AMQP 1.0](service-bus-amqp-dotnet.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Středisko pro vývojáře Java](https://azure.microsoft.com/develop/java/)

