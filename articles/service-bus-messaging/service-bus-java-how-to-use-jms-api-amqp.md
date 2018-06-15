---
title: Použití protokolu AMQP 1.0 se rozhraní API Java Service Bus | Microsoft Docs
description: Jak používat službu zpráva Java (JMS) s Azure Service Bus a rozšířené zpráv služby Řízení front Protodol (AMQP) 1.0.
services: service-bus-messaging
documentationcenter: java
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: be766f42-6fd1-410c-b275-8c400c811519
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: 0848facd764c4fb0d7f95c1ae89ecb02a32257e1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23868379"
---
# <a name="how-to-use-the-java-message-service-jms-api-with-service-bus-and-amqp-10"></a>Jak používat Java zprávy služby (JMS) rozhraní API se Service Bus a protokolu AMQP 1.0
Rozšířené zpráv služby Řízení front Protocol (AMQP) 1.0 je efektivní, spolehlivou a úroveň zasílání zpráv protokol, který můžete použít k vytvoření robustní, multiplatformní aplikace zasílání zpráv.

Podpora pro protokolu AMQP 1.0 v Service Bus znamená, že můžete použít, řazení do fronty a publikování a přihlášení k odběru zprostředkované zasílání zpráv funkcí z rozsahu platforem a efektivní binární protokol. Kromě toho můžete vytvořit aplikace skládá z komponenty sestaven pomocí kombinace jazyky, architektury a operační systémy.

Tento článek vysvětluje, jak k použití služby Service Bus (fronty a témata publikování a přihlášení k odběru) funkce pro zasílání zpráv z aplikací v jazyce Java pomocí oblíbených Java zprávy služby (JMS) standardní rozhraní API. Je [doprovodný článek](service-bus-amqp-dotnet.md) to vysvětluje, jak to provést pomocí rozhraní API služby Service Bus .NET stejné. Tyto dvě příručky můžete použít společně Další informace o zasílání zpráv mezi platformami pomocí protokolu AMQP 1.0.

## <a name="get-started-with-service-bus"></a>Začínáme se službou Service Bus
Tato příručka předpokládá, že už máte obor názvů sběrnice obsahující frontu s názvem **Frontě1**. Pokud ho použít nechcete, pak můžete [vytvořit obor názvů a fronty](service-bus-create-namespace-portal.md) pomocí [portál Azure](https://portal.azure.com). Další informace o tom, jak vytvořit obory názvů Service Bus a fronty najdete v tématu [začít pracovat s fronty Service Bus](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Oddílů fronty a témata také podporují AMQP. Další informace najdete v tématu [segmentované entity zasílání zpráv](service-bus-partitioning.md) a [podporu protokolu AMQP 1.0 pro Service Bus oddíly fronty a témata](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>Stahuje se knihovna klienta protokolu AMQP 1.0 JMS
Informace o tom, kde chcete stáhnout nejnovější verzi klientské knihovny Apache Qpid JMS protokolu AMQP 1.0, najdete v článku [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html).

Následující čtyři JAR soubory z archivu distribuční Apache Qpid JMS protokolu AMQP 1.0 je nutné přidat na cestě třídy Java při vytváření a spouštění aplikací JMS službou Service Bus:

* geronimo jms\_1.1\_specifikace 1.0.jar
* qpid-amqp-1-0-Client-[Version].JAR
* qpid-amqp-1-0-Client-jms-[Version].JAR
* qpid-amqp-1-0-Common-[Version].JAR

## <a name="coding-java-applications"></a>Kódování aplikací v jazyce Java
### <a name="java-naming-and-directory-interface-jndi"></a>Pojmenování Java a Directory rozhraní (JNDI)
JMS používá k vytvoření oddělení mezi názvy logické a fyzické pojmenovávání Java a Directory rozhraní (JNDI). Dva typy objektů JMS se přeloží pomocí JNDI: ConnectionFactory a cíl. JNDI používá zprostředkovatele modelu, do kterého je možné připojit jiný adresář služby pro zpracování název řešení povinností. Apache Qpid JMS protokolu AMQP 1.0 knihovny se dodává s jednoduché vlastnosti formátování na základě souborů JNDI zprostředkovatele, který je nakonfigurovaný pomocí vlastnosti souboru z následujících:

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

#### <a name="configure-the-connectionfactory"></a>Konfigurace ConnectionFactory
Záznam používá k definování **ConnectionFactory** v souboru vlastnosti Qpid JNDI zprostředkovatele je v následujícím formátu:

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

Kde **[jndi_name]** a **[ConnectionURL]** mají následující významy:

* **[jndi_name]** : Logický název ConnectionFactory. Toto je název, který se vyřeší v aplikaci Java pomocí metody JNDI IntialContext.lookup().
* **[ConnectionURL]** : Adresu URL, která poskytuje knihovnu JMS informace požadované pro zprostředkovatele protokolu AMQP.

Formát **ConnectionURL** vypadá takto:

```
amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
```
Kde **[obor názvů]**, **[SASPolicyName]** a **[SASPolicyKey]** mají následující významy:

* **[obor názvů]** : Obor názvů sběrnice služby.
* **[SASPolicyName]** : Název zásady fronty sdíleného přístupového podpisu.
* **[SASPolicyKey]** : Klíč zásad fronty sdíleného přístupového podpisu.

> [!NOTE]
> Je nutné kódování URL heslo ručně. Užitečné kódování URL nástroj je k dispozici na [http://www.w3schools.com/tags/ref_urlencode.asp](http://www.w3schools.com/tags/ref_urlencode.asp).
> 
> 

#### <a name="configure-destinations"></a>Konfigurace cíle
Položka používá k definování cíl ve zprostředkovateli Qpid soubor vlastnosti JNDI má následující formát:

```
queue.[jndi_name] = [physical_name]
```

nebo

```
topic.[jndi_name] = [physical_name]
```

Kde **[jndi\_název]** a **[fyzické\_název]** mají následující významy:

* **[jndi_name]** : Logický název cílové. Toto je název, který se vyřeší v aplikaci Java pomocí metody JNDI IntialContext.lookup().
* **[physical_name]** : Název entity služby sběrnice, ke kterému aplikace odeslání nebo přijetí zprávy.

> [!NOTE]
> Při přijímání od odběr tématu Service Bus, fyzický název zadaný v JNDI musí být název tématu. Při vytvoření odolné odběru v kódu aplikace JMS, je zadán název odběru. [Service Bus AMQP 1.0 – Příručka vývojáře](service-bus-amqp-dotnet.md) obsahuje další informace o práci s témat sběrnice Service Bus z JMS.
> 
> 

### <a name="write-the-jms-application"></a>Zapište aplikaci JMS
Neexistují žádné speciální rozhraní API nebo možnosti, které jsou potřeba při použití JMS službou Service Bus. Existují však několik omezení, kterým se budeme později. Stejně jako u jakékoli aplikace JMS nejprve thing požadované je konfigurace JNDI prostředí, abyste mohli vyřešit **ConnectionFactory** a cíle.

#### <a name="configure-the-jndi-initialcontext"></a>Konfigurace JNDI InitialContext
JNDI prostředí je nakonfigurováno pomocí předání do konstruktoru třídy javax.naming.InitialContext zatřiďovací tabulku informací o konfiguraci. Dva požadované prvky v zatřiďovací tabulce jsou název třídy počáteční vytváření kontextu a adresu poskytovatele. Následující kód ukazuje, jak nakonfigurovat prostředí JNDI používat Qpid soubor vlastnosti zprostředkovatele na bázi JNDI s vlastnosti souboru s názvem **servicebus.properties**.

```java
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
InitialContext context = new InitialContext(env);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Jednoduché JMS aplikace pomocí fronty Service Bus
V následujícím příkladu programu odešle JMS TextMessages do fronty Service Bus s JNDI logický název fronty a přijímá zprávy zpět.

```java
// SimpleSenderReceiver.java

import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.Hashtable;
import java.util.Random;

public class SimpleSenderReceiver implements MessageListener {
    private static boolean runReceiver = true;
    private Connection connection;
    private Session sendSession;
    private Session receiveSession;
    private MessageProducer sender;
    private MessageConsumer receiver;
    private static Random randomGenerator = new Random();

    public SimpleSenderReceiver() throws Exception {
        // Configure JNDI environment
        Hashtable<String, String> env = new Hashtable<String, String>();
        env.put(Context.INITIAL_CONTEXT_FACTORY, 
                   "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
        env.put(Context.PROVIDER_URL, "servicebus.properties");
        Context context = new InitialContext(env);

        // Look up ConnectionFactory and Queue
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        Destination queue = (Destination) context.lookup("QUEUE");

        // Create Connection
        connection = cf.createConnection();

        // Create sender-side Session and MessageProducer
        sendSession = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
        sender = sendSession.createProducer(queue);

        if (runReceiver) {
            // Create receiver-side Session, MessageConsumer,and MessageListener
            receiveSession = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            receiver = receiveSession.createConsumer(queue);
            receiver.setMessageListener(this);
            connection.start();
        }
    }

    public static void main(String[] args) {
        try {

            if ((args.length > 0) && args[0].equalsIgnoreCase("sendonly")) {
                runReceiver = false;
            }

            SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();
            System.out.println("Press [enter] to send a message. Type 'exit' + [enter] to quit.");
            BufferedReader commandLine = new java.io.BufferedReader(new InputStreamReader(System.in));

            while (true) {
                String s = commandLine.readLine();
                if (s.equalsIgnoreCase("exit")) {
                    simpleSenderReceiver.close();
                    System.exit(0);
                } else {
                    simpleSenderReceiver.sendMessage();
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    private void sendMessage() throws JMSException {
        TextMessage message = sendSession.createTextMessage();
        message.setText("Test AMQP message from JMS");
        long randomMessageID = randomGenerator.nextLong() >>>1;
        message.setJMSMessageID("ID:" + randomMessageID);
        sender.send(message);
        System.out.println("Sent message with JMSMessageID = " + message.getJMSMessageID());
    }

    public void close() throws JMSException {
        connection.close();
    }

    public void onMessage(Message message) {
        try {
            System.out.println("Received message with JMSMessageID = " + message.getJMSMessageID());
            message.acknowledge();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}    
```

### <a name="run-the-application"></a>Spuštění aplikace
Spuštění aplikace vytváří výstup ve formátu:

```
> java SimpleSenderReceiver
Press [enter] to send a message. Type 'exit' + [enter] to quit.

Sent message with JMSMessageID = ID:2867600614942270318
Received message with JMSMessageID = ID:2867600614942270318

Sent message with JMSMessageID = ID:7578408152750301483
Received message with JMSMessageID = ID:7578408152750301483

Sent message with JMSMessageID = ID:956102171969368961
Received message with JMSMessageID = ID:956102171969368961
exit
```

## <a name="cross-platform-messaging-between-jms-and-net"></a>Napříč platformami zasílání zpráv mezi JMS a rozhraní .NET
Tento průvodce vám ukázal, jak odesílat a přijímat zprávy do a ze služby Service Bus pomocí JMS. Mezi klíčové výhody protokolu AMQP 1.0 je však umožňuje aplikace má být sestaven z komponent, které jsou napsané v různých jazycích, pomocí zprávy vyměňují spolehlivé a na úplné přesnost.

Pomocí ukázkovou aplikaci JMS popsané výše a podobné aplikace .NET prováděné z článku doprovodné [pomocí Service Bus pomocí technologie .NET pomocí protokolu AMQP 1.0](service-bus-amqp-dotnet.md), můžou vyměňovat zprávy mezi .NET a Javu. Přečtěte si tento článek Další informace o podrobnosti o platformě zasílání zpráv pomocí sběrnice a protokolu AMQP 1.0.

### <a name="jms-to-net"></a>JMS na rozhraní .NET
K předvedení JMS .NET zasílání zpráv:

* Spuštění ukázkové aplikace .NET bez argumentů příkazového řádku.
* Spuštění ukázkové aplikace Java s argumentem příkazového řádku "sendonly". V tomto režimu, aplikace nebude přijímat zprávy z fronty, kterou bude pouze odesílat.
* Stiskněte klávesu **Enter** několikrát v konzole aplikace Java, což způsobí, že odesílání zpráv.
* Tyto zprávy jsou přijímány aplikace .NET.

#### <a name="output-from-jms-application"></a>Výstup z aplikace JMS
```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

#### <a name="output-from-net-application"></a>Výstup z aplikace .NET
```
> SimpleSenderReceiver.exe    
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

### <a name="net-to-jms"></a>.NET na JMS
K předvedení .NET JMS zasílání zpráv:

* Spuštění ukázkové aplikace .NET s argumentem příkazového řádku "sendonly". V tomto režimu, aplikace nebude přijímat zprávy z fronty, kterou bude pouze odesílat.
* Spuštění ukázkové aplikace Java bez argumentů příkazového řádku.
* Stiskněte klávesu **Enter** několikrát v konzole aplikace .NET, což způsobí, že odesílání zpráv.
* Tyto zprávy jsou přijímány aplikace Java.

#### <a name="output-from-net-application"></a>Výstup z aplikace .NET
```
> SimpleSenderReceiver.exe sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with MessageID = d64e681a310a48a1ae0ce7b017bf1cf3    
Sent message with MessageID = 98a39664995b4f74b32e2a0ecccc46bb
Sent message with MessageID = acbca67f03c346de9b7893026f97ddeb
exit
```

#### <a name="output-from-jms-application"></a>Výstup z aplikace JMS
```
> java SimpleSenderReceiver    
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with JMSMessageID = ID:d64e681a310a48a1ae0ce7b017bf1cf3
Received message with JMSMessageID = ID:98a39664995b4f74b32e2a0ecccc46bb
Received message with JMSMessageID = ID:acbca67f03c346de9b7893026f97ddeb
exit
```

## <a name="unsupported-features-and-restrictions"></a>Nepodporované funkce a omezení
Při použití JMS prostřednictvím protokolu AMQP 1.0 se Service Bus, konkrétně, existují následující omezení:

* Pouze jeden **MessageProducer** nebo **MessageConsumer** je povolena na **relace**. Pokud je potřeba vytvořit několik **MessageProducers** nebo **MessageConsumers** v aplikaci, vytvořte vyhrazená **relace** pro každý z nich.
* Odběry témat volatile nejsou aktuálně podporovány.
* **MessageSelectors** nejsou aktuálně podporovány.
* Dočasné cíle; například **TemporaryQueue**, **TemporaryTopic** nejsou aktuálně podporovány, spolu s **QueueRequestor** a **TopicRequestor** rozhraní API, která je používat.
* Zpracované relací a distribuované transakce nejsou podporovány.

## <a name="summary"></a>Souhrn
Tento postup Průvodce vám ukázal, jak používat funkce Service Bus zprostředkované zasílání zpráv (fronty a témata publikování a přihlášení k odběru) z prostředí Java pomocí Oblíbené JMS rozhraní API a protokolu AMQP 1.0.

Můžete taky Service Bus protokolu AMQP 1.0 z dalších jazycích včetně .NET, C, Python nebo PHP. Součásti vytvořená s využitím těchto různé jazyky můžete výměna zpráv spolehlivě a na úplné věrnosti pomocí protokolu AMQP 1.0 podporovat v Service Bus.

## <a name="next-steps"></a>Další kroky
* [Podpora protokolu AMQP 1.0 v Azure Service Bus](service-bus-amqp-overview.md)
* [Použití protokolu AMQP 1.0 se rozhraní API služby Service Bus rozhraní .NET](service-bus-dotnet-advanced-message-queuing.md)
* [Service Bus AMQP 1.0 – Příručka vývojáře](service-bus-amqp-dotnet.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Středisko pro vývojáře Java](https://azure.microsoft.com/develop/java/)

