---
title: Formát Avro v Azure Data Factory
description: Toto téma popisuje, jak v Azure Data Factory pracovat s formátem Avro.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 9e962a0e76cdc0d51a87df3c33927c34db991fc7
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927414"
---
# <a name="avro-format-in-azure-data-factory"></a>Formát Avro v Azure Data Factory

Pokud chcete **analyzovat soubory Avro nebo zapsat data do formátu Avro**, postupujte podle tohoto článku. 

Formát Avro se podporuje pro následující konektory: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [systém souborů](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)a [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. V této části najdete seznam vlastností podporovaných datovou sadou Avro.

| Vlastnost         | Popis                                                  | Požaduje se |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Vlastnost Type datové sady musí být nastavená na **Avro**. | Ano      |
| location         | Nastavení umístění souborů. Každý konektor založený na souborech má svůj vlastní typ umístění a podporované vlastnosti v rámci `location`. **Podrobnosti najdete v článku o konektoru – > Vlastnosti datové sady**. | Ano      |
| avroCompressionCodec | Kompresní kodek, který se má použít při zápisu do souborů Avro Při čtení ze souborů Avro Data Factory automaticky určit Kompresní kodek na základě metadat souboru.<br>Podporované typy jsou**none**(výchozí), "**uprostřed**", "**přichycení**". | Ne       |

> [!NOTE]
> Prázdné znaky v názvu sloupce nejsou pro soubory Avro podporovány.

Níže je příklad datové sady Avro v Azure Blob Storage:

```json
{
    "name": "AvroDataset",
    "properties": {
        "type": "Avro",
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
            "avroCompressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. V této části najdete seznam vlastností, které Avro zdroj a jímka podporuje.

### <a name="avro-as-source"></a>Avro as source

V části ***\*zdrojového\**** aktivity kopírování jsou podporovány následující vlastnosti.

| Vlastnost      | Popis                                                  | Požaduje se |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **AvroSource**. | Ano      |
| storeSettings | Skupina vlastností, jak číst data z úložiště dat. Jednotlivé konektory založené na souborech mají ve `storeSettings`své vlastní podporované nastavení pro čtení. **Podrobnosti najdete v článku informace o konektoru – > část kopírování vlastností aktivity**. | Ne       |

### <a name="avro-as-sink"></a>Avro jako jímka

V části\*aktivita kopírování ***\*jímka*** jsou podporovány následující vlastnosti.

| Vlastnost      | Popis                                                  | Požaduje se |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **AvroSink**. | Ano      |
| storeSettings | Skupina vlastností, jak zapisovat data do úložiště dat. Jednotlivé konektory založené na souborech mají vlastní podporované nastavení zápisu v rámci `storeSettings`. **Podrobnosti najdete v článku informace o konektoru – > část kopírování vlastností aktivity**. | Ne       |

## <a name="data-type-support"></a>Podpora datových typů

Avro [komplexní datové typy](https://avro.apache.org/docs/current/spec.html#schema_complex) se nepodporují (záznamy, výčty, pole, mapy, sjednocení a pevné).

## <a name="next-steps"></a>Další kroky

- [Přehled aktivit kopírování](copy-activity-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
