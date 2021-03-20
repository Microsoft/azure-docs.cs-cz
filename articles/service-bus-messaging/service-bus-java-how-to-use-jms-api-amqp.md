---
title: Použití AMQP s rozhraním API služby Java Message Service a Azure Service Bus
description: Použijte službu JMS (Java Message Service) s Azure Service Bus a rozšířený protokol řízení front zpráv (AMQP) (AMQP) 1,0.
ms.topic: article
ms.date: 06/23/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 97031abaedaa3e5595e290fa0292646feb744d47
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "90086687"
---
# <a name="use-the-java-message-service-with-azure-service-bus-and-amqp-10"></a>Použijte službu zprávy Java s Azure Service Bus a AMQP 1,0

> [!WARNING]
> Tento článek se *týká omezené podpory* rozhraní JMS (Java Message Service) 1,1 API a existuje jenom pro Azure Service Bus úrovně Standard.
>
> Plná podpora rozhraní Java Message Service 2,0 API je k dispozici pouze na [úrovni služby Azure Service Bus Premium ve verzi Preview](how-to-use-java-message-service-20.md). Doporučujeme použít tuto úroveň.
>

Tento článek vysvětluje, jak používat funkce Service Bus zasílání zpráv z aplikací Java pomocí oblíbených JMS API Standard. Tyto funkce zasílání zpráv zahrnují fronty a publikování nebo přihlášení k odběru témat. [Doprovodný článek](service-bus-amqp-dotnet.md) vysvětluje, jak to samé provést pomocí rozhraní API Azure Service Bus .NET. Tyto dva články můžete použít společně a získat informace o zasílání zpráv mezi různými platformami pomocí rozšířený protokol řízení front zpráv (AMQP) (AMQP) 1,0.

AMQP 1,0 je efektivní a spolehlivý přenosový protokol zasílání zpráv, který můžete použít k vytváření robustních aplikací zasílání zpráv pro různé platformy.

Podpora pro AMQP 1,0 v Service Bus znamená, že můžete používat funkce řazení a publikování nebo předplacení zprostředkovaných zpráv z řady platforem pomocí efektivního binárního protokolu. Můžete také vytvářet aplikace tvořené komponentami sestavenými pomocí kombinace jazyků, platforem a operačních systémů.

## <a name="get-started-with-service-bus"></a>Začínáme se službou Service Bus

V tomto článku se předpokládá, že už máte Service Bus obor názvů, který obsahuje frontu s názvem `basicqueue` . Pokud to neuděláte, můžete [vytvořit obor názvů a frontu](service-bus-create-namespace-portal.md) pomocí [Azure Portal](https://portal.azure.com). Další informace o tom, jak vytvořit Service Bus obory názvů a fronty, najdete v tématu Začínáme [s Service Bus fronty](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Dělené fronty a témata také podporují AMQP. Další informace najdete v tématech [rozdělené entity zasílání zpráv](service-bus-partitioning.md) a [Podpora AMQP 1,0 pro Service Bus dělené fronty a témata](./service-bus-amqp-protocol-guide.md).
> 
> 

## <a name="download-the-amqp-10-jms-client-library"></a>Stažení klientské knihovny AMQP 1,0 JMS

Informace o tom, kde stáhnout nejnovější verzi klientské knihovny Apache Qpid JMS AMQP 1,0, najdete na [webu Apache Qpid Download](https://qpid.apache.org/download.html).

Pokud sestavíte a spouštíte aplikace JMS pomocí Service Bus, je nutné přidat následující soubory JAR z distribučního archivu Apache Qpid JMS AMQP 1,0 do proměnné prostředí pro cestu jazyka Java.

* Geronimo-JMS \_ 1,1 \_ spec-1.0. jar
* qpid-JMS-Client-[verze]. jar

> [!NOTE]
> Je možné, že se změnily názvy a verze JAR JMS. Další informace najdete v tématu [QPID JMS AMQP 1,0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).

## <a name="code-java-applications"></a>Aplikace kódu Java

### <a name="java-naming-and-directory-interface"></a>Rozhraní Java – názvové rozhraní a adresář

JMS pomocí rozhraní JNDI (naming and Directory Interface) Java vytvoří oddělení mezi logickými názvy a fyzickými názvy. Dva typy objektů JMS jsou vyřešeny pomocí JNDI: **ConnectionFactory** a **Destination**. JNDI používá model poskytovatele, ve kterém můžete připojit různé adresářové služby ke zpracování povinností překladu názvů. Knihovna Apache Qpid JMS AMQP 1,0 obsahuje jednoduchý poskytovatel JNDI založený na souborech vlastností, který je nakonfigurován pomocí souboru vlastností v následujícím formátu:

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

#### <a name="set-up-jndi-context-and-configure-the-connectionfactory-object"></a>Nastavení kontextu JNDI a konfigurace objektu ConnectionFactory

Odkaz na připojovací řetězec je ten, který je dostupný v zásadách sdíleného přístupu v [Azure Portal](https://portal.azure.com) v části **primární připojovací řetězec**.

```java
// The connection string builder is the only part of the azure-servicebus SDK library
// we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
// connection string. 
ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
// Set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");

// Look up queue
Destination queue = (Destination) context.lookup("QUEUE");
```

#### <a name="configure-producer-and-consumer-destination-queues"></a>Konfigurovat fronty výrobců a cílových uživatelů

Položka, která se používá k definování cíle v Qpid vlastností souboru JNDI, má následující formát.

Vytvoření cílové fronty pro producenta:
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create producer
MessageProducer producer = session.createProducer(queue);
```

Vytvoření cílové fronty pro příjemce:
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create consumer
MessageConsumer consumer = session.createConsumer(queue);
```

### <a name="write-the-jms-application"></a>Zápis aplikace JMS

Při použití JMS s Service Bus nejsou potřeba žádná speciální rozhraní API ani možnosti. Existuje několik omezení, která se týkají později. Stejně jako u jakékoli aplikace JMS je nutné nejprve nakonfigurovat prostředí JNDI, aby bylo možné přeložit objekt **ConnectionFactory** a cíle.

#### <a name="configure-the-jndi-initialcontext-object"></a>Konfigurace objektu JNDI InitialContext

Prostředí JNDI je konfigurováno předáním zatřiďovací tabulky informací o konfiguraci do konstruktoru třídy javax.naming.InitialContext. Dva vyžadované prvky v zatřiďovací tabulce jsou název třídy počátečního kontextu objektu pro vytváření kontextu a adresy URL poskytovatele. Následující kód ukazuje, jak nakonfigurovat prostředí JNDI tak, aby používalo Qpid vlastnosti JNDIho poskytovatele se souborem vlastností s názvem **ServiceBus. Properties**.

```java
// Set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + \
"?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);
``` 

### <a name="a-simple-jms-application-that-uses-a-service-bus-queue"></a>Jednoduchá aplikace JMS, která používá frontu Service Bus

Následující ukázkový program odesílá JMS textové zprávy do fronty Service Bus s logickým názvem JNDI fronty a přijímá zprávy zpět.

Ke všem informacím o zdrojovém kódu a konfiguraci můžete získat přístup Azure Service Bus z úvodní služby [JMS Samples Queue Starting](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart).

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
 * This sample demonstrates how to send messages from a JMS queue producer into
 * an Azure Service Bus queue and receive them with a JMS message consumer.
 * JMS queue. 
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
        
        // Set up JNDI context
        Hashtable<String, String> hashtable = new Hashtable<>();
        hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
        hashtable.put("queue.QUEUE", "BasicQueue");
        hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
        Context context = new InitialContext(hashtable);
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        
        // Look up queue
        Destination queue = (Destination) context.lookup("QUEUE");

        // We create a scope here so we can use the same set of local variables cleanly 
        // again to show the receive side separately with minimal clutter.
        {
            // Create connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            // Create session, no transaction, client ack
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
            // Create connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            connection.start();
            // Create session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            // Create consumer
            MessageConsumer consumer = session.createConsumer(queue);
            // Create a listener callback to receive the messages
            consumer.setMessageListener(message -> {
                try {
                    // Received message is passed to callback
                    System.out.printf("Received message %d with sq#: %s\n",
                            totalReceived.incrementAndGet(), // increments the tracking counter
                            message.getJMSMessageID());
                    message.acknowledge();
                } catch (Exception e) {
                    logger.error(e);
                }
            });

            // Wait on the main thread until all sent messages have been received
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

            // Parse connection string from command line
            Options options = new Options();
            options.addOption(new Option("c", true, "Connection string"));
            CommandLineParser clp = new DefaultParser();
            CommandLine cl = clp.parse(options, args);
            if (cl.getOptionValue("c") != null) {
                connectionString = cl.getOptionValue("c");
            }

            // Get overrides from the environment
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

Pokud chcete aplikaci spustit, předejte **připojovací řetězec** ze zásad sdíleného přístupu.
Následující výstup má formu spuštění aplikace:

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

## <a name="amqp-disposition-and-service-bus-operation-mapping"></a>Mapování AMQP a operace Service Bus

Tady je postup, jak AMQP dispozice v rámci operace Service Bus:

```Output
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```

## <a name="jms-topics-vs-service-bus-topics"></a>Témata JMS a témata týkající se Service Bus

Použití Service Bus témat a předplatných prostřednictvím rozhraní JMS API poskytuje základní funkce pro odesílání a příjem. To je vhodná volba při portování aplikací od jiných zprostředkovatelů zpráv s rozhraními API kompatibilních s JMS, i když Service Bus témata se liší od témat JMS a vyžadují několik úprav.

Service Bus témata směrují zprávy do pojmenovaných, sdílených a trvalých předplatných spravovaných prostřednictvím rozhraní pro správu prostředků Azure, nástrojů příkazového řádku Azure nebo Azure Portal. Každé předplatné umožňuje až 2 000 pravidel výběru, z nichž každá může mít podmínku filtru a pro filtry SQL i akci transformace metadat. Každá Podmínka filtru se shoduje s výběrem vstupní zprávy, která se má zkopírovat do předplatného.  

Příjem zpráv z předplatných je stejný jako příjem zpráv z front. Každé předplatné má přidruženou frontu nedoručených zpráv a možnost automatického přeposílání zpráv do jiné fronty nebo témat.

Témata JMS umožňují klientům dynamicky vytvářet netrvalé a trvalé předplatitele, které volitelně umožňují filtrovat zprávy pomocí selektorů zpráv. Tyto nesdílené entity nejsou podporované nástrojem Service Bus. Syntaxe pravidla filtru SQL pro Service Bus je podobná syntaxi selektoru zpráv, kterou podporuje JMS.

Strana vydavatele tématu JMS je kompatibilní s Service Bus, jak je znázorněno v této ukázce, ale dynamická předplatitelé ne. Následující rozhraní JMS API související s topologií nejsou u Service Bus podporována.

## <a name="unsupported-features-and-restrictions"></a>Nepodporované funkce a omezení

Následující omezení existují, když používáte JMS over AMQP 1,0 s Service Bus, konkrétně:

* V jedné relaci je povolen pouze jeden objekt **MessageProducer** nebo **MessageConsumer** . Pokud potřebujete vytvořit v aplikaci více objektů **MessageProducer** nebo **MessageConsumer** , vytvořte pro každou z nich vyhrazenou relaci.
* Nestálá předplatná témat nejsou v současné době podporována.
* Objekty **MessageSelector** se v tuto chvíli nepodporují.
* Distribuované transakce nejsou podporované, ale podporují se transakční relace.

Service Bus rozdělí plochu ovládacího prvku z roviny dat, takže nepodporuje několik funkcí dynamické topologie JMS.

| Nepodporovaná metoda          | Nahradit hodnotou                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| createDurableSubscriber     | Vytvořte předplatné tématu, které vystaví selektor zpráv.                                |
| createDurableConsumer       | Vytvořte předplatné tématu, které vystaví selektor zpráv.                                |
| createSharedConsumer        | Service Bus témata jsou vždycky sdílená. Přečtěte si část "témata JMS a témata Service Bus."                                    |
| createSharedDurableConsumer | Service Bus témata jsou vždycky sdílená. Přečtěte si část "témata JMS a témata Service Bus."                                      |
| createTemporaryTopic        | Vytvořte téma prostřednictvím rozhraní API pro správu, nástrojů nebo portálu s *AutoDeleteOnIdle* nastaveným na období vypršení platnosti. |
| createTopic                 | Vytvořte téma prostřednictvím rozhraní API pro správu, nástrojů nebo portálu.                                         |
| Odhlásit odběr                 | Odstraňte rozhraní API pro správu tématu, nástroje nebo portál.                                            |
| createBrowser               | Neplatné. Použijte funkci Náhled () rozhraní API pro Service Bus.                         |
| createQueue                 | Vytvořte frontu pomocí rozhraní API pro správu, nástrojů nebo portálu.                                           | 
| createTemporaryQueue        | Vytvořte frontu pomocí rozhraní API pro správu, nástrojů nebo portálu s *AutoDeleteOnIdle* nastavenou na období vypršení platnosti. |
| receiveNoWait               | Použijte metodu Receive () poskytovanou sadou Service Bus SDK a zadejte velmi nízký nebo nulový časový limit. |

## <a name="summary"></a>Souhrn

Tento článek vám ukázal, jak Service Bus pomocí oblíbených JMS rozhraní API a AMQP 1,0 používat funkce zprostředkovaných zpráv, jako jsou fronty a témata pro publikování nebo odběry v jazyce Java.

Service Bus AMQP 1,0 můžete také použít z jiných jazyků, jako jsou .NET, C, Python nebo PHP. Komponenty sestavené pomocí těchto různých jazyků můžou spolehlivě vyměňovat zprávy a s plnou věrností pomocí podpory AMQP 1,0 v Service Bus.

## <a name="next-steps"></a>Další kroky

* [Podpora AMQP 1,0 v Azure Service Bus](service-bus-amqp-overview.md)
* [Použití AMQP 1,0 s rozhraním API Service Bus .NET](./service-bus-amqp-dotnet.md)
* [Příručka pro vývojáře Service Bus AMQP 1,0](service-bus-amqp-dotnet.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Středisko pro vývojáře Java](https://azure.microsoft.com/develop/java/)