---
title: Formát Avro ve službě Azure Data Factory
description: Toto téma popisuje, jak zajít s formátem Avro v Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: jingwang
ms.openlocfilehash: 1717969aeb24a153f986c70ef60db1aac5c840fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78267791"
---
# <a name="avro-format-in-azure-data-factory"></a>Formát Avro ve službě Azure Data Factory

Postupujte podle tohoto článku, pokud chcete **analyzovat soubory Avro nebo zapsat data do formátu Avro**. 

Formát Avro je podporovaný pro následující konektory: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), File [System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)a [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [Datové sady.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných datovou sadou Avro.

| Vlastnost         | Popis                                                  | Požaduje se |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Vlastnost type datové sady musí být nastavena na **Avro**. | Ano      |
| location         | Nastavení umístění souborů. Každý konektor založený na souboru má svůj `location`vlastní typ umístění a podporované vlastnosti v části . **Viz podrobnosti v článku konektoru -> část vlastností datové sady**. | Ano      |
| avroCompressionCodec | Kompresní kodek, který se má použít při zápisu do souborů Avro. Při čtení ze souborů Avro Data Factory automaticky určí kompresní kodek na základě metadat souboru.<br>Podporované typy jsou "**none**" (výchozí), "**deflate**", "**snappy**". Poznámka: Aktuálně kopírovat aktivity nepodporuje Snappy při čtení a zápisu avro soubory. | Ne       |

> [!NOTE]
> Prázdné místo v názvu sloupce není pro soubory Avro podporováno.

Níže je uveden příklad datové sady Avro ve službě Azure Blob Storage:

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem a jímkou Avro.

### <a name="avro-as-source"></a>Avro jako zdroj

Následující vlastnosti jsou podporovány v části *** \*zdroje aktivity\* *** kopírování.

| Vlastnost      | Popis                                                  | Požaduje se |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Vlastnost type zdroje aktivity kopírování musí být nastavena na **AvroSource**. | Ano      |
| storeSettings | Skupina vlastností o čtení dat z úložiště dat. Každý konektor založený na souborech má `storeSettings`vlastní podporovaná nastavení čtení v části . **Viz podrobnosti v článku konektoru -> Kopírovat vlastnosti aktivity .** | Ne       |

### <a name="avro-as-sink"></a>Avro jako umyvadlo

Následující vlastnosti jsou podporovány v části *** \*jímky\* *** aktivity kopírování.

| Vlastnost      | Popis                                                  | Požaduje se |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Vlastnost type zdroje aktivity kopírování musí být nastavena na **AvroSink**. | Ano      |
| storeSettings | Skupina vlastností o tom, jak zapisovat data do úložiště dat. Každý konektor založený na souborech má `storeSettings`vlastní podporovaná nastavení zápisu v části . **Viz podrobnosti v článku konektoru -> Kopírovat vlastnosti aktivity .** | Ne       |

## <a name="data-type-support"></a>Podpora datového typu

### <a name="copy-activity"></a>Aktivita kopírování
[Avro komplexní datové typy](https://avro.apache.org/docs/current/spec.html#schema_complex) nejsou podporovány (záznamy, výčty, pole, mapy, sjednocení a pevné) v aktivitě kopírování.

### <a name="data-flows"></a>Toky dat
Při práci se soubory Avro v tocích dat můžete číst a zapisovat složité datové typy, ale nejprve vymažte fyzické schéma z datové sady. V datových tocích můžete nastavit logickou projekci a odvodit sloupce, které jsou složitými strukturami, a pak tato pole automaticky mapovat na soubor Avro.

## <a name="next-steps"></a>Další kroky

- [Kopírovat přehled aktivit](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)
- [Aktivita getMetadata](control-flow-get-metadata-activity.md)
