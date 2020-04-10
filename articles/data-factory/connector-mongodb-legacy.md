---
title: Kopírování dat z MongoDB pomocí starších verzí
description: Zjistěte, jak zkopírovat data z Mongo DB do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 08/12/2019
ms.openlocfilehash: 31588f9657a883e9c4a5600d2d164125a5f68edf
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991939"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Kopírování dat z MongoDB pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-on-premises-mongodb-connector.md)
> * [Aktuální verze](connector-mongodb.md)

Tento článek popisuje, jak použít aktivitu kopírování v Azure Data Factory ke kopírování dat z databáze MongoDB. Vychází z článku [přehledu aktivity kopírování,](copy-activity-overview.md) který představuje obecný přehled aktivity kopírování.

>[!IMPORTANT]
>ADF vydání nového konektoru MongoDB, který poskytuje lepší nativní podporu MongoDB ve srovnání s touto implementací založenou na ROZHRANÍ ODBC, naleznete v článku [konektoru MongoDB](connector-mongodb.md) o podrobnostech. Tento starší konektor MongoDB je stále podporován jako-je pro zpětnou kompatibilitu, zatímco pro všechny nové úlohy, použijte nový konektor.

## <a name="supported-capabilities"></a>Podporované možnosti

Data z databáze MongoDB můžete zkopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitou kopírování, naleznete v tabulce [Podporovaná úložiště dat.](copy-activity-overview.md#supported-data-stores-and-formats)

Konkrétně tento konektor MongoDB podporuje:

- MongoDB **verze 2.4, 2.6, 3.0, 3.2, 3.4 a 3.6**.
- Kopírování dat pomocí **základního** nebo **anonymního** ověřování.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Prostředí Integration Runtime poskytuje integrovaný ovladač MongoDB, proto není nutné ručně instalovat žádný ovladač při kopírování dat z MongoDB.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Data Factory specifických pro konektor MongoDB.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu MongoDB jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type |Vlastnost type musí být nastavena na: **MongoDb.** |Ano |
| server |IP adresa nebo název hostitele serveru MongoDB. |Ano |
| port |Tcp port, který server MongoDB používá k naslouchání pro připojení klientů. |Ne (výchozí hodnota je 27017) |
| Databasename |Název databáze MongoDB, ke které chcete získat přístup. |Ano |
| authenticationType | Typ ověřování používaný pro připojení k databázi MongoDB.<br/>Povolené hodnoty jsou: **Basic**a **Anonymous**. |Ano |
| uživatelské jméno |Uživatelský účet pro přístup k MongoDB. |Ano (pokud se používá základní ověřování). |
| heslo |Heslo pro tohoto uživatele. Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). |Ano (pokud se používá základní ověřování). |
| authSource |Název databáze MongoDB, kterou chcete použít ke kontrole přihlašovacích údajů pro ověřování. |Ne. Pro základní ověřování je výchozí použití účtu správce a databáze určené pomocí vlastnosti databaseName. |
| enableSsl | Určuje, zda jsou připojení k serveru šifrována pomocí systému TLS. Výchozí hodnota je False.  | Ne |
| allowSelfSignedServerCert | Určuje, zda mají být ze serveru povoleny certifikáty podepsané svým držitelem. Výchozí hodnota je False.  | Ne |
| connectVia | [Prostředí Integrace Runtime,](concepts-integration-runtime.md) které se má použít k připojení k úložišti dat. Další informace naleznete v části [Požadavky.](#prerequisites) Pokud není zadán, používá výchozí Azure Integration Runtime. |Ne |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete [v tématu Datové sady a propojené služby](concepts-datasets-linked-services.md). Pro datovou sadu MongoDB jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena na: **MongoDbCollection.** | Ano |
| název kolekce |Název kolekce v databázi MongoDB. |Ano |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem MongoDB.

### <a name="mongodb-as-source"></a>MongoDB jako zdroj

V části **zdroje** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavena na: **MongoDbSource.** | Ano |
| query |Ke čtení dat použijte vlastní dotaz SQL-92. Příklad: vyberte * z MyTable. |Ne (pokud je zadán "collectionName" v datové sadě) |

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
> Při zadávání dotazu SQL věnujte pozornost formátu DateTime. Například: `SELECT * FROM Account WHERE LastModifiedDate >= '2018-06-01' AND LastModifiedDate < '2018-06-02'` nebo použít parametr`SELECT * FROM Account WHERE LastModifiedDate >= '@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}' AND LastModifiedDate < '@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'`

## <a name="schema-by-data-factory"></a>Schéma podle datové továrny

Služba Azure Data Factory odvodí schéma z kolekce MongoDB pomocí **nejnovějších 100 dokumentů** v kolekci. Pokud těchto 100 dokumentů neobsahuje úplné schéma, některé sloupce mohou být během operace kopírování ignorovány.

## <a name="data-type-mapping-for-mongodb"></a>Mapování datových typů pro MongoDB

Při kopírování dat z MongoDB se používají následující mapování z datových typů MongoDB do dočasných datových typů Azure Data Factory. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ do jímky, najdete v tématu [mapování schématu a datových typů.](copy-activity-schema-and-type-mapping.md)

| Datový typ MongoDB | Dočasný datový typ datové továrny |
|:--- |:--- |
| binární |Bajt[] |
| Logická hodnota |Logická hodnota |
| Datum |DateTime |
| Číslo Double |Double |
| NumberInt |Int32 |
| Početdlouhé |Int64 |
| ObjectId |Řetězec |
| Řetězec |Řetězec |
| Uuid |Identifikátor GUID |
| Objekt |Znovu normalizováno do narovnat sloupce s "_" jako vnořeným oddělovačem |

> [!NOTE]
> Další informace o podpoře polí používajících virtuální tabulky najdete [v části Podpora složitých typů používajících virtuální tabulky.](#support-for-complex-types-using-virtual-tables)
>
> V současné době nejsou podporovány následující datové typy MongoDB: DBPointer, JavaScript, Max/Min klíč, regulární výraz, symbol, časové razítko, nedefinované.

## <a name="support-for-complex-types-using-virtual-tables"></a>Podpora složitých typů pomocí virtuálních tabulek

Azure Data Factory používá integrovaný ovladač ODBC pro připojení a kopírování dat z databáze MongoDB. U složitých typů, jako jsou pole nebo objekty s různými typy v dokumentech, ovladač znovu normalizuje data do odpovídajících virtuálních tabulek. Konkrétně pokud tabulka obsahuje takové sloupce, ovladač generuje následující virtuální tabulky:

* **Základní tabulka**, která obsahuje stejná data jako skutečná tabulka s výjimkou sloupců komplexního typu. Základní tabulka používá stejný název jako skutečná tabulka, kterou představuje.
* **Virtuální tabulka** pro každý sloupec komplexního typu, která rozbalí vnořená data. Virtuální tabulky jsou pojmenovány pomocí názvu skutečné tabulky, oddělovače "_" a názvu pole nebo objektu.

Virtuální tabulky odkazují na data v reálné tabulce, což umožňuje ovladači přístup k nenormalizovaným datům. K obsahu polí MongoDB můžete přistupovat dotazováním a připojením k virtuálním tabulkám.

### <a name="example"></a>Příklad

Například ExampleTable zde je tabulka MongoDB, která má jeden sloupec s polem objektů v každé buňce – Faktury a jeden sloupec s polem skalárních typů – Hodnocení.

| _id | Jméno zákazníka | Faktury | Úroveň služeb | Ratings |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id:"123", položka:"toustovač", cena:"456", sleva:"0.2"}, {invoice_id:"124", položka:"trouba", cena: "1235", sleva: "0.2"}] |Silver |[5,6] |
| 2222 |XYZ |[{invoice_id:"135", položka:"lednice", cena: "12543", sleva: "0.0"}] |Gold |[1,2] |

Ovladač by generovat více virtuálních tabulek představují tuto jednu tabulku. První virtuální tabulka je základní tabulka s názvem "ExampleTable", která je uvedena v příkladu. Základní tabulka obsahuje všechna data původní tabulky, ale data z polí byla vynechána a jsou rozbalena ve virtuálních tabulkách.

| _id | Jméno zákazníka | Úroveň služeb |
| --- | --- | --- |
| 1111 |ABC |Silver |
| 2222 |XYZ |Gold |

V následujících tabulkách jsou zobrazeny virtuální tabulky, které představují původní pole v příkladu. Tyto tabulky obsahují následující:

* Odkaz zpět na původní sloupec primárního klíče odpovídající řádku původního pole (přes sloupec _id)
* Označení polohy dat v původním poli
* Rozšířená data pro každý prvek v rámci pole

**Tabulka "ExampleTable_Invoices":**

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | položka | price | Discount |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |Topinkovač |456 |0.2 |
| 1111 |1 |124 |Trouba |1235 |0.2 |
| 2222 |0 |135 |Lednice |12543 |0,0 |

**Tabulka "ExampleTable_Ratings":**

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v Azure Data Factory najdete v [tématu podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
