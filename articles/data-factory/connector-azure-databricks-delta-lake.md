---
title: Kopírování dat do a z Azure Databricks rozdílových Lake
description: Naučte se, jak kopírovat data do a z Azure Databricks rozdílových Lake pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/28/2020
ms.openlocfilehash: 4ff1a793b3e8c4fe642aa304f1aa59bd8edefb8c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91405615"
---
# <a name="copy-data-to-and-from-azure-databricks-delta-lake-by-using-azure-data-factory"></a>Kopírování dat z Azure Databricks rozdílových Lake pomocí Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory kopírovat data z a do Azure Databricks rozdílových Lake. Sestaví se na [aktivitu kopírování v Azure Data Factory](copy-activity-overview.md) článku, která představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento Azure Databricks Delta Lake Connector se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou tabulkou matice zdroje/jímky](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)

Obecně platí, že Azure Data Factory podporuje rozdílové Lake s následujícími funkcemi, aby splňovaly vaše různé potřeby.

- Aktivita kopírování podporuje Azure Databricks rozdílového konektoru Lake ke kopírování dat z libovolného podporovaného zdrojového úložiště dat do Azure Databricks rozdílových Lake Table a z rozdílové tabulky Lake do libovolného podporovaného úložiště dat jímky. Využívá svůj cluster datacihlů k provádění přesunu dat, viz podrobnosti v [části požadavky](#prerequisites).
- [Mapování toku dat](concepts-data-flow-overview.md) podporuje [Formát obecného rozdílu](format-delta.md) v Azure Storage jako zdroj a jímka pro čtení a zápis rozdílových souborů pro ETL bez kódu a spouštění na spravovaných Azure Integration runtime.
- [Aktivity datacihlů](transform-data-databricks-notebook.md) podporují orchestraci prostředí ETL zaměřeného na kód nebo úlohy strojového učení v rámci rozdílových Lake.

## <a name="prerequisites"></a>Požadavky

Pokud chcete použít tento Azure Databricks rozdílového konektoru Lake, musíte nastavit cluster v Azure Databricks.

- Pokud chcete kopírovat data do rozdílových Lake, aktivita kopírování vyvolá Azure Databricks clusteru pro čtení dat z Azure Storage, což je buď původní zdroj nebo pracovní oblast, kde Data Factory nejprve zapisuje zdrojová data prostřednictvím integrované připravené kopie. Další informace najdete v [části Delta Lake jako zdroj](#delta-lake-as-source).
- Podobně platí, že pokud chcete kopírovat data z rozdílového jezera, aktivita kopírování vyvolá Azure Databricks clusteru pro zápis dat do Azure Storage, což je buď původní jímka, nebo pracovní oblast, ze které Data Factory nadále zapisovat data do konečné jímky prostřednictvím integrované připravené kopie. Další informace najdete v [části Delta Lake jako jímky](#delta-lake-as-sink).

Cluster datacihly musí mít přístup ke službě Azure Blob nebo Azure Data Lake Storage Gen2 účtu, a to jak kontejner úložiště, tak systém souborů, který se používá pro zdroj, jímku a přípravu a kontejner nebo systém souborů, kde chcete zapsat rozdílové tabulky Lake.

- Pokud chcete použít **Azure Data Lake Storage Gen2**, můžete nakonfigurovat **instanční objekt** nebo **přístupový klíč účtu úložiště** v clusteru datacihly jako součást konfigurace Apache Spark. Postupujte podle kroků v části [přímý přístup k instančnímu objektu](https://docs.microsoft.com/azure/databricks/data/data-sources/azure/azure-datalake-gen2#--access-directly-with-service-principal-and-oauth-20) nebo [přímý přístup pomocí přístupového klíče účtu úložiště](https://docs.microsoft.com/azure/databricks/data/data-sources/azure/azure-datalake-gen2#--access-directly-using-the-storage-account-access-key).

- Pokud chcete používat **úložiště objektů BLOB v Azure**, můžete v rámci konfigurace Apache Spark nakonfigurovat **přístupový klíč účtu úložiště** nebo **token SAS** v clusteru datacihly. Postupujte podle kroků v části [přístup k úložišti objektů BLOB v Azure pomocí rozhraní RDD API](https://docs.microsoft.com/azure/databricks/data/data-sources/azure/azure-storage#access-azure-blob-storage-using-the-rdd-api).

Pokud byl cluster, který jste nakonfigurovali, ukončen při spuštění aktivity kopírování, Data Factory ho automaticky spustí. Pokud vytváříte kanál pomocí uživatelského rozhraní Data Factory vytváření obsahu, pro operace, jako je data Preview, potřebujete mít živý cluster, Data Factory nebude cluster vaším jménem začínat.

#### <a name="specify-the-cluster-configuration"></a>Zadejte konfiguraci clusteru.

1. V rozevíracím seznamu **režim clusteru** vyberte **standardní**.

2. V rozevíracím seznamu **verze Databricks runtime** vyberte verzi modulu runtime datacihly.

3. Zapnout [Automatické optimalizace](https://docs.microsoft.com/azure/databricks/delta/optimizations/auto-optimize) přidáním následujících vlastností do [Konfigurace Sparku](https://docs.microsoft.com/azure/databricks/clusters/configure#spark-config):

   ```
   spark.databricks.delta.optimizeWrite.enabled true
   spark.databricks.delta.autoCompact.enabled true
   ```

4. Nakonfigurujte cluster v závislosti na potřebách integrace a škálování.

Podrobnosti o konfiguraci clusteru najdete v tématu [konfigurace clusterů](https://docs.microsoft.com/azure/databricks/clusters/configure).

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které definují Data Factory entit specifických pro Azure Databricks rozdílových konektorů Lake.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu Azure Databricks Delta Lake jsou podporovány následující vlastnosti.

| Vlastnost    | Popis                                                  | Povinné |
| :---------- | :----------------------------------------------------------- | :------- |
| typ        | Vlastnost Type musí být nastavená na **AzureDatabricksDeltaLake**. | Yes      |
| doména      | Zadejte adresu URL Azure Databricks pracovního prostoru, třeba `https://adb-xxxxxxxxx.xx.azuredatabricks.net` . |          |
| clusterId   | Zadejte ID clusteru existujícího clusteru. Měl by to být již vytvořený interaktivní cluster. <br>ID clusteru interaktivního clusteru můžete najít v pracovním prostoru datacihly – > clustery – > interaktivní název clusteru – > značky konfigurace >. [Další informace](https://docs.microsoft.com/azure/databricks/clusters/configure#cluster-tags). |          |
| accessToken | Pro Data Factory ověřování pro Azure Databricks je vyžadován přístupový token. Přístupový token se musí vygenerovat z pracovního prostoru datacihly. Podrobnější kroky pro vyhledání přístupového tokenu najdete [tady](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/authentication#generate-token). |          |
| connectVia  | [Prostředí Integration runtime](concepts-integration-runtime.md) , které se používá pro připojení k úložišti dat. Můžete použít prostředí Azure Integration runtime nebo místní prostředí Integration runtime (Pokud se vaše úložiště dat nachází v privátní síti). Pokud není zadaný, použije se výchozí prostředí Azure Integration runtime. | No       |

**Příklad:**

```json
{
    "name": "AzureDatabricksDeltaLakeLinkedService",
    "properties": {
        "type": "AzureDatabricksDeltaLake",
        "typeProperties": {
            "domain": "https://adb-xxxxxxxxx.xx.azuredatabricks.net",
            "clusterId": "<cluster id>",
            "accessToken": {
                "type": "SecureString", 
                "value": "<access token>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . 

Pro Azure Databricks rozdílovou datovou sadu se podporují následující vlastnosti.

| Vlastnost  | Popis                                                  | Povinné                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| typ      | Vlastnost Type datové sady musí být nastavená na **AzureDatabricksDeltaLakeDataset**. | Yes                         |
| database | Název databáze. |Ne pro zdroj, Ano pro jímku  |
| stolu | Název rozdílové tabulky |Ne pro zdroj, Ano pro jímku  |

**Příklad:**

```json
{
    "name": "AzureDatabricksDeltaLakeDataset",
    "properties": {
        "type": "AzureDatabricksDeltaLakeDataset",
        "typeProperties": {
            "database": "<database name>",
            "table": "<delta table name>"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "linkedServiceName": {
            "referenceName": "<name of linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností, které Azure Databricks rozdílový zdroj a jímka Lake Lake podporuje.

### <a name="delta-lake-as-source"></a>Rozdílový Lake jako zdroj

Pokud chcete kopírovat data z Azure Databricks rozdílových Lake, v části **zdroj** aktivity kopírování se podporují následující vlastnosti.

| Vlastnost                     | Popis                                                  | Povinné |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| typ                         | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **AzureDatabricksDeltaLakeSource**. | Yes      |
| query          | Zadejte dotaz SQL pro čtení dat. Pro řízení času služební cesty použijte následující vzor:<br>- `SELECT * FROM events TIMESTAMP AS OF timestamp_expression`<br>- `SELECT * FROM events VERSION AS OF version` | No       |
| exportSettings | Rozšířená nastavení používaná k načtení dat z rozdílové tabulky. | No       |
| ***V části `exportSettings` :*** |  |  |
| typ | Typ příkazu pro export nastavený na **AzureDatabricksDeltaLakeExportCommand**. | Yes |
| Parametr DateFormat | Umožňuje formátovat typ data String pomocí formátu data. Vlastní formáty data se řídí formáty ve [vzoru data a času](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html). Pokud není zadaný, použije se výchozí hodnota `yyyy-MM-dd` . | No |
| timestampFormat | Formátujte typ časového razítka na řetězec ve formátu timestamp. Vlastní formáty data se řídí formáty ve [vzoru data a času](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html). Pokud není zadaný, použije se výchozí hodnota `yyyy-MM-dd'T'HH:mm:ss[.SSS][XXX]` . | No |

#### <a name="direct-copy-from-delta-lake"></a>Přímá kopie ze rozdílových Lake

Pokud vaše úložiště a formát dat jímky splňují kritéria popsaná v této části, můžete aktivitu kopírování použít k přímému kopírování z Azure Databricks rozdílových tabulek do jímky. Data Factory kontroluje nastavení a v případě, že nejsou splněná následující kritéria, se spustí aktivita kopírování:

- **Propojená služba jímky** je [úložiště objektů BLOB v Azure](connector-azure-blob-storage.md) nebo [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md). Přihlašovací údaje účtu by měly být předem nakonfigurované v Azure Databricks konfiguraci clusteru, další informace najdete v části [požadavky](#prerequisites).

- **Formát dat jímky** je **Parquet**, **oddělený text**nebo **Avro** s následujícími konfiguracemi a odkazuje na složku namísto souboru.

    - V případě formátu **Parquet** je Kompresní kodek **žádný**, **přichycený**nebo **gzip**.
    - Textový formát s **oddělovači** :
        - `rowDelimiter` je libovolný jeden znak.
        - `compression` může být **none**, **bzip2**, **gzip**.
        - `encodingName` Kódování UTF-7 není podporováno.
    - V případě formátu **Avro** je Kompresní kodek **žádný**, **zúžený**nebo **přichycený**.

- Ve zdroji aktivity kopírování není `additionalColumns` zadán.
- Pokud kopírujete data na text s oddělovači, musí být v jímky aktivity kopírování `fileExtension` ". csv".
- V mapování aktivity kopírování není povolen převod typu.

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delta lake input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureDatabricksDeltaLakeSource",
                "sqlReaderQuery": "SELECT * FROM events TIMESTAMP AS OF timestamp_expression"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="staged-copy-from-delta-lake"></a>Zpracovaná kopie z rozdílového jezera

Pokud se úložiště nebo formát dat jímky neshodují s kritérii přímé kopie, jak je uvedeno v poslední části, povolte vestavěnou fázi kopírování s použitím dočasné instance služby Azure Storage. Funkce dvoufázové kopírování nabízí také lepší propustnost. Data Factory exportuje data z Azure Databricks rozdílových Lake do přípravného úložiště, pak zkopíruje data do jímky a nakonec vyčistí dočasná data z přípravného úložiště. Podrobnosti o kopírování dat pomocí přípravy najdete v tématu [připravené kopírování](copy-activity-performance-features.md#staged-copy) .

Pokud chcete tuto funkci použít, vytvořte [propojenou službu Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) nebo [Azure Data Lake Storage Gen2 propojenou službu](connector-azure-data-lake-storage.md#linked-service-properties) , která jako dočasné přípravy odkazuje na účet úložiště. Pak zadejte `enableStaging` vlastnosti a `stagingSettings` v aktivitě kopírování.

>[!NOTE]
>Přihlašovací údaje k pracovnímu účtu úložiště by se měly předem nakonfigurovat v Azure Databricks konfiguraci clusteru. Další informace najdete v části [požadavky](#prerequisites).

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delta lake input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureDatabricksDeltaLakeSource",
                "sqlReaderQuery": "SELECT * FROM events TIMESTAMP AS OF timestamp_expression"
            },
            "sink": {
                "type": "<sink type>"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingStorage",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

### <a name="delta-lake-as-sink"></a>Rozdílový Lake jako jímka

Chcete-li kopírovat data do Azure Databricks rozdílových dat, jsou v části **jímka** aktivity kopírování podporovány následující vlastnosti.

| Vlastnost      | Popis                                                  | Povinné |
| :------------ | :----------------------------------------------------------- | :------- |
| typ          | Vlastnost Type jímky aktivity kopírování nastavená na **AzureDatabricksDeltaLakeSink**. | Yes      |
| preCopyScript | Zadejte dotaz SQL pro aktivitu kopírování, která se má spustit před zápisem dat do tabulky Delta datacihly v každém spuštění. Tuto vlastnost můžete použít k vyčištění předem načtených dat nebo k přidání zkrácené tabulky nebo výpisu vaku. | No       |
| importSettings | Rozšířená nastavení použitá pro zápis dat do rozdílové tabulky | No |
| ***V části `importSettings` :*** |                                                              |  |
| typ | Typ příkazu pro import, který je nastavený na **AzureDatabricksDeltaLakeImportCommand**. | Yes |
| Parametr DateFormat | Řetězec formátu pro typ data s formátem data Vlastní formáty data se řídí formáty ve [vzoru data a času](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html). Pokud není zadaný, použije se výchozí hodnota `yyyy-MM-dd` . | No |
| timestampFormat | Řetězec formátu pro typ časového razítka ve formátu časového razítka. Vlastní formáty data se řídí formáty ve [vzoru data a času](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html). Pokud není zadaný, použije se výchozí hodnota `yyyy-MM-dd'T'HH:mm:ss[.SSS][XXX]` . | No |

#### <a name="direct-copy-to-delta-lake"></a>Přímá kopie do rozdílových Lake

Pokud zdrojové úložiště a formát dat splňují kritéria popsaná v této části, můžete aktivitu kopírování použít k přímému kopírování ze zdroje do Azure Databricks rozdílového Lake. Azure Data Factory kontroluje nastavení a v případě, že nejsou splněná následující kritéria, se spustí aktivita kopírování:

- **Zdrojová propojená služba** je [úložiště objektů BLOB v Azure](connector-azure-blob-storage.md) nebo [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md). Přihlašovací údaje účtu by měly být předem nakonfigurované v Azure Databricks konfiguraci clusteru, další informace najdete v části [požadavky](#prerequisites).

- **Zdrojový formát dat** je **Parquet**, **oddělený text**nebo **Avro** s následujícími konfiguracemi a odkazuje na složku namísto souboru.

    - V případě formátu **Parquet** je Kompresní kodek **žádný**, **přichycený**nebo **gzip**.
    - Textový formát s **oddělovači** :
        - `rowDelimiter` je výchozí nebo jakýkoli jeden znak.
        - `compression` může být **none**, **bzip2**, **gzip**.
        - `encodingName` Kódování UTF-7 není podporováno.
    - V případě formátu **Avro** je Kompresní kodek **žádný**, **zúžený**nebo **přichycený**.

- Ve zdroji aktivity kopírování: 

    - `wildcardFileName` obsahuje pouze zástupný znak `*` `?` , nikoli a není `wildcardFolderName` zadán.
    - `prefix`nejsou `modifiedDateTimeStart` `modifiedDateTimeEnd` zadány,, a `enablePartitionDiscovery` .
    - `additionalColumns` není zadáno.

- V mapování aktivity kopírování není povolen převod typu.

**Příklad:**

```json
"activities":[
    {
        "name": "CopyToDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Delta lake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDatabricksDeltaLakeSink"
            }
        }
    }
]
```

#### <a name="staged-copy-to-delta-lake"></a>Připravené kopírování do rozdílových Lake

Pokud zdrojové úložiště dat nebo formát neodpovídá kritériím pro přímý kopírování, jak je uvedeno v poslední části, povolte vestavěnou fázi kopírování s použitím dočasné instance služby Azure Storage. Funkce dvoufázové kopírování nabízí také lepší propustnost. Data Factory automaticky převede data tak, aby splňovala požadavky na formát dat do pracovního úložiště, a pak z nich načtěte data do rozdílových dat. Nakonec vymaže vaše dočasná data z úložiště. Podrobnosti o kopírování dat pomocí přípravy najdete v tématu [připravené kopírování](copy-activity-performance-features.md#staged-copy) .

Pokud chcete tuto funkci použít, vytvořte [propojenou službu Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) nebo [Azure Data Lake Storage Gen2 propojenou službu](connector-azure-data-lake-storage.md#linked-service-properties) , která jako dočasné přípravy odkazuje na účet úložiště. Pak zadejte `enableStaging` vlastnosti a `stagingSettings` v aktivitě kopírování.

>[!NOTE]
>Přihlašovací údaje k pracovnímu účtu úložiště by se měly předem nakonfigurovat v Azure Databricks konfiguraci clusteru. Další informace najdete v části [požadavky](#prerequisites).

**Příklad:**

```json
"activities":[
    {
        "name": "CopyToDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Delta lake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDatabricksDeltaLakeSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

## <a name="monitoring"></a>Monitorování

Azure Data Factory poskytuje stejné [možnosti monitorování aktivity kopírování](copy-activity-monitoring.md) jako jiné konektory. Vzhledem k tomu, že je v clusteru Azure Databricks spuštěno načítání dat z/do rozdílového Lake, můžete dále [Zobrazit podrobné protokoly clusteru](https://docs.microsoft.com/azure/databricks/clusters/clusters-manage#--view-cluster-logs) a [monitorovat výkon](https://docs.microsoft.com/azure/databricks/clusters/clusters-manage#--monitor-performance).

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Další informace o vlastnostech najdete v tématu [aktivita vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky

Seznam úložišť dat podporovaných jako zdroje a jímky pomocí aktivity kopírování v Data Factory najdete v tématu [podporovaná úložiště dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats).
