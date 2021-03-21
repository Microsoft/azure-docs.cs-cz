---
title: Transformace zdroje v toku dat mapování
description: Přečtěte si, jak nastavit transformaci zdroje v toku dat mapování.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/10/2021
ms.openlocfilehash: 310a062a8600539750935c93c7d10a1cf17a885d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103016385"
---
# <a name="source-transformation-in-mapping-data-flow"></a>Transformace zdroje v toku dat mapování

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Zdrojová transformace konfiguruje zdroj dat pro tok dat. Při návrhu toků dat je prvním krokem vždy konfigurace transformace zdroje. Chcete-li přidat zdroj, vyberte pole **Přidat zdroj** v plátně toku dat.

Každý tok dat vyžaduje aspoň jednu zdrojovou transformaci, ale můžete přidat tolik zdrojů, kolik jich je potřeba k dokončení transformace dat. Tyto zdroje můžete spojit spolu s transformací, vyhledáváním nebo sjednocením.

Každá transformace zdroje je přidružená k přesně jedné datové sadě nebo propojené službě. Datová sada definuje tvar a umístění dat, ke kterým chcete zapisovat, nebo z nich číst. Použijete-li souborovou datovou sadu, můžete ve zdroji použít zástupné znaky a seznamy souborů pro práci s více než jedním souborem v jednom okamžiku.

## <a name="inline-datasets"></a>Vložené datové sady

První rozhodnutí, které uděláte při vytváření zdrojové transformace, je, zda jsou zdrojové informace definovány v objektu DataSet nebo v rámci transformace zdroje. Většina formátů je k dispozici pouze v jedné nebo druhé. Informace o použití konkrétního konektoru najdete v dokumentu příslušného konektoru.

Pokud je pro vložené i v objektu DataSet podporován formát, existují výhody obou. Objekty datové sady jsou opakovaně použitelné entity, které lze použít v jiných datových tocích a aktivitách, jako je například kopírování. Tyto opakovaně použitelné entity jsou obzvláště užitečné při použití posíleného schématu. Datové sady nejsou založené na Sparku. V některých případech může být nutné přepsat určitá nastavení nebo projekce schématu ve zdrojové transformaci.

Vložené datové sady jsou doporučeny při použití flexibilních schémat, jednorázových zdrojových instancí nebo parametrizovaných zdrojů. Pokud je váš zdroj silně parametrizovaný, vložené datové sady vám umožní vytvořit "fiktivní" objekt. Vložené datové sady jsou založené na Sparku a jejich vlastnosti jsou nativní pro tok dat.

Pokud chcete použít vloženou datovou sadu, vyberte požadovaný formát v selektoru **typu zdroje** . Místo výběru zdrojové datové sady vyberte propojenou službu, ke které se chcete připojit.

![Snímek obrazovky, který zobrazuje vybrané vložené.](media/data-flow/inline-selector.png "Snímek obrazovky, který zobrazuje vybrané vložené.")

##  <a name="supported-source-types"></a><a name="supported-sources"></a> Podporované typy zdrojů

Mapování toku dat sleduje přístup k extrakci, načítání a transformaci (ELT) a pracuje s *pracovními* datovými sadami, které jsou všechny v Azure. V současné době lze v transformaci zdroje použít následující datové sady.

| Konektor | Formát | Sada dat/vložené |
| --------- | ------ | -------------- |
| [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Text oddělený textem](format-delimited-text.md#mapping-data-flow-properties)<br>[Rozdíl](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties) <br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/✓<br>✓/✓<br>✓/✓<br>✓/✓<br/>✓/-<br>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Cosmos DB (SQL API)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Text oddělený textem](format-delimited-text.md#mapping-data-flow-properties)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/✓<br>✓/✓<br>✓/✓<br/>✓/-<br>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Common Data Service](format-common-data-model.md#source-properties)<br>[Text oddělený textem](format-delimited-text.md#mapping-data-flow-properties)<br>[Rozdíl](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/✓<br/>-/✓<br>✓/✓<br>✓/✓<br>✓/✓<br>✓/-<br/>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Database for MySQL](connector-azure-database-for-mysql.md) |  | ✓/✓ |
| [Azure Database for PostgreSQL](connector-azure-database-for-postgresql.md) |  | ✓/✓ |
| [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Spravovaná instance Azure SQL](connector-azure-sql-managed-instance.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Hive](connector-hive.md#mapping-data-flow-properties) | | -/✓ |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |

Nastavení specifická pro tyto konektory jsou umístěna na kartě **Možnosti zdrojového kódu** . Příklady skriptu informace a toku dat v těchto nastaveních se nachází v dokumentaci konektoru.

Azure Data Factory má přístup k více než [90 nativním konektorům](connector-overview.md). Pokud chcete do toku dat zahrnout data z jiných zdrojů, použijte aktivitu kopírování a načtěte tato data do jedné z podporovaných pracovních oblastí.

## <a name="source-settings"></a>Nastavení zdroje

Po přidání zdroje nakonfigurujte přes kartu **Nastavení zdroje** . Tady můžete vybrat nebo vytvořit datovou sadu, na které se zdrojové body nacházejí. Můžete také vybrat možnosti schématu a vzorkování pro vaše data.

Hodnoty vývoje pro parametry datové sady lze konfigurovat v [nastavení ladění](concepts-data-flow-debug-mode.md). (Režim ladění musí být zapnutý.)

![Snímek obrazovky zobrazující kartu Nastavení zdroje](media/data-flow/source1.png "Snímek obrazovky zobrazující kartu Nastavení zdroje")

**Název výstupního datového proudu**: Název transformace zdroje.

**Typ zdroje**: vyberte, zda chcete použít vloženou datovou sadu nebo existující objekt DataSet.

**Test připojení**: Otestujte, jestli se může služba Spark datového toku úspěšně připojit k propojené službě použité ve zdrojové datové sadě. Aby bylo možné tuto funkci povolit, musí být zapnutý režim ladění.

**Posun schématu**: [posun schématu](concepts-data-flow-schema-drift.md) je schopnost Data Factory nativně zpracovávat flexibilní schémata v datových tocích, aniž by bylo nutné explicitně definovat změny sloupců.

* Zaškrtněte políčko pro **Povolení posunu schématu** , pokud se zdrojové sloupce často mění. Toto nastavení umožňuje, aby všechna vstupní pole zdroje prošla transformacemi do jímky.

* Výběr možnosti **odvodit typy** nenastavených sloupců nastaví data Factory ke zjištění a definování datových typů pro každý nalezený nový sloupec. Když je tato funkce vypnutá, všechny sloupce poklesu budou typu String.

**Ověřit schéma:** Pokud je vybraná možnost **ověřit schéma** , tok dat se nepodaří spustit, pokud příchozí zdrojová data neodpovídají definovanému schématu datové sady.

**Přeskočit počet řádků**: pole **Přeskočit počet** řádků určuje, kolik řádků se má na začátku datové sady ignorovat.

**Vzorkování**: Povolte **vzorkování** pro omezení počtu řádků ze zdroje. Toto nastavení použijte při testování nebo vzorkování dat ze zdroje pro účely ladění. To je velmi užitečné při provádění toků dat v režimu ladění z kanálu.

Pokud chcete ověřit, že je váš zdroj správně nakonfigurovaný, zapněte režim ladění a načtěte data ve verzi Preview. Další informace naleznete v tématu [režim ladění](concepts-data-flow-debug-mode.md).

> [!NOTE]
> Když je režim ladění zapnutý, při konfiguraci limitu řádků v nastavení ladění dojde k přepsání nastavení vzorkování ve zdroji během náhledu dat.

## <a name="source-options"></a>Možnosti zdroje

Karta **Možnosti zdroje** obsahuje nastavení specifická pro vybraný konektor a formát. Další informace a příklady naleznete v příslušné [dokumentaci k konektoru](#supported-sources).

## <a name="projection"></a>Projekce

Stejně jako schémata v datových sadách definuje projekce ve zdroji datové sloupce, typy a formáty ze zdrojových dat. U většiny typů datových sad, jako je například SQL a Parquet, je projekce ve zdroji pevná, aby odrážela schéma definované v datové sadě. Pokud zdrojové soubory nejsou silného typu (například ploché soubory CSV namísto souborů Parquet), můžete definovat datové typy pro každé pole ve zdrojové transformaci.

![Snímek obrazovky, který zobrazuje nastavení na kartě projekce](media/data-flow/source3.png "Snímek obrazovky, který zobrazuje nastavení na kartě projekce")

Pokud textový soubor nemá žádné definované schéma, vyberte možnost **detekovat datový typ** , aby data Factory vzorkovat a odvodit datové typy. Pro automatické rozpoznání výchozích formátů dat vyberte možnost **definovat výchozí formát** .

**Resetování schématu** obnoví projekci na to, co je definováno v odkazované datové sadě.

Můžete upravit typy datových sloupců v transformaci odvozeného sloupce pro podřízený objekt. Použijte transformaci SELECT k úpravě názvů sloupců.

### <a name="import-schema"></a>Importovat schéma

Vyberte tlačítko **importovat schéma** na kartě **projekce** a použijte aktivní cluster pro ladění k vytvoření projekce schématu. Je k dispozici v každém typu zdroje. Import schématu sem přepíše projekci definovanou v datové sadě. Objekt DataSet se nezmění.

Import schématu je užitečný v datových sadách, jako je Avro a Azure Cosmos DB, které podporují složité datové struktury, které nevyžadují, aby definice schématu existovaly v datové sadě. Pro vložené datové sady je import schématu jediným způsobem, jak odkazovat na metadata sloupce bez posunu schématu.

## <a name="optimize-the-source-transformation"></a>Optimalizace zdrojové transformace

Karta **optimalizace** umožňuje upravovat informace o oddílech v jednotlivých krocích transformace. Ve většině případů se **použití aktuálního dělení** optimalizuje pro ideální strukturu vytváření oddílů pro zdroj.

Pokud čtete ze zdroje Azure SQL Database, bude se **při vlastním vytváření** oddílů pravděpodobně číst data nejrychlejší. Data Factory načte velké dotazy tím, že se paralelně navazování připojení k vaší databázi. Tento zdrojový oddíl lze provést na sloupec nebo pomocí dotazu.

![Snímek obrazovky zobrazující nastavení zdrojového oddílu](media/data-flow/sourcepart3.png "Snímek obrazovky zobrazující nastavení zdrojového oddílu")

Další informace o optimalizaci v rámci mapování toku dat najdete na [kartě optimalizace](concepts-data-flow-overview.md#optimize).

## <a name="next-steps"></a>Další kroky

Začněte sestavovat tok dat pomocí [transformace odvozeného sloupce](data-flow-derived-column.md) a [transformace Select](data-flow-select.md).
