---
title: Přesun dat ze služby Salesforce pomocí služby Data Factory | Microsoft Docs
description: Další informace o tom, jak přesunout data ze služby Salesforce pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: dbe3bfd6-fa6a-491a-9638-3a9a10d396d1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: dddb29d2a796449d6a2c9b68e01e94f32d3022e4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621985"
---
# <a name="move-data-from-salesforce-by-using-azure-data-factory"></a>Přesun dat ze služby Salesforce pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](data-factory-salesforce-connector.md)
> * [Verze 2 – Preview](../connector-salesforce.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verze 2 služby Data Factory, který je ve verzi preview, najdete v části [konektor služby Salesforce v V2](../connector-salesforce.md).


Tento článek popisuje, jak pomocí aktivity kopírování v objektu pro vytváření dat Azure ke kopírování dat ze služby Salesforce do jakékoli úložiště dat, která je uvedena ve sloupci jímka v [podporované zdroje a jímky](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabulky. Tento článek vychází [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který uvádí obecný přehled přesun dat s aktivitou kopírování a kombinace podporované datové úložiště.

Azure Data Factory aktuálně podporuje pouze přesunutí dat ze služby Salesforce k [podporovanými úložišti dat podřízený](data-factory-data-movement-activities.md#supported-data-stores-and-formats), ale nemá nepodporuje přesun dat z jiných dat ukládá do služby Salesforce.

## <a name="supported-versions"></a>Podporované verze
Tento konektor podporuje následujících edice Salesforce: Developer Edition, edice Professional, Enterprise Edition nebo neomezená Edition. A podporuje kopírování z výroby, izolovaný prostor a vlastní doménu služby Salesforce.

## <a name="prerequisites"></a>Požadavky
* Musí být povoleno oprávnění rozhraní API. V tématu [povolení přístup pomocí rozhraní API v Salesforce sadou oprávnění?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)
* Kopírování dat ze služby Salesforce do místního úložiště dat, pokud nemáte alespoň 2.0 brány správy dat ve vašem prostředí místní instalace.

## <a name="salesforce-request-limits"></a>Omezení žádostí o služby Salesforce
Služba Salesforce má limity pro celkový počet žádostí o rozhraní API a souběžných žádostí o rozhraní API. Je třeba počítat s následujícím:

- Pokud počet souběžných požadavků překročí limit, dojde k omezení šířky pásma a zobrazí se náhodné chyby.
- Pokud celkový počet požadavků překračuje limit, účtu Salesforce se zablokuje 24 hodin.

V obou případech se může také zobrazí chyba "REQUEST_LIMIT_EXCEEDED". Najdete v části "API omezení počtu požadavků" v [Salesforce vývojáře omezení](http://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) článku.

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, který přesouvá data ze služby Salesforce pomocí různých nástrojů nebo rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál je použití **Průvodce kopírováním**. V tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) podrobný rychlé vytvoření kanálu pomocí Průvodce kopírováním data.

Tyto nástroje můžete také použít k vytvoření kanálu: **portál Azure**, **Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru**, **.NET API**, a **REST API**. V tématu [kurzu aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování. 

Jestli používáte nástroje nebo rozhraní API, je třeba provést následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat podřízený: 

1. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do data factory.
2. Vytvoření **datové sady** představují vstupní a výstupní data pro kopírování. 
3. Vytvoření **kanálu** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup. 

Když použijete průvodce, jsou automaticky vytvoří definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál). Při použití nástroje nebo rozhraní API (s výjimkou .NET API), definujete tyto entity služby Data Factory pomocí formátu JSON.  Příklad s definicemi JSON entit služby Data Factory, které slouží ke kopírování dat ze služby Salesforce, naleznete v tématu [JSON příklad: kopírování dat ze služby Salesforce do objektu Blob Azure](#json-example-copy-data-from-salesforce-to-azure-blob) tohoto článku. 

Následující části obsahují podrobnosti o vlastnostech formátu JSON, které slouží k určení entit služby Data Factory konkrétní do služby Salesforce: 

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka obsahuje popis elementy JSON, které jsou specifické pro službu propojené služby Salesforce.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost typu musí být nastavena na: **Salesforce**. |Ano |
| environmentUrl | Zadejte adresu URL služby Salesforce instanci. <br><br> -Výchozí hodnota je "https://login.salesforce.com". <br> -Ke zkopírování dat z izolovaného prostoru, zadejte "https://test.salesforce.com". <br> -Ke zkopírování dat z vlastní domény, zadejte, například "https://[domain].my.salesforce.com". |Ne |
| uživatelské jméno |Zadejte uživatelské jméno pro uživatelský účet. |Ano |
| heslo |Zadejte heslo pro uživatelský účet. |Ano |
| securityToken |Zadejte token zabezpečení pro uživatelský účet. V tématu [získal token zabezpečení](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) pokyny o tom, jak resetování nebo získat token zabezpečení. Obecné informace o tokeny zabezpečení najdete v tématu [zabezpečení a rozhraní API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Ano |

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly, jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny typy datovou sadu (Azure SQL, objektů blob v Azure, Azure table atd.).

**Rámci typeProperties** oddílu se liší pro jednotlivé typy datovou sadu a informace o umístění dat v úložišti dat. Rámci typeProperties část datové sady typu **RelationalTable** má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky v Salesforce. |Ne (Pokud **dotazu** z **RelationalSource** je zadána) |

> [!IMPORTANT]
> Část "__c" název rozhraní API je potřeba pro všechny vlastní objekt.

![Název objektu pro vytváření – připojení Salesforce – rozhraní API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, jako je název, popis, vstupní a výstupní tabulky a jsou dostupné pro všechny typy aktivit různé zásady.

Vlastnosti, které jsou k dispozici v rámci typeProperties části aktivity, se liší na druhé straně, každý typ aktivity. Pro aktivitu kopírování budou lišit v závislosti na typech zdrojů a jímky.

Při aktivitě kopírování, pokud je zdroj typu **RelationalSource** (která zahrnuje Salesforce), v rámci typeProperties části jsou k dispozici následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Čtení dat pomocí vlastního dotazu. |Dotaz SQL 92 nebo [Salesforce objektu dotazu jazyka (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) dotazu. Například `select * from MyTable__c`. |Ne (Pokud **tableName** z **datovou sadu** je zadána) |

> [!IMPORTANT]
> Část "__c" název rozhraní API je potřeba pro všechny vlastní objekt.

![Název objektu pro vytváření – připojení Salesforce – rozhraní API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## <a name="query-tips"></a>Typy dotazů
### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>Načítání dat pomocí where klauzule ve sloupci data a času
Při zadejte SOQL nebo SQL dotaz, věnujte pozornost rozdíl formátu data a času. Příklad:

* **Ukázka SOQL**: `$$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', WindowStart, WindowEnd)`
* **Ukázka SQL**:
    * **Pomocí Průvodce kopírováním vytvoříte dotaz:** `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)`
    * **Pomocí úpravy zadat dotaz JSON (char vyhnuli správně):** `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', WindowStart, WindowEnd)`

### <a name="retrieving-data-from-salesforce-report"></a>Načítání dat ze sestavy služby Salesforce
Ze sestavy služby Salesforce můžete data načíst zadáním dotazu jako `{call "<report name>"}`, např. `"query": "{call \"TestReport\"}"`.

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>Načítání odstranit záznamy z funkce Koš služby Salesforce
Chcete-li prohledávat logicky odstraněné záznamy z funkce Koš služby Salesforce, můžete zadat **"IsDeleted = 1"** v dotazu. Například:

* Chcete-li prohledávat pouze odstraněné záznamy, zadejte "vybrat * z MyTable__c **kde IsDeleted = 1**"
* Chcete-li prohledávat všechny záznamy, včetně existující a Odstraněná, zadejte "vybrat * z MyTable__c **kde IsDeleted = 0 nebo IsDeleted = 1**"

## <a name="json-example-copy-data-from-salesforce-to-azure-blob"></a>Příklad JSON: kopírování dat ze služby Salesforce do objektu Blob Azure
Následující příklad uvádí ukázka JSON definice, které můžete použít k vytvoření kanálu pomocí [portál Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Se ukazují, jak zkopírovat data ze služby Salesforce do Azure Blob Storage. Však lze zkopírovat data do jakéhokoli z jímky uvádí [sem](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.   

Tady jsou artefaktů služby Data Factory, které budete muset vytvořit implementovat scénář. Části seznamu obsahují podrobnosti o těchto krocích.

* Propojené služby typu [Salesforce](#linked-service-properties)
* Propojené služby typu [azurestorage.](data-factory-azure-blob-connector.md#linked-service-properties)
* Vstup [datovou sadu](data-factory-create-datasets.md) typu [RelationalTable](#dataset-properties)
* Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [RelationalSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

**Salesforce propojené služby**

Tento příklad používá **Salesforce** propojené služby. Najdete v článku [Salesforce propojená služba](#linked-service-properties) části pro vlastnosti, které podporuje tuto propojenou službu.  V tématu [získal token zabezpečení](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) pokyny o tom, jak resetování nebo získat token zabezpečení.

```json
{
    "name": "SalesforceLinkedService",
    "properties":
    {
        "type": "Salesforce",
        "typeProperties":
        {
            "username": "<user name>",
            "password": "<password>",
            "securityToken": "<security token>"
        }
    }
}
```
**Propojená služba Azure Storage**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
    "type": "AzureStorage",
    "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
    }
}
```
**Vstupní datové sady služby Salesforce**

```json
{
    "name": "SalesforceInput",
    "properties": {
        "linkedServiceName": "SalesforceLinkedService",
        "type": "RelationalTable",
        "typeProperties": {
            "tableName": "AllDataType__c"  
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Nastavení **externí** k **true** služba Data Factory informuje, že datová sada je externí k objektu pro vytváření dat a není vyprodukované aktivitu v datové továrně.

> [!IMPORTANT]
> Část "__c" název rozhraní API je potřeba pro všechny vlastní objekt.

![Název objektu pro vytváření – připojení Salesforce – rozhraní API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

**Výstupní datová sada Azure Blob**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodiny, interval: 1).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/alltypes_c"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Kanál s aktivitou kopírování**

Kanál obsahuje aktivitu kopírování, která je konfigurovaná pro používání vstupní a výstupní datové sady a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **RelationalSource**a **podřízený** je typ nastaven na **BlobSink**.

V tématu [vlastnosti typu RelationalSource](#copy-activity-properties) pro seznam vlastností, které jsou podporovány RelationalSource.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2016-06-01T18:00:00",
        "end":"2016-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
        {
            "name": "SalesforceToAzureBlob",
            "description": "Copy from Salesforce to an Azure blob",
            "type": "Copy",
            "inputs": [
            {
                "name": "SalesforceInput"
            }
            ],
            "outputs": [
            {
                "name": "AzureBlobOutput"
            }
            ],
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"                
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }
        ]
    }
}
```
> [!IMPORTANT]
> Část "__c" název rozhraní API je potřeba pro všechny vlastní objekt.

![Název objektu pro vytváření – připojení Salesforce – rozhraní API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)


### <a name="type-mapping-for-salesforce"></a>Mapování typu pro Salesforce
| Typ Salesforce | . Na základě NET typu |
| --- | --- |
| Automatické číslování |Řetězec |
| Zaškrtávací políčko |Logická hodnota |
| Měna |Double |
| Datum |DateTime |
| Datum/čas |DateTime |
| Email |Řetězec |
| ID |Řetězec |
| Relace hledání |Řetězec |
| Vybrat víc rozevíracího seznamu |Řetězec |
| Číslo |Double |
| Procento |Double |
| Telefon |Řetězec |
| Rozevírací seznam |Řetězec |
| Text |Řetězec |
| Textová oblast |Řetězec |
| Textová oblast (Long) |Řetězec |
| (Rich) textová oblast |Řetězec |
| Text (šifrované) |Řetězec |
| zprostředkovatele identity |Řetězec |

> [!NOTE]
> Mapování sloupců z datové sady zdroje na sloupce ze sady jímku dat naleznete v tématu [mapování sloupců datovou sadu v Azure Data Factory](data-factory-map-columns.md).

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Výkon a ladění
Najdete v článku [výkonu kopie aktivity a vyladění průvodce](data-factory-copy-activity-performance.md) Další informace o klíčových faktorů, že dopad výkon přesun dat (aktivita kopírování) v Azure Data Factory a různé způsoby, jak optimalizovat ho.
