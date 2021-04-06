---
title: Transformace dat pomocí skriptu U-SQL – Azure
description: Naučte se zpracovávat nebo transformovat data spuštěním skriptů U-SQL ve službě Azure Data Lake Analytics COMPUTE Service – verze 1.
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/01/2017
author: nabhishek
ms.author: abnarain
ms.custom: devx-track-csharp
robots: noindex
ms.openlocfilehash: 5931cb28721e8658a771ceea1cd94624a0c09f7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100392914"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Transformace dat spuštěním skriptů U-SQL v Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](data-factory-usql-activity.md)
> * [Verze 2 (aktuální verze)](../transform-data-using-data-lake-analytics.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [aktivita U-SQL ve verzi v2](../transform-data-using-data-lake-analytics.md).

Kanál v datové továrně Azure zpracovává data v propojených službách úložiště pomocí propojených výpočetních služeb. Obsahuje posloupnost aktivit, kde každá aktivita provádí určitou operaci zpracování. Tento článek popisuje **aktivitu Data Lake Analytics u-SQL** , která spouští skript **U-SQL** v propojené službě **Azure Data Lake Analytics** Compute. 

Vytvořte účet Azure Data Lake Analytics před vytvořením kanálu s aktivitou Data Lake Analytics U-SQL. Další informace o Azure Data Lake Analytics najdete v tématu [Začínáme s Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

Podrobné pokyny k vytvoření datové továrny, propojených služeb, datových sad a kanálu najdete v [kurzu sestavení prvního kanálu](data-factory-build-your-first-pipeline.md) . K vytvoření Data Factory entit použijte fragmenty JSON s editorem Data Factory nebo sadou Visual Studio nebo Azure PowerShell.

## <a name="supported-authentication-types"></a>Podporované typy ověřování
Aktivita U-SQL podporuje níže uvedené typy ověřování proti Data Lake Analytics:
* Ověřování instančních objektů
* Ověřování přihlašovacích údajů uživatele (OAuth) 

Doporučujeme použít ověřování instančního objektu, zejména pro plánované spuštění U-SQL. K chování vypršení platnosti tokenu může dojít při ověřování přihlašovacích údajů uživatele. Podrobnosti o konfiguraci najdete v části [Vlastnosti propojené služby](#azure-data-lake-analytics-linked-service) .

## <a name="azure-data-lake-analytics-linked-service"></a>Propojená služba Azure Data Lake Analytics
Vytvoříte propojenou službu **Azure Data Lake Analytics** pro propojení Azure Data Lake Analytics výpočetní služby s objektem pro vytváření dat Azure. Aktivita Data Lake Analytics U-SQL v kanálu odkazuje na tuto propojenou službu. 

Následující tabulka uvádí popisy obecných vlastností použitých v definici JSON. Můžete si vybrat mezi instančním objektem a ověřením přihlašovacích údajů uživatele.

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| **textový** |Vlastnost Type by měla být nastavená na: **AzureDataLakeAnalytics**. |Yes |
| **accountName** |Azure Data Lake Analytics název účtu. |Yes |
| **dataLakeAnalyticsUri** |Azure Data Lake Analytics identifikátor URI. |No |
| **subscriptionId** |ID předplatného Azure |Ne (Pokud není zadaný, použije se předplatné datové továrny). |
| **resourceGroupName** |Název skupiny prostředků Azure |Ne (Pokud není zadaný, použije se skupina prostředků objektu pro vytváření dat). |

### <a name="service-principal-authentication-recommended"></a>Ověřování instančního objektu (doporučeno)
Pokud chcete použít ověřování instančního objektu, zaregistrujte entitu aplikace ve službě Azure Active Directory (Azure AD) a udělte jí přístup k Data Lake Store. Podrobný postup najdete v tématu [ověřování služba-služba](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md). Poznamenejte si následující hodnoty, které použijete k definování propojené služby:
* ID aplikace
* Klíč aplikace 
* ID tenanta

Použijte ověřování instančního objektu zadáním následujících vlastností:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| **servicePrincipalId** | Zadejte ID klienta aplikace. | Yes |
| **servicePrincipalKey** | Zadejte klíč aplikace. | Yes |
| **tenant** | Zadejte informace o tenantovi (název domény nebo ID tenanta), pod kterým se vaše aplikace nachází. Můžete ho načíst tak, že najedete myší v pravém horním rohu Azure Portal. | Yes |

**Příklad: ověřování instančního objektu**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics.net",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

### <a name="user-credential-authentication"></a>Ověřování přihlašovacích údajů uživatele
Alternativně můžete použít ověření přihlašovacích údajů uživatele pro Data Lake Analytics zadáním následujících vlastností:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| **udělován** | V editoru Data Factory klikněte na tlačítko **autorizovat** a zadejte přihlašovací údaje, které přiřadí automaticky vygenerované autorizační URL k této vlastnosti. | Yes |
| **sessionId** | ID relace OAuth z autorizační relace OAuth. Každé ID relace je jedinečné a dá se použít jenom jednou. Toto nastavení se generuje automaticky, když použijete Editor Data Factory. | Yes |

**Příklad: ověření přihlašovacích údajů uživatele**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="token-expiration"></a>Vypršení platnosti tokenu
Autorizační kód, který jste vygenerovali pomocí tlačítka **autorizovat** , vyprší po nějaké době. V následující tabulce najdete dobu vypršení platnosti různých typů uživatelských účtů. V případě **vypršení platnosti ověřovacího tokenu** se může zobrazit následující chybová zpráva: Chyba operace Credential: INVALID_GRANT-AADSTS70002: Chyba při ověřování přihlašovacích údajů. AADSTS70008: poskytnutý nebo odvolaný udělený přístup vypršel. ID trasování: ID korelace d18629e8-af88-43c5-88e3-d8419eb1fca1: fac30a0c-6be6-4e02-8D69-a776d2ffefd7 časové razítko: 2015-12-15 21:09:31Z

| Typ uživatele | Platnost vyprší po |
|:--- |:--- |
| Uživatelské účty nespravované pomocí Azure Active Directory ( @hotmail.com , @live.com atd.) |12 hodin |
| Uživatelské účty spravované pomocí Azure Active Directory (AAD) |14 dní po posledním spuštění řezu. <br/><br/>90 dnů, pokud je řez založený na propojené službě založené na protokolu OAuth spuštěn nejméně jednou za 14 dní. |

Pokud chcete tuto chybu předejít, znovu autorizovat pomocí tlačítka **autorizovat** po **vypršení platnosti tokenu** a znovu nasaďte propojenou službu. Hodnoty pro **identifikátor SessionID** a vlastnosti **autorizace** můžete také generovat programově pomocí kódu následujícím způsobem:

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

Podrobnosti o třídách Data Factory používaných v kódu naleznete v tématech [Třída](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice), třída [AzureDataLakeAnalyticsLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice)a [AuthorizationSessionGetResponse třídy](/dotnet/api/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse) . Přidejte odkaz na: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll pro třídu WindowsFormsWebAuthenticationDialog. 

## <a name="data-lake-analytics-u-sql-activity"></a>Aktivita U-SQL služby Data Lake Analytics
Následující fragment kódu JSON definuje kanál s aktivitou Data Lake Analytics U-SQL. Definice aktivity obsahuje odkaz na propojenou službu Azure Data Lake Analytics, kterou jste vytvořili dříve.   

```json
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
        "activities": 
        [
            {
                "type": "DataLakeAnalyticsU-SQL",
                "typeProperties": {
                    "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                    "scriptLinkedService": "StorageLinkedService",
                    "degreeOfParallelism": 3,
                    "priority": 100,
                    "parameters": {
                        "in": "/datalake/input/SearchLog.tsv",
                        "out": "/datalake/output/Result.tsv"
                    }
                },
                "inputs": [
                    {
                        "name": "DataLakeTable"
                    }
                ],
                "outputs": 
                [
                    {
                        "name": "EventsByRegionTable"
                    }
                ],
                "policy": {
                    "timeout": "06:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "EventsByRegion",
                "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
            }
        ],
        "start": "2015-08-08T00:00:00Z",
        "end": "2015-08-08T01:00:00Z",
        "isPaused": false
    }
}
```

V následující tabulce jsou popsány názvy a popisy vlastností, které jsou specifické pro tuto aktivitu. 

| Vlastnost            | Popis                              | Povinné                                 |
| :------------------ | :--------------------------------------- | :--------------------------------------- |
| typ                | Vlastnost Type musí být nastavená na **DataLakeAnalyticsU-SQL**. | Yes                                      |
| linkedServiceName   | Odkaz na Azure Data Lake Analytics zaregistrovaný jako propojená služba v Data Factory | Yes                                      |
| scriptPath          | Cesta ke složce, která obsahuje skript U-SQL. Název souboru rozlišuje velká a malá písmena. | Ne (Pokud používáte skript)                   |
| scriptLinkedService | Propojená služba, která propojuje úložiště obsahující skript s datovou továrnou | Ne (Pokud používáte skript)                   |
| script              | Místo zadání scriptPath a scriptLinkedService zadejte vložený skript. Příklad: `"script": "CREATE DATABASE test"`. | Ne (Pokud používáte scriptPath a scriptLinkedService) |
| degreeOfParallelism | Maximální počet uzlů současně použitých ke spuštění úlohy. | No                                       |
| upřednostněn            | Určuje, které úlohy ze všech, které jsou zařazené do fronty, by měly být vybrány pro první spuštění. Čím nižší číslo, tím vyšší Priorita. | No                                       |
| parameters          | Parametry pro skript U-SQL          | No                                       |
| runtimeVersion      | Verze modulu runtime pro modul U-SQL, který se má použít | No                                       |
| CompilationMode nastavena     | <p>Režim kompilace U-SQL Musí se jednat o jednu z těchto hodnot:</p> <ul><li>**Sémantika:** Provádět pouze sémantické kontroly a nezbytné správnosti kontroly.</li><li>**Úplné:** Proveďte úplnou kompilaci, včetně kontroly syntaxe, optimalizace, generování kódu atd.</li><li>**SingleBox:** Proveďte úplnou kompilaci s nastavením TargetType na SingleBox.</li></ul><p>Pokud nezadáte hodnotu pro tuto vlastnost, server určí optimální režim kompilace. </p> | No                                       |

Definice skriptu najdete v tématu [SearchLogProcessing.txt definice skriptu](#sample-u-sql-script) . 

## <a name="sample-input-and-output-datasets"></a>Ukázkové vstupní a výstupní datové sady
### <a name="input-dataset"></a>Vstupní datová sada
V tomto příkladu se vstupní data nachází v Azure Data Lake Store (soubor. TSV ve složce datalake/Input). 

```json
{
    "name": "DataLakeTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}    
```

### <a name="output-dataset"></a>Výstupní datová sada
V tomto příkladu se výstupní data vytvořená skriptem U-SQL ukládají do Azure Data Lake Store (složka datalake/Output). 

```json
{
    "name": "EventsByRegionTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="sample-data-lake-store-linked-service"></a>Ukázka propojené služby Data Lake Store
Tady je definice ukázkové Azure Data Lake Store propojené služby používané vstupními a výstupními datovými sadami. 

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
        }
    }
}
```

Popisy vlastností JSON najdete v článku [přesun dat do a z Azure Data Lake Store](data-factory-azure-datalake-connector.md) . 

## <a name="sample-u-sql-script"></a>Ukázka skriptu U-SQL

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
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

Hodnoty parametrů **\@ in** a **\@ out** ve skriptu U-SQL se dynamicky předávají pomocí ADF pomocí oddílu Parameters. Viz část Parameters (parametry) v definici kanálu.

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
    "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
    "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
}
```

V takovém případě se vstupní soubory stále vybírají ze složky/datalake/Input a výstupní soubory se generují ve složce/datalake/Output. Názvy souborů jsou dynamické na základě počátečního času řezu.