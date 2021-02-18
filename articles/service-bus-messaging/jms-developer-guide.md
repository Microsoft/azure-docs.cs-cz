---
title: Azure Service Bus příručka pro vývojáře JMS 2,0
description: Jak používat rozhraní JMS (Java Message Service) 2,0 API ke komunikaci s Azure Service Bus
ms.topic: article
ms.date: 01/17/2021
ms.openlocfilehash: 6c535b12906b6d9385029896dc5d0caf85d3399a
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654171"
---
# <a name="azure-service-bus-jms-20-developer-guide"></a>Azure Service Bus příručka pro vývojáře JMS 2,0

Tato příručka obsahuje podrobné informace, které vám pomohou úspěšně komunikovat s Azure Service Bus pomocí rozhraní Java Message Service (JMS) 2,0 API.

Pokud se Azure Service Bus vývojářům v jazyce Java, zvažte prosím, jak si přečtěte níže uvedené články.

| Začínáme | Koncepty |
|----------------|-------|
| <ul> <li> [Co je Azure Service Bus](service-bus-messaging-overview.md) </li> <li> [Fronty, témata a odběry](service-bus-queues-topics-subscriptions.md) </li> </ul> | <ul> <li> [Azure Service Bus – úroveň Premium](service-bus-premium-messaging.md) </li> </ul> |

## <a name="java-message-service-jms-programming-model"></a>Programovací model JMS (Java Message Service)

Programovací model rozhraní API pro službu Message Services jazyka Java je znázorněný níže. 

> [!NOTE]
>
>**Úroveň Premium Azure Service Bus** podporuje JMS 1,1 a JMS 2,0.
> <br> <br>
> **Azure Service Bus – úroveň Standard** podporuje omezené funkce JMS 1,1. Další podrobnosti najdete v této [dokumentaci](service-bus-java-how-to-use-jms-api-amqp.md).
>

# <a name="jms-20-programming-model"></a>[Programovací model JMS 2,0](#tab/JMS-20)

:::image type="content" source="./media/jms-developer-guide/java-message-service-20-programming-model.png"alt-text="Diagram znázorňující programovací model JMS 2,0"border="false":::

# <a name="jms-11-programming-model"></a>[Programovací model JMS 1,1](#tab/JMS-11)

:::image type="content" source="./media/jms-developer-guide/java-message-service-11-programming-model.png"alt-text="Diagram znázorňující programovací model JMS 1,1"border="false":::

---

## <a name="jms---building-blocks"></a>JMS – stavební bloky

Níže uvedené stavební bloky jsou k dispozici ke komunikaci s aplikací JMS.

> [!NOTE]
> Následující příručka byla upravena z [kurzu Oracle Java EE 6 pro službu zprávy Java (JMS)](https://docs.oracle.com/javaee/6/tutorial/doc/bnceh.html) .
>
> Odkaz na tento kurz se doporučuje pro lepší porozumění službě Java Message Service (JMS).
>

### <a name="connection-factory"></a>Továrna připojení
Objekt factory připojení se používá pro připojení klienta k poskytovateli JMS. Objekt pro vytváření připojení zapouzdřuje sadu parametrů konfigurace připojení, které jsou definovány správcem.

Každá továrna připojení je instancí `ConnectionFactory` `QueueConnectionFactory` `TopicConnectionFactory` rozhraní nebo.

Pro zjednodušení připojení k Azure Service Bus jsou tato rozhraní implementována prostřednictvím `ServiceBusJmsConnectionFactory` `ServiceBusJmsQueueConnectionFactory` a `ServiceBusJmsTopicConnectionFactory` v uvedeném pořadí. Pomocí níže uvedených parametrů se dá vytvořit instance objektu pro vytváření připojení – 
   * Připojovací řetězec – připojovací řetězec pro obor názvů Azure Service Bus úrovně Premium.
   * Kontejner vlastností ServiceBusJmsConnectionFactorySettings, který obsahuje
      * connectionIdleTimeoutMS-nečinný časový limit připojení v milisekundách
      * příznak traceFrames-Boolean pro shromáždění snímků trasování AMQP pro ladění.
      * *Další parametry konfigurace*

Objekt pro vytváření lze vytvořit níže. Připojovací řetězec je povinný parametr, ale další vlastnosti jsou volitelné.

```java
ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, null);
```

> [!IMPORTANT]
> Aplikace Java využívající rozhraní JMS 2,0 API se musí připojit k Azure Service Bus jenom pomocí připojovacího řetězce. V současné době se ověřování pro klienty JMS podporuje jenom pomocí připojovacího řetězce.
>
> Ověřování zálohovaným službou Azure Active Directory (AAD) se momentálně nepodporuje.
>

### <a name="jms-destination"></a>JMS cíl

Cíl je objekt, který klient používá k určení cíle zpráv, které vytváří, a zdroje zpráv, které spotřebovává.

Cíle jsou mapovány na entity ve Azure Service Bus – fronty (ve scénářích Point-to-Point) a témata (ve scénářích s podadresáři pro publikování).

### <a name="connections"></a>Připojení

Připojení zapouzdřuje virtuální připojení k poskytovateli JMS. V Azure Service Bus to představuje stavové spojení mezi aplikací a Azure Service Bus přes AMQP.

Připojení se vytvoří z továrny připojení, jak je znázorněno níže.

```java
Connection connection = factory.createConnection();
```

### <a name="sessions"></a>Relace

Relace je kontext s jedním vláknem pro vytváření a využívání zpráv. Dá se využít k vytváření zpráv, výrobcům zpráv a spotřebitelům, ale také poskytuje transakční kontext, který umožňuje seskupení odeslání a přijetí do atomické pracovní jednotky.

Relaci lze vytvořit z objektu připojení, jak je znázorněno níže.

```java
Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
```

#### <a name="session-modes"></a>Režimy relace

Relaci lze vytvořit v libovolném z následujících režimů.

| Režimy relace | Chování |
| ----------------- | -------- |
|**Session.AUTO_ACKNOWLEDGE** | Relace automaticky potvrdí přijetí zprávy u klienta buď v případě, že se relace úspěšně vrátila z volání funkce Receive, nebo když zpráva naslouchacího procesu, kterou relace volala ke zpracování zprávy, úspěšně vrátí.|
|**Session.CLIENT_ACKNOWLEDGE** |Klient potvrdí využívané zprávy voláním metody potvrzení zprávy.|
|**Session.DUPS_OK_ACKNOWLEDGE**|Tento potvrzovací režim instruuje relaci, aby laxně vytvářená potvrzení doručení zpráv.| 
|**Session.SESSION_TRANSACTED**|Tato hodnota může být předána jako argument metody createSession (int sessionMode) objektu Connection, aby bylo možné určit, že relace má používat místní transakci.| 

Pokud není zadán režim relace, **Session.AUTO_ACKNOWLEDGE** je ve výchozím nastavení vybrán.

### <a name="jmscontext"></a>JMSContext

> [!NOTE]
> JMSContext se definuje jako součást specifikace JMS 2,0.
>

JMSContext kombinuje funkce poskytované připojením a objektem relace. Dá se vytvořit z objektu factory připojení.

```java
JMSContext context = connectionFactory.createContext();
```

#### <a name="jmscontext-modes"></a>JMSContext režimy

Stejně jako u objektu **relace** lze JMSContext vytvořit se stejnými režimy potvrzení, jak je uvedeno v [režimech relace](#session-modes).

```java
JMSContext context = connectionFactory.createContext(JMSContext.AUTO_ACKNOWLEDGE);
```

Pokud není zadán režim, **JMSContext.AUTO_ACKNOWLEDGE** je vybráno ve výchozím nastavení.

### <a name="jms-message-producers"></a>JMS výrobci zpráv

Výrobce zprávy je objekt, který je vytvořen pomocí JMSContext nebo relace a slouží k odesílání zpráv do cíle.

Dá se vytvořit buď jako samostatný objekt, jak je uvedeno níže. 

```java
JMSProducer producer = context.createProducer();
```

nebo vytvořená za běhu, když se vyžaduje odeslání zprávy.

```java
context.createProducer().send(destination, message);
```

### <a name="jms-message-consumers"></a>Příjemci zpráv JMS

Příjemce zprávy je objekt vytvořený pomocí JMSContext nebo relace a slouží k přijímání zpráv odeslaných do cílového umístění. Dá se vytvořit, jak je znázorněno níže.

```java
JMSConsumer consumer = context.createConsumer(dest);
```

#### <a name="synchronous-receives-via-receive-method"></a>Synchronní příjem prostřednictvím metody Receive ()

Příjemce zprávy poskytuje synchronní způsob, jak přijímat zprávy z cíle prostřednictvím `receive()` metody.

Pokud nejsou zadány žádné argumenty nebo časový limit, nebo je zadán časový limit 0, pak příjemce zablokuje neomezenou dobu, pokud nepřijde zpráva, nebo je připojení přerušeno (podle toho, co je dřívější).

```java
Message m = consumer.receive();
Message m = consumer.receive(0);
```

Při zadání nenulového kladného argumentu zablokuje příjemce do vypršení platnosti časovače.

```java
Message m = consumer.receive(1000); // time out after one second.
```

#### <a name="asynchronous-receives-with-jms-message-listeners"></a>Asynchronní příjem pomocí naslouchacího procesu zpráv JMS

Naslouchací proces zpráv je objekt, který se používá k asynchronnímu zpracování zpráv v cíli. Implementuje rozhraní, `MessageListener` které obsahuje `onMessage` metodu, ve které musí být konkrétní obchodní logika živá.

Pro objekt naslouchacího procesu zprávy musí být vytvořena instance a zaregistrována pro konkrétního příjemce zprávy pomocí `setMessageListener` metody.

```java
Listener myListener = new Listener();
consumer.setMessageListener(myListener);
```

### <a name="consuming-from-topics"></a>Využívání z témat

[Příjemci zpráv JMS](#jms-message-consumers) se vytvářejí s [cílem](#jms-destination) , který může být frontou nebo tématem.

Příjemci na frontách jsou jenom objekty na straně klienta, které jsou v kontextu relace (a připojení) mezi klientskou aplikací a Azure Service Bus.

Příjemci s tématy však mají 2 části – 
   * **Objekt na straně klienta** , který je v kontextu relace (nebo JMSContext), a
   * **Předplatné** , které je entita na Azure Service Bus.

Tato předplatná jsou popsána [zde](java-message-service-20-entities.md#java-message-service-jms-subscriptions) a může to být jedna z následujících: 
   * Sdílená trvalá předplatná
   * Sdílená netrvalá předplatná
   * Nesdílené trvalé odběry
   * Nesdílené odběry, které nejsou trvalé

### <a name="jms-queue-browsers"></a>Prohlížeče fronty JMS

Rozhraní JMS API poskytuje `QueueBrowser` objekt, který umožňuje aplikaci procházet zprávy ve frontě a zobrazovat hodnoty hlaviček pro každou zprávu.

Prohlížeč front lze vytvořit pomocí JMSContext, jak je uvedeno níže.

```java
QueueBrowser browser = context.createBrowser(queue);
```

> [!NOTE]
> Rozhraní JMS API neposkytuje rozhraní API pro procházení tématu.
>
> Důvodem je skutečnost, že se zprávy neukládají do samotného tématu. Jakmile se zpráva pošle do tématu, přepošle se na příslušné odběry.
>

### <a name="jms-message-selectors"></a>Selektory zpráv JMS

Selektory zpráv lze použít při přijímání aplikací k filtrování přijatých zpráv. V případě selektorů zpráv přijímající aplikace přesměruje práci s filtrováním zpráv do poskytovatele JMS (v tomto případě Azure Service Bus) místo toho, aby tuto odpovědnost přebíral.

Selektory se dají využít při vytváření kteréhokoli z následujících uživatelů – 
   * Sdílené trvalé předplatné
   * Nesdílené trvalé předplatné
   * Sdílené netrvalé předplatné
   * Nesdílené předplatné bez trvalého sdílení
   * Prohlížeč fronty


## <a name="summary"></a>Souhrn

Tento průvodce vývojářem předvádí, jak se pomocí služby JMS (Java Message Service) může připojit k Azure Service Bus.

## <a name="next-steps"></a>Další kroky

Další informace o Azure Service Bus a podrobnostech o entitách JMS (Java Message Service) najdete na odkazech níže. 
* [Service Bus – fronty, témata a předplatná](service-bus-queues-topics-subscriptions.md)
* [Service Bus – entity služby zprávy Java](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities)
* [Podpora AMQP 1,0 v Azure Service Bus](service-bus-amqp-overview.md)
* [Příručka pro vývojáře Service Bus AMQP 1,0](service-bus-amqp-dotnet.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Rozhraní API služby zpráv Java (externí doc Oracle)](https://docs.oracle.com/javaee/7/api/javax/jms/package-summary.html)
* [Naučte se migrovat z ActiveMQ na Service Bus](migrate-jms-activemq-to-servicebus.md)
