---
title: 'Úvodní příručka: Streamování dat pomocí Azure Event Hubs pomocí protokolu Kafka'
description: 'Úvodní příručka: Tento článek obsahuje informace o tom, jak streamovat do Azure Event Hubs pomocí protokolu Kafka a api.'
services: event-hubs
author: ShubhaVijayasarathy
ms.author: shvija
ms.service: event-hubs
ms.topic: quickstart
ms.custom: seodec18
ms.date: 02/12/2020
ms.openlocfilehash: 67ee882acab22d977f08124591289e9cfc7cded1
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261818"
---
# <a name="quickstart-data-streaming-with-event-hubs-using-the-kafka-protocol"></a>Úvodní příručka: Streamování dat pomocí centra událostí pomocí protokolu Kafka
Tento rychlý start ukazuje, jak streamovat do centra událostí bez změny klientů protokolu nebo spuštění vlastních clusterů. Naučíte se, jak používat výrobce a spotřebitele k rozhovoru s event huby s jenom změnou konfigurace ve vašich aplikacích. Azure Event Hubs podporuje [Apache Kafka verze 1.0](https://kafka.apache.org/10/documentation.html).

> [!NOTE]
> Tato ukázka je k dispozici na [GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java)

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

* Přečtěte si článek [Event Hubs pro Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md).
* Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks).
* [Stáhněte](https://maven.apache.org/download.cgi) a [nainstalujte](https://maven.apache.org/install.html) binární archiv Maven.
* [Git](https://www.git-scm.com/)


## <a name="create-an-event-hubs-namespace"></a>Vytvoření oboru názvů služby Event Hubs
Když vytvoříte obor názvů Centra událostí **standardní** vrstvy, koncový bod Kafka pro obor názvů je automaticky povolen. Můžete streamovat události z vašich aplikací, které používají protokol Kafka do centra událostí standardní úrovně. Postupujte podle podrobných pokynů v [centru vytváření událostí pomocí portálu Azure portal](event-hubs-create.md) k vytvoření oboru názvů Centra událostí na úrovni **úrovně.** 

> [!NOTE]
> Event Huby pro Kafku jsou dostupné jenom na **standardních** a **vyhrazených** úrovních. **Základní** úroveň nepodporuje Kafka v event hubech.

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Odesílání a příjem zpráv pomocí Kafka ve službě Event Hubs

1. Naklonujte [úložiště Azure Event Hubs pro ekosystém Kafka](https://github.com/Azure/azure-event-hubs-for-kafka).

2. Přejděte na adresu `azure-event-hubs-for-kafka/quickstart/java/producer`.

3. Aktualizujte podrobnosti o konfiguraci producenta v `src/main/resources/producer.config` následujícím způsobem:

    **TLS/SSL:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
    **Oauth:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ```    

    Zdrojový kód pro třídu ukázkové obslužné rutiny CustomAuthenticateCallbackHandler najdete [na GitHubu zde](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/producer/src/main/java).
4. Spusťte kód producenta a streamujte události do centra událostí:
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
    
5. Přejděte na adresu `azure-event-hubs-for-kafka/quickstart/java/consumer`.

6. Aktualizujte podrobnosti o konfiguraci příjemce v `src/main/resources/consumer.config` následujícím způsobem:
   
    **TLS/SSL:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

    **Oauth:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ``` 

    Zdrojový kód pro třídu ukázkové obslužné rutiny CustomAuthenticateCallbackHandler najdete [na GitHubu zde](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/consumer/src/main/java).

    Všechny ukázky OAuth pro Event Huby pro Kafku [najdete zde](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth).
7. Spusťte kód příjemce a zpracovávejte události z centra událostí pomocí klientů Kafka:

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Pokud váš cluster Event Hubs Kafka obsahuje události, začnete je teď přijímat od příjemce.

## <a name="next-steps"></a>Další kroky
V tomto článku jste se dozvěděli, jak streamovat do centra událostí bez změny klientů protokolu nebo spuštění vlastních clusterů. Další informace najdete v [tématu Apache Kafka developer guide for Azure Event Hubs](apache-kafka-developer-guide.md). 
