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
ms.date: 10/15/2020
ms.openlocfilehash: 81ce3fae74a14c91db23c991ab0b53accd6568a6
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92107704"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Transformace jímky v toku dat mapování

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Až dokončíte transformaci dat, zapište ji do cílového úložiště pomocí transformace jímky. Každý tok dat vyžaduje alespoň jednu transformaci jímky, ale můžete zapisovat do tolika umyvadel, kolik je potřeba k dokončení toku transformace. Chcete-li zapisovat do dalších umyvadel, vytvářejte nové datové proudy pomocí nových větví a podmíněných rozdělení.

Každá transformace jímky je přidružená k přesně jednomu Azure Data Factory objektu DataSet nebo propojené službě. Transformace jímky určuje tvar a umístění dat, do kterých chcete zapisovat.

## <a name="inline-datasets"></a>Vložené datové sady

Při vytváření transformace jímky vyberte, zda jsou informace jímky definovány v objektu DataSet nebo v rámci transformace jímky. Většina formátů je k dispozici pouze v jednom nebo druhém. Pokud se chcete dozvědět, jak používat konkrétní konektor, použijte prosím odkaz na příslušný dokument konektoru.

Pokud je pro vložené i v objektu DataSet podporován formát, existují výhody obou. Objekty datové sady jsou opakovaně použitelné entity, které lze využít v jiných datových tocích a aktivitách, jako je například kopírování. To je užitečné hlavně při použití zpřísněného schématu. Datové sady nejsou založené na Sparku a občas možná budete muset v transformaci jímky přepsat určitá nastavení nebo projekce schématu.

Vložené datové sady jsou doporučeny při použití flexibilních schémat, instancí jednorázové jímky nebo parametrizovaných umyvadel. Pokud je vaše jímka silně Parametrizovaná, vložené datové sady vám umožní vytvořit "fiktivní" objekt. Vložené datové sady jsou založené na Sparku a jejich vlastnosti jsou nativní pro tok dat.

Chcete-li použít vloženou datovou sadu, vyberte požadovaný formát v selektoru **typu jímky** . Místo výběru datové sady jímky vyberete propojenou službu, ke které se chcete připojit.

![Vložená datová sada](media/data-flow/inline-selector.png "Vložená datová sada")

##  <a name="supported-sink-types"></a><a name="supported-sinks"></a> Podporované typy jímky

Mapování toku dat sleduje přístup k extrakci, načítání, transformaci (ELT) a pracuje s *přípravnými* datovými sadami, které jsou všechny v Azure. V současné době je možné v transformaci zdroje použít následující datové sady:

| Konektor | Formát | Sada dat/vložené |
| --------- | ------ | -------------- |
| [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [Text oddělený textem](format-delimited-text.md#mapping-data-flow-properties) <br> [Rozdíl (Preview)](format-delta.md) <br> [ORC](format-orc.md#mapping-data-flow-properties)<br> [Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br> ✓/- <br> ✓/- <br> -/✓ <br>✓/✓<br> ✓/- |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [Text oddělený textem](format-delimited-text.md#mapping-data-flow-properties) <br> [ORC](format-orc.md#mapping-data-flow-properties)<br/> [Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br> ✓/- <br> ✓/- <br>✓/✓<br> ✓/- |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [Text oddělený textem](format-delimited-text.md#mapping-data-flow-properties) <br> [Rozdíl (Preview)](format-delta.md) <br> [ORC](format-orc.md#mapping-data-flow-properties)<br/> [Parquet](format-parquet.md#mapping-data-flow-properties)  <br> [Common data model (Preview)](format-common-data-model.md#sink-properties) | ✓/- <br> ✓/- <br> ✓/- <br> -/✓ <br>✓/✓<br> ✓/- <br> -/✓ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Spravovaná instance Azure SQL (Preview)](connector-azure-sql-managed-instance.md#mapping-data-flow-properties) | | ✓/- |
| [Azure CosmosDB (SQL API)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |

Nastavení specifická pro tyto konektory jsou umístěna na kartě **Nastavení** . Příklady skriptu informace a toku dat v těchto nastaveních se nachází v dokumentaci konektoru. 

Azure Data Factory má přístup k více než [90 nativním konektorům](connector-overview.md). Chcete-li zapsat data do těchto jiných zdrojů z datového toku, použijte aktivitu kopírování a načtěte tato data z podporované jímky.

## <a name="sink-settings"></a>Nastavení jímky

Po přidání jímky proveďte konfiguraci přes kartu **jímka** . Tady můžete vybrat nebo vytvořit datovou sadu, do které zapisuje jímka. Hodnoty vývoje pro parametry datové sady lze konfigurovat v [nastavení ladění](concepts-data-flow-debug-mode.md) (vyžaduje zapnutí režimu ladění).

Níže je video vysvětlující řadu různých možností jímky pro typy souborů s oddělovači textu:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4tf7T]

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
