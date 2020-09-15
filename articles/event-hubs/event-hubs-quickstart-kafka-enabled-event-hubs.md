---
title: 'Rychlý Start: streamování dat pomocí Azure Event Hubs s využitím protokolu Kafka'
description: 'Rychlý Start: Tento článek poskytuje informace o tom, jak streamovat do Azure Event Hubs pomocí protokolu Kafka a rozhraní API.'
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 612a7515c14b77569b34c91d9b43413353b4d0e8
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2020
ms.locfileid: "90061542"
---
# <a name="quickstart-data-streaming-with-event-hubs-using-the-kafka-protocol"></a>Rychlý Start: streamování dat pomocí Event Hubs s využitím protokolu Kafka
V tomto rychlém startu se dozvíte, jak streamovat do Event Hubs bez změny klientů protokolu nebo spouštění vlastních clusterů. Naučíte se, jak používat vaše producenty a spotřebitelé ke komunikaci s Event Hubs jenom se změnou konfigurace v aplikacích. 

> [!NOTE]
> Tato ukázka je k dispozici na [GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java) .

## <a name="prerequisites"></a>Předpoklady

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

* Přečtěte si článek [Event Hubs pro Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md).
* Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks).
* [Stáhněte](https://maven.apache.org/download.cgi) a [nainstalujte](https://maven.apache.org/install.html) binární archiv Maven.
* [Git](https://www.git-scm.com/)


## <a name="create-an-event-hubs-namespace"></a>Vytvoření oboru názvů služby Event Hubs
Když vytvoříte obor názvů úrovně **standard** Event Hubs, je automaticky povolen koncový bod Kafka pro obor názvů. Můžete streamovat události z vašich aplikací, které používají protokol Kafka, do úrovně Standard Event Hubs. Postupujte podle podrobných pokynů v tématu [vytvoření centra událostí pomocí Azure Portal](event-hubs-create.md) k vytvoření oboru názvů Event Hubs úrovně **Standard** . 

> [!NOTE]
> Event Hubs pro Kafka je k dispozici pouze na **Standard** a **vyhrazené** úrovni. Úroveň **Basic** nepodporuje Kafka na Event Hubs.

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Odesílání a příjem zpráv pomocí Kafka ve službě Event Hubs

1. Naklonujte [úložiště Azure Event Hubs pro ekosystém Kafka](https://github.com/Azure/azure-event-hubs-for-kafka).

2. Přejděte na adresu `azure-event-hubs-for-kafka/quickstart/java/producer`.

3. Aktualizujte podrobnosti o konfiguraci producenta v `src/main/resources/producer.config` následujícím způsobem:

    **PROTOKOL TLS/SSL:**

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
4. Spusťte kód producenta a streamování událostí do Event Hubs:
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
    
5. Přejděte na adresu `azure-event-hubs-for-kafka/quickstart/java/consumer`.

6. Aktualizujte podrobnosti o konfiguraci příjemce v `src/main/resources/consumer.config` následujícím způsobem:
   
    **PROTOKOL TLS/SSL:**

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
7. Spusťte kód příjemce a zpracujte události z centra událostí pomocí klientů Kafka:

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Pokud váš cluster Event Hubs Kafka obsahuje události, začnete je teď přijímat od příjemce.

## <a name="next-steps"></a>Další kroky
V tomto článku jste zjistili, jak se streamovat do Event Hubs bez změny klientů protokolu nebo spouštění vlastních clusterů. Další informace najdete v tématu [Apache Kafka příručka pro vývojáře pro Azure Event Hubs](apache-kafka-developer-guide.md). 
