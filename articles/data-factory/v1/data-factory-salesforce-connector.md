---
title: Přesunutí dat ze služby Salesforce pomocí datové továrny
description: Přečtěte si, jak přesunout data z Salesforce pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: dbe3bfd6-fa6a-491a-9638-3a9a10d396d1
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8b94f6388d77cca2ef74c802aec7648091172775
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281129"
---
# <a name="move-data-from-salesforce-by-using-azure-data-factory"></a>Přesunutí dat ze služby Salesforce pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](data-factory-salesforce-connector.md)
> * [Verze 2 (aktuální verze)](../connector-salesforce.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [Konektor Salesforce ve verzi 2](../connector-salesforce.md).

Tento článek popisuje, jak můžete použít kopírovat aktivitu v továrně dat Azure ke kopírování dat z Salesforce do libovolného úložiště dat, které je uvedeno ve sloupci Jímka v [tabulce podporované zdroje a propady.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Tento článek vychází z článku [aktivity přesunu dat,](data-factory-data-movement-activities.md) který představuje obecný přehled přesunu dat s kombinacemi kopírování aktivity a podporovaných kombinací úložiště dat.

Azure Data Factory aktuálně podporuje jenom přesun dat z Salesforce do [podporovaných úložišť dat jímky](data-factory-data-movement-activities.md#supported-data-stores-and-formats), ale nepodporuje přesun dat z jiných úložišť dat do Salesforce.

## <a name="supported-versions"></a>Podporované verze
Tento konektor podporuje následující edice služby Salesforce: Developer Edition, Professional Edition, Enterprise Edition nebo Unlimited Edition. A podporuje kopírování z produkce Salesforce, izolovaného prostoru a vlastní domény.

## <a name="prerequisites"></a>Požadavky
* Oprávnění rozhraní API musí být povolena. Přečtěte [si, jak povolím přístup rozhraní API ve službách Salesforce podle sady oprávnění?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)
* Chcete-li kopírovat data z Salesforce do místních úložišť dat, musíte mít v místním prostředí nainstalovanou alespoň bránu Pro správu dat 2.0.

## <a name="salesforce-request-limits"></a>Omezení požadavků salesforce
Salesforce má limity pro celkové požadavky rozhraní API i souběžné požadavky rozhraní API. Je třeba počítat s následujícím:

- Pokud počet souběžných požadavků překročí limit, dojde k omezení a zobrazí se náhodné chyby.
- Pokud celkový počet požadavků překročí limit, bude účet Salesforce zablokován na 24 hodin.

Může se také zobrazit chyba "REQUEST_LIMIT_EXCEEDED" v obou scénářích. Podrobnosti najdete v části Limity požadavků rozhraní API v článku [Omezení pro vývojáře Salesforce.](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf)

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesune data z Salesforce pomocí různých nástrojů nebo api.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Viz [Kurz: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) pro rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat.

K vytvoření kanálu můžete taky použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Šablona Azure Resource Manager**, Rozhraní **.NET API**a REST **API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v tématu [Kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aktivity.

Bez ohledu na to, zda používáte nástroje nebo api, provedete následující kroky k vytvoření kanálu, který přesune data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **propojené služby** pro propojení vstupních a výstupních úložišť dat s vaší továrně dat.
2. Vytvořte **datové sady** představující vstupní a výstupní data pro operaci kopírování.
3. Vytvořte **kanál** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup.

Při použití průvodce jsou automaticky vytvořeny definice JSON pro tyto entity Data Factory (propojené služby, datové sady a kanál). Při použití nástrojů nebo rozhraní API (s výjimkou rozhraní .NET API) definujete tyto entity Data Factory pomocí formátu JSON. Ukázka s definicemi JSON pro entity Data Factory, které se používají ke kopírování dat z Salesforce, najdete [v tématu Příklad JSON: Kopírování dat z Salesforce do Azure Blob](#json-example-copy-data-from-salesforce-to-azure-blob) části tohoto článku.

V následujících částech jsou uvedeny podrobnosti o vlastnostech JSON, které se používají k definování entit Factory dat specifických pro Salesforce:

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb
Následující tabulka obsahuje popisy prvků JSON, které jsou specifické pro propojenou službu Salesforce.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost type musí být nastavena **na: Salesforce**. |Ano |
| prostředíUrl | Zadejte adresu URL instance Salesforce. <br><br> - Výchozí hodnota\/je "https: /login.salesforce.com". <br> - Chcete-li kopírovat data zhttps://test.salesforce.comizolovaného prostoru, zadejte " ". <br> - Chcete-li kopírovat data z vlastní domény, zadejte například "https://[domain].my.salesforce.com". |Ne |
| uživatelské jméno |Zadejte uživatelské jméno uživatelského účtu. |Ano |
| heslo |Zadejte heslo pro uživatelský účet. |Ano |
| token security |Zadejte token zabezpečení pro uživatelský účet. Pokyny k resetování/získání tokenu zabezpečení najdete v tématu [Získání tokenu zabezpečení.](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) Informace o tokenech zabezpečení obecně naleznete v [tématu Zabezpečení a rozhraní API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Ano |

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [Vytváření datových sad.](data-factory-create-datasets.md) Oddíly, jako je struktura, dostupnost a zásady datové sady JSON, jsou podobné pro všechny typy datových sad (Azure SQL, Azure blob, Tabulka Azure a tak dále).

Sekce **typeProperties** se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. Oddíl typeProperties pro datovou sadu typu **RelationalTable** má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky v Salesforce. |Ne (pokud je zadán **dotaz** **RelationalSource)** |

> [!IMPORTANT]
> "__c" část názvu rozhraní API je potřeba pro všechny vlastní objekt.

![Factory dat - Salesforce připojení - název rozhraní API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Vytváření kanálů.](data-factory-create-pipelines.md) Vlastnosti, jako je název, popis, vstupní a výstupní tabulky a různé zásady jsou k dispozici pro všechny typy aktivit.

Vlastnosti, které jsou k dispozici v části typeProperties aktivity, na druhé straně se liší podle jednotlivých typů aktivit. U aktivity kopírování se liší v závislosti na typech zdrojů a propadů.

V aktivitě kopírování, pokud je zdroj typu **RelationalSource** (který zahrnuje Salesforce), jsou v části typeProperties k dispozici následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Ke čtení dat použijte vlastní dotaz. |Dotaz SQL-92 nebo dotaz [ovacího jazyka objektu Salesforce (SOQL).](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) Například: `select * from MyTable__c`. |Ne (pokud je zadán **název tabulky** **datové sady)** |

> [!IMPORTANT]
> "__c" část názvu rozhraní API je potřeba pro všechny vlastní objekt.

![Factory dat - Salesforce připojení - název rozhraní API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## <a name="query-tips"></a>Tipy pro dotazy
### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>Načítání dat pomocí klauzule where ve sloupci DateTime
Při zadání dotazu SOQL nebo SQL, dávat pozor na rozdíl formátu DateTime. Například:

* **SOQL vzorek**:`$$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', WindowStart, WindowEnd)`
* **Ukázka SQL**:
    * **Zadání dotazu pomocí průvodce kopírováním:**`$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)`
    * **Použití úprav JSON k určení dotazu (správně uniknout znak):**`$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', WindowStart, WindowEnd)`

### <a name="retrieving-data-from-salesforce-report"></a>Načítání dat ze sestavy Salesforce
Data ze sestav Salesforce můžete načíst zadáním dotazu jako `{call "<report name>"}`,například. `"query": "{call \"TestReport\"}"`.

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>Načítání odstraněných záznamů z koše Salesforce
Chcete-li zadat dotaz na obnovitelné odstraněné záznamy z koše Salesforce, můžete v dotazu zadat **hodnotu IsDeleted = 1.** Například:

* Chcete-li zadat dotaz pouze na odstraněné záznamy, zadejte "select * from MyTable__c **where IsDeleted= 1**"
* Chcete-li zadat dotaz na všechny záznamy včetně existujícího a odstraněných, zadejte "select * from MyTable__c **where IsDeleted = 0 or IsDeleted = 1**"

## <a name="json-example-copy-data-from-salesforce-to-azure-blob"></a>Příklad JSON: Kopírování dat z Salesforce do objektu Blob Azure
Následující příklad obsahuje ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí [Visual Studia](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [Azure PowerShellu](data-factory-copy-activity-tutorial-using-powershell.md). Ukazují, jak zkopírovat data z Salesforce do úložiště objektů blob Azure. Data však můžete zkopírovat do libovolného jímky [uvedené zde](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.

Tady jsou artefakty data factory, které budete muset vytvořit k implementaci scénáře. Části, které následují podle seznamu, obsahují podrobnosti o těchto krocích.

* Propojená služba typu [Salesforce](#linked-service-properties)
* Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Vstupní [datová sada](data-factory-create-datasets.md) typu [RelationalTable](#dataset-properties)
* Výstupní [datová sada](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, který používá [Relační zdroj](#copy-activity-properties) a [objekt BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

**Propojená služba Salesforce**

Tento příklad používá propojenou službu **Salesforce.** Vlastnosti, které tato propojená služba podporuje, naleznete v části [Propojené služby Salesforce.](#linked-service-properties) Pokyny k resetování/získání tokenu zabezpečení najdete v tématu [Získání tokenu zabezpečení.](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm)

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

Nastavení **externí** **na hodnotu true** informuje službu Data Factory, že datová sada je externí pro datovou továrnu a není vytvářena aktivitou v datové továrně.

> [!IMPORTANT]
> "__c" část názvu rozhraní API je potřeba pro všechny vlastní objekt.

![Factory dat - Salesforce připojení - název rozhraní API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

**Výstupní datová sada objektů blob Azure**

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

Kanál obsahuje aktivitu kopírování, která je nakonfigurována pro použití vstupních a výstupních datových sad a je naplánována na každou hodinu. V definici kanálu JSON je **typ zdroje** nastaven na **Relační zdroj**a typ **jímky** je nastaven na **objekt BlobSink**.

Viz [Vlastnosti typu Relační zdroj](#copy-activity-properties) pro seznam vlastností, které jsou podporovány zdrojem relačních zdrojů.

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
> "__c" část názvu rozhraní API je potřeba pro všechny vlastní objekt.

![Factory dat - Salesforce připojení - název rozhraní API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)


### <a name="type-mapping-for-salesforce"></a>Mapování typů pro salesforce

| Typ salesforce | . Typ založený na NET |
| --- | --- |
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

> [!NOTE]
> Pokud chcete mapovat sloupce ze zdrojové datové sady na sloupce z datové sady jímky, přečtěte si témat [mapování sloupců datových sad v Azure Data Factory](data-factory-map-columns.md).

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Výkon a ladění
V průvodci [výkonem a laděním aktivity kopírování](data-factory-copy-activity-performance.md) najdete informace o klíčových faktorech, které ovlivňují výkon přesunu dat (aktivita kopírování) ve službě Azure Data Factory, a o různých způsobech jeho optimalizace.
