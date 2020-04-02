---
title: Azure Event Hubs – zpracovat události Apache Kafka
description: 'Kurz: Tento článek ukazuje, jak zpracovat události Kafka, které jsou ingestovány prostřednictvím centra událostí pomocí Azure Stream Analytics'
services: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 4cabd63dc39590a9bf728528f3e7b3aa5d3f6b04
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521761"
---
# <a name="tutorial-process-apache-kafka-for-event-hubs-events-using-stream-analytics"></a>Kurz: Zpracování událostí Apache Kafka pro event huby pomocí analýzy Stream 
Tento článek ukazuje, jak streamovat data do centra událostí a zpracovat je pomocí Azure Stream Analytics. Provede vás následujícími kroky: 

1. Vytvořte obor názvů služby Event Hubs.
2. Vytvořte klienta Kafka, který odesílá zprávy do centra událostí.
3. Vytvořte úlohu Stream Analytics, která kopíruje data z centra událostí do úložiště objektů blob Azure. 

Není nutné měnit klienty protokolu nebo spouštět vlastní clustery při použití koncového bodu Kafka vystavené rozbočovač událostí. Azure Event Hubs podporuje [Apache Kafka verze 1.0](https://kafka.apache.org/10/documentation.html). a výše. 


## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

* Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks).
* [Stáhněte](https://maven.apache.org/download.cgi) a [nainstalujte](https://maven.apache.org/install.html) binární archiv Maven.
* [Git](https://www.git-scm.com/)
* **Účet azure úložiště**. Pokud ho nemáte, [vytvořte si ho,](../storage/common/storage-account-create.md) než budete pokračovat dále. Úloha Stream Analytics v tomto návodu ukládá výstupní data v úložišti objektů blob Azure. 


## <a name="create-an-event-hubs-namespace"></a>Vytvoření oboru názvů služby Event Hubs
Když vytvoříte obor názvů Centra událostí **standardní** vrstvy, koncový bod Kafka pro obor názvů je automaticky povolen. Můžete streamovat události z vašich aplikací, které používají protokol Kafka do centra událostí standardní úrovně. Postupujte podle podrobných pokynů v [centru vytváření událostí pomocí portálu Azure portal](event-hubs-create.md) k vytvoření oboru názvů Centra událostí na úrovni **úrovně.** 

> [!NOTE]
> Event Huby pro Kafku jsou dostupné jenom na **standardních** a **vyhrazených** úrovních. **Základní** úroveň nepodporuje Kafka v event hubech.

## <a name="send-messages-with-kafka-in-event-hubs"></a>Posílání zpráv pomocí Kafky v Event Hubs

1. Klonujte [centra událostí Azure pro úložiště Kafka](https://github.com/Azure/azure-event-hubs-for-kafka) do vašeho počítače.
2. Přejděte do `azure-event-hubs-for-kafka/quickstart/java/producer`složky: . 
4. Aktualizujte podrobnosti konfigurace `src/main/resources/producer.config`pro výrobce v aplikaci . Zadejte **název** a **připojovací řetězec** pro obor názvů centra **událostí**. 

    ```xml
    bootstrap.servers={EVENT HUB NAMESPACE}.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING for EVENT HUB NAMESPACE}";
    ```

5. Přejděte `azure-event-hubs-for-kafka/quickstart/java/producer/src/main/java/`na soubor **TestDataReporter.java** v editoru podle vašeho výběru. 
6. Zakomentujte následující řádek kódu:

    ```java
                //final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "Test Data " + i);
    ```
3. Místo kódu s komentáři přidejte následující řádek kódu: 

    ```java
                final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "{ \"eventData\": \"Test Data " + i + "\" }");            
    ```

    Tento kód odesílá data událostí ve formátu **JSON.** Při konfiguraci vstupu pro úlohu Stream Analytics zadáte JSON jako formát pro vstupní data. 
7. **Spusťte producenta** a streamujte do centra událostí. V počítači se systémem Windows přepněte při použití **příkazového řádku Node.js**do `azure-event-hubs-for-kafka/quickstart/java/producer` složky před spuštěním těchto příkazů. 
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```

## <a name="verify-that-event-hub-receives-the-data"></a>Ověřte, zda centrum událostí přijímá data

1. V části **ENTITY**Vyberte **Centra událostí** . Potvrďte, že se vám zobrazí centrum událostí s názvem **Test**. 

    ![Centrum událostí – test](./media/event-hubs-kafka-stream-analytics/test-event-hub.png)
2. Zkontrolujte, zda se do centra událostí zobrazují zprávy. 

    ![Centrum událostí – zprávy](./media/event-hubs-kafka-stream-analytics/confirm-event-hub-messages.png)

## <a name="process-event-data-using-a-stream-analytics-job"></a>Zpracování dat událostí pomocí úlohy Stream Analytics
V této části vytvoříte úlohu Azure Stream Analytics. Klient Kafka odesílá události do centra událostí. Vytvoříte úlohu Stream Analytics, která přebírá data událostí jako vstup a výstupy do úložiště objektů blob Azure. Pokud nemáte **účet Azure Storage**, [vytvořte si ho](../storage/common/storage-account-create.md).

Dotaz v úloze Stream Analytics prochází daty bez provedení jakékoli analýzy. Můžete vytvořit dotaz, který transformuje vstupní data k vytvoření výstupních dat v jiném formátu nebo se získanými přehledy.  

### <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics 

1. Vyberte **+ Vytvořit prostředek** na webu Azure [Portal](https://portal.azure.com).
2. V nabídce **Azure Marketplace** vyberte **Analytics** a vyberte **Úloha Stream Analytics**. 
3. Na stránce **New Stream Analytics** proveďte následující akce: 
    1. Zadejte **název** úlohy. 
    2. Vyberte **předplatné**.
    3. Vyberte **Vytvořit nový** pro **skupinu prostředků** a zadejte název. Můžete také **použít existující skupinu** prostředků. 
    4. Vyberte **umístění** úlohy.
    5. Chcete-li úlohu vytvořit, vyberte **vytvořit.** 

        ![Nová práce Stream Analytics](./media/event-hubs-kafka-stream-analytics/new-stream-analytics-job.png)

### <a name="configure-job-input"></a>Konfigurace vstupu úlohy

1. V oznámení vyberte **Přejít na zdroj** a podívejte se na stránku **úlohy Stream Analytics.** 
2. V levé nabídce vyberte **Vstupy** v části **TOPOLOGIE ÚLOHY.**
3. Vyberte **Přidat vstup datového proudu**a pak vyberte Centrum **událostí**. 

    ![Přidání centra událostí jako vstupu](./media/event-hubs-kafka-stream-analytics/select-event-hub-input.png)
4. Na stránce **konfigurace vstupu centra událostí** proveďte následující akce: 

    1. Zadejte **alias** pro vstup. 
    2. Vyberte své **předplatné Azure**.
    3. Vyberte **obor názvů centra událostí,** který jste vytvořili dříve. 
    4. Vyberte **test** pro **centrum událostí**. 
    5. Vyberte **Uložit**. 

        ![Konfigurace vstupu centra událostí](./media/event-hubs-kafka-stream-analytics/event-hub-input-configuration.png)

### <a name="configure-job-output"></a>Konfigurace výstupu úlohy 

1. V nabídce vyberte **Výstupy** v části **TOPOLOGIE ÚLOHY.** 
2. Vybrat **+ Přidat** na panelu nástrojů a vybrat úložiště objektů **Blob**
3. Na stránce nastavení výstupu úložiště objektů blob proveďte následující akce: 
    1. Zadejte **alias** pro výstup. 
    2. Vyberte **předplatné**Azure . 
    3. Vyberte **svůj účet Azure Storage**. 
    4. Zadejte **název kontejneru,** který ukládá výstupní data z dotazu Stream Analytics.
    5. Vyberte **Uložit**.

        ![Konfigurace výstupu úložiště objektů blob](./media/event-hubs-kafka-stream-analytics/output-blob-settings.png)
 

### <a name="define-a-query"></a>Definování dotazu
Po dokončení nastavení úlohy Stream Analytics pro čtení příchozího datového streamu je dalším krokem vytvoření transformace, která analyzuje data v reálném čase. Transformační dotaz definujete pomocí jazyka [Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). V tomto návodu definujete dotaz, který prochází daty bez provedení jakékoli transformace.

1. Vyberte **dotaz**.
2. V okně dotazu `[YourOutputAlias]` nahraďte výstupním aliasem, který jste vytvořili dříve.
3. Nahraďte `[YourInputAlias]` vstupním aliasem, který jste vytvořili dříve. 
4. Na panelu nástrojů vyberte **Uložit**. 

    ![Dotaz](./media/event-hubs-kafka-stream-analytics/query.png)


### <a name="run-the-stream-analytics-job"></a>Spuštění úlohy Stream Analytics

1. V levé nabídce vyberte **Přehled.** 
2. Vyberte **Spustit**. 

    ![Nabídka Start](./media/event-hubs-kafka-stream-analytics/start-menu.png)
1. Na stránce **Spustit úlohu** vyberte **Spustit**. 

    ![Stránka Počáteční úloha](./media/event-hubs-kafka-stream-analytics/start-job-page.png)
1. Počkejte, až se stav úlohy změní z **Počáteční** na **spuštěný**. 

    ![Stav úlohy - spuštěný](./media/event-hubs-kafka-stream-analytics/running.png)

## <a name="test-the-scenario"></a>Otestujte scénář
1. Spusťte **výrobce Kafka** znovu odeslat události do centra událostí. 

    ```shell
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
1. Zkontrolujte, že se **výstupní data** generují v **úložišti objektů blob Azure**. V kontejneru se zobrazí soubor JSON se 100 řádky, které vypadají jako následující ukázkové řádky: 

    ```
    {"eventData":"Test Data 0","EventProcessedUtcTime":"2018-08-30T03:27:23.1592910Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 1","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 2","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    ```

    Úloha Azure Stream Analytics přijala vstupní data z centra událostí a uložila je do úložiště objektů blob Azure v tomto scénáři. 



## <a name="next-steps"></a>Další kroky
V tomto článku jste se dozvěděli, jak streamovat do centra událostí bez změny klientů protokolu nebo spuštění vlastních clusterů. Další informace o službě Event Hubs a Event Hubs pro ekosystém Kafka najdete v následujícím tématu:  

- [Informace o službě Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs pro Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Vytvoření centra událostí](event-hubs-create.md)
- [Streamování do služby Event Hubs z aplikací Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Zrcadlení zprostředkovatele Kafka v centru událostí](event-hubs-kafka-mirror-maker-tutorial.md)
- [Připojení Apache Sparku k centru událostí](event-hubs-kafka-spark-tutorial.md)
- [Připojení Apache Flinku k centru událostí](event-hubs-kafka-flink-tutorial.md)
- [Integrace aplikace Kafka Connect s centrem událostí](event-hubs-kafka-connect-tutorial.md)
- [Připojení Streamů Akka k centru událostí](event-hubs-kafka-akka-streams-tutorial.md)
- [Prozkoumejte ukázky na našem GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka) 
