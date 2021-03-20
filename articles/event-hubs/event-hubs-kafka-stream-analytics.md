---
title: Event Hubs Azure – procesní události Apache Kafka
description: 'Kurz: v tomto článku se dozvíte, jak zpracovávat události Kafka, které se ingestují prostřednictvím centra událostí pomocí Azure Stream Analytics'
ms.topic: tutorial
ms.date: 06/23/2020
ms.openlocfilehash: 328537eebd05391fc4c8138395a9c10f0a5d072f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92319382"
---
# <a name="tutorial-process-apache-kafka-for-event-hubs-events-using-stream-analytics"></a>Kurz: proces Apache Kafka pro události Event Hubs pomocí Stream Analytics 
Tento článek ukazuje, jak streamovat data do Event Hubs a zpracovávat je pomocí Azure Stream Analytics. Provede vás následujícími kroky: 

1. Vytvořte obor názvů služby Event Hubs.
2. Vytvořte klienta Kafka, který odesílá zprávy do centra událostí.
3. Vytvořte úlohu Stream Analytics, která kopíruje data z centra událostí do úložiště objektů BLOB v Azure. 

Pokud používáte koncový bod Kafka vystavený centrem událostí, nemusíte měnit klienty protokolu ani spouštět vlastní clustery. Azure Event Hubs podporuje [Apache Kafka verze 1.0](https://kafka.apache.org/10/documentation.html). a vyšší. 


## <a name="prerequisites"></a>Předpoklady

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

* Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Java Development Kit (JDK) 1.7+](/azure/developer/java/fundamentals/java-jdk-long-term-support).
* [Stáhněte](https://maven.apache.org/download.cgi) a [nainstalujte](https://maven.apache.org/install.html) binární archiv Maven.
* [Git](https://www.git-scm.com/)
* **Účet Azure Storage**. Pokud ho ještě nemáte, [vytvořte ho](../storage/common/storage-account-create.md) ještě předtím, než budete pokračovat. Úloha Stream Analytics v tomto návodu ukládá výstupní data do úložiště objektů BLOB v Azure. 


## <a name="create-an-event-hubs-namespace"></a>Vytvoření oboru názvů služby Event Hubs
Když vytvoříte obor názvů úrovně **standard** Event Hubs, je automaticky povolen koncový bod Kafka pro obor názvů. Můžete streamovat události z vašich aplikací, které používají protokol Kafka, do úrovně Standard Event Hubs. Postupujte podle podrobných pokynů v tématu [vytvoření centra událostí pomocí Azure Portal](event-hubs-create.md) k vytvoření oboru názvů Event Hubs úrovně **Standard** . 

> [!NOTE]
> Event Hubs pro Kafka je k dispozici pouze na **Standard** a **vyhrazené** úrovni. Úroveň **Basic** nepodporuje Kafka na Event Hubs.

## <a name="send-messages-with-kafka-in-event-hubs"></a>Posílání zpráv pomocí Kafka v Event Hubs

1. Naklonujte [Azure Event Hubs pro úložiště Kafka](https://github.com/Azure/azure-event-hubs-for-kafka) do vašeho počítače.
2. Přejděte do složky: `azure-event-hubs-for-kafka/quickstart/java/producer` . 
4. Aktualizujte podrobnosti o konfiguraci pro producenta v `src/main/resources/producer.config` . Zadejte **název** a **připojovací řetězec** pro **obor názvů centra událostí**. 

    ```xml
    bootstrap.servers={EVENT HUB NAMESPACE}.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING for EVENT HUB NAMESPACE}";
    ```

5. Přejděte na `azure-event-hubs-for-kafka/quickstart/java/producer/src/main/java/` a otevřete soubor **TestDataReporter. Java** v editoru dle vašeho výběru. 
6. Odkomentujte následující řádek kódu:

    ```java
                //final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "Test Data " + i);
    ```
3. Přidejte následující řádek kódu místo kódu s komentářem: 

    ```java
                final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "{ \"eventData\": \"Test Data " + i + "\" }");            
    ```

    Tento kód odesílá data události ve formátu **JSON** . Když nakonfigurujete vstup pro Stream Analytics úlohu, jako formát vstupních dat zadáte JSON. 
7. **Spusťte producenta** a stream do Event Hubs. V počítači s Windows při použití **příkazového řádkuNode.js** přepněte do `azure-event-hubs-for-kafka/quickstart/java/producer` složky před spuštěním těchto příkazů. 
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```

## <a name="verify-that-event-hub-receives-the-data"></a>Ověřte, že centrum událostí přijímá data.

1. V části **entity** vyberte **Event Hubs** . Ověřte, že se zobrazuje centrum událostí s názvem **test**. 

    ![Centrum událostí – test](./media/event-hubs-kafka-stream-analytics/test-event-hub.png)
2. Ověřte, že se zobrazují zprávy přicházející do centra událostí. 

    ![Centrum událostí – zprávy](./media/event-hubs-kafka-stream-analytics/confirm-event-hub-messages.png)

## <a name="process-event-data-using-a-stream-analytics-job"></a>Zpracování dat událostí pomocí Stream Analytics úlohy
V této části vytvoříte úlohu Azure Stream Analytics. Klient Kafka odesílá události do centra událostí. Vytvoříte úlohu Stream Analytics, která jako vstup převezme data událostí, a zapíše ji do úložiště objektů BLOB v Azure. Pokud **účet Azure Storage** nemáte, [vytvořte ho](../storage/common/storage-account-create.md).

Dotaz v Stream Analytics úloze projde daty bez provedení jakýchkoli analýz. Můžete vytvořit dotaz, který transformuje vstupní data k vytvoření výstupních dat v jiném formátu nebo pomocí zkušeností s přehledem.  

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
3. Vyberte **Přidat vstup streamu** a pak vyberte **centrum událostí**. 

    ![Přidat centrum událostí jako vstup](./media/event-hubs-kafka-stream-analytics/select-event-hub-input.png)
4. Na stránce konfigurace **vstupu centra událostí** proveďte následující akce: 

    1. Zadejte **alias** pro vstup. 
    2. Vyberte své **předplatné Azure**.
    3. Vyberte **obor názvů centra událostí** , který jste vytvořili dříve. 
    4. Vyberte **test** **centra událostí**. 
    5. Vyberte **Uložit**. 

        ![Konfigurace vstupu centra událostí](./media/event-hubs-kafka-stream-analytics/event-hub-input-configuration.png)

### <a name="configure-job-output"></a>Konfigurace výstupu úlohy 

1. V nabídce vyberte **výstupy** v části **topologie úloh** . 
2. Na panelu nástrojů vyberte **+ Přidat** a vyberte **úložiště objektů BLOB** .
3. Na stránce nastavení výstupu služby Blob Storage proveďte následující akce: 
    1. Zadejte **alias** pro výstup. 
    2. Vyberte své **předplatné** Azure. 
    3. Vyberte **účet Azure Storage**. 
    4. Zadejte **název kontejneru** , ve kterém jsou uložena výstupní data z Stream Analytics dotazu.
    5. Vyberte **Uložit**.

        ![Konfigurace výstupu Blob Storage](./media/event-hubs-kafka-stream-analytics/output-blob-settings.png)
 

### <a name="define-a-query"></a>Definovat dotaz
Po dokončení nastavení úlohy Stream Analytics pro čtení příchozího datového streamu je dalším krokem vytvoření transformace, která analyzuje data v reálném čase. Transformační dotaz definujete pomocí jazyka [Stream Analytics Query Language](/stream-analytics-query/stream-analytics-query-language-reference). V tomto návodu definujete dotaz, který projde daty bez provedení jakékoli transformace.

1. Vyberte **dotaz**.
2. V okně dotazu nahraďte `[YourOutputAlias]` aliasem výstupu, který jste vytvořili dříve.
3. Nahraďte `[YourInputAlias]` vstupním aliasem, který jste vytvořili dříve. 
4. Na panelu nástrojů vyberte **Uložit**. 

    ![Snímek obrazovky znázorňuje okno dotazu s hodnotami pro vstupní a výstupní proměnné.](./media/event-hubs-kafka-stream-analytics/query.png)


### <a name="run-the-stream-analytics-job"></a>Spuštění úlohy Stream Analytics

1. V nabídce vlevo vyberte **Přehled** . 
2. Vyberte **Spustit**. 

    ![Nabídka Start](./media/event-hubs-kafka-stream-analytics/start-menu.png)
1. Na stránce **Spustit úlohu** vyberte **Spustit**. 

    ![Stránka pro spuštění úlohy](./media/event-hubs-kafka-stream-analytics/start-job-page.png)
1. Počkejte, až se stav úlohy změní z **počáteční** na **spuštěno**. 

    ![Stav úlohy – spuštěno](./media/event-hubs-kafka-stream-analytics/running.png)

## <a name="test-the-scenario"></a>Testování scénáře
1. Znovu spusťte **producenta Kafka** , abyste mohli odesílat události do centra událostí. 

    ```shell
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
1. Ověřte, že se v **úložišti objektů BLOB v Azure** vygenerovala **výstupní data** . V kontejneru se zobrazí soubor JSON s 100 řádky, které vypadají jako následující ukázkové řádky: 

    ```
    {"eventData":"Test Data 0","EventProcessedUtcTime":"2018-08-30T03:27:23.1592910Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 1","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 2","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    ```

    Úloha Azure Stream Analytics přijala vstupní data z centra událostí a uloží je v tomto scénáři do úložiště objektů BLOB v Azure. 



## <a name="next-steps"></a>Další kroky
V tomto článku jste zjistili, jak se streamovat do Event Hubs bez změny klientů protokolu nebo spouštění vlastních clusterů. Další informace o Event Hubs pro Apache Kafka najdete v tématu [Apache Kafka příručka pro vývojáře pro Azure Event Hubs](apache-kafka-developer-guide.md).