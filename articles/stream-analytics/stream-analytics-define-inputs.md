---
title: Streamování dat jako vstup do Azure Stream Analytics
description: Přečtěte si o nastavení datového připojení v Azure Stream Analytics. Vstupy zahrnují datový proud z událostí a také referenční data.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2020
ms.openlocfilehash: fb5aca1739fbb4a77cbcb7eed6b9dce1b3ccc182
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "93027580"
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Streamování dat jako vstup do Stream Analytics

Stream Analytics má prvotřídní integraci s datovými proudy Azure jako vstupy ze tří druhů prostředků:

- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 

Tyto vstupní prostředky můžou být živé ve stejném předplatném Azure jako vaše úloha Stream Analytics nebo jiné předplatné.

### <a name="compression"></a>Komprese

Stream Analytics podporuje kompresi napříč všemi vstupními zdroji datových proudů. Podporované typy komprese jsou: None, GZip a Deflate Compression. Podpora komprese není pro referenční data k dispozici. Pokud je vstupní formát Avro dat, která jsou komprimovaná, bude zpracována transparentně. Nemusíte určovat typ komprese s Avro serializací. 

## <a name="create-edit-or-test-inputs"></a>Vytváření, úpravy a testování vstupů

Pomocí [Azure Portal](stream-analytics-quick-create-portal.md), sady [Visual Studio](stream-analytics-quick-create-vs.md)a [Visual Studio Code](quick-create-visual-studio-code.md) můžete přidat a zobrazit nebo upravit existující vstupy v úloze streamování. Vstupní připojení a [testovací dotazy](stream-analytics-manage-job.md#test-your-query) můžete také testovat z ukázkových dat z Azure Portal, sady [Visual Studio](stream-analytics-vs-tools-local-run.md)a [Visual Studio Code](visual-studio-code-local-run.md). Při psaní dotazu se zobrazí seznam vstupů v klauzuli FROM. Seznam dostupných vstupů můžete získat na stránce **dotaz** na portálu. Pokud chcete použít více vstupů, můžete `JOIN` je nebo napsat více `SELECT` dotazů.


## <a name="stream-data-from-event-hubs"></a>Streamování dat z Event Hubs

Azure Event Hubs poskytuje vysoce škálovatelné investory o událostech publikování a odběru. Centrum událostí může shromažďovat miliony událostí za sekundu, abyste mohli zpracovávat a analyzovat obrovské objemy dat vytvářených zařízeními a aplikacemi připojenými k vaší síti. Společně Event Hubs a Stream Analytics poskytují ucelené řešení pro analýzy v reálném čase. Event Hubs umožňuje zasílat události do Azure v reálném čase a úlohy Stream Analytics můžou tyto události zpracovat v reálném čase. Můžete například odeslat webové kliknutí, čtení ze senzorů nebo online události protokolu a Event Hubs. Pak můžete vytvořit Stream Analytics úlohy, které budou používat Event Hubs jako vstupní datové proudy pro filtrování, agregaci a korelaci v reálném čase.

`EventEnqueuedUtcTime` je časové razítko příjezdu události v centru událostí a je výchozím časovým razítkem událostí přicházejících z Event Hubs do Stream Analytics. Chcete-li zpracovat data jako datový proud pomocí časového razítka v datové části události, je nutné použít klíčové slovo [timestamp by](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) .

### <a name="event-hubs-consumer-groups"></a>Event Hubs skupiny příjemců

Každý Stream Analytics vstup centra událostí byste měli nakonfigurovat tak, aby měl svou vlastní skupinu uživatelů. Pokud úloha obsahuje více vstupů nebo má více vstupů, mohou být některé vstupy čteny více než jedním čtecím modulem pro čtení. Tato situace má vliv na počet čtenářů v jedné skupině příjemců. Abyste se vyhnuli překročení Event Hubs limitu pěti čtenářů na skupinu uživatelů na oddíl, je osvědčeným postupem určení skupiny uživatelů pro každou úlohu Stream Analytics. K dispozici je také limit 20 skupin uživatelů pro centrum událostí úrovně Standard. Další informace najdete v tématu [řešení potíží s Azure Stream Analytics vstupy](stream-analytics-troubleshoot-input.md).

### <a name="create-an-input-from-event-hubs"></a>Vytvoření vstupu z Event Hubs

Následující tabulka vysvětluje jednotlivé vlastnosti na **nové vstupní** stránce v Azure Portal ke streamování vstupu dat z centra událostí:

| Vlastnost | Popis |
| --- | --- |
| **Alias vstupu** |Popisný název, který použijete v dotazu úlohy pro odkazování na tento vstup. |
| **Předplatné** | Vyberte předplatné, ve kterém prostředek centra událostí existuje. | 
| **Obor názvů centra událostí** | Obor názvů centra událostí je kontejner pro sadu entit zasílání zpráv. Když vytváříte nové centrum událostí, vytvoříte také obor názvů. |
| **Název centra událostí** | Název centra událostí, které se má použít jako vstup |
| **Název zásad centra událostí** | Zásady sdíleného přístupu, které poskytují přístup k centru událostí. Každá zásada sdíleného přístupu má název, oprávnění, která jste nastavili, a přístupové klíče. Tato možnost se vyplní automaticky, pokud nevyberete možnost zadat nastavení centra událostí ručně.|
| **Skupina uživatelů centra událostí** (doporučeno) | Důrazně doporučujeme použít pro každou úlohu Stream Analytics odlišnou skupinu uživatelů. Tento řetězec identifikuje skupinu uživatelů, která se má použít k ingestování dat z centra událostí. Pokud není zadána žádná skupina příjemců, úloha Stream Analytics používá skupinu uživatelů $Default.  |
| **Klíč oddílu** | Toto volitelné pole je dostupné jenom v případě, že je vaše úloha nakonfigurovaná na používání [úrovně kompatibility](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level) 1,2 nebo vyšší. Pokud je vstup rozdělený pomocí vlastnosti, můžete sem přidat název této vlastnosti. Tato metoda se používá ke zvýšení výkonu dotazu, pokud obsahuje klauzuli PARTITION BY nebo GROUP BY pro tuto vlastnost. Pokud tato úloha používá úroveň kompatibility 1,2 nebo vyšší, toto pole je standardně nastavené na "PartitionId". |
| **Formát serializace události** | Formát serializace (JSON, CSV, Avro nebo [jiný (Protobuf, XML, proprietární...)](custom-deserializer.md)) příchozího datového proudu.  Ujistěte se, že formát JSON se zarovnává se specifikací a neobsahuje úvodní číslo 0 pro desetinná čísla. |
| **Kódování** | Kódování UTF-8 je aktuálně jediným podporovaným formátem kódování. |
| **Typ komprese události** | Typ komprese používaný ke čtení příchozího datového proudu, jako je například None (výchozí), GZip nebo deflate. |

Když data pocházejí z datového proudu centra událostí, máte v Stream Analytics dotazu přístup k následujícím polím metadat:

| Vlastnost | Popis |
| --- | --- |
| **EventProcessedUtcTime** |Datum a čas, kdy byla událost zpracována Stream Analytics. |
| **EventEnqueuedUtcTime** |Datum a čas přijetí události Event Hubs. |
| **Oddílu** |IDENTIFIKÁTOR oddílu založeného na nule pro vstupní adaptér. |

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

## <a name="stream-data-from-iot-hub"></a>Streamování dat z IoT Hub

Azure IoT Hub je vysoce škálovatelný ingestování událostí publikování a odběru optimalizované pro scénáře IoT.

Výchozím časovým razítkem událostí přicházejících z IoT Hub v Stream Analytics je časové razítko, které událost dorazila do IoT Hub, což je `EventEnqueuedUtcTime` . Chcete-li zpracovat data jako datový proud pomocí časového razítka v datové části události, je nutné použít klíčové slovo [timestamp by](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) .

### <a name="iot-hub-consumer-groups"></a>Skupiny uživatelů centra IoT Hub

Každý Stream Analytics IoT Hub vstupu byste měli nakonfigurovat tak, aby měl svou vlastní skupinu uživatelů. Pokud úloha obsahuje více vstupů, nebo pokud má více vstupů, může být určitý vstup čten více než jedním čtecím modulem. Tato situace má vliv na počet čtenářů v jedné skupině příjemců. Aby nedošlo k překročení limitu služby Azure IoT Hub po dobu pěti čtenářů na jeden oddíl, je osvědčeným postupem určení skupiny uživatelů pro každou úlohu Stream Analytics.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Konfigurace IoT Hub jako vstupu datového proudu

V následující tabulce jsou popsány jednotlivé vlastnosti na **nové vstupní** stránce v Azure Portal při konfiguraci IoT Hub jako vstupu datového proudu.

| Vlastnost | Popis |
| --- | --- |
| **Alias vstupu** | Popisný název, který použijete v dotazu úlohy pro odkazování na tento vstup.|
| **Předplatné** | Vyberte předplatné, ve kterém existuje IoT Hub prostředek. | 
| **IoT Hub** | Název IoT Hub, který se má použít jako vstup |
| **Koncový bod** | Koncový bod pro IoT Hub.|
| **Název zásady sdíleného přístupu** | Zásada sdíleného přístupu, která poskytuje přístup k IoT Hub. Každá zásada sdíleného přístupu má název, oprávnění, která jste nastavili, a přístupové klíče. |
| **Klíč zásad sdíleného přístupu** | Sdílený přístupový klíč, který slouží k autorizaci přístupu k IoT Hub.  Tato možnost se vyplní automaticky, pokud nevyberete možnost zadat nastavení centra IoT Hub ručně. |
| **Skupina uživatelů** | Důrazně doporučujeme pro každou úlohu Stream Analytics použít jinou skupinu uživatelů. Skupina příjemců slouží k ingestování dat z IoT Hub. Stream Analytics používá skupinu příjemců $Default, pokud neurčíte jinak.  |
| **Klíč oddílu** | Toto volitelné pole je dostupné jenom v případě, že je vaše úloha nakonfigurovaná na používání [úrovně kompatibility](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level) 1,2 nebo vyšší. Pokud je vstup rozdělený pomocí vlastnosti, můžete sem přidat název této vlastnosti. Tato metoda se používá ke zvýšení výkonu dotazu, pokud obsahuje klauzuli PARTITION BY nebo GROUP BY pro tuto vlastnost. Pokud tato úloha používá úroveň kompatibility 1,2 nebo vyšší, toto pole je standardně nastavené na "PartitionId". |
| **Formát serializace události** | Formát serializace (JSON, CSV, Avro nebo [jiný (Protobuf, XML, proprietární...)](custom-deserializer.md)) příchozího datového proudu.  Ujistěte se, že formát JSON se zarovnává se specifikací a neobsahuje úvodní číslo 0 pro desetinná čísla. |
| **Kódování** | Kódování UTF-8 je aktuálně jediným podporovaným formátem kódování. |
| **Typ komprese události** | Typ komprese používaný ke čtení příchozího datového proudu, jako je například None (výchozí), GZip nebo deflate. |


Pokud používáte data datového proudu z IoT Hub, máte v dotazu Stream Analytics přístup k následujícím polím metadat:

| Vlastnost | Popis |
| --- | --- |
| **EventProcessedUtcTime** | Datum a čas, kdy byla událost zpracována. |
| **EventEnqueuedUtcTime** | Datum a čas přijetí události IoT Hub. |
| **Oddílu** | IDENTIFIKÁTOR oddílu založeného na nule pro vstupní adaptér. |
| **IoTHub. MessageId** | ID, které slouží ke korelaci obousměrné komunikace v IoT Hub. |
| **IoTHub. ID korelace** | ID, které se používá v odpovědích zpráv a zpětná vazba v IoT Hub. |
| **IoTHub. ConnectionDeviceId** | ID ověřování použité k odeslání této zprávy. Tato hodnota je označena servicebound zprávami IoT Hub. |
| **IoTHub. ConnectionDeviceGenerationId** | ID generování ověřeného zařízení, které bylo použito k odeslání této zprávy. Tato hodnota je označena servicebound zprávami IoT Hub. |
| **IoTHub. EnqueuedTime** | Čas, kdy IoT Hub přijal zprávu. |


## <a name="stream-data-from-blob-storage"></a>Streamování dat z úložiště objektů BLOB
Pro scénáře s velkým množstvím nestrukturovaných dat, která se mají ukládat v cloudu, Azure Blob Storage nabízí nákladově efektivní a škálovatelné řešení. Data v úložišti objektů BLOB se obvykle považují za neaktivní data. data objektů BLOB se ale dají zpracovat jako datový proud pomocí Stream Analytics. 

Zpracování protokolu je běžně používaný scénář pro použití vstupů služby Blob Storage s Stream Analytics. V tomto scénáři byly datové soubory telemetrie zachyceny ze systému a musí být analyzovány a zpracovány pro extrakci smysluplných dat.

Výchozím časovým razítkem událostí služby Blob Storage v Stream Analytics je časové razítko, které bylo naposledy změněno v objektu BLOB `BlobLastModifiedUtcTime` . Pokud se objekt BLOB nahraje do účtu úložiště v 13:00 a úloha Azure Stream Analytics se spustí pomocí možnosti *Now* (13:01), objekt BLOB se nevybere, protože čas změny spadá mimo dobu běhu úlohy.

Pokud se objekt BLOB nahraje do kontejneru účtu úložiště v 13:00 a úloha Azure Stream Analytics se spustí s využitím *vlastního času* v 13:00 nebo dřívějším, bude se tento objekt BLOB vyzvednout, protože čas změny spadá do doby běhu úlohy.

Pokud se úloha Azure Stream Analytics začala používat *hned* v 13:00 a do kontejneru účtu úložiště se nahraje objekt blob na 13:01, Azure Stream Analytics si tento objekt BLOB zachová. Časové razítko přiřazené ke každému objektu BLOB je založeno pouze na `BlobLastModifiedTime` . Složka, ve které je objekt blob, nemá žádnou relaci s přiřazeným časovým razítkem. Pokud je například objekt BLOB *2019/10-01/00/b1.txt* s `BlobLastModifiedTime` 2019-11-11m, pak časové razítko přiřazené tomuto objektu BLOB je 2019-11-11.

Chcete-li zpracovat data jako datový proud pomocí časového razítka v datové části události, je nutné použít klíčové slovo [timestamp by](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) . Stream Analytics úloha vyžádá data ze vstupu Azure Blob Storage každou sekundu, pokud je k dispozici soubor BLOB. Pokud soubor BLOB není k dispozici, existuje exponenciální omezení rychlosti s maximálním časovým intervalem 90 sekund.

Vstupy ve formátu CSV vyžadují pro definování polí pro datovou sadu řádek záhlaví a všechna řádková pole záhlaví musí být jedinečná.

> [!NOTE]
> Stream Analytics nepodporuje přidávání obsahu do existujícího souboru objektů BLOB. Stream Analytics bude každý soubor zobrazovat pouze jednou a všechny změny, ke kterým došlo v souboru poté, co úloha data přečte, nejsou zpracovány. Osvědčeným postupem je nahrát všechna data pro soubor objektu BLOB najednou a pak přidat další novější události do jiného, nového souboru BLOB.

Ve scénářích, kdy se průběžně přidávají spousty objektů BLOB a Stream Analytics zpracovává objekty BLOB při jejich přidání, je možné, že některé objekty blob budou ve výjimečných případech přeskočeny kvůli členitosti `BlobLastModifiedTime` . To můžete zmírnit tím, že nahrajete objekty blob alespoň dvě sekundy od sebe. Pokud tato možnost není proveditelná, můžete použít Event Hubs ke streamování velkých objemů událostí.

### <a name="configure-blob-storage-as-a-stream-input"></a>Konfigurace úložiště objektů BLOB jako vstupu datového proudu 

V následující tabulce jsou popsány jednotlivé vlastnosti na **nové vstupní** stránce v Azure Portal, když nakonfigurujete úložiště objektů BLOB jako vstup datového proudu.

| Vlastnost | Popis |
| --- | --- |
| **Alias vstupu** | Popisný název, který použijete v dotazu úlohy pro odkazování na tento vstup. |
| **Předplatné** | Vyberte předplatné, ve kterém existuje IoT Hub prostředek. | 
| **Účet úložiště** | Název účtu úložiště, ve kterém se nacházejí soubory objektů BLOB. |
| **Klíč účtu úložiště** | Tajný klíč přidružený k účtu úložiště Tato možnost se vyplní automaticky, pokud nevyberete možnost zadat nastavení úložiště objektů BLOB ručně. |
| **Kontejner** | Kontejner pro vstup objektu BLOB Kontejnery poskytují logické seskupení pro objekty blob uložené v Blob service Microsoft Azure. Když nahrajete objekt blob do služby Azure Blob Storage, musíte pro tento objekt BLOB zadat kontejner. Můžete zvolit možnost **použít existující** kontejner nebo  **vytvořit nový** , chcete-li vytvořit nový kontejner.|
| **Vzor cesty** (volitelné) | Cesta k souboru, který se používá k vyhledání objektů BLOB v zadaném kontejneru. Pokud chcete číst objekty BLOB z kořenového adresáře kontejneru, nenastavujte vzor cesty. V cestě můžete zadat jednu nebo více instancí následujících tří proměnných: `{date}` , `{time}` nebo. `{partition}`<br/><br/>Příklad 1: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>Příklad 2: `cluster1/logs/{date}`<br/><br/>`*`Znak není povolená hodnota pro předponu cesty. Jsou povoleny pouze platné <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">znaky objektu BLOB v Azure</a> . Nezahrnujte názvy kontejnerů nebo názvy souborů. |
| **Formát data** (volitelné) | Použijete-li proměnnou data v cestě, formát data, ve kterém jsou soubory uspořádány. Příklad: `YYYY/MM/DD` <br/><br/> Pokud má vstup objektu BLOB `{date}` nebo `{time}` v jeho cestě, složky se procházejí ve vzestupném časovém pořadí.|
| **Formát času** (volitelné) |  Použijete-li časovou proměnnou v cestě, formát času, ve kterém jsou soubory uspořádány. V současné době je jediná podporovaná hodnota `HH` pro hodiny. |
| **Klíč oddílu** | Toto volitelné pole je dostupné jenom v případě, že je vaše úloha nakonfigurovaná na používání [úrovně kompatibility](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level) 1,2 nebo vyšší. Pokud je vstup rozdělený pomocí vlastnosti, můžete sem přidat název této vlastnosti. Tato metoda se používá ke zvýšení výkonu dotazu, pokud obsahuje klauzuli PARTITION BY nebo GROUP BY pro tuto vlastnost. Pokud tato úloha používá úroveň kompatibility 1,2 nebo vyšší, toto pole je standardně nastavené na "PartitionId". |
| **Počet vstupních oddílů** | Toto pole je k dispozici pouze v případě, že je ve vzoru cesty uveden oddíl {partition}. Hodnota této vlastnosti je celé číslo >= 1. Bez ohledu na to, kde {partition} se zobrazí v pathPattern, se použije číslo mezi 0 a hodnotou tohoto pole – 1. |
| **Formát serializace události** | Formát serializace (JSON, CSV, Avro nebo [jiný (Protobuf, XML, proprietární...)](custom-deserializer.md)) příchozího datového proudu.  Ujistěte se, že formát JSON se zarovnává se specifikací a neobsahuje úvodní číslo 0 pro desetinná čísla. |
| **Kódování** | V případě sdílených svazků clusteru a JSON je kódování UTF-8 aktuálně jediným podporovaným formátem kódování. |
| **Komprese** | Typ komprese používaný ke čtení příchozího datového proudu, jako je například None (výchozí), GZip nebo deflate. |

Když data pocházejí ze zdroje úložiště objektů blob, máte v Stream Analytics dotazu přístup k následujícím polím metadat:

| Vlastnost | Popis |
| --- | --- |
| **BlobName** |Název vstupního objektu blob, ze kterého pochází událost. |
| **EventProcessedUtcTime** |Datum a čas, kdy byla událost zpracována Stream Analytics. |
| **BlobLastModifiedUtcTime** |Datum a čas poslední změny objektu BLOB |
| **Oddílu** |IDENTIFIKÁTOR oddílu založeného na nule pro vstupní adaptér. |

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
> [Rychlý start: Vytvoření úlohy Stream Analytics pomocí webu Azure Portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
