---
title: Kopírování dat z cloudu služby Salesforce a do služby Salesforce
description: Zjistěte, jak kopírovat data z Salesforce Service Cloud do podporovaných úložišť dat jímky nebo z podporovaných zdrojových úložišť dat do služby Salesforce Service Cloud pomocí aktivity kopírování v kanálu datové továrny.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: 4540b27a9241a14b3d1a153d11bf43900e8ae0ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153844"
---
# <a name="copy-data-from-and-to-salesforce-service-cloud-by-using-azure-data-factory"></a>Kopírování dat z a do cloudu služby Salesforce pomocí Azure Data Factory

Tento článek popisuje, jak používat aktivitu kopírování v Azure Data Factory ke kopírování dat z a do Cloudu služeb Salesforce. Vychází z článku [přehled aktivity kopírování,](copy-activity-overview.md) který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Salesforce Service Cloud je podporován pro následující aktivity:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Data z Cloudu služby Salesforce Service můžete zkopírovat do libovolného podporovaného úložiště dat jímky. Můžete také kopírovat data z libovolného podporovaného zdrojového úložiště dat do služby Salesforce Service Cloud. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitou Kopírování, naleznete v tabulce [Podporovaná úložiště dat.](copy-activity-overview.md#supported-data-stores-and-formats)

Tento konektor Salesforce Service Cloud konkrétně podporuje:

- Edice Salesforce Developer, Professional, Enterprise nebo Unlimited.
- Kopírování dat z a do produkčního prostředí Salesforce, izolovaného prostoru a vlastní domény.

Konektor Salesforce je postavený na rozhraní SALESFORCE REST/Bulk API. Ve výchozím nastavení konektor používá [v45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) ke kopírování dat z Salesforce a používá [v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) ke kopírování dat do Salesforce. Můžete také explicitně nastavit verzi rozhraní API používanou ke čtení a zápisu dat prostřednictvím [ `apiVersion` vlastnosti](#linked-service-properties) v propojené službě.

## <a name="prerequisites"></a>Požadavky

Oprávnění rozhraní API musí být povoleno v Salesforce. Další informace naleznete v tématu [Povolení přístupu k rozhraní API v Salesforce podle sady oprávnění](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)

## <a name="salesforce-request-limits"></a>Omezení požadavků salesforce

Salesforce má limity pro celkové požadavky rozhraní API i souběžné požadavky rozhraní API. Je třeba počítat s následujícím:

- Pokud počet souběžných požadavků překročí limit, dojde k omezení a zobrazí se náhodné chyby.
- Pokud celkový počet požadavků překročí limit, účet Salesforce bude na 24 hodin zablokován.

V obou scénářích se také může zobrazit chybová zpráva "REQUEST_LIMIT_EXCEEDED". Další informace naleznete v části Limity požadavků rozhraní API v [části Omezení pro vývojáře Salesforce](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf).

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Data Factory specifických pro konektor Salesforce Service Cloud.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu Salesforce jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type |Vlastnost type musí být nastavena na **SalesforceServiceCloud**. |Ano |
| prostředíUrl | Zadejte adresu URL instance Služby Salesforce. <br> - Výchozí `"https://login.salesforce.com"`nastavení je . <br> - Chcete-li kopírovat data `"https://test.salesforce.com"`z izolovaného prostoru, zadejte . <br> - Chcete-li kopírovat data z vlastní `"https://[domain].my.salesforce.com"`domény, zadejte například . |Ne |
| uživatelské jméno |Zadejte uživatelské jméno uživatelského účtu. |Ano |
| heslo |Zadejte heslo pro uživatelský účet.<br/><br/>Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). |Ano |
| token security |Zadejte token zabezpečení pro uživatelský účet. <br/><br/>Informace o tokenech zabezpečení obecně naleznete v [tématu Zabezpečení a rozhraní API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). Token zabezpečení lze přeskočit pouze v případě, že přidáte IP adresu IP prostředí integrace do [seznamu důvěryhodných adres IP](https://developer.salesforce.com/docs/atlas.en-us.securityImplGuide.meta/securityImplGuide/security_networkaccess.htm) v salesforce. Když používáte Azure IR, podívejte se na [IP adresy Azure Integration Runtime .](azure-integration-runtime-ip-addresses.md)<br/><br/>Pokyny k získání a resetování tokenu zabezpečení naleznete v [tématu Získání tokenu zabezpečení](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm). Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). |Ne |
| apiVersion | Zadejte verzi rozhraní API Služby USN/Bulk, která má být používána, například `48.0`. Ve výchozím nastavení konektor používá [v45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) ke kopírování dat z Salesforce a používá [v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) ke kopírování dat do Salesforce. | Ne |
| connectVia | [Integrační runtime,](concepts-integration-runtime.md) který se má použít k připojení k úložišti dat. Pokud není zadán, používá výchozí Azure Integration Runtime. | Ne pro zdroj, Ano pro jímku, pokud zdrojová propojená služba nemá integrační runtime |

>[!IMPORTANT]
>Když zkopírujete data do cloudu Služby Salesforce, výchozí prostředí Azure Integration Runtime nelze použít ke spuštění kopírování. Jinými slovy, pokud vaše zdrojová propojená služba nemá zadaný integrační runtime, explicitně [vytvořte prostředí Integrace Azure](create-azure-integration-runtime.md#create-azure-ir) s umístěním v blízkosti instance Salesforce Service Cloud. Přidružte propojenou službu Salesforce Service Cloud jako v následujícím příkladu.

**Příklad: Uložení přihlašovacích údajů v datové továrně**

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

**Příklad: Uložení přihlašovacích údajů v trezoru klíčů**

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [Datové sady.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných datovou sadou Salesforce Service Cloud.

Chcete-li kopírovat data z a do služby Salesforce Service Cloud, jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na **SalesforceServiceCloudObject**.  | Ano |
| objectApiName | Název objektu Salesforce, ze který chcete načíst data. | Ne pro zdroj, Ano pro umyvadlo |

> [!IMPORTANT]
> "__c" část **názvu rozhraní API** je potřeba pro všechny vlastní objekt.

![Název rozhraní API pro připojení Salesforce data](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

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
| type | Vlastnost type datové sady musí být nastavena na **Relační tabulka**. | Ano |
| tableName | Název tabulky v cloudu služby Salesforce Service. | Ne (pokud je zadán "dotaz" ve zdroji aktivity) |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem a jímkou služby Salesforce Service.

### <a name="salesforce-service-cloud-as-a-source-type"></a>Salesforce Service Cloud jako zdrojový typ

Chcete-li kopírovat data z služby Salesforce Service Cloud, jsou v části **zdroje aktivity** kopírování podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavena na **SalesforceServiceCloudSource**. | Ano |
| query |Ke čtení dat použijte vlastní dotaz. Můžete použít dotaz [Jazyka dotazů na objektY Salesforce (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) nebo dotaz SQL-92. Další tipy najdete v části [Tipy pro dotazy.](#query-tips) Pokud dotaz není zadán, budou načtena všechna data objektu Služby Salesforce Cloud zadaná v poli "objectApiName" v datové sadě. | Ne (pokud je v datové sadě zadán "objectApiName") |
| readBehavior | Označuje, zda se mají dotazovat na existující záznamy nebo zda mají být dotazovány na všechny záznamy včetně odstraněných záznamů. Pokud není zadán, výchozí chování je první. <br>Povolené hodnoty: **dotaz** (výchozí), **queryAll**.  | Ne |

> [!IMPORTANT]
> "__c" část **názvu rozhraní API** je potřeba pro všechny vlastní objekt.

![Seznam názvů rozhraní API pro připojení Salesforce Data Factory](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

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

### <a name="salesforce-service-cloud-as-a-sink-type"></a>Salesforce Service Cloud jako typ jímky

Chcete-li zkopírovat data do služby Salesforce Service Cloud, jsou v části **jímky** aktivity kopírování podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type jímky aktivity kopírování musí být nastavena na **SalesforceServiceCloudSink**. | Ano |
| writeBehavior | Chování zápisu pro operaci.<br/>Povolené hodnoty jsou **Insert** a **Upsert**. | Ne (výchozí hodnota je Vložit) |
| externalIdFieldName | Název pole externího ID pro operaci upsert. Zadané pole musí být definováno jako "Pole externího Id" v objektu Salesforce Service Cloud. Nemůže mít hodnoty NULL v odpovídajících vstupních datech. | Ano pro "Upsert" |
| writeBatchSize | Počet řádků dat zapsaných do služby Salesforce Service Cloud v každé dávce. | Ne (výchozí hodnota je 5 000) |
| ignoreNullValues | Označuje, zda se mají ignorovat hodnoty NULL ze vstupních dat během operace zápisu.<br/>Povolené hodnoty jsou **pravdivé** a **nepravdivé**.<br>- **Pravda**: Ponechte data v cílovém objektu beze změny, když proveďte operaci upsert nebo update. Při operaci vložení vložte definovanou výchozí hodnotu.<br/>- **False**: Aktualizace dat v cílovém objektu na hodnotu NULL při operaci upsert nebo aktualizace. Při operaci vložení vložte hodnotu NULL. | Ne (výchozí hodnota je nepravdivá) |

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

### <a name="retrieve-data-from-a-salesforce-service-cloud-report"></a>Načtení dat ze sestavy Služby Salesforce

Data ze sestav Služby Salesforce můžete načíst zadáním dotazu jako `{call "<report name>"}`. Příklad: `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-service-cloud-recycle-bin"></a>Načtení odstraněných záznamů z koše cloudového cloudu služby Salesforce Service

Chcete-li zadat dotaz na obnovitelné odstraněné záznamy z `readBehavior` `queryAll`koše služby Salesforce Service Cloud, můžete zadat jako . 

### <a name="difference-between-soql-and-sql-query-syntax"></a>Rozdíl mezi syntaxí dotazu SOQL a SQL

Při kopírování dat z salesforce service cloudu můžete použít buď dotaz SOQL, nebo dotaz SQL. Všimněte si, že tyto dva má různé syntaxe a podporu funkčnosti, nemíchejte ji. Doporučujeme použít dotaz SOQL, který je nativně podporován službou Salesforce Service Cloud. V následující tabulce jsou uvedeny hlavní rozdíly:

| Syntaxe | Režim SOQL | Režim SQL |
|:--- |:--- |:--- |
| Výběr sloupce | Je třeba vytvořit výčet polí, která mají být v dotazu zkopírována, např.`SELECT field1, filed2 FROM objectname` | `SELECT *`je podporována kromě výběru sloupců. |
| Uvozovek | Názvy souborů/objektů nelze uvést. | Lze uvést názvy polí/objektů, např.`SELECT "id" FROM "Account"` |
| Formát datačasu |  Zde naleznete [podrobnosti](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm) a ukázky v další části. | Zde naleznete [podrobnosti](https://docs.microsoft.com/sql/odbc/reference/develop-app/date-time-and-timestamp-literals?view=sql-server-2017) a ukázky v další části. |
| Logické hodnoty | Reprezentované jako `False` a `True`, např. `SELECT … WHERE IsDeleted=True` | Reprezentované jako 0 `SELECT … WHERE IsDeleted=1`nebo 1, např. |
| Přejmenování sloupců | Není podporováno. | Podporováno např.: `SELECT a AS b FROM …`. |
| Relace | Podporováno např. `Account_vod__r.nvs_Country__c` | Není podporováno. |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Načtení dat pomocí klauzule where ve sloupci DateTime

Při zadávání dotazu SOQL nebo SQL, dávat pozor na rozdíl formátu DateTime. Například:

* **SOQL vzorek**:`SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **Ukázka SQL**:`SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformed_querytruncated"></a>Chyba MALFORMED_QUERY:Zkráceno

Pokud stisknete chybu "MALFORMED_QUERY: Zkráceno", obvykle je to kvůli tomu, že máte junctionidlist typ sloupce v datech a Salesforce má omezení na podporu těchto dat s velkým počtem řádků. Chcete-li zmírnit, zkuste vyloučit JunctionIdList sloupec nebo omezit počet řádků ke kopírování (můžete rozdělit na více spuštění aktivity kopírování).

## <a name="data-type-mapping-for-salesforce-service-cloud"></a>Mapování datových typů pro cloud služby Salesforce Service

Při kopírování dat z salesforce service cloudu se používají následující mapování z datových typů Salesforce Service Cloud do dočasných datových typů Data Factory. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ do jímky, naleznete v [tématu Schémata a mapování datových typů](copy-activity-schema-and-type-mapping.md).

| Datový typ Salesforce Service Cloud | Dočasný datový typ datové továrny |
|:--- |:--- |
| Automatické číslo |Řetězec |
| Zaškrtávací políčko |Logická hodnota |
| Měna |Desetinné číslo |
| Datum |DateTime |
| Datum/Čas |DateTime |
| E-mail |Řetězec |
| ID |Řetězec |
| Vyhledávací vztah |Řetězec |
| Výběrový seznam s více výběry |Řetězec |
| Číslo |Desetinné číslo |
| Procento |Desetinné číslo |
| Telefon |Řetězec |
| Picklist |Řetězec |
| Text |Řetězec |
| Oblast Textu |Řetězec |
| Textová oblast (dlouhá) |Řetězec |
| Oblast textu (bohatá) |Řetězec |
| Text (šifrovaný) |Řetězec |
| zprostředkovatele identity |Řetězec |

## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v datové továrně naleznete v [tématu Podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
