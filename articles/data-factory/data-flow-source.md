---
title: Transformace zdroje v toku dat mapování
description: Přečtěte si, jak nastavit zdrojovou transformaci v toku dat mapování.
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: e4f1369aa850a244128da470aee81b2efc2e09be
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413206"
---
# <a name="source-transformation-in-mapping-data-flow"></a>Transformace zdroje v toku dat mapování 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Zdrojová transformace konfiguruje zdroj dat pro tok dat. Při navrhování datových toků bude prvním krokem konfigurace transformace zdroje. Chcete-li přidat zdroj, klikněte na pole **Přidat zdroj** na plátně toku dat.

Každý tok dat vyžaduje alespoň jednu transformaci zdroje, ale můžete přidat tolik zdrojů, kolik je potřeba k dokončení transformace dat. Tyto zdroje můžete spojit společně s spojením, vyhledáváním nebo transformací sjednocení.

Každá zdrojová transformace je přidružena k přesně jedné datové sadě Data Factory. Datová sada definuje tvar a umístění dat, do kterých chcete zapisovat nebo z nich číst. Pokud používáte datovou sadu založenou na souborech, můžete pomocí zástupných znaků a seznamů souborů ve zdroji pracovat s více než jedním souborem najednou.

## <a name="supported-source-connectors-in-mapping-data-flow"></a>Podporované zdrojové konektory v toku dat mapování

Mapování toku dat následuje extrahovat, načíst, transformovat (ELT) přístup a pracuje s *pracovní* datové sady, které jsou všechny v Azure. V současné době lze ve zdrojové transformaci použít následující datové sady:
    
* [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, Avro, Text, Parkety)
* [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, Avro, Text, Parkety)
* [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, Avro, Text, Parkety)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

Nastavení specifická pro tyto konektory jsou umístěna na kartě **Možnosti zdroje.** 

Azure Data Factory má přístup k více než [90 nativním konektorům](connector-overview.md). Chcete-li zahrnout data z těchto jiných zdrojů do toku dat, použijte aktivitu kopírování k načtení těchto dat do jedné z podporovaných pracovních oblastí.

## <a name="source-settings"></a>Nastavení zdroje

Po přidání zdroje nakonfigurujte na kartě **Nastavení zdroje.** Zde můžete vybrat nebo vytvořit datovou sadu zdrojových bodů. Můžete také vybrat možnosti schématu a vzorkování pro vaše data.

![Karta Nastavení zdroje](media/data-flow/source1.png "Karta Nastavení zdroje")

**Testovací připojení:** Otestujte, zda se zapalovací služba toku dat může úspěšně připojit k propojené službě používané ve zdrojové datové sadě. Aby byla tato funkce povolena, musí být zapnutý režim ladění.

**Posun schématu:** [Posun schématu](concepts-data-flow-schema-drift.md) je schopnost datové továrny nativně zpracovávat flexibilní schémata v tocích dat bez nutnosti explicitně definovat změny sloupců.

* Zaškrtněte **políčko Povolit posun schématu,** pokud se zdrojové sloupce budou často měnit. Toto nastavení umožňuje všechny příchozí zdrojová pole tok transformace do jímky.

* Výběrem **možnosti Odvodit posunuté typy sloupců** instruujete továrnu dat ke zjišťování a definování datových typů pro každý nový zjištěný sloupec. Při vypnuté této funkci budou všechny posunuté sloupce typu.

**Ověřit schéma:** Pokud je vybráno schéma ověření, tok dat se nepodaří spustit, pokud příchozí zdrojová data neodpovídají definovanému schématu datové sady.

**Přeskočit počet řádků:** Pole počet přeskočení řádků určuje, kolik řádků má být ignorováno na začátku datové sady.

**Odběr vzorků:** Povolit vzorkování omezit počet řádků ze zdroje. Toto nastavení použijte při testování nebo ukázková data ze zdroje pro účely ladění.

**Víceřádkové řádky:** Vyberte víceřádkové řádky, pokud zdrojový textový soubor obsahuje řetězcové hodnoty, které pokrývají více řádků, tj. Toto nastavení je k dispozici pouze v datových sadách Oddělovač textu.

Chcete-li ověřit, že je zdroj správně nakonfigurován, zapněte režim ladění a načtete náhled dat. Další informace naleznete v [tématu Režim ladění](concepts-data-flow-debug-mode.md).

> [!NOTE]
> Když je režim ladění zapnutý, konfigurace limitu řádků v nastavení ladění přepíše nastavení vzorkování ve zdroji během náhledu dat.

## <a name="projection"></a>Projekce

Podobně jako schémata v datových sadách definuje projekce ve zdroji datové sloupce, typy a formáty ze zdrojových dat. U většiny typů datových sad, jako je SQL a Parkety, je projekce ve zdroji pevná tak, aby odrážela schéma definované v datové sadě. Pokud nejsou zdrojové soubory silně zadány (například ploché soubory CSV spíše než soubory parket), můžete definovat datové typy pro každé pole ve zdrojové transformaci.

![Nastavení na kartě Projekce](media/data-flow/source3.png "Projekce")

Pokud textový soubor nemá žádné definované schéma, vyberte **Rozpoznat datový typ,** aby se z datového typu vzorkovaly a odvodily datové typy. Vyberte **Definovat výchozí formát,** chcete-li automaticky rozpoznat výchozí datové formáty.

**Obnovení schématu** obnoví projekci na hodnotu definovanou v odkazované datové sadě.

Datové typy sloupců můžete upravit v transformaci odvozeného sloupce dolů. Pomocí vybrané transformace upravte názvy sloupců.

### <a name="import-schema"></a>Schéma importu

Tlačítko **Importovat schéma** na kartě **Projekce** umožňuje použít aktivní ladicí cluster k vytvoření projekce schématu. Import zde uvedeného schématu, který je k dispozici v každém typu zdroje, přepíše projekci definovanou v datové sadě. Objekt datové sady nebude změněn.

To je užitečné v datových sadách, jako je Avro a CosmosDB, které podporují složité datové struktury nevyžadují definice schématu existovat v datové sadě.

## <a name="optimize-the-source-transformation"></a>Optimalizace transformace zdroje

Na kartě **Optimalizace** pro zdrojovou transformaci se může zobrazit typ **zdrojového** oddílu. Tato možnost je k dispozici pouze v případě, že váš zdroj je Azure SQL Database. Důvodem je, že Data Factory se pokusí vytvořit připojení paralelní ke spuštění velkých dotazů proti zdroji databáze SQL.

![Nastavení zdrojového oddílu](media/data-flow/sourcepart3.png "Dělení")

Není nutné rozdělit data na zdroj databáze SQL, ale oddíly jsou užitečné pro velké dotazy. Oddíl můžete založit na sloupci nebo dotazu.

### <a name="use-a-column-to-partition-data"></a>Použití sloupce k rozdělení dat

Ve zdrojové tabulce vyberte sloupec, na který chcete rozdělit. Nastavte také počet oddílů.

### <a name="use-a-query-to-partition-data"></a>Použití dotazu k rozdělení dat

Můžete se rozhodnout rozdělit připojení na základě dotazu. Zadejte obsah predikátu WHERE. Zadejte například rok > 1980.

Další informace o optimalizaci v rámci toku dat mapování naleznete na [kartě Optimalizace](concepts-data-flow-overview.md#optimize).

## <a name="next-steps"></a>Další kroky

Začněte vytvářet [odvozenou transformaci sloupce](data-flow-derived-column.md) a [vybranou transformaci](data-flow-select.md).
