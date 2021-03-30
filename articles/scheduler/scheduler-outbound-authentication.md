---
title: Ověřování odchozích spojení
description: Přečtěte si, jak nastavit nebo odebrat odchozí ověřování pro Azure Scheduler.
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/15/2016
ms.openlocfilehash: 0a8d79af9f45731971cb1be1f39fc193f9d0f0d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "80878965"
---
# <a name="outbound-authentication-for-azure-scheduler"></a>Odchozí ověřování pro Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) nahrazuje [vyřazení](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)Azure Scheduleru. Pokud chcete pokračovat v práci s úlohami, které jste nastavili v plánovači, [migrujte prosím na Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) co nejdříve. 
>
> Plánovač již není v Azure Portal k dispozici, ale [rutiny prostředí PowerShell](scheduler-powershell-reference.md) [REST API](/rest/api/scheduler) a Azure Scheduler jsou v tuto chvíli dostupné, abyste mohli spravovat úlohy a kolekce úloh.

Úlohy Azure Scheduleru můžou potřebovat volat služby, které vyžadují ověřování, jako jsou třeba jiné služby Azure, Salesforce.com, Facebook a zabezpečení vlastních webů. Volaná služba může určit, jestli má úloha Scheduleru přístup k požadovaným prostředkům. 

Plánovač podporuje tyto modely ověřování: 

* Ověřování *certifikátu klienta* při použití certifikátů klientů SSL/TLS
* *Základní* ověřování
* Ověřování *služby Active Directory OAuth*

## <a name="add-or-remove-authentication"></a>Přidat nebo odebrat ověřování

* Chcete-li přidat ověřování do úlohy plánovače při vytváření nebo aktualizaci úlohy, přidejte `authentication` do elementu podřízený prvek JavaScript Object Notation (JSON) `request` . 

  Odpovědi nikdy nevrací tajné kódy, které se předávají službě plánovače prostřednictvím požadavku PUT, PATCH nebo POST v `authentication` objektu. 
  Odpovědi nastavily tajné informace na hodnotu null nebo můžou používat veřejný token, který představuje ověřenou entitu. 

* Chcete-li odebrat ověřování z úlohy Scheduleru, explicitně spusťte požadavek PUT nebo PATCH v úloze a nastavte `authentication` objekt na hodnotu null. Odpověď neobsahuje žádné vlastnosti ověřování.

## <a name="client-certificate"></a>Certifikát klienta

### <a name="request-body---client-certificate"></a>Text žádosti – klientský certifikát

Při přidávání ověřování pomocí `ClientCertificate` modelu zadejte tyto další prvky v textu žádosti.  

| Prvek | Povinné | Popis |
|---------|----------|-------------|
| **ověřování** (nadřazený element) | Objekt ověřování pro použití certifikátu klienta protokolu SSL/TLS |
| **textový** | Ano | Typ ověřování. Pro klientské certifikáty SSL/TLS je hodnota `ClientCertificate` . |
| **PFX** | Ano | Obsah souboru PFX zakódovaný ve formátu base64 |
| **heslo** | Ano | Heslo pro přístup k souboru PFX |
||| 

### <a name="response-body---client-certificate"></a>Tělo odpovědi – klientský certifikát 

Při odeslání požadavku s ověřovacími informacemi obsahuje odpověď tyto ověřovací prvky.

| Element | Popis | 
|---------|-------------| 
| **ověřování** (nadřazený element) | Objekt ověřování pro použití certifikátu klienta protokolu SSL/TLS |
| **textový** | Typ ověřování. Pro klientské certifikáty SSL/TLS je hodnota `ClientCertificate` . |
| **certificateThumbprint** |Kryptografický otisk certifikátu |
| **certificateSubjectName** |Rozlišující název subjektu certifikátu |
| **certificateExpiration** | Datum vypršení platnosti certifikátu |
||| 

### <a name="sample-rest-request---client-certificate"></a>Ukázka žádosti REST – klientský certifikát

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "clientcertificate",
          "password": "password",
          "pfx": "pfx key"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled"
  }
}
```

### <a name="sample-rest-response---client-certificate"></a>Ukázka odpovědi REST – klientský certifikát

```json
HTTP/1.1 200 OKCache-Control: no-cache
Pragma: no-cache
Content-Length: 858
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 56c7b40e-721a-437e-88e6-f68562a73aa8
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 1075219e-e879-4030-bc81-094e54fbabce
x-ms-routing-request-id: WESTUS:20160316T190424Z:1075219e-e879-4030-bc81-094e54fbabce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:04:23 GMT

{
  "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
  "type": "Microsoft.Scheduler/jobCollections/jobs",
  "name": "southeastasiajc/httpjob",
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "certificateThumbprint": "88105CG9DF9ADE75B835711D899296CB217D7055",
          "certificateExpiration": "2021-01-01T07:00:00Z",
          "certificateSubjectName": "CN=Scheduler Mgmt",
          "type": "ClientCertificate"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
    "status": {
      "nextExecutionTime": "2016-03-16T19:05:00Z",
      "executionCount": 0,
      "failureCount": 0,
      "faultedCount": 0
    }
  }
}
```

## <a name="basic"></a>Basic

### <a name="request-body---basic"></a>Text žádosti – základní

Při přidávání ověřování pomocí `Basic` modelu zadejte tyto další prvky v textu žádosti.

| Prvek | Povinné | Popis |
|---------|----------|-------------|
| **ověřování** (nadřazený element) | Objekt ověřování pro použití základního ověřování | 
| **textový** | Ano | Typ ověřování. Pro základní ověřování je tato hodnota `Basic` . | 
| **jmen** | Ano | Uživatelské jméno, které se má ověřit | 
| **heslo** | Ano | Heslo, které se má ověřit |
|||| 

### <a name="response-body---basic"></a>Tělo odpovědi – základní

Při odeslání požadavku s ověřovacími informacemi obsahuje odpověď tyto ověřovací prvky.

| Element | Popis | 
|---------|-------------|
| **ověřování** (nadřazený element) | Objekt ověřování pro použití základního ověřování |
| **textový** | Typ ověřování. Pro základní ověřování je tato hodnota `Basic` . |
| **jmen** | Ověřené uživatelské jméno |
||| 

### <a name="sample-rest-request---basic"></a>Ukázka žádosti REST – základní

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 562
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "basic",
          "username": "user",
          "password": "password"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled"
  }
}
```

### <a name="sample-rest-response---basic"></a>Ukázka odpovědi REST – Basic

```json
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 701
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: a2dcb9cd-1aea-4887-8893-d81273a8cf04
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 7816f222-6ea7-468d-b919-e6ddebbd7e95
x-ms-routing-request-id: WESTUS:20160316T190506Z:7816f222-6ea7-468d-b919-e6ddebbd7e95
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:05:06 GMT

{  
   "id":"/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "username":"user1",
               "type":"Basic"
            }
         },
         "type":"Http"
      },
      "recurrence":{  
         "frequency":"Minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"Enabled",
      "status":{  
         "nextExecutionTime":"2016-03-16T19:06:00Z",
         "executionCount":0,
         "failureCount":0,
         "faultedCount":0
      }
   }
}
```

## <a name="active-directory-oauth"></a>Protokol OAuth pro Active Directory

### <a name="request-body---active-directory-oauth"></a>Tělo žádosti – protokol OAuth pro Active Directory 

Při přidávání ověřování pomocí `ActiveDirectoryOAuth` modelu zadejte tyto další prvky v textu žádosti.

| Prvek | Povinné | Popis |
|---------|----------|-------------|
| **ověřování** (nadřazený element) | Ano | Objekt ověřování pro použití ověřování ActiveDirectoryOAuth |
| **textový** | Ano | Typ ověřování. Pro ověřování ActiveDirectoryOAuth je hodnota `ActiveDirectoryOAuth` . |
| **tenant** | Ano | Identifikátor tenanta pro tenanta služby Azure AD. Pokud chcete najít identifikátor tenanta pro tenanta Azure AD, spusťte `Get-AzureAccount` v Azure PowerShell. |
| **osoby** | Ano | Tato hodnota je nastavena na `https://management.core.windows.net/` . | 
| **clientId** | Ano | Identifikátor klienta pro aplikaci Azure AD | 
| **Tajný kód** | Ano | Tajný kód klienta, který požaduje token | 
|||| 

### <a name="response-body---active-directory-oauth"></a>Tělo odpovědi – Active Directory OAuth

Při odeslání požadavku s ověřovacími informacemi obsahuje odpověď tyto ověřovací prvky.

| Element | Popis |
|---------|-------------|
| **ověřování** (nadřazený element) | Objekt ověřování pro použití ověřování ActiveDirectoryOAuth |
| **textový** | Typ ověřování. Pro ověřování ActiveDirectoryOAuth je hodnota `ActiveDirectoryOAuth` . | 
| **tenant** | Identifikátor tenanta pro tenanta Azure AD |
| **osoby** | Tato hodnota je nastavena na `https://management.core.windows.net/` . |
| **clientId** | Identifikátor klienta pro aplikaci Azure AD |
||| 

### <a name="sample-rest-request---active-directory-oauth"></a>Ukázka žádosti REST – Active Directory OAuth

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 757
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "tenant":"microsoft.onmicrosoft.com",
          "audience":"https://management.core.windows.net/",
          "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
          "secret": "G6u071r8Gjw4V4KSibnb+VK4+tX399hkHaj7LOyHuj5=",
          "type":"ActiveDirectoryOAuth"
        }
      },
      "type": "Http"
    },
    "recurrence": {
      "frequency": "Minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "Enabled"
  }
}
```

### <a name="sample-rest-response---active-directory-oauth"></a>Ukázka odpovědi REST – Active Directory OAuth

```json
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 885
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 86d8e9fd-ac0d-4bed-9420-9baba1af3251
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
x-ms-routing-request-id: WESTUS:20160316T191003Z:5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:10:02 GMT

{
   "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type": "Microsoft.Scheduler/jobCollections/jobs",
   "name": "southeastasiajc/httpjob",
   "properties": {
      "startTime": "2015-05-14T14:10:00Z",
      "action": {  
         "request": {
            "uri": "https://mywebserviceendpoint.com",
            "method": "GET",
            "headers": {  
               "x-ms-version": "2013-03-01"
            },
            "authentication": {  
               "tenant": "microsoft.onmicrosoft.com",
               "audience": "https://management.core.windows.net/",
               "clientId": "dc23e764-9be6-4a33-9b9a-c46e36f0c137",
               "type": "ActiveDirectoryOAuth"
            }
         },
         "type": "Http"
      },
      "recurrence": {  
         "frequency": "minute",
         "endTime": "2016-04-10T08:00:00Z",
         "interval": 1
      },
      "state": "Enabled",
      "status": {  
         "lastExecutionTime": "2016-03-16T19:10:00.3762123Z",
         "nextExecutionTime": "2016-03-16T19:11:00Z",
         "executionCount": 5,
         "failureCount": 5,
         "faultedCount": 1
      }
   }
}
```

## <a name="next-steps"></a>Další kroky

* [Koncepty, terminologie a hierarchie entit služby Azure Scheduler](scheduler-concepts-terms.md)
* [Omezení, výchozí hodnoty a chybové kódy Azure Scheduleru](scheduler-limits-defaults-errors.md)
* [REST API Azure Scheduleru – referenční informace](/rest/api/scheduler)
* [Rutiny PowerShellu pro Azure Scheduler – referenční informace](scheduler-powershell-reference.md)