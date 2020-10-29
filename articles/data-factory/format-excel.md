---
title: Excelový formát v Azure Data Factory
description: Toto téma popisuje, jak pracovat s excelovým formátem v Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: jingwang
ms.openlocfilehash: 74cfabff22074ee405d7b417e306da62ef69ae19
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "92927104"
---
# <a name="excel-format-in-azure-data-factory"></a>Excelový formát v Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Chcete-li **analyzovat soubory aplikace Excel** , postupujte podle tohoto článku. Azure Data Factory podporuje obě ". xls" i ". xlsx".

Formát aplikace Excel je podporován pro následující konektory [: Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)a [SFTP](connector-sftp.md). Podporuje se jako zdroj, ale ne jímka. 

**Poznámka** : při použití [protokolu HTTP](connector-http.md)není podporován formát ". xls". 

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných datovou sadou aplikace Excel.

| Vlastnost         | Popis                                                  | Povinné |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Vlastnost Type datové sady musí být nastavená na **Excel** .   | Yes      |
| location         | Nastavení umístění souborů. Každý konektor založený na souborech má svůj vlastní typ umístění a podporované vlastnosti v rámci `location` . | Yes      |
| sheetName        | Název sešitu aplikace Excel, ve kterém se mají číst data                       | Yes      |
| range            | Rozsah buněk v daném listu pro hledání selektivních dat, např.:<br>-Neurčeno: přečte celý list jako tabulku z prvního neprázdného řádku a sloupce.<br>- `A3`: přečte tabulku od dané buňky, dynamicky detekuje všechny řádky níže a všechny sloupce napravo.<br>- `A3:H5`: přečte tento pevný rozsah jako tabulku.<br>- `A3:A3`: přečte tuto jedinou buňku. | No       |
| firstRowAsHeader | Určuje, zda má být první řádek v daném listu nebo rozsahu považován za řádek záhlaví s názvy sloupců.<br>Povolené hodnoty jsou **true** a **false** (výchozí). | No       |
| nullValue        | Určuje řetězcovou reprezentaci hodnoty null. <br>Výchozí hodnota je **prázdný řetězec** . | No       |
| komprese | Skupina vlastností pro konfiguraci komprese souborů. Tuto část nakonfigurujte, pokud chcete během provádění aktivit provést kompresi nebo dekompresi. | No |
| typ<br/>( *pod `compression`* ) | Kompresní kodek používaný pro čtení a zápis souborů JSON. <br>Povolené hodnoty jsou **bzip2** , **gzip** , **Deflate** , **ZipDeflate** , **TarGzip** , **tar** , **přichycení** a **LZ4** . Výchozí hodnota není komprimovaná.<br>**Poznámka:** aktivita kopírování nepodporuje "přichycení" & "LZ4" a tok dat mapování nepodporuje "ZipDeflate", "TarGzip" a "tar".<br>**Poznámka** : při použití aktivity kopírování k dekompresi souborů **ZipDeflate** a zápisu do úložiště dat jímky založeného na souborech se soubory extrahují do složky: `<path specified in dataset>/<folder named as source zip file>/` . | Ne.  |
| úroveň<br/>( *pod `compression`* ) | Kompresní poměr <br>Povolené hodnoty jsou **optimální** nebo **nejrychlejší** .<br>- **Nejrychlejší:** Kompresní operace by se měla dokončit co nejrychleji, a to i v případě, že výsledný soubor není optimálně komprimován.<br>- **Optimální** : komprese by měla být optimálně komprimována i v případě, že dokončení operace trvá delší dobu. Další informace najdete v tématu [úroveň komprese](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . | No       |

Níže je příklad datové sady Excelu v Azure Blob Storage:

```json
{
    "name": "ExcelDataset",
    "properties": {
        "type": "Excel",
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
            "sheetName": "MyWorksheet",
            "range": "A3:H5",
            "firstRowAsHeader": true
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností, které jsou podporovány zdrojem aplikace Excel.

### <a name="excel-as-source"></a>Jako zdroj Excelu 

V oddílu aktivita kopírování **_ \_ zdroj \*** * jsou podporovány následující vlastnosti.

| Vlastnost      | Popis                                                  | Povinné |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **ExcelSource** . | Yes      |
| storeSettings | Skupina vlastností, jak číst data z úložiště dat. Jednotlivé konektory založené na souborech mají v rámci své vlastní podporované nastavení pro čtení `storeSettings` . | No       |

```json
"activities": [
    {
        "name": "CopyFromExcel",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "ExcelSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            ...
        }
        ...
    }
]
```

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

V části mapování toků dat můžete číst excelový formát v následujících úložištích dat: [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)a [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties). Můžete odkazovat na excelové soubory buď pomocí datové sady Excelu, nebo pomocí [vložené datové sady](data-flow-source.md#inline-datasets).

### <a name="source-properties"></a>Vlastnosti zdroje

V níže uvedené tabulce jsou uvedeny vlastnosti podporované zdrojem aplikace Excel. Tyto vlastnosti můžete upravit na kartě **Možnosti zdrojového kódu** . Při použití vložené datové sady se zobrazí další nastavení souborů, která jsou stejná jako vlastnosti popsané v části [Vlastnosti datové sady](#dataset-properties) .

| Název                      | Popis                                                  | Povinné | Povolené hodnoty                                            | Vlastnost skriptu toku dat         |
| ------------------------- | ------------------------------------------------------------ | -------- | --------------------------------------------------------- | --------------------------------- |
| Cesty k zástupným kartám           | Budou zpracovány všechny soubory, které odpovídají zástupné cestě. Přepíše složku a cestu k souboru nastavenou v datové sadě. | ne       | Řetězec []                                                  | wildcardPaths                     |
| Kořenová cesta oddílu       | Pro souborová data, která jsou rozdělená na oddíly, můžete zadat kořenovou cestu oddílu, aby bylo možné číst rozdělené složky jako sloupce. | ne       | Řetězec                                                    | partitionRootPath                 |
| Seznam souborů             | Určuje, zda váš zdroj odkazuje na textový soubor se seznamem souborů, které se mají zpracovat. | ne       | `true` nebo `false`                                         | fileList                          |
| Sloupec, ve kterém se má uložit název souboru | Vytvoří nový sloupec s názvem a cestou ke zdrojovému souboru.       | ne       | Řetězec                                                    | rowUrlColumn                      |
| Po dokončení          | Odstraní nebo přesune soubory po zpracování. Cesta k souboru začíná z kořene kontejneru | ne       | Odstranit: `true` nebo `false` <br> Pøesunout `['<from>', '<to>']` | purgeFiles <br> moveFiles         |
| Filtrovat podle poslední změny   | Zvolit filtrování souborů podle toho, kdy se naposledy změnily | ne       | Timestamp                                                 | modifiedAfter <br> modifiedBefore |
| Nenalezeny žádné soubory | Pokud je nastaveno na true, chyba není vyvolána, pokud nebyly nalezeny žádné soubory. | ne | `true` nebo `false` | ignoreNoFilesFound |

### <a name="source-example"></a>Zdrojový příklad

Níže uvedený obrázek je příkladem zdrojové konfigurace aplikace Excel v části mapování toků dat pomocí režimu DataSet.

![Zdroj Excelu](media/data-flow/excel-source.png)

Přidružený skript toku dat je:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    wildcardPaths:['*.xls']) ~> ExcelSource
```

Použijete-li vloženou datovou sadu, v části mapování toku dat se zobrazí následující možnosti zdroje.

![Vložená datová sada zdroje Excelu](media/data-flow/excel-source-inline-dataset.png)

Přidružený skript toku dat je:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'excel',
    fileSystem: 'container',
    folderPath: 'path',
    fileName: 'sample.xls',
    sheetName: 'worksheet',
    firstRowAsHeader: true) ~> ExcelSourceInlineDataset
```

## <a name="next-steps"></a>Další kroky

- [Přehled aktivit kopírování](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)