---
title: Transformace jímky v toku dat mapování
description: Naučte se konfigurovat transformaci jímky v mapování toku dat.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/03/2020
ms.openlocfilehash: 2c57ddd88046044cccd13b0ade23144cd5649455
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2020
ms.locfileid: "84433316"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Transformace jímky v toku dat mapování

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Po transformaci dat můžete data zajímky do cílové datové sady. Každý tok dat vyžaduje alespoň jednu transformaci jímky, ale můžete zapisovat do tolika umyvadel, kolik je potřeba k dokončení toku transformace. Chcete-li zapisovat do dalších umyvadel, vytvářejte nové datové proudy pomocí nových větví a podmíněných rozdělení.

Každá transformace jímky je přidružená k právě jedné datové sadě Data Factory. Datová sada definuje tvar a umístění dat, do kterých chcete zapisovat.

## <a name="inline-datasets"></a>Vložené datové sady

Při vytváření transformace jímky vyberte, zda jsou informace jímky definovány v objektu DataSet nebo v rámci transformace jímky. Většina formátů je k dispozici pouze v jednom nebo druhém. Pokud se chcete dozvědět, jak používat konkrétní konektor, použijte prosím odkaz na příslušný dokument konektoru.

Pokud je pro vložené i v objektu DataSet podporován formát, existují výhody obou. Objekty datové sady jsou opakovaně použitelné entity, které lze využít v jiných datových tocích a aktivitách, jako je například kopírování. To je užitečné hlavně při použití zpřísněného schématu. Datové sady nejsou založené na Sparku a občas možná budete muset v transformaci jímky přepsat určitá nastavení nebo projekce schématu.

Vložené datové sady jsou doporučeny při použití flexibilních schémat, instancí jednorázové jímky nebo parametrizovaných umyvadel. Pokud je vaše jímka silně Parametrizovaná, vložené datové sady vám umožní vytvořit "fiktivní" objekt. Vložené datové sady jsou založené na Sparku a jejich vlastnosti jsou nativní pro tok dat.

Chcete-li použít vloženou datovou sadu, vyberte požadovaný formát v selektoru **typu jímky** . Místo výběru datové sady jímky vyberete propojenou službu, ke které se chcete připojit.

![Vložená datová sada](media/data-flow/inline-selector.png "Vložená datová sada")

### <a name="supported-inline-dataset-formats"></a>Podporované formáty vložených datových sad

V současné době je jediným dostupným formátem vložené datové sady [společný datový model](format-common-data-model.md#sink-properties) načtený z [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md).

## <a name="supported-sink-connectors-in-mapping-data-flow"></a>Podporované konektory jímky v mapování toku dat

V současné době lze v transformaci jímky použít následující datové sady:
    
* [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, Avro, text, Parquet)
* [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, Avro, text, Parquet)
* [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, Avro, text, Parquet)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties)
* [CosmosDB Azure](connector-azure-cosmos-db.md#mapping-data-flow-properties)

Nastavení specifická pro tyto konektory jsou umístěna na kartě **Nastavení** . informace o těchto nastaveních najdete v dokumentaci konektoru. 

Azure Data Factory má přístup k více než [90 nativním konektorům](connector-overview.md). Pokud chcete zapsat data do těchto dalších konektorů z toku dat, pomocí aktivity kopírování načtěte tato data z jedné z podporovaných pracovních oblastí po dokončení toku dat.

## <a name="sink-settings"></a>Nastavení jímky

Po přidání jímky proveďte konfiguraci přes kartu **jímka** . Tady můžete vybrat nebo vytvořit datovou sadu, do které zapisuje jímka. Níže je video vysvětlující řadu různých možností jímky pro typy souborů s oddělovači textu:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tf7T]

![Nastavení jímky](media/data-flow/sink-settings.png "Nastavení jímky")

**Posun schématu:** [posun schématu](concepts-data-flow-schema-drift.md) je schopnost objektu pro vytváření dat nativně zpracovávat flexibilní schémata v datových tocích, aniž by bylo nutné explicitně definovat změny sloupců. Povolit možnost **Povolit posun schématu** pro zápis dalších sloupců nad to, co je definováno ve schématu dat jímky.

**Ověřit schéma:** Pokud je vybraná možnost ověřit schéma, tok dat selže, pokud se ve zdrojové projekci nenalezne libovolný sloupec schématu příchozích zdrojů, nebo pokud se datové typy neshodují. Pomocí tohoto nastavení můžete vynutilit, aby zdrojová data splňovala kontrakt vaší definované projekce. Je velmi užitečné ve scénářích zdroje databáze k signalizaci, že se změnily názvy nebo typy sloupců.

## <a name="field-mapping"></a>Mapování pole

Podobně jako u transformace Select se můžete rozhodnout, které příchozí sloupce budou zapsány na kartě **mapování** jímky. Ve výchozím nastavení jsou namapovány všechny vstupní sloupce, včetně unášených sloupců. Toto je známé jako **automatické mapování**.

Když automatické mapování vypnete, budete mít možnost přidat buď pevná mapování na sloupce, nebo mapování na základě pravidel. Mapování na základě pravidel umožňují psát výrazy s porovnáváním vzorů, zatímco pevné mapování bude mapovat logické a fyzické názvy sloupců. Další informace o mapování na základě pravidel najdete v tématu [vzory sloupců v části mapování toku dat](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink).

## <a name="custom-sink-ordering"></a>Vlastní řazení jímky

Ve výchozím nastavení jsou data zapisována do více umyvadel v nedeterministickém pořadí. Spouštěcí modul zapisuje data paralelně, protože je dokončena transformace logiky a řazení jímky se může u každého spuštění lišit. Chcete-li zadat a přesné řazení jímky, povolte **vlastní řazení jímky** na kartě Obecné v toku dat. Pokud je povoleno, jímky budou zapisovány postupně ve vzestupném pořadí.

![Vlastní řazení jímky](media/data-flow/custom-sink-ordering.png "Vlastní řazení jímky")

## <a name="data-preview-in-sink"></a>Náhled dat v jímky

Při načítání náhledu dat v clusteru ladění nebudou do jímky zapsána žádná data. Vrátí se snímek toho, co budou data vypadat, ale do svého cíle se nezapisují žádné údaje. Pokud chcete testovat zápis dat do jímky, spusťte ladění kanálu z plátna kanálu.

## <a name="next-steps"></a>Další kroky
Teď, když jste vytvořili tok dat, přidejte [do svého kanálu aktivitu toku dat](concepts-data-flow-overview.md).
