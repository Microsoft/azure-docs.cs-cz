---
title: Textový formát s oddělovači v Azure Data Factory | Microsoft Docs
description: Toto téma popisuje, jak v Azure Data Factory pracovat s odděleným formátem textu.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: fb836b44ebd567f0ce1c833ca523b1c199ed9c9a
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72785998"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Textový formát s oddělovači v Azure Data Factory

Použijte tento článek, pokud chcete **analyzovat soubory s oddělovači nebo zapsat data do textového formátu s oddělovači**. 

Pro následující konektory se podporuje textový formát s oddělovači: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [systém souborů](connector-file-system.md), [FTP](connector-ftp.md), [ Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)a [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných datovou sadou textů s oddělovači.

| Vlastnost         | Popis                                                  | Požaduje se |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Vlastnost Type datové sady musí být nastavená na **DelimitedText**. | Ano      |
| location         | Nastavení umístění souborů. Každý konektor založený na souborech má svůj vlastní typ umístění a podporované vlastnosti v rámci `location`.  | Ano      |
| columnDelimiter  | Znak (y) použitý k oddělení sloupců v souboru. V současné době je oddělovač více znaků podporován pouze pro tok dat mapování, nikoli pro aktivitu kopírování. <br>Výchozí hodnota je **čárka `,`** , pokud je oddělovač sloupce definován jako prázdný řetězec, což znamená bez oddělovače, je celý řádek považován za jeden sloupec. | Ne       |
| rowDelimiter     | Jeden znak nebo "\r\n", který se používá k oddělení řádků v souboru.<br>Výchozí hodnota je libovolná z následujících hodnot pro **čtení: ["\r\n", "\r", "\n"]** a **"\n" nebo "\r\n" při zápisu** pomocí mapování toku dat a aktivity kopírování v uvedeném pořadí. <br>Je-li hodnota `rowDelimiter` nastavená na hodnotu bez oddělovače (prázdný řetězec), musí být `columnDelimiter` nastavena také na hodnotu bez oddělovače (prázdný řetězec), což znamená, že bude celý obsah zacházet jako jediná hodnota. | Ne       |
| quoteChar        | Jediný znak pro hodnoty sloupce uvozovek, pokud obsahuje oddělovač sloupců. <br>Výchozí hodnota je **dvojité uvozovky** `"`. <br>Pro mapování toku dat `quoteChar` nemůže být prázdný řetězec. <br>Pokud je pro aktivitu kopírování definovaná hodnota `quoteChar`, znamená to, že není v uvozovkách žádná znak citace ani hodnota sloupce a `escapeChar` se používá k opuštění oddělovače sloupců a sebe samé. | Ne       |
| escapeChar       | Jeden znak pro řídicí uvozovky uvnitř hodnoty v uvozovkách.<br>Výchozí hodnota je **zpětné lomítko `\`** . <br>Pro mapování toku dat `escapeChar` nemůže být prázdný řetězec. <br/>Pokud je pro aktivitu kopírování definovaná hodnota `escapeChar`, musí být hodnota `quoteChar` nastavena jako prázdný řetězec. v takovém případě zajistěte, aby všechny hodnoty sloupce neobsahovaly oddělovače. | Ne       |
| firstRowAsHeader | Určuje, zda má být první řádek považován za řádek záhlaví s názvy sloupců.<br>Povolené hodnoty jsou **true** a **false** (výchozí). | Ne       |
| nullValue        | Určuje řetězcovou reprezentaci hodnoty null. <br>Výchozí hodnota je **prázdný řetězec**. | Ne       |
| encodingName     | Typ kódování používaný pro čtení a zápis testovacích souborů. <br>Povolené hodnoty jsou následující: UTF-8, UTF-16, "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", " IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149" , "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13", "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", " WINDOWS-1252 "," WINDOWS-1253 "," WINDOWS-1254 "," WINDOWS-1255 "," WINDOWS-1256 "," WINDOWS-1257 "," WINDOWS-1258 ".<br>Datový tok mapování poznámek nepodporuje kódování UTF-7. | Ne       |
| compressionCodec | Kompresní kodek používaný pro čtení a zápis textových souborů. <br>Povolené hodnoty jsou **bzip2**, **gzip**, **Deflate**, **ZipDeflate**, **přichycení**nebo **LZ4**. pro použití při ukládání souboru. <br>Poznámka: aktivita kopírování nepodporuje "přichycení" & "LZ4" a tok dat mapování nepodporuje "ZipDeflate". | Ne       |
| compressionLevel | Kompresní poměr <br>Povolené hodnoty jsou **optimální** nebo **nejrychlejší**.<br>- **nejrychlejší:** komprese by měla být dokončena co nejrychleji, a to i v případě, že výsledný soubor není optimálně komprimován.<br>- **optimální**: operace komprese by měla být optimálně komprimovaná, i když dokončení operace trvá déle. Další informace najdete v tématu [úroveň komprese](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . | Ne       |

Níže je příklad objektu s oddělovači textu v Azure Blob Storage:

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . Tato část uvádí seznam vlastností, které jsou podporovány zdrojem a jímkou textu s oddělovači.

### <a name="delimited-text-as-source"></a>Text s oddělovači jako zdroj 

V části ***\*zdrojového\**** aktivity kopírování jsou podporovány následující vlastnosti.

| Vlastnost       | Popis                                                  | Požaduje se |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **DelimitedTextSource**. | Ano      |
| formatSettings | Skupina vlastností Viz tabulka **nastavení čtení s oddělovači textu** níže. | Ne       |
| storeSettings  | Skupina vlastností, jak číst data z úložiště dat. Každý konektor založený na souborech má své vlastní podporované nastavení čtení v části `storeSettings`. | Ne       |

Podporované **nastavení čtení textu s oddělovači** v rámci `formatSettings`:

| Vlastnost      | Popis                                                  | Požaduje se |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Typ formatSettings musí být nastaven na hodnotu **DelimitedTextReadSetting**. | Ano      |
| skipLineCount | Označuje počet **neprázdných** řádků, které se mají přeskočit při čtení dat ze vstupních souborů. <br>Pokud je zadaný parametr skipLineCount i firstRowAsHeader, nejdřív se přeskočí příslušný počet řádků a potom se ze vstupního souboru načtou informace záhlaví. | Ne       |

### <a name="delimited-text-as-sink"></a>Text s oddělovači jako jímku

V části\*aktivita kopírování ***\*jímka*** jsou podporovány následující vlastnosti.

| Vlastnost       | Popis                                                  | Požaduje se |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **DelimitedTextSink**. | Ano      |
| formatSettings | Skupina vlastností Viz tabulka **nastavení zápisu s oddělovači textu** níže. |          |
| storeSettings  | Skupina vlastností, jak zapisovat data do úložiště dat. Jednotlivé konektory založené na souborech mají vlastní podporované nastavení zápisu v rámci `storeSettings`.  | Ne       |

Podporované **nastavení zápisu textu s oddělovači** v rámci `formatSettings`:

| Vlastnost      | Popis                                                  | Požaduje se                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | Typ formatSettings musí být nastaven na hodnotu **DelimitedTextWriteSetting**. | Ano                                                   |
| fileExtension | Přípona souboru, která slouží k pojmenování výstupních souborů, např. `.csv`, `.txt`. Je nutné zadat, když `fileName` není zadáno ve výstupní datové sadě DelimitedText. | Ano, pokud není zadaný název souboru v výstupní datové sadě |

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

Přečtěte si podrobnosti o [transformaci zdrojového kódu](data-flow-source.md) a [transformaci jímky](data-flow-sink.md) v mapování toku dat.

## <a name="next-steps"></a>Další kroky

- [Přehled aktivit kopírování](copy-activity-overview.md)
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
