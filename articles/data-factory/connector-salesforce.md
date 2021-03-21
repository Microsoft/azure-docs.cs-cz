---
title: Kopírování dat z a do Salesforce
description: Naučte se, jak kopírovat data z Salesforce do podporovaných úložišť dat jímky nebo z podporovaných zdrojových úložišť dat do Salesforce pomocí aktivity kopírování v kanálu služby Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: 5b49e62330c789d6d5cbe2af2edb28a2c3e1238f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104583084"
---
# <a name="copy-data-from-and-to-salesforce-by-using-azure-data-factory"></a>Kopírování dat z a do Salesforce pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-salesforce-connector.md)
> * [Aktuální verze](connector-salesforce.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory kopírovat data z a do Salesforce. Vytvoří se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor Salesforce se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)

Data z Salesforce můžete kopírovat do libovolného podporovaného úložiště dat jímky. Data můžete také kopírovat z libovolného podporovaného zdrojového úložiště dat do Salesforce. Seznam úložišť dat, která jsou v rámci aktivity kopírování podporovaná jako zdroje nebo jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Konkrétně tento konektor Salesforce podporuje:

- Vývojář Salesforce, edice Professional, Enterprise nebo Unlimited.
- Kopírování dat z a do služby Salesforce produkčního prostředí, izolovaného prostoru (sandboxu) a vlastní domény.

Konektor Salesforce je postaven nad rozhraním API REST nebo Bulk pro Salesforce. Ve výchozím nastavení se při kopírování dat z Salesforce konektor používá [v45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) a automaticky se volí mezi Rest a hromadnými rozhraními API na základě velikosti dat – pokud je sada výsledků rozsáhlá, používá se pro lepší výkon hromadné rozhraní API. Při zápisu dat do Salesforce používá konektor [v40y](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) rozhraní API pro hromadné použití. Můžete také explicitně nastavit verzi rozhraní API použitou pro čtení a zápis dat prostřednictvím [ `apiVersion` vlastnosti](#linked-service-properties) v propojené službě.

## <a name="prerequisites"></a>Předpoklady

V Salesforce musí být povolené oprávnění API. Další informace najdete v tématu [Povolení přístupu k rozhraní API v Salesforce pomocí sady oprávnění](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/) .

## <a name="salesforce-request-limits"></a>Omezení požadavku Salesforce

Salesforce má omezení pro celkové požadavky rozhraní API i pro souběžné požadavky rozhraní API. Je třeba počítat s následujícím:

- Pokud počet souběžných požadavků překročí limit, dojde k omezení a uvidíte náhodné chyby.
- Pokud celkový počet požadavků překročí limit, účet Salesforce se zablokuje na 24 hodin.

Může se také zobrazit chybová zpráva "REQUEST_LIMIT_EXCEEDED" v obou scénářích. Další informace najdete v části omezení požadavků na rozhraní API v [omezeních vývojářů Salesforce](https://developer.salesforce.com/docs/atlas.en-us.218.0.salesforce_app_limits_cheatsheet.meta/salesforce_app_limits_cheatsheet/salesforce_app_limits_platform_api.htm).

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které se používají k definování Data Factory entit specifických pro konektor Salesforce.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu Salesforce jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ |Vlastnost Type musí být nastavená na **Salesforce**. |Yes |
| environmentUrl | Zadejte adresu URL instance Salesforce. <br> – Výchozí hodnota je `"https://login.salesforce.com"` . <br> – Chcete-li kopírovat data z izolovaného prostoru (sandbox), zadejte `"https://test.salesforce.com"` . <br> – Chcete-li kopírovat data z vlastní domény, zadejte například `"https://[domain].my.salesforce.com"` . |No |
| username |Zadejte uživatelské jméno pro uživatelský účet. |Yes |
| heslo |Zadejte heslo pro uživatelský účet.<br/><br/>Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| Element |Zadejte token zabezpečení pro uživatelský účet. <br/><br/>Obecné informace o tokenech zabezpečení najdete v tématu [zabezpečení a rozhraní API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). Token zabezpečení se dá přeskočit jenom v případě, že přidáte IP adresu Integration Runtime do [seznamu DŮVĚRYHODNÝCH IP adres](https://developer.salesforce.com/docs/atlas.en-us.securityImplGuide.meta/securityImplGuide/security_networkaccess.htm) na Salesforce. Pokud používáte Azure IR, přečtěte si téma [Azure Integration runtime IP adresy](azure-integration-runtime-ip-addresses.md).<br/><br/>Pokyny k získání a resetování tokenu zabezpečení najdete v tématu [získání tokenu zabezpečení](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm). Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). |No |
| apiVersion | Zadejte verzi REST nebo hromadných rozhraní API Salesforce, kterou chcete použít, např. `48.0` . Ve výchozím nastavení konektor používá [v45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) ke kopírování dat z Salesforce a používá [V40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) ke kopírování dat do Salesforce. | No |
| connectVia | [Prostředí Integration runtime](concepts-integration-runtime.md) , které se má použít pro připojení k úložišti dat. Pokud není zadaný, použije se výchozí Azure Integration Runtime. | No |

**Příklad: uložení přihlašovacích údajů v Data Factory**

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

**Příklad: uložení přihlašovacích údajů v Key Vault**

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných datovou sadou Salesforce.

Chcete-li kopírovat data z a do Salesforce, nastavte vlastnost Type datové sady na **SalesforceObject**. Podporovány jsou následující vlastnosti.

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type musí být nastavená na **SalesforceObject**.  | Yes |
| objectApiName | Název objektu Salesforce, ze kterého se mají načíst data | Ne pro zdroj, Ano pro jímku |

> [!IMPORTANT]
> Část "__c" **názvu rozhraní API** je nutná pro libovolný vlastní objekt.

![Název rozhraní API pro připojení Salesforce Data Factory](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Příklad:**

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "type": "SalesforceObject",
        "typeProperties": {
            "objectApiName": "MyTable__c"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Salesforce linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

>[!NOTE]
>Z důvodu zpětné kompatibility: Když kopírujete data ze služby Salesforce, pokud použijete předchozí datovou sadu typu "relační", bude fungovat i v případě, že se zobrazí návrh, který přepne na nový typ "SalesforceObject".

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type datové sady musí být nastavená na **relační**. | Yes |
| tableName | Název tabulky v Salesforce. | Ne (Pokud je zadáno "dotaz" ve zdroji aktivity) |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných zdrojem a jímkou Salesforce.

### <a name="salesforce-as-a-source-type"></a>Salesforce jako typ zdroje

Pokud chcete kopírovat data ze služby Salesforce, nastavte typ zdroje v aktivitě kopírování na **SalesforceSource**. V části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **SalesforceSource**. | Yes |
| query |Pomocí vlastního dotazu můžete číst data. Můžete použít dotaz [SOQL (Salesforce Object Query Language)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) nebo dotaz SQL-92. Další tipy najdete v části [tipy pro dotazy](#query-tips) . Pokud není zadán dotaz, budou načtena všechna data objektu Salesforce zadaná v "objectApiName" v datové sadě. | Ne (Pokud je zadána položka "objectApiName" v sadě dat) |
| readBehavior | Označuje, zda se má dotazovat na existující záznamy, nebo zadat dotaz na všechny záznamy, včetně odstraněných. Pokud není zadán, výchozí chování je původní. <br>Povolené hodnoty: **dotaz** (výchozí), **queryAll**.  | No |

> [!IMPORTANT]
> Část "__c" **názvu rozhraní API** je nutná pro libovolný vlastní objekt.

![Data Factory seznam názvů rozhraní API pro připojení Salesforce](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

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
>Z důvodu zpětné kompatibility: Když kopírujete data ze služby Salesforce, pokud použijete předchozí kopii typu "RelationalSource", zdroj udržuje práci, i když se zobrazí návrh, který přepne na nový typ "SalesforceSource".

### <a name="salesforce-as-a-sink-type"></a>Salesforce jako typ jímky

Pokud chcete kopírovat data do Salesforce, nastavte typ jímky v aktivitě kopírování na **SalesforceSink**. V části **jímka** aktivity kopírování jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type jímky aktivity kopírování musí být nastavená na **SalesforceSink**. | Yes |
| writeBehavior | Chování zápisu pro operaci.<br/>Povolené hodnoty jsou **INSERT** a **Upsert**. | Ne (výchozí je vložení) |
| externalIdFieldName | Název pole externího ID pro operaci Upsert Zadané pole musí být definováno jako pole externího ID v objektu Salesforce. V odpovídajících vstupních datech nesmí mít hodnoty NULL. | Ano pro "Upsert" |
| writeBatchSize | Počet řádků dat zapsaných do Salesforce v každé dávce. | Ne (výchozí hodnota je 5 000) |
| ignoreNullValues | Určuje, zda se během operace zápisu mají ignorovat hodnoty NULL ze vstupních dat.<br/>Povolené hodnoty jsou **true** a **false**.<br>- **True**: když provedete operaci Upsert nebo Update, ponechte data v cílovém objektu beze změny. Při operaci INSERT vložte definovanou výchozí hodnotu.<br/>- **False**: při operaci Upsert nebo Update aktualizujte data v cílovém objektu na hodnotu null. Při operaci vložení vložte hodnotu NULL. | Ne (výchozí hodnota je false) |
| maxConcurrentConnections |Horní limit souběžných připojení navázaných na úložiště dat během spuštění aktivity. Zadejte hodnotu pouze v případě, že chcete omezit souběžná připojení.| No |

**Příklad: jímka Salesforce v aktivitě kopírování**

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

## <a name="query-tips"></a>Tipy pro dotazy

### <a name="retrieve-data-from-a-salesforce-report"></a>Načtení dat ze sestavy Salesforce

Data ze sestav Salesforce můžete načíst zadáním dotazu jako `{call "<report name>"}` . Příklad: `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-recycle-bin"></a>Načtení odstraněných záznamů ze odpadkového koše Salesforce

Chcete-li zadat dotaz na nepodmíněné odstraněné záznamy z koše Salesforce, můžete určit `readBehavior` jako `queryAll` . 

### <a name="difference-between-soql-and-sql-query-syntax"></a>Rozdíl mezi syntaxí SOQL a dotazem SQL

Při kopírování dat z Salesforce můžete použít buď dotaz SOQL, nebo dotaz SQL. Všimněte si, že tyto dva mají jinou podporu syntaxe a funkcí, nemíchejte ji. Navrhnete použití dotazu SOQL, který je nativně podporován Salesforce. V následující tabulce jsou uvedeny hlavní rozdíly:

| Syntax | SOQL režim | Režim SQL |
|:--- |:--- |:--- |
| Výběr sloupce | Je nutné vytvořit výčet polí, která mají být kopírována v dotazu, např. `SELECT field1, filed2 FROM objectname` | `SELECT *` je podporováno kromě výběru sloupce. |
| Uvozovky | Názvy archivovaných objektů nebo objektů nemohou být v uvozovkách. | Názvy polí a objektů mohou být v uvozovkách, např. `SELECT "id" FROM "Account"` |
| Formát data a času |  [Tady](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm) najdete podrobnosti a ukázky v další části. | [Tady](/sql/odbc/reference/develop-app/date-time-and-timestamp-literals) najdete podrobnosti a ukázky v další části. |
| Logické hodnoty | Reprezentované jako `False` a `True` , např. `SELECT … WHERE IsDeleted=True` | Reprezentované jako 0 nebo 1, např `SELECT … WHERE IsDeleted=1` . |
| Přejmenování sloupce | Nepodporováno | Podporováno, např.: `SELECT a AS b FROM …` . |
| Relace | Podporováno, například `Account_vod__r.nvs_Country__c` . | Nepodporováno |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Načtení dat pomocí klauzule WHERE ve sloupci DateTime

Když zadáte SOQL nebo SQL dotaz, věnujte pozornost rozdílům ve formátu data a času. Například:

* **Ukázka SOQL**: `SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **Ukázka SQL**: `SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformed_query-truncated"></a>Chyba MALFORMED_QUERY: zkrácení

Pokud se zobrazí chyba "MALFORMED_QUERY: zkrácena", obvykle je z důvodu, že máte sloupec typu JunctionIdList v datech a Salesforce má omezení podporující taková data s velkým počtem řádků. Pokud chcete zmírnit, zkuste vyloučit sloupec JunctionIdList nebo omezit počet řádků ke zkopírování (můžete rozdělit na více spuštění aktivity kopírování).

## <a name="data-type-mapping-for-salesforce"></a>Mapování datových typů pro Salesforce

Při kopírování dat ze služby Salesforce se z datových typů Salesforce používají následující mapování pro Data Factory dočasných datových typů. Další informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ k jímky, najdete v tématu [mapování schémat a datových typů](copy-activity-schema-and-type-mapping.md).

| Typ dat Salesforce | Data Factory pomocný datový typ |
|:--- |:--- |
| Automatické číslo |Řetězec |
| Zaškrtávací políčko |Logická hodnota |
| Měna |Decimal |
| Date (Datum) |DateTime |
| Datum/Čas |DateTime |
| E-mail |Řetězec |
| ID |Řetězec |
| Relace vyhledávání |Řetězec |
| Vícenásobný výběr rozevíracího seznamu |Řetězec |
| Číslo |Decimal |
| Procento |Decimal |
| Rozložení |Řetězec |
| Picklist |Řetězec |
| Text |Řetězec |
| Oblast textu |Řetězec |
| Oblast textu (Long) |Řetězec |
| Textová oblast (bohatá) |Řetězec |
| Text (zašifrovaný) |Řetězec |
| URL |Řetězec |

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).