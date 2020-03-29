---
title: Streamování dat jako vstupu do Azure Stream Analytics
description: Přečtěte si o nastavení datového připojení ve Službě Azure Stream Analytics. Vstupy zahrnují datový proud z událostí a také referenční data.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 388f43fec9242f6a4b448483d9486aa4413d2612
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254466"
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Streamování dat jako vstupu do Stream Analytics

Stream Analytics má prvotřídní integraci s datovými toky Azure jako vstupy ze tří druhů prostředků:

- [Centra událostí Azure](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Úložiště objektů blob Azure](https://azure.microsoft.com/services/storage/blobs/) 

Tyto vstupní prostředky můžou žít ve stejném předplatném Azure jako vaše úloha Stream Analytics nebo jiné předplatné.

### <a name="compression"></a>Komprese

Stream Analytics podporuje kompresi ve všech vstupních zdrojích datových proudů. Podporované typy komprese jsou: Žádné, GZip a Deflate komprese. Podpora komprese není k dispozici pro referenční data. Pokud vstupní formát je Avro data, která je komprimována, je zpracována transparentně. Není nutné zadat typ komprese s serializací Avro. 

## <a name="create-edit-or-test-inputs"></a>Vytváření, úpravy nebo testování vstupů

Pomocí [portálu Azure](stream-analytics-quick-create-portal.md), [Visual Studia](stream-analytics-quick-create-vs.md)a kódu [Visual Studia](quick-create-vs-code.md) můžete přidat a zobrazit nebo upravit existující vstupy v úloze streamování. Můžete také otestovat vstupní připojení a [testovat dotazy](stream-analytics-manage-job.md#test-your-query) z ukázkových dat z portálu Azure, [Visual Studia](stream-analytics-vs-tools-local-run.md)a kódu Sady [Visual Studio](visual-studio-code-local-run.md). Při psaní dotazu, můžete uvést vstup do from klauzule. Seznam dostupných vstupů můžete získat na stránce **Dotaz** na portálu. Pokud chcete použít více vstupů, `JOIN` můžete je `SELECT` nebo napsat více dotazů.


## <a name="stream-data-from-event-hubs"></a>Streamování dat z Event Hubs

Azure Event Hubs poskytuje vysoce škálovatelné ingestory událostí publikování a odběru. Centrum událostí může shromažďovat miliony událostí za sekundu, takže můžete zpracovávat a analyzovat obrovské množství dat vytvořených připojenými zařízeními a aplikacemi. Centra událostí a Stream Analytics společně poskytují komplexní řešení pro analýzy v reálném čase. Centra událostí vám umožní přenášet události do Azure v reálném čase a úlohy Stream Analytics můžou tyto události zpracovávat v reálném čase. Můžete například odesílat kliknutí na web, údaje ze senzorů nebo události online protokolu do centra událostí. Pak můžete vytvořit úlohy Stream Analytics pro použití centra událostí jako vstupní datové proudy pro filtrování, agregaci a korelaci v reálném čase.

`EventEnqueuedUtcTime`je časové razítko příchodu události v centru událostí a je výchozím časovým razítkem událostí přicházejících z Centra událostí do Stream Analytics. Chcete-li zpracovat data jako datový proud pomocí časového razítka v datové části události, musíte použít klíčové slovo [TIMESTAMP BY.](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)

### <a name="event-hubs-consumer-groups"></a>Skupiny spotřebitelů centra event hub

Každý vstup centra událostí Stream Analytics byste měli nakonfigurovat tak, aby měl vlastní skupinu spotřebitelů. Pokud úloha obsahuje vlastní spojení nebo má více vstupů, některé vstupy může číst více než jeden čtenář po proudu. Tato situace má vliv na počet čtenářů v jedné skupině spotřebitelů. Chcete-li se vyhnout překročení limitu centra událostí pěti čtenářů na skupinu spotřebitelů na oddíl, je osvědčeným postupem určit skupinu spotřebitelů pro každou úlohu Služby Streamování. Pro centrum událostí úrovně Standard je také limit 20 skupin spotřebitelů. Další informace najdete [v tématu Poradce při potížích se vstupy Azure Stream Analytics](stream-analytics-troubleshoot-input.md).

### <a name="create-an-input-from-event-hubs"></a>Vytvoření vstupu z centra událostí

Následující tabulka vysvětluje každou vlastnost na **stránce Nový vstup** na webu Azure Portal pro streamování datových vstupů z centra událostí:

| Vlastnost | Popis |
| --- | --- |
| **Vstupní alias** |Popisný název, který použijete v dotazu úlohy k odkazu na tento vstup. |
| **Předplatné** | Zvolte předplatné, ve kterém existuje prostředek centra událostí. | 
| **Obor názvů centra událostí** | Obor názvů centra událostí je kontejner pro sadu entit zasílání zpráv. Když vytvoříte nové centrum událostí, vytvoříte také obor názvů. |
| **Název centra událostí** | Název centra událostí, který chcete použít jako vstup. |
| **Název zásad centra událostí** | Zásady sdíleného přístupu, které poskytují přístup k centru událostí. Každá zásada sdíleného přístupu má název, nastavená oprávnění a přístupové klíče. Tato možnost se automaticky vyplní, pokud nevyberete možnost ručního zadání nastavení Centra událostí.|
| **Skupina spotřebitelů Centra událostí** (doporučeno) | Důrazně doporučujeme použít pro každou úlohu Stream Analytics odlišnou skupinu spotřebitelů. Tento řetězec identifikuje skupinu spotřebitelů, která má být používána k ingestování dat z centra událostí. Pokud není zadána žádná skupina spotřebitelů, úloha Stream Analytics používá skupinu $Default příjemce.  |
| **Klíč oddílu** | Pokud je váš vstup rozdělen na oddíly podle vlastnosti, můžete přidat název této vlastnosti. Klíče oddílu jsou volitelné a slouží ke zlepšení výkonu dotazu, pokud obsahuje klauzuli PARTITION BY nebo GROUP BY v této vlastnosti. |
| **Formát serializace události** | Formát serializace (JSON, CSV, Avro nebo [Jiný (Protobuf, XML, proprietární...)](custom-deserializer.md)příchozího datového proudu.  Ujistěte se, že formát JSON je zarovnán se specifikací a neobsahuje pro desetinná čísla úvodní 0. |
| **Kódování** | UTF-8 je v současné době jediným podporovaným formátem kódování. |
| **Typ komprese událostí** | Typ komprese používaný ke čtení příchozídatový proud, například None (výchozí), GZip nebo Deflate. |

Když vaše data pocházejí ze vstupu datového proudu centra událostí, máte v dotazu Stream Analytics přístup k následujícím polím metadat:

| Vlastnost | Popis |
| --- | --- |
| **EventProcessedUtcTime** |Datum a čas, kdy byla událost zpracována službou Stream Analytics. |
| **EventEnqueuedUtcTime** |Datum a čas, kdy událost byla přijata Event Hubs. |
| **Partitionid** |ID oddílu založené na nule pro vstupní adaptér. |

Pomocí těchto polí můžete například napsat dotaz jako následující příklad:

```sql
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
```

> [!NOTE]
> Při použití centra událostí jako koncového bodu pro trasy služby IoT Hub můžete přistupovat k metadatům služby IoT Hub pomocí [funkce GetMetadataPropertyValue](https://docs.microsoft.com/stream-analytics-query/getmetadatapropertyvalue).
> 

## <a name="stream-data-from-iot-hub"></a>Streamování dat z IoT Hubu

Azure IoT Hub je vysoce škálovatelný publikování a odebírat události ingestor optimalizované pro scénáře IoT.

Výchozí časové razítko událostí přicházejících z centra IoT hub v Stream Analytics je časové razítko, které událost dorazila do služby IoT Hub, což je `EventEnqueuedUtcTime`. Chcete-li zpracovat data jako datový proud pomocí časového razítka v datové části události, musíte použít klíčové slovo [TIMESTAMP BY.](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)

### <a name="iot-hub-consumer-groups"></a>Skupiny spotřebitelů iot Hub

Každý vstup služby Stream Analytics IoT Hub byste měli nakonfigurovat tak, aby měl vlastní skupinu spotřebitelů. Pokud úloha obsahuje vlastní spojení nebo pokud má více vstupů, některé vstupy může číst více než jeden čtenář po proudu dat. Tato situace má vliv na počet čtenářů v jedné skupině spotřebitelů. Chcete-li se vyhnout překročení limitu služby Azure IoT Hub pěti čtenářů na skupinu spotřebitelů na oddíl, je osvědčeným postupem určit skupinu spotřebitelů pro každou úlohu Služby Streamování.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Konfigurace služby IoT Hub jako vstupu datového proudu

Následující tabulka vysvětluje každou vlastnost na **stránce Nový vstup** na webu Azure Portal při konfiguraci služby IoT Hub jako vstupu datového proudu.

| Vlastnost | Popis |
| --- | --- |
| **Vstupní alias** | Popisný název, který použijete v dotazu úlohy k odkazu na tento vstup.|
| **Předplatné** | Zvolte předplatné, ve kterém existuje prostředek služby IoT Hub. | 
| **IoT Hub** | Název centra IoT, který chcete použít jako vstup. |
| **Koncový bod** | Koncový bod pro centrum IoT Hub.|
| **Název zásady sdíleného přístupu** | Zásady sdíleného přístupu, které poskytují přístup k centru IoT Hub. Každá zásada sdíleného přístupu má název, nastavená oprávnění a přístupové klíče. |
| **Klíč zásad sdíleného přístupu** | Sdílený přístupový klíč používaný k autorizaci přístupu k centru IoT Hub.  Tato možnost se automaticky vyplní, pokud nevyberete možnost ručního zadání nastavení služby Iot Hub. |
| **Skupina uživatelů** | Důrazně doporučujeme použít pro každou úlohu Služby Stream Analytics jinou skupinu spotřebitelů. Skupina spotřebitelů se používá k ingestování dat z centra IoT Hub. Stream Analytics používá skupinu $Default spotřebitelů, pokud neurčíte jinak.  |
| **Klíč oddílu** | Pokud je váš vstup rozdělen na oddíly podle vlastnosti, můžete přidat název této vlastnosti. Klíče oddílu jsou volitelné a slouží ke zlepšení výkonu dotazu, pokud obsahuje klauzuli PARTITION BY nebo GROUP BY v této vlastnosti. |
| **Formát serializace události** | Formát serializace (JSON, CSV, Avro nebo [Jiný (Protobuf, XML, proprietární...)](custom-deserializer.md)příchozího datového proudu.  Ujistěte se, že formát JSON je zarovnán se specifikací a neobsahuje pro desetinná čísla úvodní 0. |
| **Kódování** | UTF-8 je v současné době jediným podporovaným formátem kódování. |
| **Typ komprese událostí** | Typ komprese používaný ke čtení příchozídatový proud, například None (výchozí), GZip nebo Deflate. |


Při použití dat datového proudu z centra IoT Hub máte v dotazu Stream Analytics přístup k následujícím polím metadat:

| Vlastnost | Popis |
| --- | --- |
| **EventProcessedUtcTime** | Datum a čas zpracování události. |
| **EventEnqueuedUtcTime** | Datum a čas, kdy byla událost přijata centrem IoT Hub. |
| **Partitionid** | ID oddílu založené na nule pro vstupní adaptér. |
| **IoTHub.MessageId** | ID, které se používá ke korelaci obousměrné komunikace v centru IoT Hub. |
| **IoTHub.CorrelationId** | ID, které se používá v odpovědích na zprávy a zpětnou vazbu v centru IoT Hub. |
| **IoTHub.ConnectionId zařízení** | ID ověřování použité k odeslání této zprávy. Tato hodnota je vyznačena na servicebound zprávy služby služby služby. |
| **IoTHub.ConnectionDeviceGenerationId** | ID generování ověřeného zařízení, které bylo použito k odeslání této zprávy. Tato hodnota je vyznačena na servicebound zprávy služby služby služby. |
| **IoTHub.EnqueuedTime** | Čas, kdy byla zpráva přijata službou IoT Hub. |


## <a name="stream-data-from-blob-storage"></a>Streamování dat z úložiště objektů Blob
Pro scénáře s velkým množstvím nestrukturovaných dat pro uložení v cloudu nabízí úložiště objektů blob Azure cenově výhodné a škálovatelné řešení. Data ve úložišti objektů Blob se obvykle považují za data v klidovém stavu; Data objektů blob však mohou být zpracována jako datový proud službou Stream Analytics. 

Zpracování protokolu je běžně používaný scénář pro použití vstupů úložiště objektů Blob s Stream Analytics. V tomto scénáři telemetrické datové soubory byly zachyceny ze systému a je třeba analyzovat a zpracovat extrahovat smysluplná data.

Výchozí časové razítko událostí úložiště objektů Blob v Stream Analytics je časové `BlobLastModifiedUtcTime`razítko, které byl objekt blob naposledy upraven, což je . Pokud se objekt blob nahraje do účtu úložiště v 13:00 a úloha Azure Stream Analytics se spustí pomocí *možnosti Nyní* v 13:01, objekt blob se nezvedne, protože jeho upravený čas spadá mimo dobu spuštění úlohy.

Pokud se objekt blob nahraje do kontejneru účtu úložiště v 13:00 a úloha Azure Stream Analytics se spustí pomocí *vlastního času* v 13:00 nebo dříve, objekt blob se vyzvedne, protože jeho upravený čas spadá do doby spuštění úlohy.

Pokud se úloha Azure Stream Analytics začne používat *teď* v 13:00 a objekt blob se nahraje do kontejneru účtu úložiště v 13:01, Azure Stream Analytics převezme objekt blob.

Chcete-li zpracovat data jako datový proud pomocí časového razítka v datové části události, musíte použít klíčové slovo [TIMESTAMP BY.](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) Úloha Stream Analytics získává data ze vstupu úložiště objektů blob Azure každou sekundu, pokud je k dispozici soubor objektů blob. Pokud soubor objektů blob není k dispozici, je exponenciální backoff s maximální časovou prodlevou 90 sekund.

Vstupy ve formátu CSV vyžadují řádek záhlaví k definování polí pro sadu dat a všechna řádková pole záhlaví musí být jedinečná.

> [!NOTE]
> Stream Analytics nepodporuje přidávání obsahu do existujícího souboru objektů blob. Stream Analytics zobrazí každý soubor pouze jednou a všechny změny, ke kterým dojde v souboru po dokončení úlohy přečetl data nejsou zpracovány. Osvědčeným postupem je nahrát všechna data pro soubor objektů blob najednou a pak přidat další novější události do jiného nového souboru objektů blob.

Nahrání velmi velkého počtu objektů BLOB najednou může způsobit, že služba Stream Analytics ve výjimečných případech přeskočí čtení několika objektů BLOB. Doporučujeme nahrát objekty BLOB s odstupem nejméně 2 sekund do úložiště objektů Blob. Pokud tato možnost není proveditelná, můžete pomocí centra událostí streamovat velké objemy událostí. 

### <a name="configure-blob-storage-as-a-stream-input"></a>Konfigurace úložiště objektů blob jako vstupu datového proudu 

Následující tabulka vysvětluje každou vlastnost na **stránce Nový vstup** na webu Azure Portal při konfiguraci úložiště objektů Blob jako vstupu datového proudu.

| Vlastnost | Popis |
| --- | --- |
| **Vstupní alias** | Popisný název, který použijete v dotazu úlohy k odkazu na tento vstup. |
| **Předplatné** | Zvolte předplatné, ve kterém existuje prostředek služby IoT Hub. | 
| **Účet úložiště** | Název účtu úložiště, kde jsou umístěny soubory objektů blob. |
| **Klíč účtu úložiště** | Tajný klíč přidružený k účtu úložiště. Tato možnost se automaticky vyplní, pokud nevyberete možnost ručního poskytnutí nastavení úložiště objektů Blob. |
| **Kontejner** | Kontejner pro vstup objektu blob. Kontejnery poskytují logické seskupení pro objekty BLOB uložené ve službě Objektů blob Microsoft Azure. Když nahrajete objekt blob do služby úložiště objektů blob Azure, musíte zadat kontejner pro tento objekt blob. Můžete zvolit **použít existující** kontejner nebo **vytvořit nový,** aby byl vytvořen nový kontejner.|
| **Vzorek cesty** (volitelný) | Cesta k souboru slouží k vyhledání objektů BLOB v rámci zadaného kontejneru. Pokud chcete číst objekty BLOB z kořenového adresáře kontejneru, nenastavujte vzorek cesty. V rámci cesty můžete určit jednu nebo více instancí z následujících tří proměnných: `{date}`, `{time}`, nebo`{partition}`<br/><br/>Příklad 1:`cluster1/logs/{date}/{time}/{partition}`<br/><br/>Příklad 2:`cluster1/logs/{date}`<br/><br/>Znak `*` není povolená hodnota pro předponu cesty. Jsou povoleny pouze platné <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">znaky objektu blob Azure.</a> Nezahrnujte názvy kontejnerů ani názvy souborů. |
| **Formát data** (nepovinné) | Pokud použijete proměnnou data v cestě, formát data, ve kterém jsou soubory uspořádány. Příklad: `YYYY/MM/DD` |
| **Formát času** (volitelné) |  Pokud použijete časovou proměnnou v cestě, formát času, ve kterém jsou soubory uspořádány. V současné době je `HH` jedinou podporovanou hodnotou hodiny. |
| **Klíč oddílu** | Pokud je váš vstup rozdělen na oddíly podle vlastnosti, můžete přidat název této vlastnosti. Klíče oddílu jsou volitelné a slouží ke zlepšení výkonu dotazu, pokud obsahuje klauzuli PARTITION BY nebo GROUP BY v této vlastnosti. |
| **Formát serializace události** | Formát serializace (JSON, CSV, Avro nebo [Jiný (Protobuf, XML, proprietární...)](custom-deserializer.md)příchozího datového proudu.  Ujistěte se, že formát JSON je zarovnán se specifikací a neobsahuje pro desetinná čísla úvodní 0. |
| **Kódování** | Pro CSV a JSON je UTF-8 v současné době jediným podporovaným formátem kódování. |
| **Komprese** | Typ komprese používaný ke čtení příchozídatový proud, například None (výchozí), GZip nebo Deflate. |

Když vaše data pocházejí ze zdroje úložiště objektů Blob, máte v dotazu Stream Analytics přístup k následujícím polím metadat:

| Vlastnost | Popis |
| --- | --- |
| **Název objektu BlobName** |Název vstupního objektu blob, ze kterého událost pochází. |
| **EventProcessedUtcTime** |Datum a čas, kdy byla událost zpracována službou Stream Analytics. |
| **Objekt BlobLastModifiedUtcTime** |Datum a čas, kdy byl objekt blob naposledy změněn. |
| **Partitionid** |ID oddílu založené na nule pro vstupní adaptér. |

Pomocí těchto polí můžete například napsat dotaz jako následující příklad:

```sql
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
```

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Rychlý start: Vytvoření úlohy Stream Analytics pomocí webu Azure Portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
