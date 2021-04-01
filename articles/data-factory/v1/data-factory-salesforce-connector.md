---
title: Přesun dat ze služby Salesforce pomocí Data Factory
description: Přečtěte si informace o tom, jak přesouvat data z Salesforce pomocí Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: ccc20f415d13356de755af5d1d3afc5b29de72f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100387049"
---
# <a name="move-data-from-salesforce-by-using-azure-data-factory"></a>Přesun dat ze služby Salesforce pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](data-factory-salesforce-connector.md)
> * [Verze 2 (aktuální verze)](../connector-salesforce.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [konektor Salesforce ve verzi v2](../connector-salesforce.md).

Tento článek popisuje, jak můžete pomocí aktivity kopírování v datové továrně Azure kopírovat data z Salesforce do libovolného úložiště dat, které je uvedené pod sloupcem jímky v tabulce [podporovaných zdrojů a jímky](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Tento článek se týká článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , který prezentuje obecný přehled přesunu dat s aktivitou kopírování a podporovanými kombinacemi úložiště dat.

Azure Data Factory aktuálně podporuje pouze přesun dat z Salesforce na [podporovaná úložiště dat jímky](data-factory-data-movement-activities.md#supported-data-stores-and-formats), ale nepodporuje přesun dat z jiných úložišť dat do Salesforce.

## <a name="supported-versions"></a>Podporované verze
Tento konektor podporuje následující edice Salesforce: Developer Edition, Professional Edition, Enterprise Edition nebo Unlimited Edition. A podporuje kopírování z výroby, izolovaného prostoru a vlastní domény Salesforce.

## <a name="prerequisites"></a>Požadavky
* Musí být povolené oprávnění API. Viz [návody povolení přístupu k rozhraní API v Salesforce pomocí sady oprávnění?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)
* Pokud chcete kopírovat data z Salesforce do místních úložišť dat, musíte mít ve svém místním prostředí nainstalovanou aspoň Správa dat bránu 2,0.

## <a name="salesforce-request-limits"></a>Omezení požadavku Salesforce
Salesforce má omezení pro celkové požadavky rozhraní API i pro souběžné požadavky rozhraní API. Je třeba počítat s následujícím:

- Pokud počet souběžných požadavků překročí limit, dojde k omezení a zobrazí se náhodná selhání.
- Pokud celkový počet požadavků překročí limit, účet Salesforce se zablokuje na 24 hodin.

V obou scénářích se může zobrazit také chyba "REQUEST_LIMIT_EXCEEDED". Podrobnosti najdete v části omezení požadavků na rozhraní API v článku [omezení pro vývojáře Salesforce](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) .

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesouvá data z Salesforce pomocí různých nástrojů nebo rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat najdete v tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) .

K vytvoření kanálu můžete také použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API** a **REST API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v [kurzu kopírování aktivit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Bez ohledu na to, jestli používáte nástroje nebo rozhraní API, provedete následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **propojené služby** , které propojí vstupní a výstupní úložiště dat s datovou továrnou.
2. Vytvořte datové **sady** , které reprezentují vstupní a výstupní data pro operaci kopírování.
3. Vytvořte **kanál** s aktivitou kopírování, která převezme datovou sadu jako vstup a datovou sadu jako výstup.

Při použití Průvodce se automaticky vytvoří definice JSON pro tyto Entity Data Factory (propojené služby, datové sady a kanál). Pokud používáte nástroje/rozhraní API (s výjimkou rozhraní .NET API), definujete tyto Data Factory entit pomocí formátu JSON. Ukázku s definicemi JSON pro Data Factory entity, které se používají ke kopírování dat z Salesforce, najdete v části [JSON example: kopírování dat z Salesforce do Azure Blob](#json-example-copy-data-from-salesforce-to-azure-blob) tohoto článku.

Následující části obsahují podrobné informace o vlastnostech JSON, které se používají k definování Data Factory entit specifických pro Salesforce:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka uvádí popisy pro prvky JSON, které jsou specifické pro propojenou službu Salesforce.

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| typ |Vlastnost Type musí být nastavená na: **Salesforce**. |Yes |
| environmentUrl | Zadejte adresu URL instance Salesforce. <br><br> -Výchozí hodnota je https: \/ /Login.Salesforce.com. <br> – Chcete-li kopírovat data z izolovaného prostoru, zadejte " https://test.salesforce.com ". <br> – Chcete-li kopírovat data z vlastní domény, zadejte například "https://[doména]. my. Salesforce. com". |No |
| username |Zadejte uživatelské jméno pro uživatelský účet. |Yes |
| heslo |Zadejte heslo pro uživatelský účet. |Yes |
| Element |Zadejte token zabezpečení pro uživatelský účet. Pokyny, jak resetovat nebo získat token zabezpečení, najdete v tématu [získání tokenu zabezpečení](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) . Obecné informace o tokenech zabezpečení najdete v tématu [zabezpečení a rozhraní API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Yes |

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [vytvoření datových sad](data-factory-create-datasets.md) . Oddíly, jako je například struktura, dostupnost a zásada pro datovou sadu JSON, jsou podobné pro všechny typy datových sad (Azure SQL, Azure Blob, tabulka Azure atd.).

Oddíl **typeProperties** se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. Oddíl typeProperties pro datovou sadu **relačního** typu má následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| tableName |Název tabulky v Salesforce. |Ne (Pokud je zadán **dotaz** na **RelationalSource** ) |

> [!IMPORTANT]
> Část "__c" názvu rozhraní API je nutná pro libovolný vlastní objekt.

![Snímek obrazovky zobrazuje podrobnosti definice vlastního objektu, kde můžete zobrazit názvy vlastních objektů A P I.](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [vytvoření kanálů](data-factory-create-pipelines.md) . K dispozici jsou vlastnosti, jako je název, popis, vstupní a výstupní tabulka a různé zásady, a to pro všechny typy aktivit.

Vlastnosti, které jsou k dispozici v části typeProperties aktivity, se liší podle typu jednotlivých aktivit. U aktivity kopírování se liší v závislosti na typech zdrojů a jímky.

Pokud je zdroj v aktivitě kopírování typu **RelationalSource** (který zahrnuje Salesforce), jsou v části typeProperties k dispozici následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| query |Pomocí vlastního dotazu můžete číst data. |Dotaz SQL-92 nebo dotaz na [SOQL (Salesforce Object Query Language)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) . Příklad: `select * from MyTable__c`. |Ne (Pokud je zadaný **TableName** **objektu DataSet** ) |

> [!IMPORTANT]
> Část "__c" názvu rozhraní API je nutná pro libovolný vlastní objekt.

![Snímek obrazovky zobrazuje vlastní pole & vztahy, kde vidíte názvy vlastních objektů A P I.](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## <a name="query-tips"></a>Tipy pro dotazy
### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>Načítání dat pomocí klauzule WHERE ve sloupci DateTime
Když zadáte SOQL nebo SQL dotaz, věnujte pozornost rozdílům ve formátu data a času. Například:

* **Ukázka SOQL**: `$$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', WindowStart, WindowEnd)`
* **Ukázka SQL**:
    * **Zadání dotazu pomocí Průvodce kopírováním:**`$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)`
    * **Použití úprav JSON k zadání dotazu (řídicí znak je řádně):**`$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', WindowStart, WindowEnd)`

### <a name="retrieving-data-from-salesforce-report"></a>Načítání dat ze sestavy Salesforce
Data ze sestav Salesforce můžete načíst zadáním dotazu jako například `{call "<report name>"}` . `"query": "{call \"TestReport\"}"`.

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>Načítání odstraněných záznamů ze odpadkového koše Salesforce
Chcete-li zadat dotaz na nepodmíněné odstraněné záznamy ze složky Koš služby Salesforce, můžete v dotazu zadat **"IsDeleted" = 1** . Třeba

* Pokud chcete zadat dotaz jenom na odstraněné záznamy, zadejte SELECT * FROM MyTable__c, **kde IsDeleted = 1**.
* Chcete-li zadat dotaz na všechny záznamy včetně existujících a odstraněných, zadejte "SELECT * FROM MyTable__c, **kde IsDeleted = 0 nebo IsDeleted = 1**"

## <a name="json-example-copy-data-from-salesforce-to-azure-blob"></a>Příklad JSON: kopírování dat ze Salesforce do Azure Blob
Následující příklad poskytuje ukázkové definice JSON, které lze použít k vytvoření kanálu pomocí sady [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ukazují, jak kopírovat data z Salesforce do Azure Blob Storage. Data však lze zkopírovat do kterékoli z těchto umyvadel, které jsou [zde](data-factory-data-movement-activities.md#supported-data-stores-and-formats) uvedeny, pomocí aktivity kopírování v Azure Data Factory.

Tady jsou Data Factory artefakty, které budete muset vytvořit pro implementaci scénáře. Podrobné informace o těchto krocích najdete v oddílech uvedených v seznamu.

* Propojená služba typu [Salesforce](#linked-service-properties)
* Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Vstupní [datová sada](data-factory-create-datasets.md) [relačního](#dataset-properties) typu
* Výstupní [datová sada](data-factory-create-datasets.md) typu [azureblobu](data-factory-azure-blob-connector.md#dataset-properties)
* [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [RelationalSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

**Propojená Služba Salesforce**

V tomto příkladu se používá propojená služba **Salesforce** . Vlastnosti podporované touto propojenou službou najdete v části [propojená Služba Salesforce](#linked-service-properties) . Pokyny, jak resetovat nebo získat token zabezpečení, najdete v tématu [získání tokenu zabezpečení](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) .

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

Nastavením možnosti **externí** na **hodnotu true** dojde k informování služby Data Factory, že datová sada je pro objekt pro vytváření dat externá a není vytvořená aktivitou v datové továrně.

> [!IMPORTANT]
> Část "__c" názvu rozhraní API je nutná pro libovolný vlastní objekt.

![Snímek obrazovky se zobrazí v podrobnostech o definici vlastního objektu, kde můžete zobrazit popisek jednotného přidaných dat, popisek plural, název objektu a název P.](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

**Výstupní datová sada Azure Blob**

Data se zapisují do nového objektu BLOB každou hodinu (frekvence: hodina, interval: 1).

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

Kanál obsahuje aktivitu kopírování, která je nakonfigurovaná tak, aby používala vstupní a výstupní datové sady a je naplánované spuštění každou hodinu. V definici JSON kanálu je typ **zdroje** nastavený na **RelationalSource** a typ **jímky** je nastavený na **BlobSink**.

Seznam vlastností, které jsou podporovány rozhraním RelationalSource, naleznete v tématu [vlastnosti typu RelationalSource](#copy-activity-properties) .

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
> Část "__c" názvu rozhraní API je nutná pro libovolný vlastní objekt.

![Snímek obrazovky zobrazuje vlastní pole & relace s názvy A P s názvem.](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)


### <a name="type-mapping-for-salesforce"></a>Mapování typů pro Salesforce

| Typ Salesforce | . Typ založený na síti |
| --- | --- |
| Automatické číslo |Řetězec |
| Zaškrtávací políčko |Logická hodnota |
| Měna |Decimal |
| Date (Datum) |DateTime |
| Datum/Čas |DateTime |
| E-mail |Řetězec |
| Id |Řetězec |
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

> [!NOTE]
> Chcete-li mapovat sloupce ze zdrojové datové sady na sloupce z datové sady jímky, přečtěte si téma [mapování sloupců datové sady v Azure Data Factory](data-factory-map-columns.md).

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Výkon a ladění
Informace o klíčových faktorech, které mají vliv na výkon přesunu dat (aktivita kopírování) v Azure Data Factory a různých způsobech jejich optimalizace, najdete v [Průvodci výkonem a optimalizací aktivity kopírování](data-factory-copy-activity-performance.md) .
