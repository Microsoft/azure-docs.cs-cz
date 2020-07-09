---
title: Migrace aplikací JMS (Java Message Service) z ActiveMQ na Azure Service Bus | Microsoft Docs
description: Tento článek vysvětluje, jak migrovat existující aplikace JMS, které spolupracují s aktivním MQ pro interakci s Azure Service Bus.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2020
ms.author: aschhab
ms.openlocfilehash: 3da4f693f4cfec47c5456a0c5998f58f5fe02949
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2020
ms.locfileid: "86122288"
---
# <a name="migrate-existing-java-message-service-jms-20-applications-from-active-mq-to-azure-service-bus"></a>Migrujte existující aplikace JMS (Java Message Service) 2,0 z aktivního MQ do Azure Service Bus

Azure Service Bus podporuje Java/J2EE a pružinové úlohy, které využívají rozhraní JMS (Java Message Service) 2,0 API v rozšířeném protokolu AMQP (Message Queueing Protocol).

V této příručce se dozvíte, o čem byste měli vědět, kdy chcete upravit existující aplikaci JMS (Java Message Service) 2,0, která spolupracuje se službou JMS broker (konkrétně Apache ActiveMQ nebo Amazon MQ) k interakci s Azure Service Bus.

## <a name="before-you-start"></a>Než začnete

### <a name="differences-between-azure-service-bus-and-apache-activemq"></a>Rozdíly mezi Azure Service Bus a Apache ActiveMQ

Azure Service Bus a Apache ActiveMQ jsou zprostředkovatelé zpráv, které fungují jako poskytovatelé JMS pro klientské aplikace pro posílání zpráv a přijímání zpráv z. Současně umožňují sémantiku Point-to-Point s **frontami** a sémantika publikování a odběru s **tématy** a **odběry**. 

I tak je několik rozdílů v těchto dvou.

| Kategorie | Aktivní MQ | Azure Service Bus |
| --- | --- | --- |
| Vrstvení aplikací | Clusterované monolitu | Dvě vrstvy <br> (Brána + back-end) |
| Podpora protokolu | <ul> <li>AMQP</li> <li> STOMP </li> <li> OpenWire </li> </ul> | AMQP |
| Režim zřizování | <ul> <li> IaaS (místní) </li> <li> Amazon MQ (spravovaná PaaS) </li> | Spravované PaaS |
| Velikost zpráv | Uživatelsky konfigurovatelné | 1 MB (úroveň Premium) |
| Vysoká dostupnost | Spravované zákazníkem | Spravovaná platformou |
| Zotavení po havárii | Spravované zákazníkem | Spravovaná platformou | 

### <a name="current-supported-and-unsupported-features"></a>Aktuální podporované a nepodporované funkce

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

### <a name="caveats"></a>Upozornění

Dvě vrstvená povaha Azure Service Bus zajišťuje různé možnosti kontinuity podnikových operací (vysoká dostupnost a zotavení po havárii). Při používání funkcí JMS ale existují nějaké okolnosti.

#### <a name="service-upgrades"></a>Upgrady služby

V případě upgradů a restartování Service Bus se odstraní dočasné fronty nebo témata.

Pokud je aplikace citlivá na ztrátu dat pro dočasné fronty nebo témata, doporučujeme **Nepoužívat dočasné** fronty ani témata a místo toho používat odolné fronty, témata a odběry.

#### <a name="data-migration"></a>Migrace dat

V rámci migrace nebo změny klientských aplikací pro interakci s Azure Service Bus se data uložená v ActiveMQ nemigrují do Service Bus.

Vlastní aplikace může být potřeba k Vyprázdnit fronty ActiveMQ, témata a odběry a přehrajte zprávy do fronty, témata a předplatná Service Bus.

#### <a name="authentication-and-authorization"></a>Ověřování a autorizace

Pro Azure Service Bus je upřednostňovaným mechanismem ověřování Access Control na základě rolí (RBAC), které zajišťuje služba Azure Active Directory.

Protože RBAC se ale v současné době nepodporuje kvůli chybějící podpoře ověřování na základě deklarací identity založené na Apache QPID JMS.

V současné době se ověřování podporuje jenom s klíči SAS.

## <a name="pre-migration"></a>Před migrací

### <a name="version-check"></a>Kontrolu verze

Níže jsou uvedené komponenty, které se používají při psaní aplikací JMS a specifických verzí, které jsou podporované. 

| Komponenty | Verze |
|---|---|
| Rozhraní API pro JMS (Java Message Service) | 1,1 nebo vyšší |
| Protokol AMQP | 1.0 |

### <a name="ensure-that-amqp-ports-are-open"></a>Zajistěte, aby byly porty AMQP otevřené.

Azure Service Bus podporuje komunikaci přes protokol AMQP. Pro účely tohoto účelu je potřeba povolit komunikaci přes porty 5671 (AMQP) a 443 (TCP). V závislosti na tom, kde jsou klientské aplikace hostované, možná budete potřebovat lístek podpory, aby bylo možné komunikovat přes tyto porty.

> [!IMPORTANT]
> Azure Service Bus podporuje **pouze** protokol AMQP 1,0.

### <a name="set-up-enterprise-configurations-vnet-firewall-private-endpoint-etc"></a>Nastavení podnikových konfigurací (virtuální síť, brána firewall, privátní koncový bod atd.)

Azure Service Bus umožňuje různé funkce pro podnikové zabezpečení a vysokou dostupnost. Další informace o těchto tématech najdete v následujících odkazech k dokumentaci.

  * [Koncové body služby Virtual Network](service-bus-service-endpoints.md)
  * [Brána firewall](service-bus-ip-filtering.md)
  * [Šifrování na straně služby pomocí spravovaného klíče zákazníka (BYOK)](configure-customer-managed-key.md)
  * [Soukromé koncové body](private-link-service.md)
  * [Ověřování a autorizace](service-bus-authentication-and-authorization.md)

### <a name="monitoring-alerts-and-tracing"></a>Monitorování, výstrahy a trasování

Metriky jsou publikovány pro každý Service Bus obor názvů do Azure Monitor a lze je využít pro upozorňování a dynamické škálování prostředků přidělených oboru názvů.

Přečtěte si další informace o různých metrikách a o tom, jak na nich nastavit výstrahy při [Service Bus metrikách v Azure monitor](service-bus-metrics-azure-monitor.md).

Přečtěte si také další informace o [trasování na straně klienta pro operace s daty](service-bus-end-to-end-tracing.md) a [protokolování provozu a diagnostiky pro operace správy](service-bus-diagnostic-logs.md) .

### <a name="metrics---newrelic"></a>Metriky – NewRelic

Níže je užitečná příručka, na které se ActiveMQ mapují metriky, na které se metriky v Azure Service Bus. Níže jsou odkazována z NewRelic.

  * [ActiveMQ/Amazon MQ NewRelic – metriky](https://docs.newrelic.com/docs/integrations/amazon-integrations/aws-integrations-list/aws-mq-integration)
  * [Azure Service Bus metriky NewRelic](https://docs.newrelic.com/docs/integrations/microsoft-azure-integrations/azure-integrations-list/azure-service-bus-monitoring-integration)

> [!NOTE]
> NewRelic v současné době nemá bezproblémovou integraci s ActiveMQ, ale mají k dispozici metriky pro Amazon MQ.
> Vzhledem k tomu, že Amazon MQ je odvozen z ActiveMQ, následující Průvodce mapuje metriky NewRelic z AmazonMQ na Azure Service Bus.
>

|Seskupení metrik| Metrika AmazonMQ/aktivní MQ | Azure Service Bus metrika |
|------------|---------------------------|--------------------------|
|Nese|`CpuUtilization`|`CPUXNS`|
|Nese|`MemoryUsage`|`WSXNS`|
|Nese|`CurrentConnectionsCount`|`activeConnections`|
|Nese|`EstablishedConnectionsCount`|`activeConnections` + `connectionsClosed`|
|Nese|`InactiveDurableTopicSubscribersCount`|Využití metrik předplatného|
|Nese|`TotalMessageCount`|Využití úrovně fronty/tématu nebo předplatného`activeMessages`|
|Fronta/téma|`EnqueueCount`|`incomingMessages`|
|Fronta/téma|`DequeueCount`|`outgoingMessages`|
|Fronta|`QueueSize`|`sizeBytes`|



## <a name="migration"></a>Migrace

K migraci stávající aplikace v JMS 2,0 pro interakci s Azure Service Bus je třeba provést níže uvedené kroky.

### <a name="export-topology-from-activemq-and-create-the-entities-in-azure-service-bus-optional"></a>Export topologie z ActiveMQ a vytvoření entit v Azure Service Bus (volitelné)

Aby se mohly klientské aplikace bez problémů připojit k Azure Service Bus, topologie, která zahrnuje fronty, témata a odběry, se musí migrovat z **Apache ActiveMQ** na **Azure Service Bus**.

> [!NOTE]
> Pro aplikace JMS (Java Message Service) je vytváření front, témat a odběrů běhová operace. Většina poskytovatelů zpráv JMS (Java Message Service) nabízí funkci pro vytváření *front*, *témat* a *odběrů* za běhu.
>
> Výše uvedený krok je proto nepovinný.
>
> Chcete-li zajistit, aby vaše aplikace měla oprávnění k vytvoření topologie za běhu, ujistěte se, že se používá připojovací řetězec s oprávněním ***Spravovat pro správu SAS*** .

Akce 
  * Využití [nástrojů příkazového řádku ActiveMQ](https://activemq.apache.org/activemq-command-line-tools-reference) k exportu topologie
  * Opětovné vytvoření stejné topologie pomocí [šablony Azure Resource Manager](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
  * Spusťte šablonu Azure Resource Manager.


### <a name="import-the-maven-dependency-for-service-bus-jms-implementation"></a>Import závislosti Maven pro implementaci Service Bus JMS

Aby bylo zajištěno bezproblémové připojení k Azure Service Bus, musí být balíček ***Azure-ServiceBus-JMS*** přidán jako závislost k souboru Maven, `pom.xml` jak je uvedeno níže.

```xml
<dependencies>
...
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus-jms</artifactId>
    </dependency>
...
</dependencies>
```

### <a name="application-server-configuration-changes"></a>Změny konfigurace aplikačního serveru

Tato část je vlastní na aplikačním serveru, který je hostitelem klientských aplikací připojujících se k aktivnímu MQ.

#### <a name="tomcat"></a>Tomcat

Tady začínáme konfigurací specifickou pro aktivní MQ, jak je uvedeno v `/META-INF/context.xml` souboru.

```XML
<Context antiJARLocking="true">
    <Resource
        name="jms/ConnectionFactory"
        auth="Container"
        type="org.apache.activemq.ActiveMQConnectionFactory"
        description="JMS Connection Factory"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        brokerURL="tcp://localhost:61616"
        brokerName="LocalActiveMQBroker"
        useEmbeddedBroker="false"/>

    <Resource name="jms/topic/MyTopic"
        auth="Container"
        type="org.apache.activemq.command.ActiveMQTopic"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO"/>
    <Resource name="jms/queue/MyQueue"
        auth="Container"
        type="org.apache.activemq.command.ActiveMQQueue"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO.QUEUE"/>
</Context>
```

který může být upraven tak, aby odkazoval na Azure Service Bus

```xml
<Context antiJARLocking="true">
    <Resource
        name="jms/ConnectionFactory"
        auth="Container"
        type="com.microsoft.azure.servicebus.jms.ServiceBusJmsConnectionFactory"
        description="JMS Connection Factory"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        connectionString="<INSERT YOUR SERVICE BUS CONNECTION STRING HERE>"/>

    <Resource name="jms/topic/MyTopic"
        auth="Container"
        type="org.apache.qpid.jms.JmsTopic"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO"/>
    <Resource name="jms/queue/MyQueue"
        auth="Container"
        type="org.apache.qpid.jms.JmsQueue"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO.QUEUE"/>
</Context>
```

#### <a name="spring-applications"></a>Jarní aplikace

##### <a name="update-applicationproperties-file"></a>Aktualizovat `application.properties` soubor

Pokud se chcete připojit k ActiveMQ, použijte aplikaci pro jarní spouštění.

Cílem je ***Odebrat*** z tohoto souboru vlastnosti specifické pro ActiveMQ `application.properties` .

```properties
spring.activemq.broker-url=<ACTIVEMQ BROKER URL>
spring.activemq.user=<ACTIVEMQ USERNAME>
spring.activemq.password=<ACTIVEMQ PASSWORD>
```

Pak do souboru ***přidejte*** specifické vlastnosti Service Bus `application.properties` .

```properties
azure.servicebus.connection-string=Endpoint=myEndpoint;SharedAccessKeyName=mySharedAccessKeyName;SharedAccessKey=mySharedAccessKey
```

##### <a name="replace-the-activemqconnectionfactory-with-servicebusjmsconnectionfactory"></a>Nahradit ActiveMQConnectionFactory pomocí ServiceBusJmsConnectionFactory

Dalším krokem je nahrazení instance třídy ActiveMQConnectionFactory pomocí ServiceBusJmsConnectionFactory.

> [!NOTE] 
> Skutečné změny kódu jsou specifické pro aplikaci a způsob, jakým jsou spravovány závislosti, ale následující ukázka poskytuje pokyny, ***co*** je třeba změnit.
>

Dřív jste mohli vytvořit instanci objektu ActiveMQConnectionFactory, jak je uvedeno níže.

```java

String BROKER_URL = "<URL of the hosted ActiveMQ broker>";
ConnectionFactory factory = new ActiveMQConnectionFactory(BROKER_URL);

Connection connection = factory.createConnection();
connection.start();

```

Tato změna by se změnila na instanci objektu ServiceBusJmsConnectionFactory

```java

ServiceBusJmsConnectionFactorySettings settings = new ServiceBusJmsConnectionFactorySettings();
String SERVICE_BUS_CONNECTION_STRING = "<Service Bus Connection string>";

ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, settings);

Connection connection = factory.createConnection();
connection.start();

```

## <a name="post-migration"></a>Po migraci

Teď, když jste aplikaci změnili tak, aby se spouštěla odesílání a přijímala zprávy z Azure Service Bus, měli byste ověřit, že funguje podle očekávání. Až to uděláte, můžete pokračovat v dalším upřesnění a modernizovat zásobníku aplikace.

## <a name="next-steps"></a>Další kroky

Využijte [jaře Boot Starter pro Azure Service Bus JMS](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-azure-service-bus) , abyste mohli bezproblémovou integraci s Azure Service Bus.

Další informace o Service Bus zasílání zpráv a JMS (Java Message Service) najdete v následujících tématech:

* [Service Bus JMS](service-bus-java-how-to-use-jms-api-amqp.md)
* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
