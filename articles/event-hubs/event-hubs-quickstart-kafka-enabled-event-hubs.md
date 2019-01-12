---
title: Stream do centra událostí povolené Kafka – Azure Event Hubs | Dokumentace Microsoftu
description: Tento článek obsahuje informace do datového proudu do služby Azure Event Hubs pomocí rozhraní API a protokol Kafka.
services: event-hubs
author: basilhariri
ms.author: bahariri
ms.service: event-hubs
ms.topic: quickstart
ms.custom: seodec18
ms.date: 12/06/2018
ms.openlocfilehash: 541095ddff19f16e3050d79846a339d4e5bad2b8
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2019
ms.locfileid: "54244286"
---
# <a name="stream-into-event-hubs-for-the-apache-kafka"></a>Streamování do služby Event Hubs pro Apache Kafka
Tento rychlý start ukazuje, jak streamovat do služby Event Hubs s podporou Kafka, aniž byste museli měnit klienty protokolů nebo provozovat vlastní clustery. Zjistíte, jak prostou změnou konfigurace aplikací zajistit komunikaci producentů a příjemců se službou Event Hubs s podporou Kafka. Azure Event Hubs podporuje [Apache Kafka verze 1.0](https://kafka.apache.org/10/documentation.html).

> [!NOTE]
> Tato ukázka je k dispozici na [GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java).

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

* Přečtěte si článek [Event Hubs pro Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md).
* Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks).
* [Stáhněte](http://maven.apache.org/download.cgi) a [nainstalujte](http://maven.apache.org/install.html) binární archiv Maven.
* [Git](https://www.git-scm.com/)
* [Obor názvů služby Event Hubs s podporou Kafka](event-hubs-create.md)

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Vytvoření oboru názvů služby Event Hubs s podporou Kafka

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a klikněte na tlačítko **vytvořit prostředek** v levém horním rohu obrazovky.

2. Vyhledejte službu Event Hubs a vyberte zde uvedené možnosti:
    
    ![Vyhledání služby Event Hubs na portálu](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. Zadejte jedinečný název a povolte Kafka pro obor názvů. Klikněte na možnost **Vytvořit**. Poznámka: Event Hubs pro Kafka je pouze podporovány standardní a Event Hubs úrovně Dedicated. Event Hubs úrovně Basic vrátí chybu ověření téma v reakci na jakékoli operace Kafka.
    
    ![Vytvoření oboru názvů](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.jpg)
 
4. Po vytvoření oboru názvů na kartě **Nastavení** klikněte na **Zásady sdíleného přístupu** a získejte připojovací řetězec.

    ![Kliknutí na Zásady sdíleného přístupu](./media/event-hubs-create/create-event-hub7.png)

5. Můžete zvolit výchozí zásadu **RootManageSharedAccessKey** nebo přidat novou. Klikněte na název zásady a zkopírujte připojovací řetězec. 
    
    ![Výběr zásady](./media/event-hubs-create/create-event-hub8.png)
 
6. Přidejte tento připojovací řetězec do konfigurace vaší aplikace Kafka.

Teď můžete ze svých aplikací používajících protokol Kafka streamovat události do služby Event Hubs.

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Odesílání a příjem zpráv pomocí Kafka ve službě Event Hubs

1. Naklonujte [úložiště Azure Event Hubs pro ekosystém Kafka](https://github.com/Azure/azure-event-hubs-for-kafka).

2. Přejděte na adresu `azure-event-hubs-for-kafka/quickstart/java/producer`.

3. Aktualizujte podrobnosti o konfiguraci producenta v `src/main/resources/producer.config` následujícím způsobem:

    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
    
4. Spusťte kód producenta a streamování do služby Event Hubs s podporou Kafka.
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
    
5. Přejděte na adresu `azure-event-hubs-for-kafka/quickstart/java/consumer`.

6. Aktualizujte podrobnosti o konfiguraci příjemce v `src/main/resources/consumer.config` následujícím způsobem:
   
    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

7. Spusťte kód příjemce a zpracování ze služby Event Hubs s podporou Kafka pomocí klientů Kafka.

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Pokud váš cluster Event Hubs Kafka obsahuje události, začnete je teď přijímat od příjemce.

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak streamovat do služby Event Hubs s podporou Kafka, aniž byste museli měnit klienty protokolů nebo provozovat vlastní clustery. Další informace najdete v následujícím kurzu:

* [Informace o službě Event Hubs](event-hubs-what-is-event-hubs.md)
* [Informace o službě Event Hubs pro ekosystém Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Další ukázky v úložišti Event Hubs pro ekosystém Kafka na GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka)
* Použití nástroje [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) ke [streamování událostí z místního systému Kafka do služby Event Hubs s podporou Kafka v cloudu.](event-hubs-kafka-mirror-maker-tutorial.md)
* Zjistěte, jak streamovat do služby Event Hubs s podporou Kafka pomocí [Apache Flinku](event-hubs-kafka-flink-tutorial.md) nebo [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md).
