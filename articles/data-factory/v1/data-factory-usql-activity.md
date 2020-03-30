---
title: Transformace dat pomocí skriptu U-SQL – Azure
description: Zjistěte, jak zpracovat nebo transformovat data spuštěním skriptů U-SQL ve výpočetní službě Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
ms.assetid: e17c1255-62c2-4e2e-bb60-d25274903e80
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/01/2017
author: nabhishek
ms.author: abnarain
manager: anandsub
robots: noindex
ms.openlocfilehash: c6d3510dfdd02bf2eb07d656c706c44d895c582d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74927906"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Transformace dat spuštěním skriptů U-SQL v Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](data-factory-usql-activity.md)
> * [Verze 2 (aktuální verze)](../transform-data-using-data-lake-analytics.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [Aktivita U-SQL ve Verzi 2](../transform-data-using-data-lake-analytics.md).

Kanál ve společnosti Azure pro data zpracovává data v propojených službách úložiště pomocí propojených výpočetních služeb. Obsahuje posloupnost aktivit, kde každá aktivita provádí určitou operaci zpracování. Tento článek popisuje **aktivitu U-SQL analýzy datového jezera,** která spouští skript **U-SQL** na výpočetní propojené službě **Azure Data Lake Analytics.** 

Před vytvořením kanálu s aktivitou U-SQL analýzy datového jezera si vytvořte účet Azure Data Lake Analytics. Další informace o Azure Data Lake Analytics najdete [v tématu Začínáme s Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

Projděte [si vytvořit svůj první kanál kurz](data-factory-build-your-first-pipeline.md) pro podrobné kroky k vytvoření datové továrny, propojené služby, datové sady a kanál. Pomocí úryvků JSON s Editorem datových toků nebo Visual Studio nebo Azure PowerShell utvaořte entity Datové továrny.

## <a name="supported-authentication-types"></a>Podporované typy ověřování
Aktivita U-SQL podporuje níže uvedené typy ověřování oproti Data Lake Analytics:
* Ověřování instančních objektů
* Ověřování pověření uživatele (OAuth) 

Doporučujeme použít ověřování instančního objektu, zejména pro naplánované spuštění U-SQL. Při ověřování pověření uživatele může dojít k chování vypršení platnosti tokenu. Podrobnosti o konfiguraci naleznete v části [Vlastnosti propojené služby.](#azure-data-lake-analytics-linked-service)

## <a name="azure-data-lake-analytics-linked-service"></a>Propojená služba Azure Data Lake Analytics
Vytvoříte propojenou službu **Azure Data Lake Analytics,** která propojí výpočetní službu Azure Data Lake Analytics s toto datovou továrnou Azure. Aktivita U-SQL služby Data Lake Analytics v kanálu odkazuje na tuto propojenou službu. 

Následující tabulka obsahuje popis obecných vlastností použitých v definici JSON. Dále si můžete vybrat mezi instančním objektem a ověřováním pověření uživatele.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| **Typ** |Vlastnost type by měla být nastavena na: **AzureDataLakeAnalytics**. |Ano |
| **název_účtu** |Název účtu Azure Data Lake Analytics. |Ano |
| **dataLakeAnalyticsUri** |Identifikátor URI analýzy datového jezera Azure. |Ne |
| **subscriptionId** |Id předplatného Azure |Ne (Pokud není zadáno, použije se odběr datové továrny). |
| **resourceGroupName** |Název skupiny prostředků Azure |Ne (Pokud není zadáno, použije se skupina prostředků datové továrny). |

### <a name="service-principal-authentication-recommended"></a>Ověření instančního objektu (doporučeno)
Pokud chcete použít ověřování o jistině služby, zaregistrujte entitu aplikace ve službě Azure Active Directory (Azure AD) a udělte jí přístup k úložišti Data Lake Store. Podrobné kroky naleznete v [tématu Ověřování mezi službami](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Poznamenejte si následující hodnoty, které používáte k definování propojené služby:
* ID aplikace
* Klíč aplikace 
* ID tenanta

Pomocí ověřování instančního objektu zadejte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| **servicePrincipalId** | Zadejte ID klienta aplikace. | Ano |
| **servicePrincipalKey** | Zadejte klíč aplikace. | Ano |
| **Nájemce** | Zadejte informace o klientovi (název domény nebo ID klienta), pod kterým se aplikace nachází. Můžete načíst na jenom myši v pravém horním rohu portálu Azure. | Ano |

**Příklad: Ověřování instančního objektu**
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

### <a name="user-credential-authentication"></a>Ověřování pověření uživatele
Případně můžete použít ověřování přihlašovacích údajů uživatele pro Data Lake Analytics zadáním následujících vlastností:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| **Autorizace** | Klepněte na tlačítko **Autorizovat** v editoru datové továrny a zadejte pověření, které této vlastnosti přiřadí automaticky vygenerovanou autorizační adresu URL. | Ano |
| **Sessionid** | ID relace OAuth z autorizační relace OAuth. Každé ID relace je jedinečné a lze jej použít pouze jednou. Toto nastavení se automaticky vygeneruje při použití Editoru datových tocích. | Ano |

**Příklad: Ověřování pověření uživatele**
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
Platnost autorizačního kódu, který jste vygenerovali pomocí tlačítka **Autorizovat,** vyprší po určité době. V následující tabulce naleznete časy vypršení platnosti pro různé typy uživatelských účtů. Při **vypršení platnosti ověřovacího tokenu**se může zobrazit následující chybová zpráva : Chyba operace pověření: invalid_grant - AADSTS70002: Chyba při ověřování pověření. AADSTS70008: Zadaný přístupový grant vypršel nebo byl odvolán. Trace ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 Korelační ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Časové razítko: 2015-12-15 21:09:31Z

| Typ uživatele | Platnost vyprší po |
|:--- |:--- |
| Uživatelské účty, které nejsou@hotmail.comspravovány službou Azure Active Directory ( , @live.com, atd.) |12 hodin |
| Uživatelské účty spravované službou Azure Active Directory (AAD) |14 dní po posledním spuštění řezu. <br/><br/>90 dní, pokud řez založený na propojené službě založené na OAuth běží alespoň jednou za 14 dní. |

Chcete-li se této chybě vyhnout nebo vyřešit, znovu autorizujte po **vypršení platnosti tokenu** **opětovné** povolení a znovu nasadit propojenou službu. Můžete také generovat hodnoty pro **sessionId** a **autorizace** vlastnosti programově pomocí kódu takto:

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

Podrobnosti o třídách Data Factory používaných v kódu najdete v tématech [třídy AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)a [AuthorizationSessionGetResponse Class.](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) Přidejte odkaz na: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll pro třídu WindowsFormsWebAuthenticationDialog. 

## <a name="data-lake-analytics-u-sql-activity"></a>Aktivita U-SQL služby Data Lake Analytics
Následující fragment JSON definuje kanál s aktivitou U-SQL analýzy datového jezera. Definice aktivity obsahuje odkaz na propojenou službu Azure Data Lake Analytics, kterou jste vytvořili dříve.   

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

Následující tabulka popisuje názvy a popisy vlastností, které jsou specifické pro tuto aktivitu. 

| Vlastnost            | Popis                              | Požaduje se                                 |
| :------------------ | :--------------------------------------- | :--------------------------------------- |
| type                | Vlastnost type musí být nastavena na **DataLakeAnalyticsU-SQL**. | Ano                                      |
| linkedServiceName   | Odkaz na Azure Data Lake Analytics zaregistrovaná jako propojená služba v Datové továrně | Ano                                      |
| scriptPath          | Cesta ke složce, která obsahuje skript U-SQL. Název souboru rozlišuje malá a velká písmena. | Ne (pokud používáte skript)                   |
| skriptLinkedService | Propojená služba, která propojuje úložiště obsahující skript s továrně dat | Ne (pokud používáte skript)                   |
| .              | Místo zadávání skriptuPath a scriptLinkedService zadejte vložkový skript. Například: `"script": "CREATE DATABASE test"`. | Ne (pokud používáte scriptPath a scriptLinkedService) |
| degreeOfParallelism | Maximální počet uzlů současně používaných ke spuštění úlohy. | Ne                                       |
| Prioritou            | Určuje, které úlohy ze všech, které jsou zařazeny do fronty, by měly být vybrány jako první. Čím nižší číslo, tím vyšší priorita. | Ne                                       |
| parameters          | Parametry skriptu U-SQL          | Ne                                       |
| runtimeVersion      | Runtime verze modulu U-SQL, který se má použít | Ne                                       |
| Compilationmode     | <p>Režim kompilace U-SQL. Musí být jedna z těchto hodnot:</p> <ul><li>**Sémantické:** Provádějte pouze sémantické kontroly a nezbytné kontroly příčetnosti.</li><li>**Úplné:** Proveďte úplnou kompilaci, včetně kontroly syntaxe, optimalizace, generování kódu atd.</li><li>**SingleBox:** Proveďte úplnou kompilaci s nastavením TargetType na SingleBox.</li></ul><p>Pokud nezadáte hodnotu pro tuto vlastnost, server určí optimální režim kompilace. </p> | Ne                                       |

Definice skriptu naleznete v [tématu SearchLogProcessing.txt Script Definition.](#sample-u-sql-script) 

## <a name="sample-input-and-output-datasets"></a>Ukázkové vstupní a výstupní datové sady
### <a name="input-dataset"></a>Vstupní datová sada
V tomto příkladu jsou vstupní data umístěna v úložišti Azure Data Lake Store (soubor SearchLog.tsv ve složce datalake/input). 

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
V tomto příkladu jsou výstupní data vytvořená skriptem U-SQL uložena v úložišti Azure Data Lake Store (složka datalake/output). 

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

### <a name="sample-data-lake-store-linked-service"></a>Vzorová propojená služba úložiště datových jezer
Tady je definice ukázkové propojené služby Azure Data Lake Store používané vstupními a výstupními datovými sadami. 

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

Popis vlastností JSON najdete v článku [Přesunutí dat do a z Azure Data Lake Store.](data-factory-azure-datalake-connector.md) 

## <a name="sample-u-sql-script"></a>Ukázkový skript U-SQL

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

Hodnoty parametrů ** \@dovnitř** a ** \@ven** ve skriptu U-SQL jsou dynamicky předávány ADF pomocí sekce 'parameters'. Viz část "parametry" v definici kanálu.

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
    "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
    "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
}
```

V tomto případě jsou vstupní soubory stále vyzvednuty ze složky /datalake/input a výstupní soubory jsou generovány ve složce /datalake/output. Názvy souborů jsou dynamické na základě času zahájení řezu.  


