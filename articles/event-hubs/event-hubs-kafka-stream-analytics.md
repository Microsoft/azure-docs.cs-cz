---
title: Zpracování události Apache Kafka s použitím Stream analytics – služba Azure Event Hubs | Dokumentace Microsoftu
description: Tento článek ukazuje, jak zpracovávat události Kafka, které se ingestuje do služby event hubs s využitím Azure Stream Analytics
services: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: spelluru
ms.openlocfilehash: 7612e9d6444b61210da5d642530d99423220c0a4
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53076836"
---
# <a name="process-apache-kafka-for-event-hubs-events-using-stream-analytics"></a>Zpracování událostí Apache Kafka pro Event Hubs s využitím Stream Analytics 
Tento článek popisuje, jak Streamovat data do služby Event Hubs povolené Kafka a zpracovat je s Azure Stream Analytics. Provede vás provede následujícími kroky: 

1. Vytvoření oboru názvů Event Hubs povolené Kafka.
2. Vytvoření klienta Kafka, která odesílá zprávy do centra událostí.
3. Vytvořte úlohu Stream Analytics, která kopíruje data z centra událostí do služby Azure blob storage. 

Není potřeba změnit klienty protokolu nebo spustit vlastní clusterů při použití koncového bodu Kafka vystavené centra událostí. Azure Event Hubs podporuje [Apache Kafka verze 1.0](https://kafka.apache.org/10/documentation.html). a vyšší. 


## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

* Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks).
* [Stáhněte](http://maven.apache.org/download.cgi) a [nainstalujte](http://maven.apache.org/install.html) binární archiv Maven.
* [Git](https://www.git-scm.com/)
* **Účtu služby Azure Storage**. Pokud ho nemáte, [vytvořit](../storage/common/storage-quickstart-create-account.md) než budete pokračovat. Úlohy Stream Analytics v tomto názorném postupu se uloží výstupní data ve službě Azure blob storage. 


## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Vytvoření oboru názvů služby Event Hubs s podporou Kafka

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a klikněte na tlačítko **vytvořit prostředek** v levém horním rohu obrazovky.
2. Vyhledejte **Event Hubs** a vyberte požadované možnosti je vidět tady:
    
    ![Vyhledání služby Event Hubs na portálu](./media/event-hubs-kafka-stream-analytics/select-event-hubs.png) 
3. Na **Event Hubs** stránce **vytvořit**.
4. Na **vytvořit Namespace** stránce, proveďte následující akce: 
    1. Zadejte jedinečnou **název** pro obor názvů. 
    2. Vyberte **cenovou úroveň**. 
    3. Vyberte **povolit Kafka**. Tento krok je **důležité** kroku. 
    4. Vyberte vaše **předplatné** do kterých chcete obor názvů centra událostí, který se má vytvořit. 
    5. Vytvořte nový **skupiny prostředků** nebo vyberte existující skupinu prostředků. 
    6. Vyberte **umístění**. 
    7. Klikněte na možnost **Vytvořit**.
    
        ![Vytvoření oboru názvů](./media/event-hubs-kafka-stream-analytics/create-event-hub-namespace-page.png) 
4. V **oznámení**, vyberte **název skupiny prostředků**. 

    ![Vytvoření oboru názvů](./media/event-hubs-kafka-stream-analytics/creation-station-message.png)
1. Vyberte **obor názvů centra událostí** ve skupině prostředků. 
2. Po vytvoření oboru názvů vyberte **zásady sdíleného přístupu** pod **nastavení**.

    ![Kliknutí na Zásady sdíleného přístupu](./media/event-hubs-kafka-stream-analytics/shared-access-policies.png)
5. Můžete zvolit výchozí zásadu **RootManageSharedAccessKey** nebo přidat novou. Klikněte na název zásady a zkopírujte **připojovací řetězec**. Připojovací řetězec použijete ke konfiguraci klienta Kafka. 
    
    ![Výběr zásady](./media/event-hubs-kafka-stream-analytics/connection-string.png)  

Teď můžete ze svých aplikací používajících protokol Kafka streamovat události do služby Event Hubs.

## <a name="send-messages-with-kafka-in-event-hubs"></a>Odesílání zpráv s využitím Kafka ve službě Event Hubs

1. Klonování [Azure Event Hubs pro úložiště pro Kafka](https://github.com/Azure/azure-event-hubs-for-kafka) k vašemu počítači.
2. Přejděte do složky: `azure-event-hubs-for-kafka/quickstart/java/producer`. 
4. Aktualizovat podrobnosti o konfiguraci pro výrobce v `src/main/resources/producer.config`. Zadejte **název** a **připojovací řetězec** pro **obor názvů centra událostí**. 

    ```xml
    bootstrap.servers={EVENT HUB NAMESPACE}.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING for EVENT HUB NAMESPACE}";
    ```

5. Přejděte do `azure-event-hubs-for-kafka/quickstart/java/producer/src/main/java/com/example/app`a otevřete **TestDataReporter.java** soubor v editoru podle vašeho výběru. 
6. Okomentujte následující řádek kódu:

    ```java
                //final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "Test Data " + i);
    ```
3. Přidejte následující řádek kódu místo komentářem kódu: 

    ```java
                final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "{ \"eventData\": \"Test Data " + i + "\" }");            
    ```

    Tento kód odesílá data událostí **JSON** formátu. Když konfigurujete vstup pro úlohu Stream Analytics, zadáte jako formát pro vstupní data JSON. 
7. **Spustit producent** a datový proud do služby Event Hubs Kafka povolena. Na počítače s Windows, při použití **příkazový řádek Node.js**, přepněte `azure-event-hubs-for-kafka/quickstart/java/producer` složky, před spuštěním těchto příkazů. 
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```

## <a name="verify-that-event-hub-receives-the-data"></a>Ověřte, že Centrum událostí přijme data

1. Vyberte **služby Event Hubs** pod **entity**. Zkontrolujte, jestli se Centrum událostí s názvem **testování**. 

    ![Centrum událostí – testování](./media/event-hubs-kafka-stream-analytics/test-event-hub.png)
2. Ověřte, že se zprávy přicházející do centra událostí. 

    ![Centrum událostí – zprávy](./media/event-hubs-kafka-stream-analytics/confirm-event-hub-messages.png)

## <a name="process-event-data-using-a-stream-analytics-job"></a>Zpracování dat o událostech prostřednictvím úlohu Stream Analytics
V této části vytvoříte úlohu Azure Stream Analytics. Kafka klient odesílá události do centra událostí. Vytvoření úlohy Stream Analytics, která přijímá data událostí jako vstup a výstup do služby Azure blob storage. Pokud nemáte **účtu služby Azure Storage**, [vytvořit](../storage/common/storage-quickstart-create-account.md).

Dotaz v úloze Stream Analytics data prochází bez provádění jakékoli analýz. Můžete vytvořit dotaz, který transformuje vstupní data a generuje výstupní data v jiném formátu, nebo s získáte přehledy.  

### <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics 

1. Vyberte **+ vytvořit prostředek** v [webu Azure portal](https://portal.azure.com).
2. Vyberte **Analytics** v **Azure Marketplace** nabídky a vybereme **úlohy Stream Analytics**. 
3. Na **nové Stream Analytics** stránce, proveďte následující akce: 
    1. Zadejte **název** pro úlohu. 
    2. Vyberte vaše **předplatné**.
    3. Vyberte **vytvořit nový** pro **skupiny prostředků** a zadejte název. Můžete také **použijte existující** skupinu prostředků. 
    4. Vyberte **umístění** pro úlohu.
    5. Vyberte **vytvořit** vytvořit příslušnou úlohu. 

        ![Nová úloha Stream Analytics](./media/event-hubs-kafka-stream-analytics/new-stream-analytics-job.png)

### <a name="configure-job-input"></a>Konfigurace vstupu úlohy

1. Ve zprávě oznámení vyberte ** Přejít k prostředku ** Pokud chcete zobrazit **úlohy Stream Analytics** stránky. 
2. Vyberte **vstupy** v **TOPOLOGIE úlohy** části v nabídce vlevo.
3. Vyberte **přidat vstup streamu**a pak vyberte **centra událostí**. 

    ![Přidat jako vstup Centrum událostí](./media/event-hubs-kafka-stream-analytics/select-event-hub-input.png)
4. Na **vstup Centrum událostí** konfigurační stránce, proveďte následující akce: 

    1. Zadejte **alias** pro vstup. 
    2. Vyberte své **předplatné Azure**.
    3. Vyberte **obor názvů centra událostí** vytvořili dříve. 
    4. Vyberte **testování** pro **centra událostí**. 
    5. Vyberte **Uložit**. 

        ![Konfigurace vstupu centra událostí](./media/event-hubs-kafka-stream-analytics/event-hub-input-configuration.png)

### <a name="configure-job-output"></a>Konfigurace výstupu úlohy 

1. Vyberte **výstupy** v **TOPOLOGIE úlohy** části v nabídce. 
2. Vyberte **+ přidat** na panelu nástrojů a vyberte **úložiště objektů Blob**
3. Na stránce nastavení objektu Blob úložiště výstup proveďte následující akce: 
    1. Zadejte **alias** pro výstup. 
    2. Vyberte své **předplatné** Azure. 
    3. Vyberte vaše **účtu služby Azure Storage**. 
    4. Zadejte **název kontejneru** , který uloží výstupní data z dotazu Stream Analytics.
    5. Vyberte **Uložit**.

        ![Konfigurace výstupu úložiště objektů BLOB](./media/event-hubs-kafka-stream-analytics/output-blob-settings.png)
 

### <a name="define-a-query"></a>Definování dotazu na
Po dokončení nastavení úlohy Stream Analytics pro čtení příchozího datového streamu je dalším krokem vytvoření transformace, která analyzuje data v reálném čase. Transformační dotaz definujete pomocí jazyka [Stream Analytics Query Language](https://msdn.microsoft.com/library/dn834998.aspx). V tomto podrobném návodu definujete dotaz, který prochází data bez provedení jakékoli transformace.

1. Vyberte **dotazu**.
2. V okně dotazu nahraďte `[YourOutputAlias]` se alias pro výstup jste vytvořili dříve.
3. Nahraďte `[YourInputAlias]` s vstupní alias, který jste vytvořili dříve. 
4. Na panelu nástrojů vyberte **Uložit**. 

    ![Dotaz](./media/event-hubs-kafka-stream-analytics/query.png)


### <a name="run-the-stream-analytics-job"></a>Spuštění úlohy Stream Analytics

1. Vyberte **přehled** v nabídce vlevo. 
2. Vyberte **Start**. 

    ![Nabídka Start](./media/event-hubs-kafka-stream-analytics/start-menu.png)
1. Na **spuštění úlohy** stránce **Start**. 

    ![Domovská stránka projektu](./media/event-hubs-kafka-stream-analytics/start-job-page.png)
1. Počkejte, dokud se stav úlohy změní z **počáteční** k **systémem**. 

    ![Stav úlohy - s](./media/event-hubs-kafka-stream-analytics/running.png)

## <a name="test-the-scenario"></a>Otestování tohoto scénáře
1. Spustit **Kafka producer** znovu k odesílání událostí do centra událostí. 

    ```shell
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
1. Zkontrolujte, jestli se **výstupní data** generován **úložiště objektů blob v Azure**. Zobrazí soubor JSON v kontejneru s 100 řádků, které vypadají podobně jako následující řádky vzorku: 

    ```
    {"eventData":"Test Data 0","EventProcessedUtcTime":"2018-08-30T03:27:23.1592910Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 1","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 2","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    ```

    Úlohy Azure Stream Analytics vstupních dat přijatých z centra událostí a uložených ve službě Azure blob storage v tomto scénáři. 



## <a name="next-steps"></a>Další postup
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
