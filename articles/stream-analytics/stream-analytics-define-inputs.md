---
title: Stream data jako vstup do Azure Stream Analytics
description: Další informace o nastavení připojení dat ve službě Azure Stream Analytics. Vstupy zahrnují datový proud z událostí a také referenční data.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 66e57d95125bd6404ec23302e4c336ab0b71ea78
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53099728"
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Stream data jako vstup do Stream Analytics

Stream Analytics je prvotřídní integrovaná se sadou Azure datové proudy jako vstupů ze tří typů prostředků:
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 

Tyto prostředky vstupní může existovat ve stejném předplatném Azure jako svou úlohu Stream Analytics nebo jiné předplatné.

### <a name="compression"></a>Komprese
Stream Analytics podporuje kompresi ve všech vstupních zdrojů dat datového proudu. Odkaz na aktuálně podporované typy jsou: None, GZip a komprese Deflate. Podpora pro kompresi není k dispozici pro referenční data. Pokud vstupní formát Avro data, která je komprimován, zpracuje se transparentně. Není nutné určit typ komprese se serializace Avro. 

## <a name="create-edit-or-test-inputs"></a>Vytvářet, upravovat nebo testovací vstupy
Můžete použít [webu Azure portal](https://portal.azure.com) k [vytváření nové vstupů](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal#configure-job-input) a umožňuje zobrazit nebo upravit existující vstupů na své úlohy streamování. Můžete také testovat vstupní připojení a [testování dotazů](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-manage-job#test-your-query) z ukázková data. Při psaní dotazu, zobrazí se seznam vstup v klauzuli FROM. Můžete získat seznam dostupných vstupů ze **dotazu** stránky na portálu. Pokud chcete použít více vstupů, můžete si `JOIN` jejich nebo zápis více `SELECT` dotazy.


## <a name="stream-data-from-event-hubs"></a>Streamování dat z Event Hubs

Azure Event Hubs poskytuje vysoce škálovatelné ingestors události publikování a odběru. V Centru událostí shromažďovat miliony událostí za sekundu, můžete zpracovávat a analyzovat velké objemy dat vytvářené vašimi připojenými zařízeními a aplikacemi. Event Hubs a Stream Analytics společně, poskytují-ucelené řešení pro analýzu v reálném čase. Event Hubs umožňuje informační kanál událostí do Azure v reálném čase, a úlohy Stream Analytics dokáže zpracovat tyto události v reálném čase. Můžete například odeslat webové kliknutí, údajů snímačů přes nebo online protokolu událostí do služby Event Hubs. Potom můžete vytvořit úlohy Stream Analytics pro účely služby Event Hubs jako vstupní datové proudy v reálném čase filtrování, agregace a korelace.

`EventEnqueuedUtcTime` časové razítko přijetí události v Centru událostí a je výchozí časové razítko události pocházející ze služby Event Hubs do služby Stream Analytics. Ke zpracování dat jako datový proud pomocí časového razítka v případě, že datová část, je nutné použít [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) – klíčové slovo.

### <a name="consumer-groups"></a>Skupiny příjemců
Měli byste nakonfigurovat každé Centrum událostí Stream Analytics zadejte mít svůj vlastní skupina uživatelů. Pokud úloha obsahuje spojení sama nebo má více vstupů, některými vstupy může číst čtečka více než jeden směru server-klient. Tato situace má dopad počet čtenářů v skupinu jednoho příjemce. Aby nedošlo k překročení limitu služby Event Hubs pět čtecích zařízení na skupinu příjemce na oddíl, je osvědčeným postupem je určit skupinu příjemců pro každou úlohu Stream Analytics. Platí omezení 20 skupin uživatelů na Centrum událostí. Další informace najdete v tématu [Poradce při potížích s Azure Stream Analytics vstupy](stream-analytics-troubleshoot-input.md).

### <a name="stream-data-from-event-hubs"></a>Streamování dat z Event Hubs
Následující tabulka popisuje každou vlastnost **nový vstup** stránky na webu Azure Portal na vstup dat datový proud z centra událostí:

| Vlastnost | Popis |
| --- | --- |
| **Vstupní alias** |Popisný název, který v dotazu úlohy používáte odkazovat tento vstup. |
| **Předplatné** | Vyberte předplatné, ve které existuje prostředek centra událostí. | 
| **Obor názvů centra událostí** | Obor názvů centra událostí je kontejner sady entit pro zasílání zpráv. Když vytvoříte nové Centrum událostí, je také vytvořit obor názvů. |
| **Název centra událostí** | Název centra událostí, který se použije jako vstup. |
| **Název zásady centra událostí** | Zásady sdíleného přístupu, která poskytuje přístup do centra událostí. Každá zásada sdíleného přístupu má název, že je nastavená oprávnění a přístupové klíče. Tato možnost se vyplní automaticky, pokud vyberete možnost určit nastavení centra událostí ručně.|
| **Skupina uživatelů centra událostí** (doporučeno) | Důrazně doporučujeme používat skupiny konzumentů distinct pro každou úlohu Stream Analytics. Tento řetězec identifikuje skupinu příjemců určený k ingestování dat z centra událostí. Pokud není zadána žádná skupina uživatelů, úloha Stream Analytics používá skupina uživatelů $Default.  |
| **Formát serializace události** | Formát serializace (JSON, CSV nebo Avro) příchozího datového proudu.  Zkontrolujte formát JSON odpovídá specifikaci a nezahrnuje 0 na desetinná čísla. |
| **Kódování** | UTF-8 je aktuálně jediný podporovaný formát kódování. |
| **Typ komprese události** | Typ komprese, který se použije ke čtení příchozího datového streamu, jako je například None (výchozí), GZip nebo Deflate. |

Pokud vaše data pochází z vstup datového proudu Event Hub, máte přístup k následující pole metadat v dotazu Stream Analytics:

| Vlastnost | Popis |
| --- | --- |
| **EventProcessedUtcTime** |Datum a čas, který událost byla zpracována službou Stream Analytics. |
| **EventEnqueuedUtcTime** |Datum a čas přijetí události pomocí služby Event Hubs. |
| **ID oddílu** |ID oddílu založený na nule pro vstupní adaptér. |

Například pomocí těchto polí můžete napsat dotaz jako v následujícím příkladu:

```sql
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
```

> [!NOTE]
> Při použití centra událostí jako koncový bod pro IoT Hub trasy, lze použít k IoT Hubu pomocí medadata [funkce GetMetadataPropertyValue](https://msdn.microsoft.com/library/azure/mt793845.aspx).
> 

## <a name="stream-data-from-iot-hub"></a>Stream dat ze služby IoT Hub
Azure Iot Hub je vysoce škálovatelná publikování a odběr schopná optimalizované pro scénáře IoT.

Výchozí časové razítko události pocházející ze služby IoT Hub ve službě Stream Analytics je časové razítko, které byly přijaty události ve službě IoT Hub, který je `EventEnqueuedUtcTime`. Ke zpracování dat jako datový proud pomocí časového razítka v případě, že datová část, je nutné použít [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) – klíčové slovo.

### <a name="consumer-groups"></a>Skupiny příjemců
Měli byste nakonfigurovat každou Stream Analytics služby IoT Hub vstup mít svůj vlastní skupina uživatelů. Pokud úloha obsahuje spojení sama nebo pokud obsahuje více vstupů, můžou některé vstup přečtený ve směru server-klient více než jeden Čtenář. Tato situace má dopad počet čtenářů v skupinu jednoho příjemce. Aby nedošlo k překročení limitu služby Azure IoT Hub pět čtecích zařízení na skupinu příjemce na oddíl, je osvědčeným postupem je určit skupinu příjemců pro každou úlohu Stream Analytics.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Konfigurace služby IoT Hub jako vstupní datový proud
Následující tabulka popisuje každou vlastnost **nový vstup** stránky na webu Azure Portal při konfiguraci služby IoT Hub jako vstupní datový proud.

| Vlastnost | Popis |
| --- | --- |
| **Vstupní alias** | Popisný název, který v dotazu úlohy používáte odkazovat tento vstup.|
| **Předplatné** | Vyberte předplatné, ve kterém existuje prostředek služby IoT Hub. | 
| **IoT Hub** | Název služby IoT Hub, který se použije jako vstup. |
| **Koncový bod** | Koncový bod pro službu IoT Hub.|
| **Název zásad sdíleného přístupu** | Zásady sdíleného přístupu, která poskytuje přístup ke službě IoT Hub. Každá zásada sdíleného přístupu má název, že je nastavená oprávnění a přístupové klíče. |
| **Klíč zásad sdíleného přístupu** | Sdílený přístupový klíč používaný k autorizaci přístupu ke službě IoT Hub.  Tato možnost se automaticky vyplní v, pokud vyberete možnost lze zadat nastavení služby Iot Hub ručně. |
| **Skupina uživatelů** | Důrazně doporučujeme použít skupiny různých příjemců pro každou úlohu Stream Analytics. Skupina uživatelů slouží k ingestování dat ze služby IoT Hub. Pokud neurčíte jinak, Stream Analytics používá skupina uživatelů $Default.  |
| **Formát serializace události** | Formát serializace (JSON, CSV nebo Avro) příchozího datového proudu.  Zkontrolujte formát JSON odpovídá specifikaci a nezahrnuje 0 na desetinná čísla. |
| **Kódování** | UTF-8 je aktuálně jediný podporovaný formát kódování. |
| **Typ komprese události** | Typ komprese, který se použije ke čtení příchozího datového streamu, jako je například None (výchozí), GZip nebo Deflate. |


Při použití streamování dat ze služby IoT Hub, máte přístup k následující pole metadat v dotazu Stream Analytics:

| Vlastnost | Popis |
| --- | --- |
| **EventProcessedUtcTime** | Datum a čas, který událost byla zpracována. |
| **EventEnqueuedUtcTime** | Datum a čas přijetí události ve službě IoT Hub. |
| **ID oddílu** | ID oddílu založený na nule pro vstupní adaptér. |
| **IoTHub.MessageId** | ID, které slouží ke sladění obousměrná komunikace ve službě IoT Hub. |
| **IoTHub.CorrelationId** | ID, které se používá v odpovědi na zprávy a zpětnou vazbu ve službě IoT Hub. |
| **IoTHub.ConnectionDeviceId** | ID ověřování používaný k odesílání této zprávy. Tato hodnota je označený na servicebound zpráv ve službě IoT Hub. |
| **IoTHub.ConnectionDeviceGenerationId** | Generování ID ověřeného zařízení, které jste použili k zaslání tuto zprávu. Tato hodnota je označený na servicebound zpráv ve službě IoT Hub. |
| **IoTHub.EnqueuedTime** | Čas, kdy byla zpráva přijata službou IoT Hub. |


## <a name="stream-data-from-blob-storage"></a>Stream data z úložiště objektů Blob
Pro scénáře s velkým množstvím nestrukturovaných dat do úložiště v cloudu Azure Blob storage nabízí nákladově efektivní a škálovatelné řešení. Data v úložišti objektů Blob se obvykle považuje za dat v klidovém stavu; Nicméně data objektu blob může být zpracována jako datový proud Stream Analytics. 

Zpracování protokolu je běžně používaný scénář pro vstupy úložiště objektů Blob pomocí Stream Analytics. V tomto scénáři telemetrická data souborů ze systému byla zachycena a nutné analyzovat a zpracovat extrahovat smysluplná data.

Výchozí časové razítko události služby Blob storage ve službě Stream Analytics je časové razítko, že byl naposledy upraven objekt blob, který je `BlobLastModifiedUtcTime`. Ke zpracování dat jako datový proud pomocí časového razítka v případě, že datová část, je nutné použít [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) – klíčové slovo. Úlohu Stream Analytics si vyžádá data z vstup úložiště objektů Blob v Azure každou sekundu Pokud je k dispozici soubor objektu blob. Pokud soubor objektu blob není k dispozici, je exponenciální regresi s maximální doba zpoždění 90 sekund.

Ve formátu CSV vstupy *vyžadují* řádek záhlaví k definování polí pro datovou sadu a všechna pole záhlaví řádku musí být jedinečný.

Stream Analytics v současné době nepodporuje při deserializaci AVRO zprávy generované zachytávání Event Hub nebo vlastní koncový bod kontejneru úložiště Azure IoT Hub.

> [!NOTE]
> Stream Analytics nepodporuje přidávání obsahu do existujícího souboru objektu blob. Stream Analytics se zobrazí každý soubor jenom jednou a nebudou zpracovány všechny změny, ke kterým dochází v souboru po úloze má číst data. Osvědčeným postupem je odeslat veškerá data pro soubor blob najednou a pak přidejte další události do souboru objektu blob různých, nové.
> 

### <a name="configure-blob-storage-as-a-stream-input"></a>Konfigurace úložiště objektů Blob jako vstupní datový proud 

Následující tabulka popisuje každou vlastnost **nový vstup** stránky na webu Azure Portal při konfiguraci úložiště objektů Blob jako vstup datového proudu.

| Vlastnost | Popis |
| --- | --- |
| **Vstupní alias** | Popisný název, který v dotazu úlohy používáte odkazovat tento vstup. |
| **Předplatné** | Vyberte předplatné, ve kterém existuje prostředek služby IoT Hub. | 
| **Účet úložiště** | Název účtu úložiště, kde jsou uložené soubory objektů blob. |
| **Klíč účtu úložiště** | Tajný klíč přidružený k účtu úložiště. Tato možnost se automaticky vyplní v, pokud vyberete možnost určit nastavení úložiště objektů Blob ručně. |
| **Kontejner** | Kontejner pro vstupní objekt blob. Kontejnery poskytují možnost logického seskupování u objektů BLOB uložených ve službě Microsoft Azure Blob. Při nahrání objektu blob do služby Azure Blob storage, je nutné zadat kontejner pro tohoto objektu blob. Můžete použít buď **použít existující** kontejneru nebo **vytvořit nový** mít vytvořený nový kontejner.|
| **Vzor cesty** (volitelné) | Cesta k souboru používaná k nalezení objektů BLOB v zadaném kontejneru. V této cestě můžete zadat jednu nebo víc instancí následujících tří proměnných: `{date}`, `{time}`, nebo `{partition}`<br/><br/>Příklad 1: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>Příklad 2: `cluster1/logs/{date}`<br/><br/>`*` Znak není povolená hodnota pro Předpona cesty. Jediné platné <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">objektů blob v Azure znaků</a> jsou povoleny. Zahrnout ne názvy kontejneru nebo souboru. |
| **Formát data** (volitelné) | Pokud použijete proměnnou datum v cestě, ve kterém se soubory jsou uspořádány formát data. Příklad: `YYYY/MM/DD` |
| **Formát času** (volitelné) |  Pokud použijete proměnnou čas v cestě, formát času, ve kterém jsou uspořádány soubory. Momentálně je jediná podporovaná hodnota `HH` hodin. |
| **Formát serializace události** | Formát serializace (JSON, CSV nebo Avro) příchozího datového proudu.  Zkontrolujte formát JSON odpovídá specifikaci a nezahrnuje 0 na desetinná čísla. |
| **Kódování** | CSV a JSON UTF-8 je aktuálně jediný podporovaný formát kódování. |
| **Komprese** | Typ komprese, který se použije ke čtení příchozího datového streamu, jako je například None (výchozí), GZip nebo Deflate. |

Pokud vaše data pocházejí ze zdrojového úložiště objektů Blob, máte přístup k následující pole metadat v dotazu Stream Analytics:

| Vlastnost | Popis |
| --- | --- |
| **BlobName** |Název vstupního objektu blob, který událost pochází. |
| **EventProcessedUtcTime** |Datum a čas, který událost byla zpracována službou Stream Analytics. |
| **BlobLastModifiedUtcTime** |Datum a čas poslední změny objektu blob. |
| **ID oddílu** |ID oddílu založený na nule pro vstupní adaptér. |

Například pomocí těchto polí můžete napsat dotaz jako v následujícím příkladu:

```sql
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
```

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Rychlý start: Vytvoření úlohy Stream Analytics pomocí webu Azure portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
