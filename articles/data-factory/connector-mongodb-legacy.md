---
title: Kopírování dat z MongoDB pomocí Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data z Mongo DB do úložišť dat podporovaných jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: 1ffd1b96e721707f69c47a7cbf11d60f17f3a7d2
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54105419"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Kopírování dat z MongoDB pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](v1/data-factory-on-premises-mongodb-connector.md)
> * [Aktuální verze](connector-mongodb.md)

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory ke zkopírování dat z databáze MongoDB. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

>[!IMPORTANT]
>Verze ADF nový konektor MongoDB, která poskytuje lepší MongoDB nativní podporu porovnává s touto implementací na základě rozhraní ODBC, přečtěte si [konektoru MongoDB](connector-mongodb.md) článek věnovaný tomu podrobnosti. Tato starší verze konektoru MongoDB, zůstane podporované jako-je zpětné kompatibility, zatímco pro jakékoli nové úlohy použijte nový konektor.

## <a name="supported-capabilities"></a>Podporované funkce

Kopírování dat z databáze MongoDB do jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor MongoDB podporuje:

- MongoDB **verze 2.4, 2.6, 3.0, 3.2, 3.4 a 3.6**.
- Kopírování dat pomocí **základní** nebo **anonymní** ověřování.

## <a name="prerequisites"></a>Požadavky

Ke zkopírování dat z databáze MongoDB, která není veřejně přístupná, budete muset nastavit modul Integration Runtime. Zobrazit [modul Integration Runtime](create-self-hosted-integration-runtime.md) článku se dozvíte podrobnosti. Prostředí Integration Runtime poskytuje integrované ovladač MongoDB, proto není nutné ručně nainstalovat všechny ovladače při kopírování dat z MongoDB.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní konektoru MongoDB.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro MongoDB propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type |Vlastnost type musí být nastavená na: **MongoDb** |Ano |
| server |IP adresa nebo název hostitele serveru MongoDB. |Ano |
| port |Port TCP, který MongoDB server používá k naslouchání pro připojení klientů. |Ne (výchozí hodnota je 27017) |
| databaseName |Název databáze MongoDB, které chcete získat přístup. |Ano |
| authenticationType. | Typ ověřování používaný pro připojení k databázi MongoDB.<br/>Povolené hodnoty jsou: **Základní**, a **anonymní**. |Ano |
| uživatelské jméno |Uživatelský účet pro přístup k MongoDB. |Ano (Pokud se používá základní ověřování). |
| heslo |Heslo pro tohoto uživatele. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). |Ano (Pokud se používá základní ověřování). |
| authSource |Název databáze MongoDB, kterou chcete použít ke kontrole přihlašovacích údajů pro ověřování. |Ne. Pro základní ověřování výchozí hodnota je používat účet správce a databáze určená vlastnost databaseName. |
| enableSsl | Určuje, zda jsou šifrované připojení k serveru pomocí SSL. Výchozí hodnota je false.  | Ne |
| allowSelfSignedServerCert | Určuje, jestli se má povolit certifikáty podepsané svým držitelem ze serveru. Výchozí hodnota je false.  | Ne |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Můžete použít modul Integration Runtime nebo prostředí Azure Integration Runtime (Pokud vaše úložiště dat je veřejně dostupná). Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, naleznete v tématu [datové sady a propojené služby](concepts-datasets-linked-services.md). Následující vlastnosti jsou podporovány pro datovou sadu MongoDB:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady, musí být nastavená na: **MongoDbCollection** | Ano |
| collectionName |Název kolekce v databázi MongoDB. |Ano |

**Příklad:**

```json
{
     "name":  "MongoDbDataset",
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností podporovaných zdrojem MongoDB.

### <a name="mongodb-as-source"></a>MongoDB jako zdroj

Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavená na: **MongoDbSource** | Ano |
| query |Použijte vlastní dotaz SQL 92 číst data. Příklad: vybrat * z MyTable. |Ne (když je "collectionName" v datové sadě zadán) |

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
> Když zadejte příkaz jazyka SQL, věnujte pozornost tomu, formát data a času. Příklad: `SELECT * FROM Account WHERE LastModifiedDate >= '2018-06-01' AND LastModifiedDate < '2018-06-02'` nebo použijte parametr `SELECT * FROM Account WHERE LastModifiedDate >= '@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}' AND LastModifiedDate < '@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'`

## <a name="schema-by-data-factory"></a>Schéma službou Data Factory

Služba Azure Data Factory odvodí schéma z kolekce MongoDB pomocí **nejnovějších 100 dokumentů** v kolekci. Pokud tyto dokumenty 100 neobsahují úplného schématu, mohou být některé sloupce ignorovány během operace kopírování.

## <a name="data-type-mapping-for-mongodb"></a>Datový typ mapování pro MongoDB

Při kopírování dat z MongoDB, se používají následující mapování z MongoDB datových typů na Azure Data Factory dočasné datové typy. Zobrazit [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) Další informace o způsobu, jakým aktivitu kopírování, která mapuje typ zdroje schéma a data na jímce.

| Datový typ MongoDB | Data factory dočasné datový typ |
|:--- |:--- |
| Binární hodnota |Byte] |
| Logická hodnota |Logická hodnota |
| Datum |DateTime |
| NumberDouble |Double |
| NumberInt |Datový typ Int32 |
| NumberLong |Int64 |
| ObjectID |Řetězec |
| Řetězec |Řetězec |
| IDENTIFIKÁTOR UUID |Guid |
| Objekt |Renormalized do sloučit sloupce s "_" jako vnořené oddělovač |

> [!NOTE]
> Další informace o podpoře pro pole pomocí virtuální tabulky, najdete v tématu [podporu pro komplexní typy s použitím virtuální tabulky](#support-for-complex-types-using-virtual-tables) oddílu.
>
> V současné době nejsou podporovány následující typy dat MongoDB: DBPointer, JavaScript, Max nebo Min klíč regulárního výrazu, symbolů, časové razítko, Nedefinováno.

## <a name="support-for-complex-types-using-virtual-tables"></a>Podpora pro komplexní typy pomocí virtuální tabulky

Azure Data Factory využívá integrované ovladače ODBC pro připojení k a kopírování dat z databáze MongoDB. U komplexních typů jako je například pole nebo objekty s různými typy mezi dokumenty ovladač znovu sjednotí data na odpovídající virtuální tabulky. Konkrétně Pokud tabulka obsahuje tyto sloupce, ovladač generuje následující virtuální tabulky:

* A **základní tabulka**, která obsahuje stejná data jako skutečné tabulky s výjimkou sloupců komplexního typu. Základní tabulka používá stejný název jako skutečné tabulky, který představuje.
* A **virtuální tabulky** pro každý sloupec komplexní typ, který rozbalí vnořené data. Virtuální tabulky jsou pojmenovány pomocí názvu tabulky skutečných, oddělovač "_" a název pole nebo objekt.

Virtuální tabulky odkazují na data v tabulce skutečné, povolení ovladače pro přístup k Nenormalizovaná data. Obsah pole MongoDB můžou používat tak dotazování a připojení k virtuální tabulky.

### <a name="example"></a>Příklad:

Například ExampleTable tady je tabulka MongoDB, která má jeden sloupec s polem objektů v každé buňce – faktury a jeden sloupec s polem skalárních typů – hodnocení.

| ID _ovládacího | Jméno zákazníka | Faktury | Úroveň služby | Hodnocení |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id: "123", položka: "toaster"; price: "456", slevy: "0.2"}, {invoice_id: "124", položka: "sušárně"; price: slevy "1235": "0.2"}] |Stříbrný |[5,6] |
| 2222 |XYZ |[{invoice_id: položky "135": "ledničky"; price: "12543", slevy: "0,0"}] |Zlatá |[1,2] |

Ovladač vygeneruje více virtuální tabulky k reprezentaci této jediné tabulce. První virtuální tabulky je základní tabulku s názvem "ExampleTable" v příkladu. Základní tabulka obsahuje všechna data v původní tabulce, ale data z pole byl vynechán a rozbalen virtuální tabulky.

| ID _ovládacího | Jméno zákazníka | Úroveň služby |
| --- | --- | --- |
| 1111 |ABC |Stříbrný |
| 2222 |XYZ |Zlatá |

Následující tabulky popisují virtuální tabulky, které představují původní pole v příkladu. Tyto tabulky obsahují následující:

* Představuje odkaz zpět na původní sloupec primárního klíče odpovídající řádek původní pole (přes sloupec ID _ovládacího)
* Údaj o umístění dat v rámci původní pole
* Rozšířená data pro každý prvek v poli

**Tabulka "ExampleTable_Invoices":**

| ID _ovládacího | ExampleTable_Invoices_dim1_idx | invoice_id | Položka | price | Sleva |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |Toaster |456 |0.2 |
| 1111 |1 |124 |sušárně |1235 |0.2 |
| 2222 |0 |135 |ledničky |12543 |0.0 |

**Tabulka "ExampleTable_Ratings":**

| ID _ovládacího | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |


## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
