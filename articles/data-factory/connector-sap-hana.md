---
title: Kopírovat data z SAP HANA
description: Naučte se, jak kopírovat data z SAP HANA do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/22/2020
ms.openlocfilehash: ce3c1e22dd030c0730bf4d9859591c00860908a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100382272"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Kopírování dat z SAP HANA pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-sap-hana-connector.md)
> * [Aktuální verze](connector-sap-hana.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z databáze SAP HANA. Sestaví se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který představuje obecný přehled aktivity kopírování.

>[!TIP]
>Pokud chcete získat přehled o celkové podpoře pro integraci dat v programu, přečtěte si článek [integrace dat SAP pomocí Azure Data Factory dokumentu White Paper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) s podrobným úvodem na jednotlivé konektory SAP, comparsion a doprovodné materiály.

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor SAP HANA se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)

Data z databáze SAP HANA můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Konkrétně tento konektor SAP HANA podporuje:

- Kopírování dat z libovolné verze SAP HANA databáze.
- Kopírování dat z **modelů informací Hana** (například analytických a výpočetních zobrazení) a **tabulek řádků a sloupců**.
- Kopírování dat pomocí **základního** ověřování nebo ověřování **systému Windows** .
- Paralelní kopírování ze zdroje SAP HANA. Podrobnosti najdete v části [paralelní kopírování z SAP HANA](#parallel-copy-from-sap-hana) .

> [!TIP]
> Chcete-li kopírovat data **do** SAP HANA úložiště dat, použijte obecný konektor ODBC. Podrobnosti najdete v části [SAP HANA jímka](#sap-hana-sink) . Všimněte si, že propojené služby konektoru SAP HANA Connector a konektor ODBC mají jiný typ, takže se nedá znovu použít.

## <a name="prerequisites"></a>Požadavky

Pokud chcete použít tento konektor SAP HANA, musíte:

- Nastavte Integration Runtime pro místní hostování. Podrobnosti najdete v článku [Integration runtime](create-self-hosted-integration-runtime.md) v místním prostředí.
- Na Integration Runtime počítač nainstalujte SAP HANA ovladač ODBC. Ovladač SAP HANA ODBC si můžete stáhnout z webu [SAP Software Download Center](https://support.sap.com/swdc). Vyhledejte pomocí klíčového slova **SAP HANA klienta pro Windows**.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factory entit specifických pro SAP HANA konektor.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro SAP HANA propojenou službu jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type musí být nastavená na: **SapHana** . | Yes |
| připojovací řetězec | Zadejte informace potřebné pro připojení k SAP HANA pomocí **základního ověřování** nebo **ověřování systému Windows**. Přečtěte si následující ukázky.<br>V připojovacím řetězci je server/port povinný (výchozí port je 30015) a uživatelské jméno a heslo je při použití základního ověřování povinné. Další upřesňující nastavení najdete v tématu [SAP HANA vlastnosti připojení ODBC](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>) .<br/>Můžete také do Azure Key Vault umístit heslo a načíst konfiguraci hesla z připojovacího řetězce. Další podrobnosti najdete [v článku uložení přihlašovacích údajů v Azure Key Vault](store-credentials-in-key-vault.md) článku. | Yes |
| userName | Při použití ověřování systému Windows zadejte uživatelské jméno. Příklad: `user@domain.com` | No |
| heslo | Zadejte heslo pro uživatelský účet. Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | No |
| connectVia | [Integration runtime](concepts-integration-runtime.md) , která se má použít pro připojení k úložišti dat Integration Runtime v místním prostředí se vyžaduje, jak je uvedeno v [požadavcích](#prerequisites). |Yes |

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

**Příklad: použití ověřování systému Windows**

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

Pokud jste používali SAP HANA propojenou službu s následující datovou částí, je stále podporovaná tak, jak je, a až budete chtít začít používat novinku dál.

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných sadou SAP HANA DataSet.

Chcete-li kopírovat data z SAP HANA, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type datové sady musí být nastavená na: **SapHanaTable** . | Yes |
| schema | Název schématu v databázi SAP HANA. | Ne (Pokud je zadáno "dotaz" ve zdroji aktivity) |
| tabulka | Název tabulky v databázi SAP HANA. | Ne (Pokud je zadáno "dotaz" ve zdroji aktivity) |

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

Pokud jste používali `RelationalTable` typovou datovou sadu, je stále podporovaná tak, jak je, a až budete chtít začít používat novinku dál.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných zdrojem SAP HANA.

### <a name="sap-hana-as-source"></a>SAP HANA jako zdroj

>[!TIP]
>K ingestování dat z SAP HANA efektivní pomocí dělení dat, další informace najdete v části [paralelní kopírování z SAP HANA](#parallel-copy-from-sap-hana) .

Chcete-li kopírovat data z SAP HANA, jsou v části **zdroje** aktivity kopírování podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type zdroje aktivity kopírování musí být nastavená na: **SapHanaSource** . | Yes |
| query | Určuje dotaz SQL pro čtení dat z instance SAP HANA. | Yes |
| partitionOptions | Určuje možnosti dělení dat používané k ingestování dat z SAP HANA. Další informace najdete v části  [paralelní kopírování z SAP HANA](#parallel-copy-from-sap-hana) .<br>Povolené hodnoty jsou: **none**   (default), **PhysicalPartitionsOfTable**, **SapHanaDynamicRange**. Další informace najdete v části  [paralelní kopírování z SAP HANA](#parallel-copy-from-sap-hana) . `PhysicalPartitionsOfTable` lze ji použít pouze při kopírování dat z tabulky, ale nikoli dotazů. <br>Pokud je možnost oddílu povolena (tj. ne `None` ), stupeň paralelismu na souběžně načtená data z SAP HANA řídí [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) Nastavení aktivity kopírování. | Ne |
| partitionSettings | Určete skupinu nastavení pro dělení dat.<br>Použijte, pokud je možnost oddílu `SapHanaDynamicRange` . | Ne |
| partitionColumnName | Zadejte název zdrojového sloupce, který bude oddíl používat pro paralelní kopírování. Pokud není zadaný, index nebo primární klíč tabulky se automaticky zjistí a použije se jako sloupec partition.<br>Použijte, pokud je parametr partition  `SapHanaDynamicRange` . Pokud použijete dotaz k načtení zdrojových dat, zapojte  `?AdfHanaDynamicRangePartitionCondition` v klauzuli WHERE. Viz příklad v sekci [paralelní kopírování z SAP HANA](#parallel-copy-from-sap-hana) . | Ano Při použití `SapHanaDynamicRange` oddílu |
| packetSize | Určuje velikost síťového paketu (v kilobajtech) pro rozdělení dat na více bloků. Pokud máte velké množství dat ke kopírování, zvýšení velikosti paketu může zvýšit rychlost čtení z SAP HANA ve většině případů. Při úpravě velikosti paketu se doporučuje testování výkonu. | No.<br>Výchozí hodnota je 2048 (2 MB). |

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

Pokud jste používali `RelationalSource` typový zdroj kopírování, je stále podporován tak, jak je, a až budete chtít začít používat nový.

## <a name="parallel-copy-from-sap-hana"></a>Paralelní kopírování z SAP HANA

Konektor Data Factory SAP HANA poskytuje integrované vytváření oddílů dat pro kopírování dat z SAP HANA paralelně. Možnosti dělení dat najdete ve **zdrojové** tabulce aktivity kopírování.

![Snímek obrazovky s možnostmi oddílů](./media/connector-sap-hana/connector-sap-hana-partition-options.png)

Když povolíte dělenou kopii, Data Factory spustí paralelní dotazy na zdroj SAP HANA a načte data podle oddílů. Paralelní míra je řízena [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) nastavením aktivity kopírování. Pokud jste například nastavili `parallelCopies` na čtyři, Data Factory souběžně generuje a spustí čtyři dotazy na základě zadané možnosti oddílu a nastavení a každý dotaz načte část dat z vašeho SAP HANA.

Navrhnete, abyste umožnili paralelní kopírování s vytvářením oddílů dat, obzvláště když obdržíte velké množství dat z SAP HANA. Následují Doporučené konfigurace pro různé scénáře. Při kopírování dat do úložiště dat založeného na souborech se doporučuje zapisovat do složky jako více souborů (zadat jenom název složky). v takovém případě je výkon lepší než zápis do jediného souboru.

| Scenario                                           | Navrhovaná nastavení                                           |
| -------------------------------------------------- | ------------------------------------------------------------ |
| Úplné načtení z velké tabulky                        | **Možnost oddílu**: fyzické oddíly tabulky. <br><br/>Během provádění Data Factory automaticky detekuje typ fyzického oddílu zadané SAP HANA tabulky a zvolí strategii pro příslušné oddíly:<br>- **Dělení rozsahu**: Získejte sloupce oddílu a rozsahy oddílů definované pro tabulku a pak zkopírujte data podle rozsahu. <br>- **Dělení hodnot hash**: použijte klíč oddílu hash jako sloupec partition a potom oddíl a zkopírujte data na základě počítaných rozsahů ADF. <br>- **Vytváření oddílů kruhového dotazování** nebo **žádného oddílu**: jako sloupec oddílu použijte primární klíč, potom oddíl a zkopírujte data na základě počítaných rozsahů ADF. |
| Načtení velkého množství dat pomocí vlastního dotazu. | **Možnost oddílu**: dynamický oddíl rozsahu.<br>**Dotaz**: `SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>` .<br>**Sloupec oddílu**: Zadejte sloupec použitý pro použití dynamického oddílu rozsahu. <br><br>Během provádění Data Factory za prvé vypočítá rozsahy hodnot v zadaném sloupci oddílu, a to rovnoměrně rozděluje řádky v řadě intervalů podle počtu jedinečných hodnot sloupců oddílů a nastavení paralelního kopírování ADF a pak `?AdfHanaDynamicRangePartitionCondition` se nahradí pomocí filtrování rozsahu hodnot sloupců oddílu pro každý oddíl a pošle se do SAP HANA.<br><br>Pokud chcete použít více sloupců jako sloupec partition, můžete zřetězit hodnoty každého sloupce jako jeden sloupec v dotazu a zadat ho jako sloupec partition v ADF, například `SELECT * FROM (SELECT *, CONCAT(<KeyColumn1>, <KeyColumn2>) AS PARTITIONCOLUMN FROM <TABLENAME>) WHERE ?AdfHanaDynamicRangePartitionCondition` . |

**Příklad: dotazování s fyzickými oddíly tabulky**

```json
"source": {
    "type": "SapHanaSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Příklad: dotaz s dynamickým oddílem rozsahu**

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

Při kopírování dat z SAP HANA se z SAP HANA datových typů používají následující mapování pro Azure Data Factory dočasných datových typů. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ do jímky, najdete v tématu [mapování typů schématu a dat](copy-activity-schema-and-type-mapping.md) .

| SAP HANA datový typ | Typ dat interim Data Factory |
| ------------------ | ------------------------------ |
| ALPHANUM           | Řetězec                         |
| BIGINT             | Int64                          |
| TVARU             | Byte []                         |
| BINTEXT            | Řetězec                         |
| PŘÍZNAKY               | Byte []                         |
| LOGICK               | Byte                           |
| DATOVÝ typ CLOB               | Řetězec                         |
| DATE               | DateTime                       |
| NOTACI            | Decimal                        |
| KLEPAT             | dvojité                         |
| Plovák              | dvojité                         |
| CELÉ ČÍSLO            | Int32                          |
| NCLOB              | Řetězec                         |
| NVARCHAR           | Řetězec                         |
| REÁLNÉ               | Jednoduché                         |
| SECONDDATE         | DateTime                       |
| SHORTTEXT          | Řetězec                         |
| SMALLDECIMAL       | Decimal                        |
| SMALLINT           | Int16                          |
| STGEOMETRYTYPE     | Byte []                         |
| STPOINTTYPE        | Byte []                         |
| TEXT               | Řetězec                         |
| ČAS               | TimeSpan                       |
| TINYINT            | Byte                           |
| VARCHAR            | Řetězec                         |
| ČASOVÉ RAZÍTKO          | DateTime                       |
| VARBINARY          | Byte []                         |

## <a name="sap-hana-sink"></a>SAP HANA jímka

V současné době není konektor SAP HANA podporován jako jímka, zatímco můžete použít obecný konektor ODBC s ovladačem SAP HANA k zápisu dat do SAP HANA. 

Postupujte podle [požadavků](#prerequisites) pro nastavení Integration runtime v místním prostředí a nejprve nainstalujte SAP HANA ovladač ODBC. Vytvořte propojenou službu ODBC pro připojení k vašemu SAP HANA úložiště dat, jak je znázorněno v následujícím příkladu, a odpovídajícím způsobem Vytvořte datovou sadu a jímku aktivity kopírování s rozhraním ODBC. Další informace najdete v článku [konektor ODBC](connector-odbc.md) .

```json
{
    "name": "SAPHANAViaODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "Driver={HDBODBC};servernode=<HANA server>.clouddatahub-int.net:30015",
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

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
