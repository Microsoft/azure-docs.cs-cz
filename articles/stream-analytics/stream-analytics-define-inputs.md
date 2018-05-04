---
title: Datový proud dat jako vstup do Azure Stream Analytics
description: Další informace o nastavení připojení dat v Azure Stream Analytics. Vstupy zahrnout datový proud z událostí a také referenční data.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/27/2018
ms.openlocfilehash: 2b2ef68622f96d87a25d203d3d67aa0877397072
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2018
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Datový proud dat jako vstup do služby Stream Analytics

Stream Analytics obsahuje prvotřídní integraci s Azure datové proudy jako vstupy z tři typy prostředků:
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 

Tyto prostředky vstupní může existovat ve stejné předplatné jako váš úlohy služby Stream Analytics, nebo z jiného předplatného.

### <a name="compression"></a>Komprese
Stream Analytics podporuje kompresi mezi všechny vstupní zdroje dat datového proudu. Odkaz na aktuálně podporované typy jsou: None, GZip a kompresi Deflate. Podpora pro kompresi není k dispozici pro referenční data. Pokud vstupní formát Avro data, která je komprimován, zpracuje se transparentně. Nemusíte určit typ komprese s Avro serializace. 

## <a name="create-or-edit-inputs"></a>Vytvořte nebo upravte vstupy
Vytvoření nové vstupy a seznam nebo upravit existující vstupy na vaše úloha streamování, můžete na portálu Azure:
1. Otevřete [portál Azure](https://portal.azure.com) vyhledejte a vyberte úlohu služby Stream Analytics.
2. Vyberte **vstupy** možnost pod **nastavení** záhlaví. 
4. **Vstupy** stránka obsahuje seznam všech existujících vstupy. 
5. Na **vstupy** vyberte **přidat vstup datového proudu** nebo **přidat odkaz na vstup** chcete otevřít stránku Podrobnosti.
6. Vyberte stávající vstup a upravte podrobnosti vyberte **Uložit** upravit stávající vstup.
7. Vyberte **Test** na stránce vstupní podrobnosti a ověřte, zda jsou možnosti připojení platný a v provozu. 
8. Klikněte pravým tlačítkem na název stávající vstup a vyberte **vzorová data ze vstupu** podle potřeby pro další testování.


## <a name="stream-data-from-event-hubs"></a>Datový proud dat ze služby Event Hubs

Azure Event Hubs poskytuje vysoce škálovatelné publikování a odběru ingestors událostí. Centra událostí můžete shromáždit miliony událostí za sekundu, takže umožňuje zpracovávat a analyzovat masivní objemy dat vytvářených připojených zařízení a aplikací. Služba Event Hubs a Stream Analytics společně poskytují-komplexní řešení pro analýzu v reálném čase. Služba Event Hubs umožňují kanálu události do Azure v reálném čase a úlohy Stream Analytics může zpracovat události v reálném čase. Například odeslat klikne na web, odečty snímačů nebo online protokolu událostí do centra událostí. Potom můžete vytvořit úlohy Stream Analytics používat služby Event Hubs jako vstupní datové proudy v reálném čase filtrování, agregace a korelace.

Výchozí časové razítko události pocházející ze služby Event Hubs v Stream Analytics je časové razítko, které události byly přijaty události rozbočovače, který je `EventEnqueuedUtcTime`. Zpracování dat jako použití časového razítka v případě, že datové části, je nutné použít datový proud [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) – klíčové slovo.

### <a name="consumer-groups"></a>Skupiny příjemců
Měli byste nakonfigurovat každý centra událostí Stream Analytics vstup do mají svůj vlastní skupiny příjemců. Pokud úloha obsahuje vlastní spojení nebo pokud obsahuje více vstupů, může číst některé vstup za více než jeden čtečky. Situace má dopad na počet čtenářů v skupinu jednoho příjemce. Aby nedošlo k překročení limitu služby Event Hubs pět čtečky za skupiny příjemců na oddíl, je osvědčeným postupem určit skupinu uživatelů pro každou úlohu služby Stream Analytics. Je také maximální počet 20 skupiny příjemců za centra událostí. Další informace najdete v tématu [řešení potíží s Azure Stream Analytics s příjemců u centra událostí](stream-analytics-event-hub-consumer-groups.md).

### <a name="stream-data-from-event-hubs"></a>Datový proud dat ze služby Event Hubs
Následující tabulka popisuje každou vlastnost **nové vstup** na portálu Azure do vstupní datový proud dat z centra událostí:

| Vlastnost | Popis |
| --- | --- |
| **Vstupní alias** |Popisný název, který používáte v dotazu úlohy odkazovat tento vstup. |
| **Předplatné** | Zvolte předplatné, ve kterém existuje prostředek centra událostí. | 
| **Názvový prostor události rozbočovače** | Obor názvů Centrum událostí je kontejner sady entit pro zasílání zpráv. Při vytváření nového centra událostí vytvořit taky obor názvů. |
| **Název centra událostí** | Název centra událostí, a použít jako vstup. |
| **Název zásady centra událostí** | Zásada sdíleného přístupu, který poskytuje přístup k Centru událostí. Každá zásada sdíleného přístupu má název, že je nastavená oprávnění a přístupové klíče. Tato možnost je automaticky vyplněno v, pokud vyberete možnost určit nastavení centra událostí ručně.|
| **Skupina uživatelů centra událostí** (doporučeno) | Důrazně doporučujeme použít skupinu odlišné příjemce pro každou úlohu služby Stream Analytics. Tento řetězec identifikuje skupinu příjemců sloužící k načítání dat z centra událostí. Pokud není zadána žádná skupina příjemců, úloha Stream Analytics používá skupina uživatelů $Default.  |
| **Formát serializace událostí** | Formát serializace (JSON, CSV nebo Avro) příchozí datový proud.  Zkontrolujte formát JSON zarovnaná s specifikace a neobsahuje 0 na desetinná čísla. |
| **Kódování** | Znakové sady UTF-8 je aktuálně jediným podporovaným formátem kódování. |
| **Typ komprese události** | Typ komprese použít ke čtení příchozí datový proud, jako je například žádné (výchozí), GZip a Deflate. |

Vaše data vycházejí z datového proudu vstup centra událostí, máte přístup k následující pole metadat v dotazu Stream Analytics:

| Vlastnost | Popis |
| --- | --- |
| **EventProcessedUtcTime** |Datum a čas zpracování události Stream Analytics. |
| **EventEnqueuedUtcTime** |Datum a čas, který událost přijala Event Hubs. |
| **ID oddílu** |ID oddílu nulovým základem pro vstupní adaptér. |

Například pomocí těchto polí, které můžete vytvořit dotaz, jako v následujícím příkladu:

```sql
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
```

> [!NOTE]
> Při používání centra událostí jako koncový bod pro IoT Hub trasy, dostanete pomocí medadata IoT Hub [funkce GetMetadataPropertyValue](https://msdn.microsoft.com/library/azure/mt793845.aspx).
> 

## <a name="stream-data-from-iot-hub"></a>Datový proud dat ze služby IoT Hub
Azure Iot Hub je vysoce škálovatelná publikování a odběru na přijímač událostí optimalizovaných pro scénáře IoT.

Výchozí časové razítko události pocházející ze služby IoT Hub v Stream Analytics je časové razítko, které byly přijaty události ve službě IoT Hub, která je `EventEnqueuedUtcTime`. Zpracování dat jako použití časového razítka v případě, že datové části, je nutné použít datový proud [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) – klíčové slovo.

> [!NOTE]
> Pouze zprávy odeslané s `DeviceClient` vlastnost může být zpracována.
> 

### <a name="consumer-groups"></a>Skupiny příjemců
Měli byste nakonfigurovat každý Stream Analytics IoT Hub vstup do mají svůj vlastní skupiny příjemců. Pokud úloha obsahuje vlastní spojení nebo pokud obsahuje více vstupů, může číst některé vstup za více než jeden čtečky. Situace má dopad na počet čtenářů v skupinu jednoho příjemce. Aby nedošlo k překročení limitu Azure IoT Hub pět čtečky za skupiny příjemců na oddíl, je osvědčeným postupem určit skupinu uživatelů pro každou úlohu služby Stream Analytics.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Konfigurace služby IoT Hub jako vstupní datový proud
Následující tabulka popisuje každou vlastnost **nové vstup** na portálu Azure při konfiguraci služby IoT Hub jako vstupní datový proud.

| Vlastnost | Popis |
| --- | --- |
| **Vstupní alias** | Popisný název, který používáte v dotazu úlohy odkazovat tento vstup.|
| **Předplatné** | Zvolte předplatné, ve kterém existuje prostředek služby IoT Hub. | 
| **IoT Hub** | Název centra IoT, které chcete použít jako vstup. |
| **koncový bod** | Koncový bod pro službu IoT Hub.|
| **Název zásady sdíleného přístupu** | Zásada sdíleného přístupu, který poskytuje přístup ke službě IoT Hub. Každá zásada sdíleného přístupu má název, že je nastavená oprávnění a přístupové klíče. |
| **Sdílený přístupový klíč zásad** | Sdílený přístupový klíč použitý k autorizaci přístupu ke službě IoT Hub.  Tato možnost je automaticky vyplněno v, pokud vyberete možnost zadat nastavení centra Iot ručně. |
| **Skupina uživatelů** | Důrazně doporučujeme použít skupinu jiný příjemce pro každou úlohu služby Stream Analytics. Skupina uživatelů slouží k načítání dat ze služby IoT Hub. Pokud neurčíte jinak, Stream Analytics používá skupina uživatelů $Default.  |
| **Formát serializace událostí** | Formát serializace (JSON, CSV nebo Avro) příchozí datový proud.  Zkontrolujte formát JSON zarovnaná s specifikace a neobsahuje 0 na desetinná čísla. |
| **Kódování** | Znakové sady UTF-8 je aktuálně jediným podporovaným formátem kódování. |
| **Typ komprese události** | Typ komprese použít ke čtení příchozí datový proud, jako je například žádné (výchozí), GZip a Deflate. |


Použijete-li datový proud dat ze služby IoT Hub, máte přístup k následující pole metadat v dotazu Stream Analytics:

| Vlastnost | Popis |
| --- | --- |
| **EventProcessedUtcTime** | Datum a čas, který byl zpracován události. |
| **EventEnqueuedUtcTime** | Datum a čas, který událost byla přijata službou IoT Hub. |
| **ID oddílu** | ID oddílu nulovým základem pro vstupní adaptér. |
| **IoTHub.MessageId** | ID, které slouží ke sladění obousměrné komunikace ve IoT Hub. |
| **IoTHub.CorrelationId** | ID, které se používá v odpovědi na zprávy a zpětnou vazbu týkající se služby IoT Hub. |
| **IoTHub.ConnectionDeviceId** | ID ověřování používaný k odesílání této zprávy. Tato hodnota je označený na servicebound zpráv ve službě IoT Hub. |
| **IoTHub.ConnectionDeviceGenerationId** | Ověřené zařízení, která byla použita pro tuto zprávu odešlete identifikátor ID generování. Tato hodnota je označený na servicebound zpráv ve službě IoT Hub. |
| **IoTHub.EnqueuedTime** | Čas, kdy se zpráva byla přijata službou IoT Hub. |
| **IoTHub.StreamId** | Vlastní události vlastnost přidal zařízení odesílatele. |


## <a name="stream-data-from-blob-storage"></a>Datový proud dat z úložiště objektů Blob
Pro scénáře s velkým množstvím nestrukturovaných dat pro uložení v cloudu úložiště objektů Azure Blob nabízí cenově výhodné a škálovatelné řešení. Data v úložišti objektů Blob se obvykle považuje dat v klidovém stavu. Však data objektu blob může zpracovat jako datový proud Stream Analytics. 

Běžně používané scénář pro vstupy úložiště objektů Blob pomocí služby Stream Analytics je zpracování protokolu. V tomto scénáři telemetrie datových souborů ze systému byla zachycena a musí být analyzovat a zpracovat extrahovat smysluplný data.

Výchozí časové razítko události úložiště objektů Blob v Stream Analytics je časové razítko, aby bylo naposledy změněno objektu blob, což je `BlobLastModifiedUtcTime`. Zpracování dat jako použití časového razítka v případě, že datové části, je nutné použít datový proud [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) – klíčové slovo.

Sdílený svazek clusteru naformátovaný vstupy *vyžadují* řádek záhlaví k definování pole pro sadu dat a všechna pole záhlaví řádků musí být jedinečný.

Stream Analytics aktuálně nepodporuje deserializaci AVRO zprávy generované zaznamenávání centra událostí nebo vlastní koncový bod kontejneru úložiště Azure IoT Hub.

> [!NOTE]
> Stream Analytics nepodporuje přidávání obsahu do existujícího souboru blob. Stream Analytics se zobrazí každý soubor jenom jednou a všechny změny, ke kterým došlo v souboru po úlohy má číst data nejsou předmětem zpracování. Osvědčeným postupem je odeslání všech dat objektu blob souboru najednou a pak přidejte další události do souboru jiný, nový objekt blob.
> 

### <a name="configure-blob-storage-as-a-stream-input"></a>Konfigurace úložiště objektů Blob jako vstupní datový proud 

Následující tabulka popisuje každou vlastnost **nové vstup** na portálu Azure při konfiguraci úložiště objektů Blob jako vstupní datový proud.

| Vlastnost | Popis |
| --- | --- |
| **Vstupní alias** | Popisný název, který používáte v dotazu úlohy odkazovat tento vstup. |
| **Předplatné** | Zvolte předplatné, ve kterém existuje prostředek služby IoT Hub. | 
| **Účet úložiště** | Název účtu úložiště, kde jsou umístěny soubory objektů blob. |
| **Klíče účtu úložiště.** | Tajný klíč přidružený k účtu úložiště. Tato možnost je automaticky vyplněno v, pokud vyberete možnost určit nastavení úložiště blobů ručně. |
| **kontejner** | Kontejner pro tento objekt blob vstup. Kontejnery poskytují možnost logického seskupování pro objekty BLOB uložené ve službě Microsoft Azure Blob. Při nahrávání do objektu blob ve službě úložiště objektů Blob v Azure, je nutné zadat kontejner pro tento objekt blob. Můžete buď **použít existující** kontejneru nebo **vytvořit nový** tak, aby měl vytvořit nový kontejner.|
| **Vzorek cesty** (volitelné) | Cesta k souboru používaná k nalezení objektů BLOB v rámci zadaného kontejneru. V této cestě můžete zadat jednu nebo více instancí následujících tří proměnných: `{date}`, `{time}`, nebo `{partition}`<br/><br/>Příklad 1: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>Příklad 2: `cluster1/logs/{date}`<br/><br/>`*` Znak není povolená hodnota pro předponu cestu. Jediné platné <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">objektů blob v Azure znaků</a> jsou povoleny. |
| **Formát data** (volitelné) | Pokud použijete proměnnou datum v cestě, ve které soubory jsou uspořádány formát data. Příklad: `YYYY/MM/DD` |
| **Formát času** (volitelné) |  Pokud použijete proměnnou čas ve formátu času, ve které soubory jsou uspořádány do cesty. Aktuálně je jediná podporovaná hodnota `HH` hodin. |
| **Formát serializace událostí** | Formát serializace (JSON, CSV nebo Avro) příchozí datový proud.  Zkontrolujte formát JSON zarovnaná s specifikace a neobsahuje 0 na desetinná čísla. |
| **Kódování** | Pro sdílený svazek clusteru a JSON je jediným podporovaným formátem kódování aktuálně UTF-8. |
| **Komprese** | Typ komprese použít ke čtení příchozí datový proud, jako je například žádné (výchozí), GZip a Deflate. |

Pokud vaše data pocházejí z zdroje úložiště objektů Blob, máte přístup na následující pole metadat v dotazu Stream Analytics:

| Vlastnost | Popis |
| --- | --- |
| **BlobName** |Název vstupního objektu blob, který událost pochází. |
| **EventProcessedUtcTime** |Datum a čas zpracování události Stream Analytics. |
| **BlobLastModifiedUtcTime** |Datum a čas poslední změny objektu blob. |
| **ID oddílu** |ID oddílu nulovým základem pro vstupní adaptér. |

Například pomocí těchto polí, které můžete vytvořit dotaz, jako v následujícím příkladu:

```sql
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
```

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Rychlý úvod: Vytvoření úlohy Stream Analytics pomocí portálu Azure](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
