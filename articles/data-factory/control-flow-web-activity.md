---
title: Webová aktivita v Azure Data Factory
description: Zjistěte, jak můžete pomocí webové aktivity, jedné z aktivit toku řízení podporovaných aplikací Data Factory, vyvolat koncový bod REST z kanálu.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: c700c9786f3bec4c79cae904a95deb5fd1c670b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260862"
---
# <a name="web-activity-in-azure-data-factory"></a>Webová aktivita v Azure Data Factory
Webová aktivita slouží k volání vlastního koncového bodu REST z kanálu služby Data Factory. Můžete předávat datové sady a propojené služby, které má aktivita používat a ke kterým má mít přístup.

> [!NOTE]
> Webová aktivita může volat pouze veřejně vystavené adresy URL. Není podporována pro adresy URL, které jsou hostované v privátní virtuální síti.

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
method | Rest API metoda pro cílový koncový bod. | Řetězec. <br/><br/>Podporované typy: "GET", "POST", "PUT" | Ano
url | Cílový koncový bod a cesta | Řetězec (nebo výraz s resultType řetězce). Aktivita bude časový rozsah na 1 minutu s chybou, pokud neobdrží odpověď z koncového bodu. | Ano
Záhlaví | Hlavičky, které jsou odeslány do požadavku. Chcete-li například nastavit jazyk a `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`typ na žádost: . | Řetězec (nebo výraz s resultType řetězce) | Ano, je vyžadováno záhlaví typu obsahu. `"headers":{ "Content-Type":"application/json"}`
text | Představuje datovou část, která je odeslána do koncového bodu.  | Řetězec (nebo výraz s resultType řetězce). <br/><br/>Podívejte se na schéma datové části požadavku v části [Požadavek na schéma datové části.](#request-payload-schema) | Vyžadováno pro metody POST/PUT.
ověřování | Metoda ověřování používaná pro volání koncového bodu. Podporované typy jsou "Základní nebo ClientCertificate." Další informace naleznete v části [Ověřování.](#authentication) Pokud ověřování není vyžadováno, vylučte tuto vlastnost. | Řetězec (nebo výraz s resultType řetězce) | Ne
datové sady | Seznam datových sad předaných koncovému bodu. | Pole odkazů na datové sady. Může být prázdné pole. | Ano
linkedServices | Seznam propojených služeb předán do koncového bodu. | Pole odkazů na propojené služby. Může být prázdné pole. | Ano

> [!NOTE]
> Koncové body REST, které vyvolána webové aktivity musí vrátit odpověď typu JSON. Aktivita bude časový rozsah na 1 minutu s chybou, pokud neobdrží odpověď z koncového bodu.

V následující tabulce jsou uvedeny požadavky na obsah JSON:

| Typ hodnoty | Text požadavku | Text odpovědi |
|---|---|---|
|Objekt JSON | Podporuje se | Podporuje se |
|Pole JSON | Podporuje se <br/>(V současné době pole JSON nefungují v důsledku chyby. Probíhá oprava.) | Nepodporované |
| Hodnota JSON | Podporuje se | Nepodporované |
| Typ jinénež JSON | Nepodporované | Nepodporované |
||||

## <a name="authentication"></a>Ověřování

Níže jsou uvedeny podporované typy ověřování ve webové aktivitě.

### <a name="none"></a>Žádný

Pokud ověřování není vyžadováno, nezahrnejte vlastnost "ověřování".

### <a name="basic"></a>Basic

Zadejte uživatelské jméno a heslo, které se má použít se základním ověřováním.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Klientský certifikát

Zadejte obsah kódu base64 souboru PFX a heslo.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Spravovaná identita

Zadejte identifikátor uri prostředku, pro který bude přístupový token požadován pomocí spravované identity pro datovou továrnu. Chcete-li volat rozhraní API `https://management.azure.com/`pro správu prostředků Azure, použijte . Další informace o tom, jak spravované identity fungují, najdete na [stránce přehledu spravovaných identit pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Pokud je vaše továrna na data nakonfigurovaná pomocí úložiště git, musíte uložit přihlašovací údaje v azure key vaultu, abyste měli použít základní nebo klientské ověřování certifikátů. Azure Data Factory neukládá hesla v gitu.

## <a name="request-payload-schema"></a>Požadovat schéma datové části
Při použití metody POST/PUT, body vlastnost představuje datovou část, která je odeslána do koncového bodu. Propojené služby a datové sady můžete předat jako součást datové části. Zde je schéma datové části:

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

## <a name="example"></a>Příklad
V tomto příkladu webové aktivity v kanálu volá koncový bod REST. Předává azure SQL propojené služby a datové sady Azure SQL do koncového bodu. Koncový bod REST používá připojovací řetězec Azure SQL pro připojení k serveru Azure SQL a vrátí název instance serveru SQL.

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

### <a name="web-service-endpoint-code"></a>Kód koncového bodu webové služby

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

## <a name="next-steps"></a>Další kroky
Podívejte se na další aktivity toku řízení podporované factory:

- [Aktivita spuštění kanálu](control-flow-execute-pipeline-activity.md)
- [Aktivita For Each](control-flow-for-each-activity.md)
- [Získat aktivitu metadat](control-flow-get-metadata-activity.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
