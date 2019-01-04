---
title: Transformace dat pomocí skriptu U-SQL – Azure | Dokumentace Microsoftu
description: Zjistěte, jak zpracovat nebo transformovat data spuštěním skriptů U-SQL na výpočetní služby Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: e17c1255-62c2-4e2e-bb60-d25274903e80
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/01/2017
ms.author: douglasl
robots: noindex
ms.openlocfilehash: 7631b103d6d14cceb2c320d56e9f68d9ea57e4d8
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54020842"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Transformovat data spuštěním skriptů U-SQL v Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-usql-activity.md)
> * [Verze 2 (aktuální verze)](../transform-data-using-data-lake-analytics.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [aktivita U-SQL ve verzi V2](../transform-data-using-data-lake-analytics.md).

Kanál v objektu pro vytváření dat Azure zpracovává data v propojené služby s využitím propojené výpočetní služby. Obsahuje posloupnost aktivit, kde každá aktivita vykonává zpracování specifické pro operaci. Tento článek popisuje **aktivita U-SQL služby Data Lake Analytics** , který běží **U-SQL** skript na **Azure Data Lake Analytics** výpočetní propojenou službu. 

Před vytvořením kanálu s aktivitou Data Lake Analytics U-SQL vytvořte účet Azure Data Lake Analytics. Další informace o Azure Data Lake Analytics najdete v tématu [Začínáme s Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

Zkontrolujte [sestavení vaší první kurz kanálu](data-factory-build-your-first-pipeline.md) podrobné pokyny týkající se vytvoření datové továrny, propojené služby, datové sady a kanál. Použití fragmentů JSON pomocí editoru služby Data Factory nebo Visual Studio nebo Azure Powershellu vytvořit entity služby Data Factory.

## <a name="supported-authentication-types"></a>Typy podporované metody ověřování
Aktivita U-SQL podporuje následující typy ověřování proti Data Lake Analytics:
* Ověřování instančních objektů
* Ověření uživatele přihlašovací údaje (OAuth) 

Doporučujeme používat ověřování instančních objektů, zejména u naplánovaného spuštění U-SQL. Vypršení platnosti tokenu chování může dojít, s ověřením pověření uživatele. Podrobnosti o konfiguraci, najdete v článku [vlastnostem propojených služeb](#azure-data-lake-analytics-linked-service) oddílu.

## <a name="azure-data-lake-analytics-linked-service"></a>Propojená služba Azure Data Lake Analytics
Vytvoření **Azure Data Lake Analytics** propojená služba Azure Data Lake Analytics výpočetní služby Azure data Factory. Aktivita Data Lake Analytics U-SQL v kanálu odkazuje na tuto propojenou službu. 

Následující tabulka obsahuje popis obecných vlastností použitých v definici JSON. Dále můžete mezi instančního objektu a ověření přihlašovacích údajů uživatele.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| **type** |Vlastnost type by měla být nastavená na: **AzureDataLakeAnalytics**. |Ano |
| **accountName** |Název účtu Azure Data Lake Analytics. |Ano |
| **dataLakeAnalyticsUri** |Identifikátor URI, Azure Data Lake Analytics. |Ne |
| **ID předplatného** |Id předplatného Azure |Ne (když není určeno, předplatné objektu pro vytváření dat se používá). |
| **resourceGroupName** |Název skupiny prostředků Azure |Ne (když není určeno, skupina prostředků objektu pro vytváření dat se používá). |

### <a name="service-principal-authentication-recommended"></a>Ověřování instančních objektů (doporučeno)
Pokud chcete používat ověřování instančních objektů, entity aplikaci zaregistrovat ve službě Azure Active Directory (Azure AD) a jí udělit přístup k Data Lake Store. Podrobné pokyny najdete v článku [ověřování služba služba](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Poznamenejte si následující hodnoty, které slouží k definování propojené služby:
* ID aplikace
* Klíč aplikace 
* ID tenanta

Použijte ověřování instančních objektů zadáním následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| **servicePrincipalId** | Zadejte ID klienta vaší aplikace. | Ano |
| **servicePrincipalKey** | Zadejte klíč aplikace. | Ano |
| **tenanta** | Zadejte informace o tenantovi (domény ID tenanta nebo název) v rámci které se nachází vaše aplikace. Podržením ukazatele myši v pravém horním rohu webu Azure portal můžete načíst ji. | Ano |

**Příklad: Ověřování instančních objektů**
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

### <a name="user-credential-authentication"></a>Ověření přihlašovacích údajů uživatele
Alternativně můžete použít ověřování pověření uživatele pro Data Lake Analytics tak, že zadáte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| **Autorizace** | Klikněte na tlačítko **Authorize** tlačítko v editoru služby Data Factory a zadejte svoje přihlašovací údaje, které přiřadí adresu URL pro autorizaci automaticky generované této vlastnosti. | Ano |
| **ID relace** | ID relace OAuth z autorizační relace OAuth. Každé ID relace je jedinečný a může být použit pouze jednou. Toto nastavení není automaticky vygenerován při použití editoru služby Data Factory. | Ano |

**Příklad: Ověření přihlašovacích údajů uživatele**
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
Autorizační kód, který jste vygenerovali pomocí **Authorize** tlačítko vyprší po nějaké době zopakovat. Čas vypršení platnosti pro různé typy uživatelských účtů naleznete v tématu v následující tabulce. Může se zobrazit následující chybová zpráva ověření **vyprší platnost tokenu**: Chyba operace přihlašovacích údajů: invalid_grant - AADSTS70002: Chyba ověřování přihlašovacích údajů. AADSTS70008: Udělení přístupu zadaná vyprší nebo odvolat. ID trasování: ID korelace d18629e8-af88-43c5-88e3-d8419eb1fca1: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 časové razítko: 2015-12-15 21:09:31Z

| Typ uživatele | Platnost vyprší po |
|:--- |:--- |
| Uživatelské účty, které nejsou spravované přes Azure Active Directory (@hotmail.com, @live.comatd.) |12 hodin |
| Uživatelské účty, které jsou spravované službou Azure Active Directory (AAD) |Spusťte 14 dnů po poslední řez. <br/><br/>90 dnů, pokud řezu založeného na základě OAuth propojenou službu používá alespoň jednou za 14 dní. |

Vyhněte se/vyřešit tuto chybu, autorizovat pomocí **Authorize** tlačítko, kdy **vyprší platnost tokenu** a znovu nasaďte propojenou službu. Můžete také vygenerovat hodnoty **sessionId** a **autorizace** vlastnosti prostřednictvím kódu programu pomocí kódu následujícím způsobem:

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

Naleznete v tématu [AzureDataLakeStoreLinkedService třídy](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService třídy](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx), a [AuthorizationSessionGetResponse třídy](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) témata podrobnosti o třídách služby Data Factory používá v kódu. Přidáte odkaz na: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll WindowsFormsWebAuthenticationDialog třídy. 

## <a name="data-lake-analytics-u-sql-activity"></a>Aktivita U-SQL služby Data Lake Analytics
Následující fragment kódu JSON definuje kanál s aktivitou Data Lake Analytics U-SQL. Definice aktivity obsahuje odkaz na službu Azure Data Lake Analytics propojené, kterou jste vytvořili dříve.   

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
| type                | Vlastnost type musí být nastavená na **DataLakeAnalyticsU SQL**. | Ano                                      |
| linkedServiceName   | Referenční dokumentace k Azure Data Lake Analytics zaregistrovaný jako propojenou službu ve službě Data Factory | Ano                                      |
| ScriptPath          | Cesta ke složce, která obsahuje skript U-SQL. Název souboru je velká a malá písmena. | Ne (když je pomocí skriptu)                   |
| scriptLinkedService | Propojené služby, která propojuje úložiště, který obsahuje skript do služby data factory | Ne (když je pomocí skriptu)                   |
| skript              | Zadejte místo určení scriptPath a scriptLinkedService zpracování vloženého skriptu. Například: `"script": "CREATE DATABASE test"`. | Ne (když použijete scriptPath a scriptLinkedService) |
| z degreeOfParallelism | Maximální počet uzlů najednou použitý ke spuštění úlohy. | Ne                                       |
| priorita            | Určuje, které z uložených ve frontě úloh by měl být vybrané ke spuštění první. Čím nižší je číslo, tím vyšší je priorita. | Ne                                       |
| parameters          | Parametry pro skript U-SQL          | Ne                                       |
| runtimeVersion      | Verze modulu runtime U-SQL stroje používat | Ne                                       |
| Vlastnost CompilationMode     | <p>Režim kompilace U-SQL. Musí být jedna z těchto hodnot:</p> <ul><li>**Sémantické:** Proveďte pouze sémantické kontroly a kontrol správnosti nezbytné.</li><li>**Úplné:** Proveďte úplná kompilace, jako je kontrola syntaxe, optimalizace, generování kódu, atd.</li><li>**SingleBox:** Proveďte úplná kompilace s TargetType nastavení SingleBox.</li></ul><p>Pokud nezadáte hodnotu pro tuto vlastnost, server určuje režim kompilace optimální. </p> | Ne                                       |

V tématu [SearchLogProcessing.txt skript definice](#sample-u-sql-script) pro definici skriptu. 

## <a name="sample-input-and-output-datasets"></a>Ukázková vstupní a výstupní datové sady
### <a name="input-dataset"></a>Vstupní datová sada
V tomto příkladu vstupní data nachází v Azure Data Lake Store (soubor SearchLog.tsv souboru ve složce datalake/input). 

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
V tomto příkladu je uložen výstupní data vytvořená skript U-SQL do Azure Data Lake Store (datalake/výstupní složka). 

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

### <a name="sample-data-lake-store-linked-service"></a>Ukázková Data Lake Store propojená služba
Tady je definice ukázku Azure Data Lake Store propojenou službu používá vstupní a výstupní datové sady. 

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

Zobrazit [přesun dat do a z Azure Data Lake Store](data-factory-azure-datalake-connector.md) článku popisy vlastností JSON. 

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

Hodnoty pro **@in** a **@out** parametry ve skriptu U-SQL jsou předávány dynamicky ADF pomocí sekci "parametrů". V části "parametrů" v definici kanálu.

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
    "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
    "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
}
```

V tomto případě vstupních souborů i nadále, vyberou se ze složky /datalake/input a výstupní soubory jsou vygenerovány ve složce /datalake/output. Názvy souborů jsou dynamické vzorce podle počáteční čas řezu.  


