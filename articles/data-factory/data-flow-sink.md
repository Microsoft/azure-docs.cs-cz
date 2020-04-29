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
ms.date: 12/12/2019
ms.openlocfilehash: 4b10a4c98abd6bec4074bf35764a9cbb85d5b157
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81605970"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Transformace jímky v toku dat mapování

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Po transformaci dat můžete data zajímky do cílové datové sady. Každý tok dat vyžaduje alespoň jednu transformaci jímky, ale můžete zapisovat do tolika umyvadel, kolik je potřeba k dokončení toku transformace. Chcete-li zapisovat do dalších umyvadel, vytvářejte nové datové proudy pomocí nových větví a podmíněných rozdělení.

Každá transformace jímky je přidružená k právě jedné datové sadě Data Factory. Datová sada definuje tvar a umístění dat, do kterých chcete zapisovat.

## <a name="supported-sink-connectors-in-mapping-data-flow"></a>Podporované konektory jímky v mapování toku dat

V současné době lze v transformaci jímky použít následující datové sady:
    
* [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, Avro, text, Parquet)
* [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, Avro, text, Parquet)
* [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, Avro, text, Parquet)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties)
* [CosmosDB Azure](connector-azure-cosmos-db.md#mapping-data-flow-properties)

Nastavení specifická pro tyto konektory jsou umístěna na kartě **Nastavení** . informace o těchto nastaveních najdete v dokumentaci konektoru. 

Azure Data Factory má přístup k více než [90 nativním konektorům](connector-overview.md). Pokud chcete do těchto jiných zdrojů zapsat data z toku dat, pomocí aktivity kopírování načtěte tato data z jedné z podporovaných pracovních oblastí po dokončení toku dat.

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
