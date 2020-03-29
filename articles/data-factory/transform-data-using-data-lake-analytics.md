---
title: Transformace dat pomocí skriptu U-SQL
description: Zjistěte, jak zpracovat nebo transformovat data spuštěním skriptů U-SQL ve výpočetní službě Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2018
ms.openlocfilehash: 257c71f7994b889540ec8cc5d0f384f3f8894f4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74913272"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Transformace dat spuštěním skriptů U-SQL v Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-usql-activity.md)
> * [Aktuální verze](transform-data-using-data-lake-analytics.md)

Kanál ve společnosti Azure pro data zpracovává data v propojených službách úložiště pomocí propojených výpočetních služeb. Obsahuje posloupnost aktivit, kde každá aktivita provádí určitou operaci zpracování. Tento článek popisuje **aktivitu U-SQL analýzy datového jezera,** která spouští skript **U-SQL** na výpočetní propojené službě **Azure Data Lake Analytics.** 

Před vytvořením kanálu s aktivitou U-SQL analýzy datového jezera si vytvořte účet Azure Data Lake Analytics. Další informace o Azure Data Lake Analytics najdete [v tématu Začínáme s Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md).


## <a name="azure-data-lake-analytics-linked-service"></a>Propojená služba Azure Data Lake Analytics
Vytvoříte propojenou službu **Azure Data Lake Analytics,** která propojí výpočetní službu Azure Data Lake Analytics s toto datovou továrnou Azure. Aktivita U-SQL služby Data Lake Analytics v kanálu odkazuje na tuto propojenou službu. 

Následující tabulka obsahuje popis obecných vlastností použitých v definici JSON. 

| Vlastnost                 | Popis                              | Požaduje se                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **Typ**                 | Vlastnost type by měla být nastavena na: **AzureDataLakeAnalytics**. | Ano                                      |
| **název_účtu**          | Název účtu Azure Data Lake Analytics.  | Ano                                      |
| **dataLakeAnalyticsUri** | Identifikátor URI analýzy datového jezera Azure.           | Ne                                       |
| **subscriptionId**       | ID předplatného Azure                    | Ne                                       |
| **resourceGroupName**    | Název skupiny prostředků Azure                | Ne                                       |

### <a name="service-principal-authentication"></a>Ověřování instančních objektů
Propojená služba Azure Data Lake Analytics vyžaduje pro připojení ke službě Azure Data Lake Analytics ověření uhlavního uživatele služby. Pokud chcete použít ověřování jistiny služby, zaregistrujte entitu aplikace ve službě Azure Active Directory (Azure AD) a udělte jí přístup k službě Data Lake Analytics i úložišti Data Lake Store, které používá. Podrobné kroky naleznete v [tématu Ověřování mezi službami](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Poznamenejte si následující hodnoty, které používáte k definování propojené služby:

* ID aplikace
* Klíč aplikace 
* ID tenanta

Udělte oprávnění k primárnímu objektu služby vašemu Aatlyics datového jezera Azure pomocí [Průvodce přidáním uživatele](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#add-a-new-user).

Pomocí ověřování instančního objektu zadejte následující vlastnosti:

| Vlastnost                | Popis                              | Požaduje se |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Zadejte ID klienta aplikace.     | Ano      |
| **servicePrincipalKey** | Zadejte klíč aplikace.           | Ano      |
| **Nájemce**              | Zadejte informace o klientovi (název domény nebo ID klienta), pod kterým se aplikace nachází. Můžete načíst na jenom myši v pravém horním rohu portálu Azure. | Ano      |

**Příklad: Ověřování instančního objektu**
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

Další informace o propojené službě najdete v [tématu Výpočetní propojené služby](compute-linked-services.md).

## <a name="data-lake-analytics-u-sql-activity"></a>Aktivita U-SQL služby Data Lake Analytics
Následující fragment JSON definuje kanál s aktivitou U-SQL analýzy datového jezera. Definice aktivity obsahuje odkaz na propojenou službu Azure Data Lake Analytics, kterou jste vytvořili dříve. Chcete-li spustit skript U-SQL analýzy data, data factory odešle skript, který jste zadali do Data Lake Analytics a požadované vstupy a výstupy je definovánve skriptu pro Data Lake Analytics pro načtení a výstup. 

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
| description         | Text popisující, co aktivita dělá.  | Ne       |
| type                | Pro aktivitu U-SQL analýzy datového jezera je typem **aktivity DataLakeAnalyticsU-SQL**. | Ano      |
| linkedServiceName   | Propojená služba s Azure Data Lake Analytics. Další informace o této propojené službě najdete v článku [Výpočetní propojené služby.](compute-linked-services.md)  |Ano       |
| scriptPath          | Cesta ke složce, která obsahuje skript U-SQL. Název souboru rozlišuje malá a velká písmena. | Ano      |
| skriptLinkedService | Propojená služba, která propojuje **Azure Data Lake Store** nebo Azure **Storage,** který obsahuje skript, s toutou továrnou dat | Ano      |
| degreeOfParallelism | Maximální počet uzlů současně používaných ke spuštění úlohy. | Ne       |
| Prioritou            | Určuje, které úlohy ze všech, které jsou zařazeny do fronty, by měly být vybrány jako první. Čím nižší číslo, tím vyšší priorita. | Ne       |
| parameters          | Parametry pro předání do skriptu U-SQL.    | Ne       |
| runtimeVersion      | Runtime verze modulu U-SQL použít. | Ne       |
| Compilationmode     | <p>Režim kompilace U-SQL. Musí být jedna z těchto hodnot: **Sémantické:** Pouze provádět sémantické kontroly a nezbytné kontroly příčetnosti, **Úplné:** Provést úplnou kompilaci, včetně kontroly syntaxe, optimalizace, generování kódu, atd., **SingleBox:** Proveďte úplnou kompilaci, s TargetType nastavení SingleBox. Pokud nezadáte hodnotu pro tuto vlastnost, server určí optimální režim kompilace. | Ne |

Definice skriptu najdete v souboru [SearchLogProcessing.txt.](#sample-u-sql-script) 

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

V příkladu výše skriptu je vstup a výstup do skriptu definován ** \@v** parametrech in a ** \@out.** Hodnoty pro ** \@** parametry dovnitř a ** \@ven** ve skriptu U-SQL jsou dynamicky předávány pomocí data factory pomocí sekce 'parameters'. 

Můžete zadat další vlastnosti, jako je například degreeOfParallelism a priority také v definici kanálu pro úlohy, které běží ve službě Azure Data Lake Analytics.

## <a name="dynamic-parameters"></a>Dynamické parametry
V definici ukázkového kanálu jsou parametry dovnitř a ven přiřazeny s pevně zakódovanými hodnotami. 

```json
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

Místo toho je možné použít dynamické parametry. Například: 

```json
"parameters": {
    "in": "/datalake/input/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/data.tsv",
    "out": "/datalake/output/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/result.tsv"
}
```

V tomto případě jsou vstupní soubory stále vyzvednuty ze složky /datalake/input a výstupní soubory jsou generovány ve složce /datalake/output. Názvy souborů jsou dynamické na základě času zahájení okna, který je předán při aktivaci kanálu.  

## <a name="next-steps"></a>Další kroky
Další články vysvětlují, jak transformovat data jinými způsoby: 

* [Aktivita úlu](transform-data-using-hadoop-hive.md)
* [Aktivita prasat](transform-data-using-hadoop-pig.md)
* [Aktivita MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Aktivita streamování hadoopu](transform-data-using-hadoop-streaming.md)
* [Aktivita jiskry](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Aktivita dávkového spuštění strojového učení](transform-data-using-machine-learning.md)
* [Aktivita uložené procedury](transform-data-using-stored-procedure.md)
