---
title: Ukládání dat a příchozí přenosy do verze Preview – Azure Time Series Insights | Microsoft Docs
description: Přečtěte si o ukládání dat a příchozím přenosu v Azure Time Series Insights ve verzi Preview.
author: lyrana
ms.author: lyhughes
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/31/2019
ms.custom: seodec18
ms.openlocfilehash: f00529d00312fd6acb045de698590047f991bec7
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76714289"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Ukládání dat a příchozí přenosy v Azure Time Series Insights ve verzi Preview

Tento článek popisuje aktualizace úložiště dat a příchozí Azure Time Series Insights ve verzi Preview. Pokrývá základní strukturu úložiště, formát souboru a ID časové řady. Popisuje také základní proces příchozího přenosu dat, osvědčené postupy a aktuální omezení verze Preview.

## <a name="data-ingress"></a>Příchozí datové přenosy

Vaše Azure Time Series Insights prostředí obsahuje modul ingestování, který umožňuje shromažďovat, zpracovávat a ukládat data časových řad. Při plánování prostředí je potřeba vzít v úvahu několik důležitých informací, aby se zajistilo, že se zpracují všechna příchozí data a aby se dosáhlo vysokého rozsahu příchozího přenosu dat a minimalizovala latence příjmu (doba potřebná pro čtení a zpracování dat z události v TSI). zdroj). 

V Time Series Insights ve verzi Preview určují zásady příchozího přenosu dat, ze kterých se dají data nacházet, a jaký formát mají mít data.

### <a name="ingress-policies"></a>Zásady příchozího přenosu dat

#### <a name="event-sources"></a>Zdroj událostí

Time Series Insights Preview podporuje následující zdroje událostí:

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Time Series Insights Preview podporuje maximálně dva zdroje událostí na instanci.

> [!WARNING] 
> * Při připojování zdroje událostí k prostředí Preview se může vyskytnout vysoká počáteční latence. 
> Latence zdroje událostí závisí na počtu událostí, které jsou aktuálně ve IoT Hub nebo v centru událostí.
> * Po prvním ingestování zdrojových dat událostí se tato vysoká latence bude nacházet. Pokud chcete pokračovat v vysoké latenci, kontaktujte nás odesláním lístku podpory prostřednictvím Azure Portal.

#### <a name="supported-data-format-and-types"></a>Podporované formáty a typy dat

Azure Time Series Insights podporuje JSON s kódováním UTF8 odeslanou prostřednictvím Azure IoT Hub nebo Azure Event Hubs. 

Níže je uveden seznam podporovaných datových typů.

| Typ dat | Popis |
|-----------|------------------|-------------|
| logick      |   Datový typ, který má jednu ze dvou stavů: true nebo false.       |
| Datum a čas    |   Představuje okamžitý čas, obvykle vyjádřený jako datum a denní dobu. Hodnoty DateTime by měly být ve formátu ISO 8601.      |
| double    |   64 desetinná čárka IEEE 754 s dvojitou přesností
| řetězec    |   Textové hodnoty sestávající ze znaků Unicode.          |

#### <a name="objects-and-arrays"></a>Objekty a pole

Komplexní typy, jako je například objekty a pole, můžete odeslat jako součást datové části události, ale při uložení se data budou podléhat procesu sloučení. Další informace o tom, jak natvarovat události JSON a také podrobnosti o komplexním typu a sloučení vnořených objektů, najdete na stránce věnované [způsobu, jakým se má tvarovat JSON pro příchozí a dotaz](./time-series-insights-update-how-to-shape-events.md).


### <a name="ingress-best-practices"></a>Osvědčené postupy pro příchozí přenosy

Doporučujeme, abyste využívali následující osvědčené postupy:

* Nakonfigurujte Time Series Insights a IoT Hub nebo centrum událostí ve stejné oblasti, aby se snížila latence přijímání dat v síti.
* Vypočítejte předpokládané požadavky na škálování tak, že vypočítáte očekávanou rychlost příjmu a ověříte, že spadá do podporované sazby uvedené níže.
* Seznamte se s tím, jak optimalizovat a natvarovat data JSON, jakož i aktuální omezení ve verzi Preview. Přečtěte si, jak můžete naformátovat [JSON pro příchozí a dotaz](./time-series-insights-update-how-to-shape-events.md).

### <a name="ingress-scale-and-limitations-in-preview"></a>Škálování a omezení příchozího přenosu dat ve verzi Preview

#### <a name="per-environment-limitations"></a>Omezení podle prostředí

Míry příchozího přenosu dat se zobrazují jako faktor počtu zařízení, která jsou ve vaší organizaci, četnosti emisí událostí a velikosti jednotlivých událostí:

*  **Počet zařízení** × **četnost měření událostí** × **Velikost každé události**.

Ve výchozím nastavení může Time Series Insights Preview ingestovat příchozí data rychlostí až 1 MB za sekundu (MB/s) **na jedno prostředí TSI**. Kontaktujte nás, pokud nesplňuje vaše požadavky, můžeme pro prostředí podporovat až 16 MB/s, a to odesláním lístku podpory v Azure Portal.
 
Příklad 1: expedice společnosti Contoso má 100 000 zařízení, která generují události třikrát za minutu. Velikost události je 200 bajtů. Používají centrum událostí se 4 oddíly jako zdroj událostí TSI.
Rychlost příjmu pro své prostředí TSI by byla: 100 000 zařízení * 200 bajtů/událost * (3/60 události/s) = 1 MB/s.
Frekvence přijímání zpráv na oddíl by byla 0,25 MB/s.
Míra ingestování společnosti Contoso bude v rámci omezení škály verze Preview.
 
Příklad 2: analýza loďstva společnosti Contoso má 60 000 zařízení, která každou sekundu emitují událost. Používají jako zdroj událostí TSI IoT Hub 24 – počet oddílů na 4. Velikost události je 200 bajtů.
Frekvence ingestování prostředí by byla: 20 000 zařízení × 200 bajtů/událost * 1 událost/s = 4 MB/s.
Frekvence za oddíl by byla 1 MB/s.
Analýza loďstva společnosti Contoso by musela odeslat žádost do TSI prostřednictvím Azure Portal pro vyhrazené prostředí pro dosažení tohoto rozsahu.

#### <a name="hub-partitions-and-per-partition-limits"></a>Omezení oddílů centra a na oddíly

Při plánování prostředí TSI je důležité zvážit konfiguraci zdrojů událostí, které budete připojovat k TSI. Azure IoT Hub i Event Hubs využívají oddíly k povolení horizontálního škálování pro zpracování událostí.  Oddíl je seřazená posloupnost událostí, která je uložena v centru. Počet oddílů se nastaví během fáze vytváření IoT nebo Event Hubs a nedá se změnit. Další informace o určení počtu oddílů najdete v Event Hubs "Nejčastější dotazy, kolik oddílů potřebuji? Pro prostředí TSI, která používají IoT Hub, obecně většina služby IoT Hub potřebuje 4 oddíly. Bez ohledu na to, jestli vytváříte nové centrum pro prostředí TSI, nebo už používáte stávající, budete muset vypočítat sazbu ingestování na oddíly, abyste zjistili, jestli se nachází v omezeních verze Preview. Verze TSI Preview má v současné době omezení **na oddíly** 0,5 MB/s. Použijte níže uvedené příklady jako referenci a vezměte v úvahu následující IoT Hub, pokud jste IoT Hub uživatel.

#### <a name="iot-hub-specific-considerations"></a>Předpoklady týkající se IoT Hub

Když se v IoT Hub vytvoří zařízení, přiřadí se k oddílu a přiřazení oddílu se nezmění. Díky tomu IoT Hub moci zaručit řazení událostí. To ale má vliv na TSI jako čtecí modul pro příjem dat v určitých scénářích. Když se zprávy z více zařízení předají do centra pomocí stejného ID zařízení brány, dostanou do stejného oddílu, takže potenciálně překročili omezení škálování na oddíly. 

**Dopad**: Pokud se v jednom oddílu udržuje nepřetržitá míra přijímání v rámci omezení verze Preview, je možné, že čtecí modul TSI nebude předtím zachytávání, než se překročila IoT Hub doba uchovávání dat. Mohlo by dojít ke ztrátě dat.

Doporučujeme následující: 

* Výpočet míry příjmu na jednotlivé prostředí a na oddíly před nasazením řešení
* Ujistěte se, že vaše zařízení IoT Hub (a tedy oddíly) jsou vyváženě vyrovnaný k nejblíže možnému prodloužení

> [!WARNING]
> Pro prostředí, která používají IoT Hub jako zdroj události, vypočítejte rychlost příjmu pomocí počtu používaných zařízení centra, aby se zajistilo, že frekvence klesne pod omezení 0,5 MB/s na oddíly ve verzi Preview.

  ![Diagram IoT Hubho oddílu](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Další informace o jednotkách a oddílech propustnosti najdete na následujících odkazech:

* [Škálování IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Škálování centra událostí](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Oddíly centra událostí](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>Úložiště dat

Když vytvoříte prostředí Time Series Insights Preview s průběžnými platbami podle aktuálního využití, vytvoříte dva prostředky Azure:

* Prostředí Time Series Insights ve verzi Preview, které může volitelně zahrnovat možnosti pro teplé úložiště.
* Účet blob Azure Storage pro obecné účely V1 pro úložiště studených dat.

Data v teplém úložišti jsou k dispozici pouze v [dotazech Time Series](./time-series-insights-update-tsq.md) a v [Průzkumníku služby Azure Time Series Insights Preview](./time-series-insights-update-explorer.md). 

Time Series Insights Preview uloží data z chladírny do úložiště objektů BLOB v Azure ve [formátu souboru Parquet](#parquet-file-format-and-folder-structure). Time Series Insights Preview spravuje tato data studeného úložiště výhradně, ale je k dispozici pro čtení přímo jako standardních souborů Parquet.

> [!WARNING]
> Jako vlastník účtu služby Azure Blob Storage, ve kterém jsou uložená data, máte plný přístup ke všem datům v účtu. Tento přístup zahrnuje oprávnění k zápisu a odstraňování. Neupravujte ani neodstraňujte data, která Time Series Insights náhled zapisuje, protože to může způsobit ztrátu dat.

### <a name="data-availability"></a>Dostupnost dat

Time Series Insights zobrazovat náhled oddílů a data indexů pro optimální výkon dotazů. Data budou k dispozici pro dotaz po jejím indexování. Množství dat, která se ingestují, můžou ovlivnit tuto dostupnost.

> [!IMPORTANT]
> Během období Preview se můžete setkat až 60 sekund, než budou data k dispozici. Pokud se setkáte s významnou latencí delší než 60 sekund, odešlete prosím lístek podpory prostřednictvím Azure Portal.

## <a name="azure-storage"></a>Azure Storage

Tato část popisuje Azure Storage detailů týkajících se Azure Time Series Insights verze Preview.

Podrobný popis úložiště objektů BLOB v Azure najdete v [úvodu do objektů BLOB úložiště](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>Váš účet úložiště

Když vytvoříte prostředí s průběžnými platbami Time Series Insights Preview, vytvoří se účet blob Azure Storage pro obecné účely v1 jako váš dlouhodobý chladírenský sklad.  

Time Series Insights Preview publikuje až dvě kopie každé události v účtu Azure Storage. Počáteční kopie má události seřazené podle času ingestování a je vždycky zachovaná, takže k ní můžete použít jiné služby. Pro zpracování nezpracovaných souborů Parquet můžete použít Spark, Hadoop a další známé nástroje. 

Time Series Insights Preview přerozdělení souborů Parquet na optimalizaci pro Time Series Insights dotaz. Tato kopie dat se také ukládá do oddílů.

Během veřejné verze Preview jsou data ve vašem účtu Azure Storage ukládána po neomezenou dobu.

#### <a name="writing-and-editing-time-series-insights-blobs"></a>Zápis a úpravy objektů BLOB Time Series Insights

Aby se zajistil výkon dotazů a dostupnost dat, neupravujte ani neodstraňujte žádné objekty blob, které Time Series Insights vytváření Preview.

#### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Přístup k datům z Time Series Insights Preview a jejich export

Můžete chtít získat přístup k datům zobrazeným v Průzkumníkovi služby Time Series Insights Preview pro použití ve spojení s jinými službami. Data můžete například použít k sestavení sestavy v Power BI nebo ke školení modelu strojového učení pomocí Azure Machine Learning Studio. Nebo můžete data použít k transformaci, vizualizaci a modelování v Jupyter poznámkových blocích.

K datům můžete získat přístup třemi obecnými způsoby:

* Z Průzkumníka služby Time Series Insights Preview. Data můžete z Průzkumníka exportovat jako soubor CSV. Další informace najdete v tématu [Time Series Insights Exploreru pro náhled](./time-series-insights-update-explorer.md).
* Z rozhraní API pro náhled Time Series Insights použijte dotaz načíst události. Pokud chcete získat další informace o tomto rozhraní API, dotaz pro čtení [časové řady](./time-series-insights-update-tsq.md).
* Přímo z účtu Azure Storage. Potřebujete přístup pro čtení k libovolnému účtu, který používáte pro přístup k datům ve verzi Preview Time Series Insights. Další informace najdete v článku [Správa přístupu k prostředkům účtu úložiště](../storage/blobs/storage-manage-access-to-resources.md).

#### <a name="data-deletion"></a>Odstranění dat

Neodstraňujte soubory ve verzi Preview Time Series Insights. Spravujte související data jenom z Time Series Insights jenom ve verzi Preview.

### <a name="parquet-file-format-and-folder-structure"></a>Formát souboru a struktura složek Parquet

Parquet je open source formát sloupcového souboru, který byl navržen pro efektivní úložiště a výkon. Time Series Insights Preview v těchto případech používá Parquet. Rozdělí data podle ID časové řady pro výkon dotazů ve velkém měřítku.  

Další informace o typu souboru Parquet najdete v [dokumentaci k Parquet](https://parquet.apache.org/documentation/latest/).

Time Series Insights Preview ukládá kopie vašich dat následujícím způsobem:

* První, počáteční kopie je dělená časem ingestování a ukládá data přibližně v pořadí doručení. Data se nachází ve složce `PT=Time`:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* Druhá, znovu rozdělená kopie je rozdělená na oddíly s ID časových řad a nachází se ve složce `PT=TsId`:

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

V obou případech časové hodnoty odpovídají času vytvoření objektu BLOB. Data ve složce `PT=Time` jsou zachována. Data ve složce `PT=TsId` budou optimalizována pro dotazy v průběhu času a nebudou zůstat statická.

> [!NOTE]
> * `<YYYY>` se mapuje na vyjádření čtyřmístného roku.
> * `<MM>` se mapuje na dvouciferné měsíční vyjádření.
> * `<YYYYMMDDHHMMSSfff>` se mapuje na časovou reprezentaci se čtyřmi číslicemi (`YYYY`), dvoumístný měsíc (`MM`), dvoumístný den (`DD`), dvě číslice (`HH`), dvě číslice (`MM`), dvě číslice (`SS`) a tři číslice milisekundy (`fff`).

Události ve verzi Preview Time Series Insights jsou namapovány na obsah souboru Parquet následujícím způsobem:

* Každá událost je mapována na jeden řádek.
* Každý řádek obsahuje sloupec **časového razítka** s časovým razítkem události. Vlastnost časového razítka není nikdy null. Ve výchozím nastavení je to čas zařazený do **fronty** , pokud není ve zdroji událostí zadána vlastnost časového razítka. Časové razítko je vždy v UTC.
* Každý řádek obsahuje sloupce s ID časové řady, jak jsou definovány při vytváření Time Series Insights prostředí. Název vlastnosti zahrnuje příponu `_string`.
* Všechny ostatní vlastnosti odeslané jako data telemetrie jsou namapovány na názvy sloupců, které končí na `_string` (String), `_bool` (Boolean), `_datetime` (DateTime) nebo `_double` (Double) v závislosti na typu vlastnosti.
* Toto schéma mapování platí pro první verzi formátu souboru, na kterou se odkazuje jako **V = 1**. Jak se tato funkce vyvíjí, může se tento název zvýšit.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, [jak pro příchozí a dotaz](./time-series-insights-update-how-to-shape-events.md)naformátovat JSON.

- Přečtěte si o nových [datových modelování](./time-series-insights-update-tsm.md).
