---
title: Stream data jako vstup do Azure Stream Analytics
description: Další informace o nastavení připojení dat ve službě Azure Stream Analytics. Vstupy zahrnují datový proud z událostí a také referenční data.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 388f43fec9242f6a4b448483d9486aa4413d2612
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314789"
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Stream data jako vstup do Stream Analytics

Stream Analytics je prvotřídní integrovaná se sadou Azure datové proudy jako vstupů ze tří typů prostředků:

- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 

Tyto prostředky vstupní může existovat ve stejném předplatném Azure jako svou úlohu Stream Analytics nebo jiné předplatné.

### <a name="compression"></a>Komprese

Stream Analytics podporuje kompresi ve všech vstupních zdrojů dat datového proudu. Podporované typy komprese jsou: None, GZip a Deflate Compression. Podpora pro kompresi není k dispozici pro referenční data. Pokud vstupní formát Avro data, která je komprimován, zpracuje se transparentně. Není nutné určit typ komprese se serializace Avro. 

## <a name="create-edit-or-test-inputs"></a>Vytvářet, upravovat nebo testovací vstupy

Pomocí [Azure Portal](stream-analytics-quick-create-portal.md), sady [Visual Studio](stream-analytics-quick-create-vs.md)a [Visual Studio Code](quick-create-vs-code.md) můžete přidat a zobrazit nebo upravit existující vstupy v úloze streamování. Vstupní připojení a [testovací dotazy](stream-analytics-manage-job.md#test-your-query) můžete také testovat z ukázkových dat z Azure Portal, sady [Visual Studio](stream-analytics-vs-tools-local-run.md)a [Visual Studio Code](visual-studio-code-local-run.md). Při psaní dotazu se zobrazí seznam vstupů v klauzuli FROM. Můžete získat seznam dostupných vstupů ze **dotazu** stránky na portálu. Pokud chcete použít více vstupů, můžete si `JOIN` jejich nebo zápis více `SELECT` dotazy.


## <a name="stream-data-from-event-hubs"></a>Streamování dat z Event Hubs

Azure Event Hubs poskytuje vysoce škálovatelné ingestors události publikování a odběru. Centrum událostí může shromažďovat miliony událostí za sekundu, abyste mohli zpracovávat a analyzovat obrovské objemy dat vytvářených zařízeními a aplikacemi připojenými k vaší síti. Event Hubs a Stream Analytics společně, poskytují-ucelené řešení pro analýzu v reálném čase. Event Hubs umožňuje informační kanál událostí do Azure v reálném čase, a úlohy Stream Analytics dokáže zpracovat tyto události v reálném čase. Můžete například odeslat webové kliknutí, údajů snímačů přes nebo online protokolu událostí do služby Event Hubs. Potom můžete vytvořit úlohy Stream Analytics pro účely služby Event Hubs jako vstupní datové proudy v reálném čase filtrování, agregace a korelace.

`EventEnqueuedUtcTime` časové razítko přijetí události v Centru událostí a je výchozí časové razítko události pocházející ze služby Event Hubs do služby Stream Analytics. Ke zpracování dat jako datový proud pomocí časového razítka v případě, že datová část, je nutné použít [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) – klíčové slovo.

### <a name="event-hubs-consumer-groups"></a>Event Hubs skupiny příjemců

Měli byste nakonfigurovat každé Centrum událostí Stream Analytics zadejte mít svůj vlastní skupina uživatelů. Pokud úloha obsahuje spojení sama nebo má více vstupů, některými vstupy může číst čtečka více než jeden směru server-klient. Tato situace má dopad počet čtenářů v skupinu jednoho příjemce. Aby nedošlo k překročení limitu služby Event Hubs pět čtecích zařízení na skupinu příjemce na oddíl, je osvědčeným postupem je určit skupinu příjemců pro každou úlohu Stream Analytics. K dispozici je také limit 20 skupin uživatelů pro centrum událostí úrovně Standard. Další informace najdete v tématu [Poradce při potížích s Azure Stream Analytics vstupy](stream-analytics-troubleshoot-input.md).

### <a name="create-an-input-from-event-hubs"></a>Vytvoření vstupu z Event Hubs

Následující tabulka popisuje každou vlastnost **nový vstup** stránky na webu Azure Portal na vstup dat datový proud z centra událostí:

| Vlastnost | Popis |
| --- | --- |
| **Vstupní alias** |Popisný název, který v dotazu úlohy používáte odkazovat tento vstup. |
| **Předplatné** | Vyberte předplatné, ve které existuje prostředek centra událostí. | 
| **Obor názvů centra událostí** | Obor názvů centra událostí je kontejner sady entit pro zasílání zpráv. Když vytvoříte nové Centrum událostí, je také vytvořit obor názvů. |
| **Název centra událostí** | Název centra událostí, který se použije jako vstup. |
| **Název zásady centra událostí** | Zásady sdíleného přístupu, která poskytuje přístup do centra událostí. Každá zásada sdíleného přístupu má název, že je nastavená oprávnění a přístupové klíče. Tato možnost se vyplní automaticky, pokud vyberete možnost určit nastavení centra událostí ručně.|
| **Skupina uživatelů centra událostí** (doporučeno) | Důrazně doporučujeme používat skupiny konzumentů distinct pro každou úlohu Stream Analytics. Tento řetězec identifikuje skupinu příjemců určený k ingestování dat z centra událostí. Pokud není zadána žádná skupina uživatelů, úloha Stream Analytics používá skupina uživatelů $Default.  |
| **Klíč oddílu** | Pokud je vstup rozdělený pomocí vlastnosti, můžete přidat název této vlastnosti. Klíče oddílů jsou volitelné a slouží ke zlepšení výkonu dotazu, pokud obsahuje klauzuli PARTITION BY nebo GROUP BY pro tuto vlastnost. |
| **Formát serializace události** | Formát serializace (JSON, CSV, Avro nebo [jiný (Protobuf, XML, proprietární...)](custom-deserializer.md)) příchozího datového proudu.  Zkontrolujte formát JSON odpovídá specifikaci a nezahrnuje 0 na desetinná čísla. |
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
> Při použití centra událostí jako koncového bodu pro IoT Hub trasy můžete k metadatům IoT Hub přistupovat pomocí [funkce GetMetadataPropertyValue](https://docs.microsoft.com/stream-analytics-query/getmetadatapropertyvalue).
> 

## <a name="stream-data-from-iot-hub"></a>Stream dat ze služby IoT Hub

Azure IoT Hub je vysoce škálovatelný ingestování událostí publikování a odběru optimalizované pro scénáře IoT.

Výchozí časové razítko události pocházející ze služby IoT Hub ve službě Stream Analytics je časové razítko, které byly přijaty události ve službě IoT Hub, který je `EventEnqueuedUtcTime`. Ke zpracování dat jako datový proud pomocí časového razítka v případě, že datová část, je nutné použít [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) – klíčové slovo.

### <a name="iot-hub-consumer-groups"></a>Skupiny uživatelů centra IoT Hub

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
| **Klíč oddílu** | Pokud je vstup rozdělený pomocí vlastnosti, můžete přidat název této vlastnosti. Klíče oddílů jsou volitelné a slouží ke zlepšení výkonu dotazu, pokud obsahuje klauzuli PARTITION BY nebo GROUP BY pro tuto vlastnost. |
| **Formát serializace události** | Formát serializace (JSON, CSV, Avro nebo [jiný (Protobuf, XML, proprietární...)](custom-deserializer.md)) příchozího datového proudu.  Zkontrolujte formát JSON odpovídá specifikaci a nezahrnuje 0 na desetinná čísla. |
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

Výchozí časové razítko události služby Blob storage ve službě Stream Analytics je časové razítko, že byl naposledy upraven objekt blob, který je `BlobLastModifiedUtcTime`. Pokud se objekt BLOB nahraje do účtu úložiště v 13:00 a úloha Azure Stream Analytics se spustí pomocí možnosti *Now* (13:01), objekt BLOB se nevybere, protože čas změny spadá mimo dobu běhu úlohy.

Pokud se objekt BLOB nahraje do kontejneru účtu úložiště v 13:00 a úloha Azure Stream Analytics se spustí s využitím *vlastního času* v 13:00 nebo dřívějším, bude se tento objekt BLOB vyzvednout, protože čas změny spadá do doby běhu úlohy.

Pokud se úloha Azure Stream Analytics začala používat *hned* v 13:00 a do kontejneru účtu úložiště se nahraje objekt blob na 13:01, Azure Stream Analytics si tento objekt BLOB zachová.

Ke zpracování dat jako datový proud pomocí časového razítka v případě, že datová část, je nutné použít [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) – klíčové slovo. Úlohu Stream Analytics si vyžádá data z vstup úložiště objektů Blob v Azure každou sekundu Pokud je k dispozici soubor objektu blob. Pokud soubor objektu blob není k dispozici, je exponenciální regresi s maximální doba zpoždění 90 sekund.

Vstupy ve formátu CSV vyžadují pro definování polí pro datovou sadu řádek záhlaví a všechna řádková pole záhlaví musí být jedinečná.

> [!NOTE]
> Stream Analytics nepodporuje přidávání obsahu do existujícího souboru objektu blob. Stream Analytics se zobrazí každý soubor jenom jednou a nebudou zpracovány všechny změny, ke kterým dochází v souboru po úloze má číst data. Osvědčeným postupem je odeslat veškerá data pro soubor blob najednou a pak přidejte další události do souboru objektu blob různých, nové.

Nahrávání velmi velkého počtu objektů BLOB najednou může způsobit Stream Analytics Přeskočit čtení několika objektů BLOB ve výjimečných případech. Doporučujeme nahrávat objekty blob alespoň 2 sekundy od úložiště objektů BLOB. Pokud tato možnost není proveditelná, můžete použít Event Hubs ke streamování velkých objemů událostí. 

### <a name="configure-blob-storage-as-a-stream-input"></a>Konfigurace úložiště objektů Blob jako vstupní datový proud 

Následující tabulka popisuje každou vlastnost **nový vstup** stránky na webu Azure Portal při konfiguraci úložiště objektů Blob jako vstup datového proudu.

| Vlastnost | Popis |
| --- | --- |
| **Vstupní alias** | Popisný název, který v dotazu úlohy používáte odkazovat tento vstup. |
| **Předplatné** | Vyberte předplatné, ve kterém existuje prostředek služby IoT Hub. | 
| **Účet úložiště** | Název účtu úložiště, kde jsou uložené soubory objektů blob. |
| **Klíč účtu úložiště** | Tajný klíč přidružený k účtu úložiště. Tato možnost se automaticky vyplní v, pokud vyberete možnost určit nastavení úložiště objektů Blob ručně. |
| **Kontejner** | Kontejner pro vstupní objekt blob. Kontejnery poskytují možnost logického seskupování u objektů BLOB uložených ve službě Microsoft Azure Blob. Při nahrání objektu blob do služby Azure Blob storage, je nutné zadat kontejner pro tohoto objektu blob. Můžete použít buď **použít existující** kontejneru nebo **vytvořit nový** mít vytvořený nový kontejner.|
| **Vzor cesty** (volitelné) | Cesta k souboru používaná k nalezení objektů BLOB v zadaném kontejneru. Pokud chcete číst objekty BLOB z kořenového adresáře kontejneru, nenastavujte vzor cesty. V této cestě můžete zadat jednu nebo víc instancí následujících tří proměnných: `{date}`, `{time}`, nebo `{partition}`<br/><br/>Příklad 1: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>Příklad 2: `cluster1/logs/{date}`<br/><br/>`*` Znak není povolená hodnota pro Předpona cesty. Jediné platné <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">objektů blob v Azure znaků</a> jsou povoleny. Nezahrnujte názvy kontejnerů nebo názvy souborů. |
| **Formát data** (volitelné) | Pokud použijete proměnnou datum v cestě, ve kterém se soubory jsou uspořádány formát data. Příklad: `YYYY/MM/DD` |
| **Formát času** (volitelné) |  Pokud použijete proměnnou čas v cestě, formát času, ve kterém jsou uspořádány soubory. Momentálně je jediná podporovaná hodnota `HH` hodin. |
| **Klíč oddílu** | Pokud je vstup rozdělený pomocí vlastnosti, můžete přidat název této vlastnosti. Klíče oddílů jsou volitelné a slouží ke zlepšení výkonu dotazu, pokud obsahuje klauzuli PARTITION BY nebo GROUP BY pro tuto vlastnost. |
| **Formát serializace události** | Formát serializace (JSON, CSV, Avro nebo [jiný (Protobuf, XML, proprietární...)](custom-deserializer.md)) příchozího datového proudu.  Zkontrolujte formát JSON odpovídá specifikaci a nezahrnuje 0 na desetinná čísla. |
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

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Rychlý start: Vytvoření úlohy Stream Analytics pomocí webu Azure portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
