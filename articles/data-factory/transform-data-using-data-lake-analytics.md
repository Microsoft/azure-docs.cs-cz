---
title: Transformace dat pomocí skriptu U-SQL
description: Naučte se zpracovávat nebo transformovat data spuštěním skriptů U-SQL ve službě Azure Data Lake Analytics Compute.
services: data-factory
documentationcenter: ''
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.openlocfilehash: cb0ff5d93afc0941faa84028ad6454371cd0442c
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893888"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Transformace dat spuštěním skriptů U-SQL na Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-usql-activity.md)
> * [Aktuální verze](transform-data-using-data-lake-analytics.md)

Kanál v datové továrně Azure zpracovává data v propojených službách úložiště pomocí propojených výpočetních služeb. Obsahuje posloupnost aktivit, kde každá aktivita provádí určitou operaci zpracování. Tento článek popisuje **aktivitu Data Lake Analytics u-SQL** , která spouští skript **U-SQL** v propojené službě **Azure Data Lake Analytics** Compute. 

Vytvořte účet Azure Data Lake Analytics před vytvořením kanálu s aktivitou Data Lake Analytics U-SQL. Další informace o Azure Data Lake Analytics najdete v tématu [Začínáme s Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md).


## <a name="azure-data-lake-analytics-linked-service"></a>Propojená služba Azure Data Lake Analytics
Vytvoříte propojenou službu **Azure Data Lake Analytics** pro propojení Azure Data Lake Analytics výpočetní služby s objektem pro vytváření dat Azure. Aktivita Data Lake Analytics U-SQL v kanálu odkazuje na tuto propojenou službu. 

Následující tabulka uvádí popisy obecných vlastností použitých v definici JSON. 

| Vlastnost                 | Popis                              | Požaduje se                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **type**                 | Vlastnost Type by měla být nastavená na: **AzureDataLakeAnalytics**. | Ano                                      |
| **accountName**          | Azure Data Lake Analytics název účtu.  | Ano                                      |
| **dataLakeAnalyticsUri** | Azure Data Lake Analytics identifikátor URI.           | Ne                                       |
| **subscriptionId**       | ID předplatného Azure                    | Ne                                       |
| **resourceGroupName**    | Název skupiny prostředků Azure                | Ne                                       |

### <a name="service-principal-authentication"></a>Ověřování instančních objektů
Propojená služba Azure Data Lake Analytics vyžaduje pro připojení ke službě Azure Data Lake Analytics ověřování instančního objektu. Pokud chcete použít ověřování instančního objektu, zaregistrujte entitu aplikace ve službě Azure Active Directory (Azure AD) a udělte jí přístup k Data Lake Analytics i Data Lake Store, kterou používá. Podrobné pokyny najdete v článku [ověřování služba služba](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Poznamenejte si následující hodnoty, které slouží k definování propojené služby:

* ID aplikace
* Klíč aplikace 
* ID tenanta

Udělte instančnímu objektu oprávnění k vašemu Azure Data Lake Anatlyics pomocí [Průvodce přidáním uživatele](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#add-a-new-user).

Použijte ověřování instančního objektu zadáním následujících vlastností:

| Vlastnost                | Popis                              | Požaduje se |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Zadejte ID klienta vaší aplikace.     | Ano      |
| **servicePrincipalKey** | Zadejte klíč aplikace.           | Ano      |
| **tenant**              | Zadejte informace o tenantovi (domény ID tenanta nebo název) v rámci které se nachází vaše aplikace. Podržením ukazatele myši v pravém horním rohu webu Azure portal můžete načíst ji. | Ano      |

**Příklad: ověřování instančního objektu**
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

Další informace o propojené službě najdete v tématu věnovaném [COMPUTE propojeným službám](compute-linked-services.md).

## <a name="data-lake-analytics-u-sql-activity"></a>Aktivita U-SQL služby Data Lake Analytics
Následující fragment kódu JSON definuje kanál s aktivitou Data Lake Analytics U-SQL. Definice aktivity obsahuje odkaz na propojenou službu Azure Data Lake Analytics, kterou jste vytvořili dříve. Chcete-li spustit skript Data Lake Analytics U-SQL, Data Factory odešle skript, který jste zadali do Data Lake Analytics, a požadované vstupy a výstupy jsou definovány ve skriptu pro Data Lake Analytics k načtení a výstupu. 

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

V následující tabulce jsou popsány názvy a popisy vlastností, které jsou specifické pro tuto aktivitu. 

| Vlastnost            | Popis                              | Požaduje se |
| :------------------ | :--------------------------------------- | :------- |
| jméno                | Název aktivity v kanálu     | Ano      |
| description         | Text popisující, co aktivita dělá.  | Ne       |
| type                | Pro aktivitu Data Lake Analytics U-SQL je typ aktivity **DataLakeAnalyticsU-SQL**. | Ano      |
| linkedServiceName   | Propojená služba s Azure Data Lake Analytics. Další informace o této propojené službě najdete v článku věnovaném [propojeným službám COMPUTE](compute-linked-services.md) .  |Ano       |
| scriptPath          | Cesta ke složce, která obsahuje skript U-SQL. Název souboru rozlišuje velká a malá písmena. | Ano      |
| scriptLinkedService | Propojená služba, která propojuje **Azure Data Lake Store** nebo **Azure Storage** obsahující skript do objektu pro vytváření dat | Ano      |
| degreeOfParallelism | Maximální počet uzlů současně použitých ke spuštění úlohy. | Ne       |
| priorita            | Určuje, které úlohy ze všech, které jsou zařazené do fronty, by měly být vybrány pro první spuštění. Čím nižší číslo, tím vyšší Priorita. | Ne       |
| parameters          | Parametry, které se mají předat skriptu U-SQL.    | Ne       |
| runtimeVersion      | Verze modulu runtime modulu U-SQL, který má být použit. | Ne       |
| compilationMode     | <p>Režim kompilace U-SQL Musí se jednat o jednu z těchto hodnot: **sémantika:** provádět pouze sémantické kontroly a nezbytné správnosti kontroly, **úplné:** proveďte úplnou kompilaci, včetně kontroly syntaxe, optimalizace, generování kódu atd., **SingleBox:** proveďte úplnou kompilaci s nastavením TargetType na SingleBox. Pokud nezadáte hodnotu pro tuto vlastnost, server určí optimální režim kompilace. | Ne |

Definici skriptu najdete v tématu [SearchLogProcessing. txt](#sample-u-sql-script) . 

## <a name="sample-u-sql-script"></a>Ukázka skriptu U-SQL

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

Ve výše uvedeném příkladu skriptu je vstup a výstup do skriptu definován v **\@** a **\@** výstupní parametry. Hodnoty parametrů **\@in** a **\@out** ve skriptu U-SQL jsou dynamicky předávány Data Factory pomocí oddílu Parameters. 

Můžete určit další vlastnosti, jako je například degreeOfParallelism a priority, i v definici kanálu pro úlohy, které jsou spouštěny ve službě Azure Data Lake Analytics.

## <a name="dynamic-parameters"></a>Dynamické parametry
V definici ukázkového kanálu se v parametrech a výstupní parametry přiřazují pevně zakódované hodnoty. 

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

V takovém případě se vstupní soubory stále vybírají ze složky/datalake/Input a výstupní soubory se generují ve složce/datalake/Output. Názvy souborů jsou dynamické na základě předávaného počátečního času okna při aktivaci kanálu.  

## <a name="next-steps"></a>Další kroky
Podívejte se na následující články, které vysvětlují, jak transformovat data jinými způsoby: 

* [Aktivita v podregistru](transform-data-using-hadoop-hive.md)
* [Aktivita prasete](transform-data-using-hadoop-pig.md)
* [Aktivita MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Aktivita streamování Hadoop](transform-data-using-hadoop-streaming.md)
* [Aktivita Sparku](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Aktivita spuštění Machine Learning dávky](transform-data-using-machine-learning.md)
* [Aktivita uložené procedury](transform-data-using-stored-procedure.md)
