---
title: Webové aktivity ve službě Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak můžete pomocí webová aktivita, jeden z aktivity toku řízení podporovaných službou Data Factory, který má být vyvolán koncového bodu REST z kanálu.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: shlo
ms.openlocfilehash: 1c657fd7b3059dcf46a371b133a5d078e02f0599
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54014195"
---
# <a name="web-activity-in-azure-data-factory"></a>Aktivita webu ve službě Azure Data Factory
Webová aktivita slouží k volání vlastního koncového bodu REST z kanálu služby Data Factory. Můžete předávat datové sady a propojené služby, které má aktivita používat a ke kterým má mít přístup. 

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

## <a name="type-properties"></a>Typ vlastnosti

Vlastnost | Popis | Povolené hodnoty | Požaduje se
-------- | ----------- | -------------- | --------
jméno | Název aktivity webu | Řetězec | Ano
type | Musí být nastaveno na **WebActivity**. | Řetězec | Ano
method | Metoda REST API pro cílový koncový bod. | řetězec. <br/><br/>Podporované typy: "GET", "POST", "UMÍSTĚNÍ" | Ano
url | Cílový koncový bod a cesty | Řetězec (nebo výraz s hodnotu resultType řetězec). Aktivita vyprší časový limit na 1 minutu s chybou, pokud neobdrží odpověď z koncového bodu. | Ano
Záhlaví | Hlavičky, které se odesílají na požadavek. Například nastavení jazyka a typu na vyžádání: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`. | Řetězec (nebo výraz s hodnotu resultType řetězec) | Ano, vyžaduje se hlavička Content-type. `"headers":{ "Content-Type":"application/json"}`
hlavní část | Představuje datovou část, která je odeslána do koncového bodu.  | Řetězec (nebo výraz s hodnotu resultType řetězec). <br/><br/>Zobrazit schéma datové části požadavku v [schématu datové části požadavku](#request-payload-schema) oddílu. | Vyžaduje se pro metody POST a PUT.
Ověřování | Metodu ověřování pro volání koncového bodu. Podporované typy jsou "Basic nebo ClientCertificate." Další informace najdete v tématu [ověřování](#authentication) oddílu. Pokud se nevyžaduje ověřování, vylučte tuto vlastnost. | Řetězec (nebo výraz s hodnotu resultType řetězec) | Ne
datové sady | Seznam datových sad předaná koncovému bodu. | Pole odkazů na datovou sadu. Může být prázdné pole. | Ano
linkedServices | Seznam propojených služeb předaná koncovému bodu. | Pole odkazů na propojenou službu. Může být prázdné pole. | Ano

> [!NOTE]
> Koncové body REST, které vyvolá aktivitu webu musí vracet odpověď typu JSON. Aktivita vyprší časový limit na 1 minutu s chybou, pokud neobdrží odpověď z koncového bodu.

V následující tabulce jsou uvedeny požadavky pro obsah JSON:

| Typ hodnoty | Text požadavku | Text odpovědi |
|---|---|---|
|JSON – objekt | Podporováno | Podporováno |
|Pole JSON | Podporováno <br/>(V současné době pole JSON nefungují v důsledku chyby. Oprava probíhá.) | Nepodporovaný |
| Hodnota JSON | Podporováno | Nepodporovaný |
| Typ non-JSON | Nepodporovaný | Nepodporovaný |
||||

## <a name="authentication"></a>Authentication

### <a name="none"></a>Žádný
Pokud se nevyžaduje ověřování, nezahrnujte vlastnosti "ověřování".

### <a name="basic"></a>Basic
Zadejte uživatelské jméno a heslo pro použití se základním ověřováním. 

```json
"authentication":{  
   "type":"Basic",
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

### <a name="managed-identity"></a>Spravovaná identita

Zadejte identifikátor uri prostředku, pro kterou přístupový token bude vyžádána pomocí spravované identity služby data factory. Chcete-li volat rozhraní API pro správu prostředků Azure, použijte `https://management.azure.com/`.

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

## <a name="request-payload-schema"></a>Schéma datové části požadavku
Pokud použijete metodu POST a PUT, vlastnost text představuje datová část odeslaná do koncového bodu. Propojené služby a datové sady můžete předat jako součást datové části. Tady je schéma pro datové části: 

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
V tomto příkladu volá webová aktivita v kanálu koncový bod REST. Předá propojené služby Azure SQL a datová sada služby Azure SQL do koncového bodu. Koncový bod REST používá připojovací řetězec Azure SQL pro připojení k serveru Azure SQL a vrátí název instance systému SQL server. 

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

### <a name="pipeline-parameter-values"></a>Hodnoty parametrů kanálu

```json
{
    "sqlTableName": "department",
    "url": "https://adftes.azurewebsites.net/api/execute/running"
}

```

### <a name="web-service-endpoint-code"></a>Koncový bod kódu webové služby

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
Zobrazit další aktivity toku řízení podporovaných službou Data Factory: 

- [Aktivita spuštění kanálu](control-flow-execute-pipeline-activity.md)
- [Pro každou aktivitu](control-flow-for-each-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
