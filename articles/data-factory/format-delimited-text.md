---
title: Formát textu s oddělovačem ve Službě Azure Data Factory
description: Toto téma popisuje, jak se vypořádat s odděleným textovým formátem v Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: jingwang
ms.openlocfilehash: 47d26ad452b8494e591ee919076e5ade8bf19cd7
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011391"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Formát textu s oddělovačem ve Službě Azure Data Factory

Tento článek následuje, pokud chcete **analyzovat oddělené textové soubory nebo zapsat data do odděleného textového formátu**. 

Formát textu s oddělovači je podporovaný pro následující konektory: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), File [System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)a [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [Datové sady.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných oddělenou textovou datovou sadou.

| Vlastnost         | Popis                                                  | Požaduje se |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Vlastnost type datové sady musí být nastavena na **DelimitedText**. | Ano      |
| location         | Nastavení umístění souborů. Každý konektor založený na souboru má svůj `location`vlastní typ umístění a podporované vlastnosti v části .  | Ano      |
| columnDelimiter  | Znaky použité k oddělení sloupců v souboru. <br>Výchozí hodnota je **čárka `,` **. Pokud je oddělovač sloupců definován jako prázdný řetězec, což znamená, že oddělovač není, celý řádek se považuje za jeden sloupec.<br>V současné době je oddělovač sloupců jako prázdný řetězec nebo více znaků podporován pouze pro mapování toku dat, ale ne pro aktivitu kopírování.  | Ne       |
| rowDelimiter     | Jeden znak nebo "\r\n" slouží k oddělení řádků v souboru. <br>Výchozí hodnota je některá z následujících hodnot **při čtení: ["\r\n", "\r", "\n"]** a **"\n" nebo "\r\n" při zápisu** mapováním toku dat a aktivity kopírování. <br>Pokud je oddělovač řádků nastaven na žádný oddělovač (prázdný řetězec), musí být oddělovač sloupců nastaven jako žádný oddělovač (prázdný řetězec), což znamená, že celý obsah bude považován za jednu hodnotu.<br>V současné době je oddělovač řádků jako prázdný řetězec podporován pouze pro mapování toku dat, ale ne pro aktivitu kopírování. | Ne       |
| quoteChar        | Jeden znak pro uvozovky hodnoty sloupců, pokud obsahuje oddělovač sloupců. <br>Výchozí hodnota je **dvojité uvozovky** `"`. <br>Pro mapování toku dat `quoteChar` nemůže být prázdný řetězec. <br>Pro aktivitu `quoteChar` Copy, pokud je definován jako prázdný řetězec, znamená to, `escapeChar` že neexistuje žádný znak uvozovky a hodnota sloupce není citována a používá se k úniku oddělovače sloupců a samotného. | Ne       |
| escapeChar       | Jediný znak uniknout uvozovky uvnitř uvozovky.<br>Výchozí hodnota je **zpětné lomítko `\` **. <br>Pro mapování toku dat `escapeChar` nemůže být prázdný řetězec. <br/>Pro aktivitu `escapeChar` Copy, pokud je `quoteChar` definován jako prázdný řetězec, musí být nastaven a také prázdný řetězec, v takovém případě se ujistěte, že všechny hodnoty sloupců neobsahují oddělovače. | Ne       |
| firstRowAsHeader | Určuje, zda má být první řádek zpracován jako řádek záhlaví s názvy sloupců.<br>Povolené hodnoty jsou **pravdivé** a **nepravdivé** (výchozí). | Ne       |
| nullValue        | Určuje řetězcovou reprezentaci nulové hodnoty. <br>Výchozí hodnota je **prázdný řetězec**. | Ne       |
| encodingName     | Typ kódování používaný ke čtení a zápisu testovacích souborů. <br>Allowed values are as follows: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13" , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".<br>Poznámka: Mapování toku dat nepodporuje kódování UTF-7. | Ne       |
| kompreseKodlič | Kompresní kodek používaný ke čtení a zápisu textových souborů. <br>Povolené hodnoty jsou **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **snappy**nebo **lz4**. Výchozí hodnota není komprimována. <br>**Poznámka:** Aktuálně kopírovat aktivitu nepodporuje "snappy" & "lz4" a mapování toku dat nepodporuje "ZipDeflate". <br>**Poznámka:** Při použití kopírování aktivity dekompresi ZipDeflate soubor (y) a zapisovat `<path specified in dataset>/<folder named as source zip file>/`do úložiště dat jímky založené na souboru, soubory budou extrahovány do složky: . | Ne       |
| kompreseÚroveň | Kompresní poměr. <br>Povolené hodnoty jsou **optimální** nebo **nejrychlejší**.<br>- **Nejrychlejší:** Operace komprese by měla být dokončena co nejrychleji, a to i v případě, že výsledný soubor není optimálně komprimován.<br>- **Optimální**: Operace komprese by měla být optimálně komprimována, i když operace trvá delší dobu. Další informace naleznete v tématu [Úroveň komprese.](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) | Ne       |

Níže je uveden příklad oddělené textové datové sady ve službě Azure Blob Storage:

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
            "escapeChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných odděleným zdrojem textu a jímkou.

### <a name="delimited-text-as-source"></a>Oddělovač textu jako zdroje 

Následující vlastnosti jsou podporovány v části *** \*zdroje aktivity\* *** kopírování.

| Vlastnost       | Popis                                                  | Požaduje se |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | Vlastnost type zdroje aktivity kopírování musí být nastavena na **DelimitedTextSource**. | Ano      |
| formatSettings | Skupina vlastností. Viz **Oddělující text číst nastavení** níže. | Ne       |
| storeSettings  | Skupina vlastností o čtení dat z úložiště dat. Každý konektor založený na souborech má `storeSettings`vlastní podporovaná nastavení čtení v části . | Ne       |

Podporované **nastavení čtení odděleného textu** v části `formatSettings`:

| Vlastnost      | Popis                                                  | Požaduje se |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Typ formatSettings musí být nastavena na **DelimitedTextReadSettings**. | Ano      |
| skipLineCount | Označuje počet **neprázdných** řádků, které mají být při čtení dat ze vstupních souborů přeskočte. <br>Pokud je zadaný parametr skipLineCount i firstRowAsHeader, nejdřív se přeskočí příslušný počet řádků a potom se ze vstupního souboru načtou informace záhlaví. | Ne       |

### <a name="delimited-text-as-sink"></a>Oddělená čára jako jímka

Následující vlastnosti jsou podporovány v části *** \*jímky\* *** aktivity kopírování.

| Vlastnost       | Popis                                                  | Požaduje se |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | Vlastnost type zdroje aktivity kopírování musí být nastavena na **DelimitedTextSink**. | Ano      |
| formatSettings | Skupina vlastností. Viz **Oddělující text nastavení nastavení** níže. |          |
| storeSettings  | Skupina vlastností o tom, jak zapisovat data do úložiště dat. Každý konektor založený na souborech má `storeSettings`vlastní podporovaná nastavení zápisu v části .  | Ne       |

Podporované **nastavení zápisu odděleného textu** pod `formatSettings`:

| Vlastnost      | Popis                                                  | Požaduje se                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | Typ formatSettings musí být nastaven a **DelimitedTextWriteSettings**. | Ano                                                   |
| Fileextension | Přípona souboru použitá k pojmenování `.csv` `.txt`výstupních souborů, např. Musí být zadán, `fileName` pokud není zadán ve výstupní datové sadě OdlimitedText. Pokud je název souboru nakonfigurován ve výstupní datové sadě, bude použit jako název souboru jímky a nastavení přípony souboru bude ignorováno.  | Ano, pokud není ve výstupní datové sadě zadán název souboru |

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

Naučte se podrobnosti z [transformace zdroje](data-flow-source.md) a [transformace jímky](data-flow-sink.md) v toku dat mapování.

## <a name="next-steps"></a>Další kroky

- [Kopírovat přehled aktivit](copy-activity-overview.md)
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)
- [Aktivita getMetadata](control-flow-get-metadata-activity.md)
