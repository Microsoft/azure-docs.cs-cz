---
title: Kopírování dat z MongoDB pomocí Azure Data Factory | Microsoft Docs
description: Zjistěte, jak ke zkopírování dat z databáze Mongo k úložištím dat. podporované podřízený pomocí aktivity kopírování v kanál služby Azure Data Factory.
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
ms.date: 06/05/2018
ms.author: jingwang
ms.openlocfilehash: 24d641247ad9bb0b5e6199952cbde9cb56fcaea7
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809285"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Kopírování dat z MongoDB pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](v1/data-factory-on-premises-mongodb-connector.md)
> * [Verze 2 – Preview](connector-mongodb.md)

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory ke zkopírování dat z databáze MongoDB. Vychází [zkopírujte aktivity přehled](copy-activity-overview.md) článek, který představuje obecný přehled aktivity kopírování.

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [MongoDB konektoru V1](v1/data-factory-on-premises-mongodb-connector.md).


## <a name="supported-capabilities"></a>Podporované možnosti

Do úložiště dat žádné podporované podřízený může kopírovat data z databáze MongoDB. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor MongoDB podporuje:

- MongoDB **verze 2.4, 2.6, 3.0, 3.2, 3.4 a 3.6**.
- Kopírování dat pomocí **základní** nebo **anonymní** ověřování.

## <a name="prerequisites"></a>Požadavky

Ke zkopírování dat z databáze MongoDB, která není veřejně přístupný, musíte nastavit Self-hosted integrace Runtime. V tématu [Self-hosted integrace Runtime](create-self-hosted-integration-runtime.md) článku se dozvíte podrobnosti. Modul Runtime integrace poskytuje integrované ovladače MongoDB, proto nemusíte ručně nainstalovat všechny ovladače při kopírování dat z MongoDB.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnosti, které slouží k určení konkrétní entity služby Data Factory ke konektoru MongoDB.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro MongoDB propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type |Vlastnost typu musí být nastavena na: **MongoDb** |Ano |
| server |IP adresa nebo název hostitele serveru MongoDB. |Ano |
| port |Port TCP, který používá MongoDB server naslouchat pro připojení klientů. |Ne (výchozí hodnota je 27017) |
| databaseName |Název databáze MongoDB, kterou chcete získat přístup. |Ano |
| authenticationType. | Typ ověřování používaný pro připojení k databázi MongoDB.<br/>Povolené hodnoty jsou: **základní**, a **anonymní**. |Ano |
| uživatelské jméno |Uživatelský účet pro přístup k MongoDB. |Ano (Pokud se používá základní ověřování). |
| heslo |Heslo pro uživatele. Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). |Ano (Pokud se používá základní ověřování). |
| authSource |Název databáze MongoDB, kterou chcete použít ke kontrole přihlašovacích údajů pro ověřování. |Ne. Pro základní ověřování výchozí hodnota je používat účet správce a do databáze určené pomocí vlastnost databaseName. |
| enableSsl | Určuje, zda jsou šifrované připojení k serveru pomocí protokolu SSL. Výchozí hodnota je false.  | Ne |
| allowSelfSignedServerCert | Určuje, jestli se má povolit certifikáty podepsané svým držitelem ze serveru. Výchozí hodnota je false.  | Ne |
| connectVia | [Integrace Runtime](concepts-integration-runtime.md) který se má použít pro připojení k úložišti. (Pokud je veřejně přístupná data store), můžete použít modul Runtime integrace Self-hosted nebo Runtime integrace Azure. Pokud není zadaný, použije výchozí Runtime integrace Azure. |Ne |

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností nepodporuje datovou sadu MongoDB.

Ke zkopírování dat z MongoDB, nastavte vlastnost typu datové sady, která **MongoDbCollection**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu datové sady musí být nastavena na: **MongoDbCollection** | Ano |
| Název_kolekce |Název kolekce v databázi MongoDB. |Ano |

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností, které jsou podporovány zdrojem MongoDB.

### <a name="mongodb-as-source"></a>MongoDB jako zdroj

Ke zkopírování dat z MongoDB, nastavte typ zdroje v aktivitě kopírování do **MongoDbSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ zdroje kopie aktivity musí být nastavena na: **MongoDbSource** | Ano |
| query |Použijte vlastní dotaz SQL 92 číst data. Příklad: vybrat * z MyTable. |Ne (když je určena "Název_kolekce" v datové sadě) |

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
> Při zadejte příkaz jazyka SQL, věnujte pozornost formátu data a času. Příklad: `SELECT * FROM Account WHERE LastModifiedDate >= '2018-06-01' AND LastModifiedDate < '2018-06-02'` nebo pomocí parametru `SELECT * FROM Account WHERE LastModifiedDate >= '@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}' AND LastModifiedDate < '@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'`

## <a name="schema-by-data-factory"></a>Schéma službou Data Factory

Služba Azure Data Factory odvodí, že schéma z kolekce MongoDB pomocí **nejnovější 100 dokumenty** v kolekci. Pokud tyto dokumenty 100 neobsahují úplné schéma, může být některé sloupce ignorován při kopírování.

## <a name="data-type-mapping-for-mongodb"></a>Datový typ mapování pro MongoDB

Při kopírování dat z MongoDB, se používají následující mapování MongoDB datových typů k Azure Data Factory dočasné datové typy. V tématu [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) a zjistěte, jak aktivity kopírování mapuje zdroje schéma a data typ jímky.

| MongoDB datový typ | Typ průběžných dat objektu pro vytváření dat |
|:--- |:--- |
| Binární hodnota |Byte] |
| Logická hodnota |Logická hodnota |
| Datum |DateTime |
| NumberDouble |Double |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |Řetězec |
| Řetězec |Řetězec |
| UUID |Guid |
| Objekt |Renormalized do vyrovnání sloupce s "_" jako vnořené oddělovače |

> [!NOTE]
> Další informace o podpoře pro polí pomocí virtuální tabulky, najdete v tématu [podporu pro komplexní typy pomocí virtuální tabulky](#support-for-complex-types-using-virtual-tables) části.
>
> V současné době nejsou podporovány následující typy dat MongoDB: DBPointer, JavaScript, Max za minutu klíče, regulární výraz, Symbol, časové razítko, Undefined.

## <a name="support-for-complex-types-using-virtual-tables"></a>Podpora pro komplexní typy pomocí virtuální tabulky

Azure Data Factory používá integrované ovladače ODBC pro připojení k a kopírování dat z databáze MongoDB. Pro komplexní typy jako pole nebo objekty s různé typy mezi dokumenty ovladač znovu sjednotí data na odpovídající virtuální tabulky. Konkrétně Pokud tabulka obsahuje tyto sloupce, ovladač generuje následující virtuální tabulky:

* A **základní tabulka**, která obsahuje stejná data jako skutečné tabulky s výjimkou komplexní typ sloupce. Základní tabulka používá stejný název jako skutečné tabulky, která reprezentuje.
* A **virtuální tabulku** pro každý sloupec komplexní typ, který rozbalí vnořená data. Virtuální tabulky jsou pojmenované pomocí názvu skutečné tabulky, oddělovač "_" a název pole nebo objekt.

Virtuální tabulky odkazovat na data v tabulce skutečné povolení ovladače pro přístup k datům nenormalizované. Dotazování a připojení virtuální tabulky, můžete přístup k obsahu polí MongoDB.

### <a name="example"></a>Příklad:

Například je zde ExampleTable MongoDB tabulku, která má jeden sloupec s pole objektů v každé buňce – faktury a jeden sloupec s pole Skalární typy – hodnocení.

| _id | Jméno zákazníka | Faktury | Úroveň služby | Hodnocení |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id: "123", položka: "Toaster byl", cena: "456", slevu: "0,2"}, {invoice_id: "124", položka: "sušárny", ceny: slevách "1235": "0,2"}] |Stříbrný |[5,6] |
| 2222 |XYZ |[{invoice_id: "135", položka: "ledničky", cena: "12543", slevu: "0,0"}] |Zlatá |[1,2] |

Ovladač by vygeneroval více virtuální tabulky k reprezentaci této jednu tabulku. První virtuální tabulky je základní tabulka s názvem "ExampleTable" v příkladu. Základní tabulka obsahuje všechna data z původní tabulky, ale data z pole byla vynechána a je v tabulkách virtuální rozbalena.

| _id | Jméno zákazníka | Úroveň služby |
| --- | --- | --- |
| 1111 |ABC |Stříbrný |
| 2222 |XYZ |Zlatá |

Virtuální tabulky, které představují původní pole v příkladu v následujících tabulkách. Tyto tabulky obsahují následující:

* Odkaz zpět na původní sloupec primárního klíče odpovídající řádek původní pole (přes sloupec _id)
* Označení pozice dat v rámci původní pole
* Rozšířená data pro každý prvek v rámci pole

**Tabulka "ExampleTable_Invoices":**

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | Položka | price | Sleva |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |Toaster byl |456 |0.2 |
| 1111 |1 |124 |sušárny |1235 |0.2 |
| 2222 |0 |135 |ledničky |12543 |0.0 |

**Tabulka "ExampleTable_Ratings":**

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |


## <a name="next-steps"></a>Další postup
Seznam úložišť dat jako zdroje a jímky nepodporuje aktivitu kopírování v Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
