---
title: Event Hubs Azure – procesní události Apache Kafka
description: 'Kurz: v tomto článku se dozvíte, jak zpracovávat události Kafka, které se ingestují prostřednictvím centra událostí pomocí Azure Stream Analytics'
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
ms.openlocfilehash: d7b060a2b35ca41bf87b69be706284174d7b1012
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587154"
---
# <a name="tutorial-process-apache-kafka-for-event-hubs-events-using-stream-analytics"></a>Kurz: proces Apache Kafka pro události Event Hubs pomocí Stream Analytics 
Tento článek popisuje, jak Streamovat data do služby Event Hubs povolené Kafka a zpracovat je s Azure Stream Analytics. Provede vás provede následujícími kroky: 

1. Vytvoření oboru názvů Event Hubs povolené Kafka.
2. Vytvoření klienta Kafka, která odesílá zprávy do centra událostí.
3. Vytvořte úlohu Stream Analytics, která kopíruje data z centra událostí do služby Azure blob storage. 

Není potřeba změnit klienty protokolu nebo spustit vlastní clusterů při použití koncového bodu Kafka vystavené centra událostí. Azure Event Hubs podporuje [Apache Kafka verze 1.0](https://kafka.apache.org/10/documentation.html). a vyšší. 


## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

* Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks).
* [Stáhněte](https://maven.apache.org/download.cgi) a [nainstalujte](https://maven.apache.org/install.html) binární archiv Maven.
* [Git](https://www.git-scm.com/)
* **Účet Azure Storage**. Pokud ho ještě nemáte, [vytvořte ho](../storage/common/storage-account-create.md) ještě předtím, než budete pokračovat. Úlohy Stream Analytics v tomto názorném postupu se uloží výstupní data ve službě Azure blob storage. 


## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Vytvoření oboru názvů služby Event Hubs s podporou Kafka
Když vytvoříte obor názvů úrovně **standard** Event Hubs, je automaticky povolen koncový bod Kafka pro obor názvů. Můžete streamovat události z vašich aplikací, které používají protokol Kafka, do úrovně Standard Event Hubs. Postupujte podle podrobných pokynů v tématu [vytvoření centra událostí pomocí Azure Portal](event-hubs-create.md) k vytvoření oboru názvů Event Hubs úrovně **Standard** . 

> [!NOTE]
> Event Hubs pro Kafka je k dispozici pouze na **Standard** a **vyhrazené** úrovni. Úroveň **Basic** nepodporuje Kafka na Event Hubs.

## <a name="send-messages-with-kafka-in-event-hubs"></a>Odesílání zpráv s využitím Kafka ve službě Event Hubs

1. Naklonujte [Azure Event Hubs pro úložiště Kafka](https://github.com/Azure/azure-event-hubs-for-kafka) do vašeho počítače.
2. Přejděte do složky: `azure-event-hubs-for-kafka/quickstart/java/producer`. 
4. Aktualizujte podrobnosti o konfiguraci pro producenta v `src/main/resources/producer.config`. Zadejte **název** a **připojovací řetězec** pro **obor názvů centra událostí**. 

    ```xml
    bootstrap.servers={EVENT HUB NAMESPACE}.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING for EVENT HUB NAMESPACE}";
    ```

5. Přejděte na `azure-event-hubs-for-kafka/quickstart/java/producer/src/main/java/com/example/app`a otevřete soubor **TestDataReporter. Java** v editoru dle vašeho výběru. 
6. Okomentujte následující řádek kódu:

    ```java
                //final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "Test Data " + i);
    ```
3. Přidejte následující řádek kódu místo komentářem kódu: 

    ```java
                final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "{ \"eventData\": \"Test Data " + i + "\" }");            
    ```

    Tento kód odesílá data události ve formátu **JSON** . Když konfigurujete vstup pro úlohu Stream Analytics, zadáte jako formát pro vstupní data JSON. 
7. **Spusťte producenta** a stream do Event Hubs s podporou Kafka. V počítači s Windows při použití **příkazového řádku Node. js**přepněte do složky `azure-event-hubs-for-kafka/quickstart/java/producer` před spuštěním těchto příkazů. 
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```

## <a name="verify-that-event-hub-receives-the-data"></a>Ověřte, že Centrum událostí přijme data

1. V části **entity**vyberte **Event Hubs** . Ověřte, že se zobrazuje centrum událostí s názvem **test**. 

    ![Centrum událostí – testování](./media/event-hubs-kafka-stream-analytics/test-event-hub.png)
2. Ověřte, že se zprávy přicházející do centra událostí. 

    ![Centrum událostí – zprávy](./media/event-hubs-kafka-stream-analytics/confirm-event-hub-messages.png)

## <a name="process-event-data-using-a-stream-analytics-job"></a>Zpracování dat o událostech prostřednictvím úlohu Stream Analytics
V této části vytvoříte úlohu Azure Stream Analytics. Kafka klient odesílá události do centra událostí. Vytvoření úlohy Stream Analytics, která přijímá data událostí jako vstup a výstup do služby Azure blob storage. Pokud **účet Azure Storage**nemáte, [vytvořte ho](../storage/common/storage-account-create.md).

Dotaz v úloze Stream Analytics data prochází bez provádění jakékoli analýz. Můžete vytvořit dotaz, který transformuje vstupní data a generuje výstupní data v jiném formátu, nebo s získáte přehledy.  

### <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics 

1. V [Azure Portal](https://portal.azure.com)vyberte **+ vytvořit prostředek** .
2. V nabídce **Azure Marketplace** vyberte **Analytics** a vyberte **Stream Analytics úloha**. 
3. Na stránce **nový Stream Analytics** proveďte následující akce: 
    1. Zadejte **název** úlohy. 
    2. Vyberte své **předplatné**.
    3. Pro **skupinu prostředků** vyberte **vytvořit novou** a zadejte název. Můžete také **použít existující** skupinu prostředků. 
    4. Vyberte **umístění** pro úlohu.
    5. Vyberte **vytvořit** a vytvořte úlohu. 

        ![Nová úloha Stream Analytics](./media/event-hubs-kafka-stream-analytics/new-stream-analytics-job.png)

### <a name="configure-job-input"></a>Konfigurace vstupu úlohy

1. V oznamovací zprávě vyberte **Přejít k prostředku** a zobrazte stránku **Stream Analytics úlohy** . 
2. V nabídce vlevo vyberte **vstupy** v části **topologie úloh** .
3. Vyberte **Přidat vstup streamu**a pak vyberte **centrum událostí**. 

    ![Přidat jako vstup Centrum událostí](./media/event-hubs-kafka-stream-analytics/select-event-hub-input.png)
4. Na stránce konfigurace **vstupu centra událostí** proveďte následující akce: 

    1. Zadejte **alias** pro vstup. 
    2. Vyberte své **předplatné Azure**.
    3. Vyberte **obor názvů centra událostí** , který jste vytvořili dříve. 
    4. Vyberte **test** **centra událostí**. 
    5. Vyberte **Save** (Uložit). 

        ![Konfigurace vstupu centra událostí](./media/event-hubs-kafka-stream-analytics/event-hub-input-configuration.png)

### <a name="configure-job-output"></a>Konfigurace výstupu úlohy 

1. V nabídce vyberte **výstupy** v části **topologie úloh** . 
2. Na panelu nástrojů vyberte **+ Přidat** a vyberte **úložiště objektů BLOB** .
3. Na stránce nastavení objektu Blob úložiště výstup proveďte následující akce: 
    1. Zadejte **alias** pro výstup. 
    2. Vyberte své **předplatné** Azure. 
    3. Vyberte **účet Azure Storage**. 
    4. Zadejte **název kontejneru** , ve kterém jsou uložena výstupní data z Stream Analytics dotazu.
    5. Vyberte **Save** (Uložit).

        ![Konfigurace výstupu úložiště objektů BLOB](./media/event-hubs-kafka-stream-analytics/output-blob-settings.png)
 

### <a name="define-a-query"></a>Definování dotazu na
Po dokončení nastavení úlohy Stream Analytics pro čtení příchozího datového streamu je dalším krokem vytvoření transformace, která analyzuje data v reálném čase. Transformační dotaz definujete pomocí jazyka [Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). V tomto podrobném návodu definujete dotaz, který prochází data bez provedení jakékoli transformace.

1. Vyberte **dotaz**.
2. V okně dotazu nahraďte `[YourOutputAlias]` aliasem výstupu, který jste vytvořili dříve.
3. Nahraďte `[YourInputAlias]` vstupním aliasem, který jste vytvořili dříve. 
4. Na panelu nástrojů vyberte **Uložit**. 

    ![Dotaz](./media/event-hubs-kafka-stream-analytics/query.png)


### <a name="run-the-stream-analytics-job"></a>Spuštění úlohy Stream Analytics

1. V nabídce vlevo vyberte **Přehled** . 
2. Vyberte **Spustit**. 

    ![Nabídka Start](./media/event-hubs-kafka-stream-analytics/start-menu.png)
1. Na stránce **Spustit úlohu** vyberte **Spustit**. 

    ![Domovská stránka projektu](./media/event-hubs-kafka-stream-analytics/start-job-page.png)
1. Počkejte, až se stav úlohy změní z **počáteční** na **spuštěno**. 

    ![Stav úlohy - s](./media/event-hubs-kafka-stream-analytics/running.png)

## <a name="test-the-scenario"></a>Otestování tohoto scénáře
1. Znovu spusťte **producenta Kafka** , abyste mohli odesílat události do centra událostí. 

    ```shell
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
1. Ověřte, že se v **úložišti objektů BLOB v Azure**vygenerovala **výstupní data** . Zobrazí soubor JSON v kontejneru s 100 řádků, které vypadají podobně jako následující řádky vzorku: 

    ```
    {"eventData":"Test Data 0","EventProcessedUtcTime":"2018-08-30T03:27:23.1592910Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 1","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 2","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    ```

    Úlohy Azure Stream Analytics vstupních dat přijatých z centra událostí a uložených ve službě Azure blob storage v tomto scénáři. 



## <a name="next-steps"></a>Další kroky
V tomto článku jste zjistili, jak streamovat do služby Event Hubs s podporou Kafka, aniž byste museli měnit klienty protokolů nebo provozovat vlastní clustery. Další informace o službě Event Hubs a Event Hubs pro ekosystém Kafka najdete v následujícím tématu:  

- [Informace o službě Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs pro Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Vytvoření služby Event Hubs s podporou Kafka](event-hubs-create-kafka-enabled.md)
- [Streamování do služby Event Hubs z aplikací Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Zrcadlení zprostředkovatele Kafka v centru událostí s podporou Kafka](event-hubs-kafka-mirror-maker-tutorial.md)
- [Připojení Apache Sparku k centru událostí s podporou Kafka](event-hubs-kafka-spark-tutorial.md)
- [Připojení Apache Flinku k centru událostí s podporou Kafka](event-hubs-kafka-flink-tutorial.md)
- [Integrace připojení Kafka s centrem událostí s podporou Kafka](event-hubs-kafka-connect-tutorial.md)
- [Připojení Akka Streams k centru událostí s podporou Kafka](event-hubs-kafka-akka-streams-tutorial.md)
- [Prozkoumejte ukázky na našem GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka) 
