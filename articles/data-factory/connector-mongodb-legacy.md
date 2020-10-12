---
title: Kopírování dat z MongoDB pomocí starší verze
description: Naučte se, jak kopírovat data z databáze Mongo DB do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu staršího Azure Data Factory.
services: data-factory
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 08/12/2019
ms.openlocfilehash: 7cf4be078a7bee0bedbeac4326acb9ca290cde88
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91331977"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory-legacy"></a>Kopírování dat z MongoDB pomocí Azure Data Factory (starší verze)

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-on-premises-mongodb-connector.md)
> * [Aktuální verze](connector-mongodb.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z databáze MongoDB. Sestaví se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který představuje obecný přehled aktivity kopírování.

>[!IMPORTANT]
>Vydání ADF nový konektor MongoDB, který poskytuje lepší nativní podporu MongoDB, porovnává s touto implementací založenou na rozhraní ODBC, najdete v článku o [konektoru](connector-mongodb.md) . Tato starší verze konektoru MongoDB je podporovaná tak, jak je kvůli zpětné shodě, ale u jakékoli nové úlohy prosím použijte nový konektor.

## <a name="supported-capabilities"></a>Podporované možnosti

Data z databáze MongoDB můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která jsou v rámci aktivity kopírování podporovaná jako zdroje a jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Konkrétně tento konektor MongoDB podporuje:

- MongoDB **verze 2,4, 2,6, 3,0, 3,2, 3,4 a 3,6**.
- Kopírování dat pomocí **základního** nebo **anonymního** ověřování.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Integration Runtime poskytuje integrovaný ovladač MongoDB, takže při kopírování dat z MongoDB nemusíte ručně instalovat žádné ovladače.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factory entit specifických pro konektor MongoDB.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu MongoDB jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ |Vlastnost Type musí být nastavená na: **MongoDB** . |Yes |
| server |IP adresa nebo název hostitele serveru MongoDB |Yes |
| port |Port TCP, který server MongoDB používá k naslouchání klientským připojením. |Ne (výchozí hodnota je 27017) |
| Databáze |Název databáze MongoDB, ke které chcete získat přístup. |Yes |
| authenticationType | Typ ověřování, který se používá pro připojení k databázi MongoDB.<br/>Povolené hodnoty jsou: **Basic**a **Anonymous**. |Yes |
| username |Uživatelský účet pro přístup k MongoDB. |Ano (Pokud se používá základní ověřování). |
| heslo |Heslo pro tohoto uživatele. Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). |Ano (Pokud se používá základní ověřování). |
| authSource |Název databáze MongoDB, kterou chcete použít ke kontrole vašich přihlašovacích údajů pro ověřování. |Ne. Pro základní ověřování se ve výchozím nastavení používá účet správce a databáze určená pomocí vlastnosti databaseName. |
| enableSsl | Určuje, jestli se připojení k serveru šifrují pomocí protokolu TLS. Výchozí hodnota je False.  | No |
| allowSelfSignedServerCert | Určuje, jestli se mají na serveru udělit certifikáty podepsané svým držitelem. Výchozí hodnota je False.  | No |
| connectVia | [Integration runtime](concepts-integration-runtime.md) , která se má použít pro připojení k úložišti dat Další informace najdete v části [požadavky](#prerequisites) . Pokud není zadaný, použije se výchozí Azure Integration Runtime. |No |

**Příklad:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDb",
        "typeProperties": {
            "server": "<server name>",
            "databaseName": "<database name>",
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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, najdete v tématu [datové sady a propojené služby](concepts-datasets-linked-services.md). Pro datovou sadu MongoDB jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type datové sady musí být nastavená na: **MongoDbCollection** . | Yes |
| collectionName |Název kolekce v databázi MongoDB |Yes |

**Příklad:**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<Collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných MongoDB zdrojem.

### <a name="mongodb-as-source"></a>MongoDB as source

V části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type zdroje aktivity kopírování musí být nastavená na: **MongoDbSource** . | Yes |
| query |Pro čtení dat použijte vlastní dotaz SQL-92. Příklad: SELECT * FROM MyTable. |Ne (Pokud je zadaná položka "CollectionName" v sadě dat.) |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
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
                "type": "MongoDbSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

> [!TIP]
> Při zadávání dotazu SQL věnujte pozornost formátu data a času. Například: `SELECT * FROM Account WHERE LastModifiedDate >= '2018-06-01' AND LastModifiedDate < '2018-06-02'` nebo pro použití parametru `SELECT * FROM Account WHERE LastModifiedDate >= '@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}' AND LastModifiedDate < '@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'`

## <a name="schema-by-data-factory"></a>Schéma podle Data Factory

Služba Azure Data Factory odvodí schéma z kolekce MongoDB pomocí **nejnovějších 100 dokumentů** v kolekci. Pokud tyto dokumenty 100 neobsahují úplné schéma, mohou být některé sloupce během operace kopírování ignorovány.

## <a name="data-type-mapping-for-mongodb"></a>Mapování datových typů pro MongoDB

Při kopírování dat z MongoDB se v datových typech MongoDB používají následující mapování k Azure Data Factory dočasných datových typů. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ do jímky, najdete v tématu [mapování typů schématu a dat](copy-activity-schema-and-type-mapping.md) .

| Datový typ MongoDB | Typ dat interim Data Factory |
|:--- |:--- |
| Binární |Byte [] |
| Logická hodnota |Logická hodnota |
| Datum |DateTime |
| NumberDouble |dvojité |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectId |Řetězec |
| Řetězec |Řetězec |
| IDENTIFIKÁTOR |Identifikátor GUID |
| Objekt |Znovu normalizovat do sloučených sloupců pomocí _ jako vnořeného oddělovače |

> [!NOTE]
> Další informace o podpoře pro pole pomocí virtuálních tabulek najdete v části [Podpora komplexních typů pomocí virtuálních tabulek](#support-for-complex-types-using-virtual-tables) .
>
> V současné době nejsou podporovány následující datové typy MongoDB: DBPointer, JavaScript, Max/min Key, regulární výraz, symbol, časové razítko, Nedefinováno.

## <a name="support-for-complex-types-using-virtual-tables"></a>Podpora komplexních typů pomocí virtuálních tabulek

Azure Data Factory používá integrovaný ovladač ODBC pro připojení a zkopírování dat z databáze MongoDB. U komplexních typů, jako jsou pole nebo objekty s různými typy v rámci dokumentů, ovladač znovu normalizuje data do odpovídajících virtuálních tabulek. Konkrétně, pokud tabulka obsahuje takové sloupce, vygeneruje ovladač následující virtuální tabulky:

* **Základní tabulka**, která obsahuje stejná data jako skutečná tabulka s výjimkou sloupců se složitým typem. Základní tabulka používá stejný název jako skutečná tabulka, kterou představuje.
* **Virtuální tabulka** pro každý sloupec komplexního typu, který rozšiřuje vnořená data. Virtuální tabulky jsou pojmenovány pomocí názvu reálné tabulky, oddělovače "_" a názvu pole nebo objektu.

Virtuální tabulky odkazují na data v reálné tabulce a umožňují tak ovladači přístup k denormalizovaným datům. K obsahu polí MongoDB můžete přistupovat dotazem a připojením k virtuálním tabulkám.

### <a name="example"></a>Příklad

Například tady je tabulka MongoDB, která má jeden sloupec s polem objektů v každé buňce – faktury a jeden sloupec s polem skalárních typů – hodnocení.

| _id | Název zákazníka | Faktury | Úroveň služeb | Ratings |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id: "123", Item: "informační zpráva", Cena: "456", sleva: "0,2"}, {invoice_id: "124", položka: "sušárna", Cena: "1235", sleva: "0,2"}] |Silver |[5, 6] |
| 2222 |XYZ |[{invoice_id: "135"; Item: "nákupem ledničky"; Price: "12543"; Discount: "0,0"}] |Gold |[1, 2] |

Ovladač by vygeneroval několik virtuálních tabulek, které reprezentují tuto jedinou tabulku. První virtuální tabulka je základní tabulka s názvem "priklad Table", která je znázorněna v příkladu. Základní tabulka obsahuje všechna data původní tabulky, ale data z těchto polí byla vynechána a jsou rozbalena ve virtuálních tabulkách.

| _id | Název zákazníka | Úroveň služeb |
| --- | --- | --- |
| 1111 |ABC |Silver |
| 2222 |XYZ |Gold |

V následujících tabulkách jsou uvedeny virtuální tabulky, které představují původní pole v příkladu. Tyto tabulky obsahují následující:

* Odkaz zpátky na původní sloupec primárního klíče, který odpovídá řádku původního pole (prostřednictvím _id sloupce)
* Označení pozice dat v původním poli
* Rozšířená data pro každý prvek v poli

**Tabulka "ExampleTable_Invoices":**

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | položka | price | Discount |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |informační zpráva |456 |0,2 |
| 1111 |1 |124 |termostat |1235 |0,2 |
| 2222 |0 |135 |nákupem ledničky |12543 |0,0 |

**Tabulka "ExampleTable_Ratings":**

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
