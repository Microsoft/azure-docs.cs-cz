---
title: Transformace zdroje v toku dat mapování
description: Přečtěte si, jak nastavit transformaci zdroje v toku dat mapování.
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/03/2020
ms.openlocfilehash: 6da0c56e11b8531192ba77d8f0c27fa16eea5de2
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2020
ms.locfileid: "84433305"
---
# <a name="source-transformation-in-mapping-data-flow"></a>Transformace zdroje v toku dat mapování 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Zdrojová transformace konfiguruje zdroj dat pro tok dat. Při navrhování toků dat bude váš první krok vždycky konfigurovat transformaci zdroje. Zdroj přidáte tak, že kliknete na pole **Přidat zdroj** v plátně toku dat.

Každý tok dat vyžaduje aspoň jednu zdrojovou transformaci, ale můžete přidat tolik zdrojů, kolik jich je potřeba k dokončení transformace dat. Tyto zdroje můžete spojit spolu s transformací, vyhledáváním nebo sjednocením.

Každá transformace zdroje je přidružená k právě jedné datové sadě Data Factory. Datová sada definuje tvar a umístění dat, ke kterým chcete zapisovat, nebo z nich číst. Pokud používáte datovou sadu založenou na souborech, můžete ve zdroji použít zástupné znaky a seznamy souborů pro práci s více než jedním souborem v jednom okamžiku.

## <a name="inline-datasets"></a>Vložené datové sady

První rozhodnutí, které uděláte při vytváření zdrojové transformace, je, jestli jsou vaše zdrojové informace definované uvnitř objektu DataSet nebo v rámci transformace zdroje. Většina formátů je k dispozici pouze v jednom nebo druhém. Pokud se chcete dozvědět, jak používat konkrétní konektor, použijte prosím odkaz na příslušný dokument konektoru.

Pokud je pro vložené i v objektu DataSet podporován formát, existují výhody obou. Objekty datové sady jsou opakovaně použitelné entity, které lze využít v jiných datových tocích a aktivitách, jako je například kopírování. To je užitečné hlavně při použití zpřísněného schématu. Datové sady nejsou založené na Sparku a občas možná budete muset v transformaci zdroje přepsat určitá nastavení nebo projekce schématu.

Vložené datové sady jsou doporučeny při použití flexibilních schémat, jednorázových zdrojových instancí nebo parametrizovaných zdrojů. Pokud je váš zdroj silně parametrizovaný, vložené datové sady umožňují nevytvářet "fiktivní" objekty. Vložené datové sady jsou založené na Sparku a jejich vlastnosti jsou nativní pro tok dat.

Chcete-li použít vloženou datovou sadu, vyberte požadovaný formát v selektoru **typu zdroje** . Místo výběru zdrojové datové sady vyberete propojenou službu, ke které se chcete připojit.

![Vložená datová sada](media/data-flow/inline-selector.png "Vložená datová sada")

### <a name="supported-inline-dataset-formats"></a>Podporované formáty vložených datových sad

V současné době je jediným dostupným formátem vložené datové sady [společný datový model](format-common-data-model.md#source-properties) načtený z [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md).

## <a name="supported-source-datasets-in-mapping-data-flow"></a>Podporované zdrojové datové sady v toku mapování dat

Mapování toku dat sleduje přístup k extrakci, načítání, transformaci (ELT) a pracuje s *přípravnými* datovými sadami, které jsou všechny v Azure. V současné době je možné v transformaci zdroje použít následující datové sady:
    
* [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, Avro, text, Parquet)
* [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, Avro, text, Parquet)
* [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, Avro, text, Parquet)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties)
* [CosmosDB Azure](connector-azure-cosmos-db.md#mapping-data-flow-properties)

Nastavení specifická pro tyto konektory jsou umístěna na kartě **Možnosti zdrojového kódu** . informace o těchto nastaveních najdete v dokumentaci k konektoru. 

Azure Data Factory má přístup k více než [90 nativním konektorům](connector-overview.md). Pokud chcete do toku dat zahrnout data z jiných zdrojů, použijte aktivitu kopírování a načtěte tato data do jedné z podporovaných pracovních oblastí.

## <a name="source-settings"></a>Nastavení zdroje

Jakmile přidáte zdroj, nakonfigurujte ho přes kartu **Nastavení zdroje** . Tady můžete vybrat nebo vytvořit datovou sadu, na které se zdrojové body nacházejí. Můžete také vybrat možnosti schématu a vzorkování pro vaše data.

![Karta nastavení zdroje](media/data-flow/source1.png "Karta nastavení zdroje")

**Test připojení:** Otestuje, jestli se služba Spark datového toku může úspěšně připojit k propojené službě použité ve zdrojové datové sadě. Aby bylo možné tuto funkci povolit, musí být zapnutý režim ladění.

**Posun schématu:** [posun schématu](concepts-data-flow-schema-drift.md) je schopnost objektu pro vytváření dat nativně zpracovávat flexibilní schémata v datových tocích, aniž by bylo nutné explicitně definovat změny sloupců.

* Zaškrtněte políčko pro **Povolení posunu schématu** , pokud se zdrojové sloupce často mění. Toto nastavení umožňuje, aby všechna vstupní pole zdroje prošla transformacemi do jímky.

* Výběr možnosti **odvodit sloupce** s vydanými sloupci způsobí pokyn objektu pro vytváření dat ke zjištění a definování datových typů pro každý nalezený nový sloupec. Když je tato funkce vypnutá, všechny sloupce poklesu budou typu String.

**Ověřit schéma:** Pokud je vybraná možnost ověřit schéma, tok dat se nepodaří spustit, pokud příchozí zdrojová data neodpovídají definovanému schématu datové sady.

**Přeskočit počet řádků:** Pole počet řádků přeskočení určuje, kolik řádků se má na začátku datové sady ignorovat.

**Vzorkování:** Povolte vzorkování, abyste omezili počet řádků ze zdroje. Toto nastavení použijte při testování nebo vzorkování dat ze zdroje pro účely ladění.

**Víceřádkové řádky:** Vyberte víceřádkové řádky, pokud zdrojový textový soubor obsahuje řetězcové hodnoty, které jsou rozloženy do více řádků, tj. newlines uvnitř hodnoty. Toto nastavení je k dispozici pouze v DelimitedText datových sadách.

Pokud chcete ověřit, že je váš zdroj správně nakonfigurovaný, zapněte režim ladění a načtěte data ve verzi Preview. Další informace naleznete v tématu [režim ladění](concepts-data-flow-debug-mode.md).

> [!NOTE]
> Když je režim ladění zapnutý, při konfiguraci limitu řádků v nastavení ladění dojde k přepsání nastavení vzorkování ve zdroji během náhledu dat.

## <a name="projection"></a>Projekce

Stejně jako schémata v datových sadách definuje projekce ve zdroji datové sloupce, typy a formáty ze zdrojových dat. U většiny typů datových sad, jako je SQL a Parquet, je projekce ve zdroji pevná, aby odrážela schéma definované v datové sadě. Pokud zdrojové soubory nejsou silného typu (například ploché soubory CSV namísto souborů Parquet), můžete definovat datové typy pro každé pole ve zdrojové transformaci.

![Nastavení na kartě projekce](media/data-flow/source3.png "Projekce")

Pokud textový soubor nemá žádné definované schéma, vyberte možnost **detekovat datový typ** , aby data Factory vzorkovat a odvodit datové typy. Pro automatické rozpoznání výchozích formátů dat vyberte možnost **definovat výchozí formát** .

**Resetování schématu** obnoví projekci na to, co je definováno v odkazované datové sadě.

Můžete upravit typy dat sloupce v transformaci odvozeného sloupce z vedlejšího datového proudu. Použijte transformaci SELECT k úpravě názvů sloupců.

### <a name="import-schema"></a>Importovat schéma

Tlačítko **importovat schéma** na kartě **projekce** umožňuje použít aktivní cluster ladění k vytvoření projekce schématu. Import schématu, který je k dispozici v každém typu zdroje, přepíše projekci definovanou v datové sadě. Objekt DataSet nebude změněn.

To je užitečné v datových sadách, jako je Avro a CosmosDB, které podporují složité datové struktury, nevyžadují, aby definice schématu existovaly v datové sadě.

## <a name="optimize-the-source-transformation"></a>Optimalizace zdrojové transformace

Na kartě **optimalizace** pro transformaci zdroje se může zobrazit typ **zdrojového** oddílu. Tato možnost je dostupná jenom v případě, že je váš zdroj Azure SQL Database. Důvodem je to, že Data Factory se snaží vytvořit propojení paralelně, aby se spouštěly velké dotazy proti vašemu zdroji SQL Database.

![Nastavení zdrojového oddílu](media/data-flow/sourcepart3.png "dělení")

Nemusíte rozdělit data na zdroj SQL Database, ale oddíly jsou užitečné pro velké dotazy. Oddíl můžete založit na sloupci nebo dotazu.

### <a name="use-a-column-to-partition-data"></a>Použití sloupce k dělení dat

Ve zdrojové tabulce vyberte sloupec, na kterém chcete vytvořit oddíly. Nastavte také počet oddílů.

### <a name="use-a-query-to-partition-data"></a>Použití dotazu k dělení dat

Můžete se rozhodnout rozdělit připojení na základě dotazu. Zadejte obsah predikátu WHERE. Zadejte například rok > 1980.

Další informace o optimalizaci v rámci mapování toku dat najdete na [kartě optimalizace](concepts-data-flow-overview.md#optimize).

## <a name="next-steps"></a>Další kroky

Začněte sestavovat [transformaci odvozeného sloupce](data-flow-derived-column.md) a [transformaci Select](data-flow-select.md).
