---
title: 'Datové připojení: Data stream vstupy z datového proudu událostí | Microsoft Docs'
description: Další informace o nastavení připojení dat do služby Stream Analytics s názvem "vstupy". Vstupy zahrnout datový proud z událostí a také referenční data.
keywords: datový proud, datové připojení, datového proudu událostí
services: stream-analytics
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: 8155823c-9dd8-4a6b-8393-34452d299b68
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 12/11/2017
ms.author: sngun
ms.openlocfilehash: 405fcf190ab03b84ec463da8a1942adb8e326498
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="data-connection-learn-about-data-stream-inputs-from-events-to-stream-analytics"></a>Datové připojení: Další informace o datech vstupy datového proudu z událostí do služby Stream Analytics
Datové připojení do úlohy Stream Analytics je datový proud událostí ze zdroje dat, který se označuje jako úlohy *vstupní*. Stream Analytics obsahuje prvotřídní integrace s datového proudu zdrojů dat Azure, včetně [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/), a [úložiště objektů Azure Blob](https://azure.microsoft.com/services/storage/blobs/). Tyto vstupního zdroje může být ze stejného předplatného Azure jako vaše úloha analýzy nebo z jiného předplatného.

## <a name="data-input-types-data-stream-and-reference-data"></a>Vstupní typy dat: Data streamu a referenčních dat
Jako data odesílána ke zdroji dat, má spotřebovávají úlohu služby Stream Analytics a zpracovány v reálném čase. Vstupy se dál dělí na dva typy: stream vstupy a referenční data vstupy dat.

### <a name="data-stream-inputs"></a>Vstupy datový proud dat
Datový proud je už bez vazby pořadí událostí v čase. Úlohy Stream Analytics musí obsahovat alespoň jeden vstup datového streamu. Služba Event Hubs, IoT Hub a úložiště objektů Blob jsou podporovány jako vstupní zdroje dat datového proudu. Služba Event hubs slouží ke shromažďování streamů událostí z více zařízení a služeb. Tyto datové proudy mohou zahrnovat sociálních médií o aktivitách, informace o uložených obchodní nebo dat ze senzorů. Centra IoT jsou optimalizované pro shromažďování dat z připojených zařízení v scénáře Internetu věcí (IoT).  Úložiště objektů BLOB můžete použít jako vstupní zdroj pro příjem dat hromadné jako datový proud, jako jsou například soubory protokolu.  

### <a name="reference-data"></a>Referenční data
Stream Analytics podporuje také označuje jako vstup *referenční data*. Toto je pomocná data, která je buď statickou nebo který změny pomalu. Se obvykle používá k provedení korelaci a vyhledávání. Například můžete spojit data vstup datového streamu k datům v referenční data tak, jak můžete provést připojení SQL k vyhledání statické hodnoty. Azure Blob storage je aktuálně podporované pouze vstupní zdroj pro referenční data. Referenční data zdroje BLOB jsou omezeny na 100 MB velikost.

Naučte se vytvořit odkaz na vstupy data, najdete v tématu [použití referenčních dat](stream-analytics-use-reference-data.md).  

## <a name="compression"></a>Komprese

Azure Stream Analytics podporuje kompresi mezi všechny datového proudu vstupního zdroje dat (Event Hubs, IoT Hub a objekt Blob úložiště). Tato funkce přidává nová možnost rozevírací seznam **nové vstup** okno portálu Azure, což umožňuje volitelně komprese datové proudy. Aktuálně podporované typy jsou odkaz - None, GZip a kompresi Deflate. Podpora pro kompresi není k dispozici pro referenční data.

Nemusíte určit typ komprese s Avro serializace. Pokud vstupní data Avro je komprimován, zpracuje se transparentně. 

## <a name="create-data-stream-input-from-event-hubs"></a>Vytvoření vstupní datový proud dat ze služby Event Hubs

Azure Event Hubs poskytuje vysoce škálovatelné publikování a odběru ingestors událostí. Centra událostí můžete shromáždit miliony událostí za sekundu, takže umožňuje zpracovávat a analyzovat masivní objemy dat vytvářených připojených zařízení a aplikací. Služba Event Hubs a Stream Analytics společně poskytují-komplexní řešení pro analýzu v reálném čase – Event Hubs umožňují kanálu události do Azure v reálném čase a úlohy Stream Analytics může zpracovat události v reálném čase. Například odeslat klikne na web, odečty snímačů nebo online protokolu událostí do centra událostí. Potom můžete vytvořit úlohy Stream Analytics používat služby Event Hubs jako vstupní datové proudy v reálném čase filtrování, agregace a korelace.

Výchozí časové razítko události pocházející ze služby Event Hubs v Stream Analytics je časové razítko, které události byly přijaty události rozbočovače, který je `EventEnqueuedUtcTime`. Zpracování dat jako použití časového razítka v případě, že datové části, je nutné použít datový proud [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) – klíčové slovo.

### <a name="consumer-groups"></a>Skupiny příjemců
Měli byste nakonfigurovat každý centra událostí Stream Analytics vstup do mají svůj vlastní skupiny příjemců. Pokud úloha obsahuje vlastní spojení nebo pokud obsahuje více vstupů, může číst některé vstup za více než jeden čtečky. Situace má dopad na počet čtenářů v skupinu jednoho příjemce. Aby nedošlo k překročení limitu služby Event Hubs pět čtečky za skupiny příjemců na oddíl, je osvědčeným postupem určit skupinu uživatelů pro každou úlohu služby Stream Analytics. Je také maximální počet 20 skupiny příjemců za centra událostí. Další informace najdete v tématu [Průvodce programováním centra událostí](../event-hubs/event-hubs-programming-guide.md).

### <a name="configure-an-event-hub-as-a-data-stream-input"></a>Konfigurace centra událostí jako vstupní datový proud
Následující tabulka popisuje každou vlastnost v **nové vstup** okno na portálu Azure při konfiguraci centra událostí jako vstup.

| Vlastnost | Popis |
| --- | --- |
| **Vstupní alias** |Popisný název, který používáte v dotazu úlohy odkazovat tento vstup. |
| **Obor názvů sběrnice** |Názvů Azure Service Bus, který je kontejner sady entit pro zasílání zpráv. Při vytváření nového centra událostí vytvořit taky obor názvů sběrnice. |
| **Název centra událostí** |Název centra událostí, a použít jako vstup. |
| **Název zásady centra událostí** |Zásada sdíleného přístupu, která poskytuje přístup k Centru událostí. Každá zásada sdíleného přístupu má název, že je nastavená oprávnění a přístupové klíče. |
| **Skupina uživatelů centra událostí** (volitelné) |Skupinu uživatelů, který se má použít pro načítání dat z centra událostí. Pokud není zadána žádná skupina příjemců, úloha Stream Analytics používá výchozí skupina příjemců. Doporučujeme vám, že používáte skupinu odlišné příjemce pro každou úlohu služby Stream Analytics. |
| **Formát serializace událostí** |Formát serializace (JSON, CSV nebo Avro) příchozí datový proud. |
| **Kódování** | Znakové sady UTF-8 je aktuálně jediným podporovaným formátem kódování. |
| **Komprese** (volitelné) | Komprese typ (None, GZip a Deflate) příchozí datový proud. |

Pokud vaše data pochází z centra událostí, máte přístup k následující pole metadat v dotazu Stream Analytics:

| Vlastnost | Popis |
| --- | --- |
| **EventProcessedUtcTime** |Datum a čas zpracování události Stream Analytics. |
| **EventEnqueuedUtcTime** |Datum a čas, který událost přijala Event Hubs. |
| **ID oddílu** |ID oddílu nulovým základem pro vstupní adaptér. |

Například pomocí těchto polí, které můžete vytvořit dotaz, jako v následujícím příkladu:

````
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
````

> [!NOTE]
> Při používání centra událostí jako koncový bod pro IoT Hub trasy, dostanete pomocí medadata IoT Hub [funkce GetMetadataPropertyValue](https://msdn.microsoft.com/en-us/library/azure/mt793845.aspx).
> 

## <a name="create-data-stream-input-from-iot-hub"></a>Vytvoření vstupní datový proud dat ze služby IoT Hub
Azure Iot Hub je vysoce škálovatelná publikování a odběru na přijímač událostí optimalizovaných pro scénáře IoT.

Výchozí časové razítko události pocházející ze služby IoT hub v Stream Analytics je časové razítko, které byly přijaty události ve službě IoT hub, která je `EventEnqueuedUtcTime`. Zpracování dat jako použití časového razítka v případě, že datové části, je nutné použít datový proud [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) – klíčové slovo.

> [!NOTE]
> Pouze zprávy odeslané s `DeviceClient` vlastnost může být zpracována.
> 
> 

### <a name="consumer-groups"></a>Skupiny příjemců
Měli byste nakonfigurovat každý Stream Analytics IoT hub vstup do mají svůj vlastní skupiny příjemců. Pokud úloha obsahuje vlastní spojení nebo pokud obsahuje více vstupů, může číst některé vstup za více než jeden čtečky. Situace má dopad na počet čtenářů v skupinu jednoho příjemce. Aby nedošlo k překročení limitu Azure IoT Hub pět čtečky za skupiny příjemců na oddíl, je osvědčeným postupem určit skupinu uživatelů pro každou úlohu služby Stream Analytics.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Konfigurace služby IoT hub jako vstupní datový proud
Následující tabulka popisuje každou vlastnost v **nové vstup** okno na portálu Azure při konfiguraci služby IoT hub jako vstup.

| Vlastnost | Popis |
| --- | --- |
| **Vstupní alias** |Popisný název, který používáte v dotazu úlohy odkazovat tento vstup.|
| **Centrum IoT** |Název centra IoT, které chcete použít jako vstup. |
| **Endpoint** |Koncový bod pro službu IoT hub.|
| **Název zásady sdíleného přístupu** |Zásada sdíleného přístupu, který poskytuje přístup ke službě IoT hub. Každá zásada sdíleného přístupu má název, že je nastavená oprávnění a přístupové klíče. |
| **Sdílený přístupový klíč zásad** |Sdílený přístupový klíč použitý k autorizaci přístupu ke službě IoT hub. |
| **Skupiny příjemců** (volitelné) |Skupinu uživatelů, který se má použít pro načítání dat ze služby IoT hub. Pokud není zadána žádná skupina příjemců, úloha Stream Analytics používá výchozí skupina příjemců. Doporučujeme vám, že používáte skupinu jiný příjemce pro každou úlohu služby Stream Analytics. |
| **Formát serializace událostí** |Formát serializace (JSON, CSV nebo Avro) příchozí datový proud. |
| **Kódování** |Znakové sady UTF-8 je aktuálně jediným podporovaným formátem kódování. |
| **Komprese** (volitelné) | Komprese typ (None, GZip a Deflate) příchozí datový proud. |

Pokud vaše data pochází ze služby IoT hub, máte přístup k následující pole metadat v dotazu Stream Analytics:

| Vlastnost | Popis |
| --- | --- |
| **EventProcessedUtcTime** |Datum a čas, který byl zpracován události. |
| **EventEnqueuedUtcTime** |Datum a čas, který událost byla přijata službou IoT hub. |
| **ID oddílu** |ID oddílu nulovým základem pro vstupní adaptér. |
| **IoTHub.MessageId** | ID, které slouží ke sladění obousměrné komunikace ve IoT hub. |
| **IoTHub.CorrelationId** |ID, které se používá v odpovědi na zprávy a zpětnou vazbu týkající se služby IoT hub. |
| **IoTHub.ConnectionDeviceId** |ID ověřování používaný k odesílání této zprávy. Tato hodnota je označený na servicebound zpráv ve službě IoT hub. |
| **IoTHub.ConnectionDeviceGenerationId** |Ověřené zařízení, která byla použita pro tuto zprávu odešlete identifikátor ID generování. Tato hodnota je označený na servicebound zpráv ve službě IoT hub. |
| **IoTHub.EnqueuedTime** |Čas, kdy se zpráva byla přijata službou IoT hub. |
| **IoTHub.StreamId** |Vlastní události vlastnost přidal zařízení odesílatele. |


## <a name="create-data-stream-input-from-blob-storage"></a>Vytvoření vstupní datový proud dat z úložiště objektů Blob
Pro scénáře s velkým množstvím nestrukturovaných dat pro uložení v cloudu úložiště objektů Azure Blob nabízí cenově výhodné a škálovatelné řešení. Data v úložišti objektů Blob se obvykle považuje dat v klidovém stavu. Však může být zpracována jako datový proud podle Stream Analytics. Typický scénář v nástroji vstupy úložiště objektů Blob s Stream Analytics je zpracování protokolu. V tomto scénáři telemetrická data byla zachycena ze systému a je třeba analyzovat a zpracovat extrahovat smysluplný data.

Výchozí časové razítko události úložiště objektů Blob v Stream Analytics je časové razítko, aby bylo naposledy změněno objektu blob, což je `BlobLastModifiedUtcTime`. Zpracování dat jako použití časového razítka v případě, že datové části, je nutné použít datový proud [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) – klíčové slovo.

Sdílený svazek clusteru naformátovaný vstupy *vyžadují* řádek záhlaví definovat polí pro datovou sadu. Kromě toho všechna pole záhlaví řádků musí být jedinečný.

> [!NOTE]
> Stream Analytics nepodporuje přidávání obsahu do existujícího souboru blob. Stream Analytics se zobrazí každý soubor jenom jednou a všechny změny, ke kterým došlo v souboru po úlohy má číst data nejsou předmětem zpracování. Osvědčeným postupem je odeslání všech dat objektu blob souboru najednou a pak přidejte další události do souboru jiný, nový objekt blob.
> 

### <a name="configure-blob-storage-as-a-data-stream-input"></a>Konfigurace úložiště objektů Blob jako vstupní datový proud

Následující tabulka popisuje každou vlastnost v **nové vstup** okno na portálu Azure při konfiguraci úložiště objektů Blob jako vstup.

| Vlastnost | Popis |
| --- | --- |
| **Vstupní alias** | Popisný název, který používáte v dotazu úlohy odkazovat tento vstup. |
| **Účet úložiště** | Název účtu úložiště, kde jsou umístěny soubory objektů blob. |
| **Klíče účtu úložiště.** | Tajný klíč přidružený k účtu úložiště. |
| **kontejner** | Kontejner pro tento objekt blob vstup. Kontejnery poskytují možnost logického seskupování pro objekty BLOB uložené ve službě Microsoft Azure Blob. Při nahrávání do objektu blob ve službě úložiště objektů Blob v Azure, je nutné zadat kontejner pro tento objekt blob. |
| **Vzorek cesty** (volitelné) | Cesta k souboru používaná k nalezení objektů BLOB v rámci zadaného kontejneru. V této cestě můžete zadat jednu nebo více instancí následujících tří proměnných: `{date}`, `{time}`, nebo `{partition}`<br/><br/>Příklad 1: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>Příklad 2: `cluster1/logs/{date}`<br/><br/>`*` Znak není povolená hodnota pro předponu cestu. Jediné platné <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">objektů blob v Azure znaků</a> jsou povoleny. |
| **Formát data** (volitelné) | Pokud použijete proměnnou datum v cestě, ve které soubory jsou uspořádány formát data. Příklad: `YYYY/MM/DD` |
| **Formát času** (volitelné) |  Pokud použijete proměnnou čas ve formátu času, ve které soubory jsou uspořádány do cesty. Aktuálně je jediná podporovaná hodnota `HH`. |
| **Formát serializace událostí** | Formát serializace (JSON, CSV nebo Avro) pro příchozí streamy. |
| **Kódování** | Pro sdílený svazek clusteru a JSON je jediným podporovaným formátem kódování aktuálně UTF-8. |
| **Komprese** (volitelné) | Komprese typ (None, GZip a Deflate) příchozí datový proud. |

Pokud vaše data pocházejí z zdroje úložiště objektů Blob, máte přístup na následující pole metadat v dotazu Stream Analytics:

| Vlastnost | Popis |
| --- | --- |
| **BlobName** |Název vstupního objektu blob, který událost pochází. |
| **EventProcessedUtcTime** |Datum a čas zpracování události Stream Analytics. |
| **BlobLastModifiedUtcTime** |Datum a čas poslední změny objektu blob. |
| **ID oddílu** |ID oddílu nulovým základem pro vstupní adaptér. |

Například pomocí těchto polí, které můžete vytvořit dotaz, jako v následujícím příkladu:

````
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
````

## <a name="get-help"></a>Podpora
Pro další pomoc, vyzkoušejte naše [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další postup
Když jste se naučili o možnosti připojení dat v Azure pro úlohy Stream Analytics. Další informace o Stream Analytics najdete v tématu:

* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
