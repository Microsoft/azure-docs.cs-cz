---
title: Kopírování dat z DB2 pomocí Azure Data Factory
description: Naučte se, jak kopírovat data z DB2 do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: jingwang
ms.openlocfilehash: 642f12386a7695e026eb0c30016acf6f53fc9e95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100381116"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Kopírování dat z DB2 pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-onprem-db2-connector.md)
> * [Aktuální verze](connector-db2.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z databáze DB2. Sestaví se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Tento databázový konektor DB2 se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)

Data z databáze DB2 můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která jsou v rámci aktivity kopírování podporovaná jako zdroje a jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Konkrétně tento konektor DB2 podporuje následující platformy a verze IBM DB2 s architekturou Distributed relačních databází (DRDA) SQL Access Manager (SQLAM) verze 9, 10 a 11.  Využívá protokol DDM/DRDA.

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
>Konektor DB2 je postaven nad Zprostředkovatel Microsoft OLE DB pro DB2. Pokud chcete řešit chyby konektoru DB2, přečtěte si [Zprostředkovatel dat kódy chyb](/host-integration-server/db2oledbv/data-provider-error-codes#drda-protocol-errors).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Integration Runtime poskytuje integrovaný ovladač DB2, takže při kopírování dat z DB2 nemusíte ručně instalovat žádné ovladače.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které se používají k definování Data Factory entit specifických pro konektor DB2.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu DB2 jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type musí být nastavená na: **Db2** . | Yes |
| připojovací řetězec | Zadejte informace potřebné pro připojení k instanci DB2.<br/> Můžete také do Azure Key Vault umístit heslo a načíst konfiguraci z `password` připojovacího řetězce. Další podrobnosti najdete v následujících ukázkách a [přihlašovací údaje úložiště v Azure Key Vault](store-credentials-in-key-vault.md) článku. | Yes |
| connectVia | [Integration runtime](concepts-integration-runtime.md) , která se má použít pro připojení k úložišti dat Další informace najdete v části [požadavky](#prerequisites) . Pokud není zadaný, použije se výchozí Azure Integration Runtime. |No |

Typické vlastnosti v připojovacím řetězci:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| server |Název serveru DB2. Můžete zadat číslo portu za názvem serveru oddělené dvojtečkou, např. `server:port` .<br>Konektor DB2 využívá protokol DDM/DRDA a ve výchozím nastavení používá port 50000, pokud není zadaný. Port, který vaše konkrétní databáze DB2 používá, se může lišit v závislosti na verzi a nastavení, např. pro DB2 LUW výchozí port je 50000, pro AS400 výchozí port je 446 nebo 448, pokud je povolený protokol TLS. V následujících dokumentech DB2 najdete informace o tom, jak je port nakonfigurovaný obvykle: [DB2 z/OS](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.5.0/com.ibm.db2.luw.qb.dbconn.doc/doc/t0008229.html), [DB2 ISERIES](https://www.ibm.com/support/knowledgecenter/ssw_ibm_i_74/ddp/rbal1ports.htm)a [DB2 LUW](https://www.ibm.com/support/knowledgecenter/en/SSEKCU_1.1.3.0/com.ibm.psc.doc/install/psc_t_install_typical_db2_port.html). |Yes |
| database |Název databáze DB2 |Yes |
| authenticationType |Typ ověřování, který se používá pro připojení k databázi DB2.<br/>Povolená hodnota je: **Basic**. |Yes |
| username |Zadejte uživatelské jméno pro připojení k databázi DB2. |Yes |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| balíček balíčkucollection    | Určete, kde jsou potřebné balíčky automaticky vytvořeny pomocí ADF při dotazování databáze. Pokud tato hodnota není nastavená, používá Data Factory jako výchozí hodnotu {username}. | No |
| certificateCommonName | Pokud používáte šifrování SSL (Secure Sockets Layer) (SSL) nebo TLS (Transport Layer Security), musíte zadat hodnotu pro běžný název certifikátu. | No |

> [!TIP]
> Pokud se zobrazí chybová zpráva oznamující, že je `The package corresponding to an SQL statement execution request was not found. SQLSTATE=51002 SQLCODE=-805` důvod potřebný balíček pro uživatele nevytvoří. Ve výchozím nastavení se ADF pokusí vytvořit balíček v kolekci s názvem jako uživatel, kterého jste použili pro připojení k DB2. Určete vlastnost kolekce balíčku, která bude označovat, kde má ADF při dotazování na databázi vytvořit potřebné balíčky.

**Příklad:**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "connectionString": "server=<server:port>;database=<database>;authenticationType=Basic;username=<username>;password=<password>;packageCollection=<packagecollection>;certificateCommonName=<certname>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```
**Příklad: uložení hesla v Azure Key Vault**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "connectionString": "server=<server:port>;database=<database>;authenticationType=Basic;username=<username>;packageCollection=<packagecollection>;certificateCommonName=<certname>;",
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

Pokud jste používali propojenou službu DB2 s následující datovou částí, je stále podporovaná tak, jak je, a až budete chtít začít používat nové.

**Předchozí datová část:**

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

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type datové sady musí být nastavená na: **Db2Table** . | Yes |
| schema | Název schématu. |Ne (Pokud je zadáno "dotaz" ve zdroji aktivity)  |
| tabulka | Název tabulky |Ne (Pokud je zadáno "dotaz" ve zdroji aktivity)  |
| tableName | Název tabulky se schématem Tato vlastnost je podporována z důvodu zpětné kompatibility. `schema` `table` Pro nové zatížení použijte a. | Ne (Pokud je zadáno "dotaz" ve zdroji aktivity) |

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

Pokud jste používali `RelationalTable` typovou datovou sadu, je stále podporovaná tak, jak je, a až budete chtít začít používat novinku dál.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných zdrojem DB2.

### <a name="db2-as-source"></a>DB2 jako zdroj

Chcete-li kopírovat data z DB2, v části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type zdroje aktivity kopírování musí být nastavená na: **Db2Source** . | Yes |
| query | Pro čtení dat použijte vlastní dotaz SQL. Příklad: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`. | Ne (Pokud je zadáno "tableName" v datové sadě |

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

Pokud jste používali `RelationalSource` typový zdroj, je stále podporován tak, jak je, a když jste navrhli začít používat nový.

## <a name="data-type-mapping-for-db2"></a>Mapování datových typů pro DB2

Při kopírování dat z DB2 se z datových typů DB2 používají následující mapování pro Azure Data Factory dočasných datových typů. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ do jímky, najdete v tématu [mapování typů schématu a dat](copy-activity-schema-and-type-mapping.md) .

| Typ databáze DB2 | Typ dat interim Data Factory |
|:--- |:--- |
| BigInt |Int64 |
| Binární |Byte [] |
| Objekt blob |Byte [] |
| Char |Řetězec |
| Datový typ CLOB |Řetězec |
| Date (Datum) |Datum a čas |
| DB2DynArray |Řetězec |
| DbClob |Řetězec |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| dvojité |dvojité |
| Float |dvojité |
| Objekty |Řetězec |
| Integer |Int32 |
| LongVarBinary |Byte [] |
| LongVarChar |Řetězec |
| LongVarGraphic |Řetězec |
| Číselný |Decimal |
| Skutečné |Jednoduché |
| SmallInt |Int16 |
| Čas |TimeSpan |
| Timestamp |DateTime |
| VarBinary |Byte [] |
| VarChar |Řetězec |
| VarGraphic |Řetězec |
| XML |Byte [] |

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).