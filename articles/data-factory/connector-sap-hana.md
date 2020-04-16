---
title: Kopírování dat ze systému SAP HANA
description: Zjistěte, jak zkopírovat data ze SAP HANA do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/17/2020
ms.openlocfilehash: 74462b68bea38e4d84219adeedb7c3bb0893bbb4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417240"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Kopírování dat ze SAP HANA pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-sap-hana-connector.md)
> * [Aktuální verze](connector-sap-hana.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak použít aktivitu kopírování v Azure Data Factory ke kopírování dat z databáze SAP HANA. Vychází z článku [přehledu aktivity kopírování,](copy-activity-overview.md) který představuje obecný přehled aktivity kopírování.

>[!TIP]
>Informace o celkové podpoře ADF ve scénáři integrace dat SAP najdete v článku [integrace dat SAP pomocí whitepaper Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) s podrobným úvodem, porovnáním a pokyny.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor SAP HANA je podporován pro následující aktivity:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Data z databáze SAP HANA můžete zkopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných jako zdroje nebo propady aktivitou kopírování naleznete v tabulce [Podporovaná data.](copy-activity-overview.md#supported-data-stores-and-formats)

Konkrétně tento konektor SAP HANA podporuje:

- Kopírování dat z libovolné verze databáze SAP HANA.
- Kopírování dat z **informačních modelů HANA** (například zobrazení Analytica a Výpočet) a **tabulek řádků/sloupců**.
- Kopírování dat pomocí **základního** ověřování nebo ověřování **systému Windows.**
- Paralelní kopírování ze zdroje SAP HANA. Podrobnosti najdete v části [Paralelní kopie z SAP HANA.](#parallel-copy-from-sap-hana)

> [!TIP]
> Chcete-li zkopírovat data **do** úložiště dat SAP HANA, použijte obecný konektor ODBC. Viz [SAP HANA jímky](connector-odbc.md#sap-hana-sink) s podrobnostmi. Všimněte si, že propojené služby pro konektor SAP HANA a konektor ODBC jsou s jiným typem, proto nelze znovu použít.

## <a name="prerequisites"></a>Požadavky

Chcete-li použít tento konektor SAP HANA, musíte:

- Nastavte runtime integrace s vlastním hostitelem. Podrobnosti najdete v článku [runtime integrace s vlastním hostitelem.](create-self-hosted-integration-runtime.md)
- Nainstalujte ovladač SAP HANA ODBC do integračního automatu Runtime. Ovladač SAP HANA ODBC si můžete stáhnout z webu [SAP Software Download Center](https://support.sap.com/swdc). Vyhledejte klíčové slovo **SAP HANA CLIENT pro Windows**.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Factory dat specifických pro konektor SAP HANA.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu SAP HANA jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na: **SapHana** | Ano |
| připojovací řetězec | Zadejte informace potřebné pro připojení k SAP HANA pomocí **základního ověřování** nebo **ověřování systému Windows**. Viz následující ukázky.<br>V připojovacím řetězci je server/port povinný (výchozí port je 30015) a uživatelské jméno a heslo je povinné při použití základního ověřování. Další upřesňující nastavení naleznete v části [Vlastnosti připojení SAP HANA ODBC](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>)<br/>Můžete také umístit heslo do azure key vault a vyžádat konfiguraci hesla z připojovacího řetězce. Další podrobnosti najdete v článku [úložiště klíčů Azure](store-credentials-in-key-vault.md) s dalšími podrobnostmi. | Ano |
| userName | Při použití ověřování systému Windows zadejte uživatelské jméno. Příklad: `user@domain.com` | Ne |
| heslo | Zadejte heslo pro uživatelský účet. Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ne |
| connectVia | [Prostředí Integrace Runtime,](concepts-integration-runtime.md) které se má použít k připojení k úložišti dat. Runtime integrace hostované samostatně je vyžadován, jak je uvedeno v [požadavky](#prerequisites). |Ano |

**Příklad: použití základního ověřování**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;UID=<userName>;PWD=<Password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Příklad: Použití ověřování systému Windows**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;",
            "userName": "<username>", 
            "password": { 
                "type": "SecureString", 
                "value": "<password>" 
            } 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Pokud jste používali službu sap hana propojené s následující datové části, je stále podporována jako-je, zatímco se doporučuje použít novou do budoucna.

**Příklad:**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datových sad.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných datovou sadou SAP HANA.

Chcete-li zkopírovat data ze systému SAP HANA, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena na: **SapHanaTable.** | Ano |
| Schématu | Název schématu v databázi SAP HANA. | Ne (pokud je zadán "dotaz" ve zdroji aktivity) |
| tabulka | Název tabulky v databázi SAP HANA. | Ne (pokud je zadán "dotaz" ve zdroji aktivity) |

**Příklad:**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "SapHanaTable",
        "typeProperties": {
            "schema": "<schema name>",
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Pokud jste `RelationalTable` používali zadaný datový soubor, je stále podporována tak, jak je, zatímco se doporučuje používat novou do budoucna.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem SAP HANA.

### <a name="sap-hana-as-source"></a>SAP HANA jako zdroj

>[!TIP]
>Efektivní ingestování dat z SAP HANA pomocí dělení dat, další informace z paralelní kopie z části [SAP HANA.](#parallel-copy-from-sap-hana)

Chcete-li kopírovat data ze systému SAP HANA, jsou v části **zdroje** aktivity kopírování podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavena na: **SapHanaSource.** | Ano |
| query | Určuje dotaz SQL pro čtení dat z instance SAP HANA. | Ano |
| partitionOptions | Určuje možnosti dělení dat používané k ingestování dat ze systému SAP HANA. Další informace o [paralelní kopii z SAP HANA](#parallel-copy-from-sap-hana) části.<br>Povolit hodnoty jsou: **None** (výchozí), **PhysicalPartitionsOfTable**, **SapHanaDynamicRange**. Další informace o [paralelní kopii z SAP HANA](#parallel-copy-from-sap-hana) části. `PhysicalPartitionsOfTable`lze použít pouze při kopírování dat z tabulky, ale nikoli při dotazování. <br>Pokud je povolena možnost oddílu `None`(to znamená, že ne ), stupeň paralelismu souběžně načíst data z SAP HANA je řízen [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) nastavení na aktivitu kopírování. | False |
| partitionSettings | Zadejte skupinu nastavení pro dělení dat.<br>Použít, pokud `SapHanaDynamicRange`je možnost oddílu . | False |
| partitionColumnName | Zadejte název zdrojového sloupce, který bude oddíl používat pro paralelní kopírování. Pokud není zadán, index nebo primární klíč tabulky je automaticky rozpoznán a použit jako sloupec oddílu.<br>Použít, pokud je `SapHanaDynamicRange`možnost oddílu . Pokud použijete dotaz k načtení `?AdfHanaDynamicRangePartitionCondition` zdrojových dat, zavěste do klauzule WHERE. Viz příklad v paralelní kopírování z části [SAP HANA.](#parallel-copy-from-sap-hana) | Ano, `SapHanaDynamicRange` při použití oddílu. |
| packetSize | Určuje velikost síťového paketu (v kilobajtech), aby se data rozdělila na více bloků. Pokud máte velké množství dat ke kopírování, zvýšení velikosti paketu může zvýšit rychlost čtení z SAP HANA ve většině případů. Při úpravě velikosti paketu se doporučuje testování výkonu. | Ne.<br>Výchozí hodnota je 2048 (2 MB). |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
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
                "type": "SapHanaSource",
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Pokud jste `RelationalSource` používali zadaný zdroj kopírování, je stále podporován tak, jak je, zatímco se doporučuje použít nový do budoucna.

## <a name="parallel-copy-from-sap-hana"></a>Paralelní kopírování z SAP HANA

Konektor SAP HANA data poskytuje vestavěné dělení dat pro paralelní kopírování dat z SAP HANA. Možnosti dělení dat najdete v **zdrojové** tabulce aktivity kopírování.

![Snímek obrazovky s možnostmi oddílu](./media/connector-sap-hana/connector-sap-hana-partition-options.png)

Když povolíte rozdělenou kopii, Data Factory spustí paralelní dotazy proti zdroji SAP HANA k načtení dat pomocí oddílů. Paralelní stupeň je [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) řízen nastavením aktivity kopírování. Pokud například nastavíte `parallelCopies` na čtyři, Data Factory současně generuje a spouští čtyři dotazy na základě zadané možnosti oddílu a nastavení a každý dotaz načte část dat z vašeho SAP HANA.

Doporučujese povolit paralelní kopírování s dělení dat zejména při ingestování velké množství dat z vašeho SAP HANA. Níže jsou navrženy konfigurace pro různé scénáře. Při kopírování dat do úložiště dat založeného na souborech se doporučuje zapisovat do složky jako více souborů (pouze zadejte název složky), v takovém případě je výkon lepší než zápis do jednoho souboru.

| Scénář                                           | Navrhovaná nastavení                                           |
| -------------------------------------------------- | ------------------------------------------------------------ |
| Plné zatížení z velkého stolu.                        | **Možnost oddílu**: Fyzické oddíly tabulky. <br><br/>Během provádění data factory automaticky detekuje typ fyzického oddílu zadané tabulky SAP HANA a zvolte odpovídající strategii oddílu:<br>- **Dělení rozsahu**: Získejte sloupec oddílu a rozsahy oddílů definované pro tabulku a potom zkopírujte data podle rozsahu. <br>- **Rozdělení hash**: Použijte klíč oddílu hash jako sloupec oddílu, potom rozdělte a zkopírujte data na základě vypočtených rozsahů ADF. <br>- **Round-Robin Dělení** nebo **Žádný oddíl**: Použijte primární klíč jako sloupec oddílu, potom oddíl a zkopírujte data na základě adf počítané rozsahy. |
| Načtěte velké množství dat pomocí vlastního dotazu. | **Možnost oddílu**: Oddíl dynamického rozsahu.<br>**Dotaz** `SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>`: .<br>**Sloupec oddílu**: Zadejte sloupec použitý k použití oddílu dynamického rozsahu. <br><br>Během provádění Data Factory nejprve vypočítá rozsahy hodnot zadaného sloupce oddílu tím, že rovnoměrně distribuuje řádky v počtu bloků podle počtu `?AdfHanaDynamicRangePartitionCondition` různých hodnot sloupců oddílu a nastavení paralelní kopie ADF, pak nahradí filtrování mačkání rozsah hodnoty sloupce oddílu pro každý oddíl a odešle SAP HANA.<br><br>Pokud chcete použít více sloupců jako sloupec oddílu, můžete zřetězit hodnoty každého sloupce jako jeden sloupec v `SELECT * FROM (SELECT *, CONCAT(<KeyColumn1>, <KeyColumn2>) AS PARTITIONCOLUMN FROM <TABLENAME>) WHERE ?AdfHanaDynamicRangePartitionCondition`dotazu a zadat jej jako sloupec oddílu v adf, jako . |

**Příklad: dotaz s fyzickými oddíly tabulky**

```json
"source": {
    "type": "SapHanaSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Příklad: dotaz s oddílem dynamického rozsahu**

```json
"source": {
    "type": "SapHanaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "SapHanaDynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<Partition_column_name>"
    }
}
```

## <a name="data-type-mapping-for-sap-hana"></a>Mapování datových typů pro SAP HANA

Při kopírování dat z SAP HANA se používají následující mapování z datových typů SAP HANA do dočasných datových typů Azure Data Factory. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ do jímky, najdete v tématu [mapování schématu a datových typů.](copy-activity-schema-and-type-mapping.md)

| Datový typ SAP HANA | Dočasný datový typ datové továrny |
| ------------------ | ------------------------------ |
| ALFANUM           | Řetězec                         |
| Bigint             | Int64                          |
| Binární             | Bajt[]                         |
| BINTEXT            | Řetězec                         |
| Blob               | Bajt[]                         |
| Bool               | Byte                           |
| Clob               | Řetězec                         |
| DATE (Datum)               | DateTime                       |
| Desetinných            | Desetinné číslo                        |
| Dvojité             | Double                         |
| Float              | Double                         |
| CELÉ ČÍSLO            | Int32                          |
| Nclob              | Řetězec                         |
| Nvarchar           | Řetězec                         |
| REÁLNÉ               | Single                         |
| DRUHÉ DATUM         | DateTime                       |
| ZKRÁCENÝ TEXT          | Řetězec                         |
| SMALLDECIMAL       | Desetinné číslo                        |
| Smallint           | Int16                          |
| STGEOMETRYTYP     | Bajt[]                         |
| STPOINTTYP        | Bajt[]                         |
| TEXT               | Řetězec                         |
| ČAS               | TimeSpan                       |
| Tinyint            | Byte                           |
| Varchar            | Řetězec                         |
| Časové razítko          | DateTime                       |
| Varbinary          | Bajt[]                         |

## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v Azure Data Factory najdete v [tématu podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
