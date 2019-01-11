---
title: Transformace dat pomocí skriptu U-SQL – Azure | Dokumentace Microsoftu
description: Zjistěte, jak zpracovat nebo transformovat data spuštěním skriptů U-SQL na výpočetní služby Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: abnarain
ms.openlocfilehash: 1874473b3ad091ce1da0a48367548cd4432737a2
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54016490"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Transformovat data spuštěním skriptů U-SQL v Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-usql-activity.md)
> * [Aktuální verze](transform-data-using-data-lake-analytics.md)

Kanál v objektu pro vytváření dat Azure zpracovává data v propojené služby s využitím propojené výpočetní služby. Obsahuje posloupnost aktivit, kde každá aktivita vykonává zpracování specifické pro operaci. Tento článek popisuje **aktivita U-SQL služby Data Lake Analytics** , který běží **U-SQL** skript na **Azure Data Lake Analytics** výpočetní propojenou službu. 

Před vytvořením kanálu s aktivitou Data Lake Analytics U-SQL vytvořte účet Azure Data Lake Analytics. Další informace o Azure Data Lake Analytics najdete v tématu [Začínáme s Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md).


## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics propojené služby
Vytvoření **Azure Data Lake Analytics** propojená služba Azure Data Lake Analytics výpočetní služby Azure data Factory. Aktivita Data Lake Analytics U-SQL v kanálu odkazuje na tuto propojenou službu. 

Následující tabulka obsahuje popis obecných vlastností použitých v definici JSON. 

| Vlastnost                 | Popis                              | Požaduje se                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **type**                 | Vlastnost type by měla být nastavená na: **AzureDataLakeAnalytics**. | Ano                                      |
| **accountName**          | Název účtu Azure Data Lake Analytics.  | Ano                                      |
| **dataLakeAnalyticsUri** | Identifikátor URI, Azure Data Lake Analytics.           | Ne                                       |
| **ID předplatného**       | ID předplatného Azure                    | Ne                                       |
| **resourceGroupName**    | Název skupiny prostředků Azure                | Ne                                       |

### <a name="service-principal-authentication"></a>Ověřování instančních objektů
Azure Data Lake Analytics propojená služba vyžaduje, aby ověřování instančního objektu služby a připojení ke službě Azure Data Lake Analytics. Pokud chcete používat ověřování instančních objektů, entity aplikaci zaregistrovat ve službě Azure Active Directory (Azure AD) a jí udělit přístup k Data Lake Analytics a na Data Lake Store používá. Podrobné pokyny najdete v článku [ověřování služba služba](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Poznamenejte si následující hodnoty, které slouží k definování propojené služby:

* ID aplikace
* Klíč aplikace 
* ID tenanta

Udělení oprávnění instančního objektu služby k Azure Data Lake Anatlyics pomocí [Průvodce přidáním uživatele](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#add-a-new-user).

Použijte ověřování instančních objektů zadáním následující vlastnosti:

| Vlastnost                | Popis                              | Požaduje se |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Zadejte ID klienta vaší aplikace.     | Ano      |
| **servicePrincipalKey** | Zadejte klíč aplikace.           | Ano      |
| **tenanta**              | Zadejte informace o tenantovi (domény ID tenanta nebo název) v rámci které se nachází vaše aplikace. Podržením ukazatele myši v pravém horním rohu webu Azure portal můžete načíst ji. | Ano      |

**Příklad: Ověřování instančních objektů**
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
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
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

Další informace o propojenou službu, najdete v článku [propojené služby Compute](compute-linked-services.md).

## <a name="data-lake-analytics-u-sql-activity"></a>Aktivita U-SQL služby Data Lake Analytics
Následující fragment kódu JSON definuje kanál s aktivitou Data Lake Analytics U-SQL. Definice aktivity obsahuje odkaz na službu Azure Data Lake Analytics propojené, kterou jste vytvořili dříve. Ke spuštění skriptu Data Lake Analytics U-SQL, Data Factory odešle skript, který jste zadali pro Data Lake Analytics a vstupy a výstupy je definováno ve skriptu pro Data Lake Analytics k načtení a výstupu. 

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
| description         | Text popisující, jakým způsobem aktivita naloží.  | Ne       |
| type                | Aktivita Data Lake Analytics U-SQL je typ aktivity **DataLakeAnalyticsU SQL**. | Ano      |
| linkedServiceName   | Propojená služba Azure Data Lake Analytics. Další informace o tuto propojenou službu, najdete v článku [propojené služby Compute](compute-linked-services.md) článku.  |Ano       |
| ScriptPath          | Cesta ke složce, která obsahuje skript U-SQL. Název souboru je velká a malá písmena. | Ano      |
| scriptLinkedService | Propojené služby, která odkazuje **Azure Data Lake Store** nebo **služby Azure Storage** , který obsahuje skript do služby data factory | Ano      |
| z degreeOfParallelism | Maximální počet uzlů najednou použitý ke spuštění úlohy. | Ne       |
| priorita            | Určuje, které z uložených ve frontě úloh by měl být vybrané ke spuštění první. Čím nižší je číslo, tím vyšší je priorita. | Ne       |
| parameters          | Parametry se mají předat do skriptu U-SQL.    | Ne       |
| runtimeVersion      | Verze modulu runtime U-SQL stroje používat. | Ne       |
| Vlastnost CompilationMode     | <p>Režim kompilace U-SQL. Musí být jedna z těchto hodnot: **Sémantické:** Pouze provádění sémantického kontroly a kontrol správnosti nezbytné **úplné:** Provést úplná kompilace, jako je kontrola syntaxe, optimalizace, generování kódu, atd., **SingleBox:** Proveďte úplná kompilace s TargetType nastavení SingleBox. Pokud nezadáte hodnotu pro tuto vlastnost, server určuje režim kompilace optimální. | Ne |

Data Factory odešle viz [definice skriptu SearchLogProcessing.txt](#sample-u-sql-script) pro definici skriptu. 

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

Ve výše ukázkový skript, vstup a výstup skriptu je definován v **@in** a **@out** parametry. Hodnoty pro **@in** a **@out** parametry ve skriptu U-SQL jsou předávány dynamicky službou Data Factory pomocí sekci "parametrů". 

Také můžete zadat další vlastnosti, například z degreeOfParallelism a priority v definici kanálu pro úlohy, které běží ve službě Azure Data Lake Analytics.

## <a name="dynamic-parameters"></a>Dynamické parametry
Ukázková definice kanálu a parametry se přiřazují s pevně definovaných hodnot. 

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

V tomto případě vstupních souborů i nadále, vyberou se ze složky /datalake/input a výstupní soubory jsou vygenerovány ve složce /datalake/output. Názvy souborů jsou dynamické vzorce založené na čase začátku okna předávaný v případě získá aktivuje kanál.  

## <a name="next-steps"></a>Další postup
Viz následující články, které vysvětlují, jak transformovat data dalšími způsoby: 

* [Aktivita hivu](transform-data-using-hadoop-hive.md)
* [Aktivita pig](transform-data-using-hadoop-pig.md)
* [Aktivita MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Aktivita streamování Hadoop](transform-data-using-hadoop-streaming.md)
* [Aktivita Spark](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Aktivita provedení dávky služby Learning počítače](transform-data-using-machine-learning.md)
* [Aktivita uložená procedura](transform-data-using-stored-procedure.md)
