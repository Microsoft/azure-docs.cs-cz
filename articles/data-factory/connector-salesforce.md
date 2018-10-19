---
title: Kopírování dat z a do Salesforce pomocí služby Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data pomocí aktivity kopírování v kanálu data factory ze Salesforce do úložišť dat jímky podporované nebo z podporovaných úložišť dat do služby Salesforce.
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
ms.date: 10/17/2018
ms.author: jingwang
ms.openlocfilehash: bc98fc2465c280c41a77823de239a5572c5d27e4
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49409573"
---
# <a name="copy-data-from-and-to-salesforce-by-using-azure-data-factory"></a>Kopírování dat z a do Salesforce pomocí služby Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](v1/data-factory-salesforce-connector.md)
> * [Aktuální verze](connector-salesforce.md)

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory pro kopírování dat z a do Salesforce. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Kopírovat data ze Salesforce do jakékoli podporovaného úložiště dat jímky. Můžete také můžete kopírovat data ze všech podporovaných zdrojů úložišť dat do Salesforce. Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor Salesforce podporuje:

- Edice Salesforce Developer, Professional, Enterprise nebo neomezený.
- Kopírování dat z a do produkčního prostředí Salesforce, izolovaný prostor a vlastní domény.

Konektor Salesforce je postavený na rozhraní REST API služby Salesforce se [v39](https://developer.salesforce.com/docs/atlas.en-us.206.0.api_rest.meta/api_rest/resources_list.htm) pro kopírování dat z a [v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) pro kopírování dat do.

## <a name="prerequisites"></a>Požadavky

Rozhraní API oprávnění musí být povolená v Salesforce. Další informace najdete v tématu [přístup k rozhraní API povolit v Salesforce. sada oprávnění](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)

## <a name="salesforce-request-limits"></a>Omezení žádostí o služby Salesforce

Salesforce má omezení pro celkový počet požadavků rozhraní API a souběžných požadavků na rozhraní API. Je třeba počítat s následujícím:

- Pokud počet souběžných požadavků překročí limit, dojde k omezení šířky pásma a zaznamenat náhodných chyb.
- Pokud celkový počet požadavků překročí limit, se po dobu 24 hodin zablokuje účtu Salesforce.

V obou případech může být také zobrazí chybová zpráva "REQUEST_LIMIT_EXCEEDED". Další informace najdete v části "Omezení žádostí o rozhraní API" v [limity pro vývojáře Salesforce](http://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf).

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní konektor Salesforce.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Následující vlastnosti jsou podporovány pro Salesforce, propojené služby.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type |Vlastnost type musí být nastavená na **Salesforce**. |Ano |
| environmentUrl | Zadejte adresu URL Salesforce instance. <br> – Výchozí hodnota je `"https://login.salesforce.com"`. <br> – Chcete-li kopírovat data z karantény, zadejte `"https://test.salesforce.com"`. <br> -Ke zkopírování dat z vlastní domény, zadejte, například `"https://[domain].my.salesforce.com"`. |Ne |
| uživatelské jméno |Zadejte uživatelské jméno uživatelského účtu. |Ano |
| heslo |Zadejte heslo pro uživatelský účet.<br/><br/>Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). |Ano |
| Token securityToken |Zadejte token zabezpečení pro uživatelský účet. Pokyny o tom, jak obnovit a získat token zabezpečení najdete v tématu [získat token zabezpečení](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm). Obecné informace o tokeny zabezpečení najdete v tématu [zabezpečení a rozhraní API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm).<br/><br/>Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). |Ano |
| connectVia | [Prostředí integration runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. | Ne pro zdroj, Ano pro jímku pokud propojený zdroj služba nemá modul runtime integrace |

>[!IMPORTANT]
>Při kopírování dat do služby Salesforce, nelze provádět kopírování použít výchozí prostředí Azure Integration Runtime. Jinými slovy, pokud zdrojem propojená služba nemá zadaný integration runtime, explicitně [vytvořit prostředí Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) umístění blízko vaší instance služby Salesforce. Přidružte Salesforce propojené služby jako v následujícím příkladu.

**Příklad: Store přihlašovacích údajů ve službě Data Factory**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
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

**Příklad: Store přihlašovacích údajů ve službě Key Vault**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. Tato část obsahuje seznam vlastností podporovaných datovou sadu Salesforce.

Pro kopírování dat z a do Salesforce, nastavte vlastnost typ datové sady na **SalesforceObject**. Jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na **SalesforceObject**.  | Ano |
| objectApiName | Název objektu Salesforce načítat data z. | Ne pro zdroj, Ano pro jímku |

> [!IMPORTANT]
> Část "__c" **název rozhraní API** je potřeba pro všechny vlastní objekt.

![Připojení Salesforce objekt pro vytváření dat název rozhraní API](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Příklad:**

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "<Salesforce linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "objectApiName": "MyTable__c"
        }
    }
}
```

>[!NOTE]
>Z důvodu zpětné kompatibility: Při kopírování dat ze služby Salesforce, pokud používáte předchozí datovou sadu typu "RelationalTable" sleduje práci a uvidíte můžete přepnout na nový typ "SalesforceObject".

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady, musí být nastavená na **RelationalTable**. | Ano |
| tableName | Název tabulky v Salesforce. | Ne (když je zadán zdroj aktivity "dotaz") |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností, které podporuje Salesforce zdroje a jímky.

### <a name="salesforce-as-a-source-type"></a>Jako typ zdroje Salesforce

Pro kopírování dat ze služby Salesforce, nastavte typ zdroje v aktivitě kopírování do **SalesforceSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** oddílu.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavená na **SalesforceSource**. | Ano |
| query |Použijte vlastní dotaz číst data. Můžete použít [Salesforce objektu dotazu jazyka (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) dotaz nebo dotaz SQL 92. Zobrazit další tipy v [dotazování tipy](#query-tips) oddílu. Pokud dotazu není zadán, bude možné načíst všechna data služby Salesforce objekt určený v "objectApiName" v datové sadě. | Ne (když je "objectApiName" v datové sadě zadán) |
| readBehavior | Označuje, zda dotaz existujících záznamů nebo dotazu včetně odstranil všechny záznamy. Pokud není zadán, výchozí chování je první. <br>Povolené hodnoty: **dotazu** (výchozí), **queryAll**.  | Ne |

> [!IMPORTANT]
> Část "__c" **název rozhraní API** je potřeba pro všechny vlastní objekt.

![Připojení Salesforce objekt pro vytváření dat seznamu název rozhraní API](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce input dataset name>",
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
                "type": "SalesforceSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

>[!NOTE]
>Z důvodu zpětné kompatibility: Při kopírování dat ze služby Salesforce, pokud používáte předchozí kopie typu "RelationalSource" zdroj sleduje práci a najdete v článku můžete přepnout na nový typ "SalesforceSource".

### <a name="salesforce-as-a-sink-type"></a>Salesforce jako typ jímky

Ke zkopírování dat do služby Salesforce, nastavte typ jímky v aktivitě kopírování do **SalesforceSink**. Následující vlastnosti jsou podporovány v aktivitě kopírování **jímky** oddílu.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Nastavte vlastnost typ jímky aktivity kopírování **SalesforceSink**. | Ano |
| WriteBehavior | Chování zápisu pro operaci.<br/>Povolené hodnoty jsou **vložit** a **Upsert**. | Ne (výchozí hodnota je vložení) |
| externalIdFieldName | Název pole externího ID operace upsert. Zadané pole musí být definován jako "Pole externího Id" v objektu Salesforce. V odpovídající vstupní data nemůže mít hodnotu NULL. | Ano pro "Upsert" |
| WriteBatchSize | Počet řádků dat zapsaných do Salesforce v každé dávce. | Ne (výchozí hodnota je 5 000) |
| ignoreNullValues | Určuje, jestli se má ignorovat hodnot NULL ze vstupních dat během operace zápisu.<br/>Povolené hodnoty jsou **true** a **false**.<br>- **Hodnota TRUE**: ponechat data v cílové objektů beze změny, pokud tak učiníte, operace upsert nebo aktualizace. Definovaná výchozí hodnota vložte, když provedete operaci vložení.<br/>- **False**: aktualizace dat v cílového objektu na hodnotu NULL, pokud tak učiníte, operace upsert nebo aktualizace. Vložení hodnoty NULL, když provedete operaci vložení. | Ne (výchozí hodnota je false) |

**Příklad: Salesforce jímky v aktivitě kopírování**

```json
"activities":[
    {
        "name": "CopyToSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Salesforce output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceSink",
                "writeBehavior": "Upsert",
                "externalIdFieldName": "CustomerId__c",
                "writeBatchSize": 10000,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="query-tips"></a>Tipy k dotazům

### <a name="retrieve-data-from-a-salesforce-report"></a>Načtení dat ze sestav Salesforce

Zadáním dotazu, protože může načítat data ze sestav Salesforce `{call "<report name>"}`. Příklad: `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-recycle-bin"></a>Načíst odstraněné záznamy z odpadkového koše služby Salesforce

K dotazování obnovitelně odstraněné záznamy z koše služby Salesforce, můžete zadat `readBehavior` jako `queryAll`. 

### <a name="difference-between-soql-and-sql-query-syntax"></a>Rozdíl mezi SOQL a SQL syntaxe dotazů

Kopírování dat ze služby Salesforce, můžete použít SOQL dotazu nebo příkaz jazyka SQL. Všimněte si, že tyto dvě má odlišnou syntaxi a podporu funkce, nelze kombinovat. Byly navrženy používat SOQL dotaz, který má nativní podporu v Salesforce. Následující tabulka uvádí hlavní rozdíly:

| Syntaxe | Režim SOQL | Režim SQL |
|:--- |:--- |:--- |
| Výběr sloupce | Třeba vyjmenování pole ke zkopírování v dotazu, například `SELECT field1, filed2 FROM objectname` | `SELECT *` se podporuje kromě výběr sloupce. |
| Uvozovky | Názvy zaznamenaná/objektů nemůže citovat. | Názvy polí nebo objektů může používat uvozovky, například `SELECT "id" FROM "Account"` |
| Formát data a času |  Přečtěte si podrobnosti o [tady](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm) a ukázky v další části. | Přečtěte si podrobnosti o [tady](https://docs.microsoft.com/sql/odbc/reference/develop-app/date-time-and-timestamp-literals?view=sql-server-2017) a ukázky v další části. |
| Logické hodnoty | Reprezentovaná jako `False` a `True`, třeba `SELECT … WHERE IsDeleted=True`. | Reprezentovaná jako 0 nebo 1, třeba `SELECT … WHERE IsDeleted=1`. |
| Přejmenování sloupce | Nepodporuje se. | Podporované, třeba: `SELECT a AS b FROM …`. |
| Relace | Podporované, třeba `Account_vod__r.nvs_Country__c`. | Nepodporuje se. |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Načtení dat pomocí where klauzuli sloupce data a času

Při zadávání dotazu SOQL nebo SQL věnujte pozornost rozdíl formátu data a času. Příklad:

* **Ukázka SOQL**: `SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **Ukázka SQL**: `SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

## <a name="data-type-mapping-for-salesforce"></a>Datový typ mapování pro Salesforce

Při kopírování dat ze služby Salesforce se používají následující mapování ze Salesforce datové typy do služby Data Factory dočasné datových typů. Další informace o jak aktivita kopírování mapuje typ zdroje schéma a data jímky, najdete v článku [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md).

| Datový typ Salesforce | Data Factory dočasné datový typ |
|:--- |:--- |
| Automatické číslo |Řetězec |
| Zaškrtávací políčko |Logická hodnota |
| Měna |Decimal |
| Datum |DateTime |
| Datum/čas |DateTime |
| Email |Řetězec |
| ID |Řetězec |
| Relaci vyhledávání |Řetězec |
| Vyberte rozevírací seznam |Řetězec |
| Číslo |Decimal |
| Procento |Decimal |
| Telefon |Řetězec |
| Rozevírací seznam |Řetězec |
| Text |Řetězec |
| Textové pole |Řetězec |
| Textová oblast (dlouhý) |Řetězec |
| Textové pole (RTF) |Řetězec |
| Text (zašifrovaný) |Řetězec |
| zprostředkovatele identity |Řetězec |

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
