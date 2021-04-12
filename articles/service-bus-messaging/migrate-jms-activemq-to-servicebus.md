---
title: Migrace aplikací JMS (Java Message Service) z Apache ActiveMQ na Azure Service Bus | Microsoft Docs
description: Tento článek vysvětluje, jak migrovat existující aplikace JMS, které komunikují s Apache ActiveMQ, abyste mohli pracovat s Azure Service Bus.
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
ms.custom: devx-track-java
ms.openlocfilehash: 4160a9ab4edbac8584eab2d4e5b9bf1ba11a9aec
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568779"
---
# <a name="migrate-existing-java-message-service-jms-20-applications-from-apache-activemq-to-azure-service-bus"></a>Migrujte stávající aplikace JMS (Java Message Service) 2,0 z Apache ActiveMQ na Azure Service Bus

Tento článek popisuje, jak upravit existující aplikaci JMS (Java Message Service) 2,0, která spolupracuje se zprostředkovatelem JMS pro interakci s Azure Service Bus. Konkrétně článek se zabývá migrací z Apache ActiveMQ nebo Amazon MQ.

Azure Service Bus podporuje platformu Java 2, edici Enterprise a pružinové úlohy, které používají rozhraní API JMS 2,0 nad rozšířeným protokolem AMQP (Message Queueing Protocol).

## <a name="before-you-start"></a>Než začnete

### <a name="differences-between-azure-service-bus-and-apache-activemq"></a>Rozdíly mezi Azure Service Bus a Apache ActiveMQ

Azure Service Bus a Apache ActiveMQ jsou zprostředkovatelé zpráv, kteří fungují jako poskytovatelé JMS pro klientské aplikace pro posílání zpráv a přijímání zpráv z. Současně umožňují sémantiku Point-to-Point s frontami a sémantiku publikování a odběru s tématy a odběry. 

I tak existuje několik rozdílů mezi těmito dvěma, jak ukazuje následující tabulka:

| Kategorie | ActiveMQ | Azure Service Bus |
| --- | --- | --- |
| Vrstvení aplikací | Clusterované monolitu | Dvě vrstvy <br> (Brána + back-end) |
| Podpora protokolu | <ul> <li>AMQP</li> <li> STOMP </li> <li> OpenWire </li> </ul> | AMQP |
| Režim zřizování | <ul> <li> Infrastruktura jako služba (IaaS), místní </li> <li> Amazon MQ (spravovaná platforma jako služba) </li> | Spravovaná platforma jako služba (PaaS) |
| Velikost zpráv | Uživatelsky konfigurovatelné | 1 MB (úroveň Premium) |
| Vysoká dostupnost | Spravované zákazníkem | Spravovaná platformou |
| Zotavení po havárii | Spravované zákazníkem | Spravovaná platformou | 

### <a name="current-supported-and-unsupported-features"></a>Aktuální podporované a nepodporované funkce

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

### <a name="considerations"></a>Požadavky

Dvě vrstvená povaha Azure Service Bus zajišťuje různé možnosti kontinuity podnikových operací (vysoká dostupnost a zotavení po havárii). Existují však některé okolnosti, pokud používáte funkce JMS.

#### <a name="service-upgrades"></a>Upgrady služby

V případě upgradu a restartování Service Bus se odstraní dočasné fronty nebo témata. Pokud je vaše aplikace citlivá na ztrátu dat pro dočasné fronty nebo témata, nepoužívejte dočasné fronty ani témata. Místo toho použijte odolné fronty, témata a odběry.

#### <a name="data-migration"></a>Migrace dat

V rámci migrace a změny klientských aplikací pro interakci s Azure Service Bus se data uložená v ActiveMQ nemigrují do Service Bus. Možná budete potřebovat vlastní aplikaci, která vyprázdní fronty, témata a odběry ActiveMQ, a poté přehraje zprávy do front, témat a předplatných Service Bus.

#### <a name="authentication-and-authorization"></a>Ověřování a autorizace

Řízení přístupu na základě role Azure (Azure RBAC), které zajišťuje Azure Active Directory, je upřednostňovaným mechanismem ověřování pro Service Bus. Vzhledem k tomu, že Apache QPID JMS v současnosti nepodporuje ověřování v Azure RBAC nebo založené na deklaracích identity, měli byste pro ověřování použít klíče SAS.

## <a name="pre-migration"></a>Před migrací

### <a name="version-check"></a>Kontrolu verze

Při psaní aplikací JMS používáte následující komponenty a verze: 

| Součást | Verze |
|---|---|
| Rozhraní API pro JMS (Java Message Service) | 1,1 nebo vyšší |
| Protokol AMQP | 1.0 |

### <a name="ensure-that-amqp-ports-are-open"></a>Zajistěte, aby byly porty AMQP otevřené.

Service Bus podporuje komunikaci přes protokol AMQP. Pro tento účel povolte komunikaci přes porty 5671 (AMQP) a 443 (TCP). V závislosti na tom, kde jsou klientské aplikace hostované, možná budete potřebovat lístek podpory, aby bylo možné komunikovat přes tyto porty.

> [!IMPORTANT]
> Service Bus podporuje pouze protokol AMQP 1,0.

### <a name="set-up-enterprise-configurations"></a>Nastavení podnikových konfigurací

Service Bus umožňuje různé funkce pro podnikové zabezpečení a vysokou dostupnost. Další informace naleznete v tématu: 

  * [Koncové body služby pro virtuální síť](service-bus-service-endpoints.md)
  * [Brána firewall](service-bus-ip-filtering.md)
  * [Šifrování na straně služby pomocí spravovaného klíče zákazníka (BYOK)](configure-customer-managed-key.md)
  * [Soukromé koncové body](private-link-service.md)
  * [Ověřování a autorizace](service-bus-authentication-and-authorization.md)

### <a name="monitoring-alerts-and-tracing"></a>Monitorování, výstrahy a trasování

Pro každý Service Bus obor názvů publikujete metriky do Azure Monitor. Tyto metriky můžete použít pro upozorňování a dynamické škálování prostředků, které jsou přiděleny oboru názvů.

Další informace o různých metrikách a o tom, jak nastavit výstrahy, najdete [v tématu Service Bus metriky v Azure monitor](service-bus-metrics-azure-monitor.md). Můžete také získat další informace o [trasování na straně klienta pro operace s daty](service-bus-end-to-end-tracing.md) a [protokolování provozu a diagnostiky pro operace správy](service-bus-diagnostic-logs.md).

### <a name="metrics---new-relic"></a>Metriky – nové Relic

Můžete sladit metriky z mapy ActiveMQ, na které se metriky v Azure Service Bus. Na novém webu Relic se podívejte na následující:

  * [ActiveMQ/Amazon MQ New Relic – metriky](https://docs.newrelic.com/docs/integrations/amazon-integrations/aws-integrations-list/aws-mq-integration)
  * [Azure Service Bus nové metriky Relic](https://docs.newrelic.com/docs/integrations/microsoft-azure-integrations/azure-integrations-list/azure-service-bus-monitoring-integration)

> [!NOTE]
> Nové Relic v současné době nemá přímou a bezproblémovou integraci s ActiveMQ, ale mají k dispozici metriky pro Amazon MQ. Protože Amazon MQ je odvozen od ActiveMQ, následující tabulka mapuje nové metriky Relic z Amazon MQ na Azure Service Bus.
>

|Seskupení metrik| ActiveMQ metrika Amazon MQ/ | Azure Service Bus metrika |
|------------|---------------------------|--------------------------|
|Nese|`CpuUtilization`|`CPUXNS`|
|Nese|`MemoryUsage`|`WSXNS`|
|Nese|`CurrentConnectionsCount`|`activeConnections`|
|Nese|`EstablishedConnectionsCount`|`activeConnections` + `connectionsClosed`|
|Nese|`InactiveDurableTopicSubscribersCount`|Použití metrik předplatného|
|Nese|`TotalMessageCount`|Použít úroveň fronty, tématu nebo předplatného `activeMessages`|
|Fronta/téma|`EnqueueCount`|`incomingMessages`|
|Fronta/téma|`DequeueCount`|`outgoingMessages`|
|Fronta|`QueueSize`|`sizeBytes`|



## <a name="migration"></a>Migrace

Pokud chcete migrovat stávající aplikaci v JMS 2,0 pro interakci s Service Bus, postupujte podle kroků v následujících částech.

### <a name="export-the-topology-from-activemq-and-create-the-entities-in-service-bus-optional"></a>Export topologie z ActiveMQ a vytvoření entit v Service Bus (volitelné)

Aby se klientské aplikace mohly bez problémů připojit k Service Bus, migrujte topologii (včetně front, témat a odběrů) z Apache ActiveMQ na Service Bus.

> [!NOTE]
> V případě aplikací JMS vytvoříte fronty, témata a předplatná jako běhové operace. Většina poskytovatelů JMS (zprostředkovatelé zpráv) vám dává možnost je vytvořit za běhu. To je důvod, proč se tento krok exportu považuje za volitelný. Chcete-li zajistit, aby měla vaše aplikace oprávnění k vytvoření topologie za běhu, použijte připojovací řetězec s `Manage` OPRÁVNĚNÍMI SAS.

Použijte následující postup:

1. K exportu topologie použijte [nástroje příkazového řádku ActiveMQ](https://activemq.apache.org/activemq-command-line-tools-reference) .
1. Znovu vytvořte stejnou topologii pomocí [šablony Azure Resource Manager](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).
1. Spusťte šablonu Azure Resource Manager.


### <a name="import-the-maven-dependency-for-service-bus-jms-implementation"></a>Import závislosti Maven pro implementaci Service Bus JMS

Chcete-li zajistit bezproblémové připojení k Service Bus, přidejte `azure-servicebus-jms` balíček jako závislost do souboru Maven následujícím `pom.xml` způsobem:

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

Tato část je přizpůsobená aplikačnímu serveru, který je hostitelem klientských aplikací připojujících se k ActiveMQ.

#### <a name="spring-applications"></a>Jarní aplikace

##### <a name="update-the-applicationproperties-file"></a>Aktualizace `application.properties` souboru

Pokud pro připojení k ActiveMQ používáte aplikaci pružinového spuštění, chcete ze souboru odebrat vlastnosti specifické pro ActiveMQ `application.properties` .

```properties
spring.activemq.broker-url=<ACTIVEMQ BROKER URL>
spring.activemq.user=<ACTIVEMQ USERNAME>
spring.activemq.password=<ACTIVEMQ PASSWORD>
```

Pak přidejte do souboru vlastnosti specifické pro Service Bus `application.properties` .

```properties
azure.servicebus.connection-string=Endpoint=myEndpoint;SharedAccessKeyName=mySharedAccessKeyName;SharedAccessKey=mySharedAccessKey
```

##### <a name="replace-activemqconnectionfactory-with-servicebusjmsconnectionfactory"></a>Nahradit `ActiveMQConnectionFactory``ServiceBusJmsConnectionFactory`

Dalším krokem je nahrazení instance třídy `ActiveMQConnectionFactory` pomocí `ServiceBusJmsConnectionFactory` .

> [!NOTE] 
> Skutečné změny kódu jsou specifické pro aplikaci a způsob, jakým jsou spravovány závislosti, ale následující ukázka poskytuje návod na to, co by mělo být změněno.
>

Dříve jste mohli vytvořit instanci objektu `ActiveMQConnectionFactory` , následujícím způsobem:

```java

String BROKER_URL = "<URL of the hosted ActiveMQ broker>";
ConnectionFactory factory = new ActiveMQConnectionFactory(BROKER_URL);

Connection connection = factory.createConnection();
connection.start();

```

Nyní tuto změnu měníte pro vytvoření instance objektu `ServiceBusJmsConnectionFactory` , následujícím způsobem:

```java

ServiceBusJmsConnectionFactorySettings settings = new ServiceBusJmsConnectionFactorySettings();
String SERVICE_BUS_CONNECTION_STRING = "<Service Bus Connection string>";

ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, settings);

Connection connection = factory.createConnection();
connection.start();

```

## <a name="post-migration"></a>Po migraci

Teď, když jste aplikaci změnili tak, aby spouštěla odesílání a příjem zpráv z Service Bus, měli byste ověřit, že funguje podle očekávání. Až to uděláte, můžete pokračovat v dalším upřesnění a modernizovat zásobníku aplikace.

## <a name="next-steps"></a>Další kroky

K bezproblémové integraci s Service Bus použijte [pružinový spouštěcí Starter pro Azure Service Bus JMS](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-azure-service-bus) .

Další informace o Service Bus zasílání zpráv a JMS najdete v tématech:

* [Service Bus JMS](service-bus-java-how-to-use-jms-api-amqp.md)
* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
