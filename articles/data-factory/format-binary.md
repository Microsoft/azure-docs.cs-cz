---
title: Binární formát v Azure Data Factory
description: Toto téma popisuje, jak v Azure Data Factory pracovat s binárním formátem.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: jingwang
ms.openlocfilehash: c7fd5cb3c6c8a991a8b5ef9b6460e9dce35dd873
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/02/2020
ms.locfileid: "84298580"
---
# <a name="binary-format-in-azure-data-factory"></a>Binární formát v Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Pro následující konektory se podporuje binární formát: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [systém souborů](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)a [SFTP](connector-sftp.md).

Můžete použít binární datovou sadu v aktivitě [kopírování](copy-activity-overview.md), [aktivitě GetMetadata](control-flow-get-metadata-activity.md)nebo [aktivity odstranění](delete-activity.md). Když použijete binární datovou sadu, ADF neanalyzuje obsah souboru, ale zpracuje ho tak, jak je. 

>[!NOTE]
>Při použití binární datové sady v aktivitě kopírování můžete kopírovat pouze z binární datové sady do binární sady dat.

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných binární datovou sadou.

| Vlastnost         | Popis                                                  | Vyžadováno |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Vlastnost Type datové sady musí být nastavená na **Binary**. | Ano      |
| location         | Nastavení umístění souborů. Každý konektor založený na souborech má svůj vlastní typ umístění a podporované vlastnosti v rámci `location` . **Podrobnosti najdete v článku o konektoru – > vlastnosti datové sady**. | Ano      |
| komprese | Skupina vlastností pro konfiguraci komprese souborů. Tuto část nakonfigurujte, pokud chcete během provádění aktivit provést kompresi nebo dekompresi. | No |
| typ | Kompresní kodek používaný pro čtení a zápis binárních souborů. <br>Povolené hodnoty jsou **bzip2**, **gzip**, **Deflate**, **ZipDeflate**. pro použití při ukládání souboru.<br>**Poznámka:** při použití aktivity kopírování k dekompresi souborů **ZipDeflate** a zápisu do úložiště dat jímky založeného na souborech se ve výchozím nastavení extrahují soubory do složky: `<path specified in dataset>/<folder named as source zip file>/` pomocí `preserveZipFileNameAsFolder` [zdroje aktivity kopírování](#binary-as-source) můžete určit, jestli se má zachovat název souboru ZIP jako struktura složek.| No       |
| úroveň | Kompresní poměr Použijte, pokud se datová sada používá v jímky aktivity kopírování.<br>Povolené hodnoty jsou **optimální** nebo **nejrychlejší**.<br>- **Nejrychlejší:** Kompresní operace by se měla dokončit co nejrychleji, a to i v případě, že výsledný soubor není optimálně komprimován.<br>- **Optimální**: komprese by měla být optimálně komprimována i v případě, že dokončení operace trvá delší dobu. Další informace najdete v tématu [úroveň komprese](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . | No       |

Níže je příklad binární datové sady v Azure Blob Storage:

```json
{
    "name": "BinaryDataset",
    "properties": {
        "type": "Binary",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "ZipDeflate"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných binárním zdrojem a jímkou.

>[!NOTE]
>Při použití binární datové sady v aktivitě kopírování můžete kopírovat pouze z binární datové sady do binární sady dat.

### <a name="binary-as-source"></a>Binární jako zdroj

V části *** \* zdroj \* *** aktivity kopírování jsou podporovány následující vlastnosti.

| Vlastnost      | Popis                                                  | Vyžadováno |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **BinarySource**. | Ano      |
| formatSettings | Skupina vlastností Další informace najdete v tabulce **Nastavení binárního čtení** níže. | No       |
| storeSettings | Skupina vlastností, jak číst data z úložiště dat. Jednotlivé konektory založené na souborech mají v rámci své vlastní podporované nastavení pro čtení `storeSettings` . **Podrobnosti najdete v článku informace o konektoru – > část kopírování vlastností aktivity**. | No       |

Podporovaná **Nastavení binárního čtení** v rámci `formatSettings` :

| Vlastnost      | Popis                                                  | Vyžadováno |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Typ formatSettings musí být nastaven na hodnotu **BinaryReadSettings**. | Ano      |
| compressionProperties | Skupina vlastností, jak dekomprimovat data pro daný Kompresní kodek. | No       |
| preserveZipFileNameAsFolder<br>(*pod `compressionProperties` *) | Platí v případě, že je vstupní datová sada nakonfigurovaná s **ZipDeflate** kompresí. Určuje, jestli se má při kopírování zachovat název zdrojového souboru ZIP jako struktura složek. Pokud je nastavená hodnota true (výchozí), Data Factory zapisuje soubory unzip do `<path specified in dataset>/<folder named as source zip file>/` ; při nastavení na hodnotu false Data Factory zapisuje soubory unzip přímo do `<path specified in dataset>` .  | No |

```json
"activities": [
    {
        "name": "CopyFromBinary",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                },
                "formatSettings": {
                    "type": "BinaryReadSettings",
                    "compressionProperties": {
                        "type": "ZipDeflateReadSettings",
                        "preserveZipFileNameAsFolder": false
                    }
                }
            },
            ...
        }
        ...
    }
]
```

### <a name="binary-as-sink"></a>Binární jako jímka

V části *** \* jímka \* *** aktivity kopírování jsou podporovány následující vlastnosti.

| Vlastnost      | Popis                                                  | Vyžadováno |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **BinarySink**. | Ano      |
| storeSettings | Skupina vlastností, jak zapisovat data do úložiště dat. Každý konektor založený na souborech má vlastní podporované nastavení zápisu v rámci `storeSettings` . **Podrobnosti najdete v článku informace o konektoru – > část kopírování vlastností aktivity**. | No       |

## <a name="next-steps"></a>Další kroky

- [Přehled aktivit kopírování](copy-activity-overview.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
- [Odstranit aktivitu](delete-activity.md)
