---
title: Kopírování dat z Teradata pomocí Azure Data Factory | Microsoft Docs
description: Konektor Teradata služby Data Factory umožňuje kopírovat data z databáze Teradata do úložišť dat, která aplikace Data Factory podporuje jako jímky.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: ce326d7284e22a8734f6be671a277795ba659522
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68720526"
---
# <a name="copy-data-from-teradata-by-using-azure-data-factory"></a>Kopírování dat z Teradata pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
>
> * [Verze 1](v1/data-factory-onprem-teradata-connector.md)
> * [Aktuální verze](connector-teradata.md)

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z databáze Teradata. Sestaví se na [Přehled aktivit kopírování](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Podporované funkce

Data z databáze Teradata můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor Teradata podporuje:

- Teradata **verze 14,10, 15,0, 15,10, 16,0, 16,10 a 16,20**.
- Kopírování dat pomocí **základního** ověřování nebo ověřování **systému Windows** .
- Paralelní kopírování ze zdroje Teradata. Podrobnosti najdete v části [paralelní kopírování z Teradata](#parallel-copy-from-teradata) .

> [!NOTE]
>
> Po vydání místního prostředí Integration runtime v 3.18 Azure Data Factory upgradován konektor Teradata. Všechna existující zatížení, která používají předchozí konektor Teradata, jsou stále podporována. Pro nové úlohy je ale vhodné použít nový. Všimněte si, že nová cesta vyžaduje jinou sadu propojených služeb, datových sad a zdrojů kopírování. Podrobnosti o konfiguraci najdete v příslušných oddílech.

## <a name="prerequisites"></a>Požadavky

Pokud vaše Teradata není veřejně přístupný, musíte nastavit [prostředí Integration runtime](create-self-hosted-integration-runtime.md)v místním prostředí. Modul runtime integrace poskytuje integrovaný ovladač Teradata od verze 3,18. Nemusíte ručně instalovat žádný ovladač. Ovladač vyžaduje "Visual C++ Redistributable 2012 Update 4" na počítači místního prostředí Integration runtime. Pokud ho ještě nemáte nainstalovanou, Stáhněte si ho odsud [.](https://www.microsoft.com/en-sg/download/details.aspx?id=30679)

Pro všechny verze prostředí Integration runtime v místním prostředí, která je starší než 3,18, nainstalujte na počítači prostředí Integration runtime [rozhraní .net zprostředkovatel dat pro Teradata](https://go.microsoft.com/fwlink/?LinkId=278886), verze 14 nebo novější. 

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factory entit specifických pro konektor Teradata.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Propojená služba Teradata podporuje následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavena na **Teradata**. | Ano |
| connectionString | Určuje informace potřebné pro připojení k instanci databáze Teradata. Přečtěte si následující ukázky.<br/>Můžete také vložit heslo do Azure Key Vault a z připojovacího řetězce si `password` vyžádat konfiguraci. Další podrobnosti najdete [v tématu uložení přihlašovacích údajů v Azure Key Vault](store-credentials-in-key-vault.md) . | Ano |
| username | Zadejte uživatelské jméno pro připojení k databázi Teradata. Platí při použití ověřování systému Windows. | Ne |
| password | Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Můžete také zvolit odkaz na [tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). <br>Platí při použití ověřování systému Windows nebo odkazování na heslo v Key Vault pro základní ověřování. | Ne |
| connectVia | [Prostředí integration runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Vyžaduje se místní prostředí Integration runtime, jak je uvedeno v [požadavcích](#prerequisites). |Ano |

**Příklad použití základního ověřování**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Příklad použití ověřování systému Windows**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>",
            "username": "<username>",
            "password": "<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

> [!NOTE]
>
> Následující datová část je stále podporována. Ale dál byste měli použít nový.

**Předchozí datová část:**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<Basic/Windows>",
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

V této části najdete seznam vlastností podporovaných datovou sadou Teradata. Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v tématu [datové sady](concepts-datasets-linked-services.md).

Chcete-li kopírovat data z Teradata, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type datové sady musí být nastavena na `TeradataTable`hodnotu. | Ano |
| database | Název databáze Teradata. | Ne (když je zadán zdroj aktivity "dotaz") |
| table | Název tabulky v databázi Teradata. | Ne (když je zadán zdroj aktivity "dotaz") |

**Příklad:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "typeProperties": {},
        "schema": [],        
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

> [!NOTE]
>
> `RelationalTable`typ DataSet je stále podporován. Doporučujeme však použít novou datovou sadu.

**Předchozí datová část:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

V této části najdete seznam vlastností podporovaných zdrojem Teradata. Úplný seznam oddílů a vlastností dostupných pro definování aktivit najdete v tématu [kanály](concepts-pipelines-activities.md). 

### <a name="teradata-as-a-source-type"></a>Teradata jako typ zdroje

> [!TIP]
>
> Chcete-li načíst data z Teradata efektivně pomocí dělení dat, přečtěte si část [paralelní kopírování z Teradata](#parallel-copy-from-teradata) .

Chcete-li kopírovat data z Teradata, v části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type zdroje aktivity kopírování musí být nastavena na `TeradataSource`hodnotu. | Ano |
| query | Použijte vlastní dotaz SQL číst data. Příklad: `"SELECT * FROM MyTable"`.<br>Pokud povolíte rozdělené zatížení, musíte v dotazu připojit všechny odpovídající předdefinované parametry oddílu. Příklady najdete v části [paralelní kopírování z Teradata](#parallel-copy-from-teradata) . | Ne (Pokud je zadaná tabulka v datové sadě) |
| partitionOptions | Určuje možnosti dělení dat používané při načítání dat z Teradata. <br>Povolené hodnoty jsou: **Žádné** (výchozí), **hash** a **DynamicRange**.<br>Pokud je povolená možnost oddílu (tj. ne `None`), [`parallelCopies`](copy-activity-performance.md#parallel-copy) nakonfigurujte také nastavení aktivity kopírování. To určuje souběžný stupeň pro souběžné načítání dat z databáze Teradata. Můžete například nastavit hodnotu 4. | Ne |
| partitionSettings | Určete skupinu nastavení pro dělení dat. <br>Použijte, pokud není `None`možnost oddílu. | Ne |
| partitionColumnName | Zadejte název zdrojového sloupce **v typu Integer** , který bude použit pro vytváření oddílů rozsahu pro paralelní kopírování. Pokud není zadaný, primární klíč tabulky se automaticky zjistí a použije se jako sloupec partition. <br>Použijte, pokud je `Hash` možnost oddílu nebo. `DynamicRange` Použijete-li dotaz k načtení zdrojových dat, vidlice `?AdfHashPartitionCondition` nebo `?AdfRangePartitionColumnName` klauzule WHERE. Viz příklad v části [paralelní kopírování z Teradata](#parallel-copy-from-teradata) . | Ne |
| partitionUpperBound | Maximální hodnota sloupce oddílu pro kopírování dat. <br>Použijte, pokud je `DynamicRange`možnost oddílu. Použijete-li dotaz k načtení zdrojových dat, `?AdfRangePartitionUpbound` zapojte v klauzuli WHERE. Příklad najdete v části [paralelní kopírování z Teradata](#parallel-copy-from-teradata) . | Ne |
| PartitionLowerBound | Minimální hodnota sloupce oddílu pro kopírování dat. <br>Použijte, pokud je `DynamicRange`parametr partition. Použijete-li dotaz k načtení zdrojových dat, `?AdfRangePartitionLowbound` zapojte v klauzuli WHERE. Příklad najdete v části [paralelní kopírování z Teradata](#parallel-copy-from-teradata) . | Ne |

> [!NOTE]
>
> `RelationalSource`typ kopie zdroje je stále podporován, ale nepodporuje nové integrované paralelní zatížení z Teradata (možnosti oddílu). Doporučujeme však použít novou datovou sadu.

**Příklad: kopírování dat pomocí základního dotazu bez oddílu**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
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
                "type": "TeradataSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-teradata"></a>Paralelní kopírování z Teradata

Konektor Data Factory Teradata poskytuje integrované datové oddíly pro kopírování dat z Teradata paralelně. Možnosti dělení dat najdete ve **zdrojové** tabulce aktivity kopírování.

![Snímek obrazovky s možnostmi oddílů](./media/connector-teradata/connector-teradata-partition-options.png)

Pokud povolíte dělenou kopii, Data Factory spustí paralelní dotazy na zdroj Teradata pro načtení dat podle oddílů. Paralelní míra je řízena [`parallelCopies`](copy-activity-performance.md#parallel-copy) nastavením aktivity kopírování. Pokud jste například nastavili `parallelCopies` na čtyři, Data Factory souběžně generuje a spustí čtyři dotazy na základě zadané možnosti oddílu a nastavení. Každý dotaz načte část dat z databáze Teradata.

Je vhodné povolit paralelní kopírování pomocí dělení dat, zejména při načítání velkého množství dat z databáze Teradata. Následují Doporučené konfigurace pro různé scénáře:

| Scénář                                                     | Navrhovaná nastavení                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Úplné načtení z velké tabulky                                   | **Možnost oddílu**: Kontrole. <br><br/>Během provádění Data Factory automaticky detekuje sloupec PK, aplikuje na něj hodnotu hash a kopíruje data podle oddílů. |
| Načtení velkého množství dat pomocí vlastního dotazu.                 | **Možnost oddílu**: Kontrole.<br>**Dotaz**: `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`.<br>**Sloupec oddílu**: Zadejte sloupec použitý pro oddíl použití algoritmu hash. Pokud není zadán, Data Factory automaticky detekuje sloupec PK tabulky, kterou jste zadali v datové sadě Teradata.<br><br>Během provádění se data Factory nahradí `?AdfHashPartitionCondition` logikou oddílu hash a pošle se do Teradata. |
| Načtěte velké množství dat pomocí vlastního dotazu, který má sloupec s celými čísly s rovnoměrně distribuovanou hodnotou pro dělení rozsahu. | **Možnosti oddílu**: Dynamický oddíl rozsahu.<br>**Dotaz**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Sloupec oddílu**: Zadejte sloupec, který se používá k dělení dat. Můžete rozdělit na sloupec s datovým typem Integer.<br>**Horní mez oddílu** a **dolní mez oddílu**: Určete, jestli chcete filtrovat podle sloupce oddílu, aby se načetla data jenom mezi dolním a horním rozsahem.<br><br>Během provádění Data Factory nahradí `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound`a `?AdfRangePartitionLowbound` skutečným názvem sloupce a rozsahy hodnoty pro každý oddíl a posílá do Teradata. <br>Pokud například sloupec oddílu "ID" nastaví s dolní hranicí jako 1 a horní mez jako 80, s paralelní kopií nastavenou na 4, Data Factory načte data po 4 oddíly. Jejich ID jsou mezi [1, 20], [21, 40], [41, 60] a [61, 80] v uvedeném pořadí. |

**Příklad: dotazování pomocí oddílu hash**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "Hash",
    "partitionSettings": {
        "partitionColumnName": "<hash_partition_column_name>"
    }
}
```

**Příklad: dotaz s dynamickým oddílem rozsahu**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-teradata"></a>Mapování datových typů pro Teradata

Při kopírování dat z Teradata platí následující mapování. Další informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ k jímky, najdete v tématu [mapování schémat a datových typů](copy-activity-schema-and-type-mapping.md).

| Datový typ Teradata | Data Factory dočasné datový typ |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| Char |Řetězec |
| Datový typ CLOB |Řetězec |
| Date |Datetime |
| Decimal |Decimal |
| Double |Double |
| Graphic |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| Integer |Int32 |
| Interval Day |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| Interval Day To Hour |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| Interval Day To Minute |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| Interval Day To Second |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| Hodina intervalu |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| Interval Hour To Minute |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| Interval Hour To Second |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| Interval Minute |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| Interval Minute To Second |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| Interval Month |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| Interval Second |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| Interval Year |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| Interval Year To Month |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| Number |Double |
| Tečka (datum) |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| Tečka (čas) |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| Období (čas s časovým pásmem) |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| Tečka (časové razítko) |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| Tečka (časové razítko s časovým pásmem) |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| SmallInt |Int16 |
| Time |TimeSpan |
| Time With Time Zone |TimeSpan |
| Timestamp |Datetime |
| Timestamp With Time Zone |Datetime |
| VarByte |Byte[] |
| VarChar |Řetězec |
| VarGraphic |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| Xml |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |


## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
