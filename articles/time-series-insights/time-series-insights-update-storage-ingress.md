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
ms.date: 04/27/2020
ms.custom: seodec18
ms.openlocfilehash: e3af10e5e9b56b537fedf0af7ffa7ddb37030c73
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82189177"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Ukládání dat a příchozí přenosy v Azure Time Series Insights ve verzi Preview

Tento článek popisuje aktualizace úložiště dat a příchozí Azure Time Series Insights ve verzi Preview. Popisuje základní strukturu úložiště, formát souboru a ID časové řady. Jsou popsány také základní procesy příchozího přenosu dat, osvědčené postupy a omezení aktuální verze Preview.

## <a name="data-ingress"></a>Příchozí datové přenosy

Vaše Azure Time Series Insights prostředí obsahuje *modul* ingestování, který umožňuje shromažďovat, zpracovávat a ukládat data časových řad.

Je třeba mít na vědomí několik důležitých informací, abyste zajistili, že se zpracovávají všechna příchozí data, aby se dosáhlo vysokého rozsahu příchozího přenosu dat a minimalizovala *latence* příjmu (doba trvání Time Series Insights čtení a zpracování dat ze zdroje událostí) při [plánování prostředí](time-series-insights-update-plan.md).

Time Series Insights ve verzi Preview zásady příchozího přenosu dat určují, z jakých dat se dají data nacházet, a jaký formát mají mít data.

### <a name="ingress-policies"></a>Zásady příchozího přenosu dat

*Data* příchozího přenosu dat zahrnují způsob, jakým se data odesílají do prostředí Azure Time Series Insights ve verzi Preview.

Klíčová konfigurace, formátování a osvědčené postupy jsou shrnuty níže.

#### <a name="event-sources"></a>Zdroje událostí

Azure Time Series Insights Preview podporuje následující zdroje událostí:

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Azure Time Series Insights Preview podporuje maximálně dva zdroje událostí na instanci. Když připojíte zdroj události, vaše prostředí TSI načte všechny události, které jsou aktuálně uložené ve službě IoT nebo v centru událostí, počínaje nejstarší událostí.

> [!IMPORTANT]
>
> * Při připojování zdroje událostí k prostředí Preview se může vyskytnout vysoká počáteční latence.
> Latence zdroje událostí závisí na počtu událostí, které jsou aktuálně ve IoT Hub nebo v centru událostí.
> * Po prvním ingestování zdrojových dat událostí se tato vysoká latence bude nacházet. Pokud se setkáte s probíhající vysokou latencí, odešlete lístek podpory prostřednictvím Azure Portal.

#### <a name="supported-data-format-and-types"></a>Podporované formáty a typy dat

Azure Time Series Insights podporuje JSON zakódovaný v kódování UTF-8, který je odesílán z Azure IoT Hub nebo Azure Event Hubs. 

Podporované datové typy jsou:

| Datový typ | Popis |
|---|---|
| **bool** | Datový typ, který má jeden ze dvou `true` stavů `false`: nebo. |
| **Hodnotu** | Představuje okamžitý čas, obvykle vyjádřený jako datum a denní dobu. Vyjádřeno ve formátu [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) . |
| **double** | 64 desetinná čárka [IEEE 754](https://ieeexplore.ieee.org/document/8766229) s dvojitou přesností. |
| **řetězec** | Textové hodnoty sestávající ze znaků Unicode.          |

#### <a name="objects-and-arrays"></a>Objekty a pole

Můžete odesílat komplexní typy, jako jsou objekty a pole, jako součást datové části události, ale při uložení se na vaše data dostanou sloučit procesy.

Podrobné informace, které popisují, jak natvarovat události JSON, odeslat složitý typ a Sloučit vnořené objekty, jsou k dispozici v tématu [How to forming JSON for příchozí a Query](./time-series-insights-update-how-to-shape-events.md) pro pomoc s plánováním a optimalizací.

### <a name="ingress-best-practices"></a>Osvědčené postupy pro příchozí přenosy

Doporučujeme, abyste využívali následující osvědčené postupy:

* Nakonfigurujte Azure Time Series Insights a jakékoli IoT Hub nebo centrum událostí ve stejné oblasti, abyste snížili možnou latenci.

* Vypočítejte [požadavky na škálování](time-series-insights-update-plan.md) tak, že vypočítáte očekávanou rychlost příjmu a ověříte, že spadá do podporované sazby uvedené níže.

* Seznamte se s tím, jak optimalizovat a natvarovat data JSON, jakož i aktuální omezení ve verzi Preview. Přečtěte si, jak můžete naformátovat [JSON pro příchozí a dotaz](./time-series-insights-update-how-to-shape-events.md).

### <a name="ingress-scale-and-preview-limitations"></a>Omezení příchozího přenosu dat a verze Preview

Omezení vstupu Azure Time Series Insights ve verzi Preview jsou popsána níže.

> [!TIP]
> Podrobný seznam všech omezení pro verzi Preview najdete v tématu [Naplánování prostředí Preview](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits) .

#### <a name="per-environment-limitations"></a>Omezení podle prostředí

Míry příchozího přenosu dat se zobrazují jako faktor počtu zařízení, která jsou ve vaší organizaci, četnosti emisí událostí a velikosti jednotlivých událostí:

*  **Počet zařízení** × **četnost měření událostí** × **Velikost každé události**.

Ve výchozím nastavení může Time Series Insights Preview ingestovat příchozí data rychlostí **až 1 MB za sekundu (MB/s) na Time Series Insights prostředí**. Existují další omezení [na oddíl centra](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress#hub-partitions-and-per-partition-limits).

> [!TIP]
>
> * Podpora prostředí pro přijímání rychlostí až 16 MB/s může být zajištěna žádostí.
> * Pokud potřebujete vyšší propustnost odesláním lístku podpory prostřednictvím Azure Portal, kontaktujte nás.
 
* **Příklad 1:**

    Při expedici společnosti Contoso je 100 000 zařízení, která generují události třikrát za minutu. Velikost události je 200 bajtů. Používají centrum IoT se čtyřmi oddíly jako zdroj události Time Series Insights.

    * Rychlost příjmu pro své Time Series Insights prostředí by byla: **100 000 zařízení * 200 bajtů/událost * (3/60 události/s) = 1 MB/s**.
    * Frekvence přijímání zpráv na oddíl by byla 0,25 MB/s.
    * Míra ingestování společnosti Contoso bude v rámci omezení škály verze Preview.

* **Příklad 2:**

    Analýza loďstva společnosti Contoso má 60 000 zařízení, která každou sekundu emitují událost. V případě Time Series Insightsho zdroje událostí používají centrum událostí s počtem oddílů 4. Velikost události je 200 bajtů.

    * Frekvence ingestování prostředí by byla: **60 000 zařízení × 200 bajtů/událost * 1 událost/s = 12 MB/** s.
    * Frekvence za oddíl by byla 3 MB/s.
    * Míra ingestování infrastruktury společnosti Contoso je nad limity prostředí a oddílů. Můžou odeslat žádost o Time Series Insights přes Azure Portal, aby se zvýšila rychlost přijímání zpráv pro své prostředí, a vytvořit centrum událostí s více oddíly, které budou v rámci limitů verze Preview.

#### <a name="hub-partitions-and-per-partition-limits"></a>Omezení oddílů centra a na oddíly

Při plánování Time Series Insightsho prostředí je důležité zvážit konfiguraci zdrojů událostí, ke kterým se budete připojovat Time Series Insights. Azure IoT Hub i Event Hubs využívají oddíly k povolení horizontálního škálování pro zpracování událostí. 

*Oddíl* je seřazená posloupnost událostí, která je držena v centru. Počet oddílů se nastaví během fáze vytváření centra a nedá se změnit.

[Informace o tom, kolik oddílů](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need) potřebuji pro Event Hubs Doporučené postupy, najdete v tématu.

> [!NOTE]
> Většina rozbočovačů IoT používaných s Azure Time Series Insights potřebuje jenom čtyři oddíly.

Bez ohledu na to, jestli vytváříte nové centrum pro Time Series Insights prostředí nebo už použijete stávající, budete muset vypočítat sazbu ingestování na oddíly, abyste zjistili, jestli se nachází v omezeních verze Preview. 

Verze Preview Azure Time Series Insights aktuálně má **omezení 0,5 MB/s na oddíly**.

#### <a name="iot-hub-specific-considerations"></a>Předpoklady týkající se IoT Hub

Při vytvoření zařízení v IoT Hub se trvale přiřadí k oddílu. V takovém případě IoT Hub moci zaručit řazení událostí (protože se přiřazení nikdy nemění).

Pevné přiřazení oddílu také ovlivňuje Time Series Insights instance, které ingestují data odesílaná z IoT Hub pro příjem dat. Když se zprávy z více zařízení předají do centra pomocí stejného ID zařízení brány, můžou dorazit do stejného oddílu ve stejnou dobu, než je omezení škálování na oddíly.

**Dopad**:

* Pokud se v jednom oddílu udržuje nepřetržitá míra přijímání v rámci limitu verze Preview, je možné, že Time Series Insights nebude synchronizovat všechny telemetrie zařízení před tím, než se překročí doba uchování dat IoT Hub. V důsledku toho může dojít ke ztrátě odeslaných dat v případě, že dojde k trvalému překročení limitů pro přijímání.

Pro zmírnění této situace doporučujeme následující osvědčené postupy:

* Před nasazením řešení Vypočítejte sazby pro ingestování na jednotlivé prostředí a na oddíly.
* Ujistěte se, že vaše zařízení IoT Hub jsou vyvážená z hlediska zatížení co nejdáleního rozsahu.

> [!IMPORTANT]
> Pro prostředí, která používají IoT Hub jako zdroj události, vypočítejte rychlost příjmu pomocí počtu používaných zařízení centra, aby se zajistilo, že frekvence klesne pod omezení 0,5 MB/s na oddíly ve verzi Preview.
>
> * I v případě, že několik událostí dorazí současně, limit verze Preview nebude překročen.

  ![Diagram IoT Hubho oddílu](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Další informace o optimalizaci propustnosti a oddílů centra najdete v následujících zdrojích informací:

* [Škálování IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Škálování centra událostí](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Oddíly centra událostí](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>Úložiště dat

Když vytvoříte prostředí Time Series Insights Preview s průběžnými *platbami* (PAYG), vytvoříte dva prostředky Azure:

* Prostředí Azure Time Series Insights ve verzi Preview, které se dá nakonfigurovat pro úložiště teplého data.
* Účet blob Azure Storage pro obecné účely V1 pro úložiště studených dat.

Data v teplém úložišti jsou k dispozici pouze v [dotazech Time Series](./time-series-insights-update-tsq.md) a v [Průzkumníku služby Azure Time Series Insights Preview](./time-series-insights-update-explorer.md). Vaše teplé úložiště bude obsahovat poslední data v rámci [doby uchování](./time-series-insights-update-plan.md#the-preview-environment) vybrané při vytváření prostředí Time Series Insights.

Time Series Insights Preview uloží data z chladírny do úložiště objektů BLOB v Azure ve [formátu souboru Parquet](#parquet-file-format-and-folder-structure). Time Series Insights Preview spravuje tato data studeného úložiště výhradně, ale je k dispozici pro čtení přímo jako standardních souborů Parquet.

> [!WARNING]
> Jako vlastník účtu služby Azure Blob Storage, ve kterém jsou uložená data, máte plný přístup ke všem datům v účtu. Tento přístup zahrnuje oprávnění k zápisu a odstraňování. Neupravujte ani neodstraňujte data, která Time Series Insights náhled zapisuje, protože to může způsobit ztrátu dat.

### <a name="data-availability"></a>Dostupnost dat

Azure Time Series Insights zobrazovat náhled oddílů a data indexů pro optimální výkon dotazů. Data budou k dispozici pro dotazy z teplého (Pokud povoleného) a studeného úložiště po jeho indexování. Množství dat, která se ingestují, můžou ovlivnit tuto dostupnost.

> [!IMPORTANT]
> Během období Preview se můžete setkat až 60 sekund, než budou data k dispozici. Pokud se setkáte s významnou latencí delší než 60 sekund, odešlete prosím lístek podpory prostřednictvím Azure Portal.

## <a name="azure-storage"></a>Azure Storage

Tato část popisuje Azure Storage detailů týkajících se Azure Time Series Insights verze Preview.

Podrobný popis úložiště objektů BLOB v Azure najdete v [úvodu do objektů BLOB úložiště](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>Váš účet úložiště

Když Azure Time Series Insights vytvoříte PAYG prostředí ve verzi Preview, vytvoří se účet blob Azure Storage pro obecné účely v1 jako váš dlouhodobý chladírenský sklad.  

Azure Time Series Insights Preview uchovává až dvě kopie každé události v účtu Azure Storage. Jedna kopie ukládá události seřazené podle času příjmu, vždy umožňuje přístup k událostem v posloupnosti seřazené podle času. V průběhu času Time Series Insights Preview také vytvoří znovu rozdělenou kopii dat, která se optimalizují pro provádění Time Series Insights dotazů.

Během veřejné verze Preview jsou data ve vašem účtu Azure Storage ukládána po neomezenou dobu.

#### <a name="writing-and-editing-time-series-insights-blobs"></a>Zápis a úpravy objektů BLOB Time Series Insights

Aby se zajistil výkon dotazů a dostupnost dat, neupravujte ani neodstraňujte žádné objekty blob, které Time Series Insights vytváření Preview.

#### <a name="accessing-time-series-insights-preview-cold-store-data"></a>Přístup k datům z chladírenského úložiště v Time Series Insights Preview

Kromě přístupu k datům z [průzkumníka Time Series Insights Preview](./time-series-insights-update-explorer.md) a [dotazu časové řady](./time-series-insights-update-tsq.md)můžete také chtít získat přístup k datům přímo ze souborů Parquet uložených v chladírenských skladech. Můžete například číst, transformovat a čistit data v Jupyter poznámkovém bloku a pak je použít ke školení modelu Azure Machine Learning ve stejném pracovním postupu Spark.

Pokud chcete získat přístup k datům přímo z účtu Azure Storage, potřebujete přístup pro čtení k účtu, který se používá k ukládání dat ve verzi Preview Time Series Insights. Následně si můžete přečíst vybraná data na základě doby vytvoření souboru Parquet ve `PT=Time` složce popsané níže v části [Formát souboru Parquet](#parquet-file-format-and-folder-structure) .  Další informace o povolení přístupu pro čtení k vašemu účtu úložiště najdete v tématu [Správa přístupu k prostředkům účtu úložiště](../storage/blobs/storage-manage-access-to-resources.md).

#### <a name="data-deletion"></a>Odstranění dat

Neodstraňujte soubory ve verzi Preview Time Series Insights. Spravujte související data jenom z Time Series Insights jenom ve verzi Preview.

### <a name="parquet-file-format-and-folder-structure"></a>Formát souboru a struktura složek Parquet

Parquet je open source formát sloupcového souboru navržený pro efektivní úložiště a výkon. Time Series Insights Preview používá Parquet k povolení škálovatelného výkonu dotazů založených na ID časové řady.  

Další informace o typu souboru Parquet najdete v [dokumentaci k Parquet](https://parquet.apache.org/documentation/latest/).

Time Series Insights Preview ukládá kopie vašich dat následujícím způsobem:

* První, počáteční kopie je dělená časem ingestování a ukládá data přibližně v pořadí doručení. Tato data se `PT=Time` nachází ve složce:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* Druhá, znovu rozdělená kopie je seskupená podle ID časových řad a nachází se ve `PT=TsId` složce:

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

V obou případech vlastnost Time souboru Parquet odpovídá času vytvoření objektu BLOB. Data ve `PT=Time` složce se uchovávají beze změn, jakmile se zapisují do souboru. Data ve `PT=TsId` složce budou optimalizována pro dotazy v průběhu času a nejsou statická.

> [!NOTE]
>
> * `<YYYY>`provede mapování na vyjádření roku se čtyřmi číslicemi.
> * `<MM>`provede mapování na vyjádření měsíčních číslic.
> * `<YYYYMMDDHHMMSSfff>`mapuje se na časovou reprezentaci se čtyřmi číslicemi (`YYYY`), měsíčním`MM`číslem (), dvěma číslicemi (`DD`), dvěma číslicemi`HH``MM`(), dvěma číslicemi (), dvěma číslicemi (`SS`) a třemi číslicemi milisekund (`fff`).

Události ve verzi Preview Time Series Insights jsou namapovány na obsah souboru Parquet následujícím způsobem:

* Každá událost je mapována na jeden řádek.
* Každý řádek obsahuje sloupec **časového razítka** s časovým razítkem události. Vlastnost časového razítka není nikdy null. Ve výchozím nastavení je čas zařazený do **fronty události** , pokud ve zdroji událostí není zadaná vlastnost časového razítka. Uložené časové razítko je vždy ve formátu UTC.
* Každý řádek obsahuje sloupce s ID časové řady (TSID), jak je definováno při vytváření prostředí Time Series Insights. Název vlastnosti TSID zahrnuje `_string` příponu.
* Všechny ostatní vlastnosti odeslané jako data telemetrie jsou mapovány na názvy sloupců, které `_string` končí (String) `_bool` , (Boolean) `_datetime` , (DateTime) nebo `_double` (Double), v závislosti na typu vlastnosti.
* Toto mapování schématu se vztahuje na první verzi formátu souboru, na kterou odkazuje **v = 1** a je uloženo v základní složce se stejným názvem. Vzhledem k tomu, že se tato funkce vyvíjí, může se toto mapování schématu změnit a zvýší se název odkazu.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, [jak pro příchozí a dotaz](./time-series-insights-update-how-to-shape-events.md)naformátovat JSON.

- Přečtěte si o nových [datových modelování](./time-series-insights-update-tsm.md).
