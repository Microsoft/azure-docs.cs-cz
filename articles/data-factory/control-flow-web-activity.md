---
title: Webové aktivity v Azure Data Factory | Microsoft Docs
description: Zjistěte, jak webové aktivity, jeden z aktivity toku řízení podporovaných službou Data Factory, můžete vyvolat koncový bod REST z kanálu.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: shlo
ms.openlocfilehash: 6d14d84610d97e487e3bf4286267623681c4eb75
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34619231"
---
# <a name="web-activity-in-azure-data-factory"></a>Aktivity webového v Azure Data Factory
Webová aktivita slouží k volání vlastního koncového bodu REST z kanálu služby Data Factory. Můžete předávat datové sady a propojené služby, které má aktivita používat a ke kterým má mít přístup. 

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), prostudujte si [dokumentaci služby Data Factory verze 1](v1/data-factory-introduction.md).

## <a name="syntax"></a>Syntaxe

```json
{  
   "name":"MyWebActivity",
   "type":"WebActivity",
   "typeProperties":{  
      "method":"Post",
      "url":"<URLEndpoint>",
      "headers":{  
         "Content-Type":"application/json"
      },
      "authentication":{  
         "type":"ClientCertificate",  
         "pfx":"****",
         "password":"****"
      },
      "datasets":[  
         {  
            "referenceName":"<ConsumedDatasetName>",
            "type":"DatasetReference",
            "parameters":{  
               ...
            }
         }
      ],
      "linkedServices":[  
         {  
            "referenceName":"<ConsumedLinkedServiceName>",
            "type":"LinkedServiceReference"
         }
      ]
   }
}

```

## <a name="type-properties"></a>Vlastnosti typu

Vlastnost | Popis | Povolené hodnoty | Požaduje se
-------- | ----------- | -------------- | --------
jméno | Název webové aktivity | Řetězec | Ano
type | Musí být nastavena na **WebActivity**. | Řetězec | Ano
method | Metoda REST API pro koncový bod cíl. | Řetězec. <br/><br/>Podporované typy: "GET", "POST", "PUT" | Ano
url | Koncový bod cíl a cesty | Řetězec (nebo výraz s hodnotou resultType řetězec). Aktivity bude časový limit na 1 minutu, zobrazí se chyba, pokud neobdrží odpověď z koncového bodu. | Ano
hlavičky | Hlavičky, které se odesílají na požadavek. Chcete-li například nastavit jazyk a typ na vyžádání: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`. | Řetězec (nebo výraz s hodnotou resultType řetězec) | Ano, je požadovaná hlavička Content-type. `"headers":{ "Content-Type":"application/json"}`
hlavní část | Představuje datovou část, která je odeslána koncovému bodu. Vyžaduje se pro metody POST nebo PUT.  | Řetězec (nebo výraz s hodnotou resultType řetězec). <br/><br/>Najdete v části schéma datová část požadavku v [schématu datová část požadavku](#request-payload-schema) části. | Ne
Ověřování | Metodu ověřování pro volání koncový bod. Podporované typy jsou "Basic nebo ClientCertificate." Další informace najdete v tématu [ověřování](#authentication) části. Pokud ověření není vyžadováno, vyloučíte tuto vlastnost. | Řetězec (nebo výraz s hodnotou resultType řetězec) | Ne
Datové sady | Seznam datových sad předaná koncovému bodu. | Pole odkazuje na datovou sadu. Může být prázdné pole. | Ano
linkedServices | Seznam propojené služby předaná koncovému bodu. | Pole Propojená služba odkazuje. Může být prázdné pole. | Ano

> [!NOTE]
> Koncové body REST, která volá webové aktivity musí vracet odpověď typu JSON. Aktivity bude časový limit na 1 minutu, zobrazí se chyba, pokud neobdrží odpověď z koncového bodu.

V následující tabulce jsou uvedeny požadavky pro obsah JSON:

| Typ hodnoty | Tělo požadavku | Text odpovědi |
|---|---|---|
|Objekt JSON | Podporováno | Podporováno |
|Pole JSON | Podporováno <br/>(V současné době pole JSON nefungují v důsledku chyby. Oprava probíhá.) | Nepodporovaný |
| Hodnota JSON | Podporováno | Nepodporovaný |
| JSON bez typu | Nepodporovaný | Nepodporovaný |
||||

## <a name="authentication"></a>Authentication

### <a name="none"></a>Žádný
Pokud ověření není potřeba, nezahrnujte vlastnosti "ověřování".

### <a name="basic"></a>Basic
Zadejte uživatelské jméno a heslo pro použití se základním ověřováním. 

```json
"authentication":{  
   "type":"Basic,
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Klientský certifikát
Zadejte obsah souboru PFX a heslo s kódováním base64. 

```json
"authentication":{  
   "type":"ClientCertificate",
   "pfx":"****",   
   "password":"****"
}
```
## <a name="request-payload-schema"></a>Schéma datová část požadavku
Pokud použijete metodu POST nebo PUT, vlastnosti body představuje datovou část, která je odeslána koncovému bodu. Propojené služby a datové sady můžete předat jako součást datové části. Tady je schéma pro datové části: 

```json
{
    "body": {
        "myMessage": "Sample",
        "datasets": [{
            "name": "MyDataset1",
            "properties": {
                ...
            }
        }],
        "linkedServices": [{
            "name": "MyStorageLinkedService1",
            "properties": {
                ...
            }
        }]
    }
} 
```

## <a name="example"></a>Příklad:
V tomto příkladu webové aktivitu v kanálu zavolá koncový bod REST. Projde propojená služba Azure SQL a datové sadě služby Azure SQL ke koncovému bodu. Koncový bod REST používá připojovací řetězec Azure SQL pro připojení k serveru Azure SQL a vrátí název instance systému SQL server. 

### <a name="pipeline-definition"></a>Definice kanálu

```json
{
    "name": "<MyWebActivityPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyWebActivity>",
                "type": "WebActivity",
                "typeProperties": {
                    "method": "Post",
                    "url": "@pipeline().parameters.url",
                    "headers": {
                        "Content-Type": "application/json"
                    },
                    "authentication": {
                        "type": "ClientCertificate",
                        "pfx": "*****",
                        "password": "*****"
                    },
                    "datasets": [
                        {
                            "referenceName": "MySQLDataset",
                            "type": "DatasetReference",
                            "parameters": {
                                "SqlTableName": "@pipeline().parameters.sqlTableName"
                            }
                        }
                    ],
                    "linkedServices": [
                        {
                            "referenceName": "SqlLinkedService",
                            "type": "LinkedServiceReference"
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "sqlTableName": {
                "type": "String"
            },
            "url": {
                "type": "String"
            }
        }
    }
}

```

### <a name="pipeline-parameter-values"></a>Hodnoty parametru kanálu

```json
{
    "sqlTableName": "department",
    "url": "https://adftes.azurewebsites.net/api/execute/running"
}

```

### <a name="web-service-endpoint-code"></a>Kódu koncový bod webové služby

```csharp

[HttpPost]
public HttpResponseMessage Execute(JObject payload)
{
    Trace.TraceInformation("Start Execute");

    JObject result = new JObject();
    result.Add("status", "complete");

    JArray datasets = payload.GetValue("datasets") as JArray;
    result.Add("sinktable", datasets[0]["properties"]["typeProperties"]["tableName"].ToString());

    JArray linkedServices = payload.GetValue("linkedServices") as JArray;
    string connString = linkedServices[0]["properties"]["typeProperties"]["connectionString"].ToString();

    System.Data.SqlClient.SqlConnection sqlConn = new System.Data.SqlClient.SqlConnection(connString);

    result.Add("sinkServer", sqlConn.DataSource);

    Trace.TraceInformation("Stop Execute");

    return this.Request.CreateResponse(HttpStatusCode.OK, result);
}

```

## <a name="next-steps"></a>Další postup
Najdete v části Další aktivity toku řízení podporovaných službou Data Factory: 

- [Aktivita spuštění kanálu](control-flow-execute-pipeline-activity.md)
- [Pro každou aktivitu](control-flow-for-each-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
