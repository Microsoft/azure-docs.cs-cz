---
title: Kopírování dat z SAP HANA pomocí Azure Data Factory | Microsoft Docs
description: Naučte se, jak kopírovat data z SAP HANA do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: 9a5592c0c5fa06d8319b91c6d624a74c83bdeb1f
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2019
ms.locfileid: "71010430"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Kopírování dat z SAP HANA pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-sap-hana-connector.md)
> * [Aktuální verze](connector-sap-hana.md)

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z databáze SAP HANA. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

>[!TIP]
>Pokud chcete získat přehled o celkové podpoře pro integraci dat přes ADF, přečtěte si článek [integrace dat SAP pomocí Azure Data Factory dokumentu White Paper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) s podrobnými pokyny k úvodu, comparsion a pokyny.

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor SAP HANA se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)

Data z databáze SAP HANA můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Konkrétně tento konektor SAP HANA podporuje:

- Kopírování dat z libovolné verze SAP HANA databáze.
- Kopírování dat z **modelů informací Hana** (například analytických a výpočetních zobrazení) a **tabulek řádků a sloupců**.
- Kopírování dat pomocí **základního** ověřování nebo ověřování **systému Windows** .

> [!TIP]
> Chcete-li kopírovat data **do** SAP HANA úložiště dat, použijte obecný konektor ODBC. Viz část [SAP HANA jímka](connector-odbc.md#sap-hana-sink) s podrobnostmi. Všimněte si, že propojené služby konektoru SAP HANA Connector a konektor ODBC mají jiný typ, takže se nedá znovu použít.

## <a name="prerequisites"></a>Požadavky

Pokud chcete použít tento konektor SAP HANA, musíte:

- Nastavte Integration Runtime pro místní hostování. Zobrazit [modul Integration Runtime](create-self-hosted-integration-runtime.md) , kde najdete podrobnosti.
- Na Integration Runtime počítač nainstalujte SAP HANA ovladač ODBC. Ovladač SAP HANA ODBC si můžete stáhnout z [webu SAP software Download Center](https://support.sap.com/swdc). Vyhledejte pomocí klíčového slova **SAP HANA klienta pro Windows**.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factory entit specifických pro SAP HANA konektor.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro SAP HANA propojenou službu jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavená na: **SapHana** | Ano |
| connectionString | Zadejte informace potřebné pro připojení k SAP HANA pomocí **základního ověřování** nebo **ověřování systému Windows**. Přečtěte si následující ukázky.<br>V připojovacím řetězci je server/port povinný (výchozí port je 30015) a uživatelské jméno a heslo je při použití základního ověřování povinné. Další upřesňující nastavení najdete v tématu [SAP HANA vlastnosti připojení ODBC](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>) .<br/>Můžete také do Azure Key Vault umístit heslo a načíst konfiguraci hesla z připojovacího řetězce. Další podrobnosti najdete [v článku uložení přihlašovacích údajů v Azure Key Vault](store-credentials-in-key-vault.md) článku. | Ano |
| userName | Při použití ověřování systému Windows zadejte uživatelské jméno. Příklad: `user@domain.com` | Ne |
| password | Zadejte heslo pro uživatelský účet. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ne |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Integration Runtime v místním prostředí se vyžaduje, jak je uvedeno v [požadavcích](#prerequisites). |Ano |

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. V této části najdete seznam vlastností podporovaných sadou SAP HANA DataSet.

Chcete-li kopírovat data z SAP HANA, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type datové sady musí být nastavená na: **SapHanaTable** | Ano |
| schema | Název schématu v databázi SAP HANA. | Ne (když je zadán zdroj aktivity "query") |
| table | Název tabulky v databázi SAP HANA. | Ne (když je zadán zdroj aktivity "query") |

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

Pokud jste `RelationalTable` používali typovou datovou sadu, je stále podporovaná tak, jak je, a až budete chtít začít používat novinku dál.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. V této části najdete seznam vlastností podporovaných zdrojem SAP HANA.

### <a name="sap-hana-as-source"></a>SAP HANA jako zdroj

Chcete-li kopírovat data z SAP HANA, jsou v části **zdroje** aktivity kopírování podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type zdroje aktivity kopírování musí být nastavená na: **SapHanaSource** | Ano |
| query | Určuje dotaz SQL pro čtení dat z instance SAP HANA. | Ano |
| packetSize | Určuje velikost síťového paketu (v kilobajtech) pro rozdělení dat na více bloků. Pokud máte velké množství dat ke kopírování, zvýšení velikosti paketu může zvýšit rychlost čtení z SAP HANA ve většině případů. Při úpravě velikosti paketu se doporučuje testování výkonu. | Ne.<br>Výchozí hodnota je 2048 (2 MB). |

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

## <a name="data-type-mapping-for-sap-hana"></a>Mapování datových typů pro SAP HANA

Při kopírování dat z SAP HANA se z SAP HANA datových typů používají následující mapování pro Azure Data Factory dočasných datových typů. Zobrazit [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) Další informace o způsobu, jakým aktivitu kopírování, která mapuje typ zdroje schéma a data na jímce.

| SAP HANA datový typ | Data factory dočasné datový typ |
| ------------------ | ------------------------------ |
| ALPHANUM           | Řetězec                         |
| BIGINT             | Int64                          |
| TVARU             | Byte[]                         |
| BINTEXT            | Řetězec                         |
| PŘÍZNAKY               | Byte[]                         |
| LOGICK               | Byte                           |
| CLOB               | Řetězec                         |
| DATE               | DateTime                       |
| NOTACI            | Decimal                        |
| KLEPAT             | Double                         |
| FLOAT              | Double                         |
| INTEGER            | Int32                          |
| NCLOB              | Řetězec                         |
| NVARCHAR           | Řetězec                         |
| NEMOVITOSTÍ               | Single                         |
| SECONDDATE         | DateTime                       |
| SHORTTEXT          | Řetězec                         |
| SMALLDECIMAL       | Decimal                        |
| SMALLINT           | Int16                          |
| STGEOMETRYTYPE     | Byte[]                         |
| STPOINTTYPE        | Byte[]                         |
| TEXT               | Řetězec                         |
| TIME               | TimeSpan                       |
| TINYINT            | Byte                           |
| VARCHAR            | Řetězec                         |
| TIMESTAMP          | DateTime                       |
| VARBINARY          | Byte[]                         |

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
