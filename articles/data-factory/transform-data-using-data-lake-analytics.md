---
title: Transformace dat pomocí skriptu U-SQL - Azure | Microsoft Docs
description: Informace o zpracování nebo transformace dat pomocí spouštění skriptů U-SQL na výpočetní služba Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: abnarain
ms.openlocfilehash: 7e55249ed187ad9fa74a39634bbb254c9b0b8b8e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Transformace dat pomocí spouštění skriptů U-SQL v Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](v1/data-factory-usql-activity.md)
> * [Verze 2 – Preview](transform-data-using-data-lake-analytics.md)

Kanál v objektu pro vytváření dat Azure zpracovává data v služby propojené úložiště pomocí propojené výpočetní služby. Obsahuje posloupnost aktivit, kde každá aktivita provede konkrétní zpracování operace. Tento článek popisuje **Data Lake Analytics U-SQL aktivity** , která se spouští **U-SQL** skript na **Azure Data Lake Analytics** výpočetní propojené služby. 

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [USQL aktivity v V1](v1/data-factory-usql-activity.md).

Vytvoření účtu Azure Data Lake Analytics před vytvořením kanálu s aktivitou Data Lake Analytics U-SQL. Další informace o Azure Data Lake Analytics najdete v tématu [Začínáme s Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md).


## <a name="azure-data-lake-analytics-linked-service"></a>Služba Azure Data Lake Analytics propojené
Vytvoříte **Azure Data Lake Analytics** propojená služba Azure Data Lake Analytics výpočetní služby s objektem pro vytváření dat Azure. Data Lake Analytics U-SQL aktivitu v kanálu odkazuje na tato propojená služba. 

Následující tabulka obsahuje popis obecné vlastnosti používané v definici JSON. 

| Vlastnost                 | Popis                              | Požaduje se                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **Typ**                 | Vlastnost typu musí být nastavená na: **AzureDataLakeAnalytics**. | Ano                                      |
| **accountName**          | Název účtu Azure Data Lake Analytics.  | Ano                                      |
| **dataLakeAnalyticsUri** | Identifikátor URI služby Azure Data Lake Analytics.           | Ne                                       |
| **subscriptionId**       | ID předplatného Azure                    | Ne (když není určeno, předplatné objektu pro vytváření dat se používá). |
| **resourceGroupName**    | Název skupiny prostředků Azure.                | Ne (když není určeno, skupinu prostředků objektu pro vytváření dat se používá). |

### <a name="service-principal-authentication"></a>Ověřování instančních objektů
Azure Data Lake Analytics propojená služba vyžaduje, aby objekt zabezpečení ověřování služby pro připojení ke službě Azure Data Lake Analytics. Pokud chcete použít ověřování hlavní služby, zaregistrujte entitu aplikace v Azure Active Directory (Azure AD) a jí udělit přístup k Data Lake Analytics a Data Lake Store, používá. Podrobné pokyny najdete v tématu [Service-to-service ověřování](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Poznamenejte si následující hodnoty, které můžete použít k definování propojené služby:

* ID aplikace
* Klíč aplikace 
* ID tenanta

Udělení oprávnění objektu služby k Azure Data Lake Anatlyics pomocí [Průvodce přidáním uživatele](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#add-a-new-user).

Použijte objekt zabezpečení ověřování služby tak, že zadáte následující vlastnosti:

| Vlastnost                | Popis                              | Požaduje se |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Zadejte ID aplikace klienta.     | Ano      |
| **servicePrincipalKey** | Zadejte klíč aplikace.           | Ano      |
| **Klienta**              | Zadejte informace o klienta (název nebo klienta domény ID) v rámci které se nachází aplikace. Můžete ji načíst podržením ukazatele myši v pravém horním rohu portálu Azure. | Ano      |

**Příkladu: Ověření objektu službu**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "<account name>",
            "dataLakeAnalyticsUri": "<azure data lake analytics URI>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant ID>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }       
    }
}
```

Další informace o propojené služby, najdete v části [výpočetní propojené služby](compute-linked-services.md).

## <a name="data-lake-analytics-u-sql-activity"></a>Aktivita U-SQL služby Data Lake Analytics
Následující fragment kódu JSON definuje kanál s aktivitou Data Lake Analytics U-SQL. Definici aktivity odkazuje na Azure Data Lake Analytics propojené služby, kterou jste vytvořili dříve. Pro spuštění skriptu Data Lake Analytics U-SQL, odešle Data Factory skriptu, který jste zadali do Data Lake Analytics a požadované vstupy a výstupy je definované ve skriptu pro Data Lake Analytics pro načtení a výstup. 

```json
{
    "name": "ADLA U-SQL Activity",
    "description": "description",
    "type": "DataLakeAnalyticsU-SQL",
    "linkedServiceName": {
        "referenceName": "<linked service name of Azure Data Lake Analytics>",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "<linked service name of Azure Data Lake Store or Azure Storage which contains the U-SQL script>",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
        "degreeOfParallelism": 3,
        "priority": 100,
        "parameters": {
            "in": "/datalake/input/SearchLog.tsv",
            "out": "/datalake/output/Result.tsv"
        }
    }   
}
```

Následující tabulka popisuje názvy a popisy vlastností, které jsou specifické pro tuto aktivitu. 

| Vlastnost            | Popis                              | Požaduje se |
| :------------------ | :--------------------------------------- | :------- |
| jméno                | Název aktivity v kanálu     | Ano      |
| description         | Popisuje, jakým způsobem aktivita naloží text.  | Ne       |
| type                | Aktivity Data Lake Analytics U-SQL, je typ aktivity **DataLakeAnalyticsU SQL**. | Ano      |
| linkedServiceName   | Propojená služba Azure Data Lake Analytics. Další informace o této propojené služby najdete v tématu [výpočetní propojené služby](compute-linked-services.md) článku.  |Ano       |
| scriptPath          | Cesta ke složce, který obsahuje skript U-SQL. Název souboru je malá a velká písmena. | Ano      |
| scriptLinkedService | Propojená služba, která odkazuje **Azure Data Lake Store** nebo **Azure Storage** obsahující skriptu pro vytváření dat. | Ano      |
| degreeOfParallelism | Maximální počet uzlů současně slouží ke spuštění úlohy. | Ne       |
| priorita            | Určuje, jaké úlohy mimo všechny, které jsou zařazeny do fronty, měla by být vybrána má spustit jako první. Čím nižší je číslo, tím vyšší je priorita. | Ne       |
| parameters          | Parametry k předání do skriptu U-SQL.    | Ne       |
| runtimeVersion      | Verze runtime – stroje U-SQL používat. | Ne       |
| compilationMode     | <p>Režim kompilace U-SQL. Musí mít jednu z těchto hodnot: **Semantic:** provádět jenom sémantického kontroly a kontrola nezbytné vhodnosti **úplné:** provést úplné kompilace, včetně kontrola syntaxe, optimalizace, generování kódu atd., **SingleBox:** provést úplné kompilace s TargetType nastavení SingleBox. Pokud nezadáte hodnotu pro tuto vlastnost, server určí režim optimální kompilace. | Ne |

Objekt pro vytváření dat odešle najdete [definice skriptu SearchLogProcessing.txt](#sample-u-sql-script) pro definici skriptu. 

## <a name="sample-u-sql-script"></a>Ukázkový skript U-SQL

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int,
            Urls            string,
            ClickedUrls     string
    FROM @in
    USING Extractors.Tsv(nullEscape:"#NULL#");

@rs1 =
    SELECT Start, Region, Duration
    FROM @searchlog
WHERE Region == "en-gb";

@rs1 =
    SELECT Start, Region, Duration
    FROM @rs1
    WHERE Start <= DateTime.Parse("2012/02/19");

OUTPUT @rs1   
    TO @out
      USING Outputters.Tsv(quoting:false, dateTimeFormat:null);
```

Ve výše příklad skriptu, vstup a výstup skriptu je definována v **@in** a **@out** parametry. Hodnoty pro **@in** a **@out** parametry ve skriptu U-SQL jsou předávány dynamicky službou Data Factory pomocí části parametry.". 

Také můžete zadat další vlastnosti, například degreeOfParallelism a priority v definici vaší kanálu pro úlohy, které běží na službu Azure Data Lake Analytics.

## <a name="dynamic-parameters"></a>Dynamické parametry
V definici ukázkový kanál a odhlašování parametry jsou přiřazeny pevně definovaných hodnot. 

```json
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

Je možné místo toho použít dynamické parametry. Příklad: 

```json
"parameters": {
    "in": "/datalake/input/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/data.tsv",
    "out": "/datalake/output/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/result.tsv"
}
```

V takovém případě vstupní soubory jsou stále zachyceny ze složky /datalake/input a výstupní soubory se generují ve složce /datalake/output. Názvy souborů jsou dynamické podle času zahájení okna předávány v při získá aktivaci kanálu.  

## <a name="next-steps"></a>Další postup
Najdete v následujících článcích, které vysvětlují, jak k transformaci dat jinými způsoby: 

* [Aktivita Hive](transform-data-using-hadoop-hive.md)
* [Pig aktivity](transform-data-using-hadoop-pig.md)
* [Činnost MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streamované aktivitě](transform-data-using-hadoop-streaming.md)
* [Spark aktivity](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning dávkového spuštění aktivity](transform-data-using-machine-learning.md)
* [Aktivita uložené procedury](transform-data-using-stored-procedure.md)
