---
title: Transformace jímky v toku dat mapování
description: Zjistěte, jak nakonfigurovat transformaci jímky v toku dat mapování.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: 4b10a4c98abd6bec4074bf35764a9cbb85d5b157
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605970"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Transformace jímky v toku dat mapování

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Po transformaci dat můžete uhloubení dat do cílové datové sady. Každý tok dat vyžaduje alespoň jednu transformaci jímky, ale můžete zapisovat do tolik jímky podle potřeby k dokončení toku transformace. Chcete-li zapisovat do dalších jímek, vytvořte nové datové proudy prostřednictvím nových větví a podmíněných rozdělení.

Každá transformace jímky je přidružena přesně k jedné datové sadě Datové továrny. Datová sada definuje tvar a umístění dat, do kterých chcete zapisovat.

## <a name="supported-sink-connectors-in-mapping-data-flow"></a>Podporované konektory jímky v toku dat mapování

V současné době následující datové sady lze použít v transformaci jímky:
    
* [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, Avro, Text, Parkety)
* [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, Avro, Text, Parkety)
* [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, Avro, Text, Parkety)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

Nastavení specifická pro tyto konektory jsou umístěna na kartě **Nastavení.** 

Azure Data Factory má přístup k více než [90 nativním konektorům](connector-overview.md). Chcete-li zapisovat data do těchto jiných zdrojů z toku dat, použijte aktivitu kopírování k načtení těchto dat z jedné z podporovaných pracovních oblastí po dokončení toku dat.

## <a name="sink-settings"></a>Nastavení jímky

Po přidání jímky nakonfigurujte na kartě **Jímka.** Zde můžete vybrat nebo vytvořit datovou sadu, do které vaše jímky zapisují. Níže je video vysvětlující řadu různých možností jímky pro typy souborů oddělených textem:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tf7T]

![Nastavení jímky](media/data-flow/sink-settings.png "Nastavení jímky")

**Posun schématu:** [Posun schématu](concepts-data-flow-schema-drift.md) je schopnost datové továrny nativně zpracovávat flexibilní schémata v tocích dat bez nutnosti explicitně definovat změny sloupců. Povolit **povolit posun schématu** zapsat další sloupce nad co je definováno ve schématu dat jímky.

**Ověřit schéma:** Pokud je vybráno schéma ověření, tok dat se nezdaří, pokud není ve zdrojové projekci nalezen žádný sloupec schématu příchozího zdroje nebo pokud se datové typy neshodují. Toto nastavení slouží k vynucení, že zdrojová data splňují smlouvu definované projekce. Je velmi užitečné ve scénářích zdroj databáze signál, že názvy sloupců nebo typy byly změněny.

## <a name="field-mapping"></a>Mapování pole

Podobně jako select transformace, v **mapování** kartu jímky, můžete rozhodnout, které příchozí sloupce budou zapsány. Ve výchozím nastavení jsou mapovány všechny vstupní sloupce, včetně posunutých sloupců. Tento jetomu označován jako **automatické mapování**.

Když automatické mapování vypnete, budete mít možnost přidat buď pevná mapování založená na sloupech, nebo mapování založená na pravidlech. Mapování založené na pravidlech umožňuje psát výrazy s porovnáváním vzorů, zatímco pevné mapování mapuje logické a fyzické názvy sloupců. Další informace o mapování založeném na pravidlech naleznete [v tématu vzory sloupců v toku dat mapování](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink).

## <a name="custom-sink-ordering"></a>Vlastní řazení jímky

Ve výchozím nastavení jsou data zapsána do více jímek v nedeterministickém pořadí. Spuštění motoru bude zapisovat data paralelně jako logika transformace je dokončena a řazení jímky se může lišit každý běh. Chcete-li zadat a přesné řazení jímky, povolte **vlastní řazení jímky** v obecné záložce toku dat. Pokud je povoleno, propady budou zapsány postupně v pořadí zvyšování.

![Vlastní řazení jímky](media/data-flow/custom-sink-ordering.png "Vlastní řazení jímky")

## <a name="data-preview-in-sink"></a>Náhled dat v jímce

Při načítání náhledu dat v ladicím clusteru se do jímky nezapisují žádná data. Snímek toho, jak data vypadají, bude vrácen, ale nic nebude zapsáno do cíle. Chcete-li otestovat zápis dat do jímky, spusťte ladění kanálu z plátna kanálu.

## <a name="next-steps"></a>Další kroky
Teď, když jste vytvořili tok dat, přidejte [aktivitu toku dat do kanálu](concepts-data-flow-overview.md).
