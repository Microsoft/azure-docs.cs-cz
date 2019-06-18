---
title: Formát odděleného textu ve službě Azure Data Factory | Dokumentace Microsoftu
description: Toto téma popisuje, jak zacházet s formátu odděleného textu ve službě Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 407b8ba2fda35d3acbf1b425bb15fe20778613d7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65146001"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Formát odděleného textu ve službě Azure Data Factory

Pokud chcete postupovat podle tohoto článku **analyzovat text s oddělovači soubory nebo zapisovat data do formátu odděleného textu**. 

Text s oddělovači formát je podporován pro následující konektory: [Amazon S3](connector-amazon-simple-storage-service.md), [objektů Blob v Azure](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Systém souborů](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)a [ SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. Tato část obsahuje seznam vlastností podporovaných datovou sadu obsahující text oddělený znaky.

| Vlastnost         | Popis                                                  | Požaduje se |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Vlastnost type datové sady, musí být nastavená na **DelimitedText**. | Ano      |
| location         | Nastavení umístění souborů. Každý konektor souborové má svůj vlastní typ umístění a nepodporuje vlastnosti z kategorie `location`. **Podrobnosti najdete v článku konektor -> vlastnosti části datové sady**. | Ano      |
| columnDelimiter  | Znaky použitý k oddělení sloupců v souboru. V současné době více znakový oddělovač je podporována pouze pro mapování toku dat, ale ne aktivitu kopírování. <br>Výchozí hodnota je **čárkou `,`** , pokud je definován oddělovač sloupců jako prázdný řetězec, což znamená, že žádný oddělovač, celý řádek je považován za jeden sloupec. | Ne       |
| rowDelimiter     | Jeden znak nebo "\r\n" slouží k oddělení řádků v souboru.<br>Výchozí hodnota je některý z následujících hodnot **pro čtení: ["\r\n", "\r", "\n"]** , a **"\n" nebo "\r\n" při zápisu do** daty mapování toku a aktivita kopírování v uvedeném pořadí. <br>Když `rowDelimiter` je nastavena na žádné oddělovače (prázdný řetězec), `columnDelimiter` musí být nastavena jako oddělovač (prázdný řetězec) jako dobře, což znamená, že bude považovat celý obsah jako jednu hodnotu. | Ne       |
| quoteChar        | Jeden znak do uvozovek hodnoty sloupce, pokud obsahuje oddělovač sloupců. <br>Výchozí hodnota je **dvojité uvozovky** `"`. <br>Pro mapování toku dat `quoteChar` nemůže být prázdný řetězec. <br>Pro aktivitu kopírování když `quoteChar` je definovaný jako prázdný řetězec, to znamená, že neexistuje žádný znak uvozovky a hodnota sloupce není uveden v uvozovkách, a `escapeChar` se používá k uvození oddělovače sloupců a jí samou. | Ne       |
| escapeChar       | Jeden znak pro přepnutí uvozovky uvnitř hodnoty v uvozovkách.<br>Výchozí hodnota je **zpětné lomítko `\`** . <br>Pro mapování toku dat `escapeChar` nemůže být prázdný řetězec. <br/>Pro aktivitu kopírování když `escapeChar` je definovaný jako prázdný řetězec, `quoteChar` musí být nastavena jako prázdný řetězec i se v takovém případě se ujistěte, že všechny hodnoty ve sloupcích neobsahují oddělovače. | Ne       |
| firstRowAsHeader | Určuje, zda považovat/zkontrolujte první řádek jako záhlaví řádku s názvy sloupců.<br>Povolené hodnoty jsou **true** a **false** (výchozí). | Ne       |
| nullValue        | Určuje řetězcovou reprezentaci hodnoty null. <br>Výchozí hodnota je **prázdný řetězec**. | Ne       |
| encodingName     | Typ kódování použité pro čtení a zápis souborů testu. <br>Povolené hodnoty jsou následující: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US ASCII", "UTF-7", "BIG5", "EUC JP", "EUC KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", " IBM852 ","IBM855","IBM857","IBM860","IBM861","IBM863","IBM864","IBM865","IBM869","IBM870","IBM01140","IBM01141","IBM01142","IBM01143","IBM01144","IBM01145","IBM01146","IBM01147","IBM01148","IBM01149","ISO-2022-JP","ISO-2022-KR ","ISO-8859-1","ISO-8859-2","ISO-8859-3","ISO-8859-4","ISO-8859-5","ISO-8859-6","ISO-8859-7","ISO-8859-8","ISO-8859-9","ISO-8859-13","ISO-8859-15","WINDOWS-874","WINDOWS-1250","WINDOWS-1251","WINDOWS-1252","WINDOWS-1253"," WINDOWS – 1254 ","WINDOWS-1255","WINDOWS-1256","WINDOWS-1257","WINDOWS-1258".<br>Všimněte si, že mapování toku dat nepodporuje kódování UTF-7. | Ne       |
| compressionCodec | Kompresní kodek používá pro čtení a zápis textových souborů. <br>Povolené hodnoty jsou **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **Tenhle**, nebo **lz4**. Chcete-li použít při ukládání souboru. <br>Poznámka: aktuálně, aktivita kopírování nepodporuje "Tenhle" & "lz4" a mapování toku dat nepodporuje "ZipDeflate". | Ne       |
| compressionLevel | Kompresní poměr. <br>Povolené hodnoty jsou **Optimal** nebo **nejrychlejší**.<br>- **Nejrychlejší:** Komprese operace by se měla dokončit tak rychle, jako je to možné, i v případě, že výsledný soubor není komprimována optimálně.<br>- **Optimální**: Komprese operace by měla být optimálně v komprimovaném tvaru i v případě, že operace trvá delší dobu pro dokončení. Další informace najdete v tématu [úroveň komprese](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) tématu. | Ne       |

Níže je příklad obsahující text oddělený znaky datová sada Azure Blob Storage:

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností nepodporuje text s oddělovači zdroje a jímky.

### <a name="delimited-text-as-source"></a>Text s oddělovači jako zdroj 

Následující vlastnosti jsou podporovány v aktivitě kopírování ***\*zdroj\**** oddílu.

| Vlastnost       | Popis                                                  | Požaduje se |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | Vlastnost type zdroje aktivity kopírování musí být nastavená na **DelimitedTextSource**. | Ano      |
| formatSettings | Skupina vlastností. Odkazovat na **text s oddělovači čtení nastavení** následující tabulka. | Ne       |
| storeSettings  | Skupina vlastností o tom, jak přečíst data z úložiště dat. Každý konektor souborové má svůj vlastní podporovaných čtení nastavení v části `storeSettings`. **Podrobnosti najdete v článku konektor -> oddíl properties aktivity kopírování**. | Ne       |

Podporované **obsahující text oddělený znaky čtení nastavení** pod `formatSettings`:

| Vlastnost      | Popis                                                  | Požaduje se |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Typ formatSettings nastavené na **DelimitedTextReadSetting**. | Ano      |
| skipLineCount | Označuje počet **neprázdný** řádků, které mají přeskočit při čtení dat ze vstupních souborů. <br>Pokud je zadaný parametr skipLineCount i firstRowAsHeader, nejdřív se přeskočí příslušný počet řádků a potom se ze vstupního souboru načtou informace záhlaví. | Ne       |

### <a name="delimited-text-as-sink"></a>Text s oddělovači jako jímka

Následující vlastnosti jsou podporovány v aktivitě kopírování ***\*jímky\**** oddílu.

| Vlastnost       | Popis                                                  | Požaduje se |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | Vlastnost type zdroje aktivity kopírování musí být nastavená na **DelimitedTextSink**. | Ano      |
| formatSettings | Skupina vlastností. Odkazovat na **text s oddělovači zápis nastavení** následující tabulka. |          |
| storeSettings  | Skupina vlastností o tom, jak zapsat data do úložiště dat. Každý konektor souborové má vlastní podporované zápisu nastavení v části `storeSettings`. **Podrobnosti najdete v článku konektor -> oddíl properties aktivity kopírování**. | Ne       |

Podporované **obsahující text oddělený znaky zápis nastavení** pod `formatSettings`:

| Vlastnost      | Popis                                                  | Požaduje se                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | Typ formatSettings nastavené na **DelimitedTextWriteSetting**. | Ano                                                   |
| fileExtension | Přípona souboru použít k pojmenování výstupních souborů, například `.csv`, `.txt`. Musí být zadán při `fileName` není zadaný ve výstupu DelimitedText datové sady. | Ano, pokud není zadaný název souboru v výstupní datovou sadu |

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

Další podrobnosti o [zdroje transformace](data-flow-source.md) a [jímky transformace](data-flow-sink.md) v mapování se předávají Data.

## <a name="next-steps"></a>Další postup

- [Přehled aktivit kopírování](copy-activity-overview.md)
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)