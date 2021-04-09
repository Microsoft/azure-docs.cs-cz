---
title: Rozdílový formát v Azure Data Factory
description: Transformace a přesun dat ze rozdílových Lake pomocí formátu Delta
author: djpmsft
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: daperlov
ms.openlocfilehash: 74df809f2206a105b405ba184949ef887096ebc2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932501"
---
# <a name="delta-format-in-azure-data-factory"></a>Rozdílový formát v Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak kopírovat data do a z rozdílového jezera uloženého v [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md) nebo v [Azure Blob Storage](connector-azure-blob-storage.md) pomocí rozdílového formátu. Tento konektor je k dispozici jako [vložená datová sada](data-flow-source.md#inline-datasets) v mapování dat toků jako zdroj i jímka.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4ALTs]

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

Tento konektor je k dispozici jako [vložená datová sada](data-flow-source.md#inline-datasets) v mapování dat toků jako zdroj i jímka.

### <a name="source-properties"></a>Vlastnosti zdroje

V níže uvedené tabulce jsou uvedeny vlastnosti podporované rozdílovým zdrojem. Tyto vlastnosti můžete upravit na kartě **Možnosti zdrojového kódu** .

| Název | Popis | Povinné | Povolené hodnoty | Vlastnost skriptu toku dat |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formát | Formát musí být `delta` | ano | `delta` | formát |
| Systém souborů | Kontejner/souborový systém rozdílových Lake | ano | Řetězec | Systému souborů |
| Cesta ke složce | Přímo na rozdíl od rozdílových Lake | ano | Řetězec | folderPath |
| Typ komprese | Typ komprese tabulky Delta | ne | `bzip2`<br>`gzip`<br>`deflate`<br>`ZipDeflate`<br>`snappy`<br>`lz4` | Argument |
| Úroveň komprese | Vyberte, jestli se komprese dokončí co nejrychleji, nebo jestli se má výsledný soubor optimálně komprimovat. | požadováno `compressedType` , pokud je zadáno. | `Optimal` nebo `Fastest` | compressionLevel |
| Doba jízdy | Vyberte, jestli se má dotazovat na starší snímek tabulky Delta. | ne | Dotaz podle časového razítka: časové razítko <br> Dotaz podle verze: celé číslo | timestampAsOf <br> versionAsOf |
| Nenalezeny žádné soubory | Pokud je nastaveno na true, chyba není vyvolána, pokud nebyly nalezeny žádné soubory. | ne | `true` nebo `false` | ignoreNoFilesFound |

#### <a name="import-schema"></a>Importovat schéma

Rozdíl je k dispozici pouze jako vložená datová sada a ve výchozím nastavení nemá přidružené schéma. Chcete-li získat metadata sloupce, klikněte na tlačítko **importovat schéma** na kartě **projekce** . To vám umožní odkazovat na názvy sloupců a datové typy určené parametrem corpus. Pro import schématu musí být [relace ladění toku dat](concepts-data-flow-debug-mode.md) aktivní a musíte mít existující definiční soubor entity CDM, na který odkazuje.
 

### <a name="delta-source-script-example"></a>Příklad zdrojového skriptu Delta

```
source(output(movieId as integer,
            title as string,
            releaseDate as date,
            rated as boolean,
            screenedOn as timestamp,
            ticketPrice as decimal(10,2)
            ),
    store: 'local',
    format: 'delta',
    versionAsOf: 0,
    allowSchemaDrift: false,
    folderPath: $tempPath + '/delta'
  ) ~> movies
```

### <a name="sink-properties"></a>Vlastnosti jímky

V níže uvedené tabulce jsou uvedeny vlastnosti, které jsou podporovány rozdílovou jímkou. Tyto vlastnosti můžete upravit na kartě **Nastavení** .

| Název | Popis | Povinné | Povolené hodnoty | Vlastnost skriptu toku dat |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formát | Formát musí být `delta` | ano | `delta` | formát |
| Systém souborů | Kontejner/souborový systém rozdílových Lake | ano | Řetězec | Systému souborů |
| Cesta ke složce | Přímo na rozdíl od rozdílových Lake | ano | Řetězec | folderPath |
| Typ komprese | Typ komprese tabulky Delta | ne | `bzip2`<br>`gzip`<br>`deflate`<br>`ZipDeflate`<br>`snappy`<br>`lz4` | Argument |
| Úroveň komprese | Vyberte, jestli se komprese dokončí co nejrychleji, nebo jestli se má výsledný soubor optimálně komprimovat. | požadováno `compressedType` , pokud je zadáno. | `Optimal` nebo `Fastest` | compressionLevel |
| Vacuum | Zadejte prahovou hodnotu uchování v hodinách pro starší verze tabulky. Výchozí hodnota je 0 nebo menší než 30 dní. | ano | Integer | sávací |
| Update – metoda | Určete, které operace aktualizace jsou povoleny na rozdílovém Lake. Pro metody, které nejsou vloženy, je nutné před označením řádků označit předchozí transformaci řádků. | ano | `true` nebo `false` | lze odstranit <br> vložitelný <br> aktualizovatelné <br> sloučení |
| Optimalizovaný zápis | Zajištění vyšší propustnosti pro operace zápisu prostřednictvím Optimalizace interního náhodného vykonání v modulech Spark. V důsledku toho si můžete všimnout méně oddílů a souborů o větší velikosti. | ne | `true` nebo `false` | optimizedWrite: true |
| Automatická komprese | Po dokončení jakékoliv operace zápisu vytvoří Spark automaticky ```OPTIMIZE``` příkaz pro opětovné uspořádání dat. v případě potřeby bude mít v případě potřeby další oddíly, aby bylo možné lépe číst výkon v budoucnu. | ne | `true` nebo `false` |   autoCompact: true |

### <a name="delta-sink-script-example"></a>Příklad skriptu jímky v rozdílu

Přidružený skript toku dat je:

```
moviesAltered sink(
          input(movieId as integer,
                title as string
            ),
           mapColumn(
                movieId,
                title
            ),
           insertable: true,
           updateable: true,
           deletable: true,
           upsertable: false,
           keys: ['movieId'],
            store: 'local',
           format: 'delta',
           vacuum: 180,
           folderPath: $tempPath + '/delta'
           ) ~> movieDB
```

### <a name="known-limitations"></a>Známá omezení

Při zápisu do rozdílové jímky existuje známé omezení, kde počet zapsaných řádků nebude vrácen ve výstupu monitorování.

## <a name="next-steps"></a>Další kroky

* Vytvoří [transformaci zdroje](data-flow-source.md) v toku dat mapování.
* Vytvoří [transformaci jímky](data-flow-sink.md) při mapování toku dat.
* Vytvořte [transformaci ALTER Row](data-flow-alter-row.md) k označení řádků jako INSERT, Update, Upsert nebo DELETE.
