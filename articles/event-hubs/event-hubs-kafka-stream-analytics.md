---
title: 'Kurz: zpracování událostí Apache Kafka pomocí Stream Analytics – Azure Event Hubs'
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
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 7801b3252ab13df1f92e7aa5e0eba071195cb76c
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720618"
---
# <a name="tutorial-process-apache-kafka-for-event-hubs-events-using-stream-analytics"></a>Kurz: proces Apache Kafka pro události Event Hubs pomocí Stream Analytics 
Tento článek ukazuje, jak streamovat data do Event Hubs s povoleným Kafka a zpracovávat je pomocí Azure Stream Analytics. Provede vás následujícími kroky: 

1. Vytvořte Kafka s povoleným oborem názvů Event Hubs.
2. Vytvořte klienta Kafka, který odesílá zprávy do centra událostí.
3. Vytvořte úlohu Stream Analytics, která kopíruje data z centra událostí do úložiště objektů BLOB v Azure. 

Pokud používáte koncový bod Kafka vystavený centrem událostí, nemusíte měnit klienty protokolu ani spouštět vlastní clustery. Azure Event Hubs podporuje [Apache Kafka verze 1.0](https://kafka.apache.org/10/documentation.html). a vyšší. 


## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

* Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks).
* [Stáhněte](https://maven.apache.org/download.cgi) a [nainstalujte](https://maven.apache.org/install.html) binární archiv Maven.
* [Git](https://www.git-scm.com/)
* **Účet Azure Storage**. Pokud ho ještě nemáte, [vytvořte ho](../storage/common/storage-quickstart-create-account.md) ještě předtím, než budete pokračovat. Úloha Stream Analytics v tomto návodu ukládá výstupní data do úložiště objektů BLOB v Azure. 


## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Vytvoření oboru názvů služby Event Hubs s podporou Kafka

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a v levém horním rohu obrazovky klikněte na **vytvořit prostředek** .
2. Vyhledejte **Event Hubs** a vyberte možnosti, které jsou tady uvedené:
    
    ![Vyhledání služby Event Hubs na portálu](./media/event-hubs-kafka-stream-analytics/select-event-hubs.png) 
3. Na stránce **Event Hubs** vyberte **vytvořit**.
4. Na stránce **vytvořit obor názvů** proveďte následující akce: 
    1. Zadejte jedinečný **název** oboru názvů. 
    2. Vyberte **cenovou úroveň**. 
    3. Vyberte **Povolit Kafka**. Tento krok je **důležitým** krokem. 
    4. Vyberte své **předplatné** , ve kterém chcete vytvořit obor názvů centra událostí. 
    5. Vytvořte novou **skupinu prostředků** nebo vyberte existující skupinu prostředků. 
    6. Vyberte **umístění**. 
    7. Klikněte na **Vytvořit**.
    
        ![Vytvoření oboru názvů](./media/event-hubs-kafka-stream-analytics/create-event-hub-namespace-page.png) 
4. V **oznamovací zprávě**vyberte **název skupiny prostředků**. 

    ![Vytvoření oboru názvů](./media/event-hubs-kafka-stream-analytics/creation-station-message.png)
1. Vyberte **obor názvů centra událostí** ve skupině prostředků. 
2. Po vytvoření oboru názvů vyberte v části **Nastavení** **zásady sdíleného přístupu** .

    ![Kliknutí na Zásady sdíleného přístupu](./media/event-hubs-kafka-stream-analytics/shared-access-policies.png)
5. Můžete zvolit výchozí zásadu **RootManageSharedAccessKey** nebo přidat novou. Klikněte na název zásady a zkopírujte **připojovací řetězec**. Připojovací řetězec můžete použít ke konfiguraci klienta Kafka. 
    
    ![Výběr zásady](./media/event-hubs-kafka-stream-analytics/connection-string.png)  

Teď můžete ze svých aplikací používajících protokol Kafka streamovat události do služby Event Hubs.

## <a name="send-messages-with-kafka-in-event-hubs"></a>Posílání zpráv pomocí Kafka v Event Hubs

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
6. Odkomentujte následující řádek kódu:

    ```java
                //final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "Test Data " + i);
    ```
3. Přidejte následující řádek kódu místo kódu s komentářem: 

    ```java
                final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "{ \"eventData\": \"Test Data " + i + "\" }");            
    ```

    Tento kód odesílá data události ve formátu **JSON** . Když nakonfigurujete vstup pro Stream Analytics úlohu, jako formát vstupních dat zadáte JSON. 
7. **Spusťte producenta** a stream do Event Hubs s podporou Kafka. V počítači s Windows při použití **příkazového řádku Node. js**přepněte do složky `azure-event-hubs-for-kafka/quickstart/java/producer` před spuštěním těchto příkazů. 
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```

## <a name="verify-that-event-hub-receives-the-data"></a>Ověřte, že centrum událostí přijímá data.

1. V části **entity**vyberte **Event Hubs** . Ověřte, že se zobrazuje centrum událostí s názvem **test**. 

    ![Centrum událostí – test](./media/event-hubs-kafka-stream-analytics/test-event-hub.png)
2. Ověřte, že se zobrazují zprávy přicházející do centra událostí. 

    ![Centrum událostí – zprávy](./media/event-hubs-kafka-stream-analytics/confirm-event-hub-messages.png)

## <a name="process-event-data-using-a-stream-analytics-job"></a>Zpracování dat událostí pomocí Stream Analytics úlohy
V této části vytvoříte úlohu Azure Stream Analytics. Klient Kafka odesílá události do centra událostí. Vytvoříte úlohu Stream Analytics, která jako vstup převezme data událostí, a zapíše ji do úložiště objektů BLOB v Azure. Pokud **účet Azure Storage**nemáte, [vytvořte ho](../storage/common/storage-quickstart-create-account.md).

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
3. Vyberte **Přidat vstup streamu**a pak vyberte **centrum událostí**. 

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
Po dokončení nastavení úlohy Stream Analytics pro čtení příchozího datového streamu je dalším krokem vytvoření transformace, která analyzuje data v reálném čase. Transformační dotaz definujete pomocí jazyka [Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). V tomto návodu definujete dotaz, který projde daty bez provedení jakékoli transformace.

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

    ![Stránka pro spuštění úlohy](./media/event-hubs-kafka-stream-analytics/start-job-page.png)
1. Počkejte, až se stav úlohy změní z **počáteční** na **spuštěno**. 

    ![Stav úlohy – spuštěno](./media/event-hubs-kafka-stream-analytics/running.png)

## <a name="test-the-scenario"></a>Testování scénáře
1. Znovu spusťte **producenta Kafka** , abyste mohli odesílat události do centra událostí. 

    ```shell
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
1. Ověřte, že se v **úložišti objektů BLOB v Azure**vygenerovala **výstupní data** . V kontejneru se zobrazí soubor JSON s 100 řádky, které vypadají jako následující ukázkové řádky: 

    ```
    {"eventData":"Test Data 0","EventProcessedUtcTime":"2018-08-30T03:27:23.1592910Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 1","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 2","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    ```

    Úloha Azure Stream Analytics přijala vstupní data z centra událostí a uloží je v tomto scénáři do úložiště objektů BLOB v Azure. 



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
