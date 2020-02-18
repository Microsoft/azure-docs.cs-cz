---
title: Kopírování dat z DB2 pomocí Azure Data Factory
description: Naučte se, jak kopírovat data z DB2 do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: jingwang
ms.openlocfilehash: 22ecac12e049e58e533cdde0078f4a25f6bb2aa6
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2020
ms.locfileid: "77423823"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Kopírování dat z DB2 pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-onprem-db2-connector.md)
> * [Aktuální verze](connector-db2.md)

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z databáze DB2. Sestaví se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Tento databázový konektor DB2 se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)

Data z databáze DB2 můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která jsou v rámci aktivity kopírování podporovaná jako zdroje a jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Konkrétně tento konektor DB2 podporuje následující platformy a verze IBM DB2 s architekturou Distributed relačních databází (DRDA) SQL Access Manager (SQLAM) verze 9, 10 a 11:

* IBM DB2 pro z/OS 12,1
* IBM DB2 pro z/OS 11,1
* IBM DB2 pro z/OS 10,1
* IBM DB2 pro i 7,3
* IBM DB2 pro i 7,2
* IBM DB2 pro i 7,1
* IBM DB2 pro LUW 11
* IBM DB2 pro LUW 10,5
* IBM DB2 pro LUW 10,1

>[!TIP]
>Konektor DB2 je postaven nad Zprostředkovatel Microsoft OLE DB pro DB2. Pokud chcete řešit chyby konektoru DB2, přečtěte si [Zprostředkovatel dat kódy chyb](https://docs.microsoft.com/host-integration-server/db2oledbv/data-provider-error-codes#drda-protocol-errors).

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Integration Runtime poskytuje integrovaný ovladač DB2, takže při kopírování dat z DB2 nemusíte ručně instalovat žádné ovladače.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které se používají k definování Data Factory entit specifických pro konektor DB2.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu DB2 jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavená na: **Db2** . | Ano |
| server |Název serveru DB2. Můžete zadat číslo portu za názvem serveru odděleným dvojtečkou, např. `server:port`. |Ano |
| database |Název databáze DB2 |Ano |
| authenticationType |Typ ověřování, který se používá pro připojení k databázi DB2.<br/>Povolená hodnota je: **Basic**. |Ano |
| uživatelské jméno |Zadejte uživatelské jméno pro připojení k databázi DB2. |Ano |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). |Ano |
| balíček balíčkucollection | Určete, kde jsou potřebné balíčky automaticky vytvořeny pomocí ADF při dotazování databáze. | Ne |
| certificateCommonName | Pokud používáte šifrování SSL (Secure Sockets Layer) (SSL) nebo TLS (Transport Layer Security), musíte zadat hodnotu pro běžný název certifikátu. | Ne |
| connectVia | [Integration runtime](concepts-integration-runtime.md) , která se má použít pro připojení k úložišti dat Další informace najdete v části [požadavky](#prerequisites) . Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

> [!TIP]
> Pokud se zobrazí chybová zpráva s oznámením, že `The package corresponding to an SQL statement execution request was not found. SQLSTATE=51002 SQLCODE=-805`, je důvod, proč se pro tohoto uživatele nevytvoří požadovaný balíček. Ve výchozím nastavení se ADF pokusí vytvořit balíček v kolekci s názvem jako uživatel, kterého jste použili pro připojení k DB2. Určete vlastnost kolekce balíčku, která bude označovat, kde má ADF při dotazování na databázi vytvořit potřebné balíčky.

**Příklad:**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "server": "<servername:port>",
            "database": "<dbname>",
            "authenticationType": "Basic",
            "username": "<username>",
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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných datovou sadou DB2.

Chcete-li kopírovat data z DB2, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type datové sady musí být nastavená na: **Db2Table** . | Ano |
| schéma | Název schématu. |Ne (když je zadán zdroj aktivity "query")  |
| tabulka | Název tabulky. |Ne (když je zadán zdroj aktivity "query")  |
| tableName | Název tabulky se schématem Tato vlastnost je podporována z důvodu zpětné kompatibility. Pro nové zatížení použijte `schema` a `table`. | Ne (když je zadán zdroj aktivity "query") |

**Příklad**

```json
{
    "name": "DB2Dataset",
    "properties":
    {
        "type": "Db2Table",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<DB2 linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Pokud jste používali `RelationalTable` typovou datovou sadu, je stále podporovaná tak, jak je, a až budete chtít začít používat nové.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných zdrojem DB2.

### <a name="db2-as-source"></a>DB2 jako zdroj

Chcete-li kopírovat data z DB2, v části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type zdroje aktivity kopírování musí být nastavená na: **Db2Source** . | Ano |
| query | Použijte vlastní dotaz SQL číst data. Například: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`. | Ne (když je "tableName" v datové sadě zadán) |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromDB2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<DB2 input dataset name>",
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
                "type": "Db2Source",
                "query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Pokud jste používali zdroj `RelationalSource`ho typu, je stále podporován tak, jak je, a až budete chtít začít používat nový.

## <a name="data-type-mapping-for-db2"></a>Mapování datových typů pro DB2

Při kopírování dat z DB2 se z datových typů DB2 používají následující mapování pro Azure Data Factory dočasných datových typů. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ do jímky, najdete v tématu [mapování typů schématu a dat](copy-activity-schema-and-type-mapping.md) .

| Typ databáze DB2 | Data factory dočasné datový typ |
|:--- |:--- |
| BigInt |Int64 |
| Binární hodnota |Byte[] |
| Objekt blob |Byte[] |
| Char |Řetězec |
| Datový typ CLOB |Řetězec |
| Datum |Datum a čas |
| DB2DynArray |Řetězec |
| DbClob |Řetězec |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Double |Double |
| Float |Double |
| Graphic |Řetězec |
| Integer |Datový typ Int32 |
| LongVarBinary |Byte[] |
| LongVarChar |Řetězec |
| LongVarGraphic |Řetězec |
| Numeric |Decimal |
| real |Jednoduché |
| SmallInt |Int16 |
| Čas |TimeSpan |
| Časové razítko |DateTime |
| VarBinary |Byte[] |
| VarChar |Řetězec |
| VarGraphic |Řetězec |
| XML |Byte[] |

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
