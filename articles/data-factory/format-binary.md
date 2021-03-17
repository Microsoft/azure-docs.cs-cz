---
title: Binární formát v Azure Data Factory
description: Toto téma popisuje, jak v Azure Data Factory pracovat s binárním formátem.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: jingwang
ms.openlocfilehash: cc5b54e99584b74b287fa66deba1694419b46b16
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393679"
---
# <a name="binary-format-in-azure-data-factory"></a>Binární formát v Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Pro následující konektory se podporuje binární formát: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [systém souborů](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)a [SFTP](connector-sftp.md).

Můžete použít binární datovou sadu v aktivitě [kopírování](copy-activity-overview.md), [aktivitě GetMetadata](control-flow-get-metadata-activity.md)nebo [aktivity odstranění](delete-activity.md). Když použijete binární datovou sadu, ADF neanalyzuje obsah souboru, ale zpracuje ho tak, jak je. 

>[!NOTE]
>Při použití binární datové sady v aktivitě kopírování můžete kopírovat pouze z binární datové sady do binární sady dat.

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných binární datovou sadou.

| Vlastnost         | Popis                                                  | Povinné |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Vlastnost Type datové sady musí být nastavená na **Binary**. | Yes      |
| location         | Nastavení umístění souborů. Každý konektor založený na souborech má svůj vlastní typ umístění a podporované vlastnosti v rámci `location` . **Podrobnosti najdete v článku o konektoru – > vlastnosti datové sady**. | Yes      |
| komprese | Skupina vlastností pro konfiguraci komprese souborů. Tuto část nakonfigurujte, pokud chcete během provádění aktivit provést kompresi nebo dekompresi. | No |
| typ | Kompresní kodek používaný pro čtení a zápis binárních souborů. <br>Povolené hodnoty jsou **bzip2**, **gzip**, **Deflate**, **ZipDeflate**, **tar** nebo **TarGzip**. <br>**Poznámka:** při použití aktivity kopírování k dekompresi souborů **ZipDeflate** / **TarGzip** /  a zápisu do úložiště dat jímky založeného na souborech se ve výchozím nastavení extrahují soubory do složky: `<path specified in dataset>/<folder named as source compressed file>/` pomocí `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` [zdroje aktivity kopírování](#binary-as-source) můžete určit, jestli se má název komprimovaných souborů zachovat jako struktura složek.| No       |
| úroveň | Kompresní poměr Použijte, pokud se datová sada používá v jímky aktivity kopírování.<br>Povolené hodnoty jsou **optimální** nebo **nejrychlejší**.<br>- **Nejrychlejší:** Kompresní operace by se měla dokončit co nejrychleji, a to i v případě, že výsledný soubor není optimálně komprimován.<br>- **Optimální**: komprese by měla být optimálně komprimována i v případě, že dokončení operace trvá delší dobu. Další informace najdete v tématu [úroveň komprese](/dotnet/api/system.io.compression.compressionlevel) . | No       |

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

V části ***\* zdroj \**** aktivity kopírování jsou podporovány následující vlastnosti.

| Vlastnost      | Popis                                                  | Povinné |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **BinarySource**. | Yes      |
| formatSettings | Skupina vlastností Další informace najdete v tabulce **Nastavení binárního čtení** níže. | No       |
| storeSettings | Skupina vlastností, jak číst data z úložiště dat. Jednotlivé konektory založené na souborech mají v rámci své vlastní podporované nastavení pro čtení `storeSettings` . **Podrobnosti najdete v článku informace o konektoru – > část kopírování vlastností aktivity**. | No       |

Podporovaná **Nastavení binárního čtení** v rámci `formatSettings` :

| Vlastnost      | Popis                                                  | Povinné |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Typ formatSettings musí být nastaven na hodnotu **BinaryReadSettings**. | Yes      |
| compressionProperties | Skupina vlastností, jak dekomprimovat data pro daný Kompresní kodek. | No       |
| preserveZipFileNameAsFolder<br>(*v části `compressionProperties` -> `type` jako `ZipDeflateReadSettings`*) | Platí v případě, že je vstupní datová sada nakonfigurovaná s **ZipDeflate** kompresí. Určuje, jestli se má při kopírování zachovat název zdrojového souboru ZIP jako struktura složek.<br>-Když je nastavená **hodnota true (výchozí)**, Data Factory zapisuje soubory unzip do `<path specified in dataset>/<folder named as source zip file>/` .<br>– Pokud je nastavené na **false**, Data Factory zapisuje soubory unzip přímo do `<path specified in dataset>` . Ujistěte se, že nemáte duplicitní názvy souborů v různých zdrojových souborech ZIP, abyste se vyhnuli neočekávanému chování.  | No |
| preserveCompressionFileNameAsFolder<br>(*v části `compressionProperties` -> `type` jako `TarGZipReadSettings` nebo `TarReadSettings`*) | Použije se, když je pro vstupní datovou sadu nakonfigurovaná komprese **TarGzip** / **tar** . Určuje, zda se má při kopírování zachovat zdrojový komprimovaný název souboru jako struktura složky.<br>-Když je nastavená **hodnota true (výchozí)**, Data Factory zapisuje dekomprimované soubory do `<path specified in dataset>/<folder named as source compressed file>/` . <br>– Pokud je nastavené na **false**, Data Factory zapisuje dekomprimované soubory přímo do `<path specified in dataset>` . Ujistěte se, že nemáte duplicitní názvy souborů v různých zdrojových souborech, abyste se vyhnuli neočekávanému chování. | No |

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
                    "recursive": true,
                    "deleteFilesAfterCompletion": true
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

V části ***\* jímka \**** aktivity kopírování jsou podporovány následující vlastnosti.

| Vlastnost      | Popis                                                  | Povinné |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **BinarySink**. | Yes      |
| storeSettings | Skupina vlastností, jak zapisovat data do úložiště dat. Každý konektor založený na souborech má vlastní podporované nastavení zápisu v rámci `storeSettings` . **Podrobnosti najdete v článku informace o konektoru – > část kopírování vlastností aktivity**. | No       |

## <a name="next-steps"></a>Další kroky

- [Přehled aktivit kopírování](copy-activity-overview.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
- [Odstranit aktivitu](delete-activity.md)