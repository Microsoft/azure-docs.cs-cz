---
title: Kopírování dat do a z Oracle pomocí služby Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data z podporované zdrojové úložiště pro databáze Oracle nebo Oracle do jímky podporovaných úložišť pomocí služby Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/14/2018
ms.author: jingwang
ms.openlocfilehash: 5039399ac875add02319e1a745d99344956c7bee
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2018
ms.locfileid: "37860210"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Kopírování dat z a do databáze Oracle pomocí služby Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](v1/data-factory-onprem-oracle-connector.md)
> * [Aktuální verze](connector-oracle.md)

Tento článek ukazuje, jak použít aktivitu kopírování ke kopírování dat z databáze Oracle ve službě Azure Data Factory. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Kopírování dat z databáze Oracle do jakékoli podporovaného úložiště dat jímky. Můžete také můžete kopírovat data ze všech podporovaných zdrojů úložišť dat k databázi Oracle. Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor Oracle podporuje následující verze databáze Oracle. Podporuje také ověřování Basic nebo identifikátor objektu:

- R1 Oracle 12c (12.1)
- Oracle 11g R1, R2 (11.1, 11.2)
- Oracle 10g R1, R2 (10,1, 10.2)
- Oracle 9i R1, R2 (9.0.1, 9.2)
- Oracle 8i R3 (8.1.7)

> [!Note]
> Nepodporuje proxy serveru Oracle.

## <a name="prerequisites"></a>Požadavky

Ke kopírování dat z databáze Oracle, který není veřejně přístupná, budete muset nastavit modul Integration Runtime. Další informace o modulu integration runtime najdete v tématu [modul Integration Runtime](create-self-hosted-integration-runtime.md). Prostředí integration runtime poskytuje integrované ovladače Oracle. Proto není nutné ručně nainstalovat ovladač při kopírování dat z a do databáze Oracle.

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní konektor Oracle.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Následující vlastnosti jsou podporovány pro Oracle propojenou službu.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na **Oracle**. | Ano |
| připojovací řetězec | Určuje informace potřebné pro připojení k instanci databáze Oracle. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md).<br><br>**Podporovaný typ připojení**: můžete použít **Oracle SID** nebo **název služby Oracle** k identifikaci vaší databáze:<br>– Pokud používáte SID: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>– Pokud používáte název služby: `Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;` | Ano |
| connectVia | [Prostředí integration runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Můžete použít modul Integration Runtime nebo prostředí Azure Integration Runtime (Pokud vaše úložiště dat je veřejně dostupná). Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

>[!TIP]
>Pokud dosáhnete o tom, že chyba "ORA-01025: UPI parametr je mimo rozsah" a systém Oracle je verze 8i, přidejte `WireProtocolMode=1` připojovací řetězec a zkuste to znovu.

**Příklad:**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. Tato část obsahuje seznam vlastností, které podporuje datové sady Oracle.

Pro kopírování dat z a do databáze Oracle, nastavte vlastnost typ datové sady na **OracleTable**. Jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady, musí být nastavená na **OracleTable**. | Ano |
| tableName |Název tabulky v databázi Oracle, který odkazuje propojenou službu. | Ano |

**Příklad:**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností, které podporuje Oracle zdroje a jímky.

### <a name="oracle-as-a-source-type"></a>Oracle jako typ zdroje

Ke zkopírování dat z Oracle, nastavte typ zdroje v aktivitě kopírování do **OracleSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** oddílu.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavená na **OracleSource**. | Ano |
| oracleReaderQuery | Použijte vlastní dotaz SQL číst data. Příklad: `"SELECT * FROM MyTable"`. | Ne |

Pokud nezadáte "oracleReaderQuery", sloupce definované v oddílu "struktura" datové sady se používají k vytvoření dotazu (`select column1, column2 from mytable`) ke spuštění databáze Oracle. Pokud není definice datové sady "struktura", jsou vybrány všechny sloupce z tabulky.

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle input dataset name>",
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
                "type": "OracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="oracle-as-a-sink-type"></a>Oracle jako typ jímky

Pro kopírování dat do databáze Oracle, nastavte typ jímky v aktivitě kopírování do **třídě OracleSink**. Následující vlastnosti jsou podporovány v aktivitě kopírování **jímky** oddílu.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Nastavte vlastnost typ jímky aktivity kopírování **třídě OracleSink**. | Ano |
| writeBatchSize | Vloží data do tabulky SQL writeBatchSize dosáhne velikosti vyrovnávací paměti.<br/>Povolené hodnoty jsou Integer (počet řádků). |Ne (výchozí hodnota je 10 000) |
| writeBatchTimeout | Čekací doba pro dávkové operace insert dokončit před vypršením časového limitu.<br/>Povolené jsou hodnoty Timespan. Příkladem je 00:30:00 (30 minut). | Ne |
| preCopyScript | Zadejte dotaz SQL pro aktivitu kopírování ke spuštění před zápisu dat do Oracle při každém spuštění. Tuto vlastnost můžete použít k vyčištění dat předem. | Ne |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyToOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Oracle output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OracleSink"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-oracle"></a>Mapování pro Oracle datového typu

Při kopírování dat z a do databáze Oracle, se používají následující mapování z datových typů Oracle do služby Data Factory dočasné datových typů. Další informace o jak aktivita kopírování mapuje typ zdroje schéma a data jímky, najdete v článku [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md).

| Oracle datového typu | Data Factory dočasné datový typ |
|:--- |:--- |
| BFILE |Byte] |
| OBJEKT BLOB |Byte]<br/>(podporováno pouze pro Oracle 10g a vyšší) |
| CHAR |Řetězec |
| DATOVÝ TYP CLOB |Řetězec |
| DATE (Datum) |DateTime |
| PLOVOUCÍ DESETINNOU ČÁRKOU |Desetinné číslo, řetězec (Pokud přesnost > 28) |
| CELÉ ČÍSLO |Desetinné číslo, řetězec (Pokud přesnost > 28) |
| LONG |Řetězec |
| DLOUHO NEZPRACOVANÉ |Byte] |
| NCHAR |Řetězec |
| NCLOB |Řetězec |
| ČÍSLO |Desetinné číslo, řetězec (Pokud přesnost > 28) |
| NVARCHAR2 |Řetězec |
| NEZPRACOVANÉ |Byte] |
| ID ŘÁDKU |Řetězec |
| ČASOVÉ RAZÍTKO |DateTime |
| ČASOVÉ RAZÍTKO S MÍSTNÍM ČASOVÉM PÁSMU |Řetězec |
| ČASOVÉ RAZÍTKO S ČASOVÝM PÁSMEM |Řetězec |
| CELÉ ČÍSLO BEZ ZNAMÉNKA |Číslo |
| VARCHAR2 |Řetězec |
| XML |Řetězec |

> [!NOTE]
> Za druhé datové typy na INTERVALU roku a měsíce a dne do INTERVALU nejsou podporovány.


## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
