---
title: Kopírování dat z a do Snowflake
description: Naučte se, jak kopírovat data z a do Snowflake pomocí Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/09/2020
ms.openlocfilehash: 43839e19eb252c9fa7ab46605fd247f3a798d223
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2020
ms.locfileid: "86220299"
---
# <a name="copy-data-from-and-to-snowflake-by-using-azure-data-factory"></a>Kopírování dat z a do Snowflake pomocí Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z a do Snowflake. Další informace o Data Factory najdete v [úvodním článku](introduction.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Snowflake je podporován pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou tabulkou matice zdroje/jímky](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)

Pro aktivitu kopírování podporuje tento konektor Snowflake tyto funkce:

- Kopírovat data z Snowflake, která využívá příkaz [Kopírovat do [location]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) Snowflake, aby dosáhla nejlepšího výkonu.
- Zkopírujte data do Snowflake, která využívá k dosažení nejlepšího výkonu výhod příkazu [Kopírovat do [Table]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) Snowflake. Podporuje Snowflake v Azure.

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které definují Data Factory entit specifických pro konektor Snowflake.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro službu propojenou s Snowflake jsou podporovány následující vlastnosti.

| Vlastnost         | Popis                                                  | Vyžadováno |
| :--------------- | :----------------------------------------------------------- | :------- |
| typ             | Vlastnost Type musí být nastavená na **Snowflake**.              | Ano      |
| připojovací řetězec | Nakonfigurujte [úplný název účtu](https://docs.snowflake.net/manuals/user-guide/connecting.html#your-snowflake-account-name) (včetně dalších segmentů, které identifikují oblast a cloudovou platformu), uživatelské jméno, heslo, databázi a datový sklad. Zadejte připojovací řetězec JDBC pro připojení k instanci Snowflake. Můžete také do Azure Key Vault umístit heslo. Další podrobnosti najdete v příkladech pod tabulkou a také s [přihlašovacími údaji úložiště v Azure Key Vaultovém](store-credentials-in-key-vault.md) článku.| Ano      |
| connectVia       | [Prostředí Integration runtime](concepts-integration-runtime.md) , které se používá pro připojení k úložišti dat. Můžete použít prostředí Azure Integration runtime nebo místní prostředí Integration runtime (Pokud se vaše úložiště dat nachází v privátní síti). Pokud není zadaný, použije se výchozí prostředí Azure Integration runtime. | Ne       |

**Případě**

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&password=<password>&db=<database>&warehouse=<warehouse>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Heslo v Azure Key Vault:**

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&db=<database>&warehouse=<warehouse>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>",
                    "type": "LinkedServiceReference"
                }, 
                "secretName": "<secretName>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . 

Následující vlastnosti jsou podporovány pro datovou sadu Snowflake.

| Vlastnost  | Popis                                                  | Vyžadováno                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| typ      | Vlastnost Type datové sady musí být nastavená na **SnowflakeTable**. | Ano                         |
| XSD | Název schématu. |Ne pro zdroj, Ano pro jímku  |
| table | Název tabulky/zobrazení |Ne pro zdroj, Ano pro jímku  |

**Případě**

```json
{
    "name": "SnowflakeDataset",
    "properties": {
        "type": "SnowflakeTable",
        "typeProperties": {
            "schema": "<Schema name for your Snowflake database>",
            "table": "<Table name for your Snowflake database>"
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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností, které Snowflake zdroj a jímka podporuje.

### <a name="snowflake-as-the-source"></a>Snowflake jako zdroj

Snowflake Connector využívá k dosažení nejlepšího výkonu příkaz Kopírovat z Snowflake [do [location]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) .

Pokud jsou úložiště a formát dat jímky nativně podporované příkazem kopírování Snowflake, můžete aktivitu kopírování použít k přímému kopírování z Snowflake do jímky. Podrobnosti najdete v tématu [Přímá kopie z Snowflake](#direct-copy-from-snowflake). Jinak použijte vestavěnou [fázi kopírování z Snowflake](#staged-copy-from-snowflake).

Chcete-li kopírovat data z Snowflake, v části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti.

| Vlastnost                     | Popis                                                  | Vyžadováno |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| typ                         | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **SnowflakeSource**. | Ano      |
| query          | Určuje dotaz SQL, který má načíst data z Snowflake.<br>Provádění uložené procedury není podporováno. | Ne       |
| exportSettings | Rozšířená nastavení používaná k načtení dat z Snowflake. Můžete nakonfigurovat ty, které podporuje příkaz Kopírovat do, který Data Factory projde při vyvolání příkazu. | Ne       |
| ***V části `exportSettings` :*** |  |  |
| typ | Typ příkazu pro export nastavený na **SnowflakeExportCopyCommand**. | Ano |
| additionalCopyOptions | Další možnosti kopírování, které jsou k dispozici jako slovník párů klíč-hodnota. Příklady: MAX_FILE_SIZE, OVERWRITE. Další informace najdete v tématu [Možnosti kopírování Snowflake](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#copy-options-copyoptions). | Ne |
| additionalFormatOptions | Další možnosti formátu souboru, které jsou k dispozici pro kopírování příkazu jako slovníku párů klíč-hodnota. Příklady: DATE_FORMAT, TIME_FORMAT TIMESTAMP_FORMAT. Další informace najdete v tématu [Možnosti typu formátu Snowflake](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#format-type-options-formattypeoptions). | Ne |

#### <a name="direct-copy-from-snowflake"></a>Přímá kopie z Snowflake

Pokud vaše úložiště a formát dat jímky splňují kritéria popsaná v této části, můžete aktivitu kopírování použít k přímému kopírování z Snowflake do jímky. Data Factory kontroluje nastavení a v případě, že nejsou splněná následující kritéria, se spustí aktivita kopírování:

- **Propojená služba jímky** je [**úložiště objektů BLOB v Azure**](connector-azure-blob-storage.md) s ověřováním pomocí **sdíleného přístupového podpisu** .

- **Formát dat jímky** je **Parquet**, **oddělený text**nebo **JSON** s následujícími konfiguracemi:

    - V případě formátu **Parquet** je Kompresní kodek **none**, **přichycení**nebo **LZO**.
    - Textový formát s **oddělovači** :
        - `rowDelimiter`je **\r\n**nebo jakýkoli jeden znak.
        - `compression`nemůže být **žádná komprese**, **gzip**, **bzip2**ani **zúžení**.
        - `encodingName`je ponechán jako výchozí nebo nastavený na **UTF-8**.
        - `quoteChar`je **dvojité uvozovky**, **jednoduché uvozovky** nebo **prázdný řetězec** (bez znaku uvozovek).
    - V případě formátu **JSON** podporuje Přímá kopie pouze jeden sloupec, protože zdroj Snowflake tabulky nebo výsledek dotazu obsahuje pouze jeden sloupec a datový typ tohoto sloupce je **variant**, **objekt**nebo **pole**.
        - `compression`nemůže být **žádná komprese**, **gzip**, **bzip2**ani **zúžení**.
        - `encodingName`je ponechán jako výchozí nebo nastavený na **UTF-8**.
        - `filePattern`v jímky aktivity kopírování je ponecháno jako výchozí nebo nastavené na **setOfObjects**.

- Ve zdroji aktivity kopírování není `additionalColumns` zadán.
- Není zadáno mapování sloupce.

**Případě**

```json
"activities":[
    {
        "name": "CopyFromSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Snowflake input dataset name>",
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
                "type": "SnowflakeSource",
                "sqlReaderQuery": "SELECT * FROM MyTable",
                "exportSettings": {
                    "type": "SnowflakeExportCopyCommand",
                    "additionalCopyOptions": {
                        "MAX_FILE_SIZE": "64000000",
                        "OVERWRITE": true
                    },
                    "additionalFormatOptions": {
                        "DATE_FORMAT": "'MM/DD/YYYY'"
                    }
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="staged-copy-from-snowflake"></a>Příprava kopírování z Snowflake

Pokud vaše úložiště nebo formát dat jímky nejsou nativně kompatibilní s příkazem kopírování Snowflake, jak je uvedeno v poslední části, povolte vestavěnou dvoufázové kopii pomocí dočasné instance služby Azure Blob Storage. Funkce dvoufázové kopírování nabízí také lepší propustnost. Data Factory exportuje data z Snowflake do pracovního úložiště, pak data zkopíruje do jímky a nakonec vyčistí dočasná data z přípravného úložiště. Podrobnosti o kopírování dat pomocí přípravy najdete v tématu [připravené kopírování](copy-activity-performance-features.md#staged-copy) .

Pokud chcete tuto funkci použít, vytvořte [propojenou službu Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) , která jako průběžné přípravy odkazuje na účet služby Azure Storage. Pak zadejte `enableStaging` vlastnosti a `stagingSettings` v aktivitě kopírování.

> [!NOTE]
> V pracovní službě Azure Blob Storage se musí použít ověřování pomocí sdíleného přístupového podpisu, jak vyžaduje příkaz Snowflake COPY. 

**Případě**

```json
"activities":[
    {
        "name": "CopyFromSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Snowflake input dataset name>",
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
                "type": "SnowflakeSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
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

### <a name="snowflake-as-sink"></a>Snowflake jako jímka

Konektor Snowflake používá k dosažení nejlepšího výkonu příkaz Kopírovat z Snowflake [do [Table]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) . Podporuje zápis dat do Snowflake v Azure.

Pokud je zdrojové úložiště dat a formát nativně podporované příkazem Snowflake COPY, můžete aktivitu kopírování použít k přímému kopírování ze zdroje do Snowflake. Podrobnosti najdete v tématu [Přímá kopírování do Snowflake](#direct-copy-to-snowflake). Jinak použijte vestavěnou [fázi kopírování do Snowflake](#staged-copy-to-snowflake).

Chcete-li kopírovat data do Snowflake, jsou v části **jímka** aktivity kopírování podporovány následující vlastnosti.

| Vlastnost          | Popis                                                  | Vyžadováno                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| typ              | Vlastnost Type jímky aktivity kopírování nastavená na **SnowflakeSink**. | Ano                                           |
| preCopyScript     | Zadejte dotaz SQL pro aktivitu kopírování, která se má spustit před zápisem dat do Snowflake při každém spuštění. Tato vlastnost slouží k vyčištění předem načtených dat. | Ne                                            |
| importSettings | Rozšířená nastavení používaná k zápisu dat do Snowflake. Můžete nakonfigurovat ty, které podporuje příkaz Kopírovat do, který Data Factory projde při vyvolání příkazu. | Ne |
| ***V části `importSettings` :*** |                                                              |  |
| typ | Typ příkazu pro import, který je nastavený na **SnowflakeImportCopyCommand**. | Ano |
| additionalCopyOptions | Další možnosti kopírování, které jsou k dispozici jako slovník párů klíč-hodnota. Příklady: ON_ERROR, FORCE, LOAD_UNCERTAIN_FILES. Další informace najdete v tématu [Možnosti kopírování Snowflake](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#copy-options-copyoptions). | Ne |
| additionalFormatOptions | Další možnosti formátu souboru, které jsou k dispozici pro příkaz kopírování, který je k dispozici jako slovník párů klíč-hodnota. Příklady: DATE_FORMAT, TIME_FORMAT TIMESTAMP_FORMAT. Další informace najdete v tématu [Možnosti typu formátu Snowflake](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#format-type-options-formattypeoptions). | Ne |

#### <a name="direct-copy-to-snowflake"></a>Přímá kopie na Snowflake

Pokud zdrojové úložiště a formát dat splňují kritéria popsaná v této části, můžete aktivitu kopírování použít k přímému kopírování ze zdroje do Snowflake. Azure Data Factory kontroluje nastavení a v případě, že nejsou splněná následující kritéria, se spustí aktivita kopírování:

- **Zdrojová propojená služba** je [**úložiště objektů BLOB v Azure**](connector-azure-blob-storage.md) s ověřováním pomocí **sdíleného přístupového podpisu** .

- **Formát zdrojových dat** je **Parquet**, **oddělený text**nebo **JSON** s následujícími konfiguracemi:

    - Pro formát **Parquet** je Kompresní kodek **žádný**nebo **přichycený**.

    - Textový formát s **oddělovači** :
        - `rowDelimiter`je **\r\n**nebo jakýkoli jeden znak. Pokud není oddělovač řádků "\r\n", `firstRowAsHeader` je nutné mít **hodnotu false**a není `skipLineCount` zadán.
        - `compression`nemůže být **žádná komprese**, **gzip**, **bzip2**ani **zúžení**.
        - `encodingName`je ponechán jako výchozí nebo nastavený na UTF-8, UTF-16, UTF-16BE, UTF-32 "," UTF-32BE "," BIG5 "," EUC-JP "," EUC-KR "," UTF ",", "," ISO-2022-JP ";" ISO-2022-KR "," ISO-8859-1 "," ISO-8859-2 "," ISO-8859-5 "," ISO-8859-6 "," ISO-8859-7 "," ISO-8859-8 "," ISO-8859-9 "," WINDOWS-1250 "," WINDOWS-1251 "," WINDOWS-1252 "," WINDOWS-1253 "," Windows-1254 "," Windows-1255 ";
        - `quoteChar`je **dvojité uvozovky**, **jednoduché uvozovky** nebo **prázdný řetězec** (bez znaku uvozovek).
    - V případě formátu **JSON** podporuje Přímá kopie pouze jeden sloupec a datový typ tohoto sloupce je **variantou**, **objektem**nebo **polem**.
        - `compression`nemůže být **žádná komprese**, **gzip**, **bzip2**ani **zúžení**.
        - `encodingName`je ponechán jako výchozí nebo nastavený na **UTF-8**.
        - Není zadáno mapování sloupce.

- Ve zdroji aktivity kopírování: 

   -  `additionalColumns`není zadáno.
   - Pokud je zdrojem složka, `recursive` je nastaveno na hodnotu true.
   - `prefix`nejsou `modifiedDateTimeStart` `modifiedDateTimeEnd` zadány.

**Případě**

```json
"activities":[
    {
        "name": "CopyToSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Snowflake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SnowflakeSink",
                "importSettings": {
                    "type": "SnowflakeImportCopyCommand",
                    "copyOptions": {
                        "FORCE": "TRUE",
                        "ON_ERROR": "SKIP_FILE",
                    },
                    "fileFormatOptions": {
                        "DATE_FORMAT": "YYYY-MM-DD",
                    }
                }
            }
        }
    }
]
```

#### <a name="staged-copy-to-snowflake"></a>Příprava kopírování na Snowflake

Pokud vaše úložiště nebo formát dat jímky nejsou nativně kompatibilní s příkazem kopírování Snowflake, jak je uvedeno v poslední části, povolte vestavěnou dvoufázové kopii pomocí dočasné instance služby Azure Blob Storage. Funkce dvoufázové kopírování nabízí také lepší propustnost. Data Factory automaticky převede data tak, aby splňovala požadavky na formát dat Snowflake. Potom vyvolá příkaz COPY pro načtení dat do Snowflake. Nakonec vymaže vaše dočasná data z úložiště objektů BLOB. Podrobnosti o kopírování dat pomocí přípravy najdete v tématu [připravené kopírování](copy-activity-performance-features.md#staged-copy) .

Pokud chcete tuto funkci použít, vytvořte [propojenou službu Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) , která jako průběžné přípravy odkazuje na účet služby Azure Storage. Pak zadejte `enableStaging` vlastnosti a `stagingSettings` v aktivitě kopírování.

> [!NOTE]
> Služba propojené služby Azure Blob Storage musí používat ověřování pomocí sdíleného přístupového podpisu, jak vyžaduje příkaz Snowflake COPY.

**Případě**

```json
"activities":[
    {
        "name": "CopyToSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Snowflake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SnowflakeSink"
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


## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Další informace o vlastnostech najdete v tématu [aktivita vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky

Seznam úložišť dat podporovaných jako zdroje a jímky pomocí aktivity kopírování v Data Factory najdete v tématu [podporovaná úložiště dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats).
