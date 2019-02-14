---
title: Přesouvání dat ze služby Salesforce pomocí služby Data Factory | Dokumentace Microsoftu
description: Další informace o tom, jak přesunout data z produktu Salesforce pomocí služby Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: dbe3bfd6-fa6a-491a-9638-3a9a10d396d1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: c2364715bfeaea473db292baff2eb1e1cce3203b
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56233010"
---
# <a name="move-data-from-salesforce-by-using-azure-data-factory"></a>Přesun dat pomocí služby Azure Data Factory ze služby Salesforce
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-salesforce-connector.md)
> * [Verze 2 (aktuální verze)](../connector-salesforce.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [konektor Salesforce ve verzi V2](../connector-salesforce.md).

Tento článek popisuje, jak můžete použít aktivitu kopírování, která ve službě Azure data factory pro kopírování dat ze Salesforce do jakékoli úložiště dat, která je uvedená ve sloupci jímky v [zdroje a jímky podporované](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabulky. Tento článek vychází [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který představuje obecný přehled o přesun dat pomocí aktivity kopírování a kombinace podporované datové úložiště.

Azure Data Factory v současné době podporuje pouze přesouvá data z produktu Salesforce do [podporovaná úložiště dat jímky](data-factory-data-movement-activities.md#supported-data-stores-and-formats), ale nepodporuje přesun dat z jiných dat ukládá do Salesforce.

## <a name="supported-versions"></a>Podporované verze
Tento konektor podporuje v následujících edicích sady Salesforce: Developer Edition, edice Professional, Enterprise Edition nebo neomezené Edition. A podporuje kopírování z výroby, izolovaný prostor a vlastních domén služby Salesforce.

## <a name="prerequisites"></a>Požadavky
* Rozhraní API oprávnění musí být povolené. Zobrazit [jak povolit přístup přes rozhraní API v Salesforce. sada oprávnění?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)
* Pro kopírování dat ze Salesforce do místních úložišť dat, je zapotřebí alespoň 2.0 brány správy dat nainstalovaný ve vašem místním prostředí.

## <a name="salesforce-request-limits"></a>Omezení žádostí o služby Salesforce
Salesforce má omezení pro celkový počet požadavků rozhraní API a souběžných požadavků na rozhraní API. Je třeba počítat s následujícím:

- Pokud počet souběžných požadavků překročí limit, dojde k omezení šířky pásma a zobrazí se náhodné chyby.
- Pokud celkový počet požadavků překročí limit, účet služby Salesforce se zablokuje po dobu 24 hodin.

V obou případech může být také zobrazí chyba "REQUEST_LIMIT_EXCEEDED". V části "Rozhraní API omezení počtu požadavků" v [limity pro vývojáře Salesforce](http://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) , kde najdete podrobnosti.

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, který přesouvá data z produktu Salesforce pomocí různých nástrojů a rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Zobrazit [kurzu: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním data.

Tyto nástroje můžete také použít k vytvoření kanálu: **Azure portal**, **sady Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru**, **rozhraní .NET API**a  **Rozhraní REST API**. Zobrazit [kurz aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování.

Ať už používáte, nástrojů nebo rozhraní API, proveďte následující kroky k vytvoření kanálu pro přesouvání dat ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do služby data factory.
2. Vytvoření **datových sad** k představují vstupní a výstupní data pro operaci kopírování.
3. Vytvoření **kanálu** s aktivitou kopírování, která přijímá jako vstupní datovou sadu a datovou sadu jako výstup.

Při použití Průvodce definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál) se automaticky vytvoří za vás. Při použití nástroje a rozhraní API (s výjimkou rozhraní .NET API), můžete definovat tyto entity služby Data Factory ve formátu JSON. Tady je příklad s definice JSON entit služby Data Factory, které se používají ke kopírování dat ze služby Salesforce, naleznete v tématu [příklad JSON: Kopírování dat ze Salesforce do objektů Blob v Azure](#json-example-copy-data-from-salesforce-to-azure-blob) části tohoto článku.

Následující části obsahují podrobnosti o vlastnostech JSON, které se používají k definování entit služby Data Factory konkrétní Salesforce:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka obsahuje popis JSON prvky, které jsou specifické pro Salesforce, propojené služby.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost type musí být nastavená na: **Salesforce**. |Ano |
| environmentUrl | Zadejte adresu URL služby Salesforce instanci. <br><br> – Výchozí hodnota je "https://login.salesforce.com". <br> – Chcete-li kopírovat data z karantény, zadejte "https://test.salesforce.com". <br> -Ke zkopírování dat z vlastní domény, zadejte, například "https://[domain].my.salesforce.com". |Ne |
| uživatelské jméno |Zadejte uživatelské jméno uživatelského účtu. |Ano |
| heslo |Zadejte heslo pro uživatelský účet. |Ano |
| Token securityToken |Zadejte token zabezpečení pro uživatelský účet. Zobrazit [získal token zabezpečení](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) pokyny o tom, jak resetování/get token zabezpečení. Obecné informace o tokeny zabezpečení najdete v tématu [zabezpečení a rozhraní API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Ano |

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly, jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny typy datovou sadu (Azure SQL, Azure blob, tabulek v Azure a tak dále).

**TypeProperties** oddílu se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti. V části datové sady typu typeProperties **RelationalTable** má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky v Salesforce. |Ne (Pokud **dotazu** z **RelationalSource** určena) |

> [!IMPORTANT]
> Část "__c" název rozhraní API je potřeba pro všechny vlastní objekt.

![Název datové továrny – připojení Salesforce – rozhraní API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, jako jsou název, popis, vstupu a výstupu tabulky a jsou dostupné pro všechny typy aktivit různé zásady.

Vlastnosti, které jsou k dispozici v části typeProperties aktivity se liší na druhé straně, každý typ aktivity. Pro aktivitu kopírování se liší v závislosti na typy zdroje a jímky.

V aktivitě kopírování, pokud je zdroj typu **RelationalSource** (který zahrnuje Salesforce), v části typeProperties jsou k dispozici následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Použijte vlastní dotaz číst data. |Dotaz SQL 92 nebo [Salesforce objektu dotazu jazyka (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) dotazu. Například: `select * from MyTable__c`. |Ne (Pokud **tableName** z **datovou sadu** určena) |

> [!IMPORTANT]
> Část "__c" název rozhraní API je potřeba pro všechny vlastní objekt.

![Název datové továrny – připojení Salesforce – rozhraní API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## <a name="query-tips"></a>Tipy k dotazům
### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>Načítání dat pomocí where klauzuli sloupce data a času
Když zadat dotaz SOQL nebo SQL, věnujte pozornost tomu rozdíl formátu data a času. Příklad:

* **Ukázka SOQL**: `$$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', WindowStart, WindowEnd)`
* **Ukázka SQL**:
    * **Pomocí Průvodce kopírováním k určení dotazu:** `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)`
    * **Pomocí úpravy zadat dotaz JSON (znak escape správně):** `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', WindowStart, WindowEnd)`

### <a name="retrieving-data-from-salesforce-report"></a>Načítání dat ze sestav Salesforce
Zadáním dotazu, protože může načítat data ze sestav Salesforce `{call "<report name>"}`, např. `"query": "{call \"TestReport\"}"`.

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>Načítání odstraní záznamy ze Salesforce odpadkový koš
Chcete-li prohledávat obnovitelně odstraněné záznamy z odpadkového koše služby Salesforce, můžete zadat **"IsDeleted = 1"** v dotazu. Například:

* Chcete-li prohledávat pouze odstraněné záznamy, zadejte "vybrat * z MyTable__c **kde IsDeleted = 1**"
* Chcete-li prohledávat všechny záznamy, včetně stávající a Odstraněná, zadejte "vybrat * z MyTable__c **kde IsDeleted = 0 nebo IsDeleted = 1**"

## <a name="json-example-copy-data-from-salesforce-to-azure-blob"></a>Příklad JSON: Kopírování dat ze Salesforce do objektů Blob v Azure
Následující příklad obsahuje ukázky JSON definice, které můžete použít k vytvoření kanálu pomocí [webu Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md), [sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Zobrazí se kopírování dat ze Salesforce do Azure Blob Storage. Ale data je možné zkopírovat do libovolné jímky uvedeno [tady](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivit kopírování ve službě Azure Data Factory.

Tady jsou artefaktů služby Data Factory, které je potřeba vytvořit k implementaci scénáře. Následující části seznamu zadejte podrobnosti o těchto krocích.

* Propojené služby typu [Salesforce](#linked-service-properties)
* Propojené služby typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Vstupní hodnota [datovou sadu](data-factory-create-datasets.md) typu [RelationalTable](#dataset-properties)
* Výstup [datovou sadu](data-factory-create-datasets.md) typu [Azureblobu](data-factory-azure-blob-connector.md#dataset-properties)
* A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [RelationalSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

**Salesforce propojené služby**

V tomto příkladu **Salesforce** propojenou službu. Najdete v článku [Salesforce propojená služba](#linked-service-properties) oddíl pro vlastnosti, které podporují tuto propojenou službu. Zobrazit [získal token zabezpečení](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) pokyny o tom, jak resetování/get token zabezpečení.

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
**Vstupní datová sada Salesforce**

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

Nastavení **externí** k **true** služby Data Factory informuje, že datová sada je externí do služby data factory a není vytvořen aktivitou ve službě data factory.

> [!IMPORTANT]
> Část "__c" název rozhraní API je potřeba pro všechny vlastní objekt.

![Název datové továrny – připojení Salesforce – rozhraní API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

**Výstupní datová sada Azure Blob**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodina, interval: 1).

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

Kanál obsahuje aktivitu kopírování, který je nakonfigurován na použití vstupních a výstupních datových sad a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **RelationalSource**a **jímky** je typ nastaven na **BlobSink**.

Zobrazit [vlastnosti typu RelationalSource](#copy-activity-properties) pro seznam vlastností, které jsou podporovány RelationalSource.

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

![Název datové továrny – připojení Salesforce – rozhraní API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)


### <a name="type-mapping-for-salesforce"></a>Mapování typu pro Salesforce
| Typ Salesforce | . NET podle typu |
| --- | --- |
| Automatické číslo |String |
| Zaškrtávací políčko |Logická hodnota |
| Měna |Decimal |
| Datum |DateTime |
| Datum a čas |DateTime |
| Email |String |
| ID |String |
| Relaci vyhledávání |String |
| Vyberte rozevírací seznam |String |
| Číslo |Decimal |
| Procento |Decimal |
| Telefon |String |
| Rozevírací seznam |String |
| Text |String |
| Textové pole |String |
| Textová oblast (dlouhý) |String |
| Textové pole (RTF) |String |
| Text (zašifrovaný) |String |
| zprostředkovatele identity |String |

> [!NOTE]
> Pokud chcete namapovat sloupce ze zdrojové datové sady na sloupce z datové sady jímky, najdete v článku [mapování sloupců v datové sadě ve službě Azure Data Factory](data-factory-map-columns.md).

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Výkon a ladění
Zobrazit [Průvodce laděním a výkonem aktivity kopírování](data-factory-copy-activity-performance.md) Další informace o klíčových faktorů této ovlivnit výkon přesouvání dat (aktivita kopírování) ve službě Azure Data Factory a různé způsoby, jak optimalizovat.
