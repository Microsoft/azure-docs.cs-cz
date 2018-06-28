---
title: Transformace dat pomocí skriptu U-SQL - Azure | Microsoft Docs
description: Informace o zpracování nebo transformace dat pomocí spouštění skriptů U-SQL na výpočetní služba Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: e17c1255-62c2-4e2e-bb60-d25274903e80
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2017
ms.author: douglasl
robots: noindex
ms.openlocfilehash: 534fbeaa8ba3c27c8d3f3bbcc59717d8bdb5c654
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37050314"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Transformace dat pomocí spouštění skriptů U-SQL v Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-usql-activity.md)
> * [Verze 2 (aktuální verze)](../transform-data-using-data-lake-analytics.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory. Pokud používáte aktuální verze služby Data Factory, přečtěte si téma [aktivity U-SQL v V2](../transform-data-using-data-lake-analytics.md).

Kanál v objektu pro vytváření dat Azure zpracovává data v služby propojené úložiště pomocí propojené výpočetní služby. Obsahuje posloupnost aktivit, kde každá aktivita provede konkrétní zpracování operace. Tento článek popisuje **Data Lake Analytics U-SQL aktivity** , která se spouští **U-SQL** skript na **Azure Data Lake Analytics** výpočetní propojené služby. 

Vytvoření účtu Azure Data Lake Analytics před vytvořením kanálu s aktivitou Data Lake Analytics U-SQL. Další informace o Azure Data Lake Analytics najdete v tématu [Začínáme s Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

Zkontrolujte [vytvoření vaší první kurz kanálu](data-factory-build-your-first-pipeline.md) podrobné pokyny pro vytváření dat, propojené služby, datových sad a kanálu. Použití JSON fragmenty pomocí editoru služby Data Factory nebo Visual Studio nebo Azure PowerShell k vytvoření entit služby Data Factory.

## <a name="supported-authentication-types"></a>Typy podporované ověřování
Aktivita U-SQL podporuje následující typy ověřování proti Data Lake Analytics:
* Ověřování instančních objektů
* Ověření přihlašovacích údajů (OAuth) uživatele 

Doporučujeme použít objekt zabezpečení ověřování služby, zejména pro naplánovaného spuštění U-SQL. Vypršení platnosti tokenu chování mohou nastat u ověření přihlašovacích údajů uživatele. Podrobnosti konfigurace najdete v tématu [propojené vlastnosti služby](#azure-data-lake-analytics-linked-service) části.

## <a name="azure-data-lake-analytics-linked-service"></a>Propojená služba Azure Data Lake Analytics
Vytvoříte **Azure Data Lake Analytics** propojená služba Azure Data Lake Analytics výpočetní služby s objektem pro vytváření dat Azure. Data Lake Analytics U-SQL aktivitu v kanálu odkazuje na tato propojená služba. 

Následující tabulka obsahuje popis obecné vlastnosti používané v definici JSON. Dále můžete mezi instanční objekt a ověření přihlašovacích údajů uživatele.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| **type** |Vlastnost typu musí být nastavená na: **AzureDataLakeAnalytics**. |Ano |
| **accountName** |Název účtu Azure Data Lake Analytics. |Ano |
| **dataLakeAnalyticsUri** |Identifikátor URI služby Azure Data Lake Analytics. |Ne |
| **ID předplatného** |Id předplatného Azure |Ne (když není určeno, předplatné objektu pro vytváření dat se používá). |
| **resourceGroupName** |Název skupiny prostředků Azure |Ne (když není určeno, skupinu prostředků objektu pro vytváření dat se používá). |

### <a name="service-principal-authentication-recommended"></a>Objekt zabezpečení ověřování služby (doporučeno)
Pokud chcete použít ověřování hlavní služby, zaregistrujte entitu aplikace v Azure Active Directory (Azure AD) a jí udělit přístup k Data Lake Store. Podrobné pokyny najdete v tématu [Service-to-service ověřování](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Poznamenejte si následující hodnoty, které můžete použít k definování propojené služby:
* ID aplikace
* Klíč aplikace 
* ID tenanta

Použijte objekt zabezpečení ověřování služby tak, že zadáte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| **servicePrincipalId** | Zadejte ID aplikace klienta. | Ano |
| **servicePrincipalKey** | Zadejte klíč aplikace. | Ano |
| **klienta** | Zadejte informace o klienta (název nebo klienta domény ID) v rámci které se nachází aplikace. Můžete ji načíst podržením ukazatele myši v pravém horním rohu portálu Azure. | Ano |

**Příkladu: Ověření objektu službu**
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

### <a name="user-credential-authentication"></a>Ověření pověření uživatele
Alternativně můžete použít ověřování pověření uživatele pro Data Lake Analytics zadáním následujících vlastností:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| **Autorizace** | Klikněte **Autorizovat** tlačítko v editoru služby Data Factory a zadejte svoje přihlašovací údaje, který přiřazuje URL pro autorizaci automaticky generovaný této vlastnosti. | Ano |
| **ID relace** | ID relace OAuth z autorizační relace OAuth. Každé ID relace je jedinečné a může být použit pouze jednou. Toto nastavení se automaticky generuje při pomocí editoru služby Data Factory. | Ano |

**Příklad: Ověření pověření uživatele**
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
Autorizační kód, který jste vygenerovali pomocí **Autorizovat** tlačítko vyprší po určité době. Pro dobu vypršení platnosti pro různé typy uživatelských účtů najdete v následující tabulce. Mohou se zobrazit následující chyby zprávy při ověřování **platnost tokenu vyprší**: přihlašovací údaje chyby operace: invalid_grant - AADSTS70002: Chyba při ověřování přihlašovacích údajů. AADSTS70008: Předloženému udělení přístupu je prošlý nebo odvolat. ID trasování: ID korelace d18629e8-af88-43c5-88e3-d8419eb1fca1: časové razítko fac30a0c-6be6-4e02-8d69-a776d2ffefd7: 2015-12-15 21:09:31Z

| Typ uživatele | Platnost vyprší po |
|:--- |:--- |
| Uživatelské účty, které nejsou spravované přes Azure Active Directory (@hotmail.com, @live.comatd.) |12 hodin |
| Účty uživatelů spravované pomocí Azure Active Directory (AAD) |14 dnů po poslední řez spustit. <br/><br/>90 dnů, pokud řezu založeného na základě OAuth propojené služby používá alespoň jednou za 14 dní. |

Vyhněte se/vyřešit tuto chybu, opětovné pověření pomocí **Authorize** tlačítko při **platnost tokenu vyprší** a znovu nasaďte propojené služby. Můžete také vygenerovat hodnoty pro **sessionId** a **autorizace** vlastnosti programově pomocí kódu následujícím způsobem:

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

V tématu [azuredatalakestorelinkedservice třída](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService třída](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx), a [AuthorizationSessionGetResponse třída](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) témata podrobnosti o třídách objekt pro vytváření dat používá v kódu. Přidat odkaz na: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll pro třídu WindowsFormsWebAuthenticationDialog. 

## <a name="data-lake-analytics-u-sql-activity"></a>Aktivita U-SQL služby Data Lake Analytics
Následující fragment kódu JSON definuje kanál s aktivitou Data Lake Analytics U-SQL. Definici aktivity odkazuje na Azure Data Lake Analytics propojené služby, kterou jste vytvořili dříve.   

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
| type                | Vlastnost typu musí být nastavená na **DataLakeAnalyticsU SQL**. | Ano                                      |
| linkedServiceName   | Referenční dokumentace k Azure Data Lake Analytics registrován jako propojené služby v datové továrně | Ano                                      |
| scriptPath          | Cesta ke složce, který obsahuje skript U-SQL. Název souboru je malá a velká písmena. | Ne (když používáte skript)                   |
| scriptLinkedService | Propojené služby, který odkazuje úložiště, který obsahuje skript pro vytváření dat. | Ne (když používáte skript)                   |
| skript              | Zadejte místo zadání scriptPath a scriptLinkedService zpracování vloženého skriptu. Například: `"script": "CREATE DATABASE test"`. | Ne (když používáte scriptPath a scriptLinkedService) |
| degreeOfParallelism | Maximální počet uzlů současně slouží ke spuštění úlohy. | Ne                                       |
| priorita            | Určuje, jaké úlohy mimo všechny, které jsou zařazeny do fronty, měla by být vybrána má spustit jako první. Čím nižší je číslo, tím vyšší je priorita. | Ne                                       |
| parameters          | Parametry pro skript U-SQL          | Ne                                       |
| runtimeVersion      | Verze runtime – stroje U-SQL používat | Ne                                       |
| compilationMode     | <p>Režim kompilace U-SQL. Musí být jedna z těchto hodnot:</p> <ul><li>**Sémantické:** provádět jenom sémantického kontroly a nezbytné správností kontroly.</li><li>**Úplné:** provést úplné kompilace, včetně kontrola syntaxe, optimalizace, generování kódu atd.</li><li>**SingleBox:** provést úplné kompilace s TargetType nastavení SingleBox.</li></ul><p>Pokud nezadáte hodnotu pro tuto vlastnost, server určí režim optimální kompilace. </p> | Ne                                       |

V tématu [definice skriptu SearchLogProcessing.txt](#sample-u-sql-script) pro definici skriptu. 

## <a name="sample-input-and-output-datasets"></a>Ukázkové vstupní a výstupní datové sady
### <a name="input-dataset"></a>Vstupní datové sady
V tomto příkladu vstupní data nachází v Azure Data Lake Store (soubor SearchLog.tsv soubor ve složce datalake/vstupu). 

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

### <a name="output-dataset"></a>Výstupní datové sady
V tomto příkladu výstupní data vytvořená skript U-SQL uložený v Azure Data Lake Store (datalake výstupní složka). 

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
Zde je definici vzorku Azure Data Lake Store propojená služba používá vstupní a výstupní datové sady. 

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

V tématu [přesun dat do a z Azure Data Lake Store](data-factory-azure-datalake-connector.md) článku popisy vlastností JSON. 

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

Hodnoty pro **@in** a **@out** parametry ve skriptu U-SQL jsou předaná dynamicky ADF pomocí části parametry.". Najdete v části 'parametry' v definici kanálu.

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
    "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
    "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
}
```

V takovém případě vstupní soubory jsou stále zachyceny ze složky /datalake/input a výstupní soubory se generují ve složce /datalake/output. Názvy souborů jsou dynamické podle času zahájení řez.  


