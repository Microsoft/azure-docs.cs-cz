---
title: Použití AMQP s rozhraním API služby Java Message Service & Azure Service Bus
description: Jak používat službu JMS (Java Message Service) s Azure Service Bus a rozšířený protokol řízení front zpráv (AMQP) (AMQP) 1,0.
ms.topic: article
ms.date: 06/23/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 04d2595951640b7fe878decfeb862863f06c17a2
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119153"
---
# <a name="use-the-java-message-service-jms-with-azure-service-bus-and-amqp-10"></a>Použití JMS (Java Message Service) s Azure Service Bus a AMQP 1,0

Podpora protokolu **Rozšířený protokol řízení front zpráv (AMQP) (AMQP) 1,0** v Azure Service Bus znamená, že můžete používat funkce řazení a publikování/přihlášení k odběru služby Řízení front zpráv z řady platforem pomocí efektivního binárního protokolu. Kromě toho můžete sestavovat aplikace tvořené komponentami sestavenými pomocí kombinace jazyků, platforem a operačních systémů.

V tomto článku se dozvíte, jak používat funkce Azure Service Bus zasílání zpráv (fronty a témata pro publikování a odběr) z aplikací Java pomocí oblíbených rozhraní API **služby Java Message Service (JMS)** v protokolu AMQP.

## <a name="get-started-with-service-bus"></a>Začínáme se službou Service Bus
V tomto průvodci se předpokládá, že už máte obor názvů Service Bus obsahující frontu s názvem `basicqueue` . Pokud to neuděláte, můžete [vytvořit obor názvů a frontu](service-bus-create-namespace-portal.md) pomocí [Azure Portal](https://portal.azure.com). Další informace o tom, jak vytvořit Service Bus obory názvů a fronty, najdete v tématu Začínáme [s Service Bus fronty](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Dělené fronty a témata také podporují AMQP. Další informace najdete v tématech [rozdělené entity zasílání zpráv](service-bus-partitioning.md) a [Podpora AMQP 1,0 pro Service Bus dělené fronty a témata](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
>

## <a name="what-jms-features-are-supported"></a>Jaké funkce JMS jsou podporovány?

Tady jsou funkce JMS, které jsou podporované v Azure Service Bus.

| Funkce | Azure Service Bus úrovně Standard – JMS 1,1 | Azure Service Bus úrovně Premium – JMS 2,0 (Preview) |
|---|---|---|
| Automatické vytváření entit přes AMQP | Nepodporuje se | **Doložen** |
| Fronty | **Doložen** | **Doložen** |
| Témata | **Doložen** | **Doložen** |
| Dočasné fronty | Nepodporuje se <br/> (Místo toho vytvořte běžnou frontu pomocí *AutoDeleteOnIdle* sady) | **Doložen** |
| Dočasná témata | Nepodporuje se | **Doložen** |
| Selektory zpráv | Nepodporuje se | **Doložen** |
| Prohlížeče front | Nepodporuje se <br/> (Použití funkce *prohlížení* rozhraní API Service Bus) | **Doložen** |
| Sdílená trvalá předplatná | **Doložen** | **Doložen**|
| Nesdílené trvalé odběry | Nepodporuje se | **Doložen** |
| Sdílená netrvalá předplatná | Nepodporuje se | **Doložen** |
| Nesdílené odběry, které nejsou trvalé | Nepodporuje se | **Doložen** |
| Zrušit odběr trvalých předplatných | Nepodporuje se | **Doložen** |
| ReceiveNoWait | Nepodporuje se | **Doložen** |
| Distribuované transakce | Nepodporuje se | Nepodporuje se |
| Trvalé trvají | Nepodporuje se | Nepodporuje se |

### <a name="additional-caveats-for-service-bus-standard-tier"></a>Další upozornění pro úroveň Service Bus úrovně Standard
V jedné **relaci**je povolena pouze jedna **MessageProducer** nebo **MessageConsumer** . Pokud potřebujete v aplikaci vytvořit více **MessageProducers** nebo **MessageConsumers** , vytvořte pro každou z nich vyhrazenou **relaci** .

## <a name="downloading-the-java-message-service-jms-client-library"></a>Stažení klientské knihovny JMS (Java Message Service)

Aby bylo možné se připojit pomocí Azure Service Bus a využívat rozhraní API JMS (Java Message Service) nad AMQP, je třeba využít níže uvedené knihovny. Ty musí být přidány do cesty sestavení pomocí nástroje pro správu preferovaných závislostí pro váš projekt.

Požadovaná knihovna klienta závisí na tom, která cenová úroveň se používá.

### <a name="premium-tier---jms-20-over-amqp-preview"></a>Úroveň Premium – JMS 2,0 nad AMQP (Preview)

Aby bylo možné využívat všechny funkce verze Preview, které jsou dostupné na úrovni Azure Service Bus Premium, využijte knihovnu [Azure-ServiceBus-JMS](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) Library.

### <a name="standard-tier---jms-11-over-amqp"></a>Úroveň Standard – JMS 1,1 nad AMQP

Pokud chcete využívat funkce JMS podporované úrovní Service Bus úrovně Standard (podívejte se, [Jaké funkce JMS se podporují?](service-bus-java-how-to-use-jms-api-amqp.md#what-jms-features-are-supported)), využijte níže uvedené knihovny –

* [Geronimo JMS 1,1 – specifikace](https://search.maven.org/artifact/org.apache.geronimo.specs/geronimo-jms_1.1_spec)
* [Klient JMS Qpid](https://search.maven.org/artifact/org.apache.qpid/qpid-jms-client)

> [!NOTE]
> Názvy a verze JAR JMS se možná změnily. Podrobnosti najdete v tématu [QPID JMS-AMQP 1,0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).
>

## <a name="coding-java-applications"></a>Kódování aplikací Java

Po naimportování závislostí můžou být aplikace Java napsány nezávislá způsobem poskytovatele JMS.

Vzhledem k tomu, že Azure Service Bus úrovně Standard a Premium se liší v závislosti a počtu podporovaných funkcí JMS, je programovací model pro tyto dva mírně odlišný.

> [!IMPORTANT]
> Níže uvedený průvodce ukazuje, jak se připojit k Azure Service Bus v dané jednoduché aplikaci.
>
> Vzhledem k tomu, že většina architektur podnikových aplikací může mít vlastní způsob, jak spravovat závislosti a konfigurace, použijte níže uvedený postup, který vám pomůže pochopit, co je potřeba, a správně přizpůsobit aplikaci.
>

### <a name="connecting-to-azure-service-bus-using-jms"></a>Připojení k Azure Service Bus pomocí JMS

Pokud se chcete připojit k Azure Service Bus pomocí klientů JMS, potřebujete **připojovací** řetězec, který je k dispozici v zásadách sdíleného přístupu v [Azure Portal](https://portal.azure.com) pod **primárním připojovacím řetězcem**.


#### <a name="connecting-to-azure-service-bus-premium-over-jms-20-preview"></a>Připojení k Azure Service Bus Premium přes JMS 2,0 (Preview)

1. Vytvoření instance`ServiceBusJmsConnectionFactorySettings`
    ```java
    ServiceBusJmsConnectionFactorySettings connFactorySettings = new ServiceBusJmsConnectionFactorySettings();

    connFactorySettings.setConnectionIdleTimeoutMS(20000);
    ```
2. Vytvořte instanci `ServiceBusJmsConnectionFactory` s příslušným `ServiceBusConnectionString` .
    ```java
    String ServiceBusConnectionString = "<SERVICE_BUS_CONNECTION_STRING_WITH_MANAGE_PERMISSIONS>";
    ConnectionFactory factory = new ServiceBusJmsConnectionFactory(ServiceBusConnectionString, connFactorySettings);
    ```

3. Použijte `ConnectionFactory` k vytvoření `Connection` a pak`Session` 
    ```java
    Connection connection = factory.createConnection();
    Session session = connection.createSession();
    ```
    nebo `JMSContext` (pro klienty JMS 2,0)

    ```java
    JMSContext jmsContext = factory.createContext();
    ```

#### <a name="connecting-to-azure-service-bus-standard-over-jms-11"></a>Připojení k Azure Service Bus Standard přes JMS 1,1

1. Vložte Azure Service Bus konfiguraci do souboru vlastností JNDI s názvem **ServiceBus. Properties**.
    ```properties
    # servicebus.properties - sample JNDI configuration
    
    # Register a ConnectionFactory in JNDI using the form:
    # connectionfactory.[jndi_name] = [ConnectionURL]
    connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
    ```

2. Nastavení kontextu JNDI a konfigurace ConnectionFactory
    ```java
    ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);

    // set up JNDI context
    Hashtable<String, String> hashtable = new Hashtable<>();
    hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
    hashtable.put("queue.QUEUE", "BasicQueue");
    hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
    Context context = new InitialContext(hashtable);
    
    ConnectionFactory factory = (ConnectionFactory) context.lookup("SBCF");
    ```
3. Použijte `ConnectionFactory` k vytvoření `Connection` a a pak `Session` .
    ```java
    Connection connection - factory.createConnection(csb.getSasKeyName(), csb.getSasKey());
    Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
    ```

### <a name="write-the-jms-application"></a>Zápis aplikace JMS

Po `Session` `JMSContext` vytvoření instance nebo může vaše aplikace využívat známá rozhraní JMS API k provádění operací správy a dat.

Pokud chcete zjistit, která rozhraní API budou v jednotlivých úrovních podporovaná, přečtěte si seznam [podporovaných funkcí JMS](service-bus-java-how-to-use-jms-api-amqp.md#what-jms-features-are-supported) pro úroveň Standard a Premium.

## <a name="summary"></a>Souhrn
Tato příručka předvádí, jak mohou klientské aplikace Java využívající JMS (Java Message Service) prostřednictvím AMQP 1,0 pracovat s Azure Service Bus.

Service Bus AMQP 1,0 můžete také použít z jiných jazyků, včetně .NET, C, Pythonu a PHP. Komponenty sestavené pomocí těchto různých jazyků můžou spolehlivě vyměňovat zprávy a s plnou věrností pomocí podpory AMQP 1,0 v Service Bus.

## <a name="next-steps"></a>Další kroky

Další informace o Azure Service Bus a podrobnostech o entitách JMS (Java Message Service) najdete v odkazech níže. 
* [Service Bus – fronty, témata a odběry](service-bus-queues-topics-subscriptions.md)
* [Service Bus – entity služby zprávy Java](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities-preview)
* [Podpora AMQP 1,0 v Azure Service Bus](service-bus-amqp-overview.md)
* [Příručka pro vývojáře Service Bus AMQP 1,0](service-bus-amqp-dotnet.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)

