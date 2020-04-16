---
title: Binární formát ve službě Azure Data Factory
description: Toto téma popisuje, jak zajít s binárníformát v Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: jingwang
ms.openlocfilehash: 4560560b3677030a66e277e96eb552d39f5c82c1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416324"
---
# <a name="binary-format-in-azure-data-factory"></a>Binární formát ve službě Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Binární formát je podporován pro následující konektory: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), File [System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)a [SFTP](connector-sftp.md).

Binární datovou sadu můžete použít v [aktivitě Kopírování](copy-activity-overview.md), [Aktivitě GetMetadata](control-flow-get-metadata-activity.md)nebo [Odstranit](delete-activity.md). Při použití binární datové sady ADF neanalyzuje obsah souboru, ale zachází s ním jako-je. 

>[!NOTE]
>Při použití binární datové sady v aktivitě kopírování můžete kopírovat pouze z binární datové sady do binární datové sady.

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [Datové sady.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných binární datovou sadou.

| Vlastnost         | Popis                                                  | Požaduje se |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Vlastnost type datové sady musí být nastavena na **binární**. | Ano      |
| location         | Nastavení umístění souborů. Každý konektor založený na souboru má svůj `location`vlastní typ umístění a podporované vlastnosti v části . **Viz podrobnosti v článku konektoru -> část vlastností datové sady**. | Ano      |
| komprese | Skupina vlastností pro konfiguraci komprese souborů. Tuto část nakonfigurujte, pokud chcete provést kompresi nebo dekompresi během provádění aktivity. | Ne |
| type | Kompresní kodek používaný ke čtení/zápisu binárních souborů. <br>Povolené hodnoty jsou **bzip2**, **gzip**, **deflate**, **ZipDeflate**. použijete při ukládání souboru.<br>Poznámka: Při použití kopírování aktivity dekompresi ZipDeflate soubor (y) a zapisovat `<path specified in dataset>/<folder named as source zip file>/`do úložiště dat jímky založené na souboru, soubory budou extrahovány do složky: . | Ne       |
| level | Kompresní poměr. Použít při použití datové sady v jímce aktivity kopírovat.<br>Povolené hodnoty jsou **optimální** nebo **nejrychlejší**.<br>- **Nejrychlejší:** Operace komprese by měla být dokončena co nejrychleji, a to i v případě, že výsledný soubor není optimálně komprimován.<br>- **Optimální**: Operace komprese by měla být optimálně komprimována, i když operace trvá delší dobu. Další informace naleznete v tématu [Úroveň komprese.](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) | Ne       |

Níže je uveden příklad binární datové sady v azure blob storage:

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných binární zdroj a jímky.

>[!NOTE]
>Při použití binární datové sady v aktivitě kopírování můžete kopírovat pouze z binární datové sady do binární datové sady.

### <a name="binary-as-source"></a>Binární jako zdroj

Následující vlastnosti jsou podporovány v části *** \*zdroje aktivity\* *** kopírování.

| Vlastnost      | Popis                                                  | Požaduje se |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Vlastnost type zdroje aktivity kopírování musí být nastavena na **BinarySource**. | Ano      |
| storeSettings | Skupina vlastností o čtení dat z úložiště dat. Každý konektor založený na souborech má `storeSettings`vlastní podporovaná nastavení čtení v části . **Viz podrobnosti v článku konektoru -> Kopírovat vlastnosti aktivity .** | Ne       |

### <a name="binary-as-sink"></a>Binární jako umyvadlo

Následující vlastnosti jsou podporovány v části *** \*jímky\* *** aktivity kopírování.

| Vlastnost      | Popis                                                  | Požaduje se |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Vlastnost type zdroje aktivity kopírování musí být nastavena na **BinarySink**. | Ano      |
| storeSettings | Skupina vlastností o tom, jak zapisovat data do úložiště dat. Každý konektor založený na souborech má `storeSettings`vlastní podporovaná nastavení zápisu v části . **Viz podrobnosti v článku konektoru -> Kopírovat vlastnosti aktivity .** | Ne       |

## <a name="next-steps"></a>Další kroky

- [Kopírovat přehled aktivit](copy-activity-overview.md)
- [Aktivita getMetadata](control-flow-get-metadata-activity.md)
- [Odstranit aktivitu](delete-activity.md)
