---
title: Jak pomocí protokolu AMQP 1.0 rozhraní Java API služby Service Bus | Dokumentace Microsoftu
description: Jak používat službu zpráva Java (JMS) s Azure Service Bus a rozšířené zprávy služby Řízení front Protodol protokol AMQP 1.0.
services: service-bus-messaging
documentationcenter: java
author: spelluru
manager: timlt
editor: ''
ms.assetid: be766f42-6fd1-410c-b275-8c400c811519
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 08/10/2018
ms.author: spelluru
ms.openlocfilehash: a3274053e772cbdf120be15a385c84d5ae37d610
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47392648"
---
# <a name="how-to-use-the-java-message-service-jms-api-with-service-bus-and-amqp-10"></a>Jak Java Message Service (JMS) rozhraní API pomocí služby Service Bus a protokolu AMQP 1.0
Rozšířené řízení front zpráv protokolu (AMQP) 1.0 je efektivní a spolehlivý přenosový protokol zasílání zpráv, který vám pomůže vytvářet aplikace napříč platformami, robustní zasílání zpráv.

Podpora pro protokolu AMQP 1.0 ve službě Service Bus znamená, že můžete použít řazení do fronty a publikování/přihlášení k odběru funkce zprostředkovaného zasílání zpráv z celou řadu platforem s využitím efektivní binární protokol. Kromě toho můžete vytvářet aplikace skládá z komponenty sestavené pomocí kombinace jazyků, architektur a operační systémy.

Tento článek vysvětluje, jak pomocí služby Service Bus (fronty a témata publikace nebo odběru) funkce zasílání zpráv v aplikacích v Javě pomocí Oblíbené Java zprávy služby JMS () standardní rozhraní API. Je [doprovodný článek](service-bus-amqp-dotnet.md) , který vysvětluje, jak provést stejný pomocí rozhraní .NET API služby Service Bus. Tyto dvě příručky můžete použít společně se dozvíte o zasílání zpráv mezi platformami pomocí AMQP 1.0.

## <a name="get-started-with-service-bus"></a>Začínáme se službou Service Bus
Tento průvodce to předpokládá, že už máte obor názvů Service Bus, který obsahuje frontu s názvem **Frontě1**. Pokud ho nevidíte, pak můžete [vytvořit obor názvů a frontu](service-bus-create-namespace-portal.md) pomocí [webu Azure portal](https://portal.azure.com). Další informace o tom, jak vytvořit obory názvů služby Service Bus a fronty, naleznete v tématu [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Dělené fronty a témata podporují také AMQP. Další informace najdete v tématu [segmentované entity zasílání zpráv](service-bus-partitioning.md) a [podpory AMQP 1.0 pro službu Service Bus dělené fronty a témata](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>Stahuje se knihovna klienta protokolu AMQP 1.0 JMS
Informace o tom, kde chcete stáhnout nejnovější verzi klientské knihovny Apache Qpid JMS protokolu AMQP 1.0, najdete v článku [ https://qpid.apache.org/download.html ](https://qpid.apache.org/download.html).

Následující čtyři soubory JAR z archivní úrovně distribuce Apache Qpid JMS protokolu AMQP 1.0 je nutné přidat do cesta třídy Java při vytváření a spouštění aplikací JMS službou Service Bus:

* geronimo jms\_1.1\_1.0.jar specifikace
* qpid-amqp-1-0-Client-[Version].JAR
* qpid-amqp-1-0-Client-jms-[Version].JAR
* qpid-amqp-1-0-Common-[Version].JAR

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

#### <a name="configure-the-connectionfactory"></a>Konfigurace ConnectionFactory
Položka sloužících k definování **ConnectionFactory** v souboru vlastnosti Qpid JNDI zprostředkovatel je v následujícím formátu:

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

Kde **[jndi_name]** a **[ConnectionURL]** mají následující význam:

* **[jndi_name]** : Logický název ConnectionFactory. Toto je název, který bude vyřešen v aplikaci v Javě pomocí metody JNDI IntialContext.lookup().
* **[ConnectionURL]** : Adresu URL, která poskytuje knihovnu JMS pomocí informací požadovaných pro zprostředkovatele protokolu AMQP.

Formát **ConnectionURL** vypadá takto:

```
amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
```
Kde **[obor názvů]**, **[SASPolicyName]** a **[SASPolicyKey]** mají následující význam:

* **[obor názvů]** : Obor názvů sběrnice služby.
* **[SASPolicyName]** : Název zásad fronty sdílený přístupový podpis.
* **[SASPolicyKey]** : Klíče zásad the fronty sdílený přístupový podpis.

> [!NOTE]
> Je nutné kódování URL heslo ručně. Užitečné kódování URL nástroj, který je k dispozici na [ http://www.w3schools.com/tags/ref_urlencode.asp ](http://www.w3schools.com/tags/ref_urlencode.asp).
> 
> 

#### <a name="configure-destinations"></a>Konfigurace cíle
Slouží k definování cíle v poskytovateli Qpid vlastnosti souboru JNDI položka je v následujícím formátu:

```
queue.[jndi_name] = [physical_name]
```

nebo

```
topic.[jndi_name] = [physical_name]
```

Kde **[jndi\_název]** a **[fyzické\_název]** mají následující význam:

* **[jndi_name]** : Logický název cíle. Toto je název, který bude vyřešen v aplikaci v Javě pomocí metody JNDI IntialContext.lookup().
* **[physical_name]** : Název entity služby Service Bus, do které aplikace odesílá nebo přijímá zprávy.

> [!NOTE]
> Pro příjem z odběru tématu služby Service Bus, fyzický název zadaný v JNDI by měl být název tématu. Název předplatného se zadává při vytvoření odolné předplatného v kódu aplikace JMS. [Service Bus AMQP 1.0 – Příručka vývojáře](service-bus-amqp-dotnet.md) obsahuje další podrobnosti o práci s tématy Service Bus z JMS.
> 
> 

### <a name="write-the-jms-application"></a>Napíšeme aplikaci JMS
Neexistují žádné zvláštní rozhraní API nebo možnosti požaduje se při použití JMS službou Service Bus. Existují však několik omezení, které se věnujeme později. Stejně jako u jakékoli aplikace JMS nejprve thing požadované je konfigurace JNDI prostředí, které mají být schopen převést **ConnectionFactory** a cíle.

#### <a name="configure-the-jndi-initialcontext"></a>Konfigurace JNDI InitialContext
JNDI prostředí je nakonfigurované pomocí předání do konstruktoru třídy javax.naming.InitialContext zatřiďovací tabulku informace o konfiguraci. Dvě požadované prvky v zatřiďovací tabulky jsou název třídy objektu pro vytváření počáteční kontext a adresa URL zprostředkovatele. Následující kód ukazuje, jak nakonfigurovat prostředí JNDI používat Qpid vlastnosti souboru na základě JNDI poskytovatele pomocí vlastnosti souboru s názvem **servicebus.properties**.

```java
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
InitialContext context = new InitialContext(env);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Jednoduchou aplikaci JMS pomocí fronty Service Bus
Následující ukázkový program JMS TextMessages odešle do fronty služby Service Bus s JNDI logický název fronty a přijímá zprávy zpět.

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
Spuštění aplikace vytvoří výstup ve formátu:

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

## <a name="cross-platform-messaging-between-jms-and-net"></a>Multiplatformní zasílání zpráv mezi JMS a .NET
Tento průvodce vám ukázal, jak posílat a přijímat zprávy do a ze služby Service Bus pomocí JMS. Mezi klíčové výhody protokolu AMQP 1.0 je však, že umožňuje aplikacím, které mají být sestaveny z komponent, které jsou napsány v různých jazycích s zprávy vyměňují spolehlivě a s plnou věrností.

Pomocí JMS ukázkové aplikace popsané výše a podobně jako aplikace rozhraní .NET z doprovodný článek, [pomocí služby Service Bus z .NET pomocí protokolu AMQP 1.0](service-bus-amqp-dotnet.md), můžou vyměňovat zprávy mezi rozhraním .NET a Javy. V tomto článku pro další informace o podrobnosti o platformě zasílání zpráv služby Service Bus a protokolu AMQP 1.0.

### <a name="jms-to-net"></a>JMS pro .NET
K předvedení JMS k zasílání zpráv .NET:

* Spusťte ukázkovou aplikaci .NET bez argumentů příkazového řádku.
* Spuštění ukázkové aplikace Java s argumentem příkazového řádku "sendonly". V tomto režimu nebude aplikace přijme zprávy z fronty, kterou bude pouze odesílat.
* Stisknutím klávesy **Enter** několikrát v konzole aplikace Java, která způsobí zpráv k odeslání.
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

### <a name="net-to-jms"></a>.NET pro JMS
K předvedení .NET pro JMS zasílání zpráv:

* Spusťte ukázkovou aplikaci .NET s argumentem příkazového řádku "sendonly". V tomto režimu nebude aplikace přijme zprávy z fronty, kterou bude pouze odesílat.
* Spusťte ukázkovou aplikaci Java bez argumentů příkazového řádku.
* Stisknutím klávesy **Enter** několikrát v konzole aplikace .NET, které způsobí zpráv k odeslání.
* Tyto zprávy jsou přijímány aplikaci v Javě.

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
Při použití JMS prostřednictvím protokolu AMQP 1.0 se Service Bus, konkrétně existují následující omezení:

* Pouze jeden **MessageProducer** nebo **MessageConsumer** smí za **relace**. Pokud je potřeba vytvořit několik **MessageProducers** nebo **MessageConsumers** v aplikaci, vytvořte vyhrazený **relace** pro každý z nich.
* Odběry témat volatile se momentálně nepodporují.
* **MessageSelectors** se momentálně nepodporují.
* Dočasné cíle; například **TemporaryQueue**, **TemporaryTopic** se momentálně nepodporují, spolu s **QueueRequestor** a **TopicRequestor**Rozhraní API, která je používat.
* Počet relací a distribuované transakce nejsou podporované.

## <a name="summary"></a>Souhrn
Tato příručka vám ukázal, jak používat funkce Service Bus pro zprostředkované zasílání zpráv (fronty a témata publikace nebo odběru) z Javy pomocí oblíbených rozhraní API pro JMS a protokolu AMQP 1.0.

Služba Service Bus protokolu AMQP 1.0 můžete použít také z jiných jazyků, včetně .NET, C, Python a PHP. Komponenty sestavené pomocí těchto různých jazyků můžete spolehlivě výměnu zpráv a s plnou věrností pomocí protokolu AMQP 1.0 podporují ve službě Service Bus.

## <a name="next-steps"></a>Další postup
* [Podpora AMQP 1.0 ve službě Azure Service Bus](service-bus-amqp-overview.md)
* [Jak rozhraní .NET API služby Service Bus pomocí protokolu AMQP 1.0](service-bus-dotnet-advanced-message-queuing.md)
* [Service Bus AMQP 1.0 – Příručka vývojáře](service-bus-amqp-dotnet.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Středisko pro vývojáře Java](https://azure.microsoft.com/develop/java/)

