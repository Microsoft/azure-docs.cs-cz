---
title: Transformace jímky v toku dat mapování
description: Naučte se konfigurovat transformaci jímky v mapování toku dat.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/10/2021
ms.openlocfilehash: 5548d82326ec4ac2306e2c8945bedc20236a4e54
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103009345"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Transformace jímky v toku dat mapování

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Až dokončíte transformaci dat, zapište ji do cílového úložiště pomocí transformace jímky. Každý tok dat vyžaduje alespoň jednu transformaci jímky, ale můžete zapisovat do tolika umyvadel, kolik je potřeba k dokončení toku transformace. Chcete-li zapisovat do dalších umyvadel, vytvářejte nové datové proudy pomocí nových větví a podmíněných rozdělení.

Každá transformace jímky je přidružená k přesně jednomu Azure Data Factory objektu DataSet nebo propojené službě. Transformace jímky určuje tvar a umístění dat, do kterých chcete zapisovat.

## <a name="inline-datasets"></a>Vložené datové sady

Když vytvoříte transformaci jímky, vyberte, zda jsou informace o jímky definovány v objektu DataSet nebo v rámci transformace jímky. Většina formátů je k dispozici pouze v jedné nebo druhé. Informace o použití konkrétního konektoru najdete v dokumentu příslušného konektoru.

Pokud je pro vložené i v objektu DataSet podporován formát, existují výhody obou. Objekty datové sady jsou opakovaně použitelné entity, které lze použít v jiných datových tocích a aktivitách, jako je například kopírování. Tyto opakovaně použitelné entity jsou obzvláště užitečné při použití posíleného schématu. Datové sady nejsou založené na Sparku. Občas může být nutné přepsat určitá nastavení nebo projekce schématu v transformaci jímky.

Vložené datové sady jsou doporučeny při použití flexibilních schémat, instancí jedné instance jímky nebo parametrizovaných umyvadel. Pokud je vaše jímka silně Parametrizovaná, vložené datové sady vám umožní vytvořit "fiktivní" objekt. Vložené datové sady jsou založené na Sparku a jejich vlastnosti jsou nativní pro tok dat.

Pokud chcete použít vloženou datovou sadu, vyberte požadovaný formát v selektoru **typu jímky** . Místo výběru datové sady jímky vyberete propojenou službu, ke které se chcete připojit.

![Snímek obrazovky, který zobrazuje vybrané vložené.](media/data-flow/inline-selector.png "Snímek obrazovky, který zobrazuje vybrané vložené.")

##  <a name="supported-sink-types"></a><a name="supported-sinks"></a> Podporované typy jímky

Mapování toku dat sleduje přístup k extrakci, načítání a transformaci (ELT) a pracuje s *pracovními* datovými sadami, které jsou všechny v Azure. V současné době lze v transformaci zdroje použít následující datové sady.

| Konektor | Formát | Sada dat/vložené |
| --------- | ------ | -------------- |
| [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties) <br>[Text oddělený textem](format-delimited-text.md#mapping-data-flow-properties) <br>[Rozdíl](format-delta.md) <br>[JSON](format-json.md#mapping-data-flow-properties) <br/>[ORC](format-orc.md#mapping-data-flow-properties)<br>[Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br>✓/- <br>-/✓ <br>✓/- <br>✓/✓<br>✓/- |
| [Azure Cosmos DB (SQL API)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties) <br>[Text oddělený textem](format-delimited-text.md#mapping-data-flow-properties) <br>[JSON](format-json.md#mapping-data-flow-properties) <br/>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br>✓/- <br>✓/- <br>✓/✓<br>✓/- |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties) <br/>[Common Data Service](format-common-data-model.md#sink-properties)<br>[Text oddělený textem](format-delimited-text.md#mapping-data-flow-properties) <br>[Rozdíl](format-delta.md) <br>[JSON](format-json.md#mapping-data-flow-properties) <br/>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br>-/✓ <br>✓/- <br>-/✓ <br>✓/-<br>✓/✓ <br>✓/- |
| [Azure Database for MySQL](connector-azure-database-for-mysql.md) |  | ✓/✓ |
| [Azure Database for PostgreSQL](connector-azure-database-for-postgresql.md) |  | ✓/✓ |
| [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Spravovaná instance Azure SQL (Preview)](connector-azure-sql-managed-instance.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |

Nastavení specifická pro tyto konektory jsou umístěna na kartě **Nastavení** . Příklady skriptu informace a toku dat v těchto nastaveních se nachází v dokumentaci konektoru.

Azure Data Factory má přístup k více než [90 nativním konektorům](connector-overview.md). Chcete-li zapsat data do těchto jiných zdrojů z datového toku, použijte aktivitu kopírování a načtěte tato data z podporované jímky.

## <a name="sink-settings"></a>Nastavení jímky

Po přidání jímky proveďte konfiguraci přes kartu **jímka** . Tady můžete vybrat nebo vytvořit datovou sadu, do které zapisuje jímka. Hodnoty vývoje pro parametry datové sady lze konfigurovat v [nastavení ladění](concepts-data-flow-debug-mode.md). (Režim ladění musí být zapnutý.)

Následující video vysvětluje řadu různých možností jímky pro typy souborů s oddělovači textu.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4tf7T]

![Snímek obrazovky, který zobrazuje nastavení jímky.](media/data-flow/sink-settings.png "Snímek obrazovky, který zobrazuje nastavení jímky.")

**Posun schématu**: [posun schématu](concepts-data-flow-schema-drift.md) je schopnost Data Factory nativně zpracovávat flexibilní schémata v datových tocích, aniž by bylo nutné explicitně definovat změny sloupců. Povolit možnost **Povolit posun schématu** pro zápis dalších sloupců nad to, co je definováno ve schématu dat jímky.

**Ověřit schéma**: Pokud je vybraná možnost ověřit schéma, tok dat selže, pokud se ve zdrojové projekci nenajde žádný sloupec pro příchozí zdrojové schéma, nebo pokud se datové typy neshodují. Pomocí tohoto nastavení můžete vynutilit, aby zdrojová data splňovala kontrakt vaší definované projekce. To je užitečné ve scénářích zdrojové databáze k signalizaci, že se změnily názvy nebo typy sloupců.

## <a name="cache-sink"></a>Jímka mezipaměti

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4HKt1]

*Jímka mezipaměti* je v případě, že tok dat zapisuje data do mezipaměti Spark místo do úložiště dat. V části mapování toků dat můžete na tato data v rámci stejného toku odkazovat vícekrát pomocí vyhledávání v *mezipaměti*. To je užitečné, pokud chcete odkazovat na data jako součást výrazu, ale nechcete, aby k nim explicitně připojila sloupce. Běžné příklady, kde může jímka mezipaměti pomáhat při hledání maximální hodnoty v úložišti dat a porovnání kódů chyb s databází chybových zpráv. 

Chcete-li zapisovat do jímky mezipaměti, přidejte transformaci jímky a jako typ jímky vyberte **mezipaměť** . Na rozdíl od jiných typů jímky nemusíte vybírat datovou sadu nebo propojenou službu, protože nepíšete do externího úložiště. 

![Vybrat jímku mezipaměti](media/data-flow/select-cache-sink.png "Vybrat jímku mezipaměti")

V nastavení jímky můžete volitelně zadat klíčové sloupce jímky mezipaměti. Ty se používají jako podmínky pro porovnání při použití `lookup()` funkce při vyhledávání mezipaměti. Pokud zadáte klíčové sloupce, nemůžete `outputs()` funkci použít při vyhledávání v mezipaměti. Další informace o syntaxi vyhledávání mezipaměti najdete v tématu [vyhledávání v mezipaměti](concepts-data-flow-expression-builder.md#cached-lookup).

![Sloupce klíčů jímky mezipaměti](media/data-flow/cache-sink-key-columns.png "Sloupce klíčů jímky mezipaměti")

Pokud například zadáte jeden klíčový sloupec `column1` v jímky cache s názvem `cacheExample` , volání `cacheExample#lookup()` by má jeden parametr určuje, na který řádek v jímky mezipaměti se bude shodovat. Funkce vytvoří výstup jednoho složitého sloupce s podsloupci pro každý namapovaný sloupec.

> [!NOTE]
> Jímka mezipaměti musí být v zcela nezávislém datovém proudu z jakékoli transformace, která na ni odkazuje prostřednictvím vyhledávání v mezipaměti. Jímka mezipaměti musí také napsaná první jímka. 

## <a name="field-mapping"></a>Mapování pole

Podobně jako u transformace Select se můžete rozhodnout, které příchozí sloupce se budou zapisovat, na kartě **mapování** jímky. Ve výchozím nastavení jsou namapovány všechny vstupní sloupce, včetně unášených sloupců. Toto chování se označuje jako *automapping*.

Po vypnutí automatického mapování můžete přidat buď pevná mapování na základě sloupců, nebo mapování na základě pravidel. Pomocí mapování založeného na pravidlech můžete psát výrazy s porovnáváním vzorů. Pevné mapování mapuje logické a fyzické názvy sloupců. Další informace o mapování na základě pravidel najdete v tématu [vzory sloupců v části mapování toku dat](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink).

## <a name="custom-sink-ordering"></a>Vlastní řazení jímky

Ve výchozím nastavení jsou data zapisována do více umyvadel v nedeterministickém pořadí. Spouštěcí modul zapisuje data paralelně jako logiku transformace a řazení jímky se může u každého spuštění lišit. Chcete-li určit přesné řazení jímky, povolte **vlastní řazení jímky** na kartě **Obecné** v toku dat. Pokud je tato možnost povolena, jsou jímky zapisovány postupně ve vzestupném pořadí.

![Snímek obrazovky, který ukazuje vlastní řazení jímky.](media/data-flow/custom-sink-ordering.png "Snímek obrazovky, který ukazuje vlastní řazení jímky.")

> [!NOTE]
> Při použití [vyhledávání uložených v mezipaměti](./concepts-data-flow-expression-builder.md#cached-lookup)se ujistěte, že vaše řazení jímky má jímky v mezipaměti nastavené na 1, nejnižší (nebo první) v pořadí řazení.

![Vlastní řazení jímky](media/data-flow/cache-2.png "Vlastní řazení jímky")

### <a name="sink-groups"></a>Skupiny jímky

Můžete seskupovat jímky pomocí stejného pořadového čísla pro řadu umyvadel. ADF bude tyto jímky považovat za skupiny, které se můžou spouštět paralelně. Možnosti paralelního spouštění se budou nacházet v aktivitě toku dat kanálu.

## <a name="error-row-handling"></a>Zpracování chybového řádku

Při zápisu do databází může dojít k selhání některých řádků dat z důvodu omezení nastavených cílem. Ve výchozím nastavení selže spuštění toku dat při první chybě, kterou získá. V určitých konektorech můžete zvolit pokračování při **chybě** , která umožňuje dokončení toku dat, i když jednotlivé řádky obsahují chyby. V současné době je tato funkce dostupná pouze v Azure SQL Database. Další informace najdete v tématu [Chyba zpracování řádků v Azure SQL DB](connector-azure-sql-database.md#error-row-handling).

Níže je uvedený video kurz o tom, jak se při transformaci jímky automaticky používá zpracování řádků chyb databáze.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4IWne]

## <a name="data-preview-in-sink"></a>Náhled dat v jímky

Při načítání náhledu dat v clusteru ladění nebudou do jímky zapsána žádná data. Vrátí se snímek toho, co budou data vypadat, ale do svého cíle se nezapisují žádné údaje. Pokud chcete testovat zápis dat do jímky, spusťte ladění kanálu z plátna kanálu.

## <a name="next-steps"></a>Další kroky

Teď, když jste vytvořili tok dat, přidejte [do svého kanálu aktivitu toku dat](concepts-data-flow-overview.md).