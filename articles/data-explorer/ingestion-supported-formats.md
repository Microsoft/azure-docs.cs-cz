---
title: Datové formáty podporované Aplikací Dat Azure pro ingestování.
description: Přečtěte si o různých formátech dat a komprese podporovaných Aplikací Azure Data Explorer pro ingestování.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 16ecdf7ac3f0062637e4bbea86d26e2560f38222
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235297"
---
# <a name="data-formats-supported-by-azure-data-explorer-for-ingestion"></a>Datové formáty podporované Aplikací Azure Data Explorer pro ingestování

Při jízání dat je proces, kterým se data přidávají do tabulky a jsou dostupná pro dotaz v Průzkumníku dat Azure. Pro všechny metody ingestování, jiné než ingest-from-query, data musí být v jednom z podporovaných formátů. V následující tabulce jsou uvedeny a popsány formáty, které Azure Data Explorer podporuje pro ingestování dat.

|Formát   |Linka   |Popis|
|---------|------------|-----------|
|Avro     |`.avro`     |[Soubor kontejneru Avro](https://avro.apache.org/docs/current/). Jsou podporovány následující `null` `deflate` kódy: , (`snappy` aktuálně není podporováno).|
|CSV      |`.csv`      |Textový soubor s hodnotami oddělenými`,`čárkami ( ). Viz [RFC 4180: _Common Format and MIME Type for Comma-Separated Values (CSV) Files_](https://www.ietf.org/rfc/rfc4180.txt).|
|JSON     |`.json`     |Textový soubor s objekty JSON oddělenými `\n` nebo `\r\n`. Viz [Řádky JSON (JSONL)](http://jsonlines.org/).|
|MultiJSON|`.multijson`|Textový soubor s json pole vlastnost tašky (každý představuje záznam) nebo libovolný počet `\n` vlastností pytle vymezené mezery nebo `\r\n`. Každá vlastnost taška může být rozložena na více řádků. Tento formát je `JSON`upřednostňován před , pokud se nejedná o nemajetková zavazadla.|
|Orc      |`.orc`      |[Soubor Skortek](https://en.wikipedia.org/wiki/Apache_ORC).|
|Parketové  |`.parquet`  |[Parketový soubor](https://en.wikipedia.org/wiki/Apache_Parquet).|
|PSV      |`.psv`      |Textový soubor s hodnotami<code>&#124;</code>oddělenými potrubím ( ).|
|Syrové      |`.raw`      |Textový soubor, jehož celý obsah je hodnota jednoho řetězce.|
|SCsv     |`.scsv`     |Textový soubor s hodnotami oddělenými středníkem (`;`).|
|SOHsv    |`.sohsv`    |Textový soubor s hodnotami oddělenými SOH. (SOH je ascii kódový bod 1; tento formát používá Hive na HDInsight.)|
|Tsv      |`.tsv`      |Textový soubor s hodnotami`\t`oddělenými tabulátory ( ).|
|TSVE     |`.tsv`      |Textový soubor s hodnotami`\t`oddělenými tabulátory ( ). Znak zpětného`\`lomítka ( ) se používá pro únik.|
|TXT      |`.txt`      |Textový soubor s řádky `\n`oddělenými písmenem . Prázdné řádky jsou přeskočeny.|

## <a name="supported-data-compression-formats"></a>Podporované formáty komprese dat

Objekty BLOB a soubory lze komprimovat pomocí některého z následujících kompresních algoritmů:

|Komprese|Linka|
|-----------|---------|
|Gzip       |.gz      |
|ZIP        |.zip     |

Označte kompresi připojením přípony k názvu objektu blob nebo souboru.

Například:
* `MyData.csv.zip`označuje objekt blob nebo soubor formátovaný jako CSV, komprimovaný pomocí ZIP (archiv nebo jeden soubor)
* `MyData.csv.gz`označuje objekt BLOB nebo soubor formátovaný jako CSV, komprimovaný pomocí GZip

Je podporována také názvy objektů Blob nebo souborů, které neobsahují rozšíření formátu, ale pouze komprese (například). V takovém případě musí být formát souboru zadán jako vlastnost ingestování, protože ji nelze odvodit.

> [!NOTE]
> Některé formáty komprese sledují původní příponu souboru jako součást komprimovaného datového proudu. Toto rozšíření je obecně ignorováno pro určení formátu souboru. Pokud formát souboru nelze určit z (komprimovaného) objektu blob nebo `format` názvu souboru, musí být zadán prostřednictvím vlastnosti ingestion.

## <a name="next-steps"></a>Další kroky

* Další informace o [přijím dat](/azure/data-explorer/ingest-data-overview)
* Další informace o [vlastnostech přiřazování dat Průzkumníka dat Azure](ingestion-properties.md)
