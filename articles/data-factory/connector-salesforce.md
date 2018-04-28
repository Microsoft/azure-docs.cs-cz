---
title: Kopírování dat z a do služby Salesforce pomocí Azure Data Factory | Microsoft Docs
description: Zjistěte, jak zkopírovat data ze služby Salesforce k úložištím dat. podporované podřízený nebo z podporované zdrojové úložiště dat do služby Salesforce pomocí aktivity kopírování v kanálu data factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: jingwang
ms.openlocfilehash: f4de97ef2df5351ac7e8574717ee1439b54a90e8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="copy-data-from-and-to-salesforce-by-using-azure-data-factory"></a>Kopírování dat z a do služby Salesforce pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – Obecně dostupná](v1/data-factory-salesforce-connector.md)
> * [Verze 2 – Preview](connector-salesforce.md)

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory ke zkopírování dat z a do služby Salesforce. Vychází [aktivity kopírování přehled](copy-activity-overview.md) článek, který představuje obecný přehled o aktivitě kopírování.

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 objektu pro vytváření dat, která je obecně k dispozici, najdete v části [konektor služby Salesforce v verze 1](v1/data-factory-salesforce-connector.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Ze služby Salesforce můžete zkopírovat data do úložiště dat žádné podporované jímky. Také můžete zkopírovat data z jakékoli úložiště podporované zdroje dat do služby Salesforce. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor služby Salesforce podporuje:

- Edice Salesforce Developer, Professional, Enterprise nebo neomezený.
- Kopírování dat z a do produkční Salesforce, izolovaného prostoru a vlastní doménu.

## <a name="prerequisites"></a>Požadavky

Rozhraní API oprávnění musí být v Salesforce povoleno. Další informace najdete v tématu [povolit rozhraní API pro přístup v Salesforce podle sady oprávnění](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)

## <a name="salesforce-request-limits"></a>Omezení žádostí o služby Salesforce

Služba Salesforce má limity pro celkový počet žádostí o rozhraní API a souběžných žádostí o rozhraní API. Je třeba počítat s následujícím:

- Pokud počet souběžných požadavků překročí limit, dojde k omezení šířky pásma a zobrazí náhodné chyby.
- Pokud celkový počet požadavků překračuje limit, zablokuje se účtu Salesforce ke 24 hodin.

Může také zobrazí chybová zpráva "REQUEST_LIMIT_EXCEEDED" v obou situacích. Další informace najdete v části "Omezení žádostí o rozhraní API" v [Salesforce vývojáře omezení](http://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf).

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnosti, které se používají k definování entit služby Data Factory, které jsou specifické pro konektor služby Salesforce.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Následující vlastnosti jsou podporovány pro službu propojené služby Salesforce.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type |Vlastnost typu musí být nastavená na **Salesforce**. |Ano |
| environmentUrl | Zadejte adresu URL instance Salesforce. <br> -Výchozí hodnota je `"https://login.salesforce.com"`. <br> -Ke zkopírování dat z izolovaného prostoru, zadejte `"https://test.salesforce.com"`. <br> -Ke zkopírování dat z vlastní domény, zadejte, například `"https://[domain].my.salesforce.com"`. |Ne |
| uživatelské jméno |Zadejte uživatelské jméno pro uživatelský účet. |Ano |
| heslo |Zadejte heslo pro uživatelský účet.<br/><br/>Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). |Ano |
| securityToken |Zadejte token zabezpečení pro uživatelský účet. Pokyny, jak obnovit a získat token zabezpečení, najdete v části [získat token zabezpečení](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm). Obecné informace o tokeny zabezpečení najdete v tématu [zabezpečení a rozhraní API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm).<br/><br/>Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). |Ano |
| connectVia | [Integrace runtime](concepts-integration-runtime.md) který se má použít pro připojení k úložišti. Pokud není zadaný, použije výchozí Runtime integrace Azure. | Žádné zdroje, Ano pro sink pokud propojený zdroj služba nemá integrace modulu runtime |

>[!IMPORTANT]
>Při kopírování dat do služby Salesforce, výchozí Runtime integrace Azure nelze použít ke spuštění kopírování. Jinými slovy, pokud vaše zdrojová propojené služby nemá zadaný integrace běhu, explicitně [vytvoření modulu Runtime integrace Azure](create-azure-integration-runtime.md#create-azure-ir) s umístěním téměř vaše instance služby Salesforce. Přidružení služby Salesforce propojené jako v následujícím příkladu.

**Příklad: Přihlašovací údaje k úložišti v datové továrně**

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

**Příklad: Přihlašovací údaje k úložišti v Key Vault**

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datové sady](concepts-datasets-linked-services.md) článku. Tato část obsahuje seznam vlastností nepodporuje datová sada služby Salesforce.

Ke zkopírování dat z a do služby Salesforce, nastavte vlastnost typu datové sady, která **SalesforceObject**. Jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu musí být nastavená na **SalesforceObject**.  | Ano |
| objectApiName | Název objektu služby Salesforce k načtení dat z. | Ne Ano pro sink zdroje |

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
>Pro zpětnou kompatibilitu: Při kopírování dat ze služby Salesforce, pokud používáte předchozí typ dataset "RelationalTable" udržuje práci a zobrazí návrhu přepnout na nový typ "SalesforceObject".

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu datové sady musí být nastavena na **RelationalTable**. | Ano |
| tableName | Název tabulky v Salesforce. | Ne (když je zadán zdroj aktivity "dotaz") |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností nepodporuje Salesforce zdroj a jímka.

### <a name="salesforce-as-a-source-type"></a>Salesforce jako typ zdroje

Ke kopírování dat ze služby Salesforce, nastavte typ zdroje v aktivitě kopírování do **SalesforceSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ zdroje kopie aktivity musí být nastavena na **SalesforceSource**. | Ano |
| query |Čtení dat pomocí vlastního dotazu. Můžete použít dotaz SQL 92 nebo [Salesforce objektu dotazu jazyka (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) dotazu. Příklad: `select * from MyTable__c`. | Ne (když je určena "tableName" v datové sadě) |
| readBehavior | Označuje, zda dotaz na existující záznamy nebo dotaz na všechny záznamy, včetně těch odstraněné. Pokud není zadaný, použije se výchozí chování je první. <br>Povolené hodnoty: **dotazu** (výchozí), **queryAll**.  | Ne |

> [!IMPORTANT]
> Část "__c" **název rozhraní API** je potřeba pro všechny vlastní objekt.

![Připojení Salesforce objekt pro vytváření dat název rozhraní API seznamu](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

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
>Pro zpětnou kompatibilitu: Při kopírování dat ze služby Salesforce, pokud používáte předchozí kopie typu "RelationalSource" zdroji udržuje práci a zobrazí návrhu přepnout na nový typ "SalesforceSource".

### <a name="salesforce-as-a-sink-type"></a>Salesforce jako typ jímky

Ke zkopírování dat do služby Salesforce, nastavte typ jímky v aktivitě kopírování do **SalesforceSink**. Následující vlastnosti jsou podporovány v aktivitě kopírování **podřízený** části.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ jímky aktivity kopírování musíte nastavit **SalesforceSink**. | Ano |
| WriteBehavior | Chování zápisu pro danou operaci.<br/>Povolené hodnoty jsou **vložit** a **Upsert**. | Ne (výchozí hodnota je Insert) |
| externalIdFieldName | Název pole externí ID pro operaci upsert. Zadané pole musí být definovaný jako "Externí Id pole" v objektu služby Salesforce. Odpovídající vstupních dat nemůže mít hodnotu NULL. | Ano pro "Upsert" |
| writeBatchSize | Počet řádků dat zapsaných na Salesforce v každé dávce. | Ne (výchozí hodnota je 5 000) |
| ignoreNullValues | Označuje, zda ignorovat hodnot NULL ze vstupních dat během operace zápisu.<br/>Povolené hodnoty jsou **true** a **false**.<br>- **Hodnota TRUE,**: ponechejte data v cílovém objektu beze změny po provedení operace upsert nebo aktualizace. Při operaci vložení, vložte definované výchozí hodnotu.<br/>- **False**: aktualizovat data v cílovém objektu na hodnotu NULL, když se operace upsert nebo aktualizace. Při operaci vložení, vložte hodnotu NULL. | Ne (výchozí hodnota je false) |

**Příklad: Salesforce jímka v aktivitě kopírování**

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

## <a name="query-tips"></a>Typy dotazů

### <a name="retrieve-data-from-a-salesforce-report"></a>Načíst data ze sestavy služby Salesforce

Ze sestavy služby Salesforce můžete data načíst zadáním dotazu jako `{call "<report name>"}`. Příklad: `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-recycle-bin"></a>Načtení odstraněné záznamy z koše služby Salesforce

Chcete-li prohledávat logicky odstraněné záznamy z koše služby Salesforce, můžete zadat **"IsDeleted = 1"** v dotazu. Příklad:

* Chcete-li prohledávat pouze odstraněné záznamy, zadejte "vybrat * z MyTable__c **kde IsDeleted = 1**."
* Chcete-li prohledávat všechny záznamy, včetně existující a Odstraněná, zadejte "vybrat * z MyTable__c **kde IsDeleted = 0 nebo IsDeleted = 1**."

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Načtení dat pomocí where klauzule ve sloupci data a času

Při zadávání dotazu SOQL nebo SQL věnujte pozornost rozdíl formátu data a času. Příklad:

* **Ukázka SOQL**: `SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **Ukázka SQL**: `SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

## <a name="data-type-mapping-for-salesforce"></a>Datový typ mapování pro Salesforce

Při kopírování dat ze služby Salesforce se používají následující mapování ze služby Salesforce datové typy k objektu pro vytváření dat dočasné datové typy. Další informace o aktivitě kopírování jak mapuje typ zdroje schéma a data na jímky najdete v tématu [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md).

| Salesforce datový typ | Typ průběžných dat objektu pro vytváření dat |
|:--- |:--- |
| Automatické číslování |Řetězec |
| Zaškrtávací políčko |Logická hodnota |
| Měna |Dvojitý |
| Datum |DateTime |
| Datum/čas |DateTime |
| Email |Řetězec |
| ID |Řetězec |
| Relace hledání |Řetězec |
| Vybrat víc rozevíracího seznamu |Řetězec |
| Číslo |Dvojitý |
| Procento |Dvojitý |
| Telefon |Řetězec |
| Rozevírací seznam |Řetězec |
| Text |Řetězec |
| Textová oblast |Řetězec |
| Textová oblast (Long) |Řetězec |
| (Rich) textová oblast |Řetězec |
| Text (šifrované) |Řetězec |
| zprostředkovatele identity |Řetězec |

## <a name="next-steps"></a>Další postup
Seznam úložišť dat jako zdroje a jímky nepodporuje aktivitu kopírování v objektu pro vytváření dat najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).