---
title: Kopírování dat z a do cloudu služby Salesforce
description: Naučte se, jak kopírovat data z cloudu služby Salesforce do podporovaných úložišť dat jímky nebo z podporovaných zdrojových úložišť dat do cloudu služby Salesforce pomocí aktivity kopírování v kanálu služby Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: 3d49ec7260017421a60268e159c8152359455871
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895621"
---
# <a name="copy-data-from-and-to-salesforce-service-cloud-by-using-azure-data-factory"></a>Kopírování dat z a do cloudu služby Salesforce pomocí Azure Data Factory

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory kopírovat data z a do cloudu služby Salesforce. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Tento cloudový konektor služby Salesforce se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)

Data z cloudu služby Salesforce můžete kopírovat do libovolného podporovaného úložiště dat jímky. Data můžete také kopírovat z libovolného podporovaného zdrojového úložiště dat do cloudu služby Salesforce. Seznam úložišť dat, která jsou v rámci aktivity kopírování podporovaná jako zdroje nebo jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Konkrétně tento cloudový konektor služby Salesforce podporuje:

- Vývojář Salesforce, edice Professional, Enterprise nebo Unlimited.
- Kopírování dat z a do služby Salesforce produkčního prostředí, izolovaného prostoru (sandboxu) a vlastní domény.

Cloudový konektor služby Salesforce je postaven na rozhraní Salesforce REST nebo Bulk API s [v45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) pro kopírování dat z a [V40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) pro kopírování dat do.

## <a name="prerequisites"></a>Předpoklady

V Salesforce musí být povolené oprávnění API. Další informace najdete v tématu [Povolení přístupu k rozhraní API v Salesforce pomocí sady oprávnění](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/) .

## <a name="salesforce-request-limits"></a>Omezení požadavku Salesforce

Salesforce má omezení pro celkové požadavky rozhraní API i pro souběžné požadavky rozhraní API. Je třeba počítat s následujícím:

- Pokud počet souběžných požadavků překročí limit, dojde k omezení a uvidíte náhodné chyby.
- Pokud celkový počet požadavků překročí limit, účet Salesforce se zablokuje na 24 hodin.

Může se také zobrazit chybová zpráva "REQUEST_LIMIT_EXCEEDED" v obou scénářích. Další informace najdete v části omezení požadavků na rozhraní API v [omezeních vývojářů Salesforce](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf).

## <a name="get-started"></a>Začít

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které se používají k definování Data Factory entit specifických pro cloudový konektor služby Salesforce.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu Salesforce jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type |Vlastnost Type musí být nastavená na **SalesforceServiceCloud**. |Ano |
| environmentUrl | Zadejte adresu URL cloudové instance služby Salesforce. <br> – Výchozí hodnota je `"https://login.salesforce.com"`. <br> – Chcete-li kopírovat data z izolovaného prostoru (sandbox), zadejte `"https://test.salesforce.com"`. <br> – Chcete-li kopírovat data z vlastní domény, zadejte například `"https://[domain].my.salesforce.com"`. |Ne |
| uživatelské jméno |Zadejte uživatelské jméno pro uživatelský účet. |Ano |
| heslo |Zadejte heslo pro uživatelský účet.<br/><br/>Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). |Ano |
| securityToken |Zadejte token zabezpečení pro uživatelský účet. Pokyny k resetování a získání tokenu zabezpečení najdete v tématu [získání tokenu zabezpečení](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm). Obecné informace o tokenech zabezpečení najdete v tématu [zabezpečení a rozhraní API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm).<br/><br/>Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). |Ano |
| connectVia | [Prostředí integration runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. | Ne pro zdroj, Ano pro jímku, pokud zdrojová propojená služba nemá modul runtime integrace |

>[!IMPORTANT]
>Při kopírování dat do cloudu služby Salesforce se výchozí Azure Integration Runtime nedá použít ke spuštění kopírování. Jinými slovy, pokud vaše zdrojová propojená služba nemá zadaný modul Integration runtime, explicitně [vytvoří Azure Integration runtime](create-azure-integration-runtime.md#create-azure-ir) s umístěním poblíž cloudové instance služby Salesforce. Přidružte propojenou cloudovou službu služby Salesforce jako v následujícím příkladu.

**Příklad: uložení přihlašovacích údajů v Data Factory**

```json
{
    "name": "SalesforceServiceCloudLinkedService",
    "properties": {
        "type": "SalesforceServiceCloud",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "securityToken": {
                "type": "SecureString",
                "value": "<security token>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Příklad: uložení přihlašovacích údajů v Key Vault**

```json
{
    "name": "SalesforceServiceCloudLinkedService",
    "properties": {
        "type": "SalesforceServiceCloud",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of password in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            },
            "securityToken": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of security token in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. V této části najdete seznam vlastností podporovaných cloudovou datovou sadou služby Salesforce.

Pokud chcete kopírovat data z a do cloudu služby Salesforce, podporují se tyto vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavená na **SalesforceServiceCloudObject**.  | Ano |
| objectApiName | Název objektu Salesforce, ze kterého se mají načíst data | Ne pro zdroj, Ano pro jímku |

> [!IMPORTANT]
> Část "__c" **názvu rozhraní API** je nutná pro libovolný vlastní objekt.

![Název rozhraní API pro připojení Salesforce Data Factory](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Příklad:**

```json
{
    "name": "SalesforceServiceCloudDataset",
    "properties": {
        "type": "SalesforceServiceCloudObject",
        "typeProperties": {
            "objectApiName": "MyTable__c"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Salesforce Service Cloud linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type datové sady musí být nastavená na **relační**. | Ano |
| tableName | Název tabulky v cloudu služby Salesforce | Ne (Pokud je zadáno "dotaz" ve zdroji aktivity) |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. V této části najdete seznam vlastností, které podporuje cloudový zdroj a jímka služby Salesforce.

### <a name="salesforce-service-cloud-as-a-source-type"></a>Cloud služby Salesforce jako typ zdroje

Pokud chcete kopírovat data z cloudu služby Salesforce, v části **zdroj** aktivity kopírování se podporují následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **SalesforceServiceCloudSource**. | Ano |
| query |Pomocí vlastního dotazu můžete číst data. Můžete použít dotaz [SOQL (Salesforce Object Query Language)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) nebo dotaz SQL-92. Další tipy najdete v části [tipy pro dotazy](#query-tips) . Pokud není zadán dotaz, budou načtena všechna data cloudového objektu služby Salesforce zadaného v "objectApiName" v datové sadě. | Ne (Pokud je zadána položka "objectApiName" v sadě dat) |
| readBehavior | Označuje, zda se má dotazovat na existující záznamy, nebo zadat dotaz na všechny záznamy, včetně odstraněných. Pokud není zadán, výchozí chování je původní. <br>Povolené hodnoty: **dotaz** (výchozí), **queryAll**.  | Ne |

> [!IMPORTANT]
> Část "__c" **názvu rozhraní API** je nutná pro libovolný vlastní objekt.

![Data Factory seznam názvů rozhraní API pro připojení Salesforce](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromSalesforceServiceCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce Service Cloud input dataset name>",
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
                "type": "SalesforceServiceCloudSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="salesforce-service-cloud-as-a-sink-type"></a>Cloud služby Salesforce jako typ jímky

Pokud chcete kopírovat data do cloudu služby Salesforce, v části **jímka** aktivity kopírování se podporují následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type jímky aktivity kopírování musí být nastavená na **SalesforceServiceCloudSink**. | Ano |
| writeBehavior | Chování zápisu pro operaci.<br/>Povolené hodnoty jsou **INSERT** a **Upsert**. | Ne (výchozí je vložení) |
| externalIdFieldName | Název pole externího ID pro operaci Upsert Zadané pole musí být definované jako pole externího ID v cloudovém objektu služby Salesforce. V odpovídajících vstupních datech nesmí mít hodnoty NULL. | Ano pro "Upsert" |
| writeBatchSize | Počet řádků dat zapsaných do cloudu služby Salesforce v každé dávce. | Ne (výchozí hodnota je 5 000) |
| ignoreNullValues | Určuje, zda se během operace zápisu mají ignorovat hodnoty NULL ze vstupních dat.<br/>Povolené hodnoty jsou **true** a **false**.<br>- **true**: ponechte data v cílovém objektu beze změny, když provedete operaci Upsert nebo Update. Při operaci INSERT vložte definovanou výchozí hodnotu.<br/>- **false**: při operaci Upsert nebo Update aktualizujte data v cílovém objektu na hodnotu null. Při operaci vložení vložte hodnotu NULL. | Ne (výchozí hodnota je false) |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyToSalesforceServiceCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Salesforce Service Cloud output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceServiceCloudSink",
                "writeBehavior": "Upsert",
                "externalIdFieldName": "CustomerId__c",
                "writeBatchSize": 10000,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="query-tips"></a>Tipy pro dotazy

### <a name="retrieve-data-from-a-salesforce-service-cloud-report"></a>Načtení dat z cloudové sestavy služby Salesforce

Data z cloudových sestav služby Salesforce můžete načíst zadáním dotazu jako `{call "<report name>"}`. Příklad: `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-service-cloud-recycle-bin"></a>Načtení odstraněných záznamů ze složky Koš cloudu služby Salesforce

Pokud chcete ze složky Koš cloudu služby Salesforce zadat dotaz na nepodmíněné odstraněné záznamy, můžete určit `readBehavior` jako `queryAll`. 

### <a name="difference-between-soql-and-sql-query-syntax"></a>Rozdíl mezi syntaxí SOQL a dotazem SQL

Při kopírování dat z cloudu služby Salesforce můžete použít buď dotaz SOQL, nebo dotaz SQL. Všimněte si, že tyto dva mají jinou podporu syntaxe a funkcí, nemíchejte ji. Navrhnete použití dotazu SOQL, který je nativně podporován cloudem služby Salesforce. V následující tabulce jsou uvedeny hlavní rozdíly:

| Syntaxe | SOQL režim | Režim SQL |
|:--- |:--- |:--- |
| Výběr sloupce | Je nutné vytvořit výčet polí, která mají být kopírována v dotazu, například `SELECT field1, filed2 FROM objectname` | Kromě výběru sloupce se podporuje `SELECT *`. |
| Uvozovky | Názvy archivovaných objektů nebo objektů nemohou být v uvozovkách. | Názvy polí a objektů mohou být v uvozovkách, například `SELECT "id" FROM "Account"` |
| Formát data a času |  [Tady](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm) najdete podrobnosti a ukázky v další části. | [Tady](https://docs.microsoft.com/sql/odbc/reference/develop-app/date-time-and-timestamp-literals?view=sql-server-2017) najdete podrobnosti a ukázky v další části. |
| hodnoty Boolean | Reprezentované jako `False` a `True`, například `SELECT … WHERE IsDeleted=True`. | Reprezentované jako 0 nebo 1, například `SELECT … WHERE IsDeleted=1`. |
| Přejmenování sloupce | Nepodporuje se. | Podporováno, např.: `SELECT a AS b FROM …`. |
| Relace | Podporováno, například `Account_vod__r.nvs_Country__c`. | Nepodporuje se. |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Načtení dat pomocí klauzule WHERE ve sloupci DateTime

Když zadáte SOQL nebo SQL dotaz, věnujte pozornost rozdílům ve formátu data a času. Například:

* **Ukázka SOQL**: `SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **Ukázka SQL**: `SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformed_querytruncated"></a>Chyba MALFORMED_QUERY: zkrácení

Pokud se zobrazí chyba "MALFORMED_QUERY: zkrácena", obvykle je z důvodu, že máte sloupec typu JunctionIdList v datech a Salesforce má omezení podporující taková data s velkým počtem řádků. Pokud chcete zmírnit, zkuste vyloučit sloupec JunctionIdList nebo omezit počet řádků ke zkopírování (můžete rozdělit na více spuštění aktivity kopírování).

## <a name="data-type-mapping-for-salesforce-service-cloud"></a>Mapování datových typů pro cloud služby Salesforce

Při kopírování dat z cloudu služby Salesforce se z cloudových datových typů služby Salesforce používají následující mapování pro Data Factory dočasných datových typů. Další informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ k jímky, najdete v tématu [mapování schémat a datových typů](copy-activity-schema-and-type-mapping.md).

| Cloudový datový typ služby Salesforce | Data Factory dočasné datový typ |
|:--- |:--- |
| Auto Number |Řetězec |
| Checkbox |Logická hodnota |
| Měna |Decimal |
| Datum |Datum a čas |
| Datum a čas |Datum a čas |
| E-mail |Řetězec |
| ID |Řetězec |
| Lookup Relationship |Řetězec |
| Multi-Select Picklist |Řetězec |
| Číslo |Decimal |
| Procento |Decimal |
| Telefon |Řetězec |
| Picklist |Řetězec |
| Text |Řetězec |
| Text Area |Řetězec |
| Text Area (Long) |Řetězec |
| Text Area (Rich) |Řetězec |
| Text (Encrypted) |Řetězec |
| Adresa URL |Řetězec |

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
