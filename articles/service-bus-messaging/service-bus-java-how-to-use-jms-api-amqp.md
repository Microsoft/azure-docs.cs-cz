---
title: Použití AMQP s rozhraním API služby Java Message Service & Azure Service Bus
description: Jak používat službu JMS (Java Message Service) s Azure Service Bus a rozšířený protokol řízení front zpráv (AMQP) (AMQP) 1,0.
ms.topic: article
ms.date: 06/23/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: be6b5de7946fc54ab58087fccabed9f5ed09251d
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/11/2020
ms.locfileid: "88065773"
---
# <a name="use-the-java-message-service-jms-with-azure-service-bus-and-amqp-10"></a>Použití JMS (Java Message Service) s Azure Service Bus a AMQP 1,0

> [!WARNING]
> Níže uvedená příručka se **týká omezené podpory** rozhraní JMS (Java Message Service) 1,1 API a existuje jenom pro Azure Service Bus jenom na úrovni Standard.
>
> Plná podpora pro rozhraní JMS (Java Message Service) 2,0 API je dostupná jenom Azure Service Bus na [úrovni Premium ve verzi Preview](how-to-use-java-message-service-20.md), která se důrazně doporučuje.
>

V tomto článku se dozvíte, jak používat funkce Azure Service Bus zasílání zpráv (fronty a témata pro publikování a odběr) z aplikací Java pomocí oblíbeného standardu rozhraní API pro JMS (Java Message Service). K dispozici je [doprovodný článek](service-bus-amqp-dotnet.md) , který vysvětluje, jak to samé provést pomocí rozhraní API Azure Service Bus .NET. Tyto dvě příručky můžete použít společně a získat další informace o zasílání zpráv mezi různými platformami pomocí AMQP 1,0.

Rozšířený protokol řízení front zpráv (AMQP) (AMQP) 1,0 je efektivní a spolehlivý přenosový protokol zasílání zpráv, který můžete použít k vytváření robustních aplikací zasílání zpráv pro různé platformy.

Podpora AMQP 1,0 v Azure Service Bus znamená, že můžete používat funkce řazení a publikování zprostředkovaných zpráv z různých platforem pomocí efektivního binárního protokolu. Kromě toho můžete sestavovat aplikace tvořené komponentami sestavenými pomocí kombinace jazyků, platforem a operačních systémů.

## <a name="get-started-with-service-bus"></a>Začínáme se službou Service Bus
V tomto průvodci se předpokládá, že už máte obor názvů Service Bus obsahující frontu s názvem `basicqueue` . Pokud to neuděláte, můžete [vytvořit obor názvů a frontu](service-bus-create-namespace-portal.md) pomocí [Azure Portal](https://portal.azure.com). Další informace o tom, jak vytvořit Service Bus obory názvů a fronty, najdete v tématu Začínáme [s Service Bus fronty](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Dělené fronty a témata také podporují AMQP. Další informace najdete v tématech [rozdělené entity zasílání zpráv](service-bus-partitioning.md) a [Podpora AMQP 1,0 pro Service Bus dělené fronty a témata](./service-bus-amqp-protocol-guide.md).
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>Stažení klientské knihovny AMQP 1,0 JMS
Informace o tom, kde stáhnout nejnovější verzi klientské knihovny Apache Qpid JMS AMQP 1,0, najdete v části [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html) .

Při sestavování a spouštění aplikací JMS pomocí Service Bus musíte do cesty k cestě Java přidat následující soubory JAR z distribučního archivu Apache Qpid JMS AMQP 1,0:

* Geronimo-JMS \_ 1,1 \_ spec-1.0. jar
* qpid-JMS-Client-[verze]. jar

> [!NOTE]
> Názvy a verze JAR JMS se možná změnily. Podrobnosti najdete v tématu [QPID JMS-AMQP 1,0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).

## <a name="coding-java-applications"></a>Kódování aplikací Java
### <a name="java-naming-and-directory-interface-jndi"></a>Názvy rozhraní a adresáře Java (JNDI)
JMS pomocí rozhraní JNDI (naming and Directory Interface) Java vytvoří oddělení mezi logickými názvy a fyzickými názvy. Dva typy objektů JMS jsou vyřešeny pomocí JNDI: ConnectionFactory a Destination. JNDI používá model poskytovatele, ve kterém můžete připojit různé adresářové služby ke zpracování povinností překladu názvů. Knihovna Apache Qpid JMS AMQP 1,0 obsahuje jednoduchý poskytovatel JNDI založený na souboru s vlastnostmi, který je nakonfigurovaný pomocí souboru vlastností v následujícím formátu:

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

#### <a name="setup-jndi-context-and-configure-the-connectionfactory"></a>Nastavení kontextu JNDI a konfigurace ConnectionFactory

**ConnectionString** , na který se odkazuje v seznamu, který je k dispozici v části zásady sdíleného přístupu v [Azure Portal](https://portal.azure.com) pod **primárním připojovacím řetězcem**
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

#### <a name="configure-producer-and-consumer-destination-queues"></a>Konfigurovat fronty výrobců a cílových uživatelů
Položka použitá k definování cíle v Qpid vlastností souboru JNDI má následující formát:

Vytvoření cílové fronty pro producenta – 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Producer
MessageProducer producer = session.createProducer(queue);
```

Vytvoření cílové fronty pro příjemce – 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Consumer
MessageConsumer consumer = session.createConsumer(queue);
```

### <a name="write-the-jms-application"></a>Zápis aplikace JMS
Při použití JMS s Service Bus se nevyžadují žádná speciální rozhraní API ani možnosti. Existuje však několik omezení, která budou zahrnuta později. Stejně jako u libovolné aplikace JMS je první požadovaná věc konfigurací prostředí JNDI, aby bylo možné přeložit **ConnectionFactory** a cíle.

#### <a name="configure-the-jndi-initialcontext"></a>Konfigurace JNDI InitialContext
Prostředí JNDI je konfigurováno předáním zatřiďovací tabulky informací o konfiguraci do konstruktoru třídy javax.naming.InitialContext. Dva vyžadované elementy v zatřiďovací tabulce jsou název třídy počátečního kontextu objektu pro vytváření kontextu a adresy URL poskytovatele. Následující kód ukazuje, jak nakonfigurovat prostředí JNDI tak, aby používalo Qpidho poskytovatele JNDI s vlastnostmi souboru s názvem **ServiceBus. Properties**.

```java
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + \
"?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Jednoduchá aplikace JMS s využitím fronty Service Bus
Následující ukázkový program odešle JMS TextMessages do fronty Service Bus s logickým názvem JNDI fronty a znovu obdrží zprávy.

Všechny informace o zdrojovém kódu a konfiguraci můžete získat z úvodního [rychlého startu z Azure Service Bus JMS Samples](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart) .

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
Pokud chcete aplikaci spustit, předejte **připojovací řetězec** ze zásad sdíleného přístupu.
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

## <a name="amqp-disposition-and-service-bus-operation-mapping"></a>Mapování AMQP a operace Service Bus
Tady je způsob, jakým je AMQP dispozice pro operaci Service Bus:

```Output
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```

## <a name="jms-topics-vs-service-bus-topics"></a>Témata JMS a témata týkající se Service Bus
Použití Azure Service Bus témata a odběry prostřednictvím rozhraní API služby Java Message Service (JMS) poskytuje základní funkce pro odesílání a příjem. To je vhodná volba při přenosu aplikací od jiných zprostředkovatelů zpráv s rozhraními API kompatibilních s JMS, i když Service Bus témata se liší od témat JMS a vyžadují několik úprav. 

Azure Service Bus témata směrují zprávy do pojmenovaných, sdílených a trvalých předplatných spravovaných prostřednictvím rozhraní pro správu prostředků Azure, nástrojů příkazového řádku Azure nebo prostřednictvím Azure Portal. Každé předplatné umožňuje až 2000 pravidel výběru, z nichž každá může mít podmínku filtru a pro filtry SQL i akci transformace metadat. Každá Podmínka filtru se shoduje s výběrem vstupní zprávy, která se má zkopírovat do předplatného.  

Příjem zpráv z předplatných se shoduje s přijímáním zpráv z front. Každé předplatné má přidruženou frontu nedoručených zpráv a možnost automatického přeposílání zpráv do jiné fronty nebo témat. 

Témata JMS umožňují klientům dynamicky vytvářet netrvalé a trvalé předplatitele, které volitelně umožňují filtrovat zprávy pomocí selektorů zpráv. Tyto nesdílené entity nejsou podporované nástrojem Service Bus. Syntaxe pravidla filtru SQL pro Service Bus je však podobná syntaxi selektoru zpráv, kterou podporuje JMS. 

Strana vydavatele tématu JMS je kompatibilní s Service Bus, jak je znázorněno v této ukázce, ale dynamická předplatitelé ne. Následující rozhraní JMS API související s topologií nejsou u Service Bus podporována. 

## <a name="unsupported-features-and-restrictions"></a>Nepodporované funkce a omezení
Následující omezení existují při použití JMS over AMQP 1,0 s Service Bus, konkrétně:

* V jedné **relaci**je povolena pouze jedna **MessageProducer** nebo **MessageConsumer** . Pokud potřebujete v aplikaci vytvořit více **MessageProducers** nebo **MessageConsumers** , vytvořte pro každou z nich vyhrazenou **relaci** .
* Nestálá předplatná témat nejsou v současné době podporována.
* **MessageSelectors** se v tuto chvíli nepodporuje.
* Distribuované transakce nejsou podporované (ale podporují se transakční relace).

Navíc Azure Service Bus rozdělí rovinu ovládacího prvku z roviny dat, a proto nepodporuje několik funkcí dynamické topologie JMS:

| Nepodporovaná metoda          | Nahradit hodnotou                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| createDurableSubscriber     | vytvoření předplatného tématu, které připravuje výběr zprávy                                 |
| createDurableConsumer       | vytvoření předplatného tématu, které připravuje výběr zprávy                                 |
| createSharedConsumer        | Témata Service Bus jsou vždycky sdílená, viz výše.                                       |
| createSharedDurableConsumer | Témata Service Bus jsou vždycky sdílená, viz výše.                                       |
| createTemporaryTopic        | vytvoření tématu prostřednictvím rozhraní API pro správu/nástrojů/portálu s *AutoDeleteOnIdle* nastavenou na období vypršení platnosti |
| createTopic                 | vytvoření tématu prostřednictvím rozhraní API pro správu/nástrojů/portálu                                           |
| Odhlásit odběr                 | Odstranění rozhraní API/nástrojů/portálu pro správu tématu                                             |
| createBrowser               | neplatné. Použití funkce prohlížet () rozhraní API pro Service Bus                         |
| createQueue                 | vytvoření fronty prostřednictvím rozhraní API pro správu/nástrojů/portálu                                           | 
| createTemporaryQueue        | vytvoření fronty prostřednictvím rozhraní API pro správu/nástrojů/portálu s *AutoDeleteOnIdle* nastavenou na období vypršení platnosti |
| receiveNoWait               | Použijte metodu Receive () poskytovanou sadou Service Bus SDK a zadejte velmi nízký nebo nulový časový limit. |

## <a name="summary"></a>Souhrn
Tato příručka ukázala, jak používat funkce Service Bus zprostředkovaných zpráv (fronty a témata pro publikování a odběr) z Java pomocí oblíbených rozhraní API pro JMS a AMQP 1,0.

Service Bus AMQP 1,0 můžete také použít z jiných jazyků, včetně .NET, C, Pythonu a PHP. Komponenty sestavené pomocí těchto různých jazyků můžou spolehlivě vyměňovat zprávy a s plnou věrností pomocí podpory AMQP 1,0 v Service Bus.

## <a name="next-steps"></a>Další kroky
* [Podpora AMQP 1,0 v Azure Service Bus](service-bus-amqp-overview.md)
* [Jak používat AMQP 1,0 s rozhraním API Service Bus .NET](./service-bus-amqp-dotnet.md)
* [Příručka pro vývojáře Service Bus AMQP 1,0](service-bus-amqp-dotnet.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Středisko pro vývojáře Java](https://azure.microsoft.com/develop/java/)