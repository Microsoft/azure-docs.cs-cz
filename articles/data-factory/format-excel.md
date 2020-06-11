---
title: Excelový formát v Azure Data Factory
description: Toto téma popisuje, jak pracovat s excelovým formátem v Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/10/2020
ms.author: jingwang
ms.openlocfilehash: 8b4876377501209e19ac496d605d228208d2323d
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/10/2020
ms.locfileid: "84670919"
---
# <a name="excel-format-in-azure-data-factory"></a>Excelový formát v Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Chcete-li **analyzovat soubory aplikace Excel**, postupujte podle tohoto článku. Azure Data Factory podporuje obě ". xls" i ". xlsx".

Formát aplikace Excel je podporován pro následující konektory [: Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)a [SFTP](connector-sftp.md). Podporuje se jako zdroj, ale ne jímka.

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných datovou sadou aplikace Excel.

| Vlastnost         | Popis                                                  | Vyžadováno |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Vlastnost Type datové sady musí být nastavená na **Excel**.   | Yes      |
| location         | Nastavení umístění souborů. Každý konektor založený na souborech má svůj vlastní typ umístění a podporované vlastnosti v rámci `location` . | Yes      |
| sheetName        | Název sešitu aplikace Excel, ve kterém se mají číst data                       | Yes      |
| range            | Rozsah buněk v daném listu pro vyhledání selektivních dat, například `A3:H5` (tabulka z a3 do H5), `A3` (tabulka začínající z buňky A3) ( `A3:A3` jedna buňka). Pokud není zadaný, ADF načte z celého listu jako tabulku. | No       |
| firstRowAsHeader | Určuje, zda má být první řádek v daném listu nebo rozsahu považován za řádek záhlaví s názvy sloupců.<br>Povolené hodnoty jsou **true** a **false** (výchozí). | No       |
| nullValue        | Určuje řetězcovou reprezentaci hodnoty null. <br>Výchozí hodnota je **prázdný řetězec**. | No       |
| compressionCodec | Kompresní kodek používaný ke čtení excelových souborů. <br>Povolené hodnoty jsou **bzip2**, **gzip**, **Deflate**, **ZipDeflate**, **přichycení**nebo **LZ4**. Výchozí hodnota není komprimovaná. <br>**Poznámka:** aktivita kopírování nepodporuje "přichycení" & "LZ4" a tok dat mapování nepodporuje "ZipDeflate". <br>**Poznámka** : při použití aktivity kopírování k dekompresi souborů **ZipDeflate** a zápisu do úložiště dat jímky založeného na souborech se soubory extrahují do složky: `<path specified in dataset>/<folder named as source zip file>/` . | No       |
| compressionLevel | Kompresní poměr <br>Povolené hodnoty jsou **optimální** nebo **nejrychlejší**.<br>- **Nejrychlejší:** Kompresní operace by se měla dokončit co nejrychleji, a to i v případě, že výsledný soubor není optimálně komprimován.<br>- **Optimální**: komprese by měla být optimálně komprimována i v případě, že dokončení operace trvá delší dobu. Další informace najdete v tématu [úroveň komprese](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . | No       |

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

V části *** \* zdroj \* *** aktivity kopírování jsou podporovány následující vlastnosti.

| Vlastnost      | Popis                                                  | Vyžadováno |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **ExcelSource**. | Yes      |
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

## <a name="next-steps"></a>Další kroky

- [Přehled aktivit kopírování](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
