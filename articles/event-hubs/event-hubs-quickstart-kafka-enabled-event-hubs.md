---
title: 'Rychlý Start: streamování dat pomocí Azure Event Hubs s využitím protokolu Kafka'
description: 'Rychlý Start: Tento článek poskytuje informace o tom, jak streamovat do Azure Event Hubs pomocí protokolu Kafka a rozhraní API.'
services: event-hubs
author: ShubhaVijayasarathy
ms.author: shvija
ms.service: event-hubs
ms.topic: quickstart
ms.custom: seodec18
ms.date: 02/12/2020
ms.openlocfilehash: 25c1cf00a418767209467c973b7a4755f62eb16f
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368376"
---
# <a name="quickstart-data-streaming-with-event-hubs-using-the-kafka-protocol"></a>Rychlý Start: streamování dat pomocí Event Hubs s využitím protokolu Kafka
Tento rychlý start ukazuje, jak streamovat do služby Event Hubs s podporou Kafka, aniž byste museli měnit klienty protokolů nebo provozovat vlastní clustery. Zjistíte, jak prostou změnou konfigurace aplikací zajistit komunikaci producentů a příjemců se službou Event Hubs s podporou Kafka. Azure Event Hubs podporuje [Apache Kafka verze 1.0](https://kafka.apache.org/10/documentation.html).

> [!NOTE]
> Tato ukázka je k dispozici na [GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java).

## <a name="prerequisites"></a>Předpoklady

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

* Přečtěte si článek [Event Hubs pro Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md).
* Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks).
* [Stáhněte](https://maven.apache.org/download.cgi) a [nainstalujte](https://maven.apache.org/install.html) binární archiv Maven.
* [Git](https://www.git-scm.com/)
* [Obor názvů služby Event Hubs s podporou Kafka](event-hubs-create.md)

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Vytvoření oboru názvů služby Event Hubs s podporou Kafka
Když vytvoříte obor názvů úrovně Standard Event Hubs, je automaticky povolen koncový bod Kafka pro obor názvů. Můžete streamovat události z vašich aplikací, které používají protokol Kafka, do úrovně Standard Event Hubs. Pro obor názvů Event Hubs úrovně Basic není povolený. 

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Odesílání a příjem zpráv pomocí Kafka ve službě Event Hubs

1. Naklonujte [úložiště Azure Event Hubs pro ekosystém Kafka](https://github.com/Azure/azure-event-hubs-for-kafka).

2. Přejděte na adresu `azure-event-hubs-for-kafka/quickstart/java/producer`.

3. Aktualizujte podrobnosti o konfiguraci producenta v `src/main/resources/producer.config` následujícím způsobem:

    **ZABEZPEČENÍ**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
    **OAuth**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ```    

    Zdrojový kód pro třídu obslužné rutiny Sample CustomAuthenticateCallbackHandler na GitHubu najdete [tady](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/producer/src/main/java).
4. Spustit kód producenta a streamovat události do Event Hubs s podporou Kafka:
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
    
5. Přejděte na adresu `azure-event-hubs-for-kafka/quickstart/java/consumer`.

6. Aktualizujte podrobnosti o konfiguraci příjemce v `src/main/resources/consumer.config` následujícím způsobem:
   
    **ZABEZPEČENÍ**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

    **OAuth**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ``` 

    Zdrojový kód pro třídu obslužné rutiny Sample CustomAuthenticateCallbackHandler na GitHubu najdete [tady](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/consumer/src/main/java).

    Všechny ukázky OAuth pro Event Hubs pro Kafka najdete [tady](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth).
7. Spusťte kód příjemce a zpracování ze služby Event Hubs s podporou Kafka pomocí klientů Kafka.

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Pokud váš cluster Event Hubs Kafka obsahuje události, začnete je teď přijímat od příjemce.

## <a name="next-steps"></a>Další kroky
V tomto článku jste zjistili, jak streamovat do služby Event Hubs s podporou Kafka, aniž byste museli měnit klienty protokolů nebo provozovat vlastní clustery. Další informace najdete v následujících článcích a ukázkách:

- [Informace o službě Event Hubs pro ekosystém Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Rychlé starty pro Event Hubs pro Kafka na GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart)
- [Kurzy Event Hubs pro Kafka na GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials)
- Pomocí [nástroje MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) můžete [streamovat události z Kafka místně do Kafka povolených Event Hubs v cloudu.](event-hubs-kafka-mirror-maker-tutorial.md)
- Zjistěte, jak streamovat do služby Event Hubs s podporou Kafka pomocí [Apache Flinku](event-hubs-kafka-flink-tutorial.md) nebo [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md).
