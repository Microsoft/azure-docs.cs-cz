---
title: Formát ORC v Azure Data Factory
description: Toto téma popisuje, jak v Azure Data Factory pracovat s formátem ORC.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: jingwang
ms.openlocfilehash: f71b739242cf4f6a3549927a2a7e61400b2f987e
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2020
ms.locfileid: "90061066"
---
# <a name="orc-format-in-azure-data-factory"></a>Formát ORC v Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Pokud chcete **analyzovat soubory ORC nebo zapsat data do formátu ORC**, postupujte podle tohoto článku. 

Formát ORC se podporuje pro následující konektory: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [systém souborů](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)a [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných datovou sadou ORC.

| Vlastnost         | Popis                                                  | Povinné |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Vlastnost Type datové sady musí být nastavená na **ORC**. | Ano      |
| location         | Nastavení umístění souborů. Každý konektor založený na souborech má svůj vlastní typ umístění a podporované vlastnosti v rámci `location` . **Podrobnosti najdete v článku o konektoru – > vlastnosti datové sady**. | Ano      |

Níže je příklad datové sady ORC v Azure Blob Storage:

```json
{
    "name": "ORCDataset",
    "properties": {
        "type": "Orc",
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
            }
        }
    }
}
```

Je třeba počítat s následujícím:

* Komplexní datové typy se nepodporují (struktura, mapování, seznam, SJEDNOCENí).
* Prázdné znaky v názvu sloupce nejsou podporovány.
* Soubory ORC mají tři [možnosti komprese](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NONE, ZLIB a SNAPPY. Data Factory podporuje čtení dat ze souborů ORC v libovolném z těchto komprimovaných formátů. K načtení dat využívá kompresní kodek v metadatech. Při zápisu do souboru ORC ale Data Factory využívá možnost ZLIB, která je pro formát ORC výchozí. V současnosti toto chování nejde potlačit.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností, které ORC zdroj a jímka podporuje.

### <a name="orc-as-source"></a>ORC as source

V části *** \* zdroj \* *** aktivity kopírování jsou podporovány následující vlastnosti.

| Vlastnost      | Popis                                                  | Povinné |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **OrcSource**. | Ano      |
| storeSettings | Skupina vlastností, jak číst data z úložiště dat. Jednotlivé konektory založené na souborech mají v rámci své vlastní podporované nastavení pro čtení `storeSettings` . **Podrobnosti najdete v článku informace o konektoru – > část kopírování vlastností aktivity**. | Ne       |

### <a name="orc-as-sink"></a>ORC jako jímka

V části *** \* jímka \* *** aktivity kopírování jsou podporovány následující vlastnosti.

| Vlastnost      | Popis                                                  | Povinné |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **OrcSink**. | Ano      |
| formatSettings | Skupina vlastností V tabulce **nastavení zápisu ORC** najdete níže. |    Ne      |
| storeSettings | Skupina vlastností, jak zapisovat data do úložiště dat. Každý konektor založený na souborech má vlastní podporované nastavení zápisu v rámci `storeSettings` . **Podrobnosti najdete v článku informace o konektoru – > část kopírování vlastností aktivity**. | Ne       |

Podporovaná **nastavení zápisu ORC** v rámci `formatSettings` :

| Vlastnost      | Popis                                                  | Povinné                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| typ          | Typ formatSettings musí být nastaven na hodnotu **OrcWriteSettings**. | Ano                                                   |
| maxRowsPerFile | Při zápisu dat do složky můžete zvolit zápis do více souborů a zadat maximální počet řádků na soubor.  | Ne |
| fileNamePrefix | Při zápisu dat do více souborů zadejte předponu názvu souboru. Výsledkem je tento vzor: `<fileNamePrefix>_00000.<fileExtension>` . Pokud tento parametr nezadáte, automaticky se vygeneruje Předpona názvu souboru. Tato vlastnost se nevztahuje na to, že zdroj je úložiště založené na souborech nebo v [úložišti dat s povolenými možnostmi pro oddíly](copy-activity-performance-features.md).  | Ne |

## <a name="using-self-hosted-integration-runtime"></a>Použití Integration Runtime pro místní hostování

> [!IMPORTANT]
> Pro kopii, která je oprávněná v místním prostředí Integration Runtime například mezi místními a cloudovým úložištěm dat, pokud soubory ORC nekopírujete **tak, jak jsou**, je třeba na počítač IR nainstalovat **64-bit JRE 8 (Java Runtime Environment) nebo OpenJDK** and **Microsoft Visual C++ 2010 Redistributable Package** . Další podrobnosti najdete v následujícím odstavci.

Pro kopírování běžící v prostředí IR v místním prostředí s ORC serializací/deserializace vyhledá ADF modul runtime Java tím, že se nejprve zkontroluje registr *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* pro JRE, pokud se nenajde, druhá kontroluje proměnnou systému *`JAVA_HOME`* pro OpenJDK.

- **Použití JRE**: 64-bit IR vyžaduje 64-bit JRE. Můžete ho najít [tady](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Použití OpenJDK**: podporuje se od verze IR 3,13. Zabalit jvm.dll se všemi ostatními požadovanými sestaveními OpenJDK do místně hostovaného počítače IR a nastavit proměnnou prostředí systému JAVA_HOME odpovídajícím způsobem.
- **Instalace balíčku Visual C++ 2010 Redistributable**: Visual C++ 2010 Distribuovatelný balíček není nainstalovaný s instalacemi v místním prostředí IR. Můžete ho najít [tady](https://www.microsoft.com/download/details.aspx?id=14632).

> [!TIP]
> Pokud kopírujete data do nebo z formátu ORC pomocí Integration Runtime v místním prostředí a omylem zaznamenáte chybu při vyvolání Java, zpráva: **Java. lang. OutOfMemoryError: prostor haldy Java**", můžete přidat proměnnou prostředí `_JAVA_OPTIONS` v počítači, který je hostitelem prostředí IR v místním prostředí, a upravit tak minimální/maximální velikost haldy pro JVM, abyste mohli takovou kopii provést, a pak znovu spustit kanál.

![Nastavení velikosti haldy JVM v místním prostředí IR](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Příklad: nastavte proměnnou `_JAVA_OPTIONS` s hodnotou `-Xms256m -Xmx16g` . Příznak `Xms` Určuje počáteční fond přidělení paměti pro prostředí Java Virtual Machine (JVM), zatímco `Xmx` Určuje maximální fond přidělení paměti. To znamená, že JVM bude spuštěn s `Xms` velikostí paměti a bude moci využít maximální `Xmx` množství paměti. Ve výchozím nastavení ADF používá minimální 64 MB a maximální 1G.

## <a name="next-steps"></a>Další kroky

- [Přehled aktivit kopírování](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
