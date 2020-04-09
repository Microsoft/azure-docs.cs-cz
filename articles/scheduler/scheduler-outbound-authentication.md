---
title: Ověřování odchozích spojení
description: Zjistěte, jak nastavit nebo odebrat odchozí ověřování pro Plánovač Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/15/2016
ms.openlocfilehash: 0a8d79af9f45731971cb1be1f39fc193f9d0f0d9
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878965"
---
# <a name="outbound-authentication-for-azure-scheduler"></a>Odchozí ověřování pro Plánovač Azure

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) nahrazuje Azure Scheduler, který [je vyřazen .](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Pokud chcete pokračovat v práci s úlohami, které jste nastavili v Plánovači, [migrujte co](../scheduler/migrate-from-scheduler-to-logic-apps.md) nejdříve do Azure Logic Apps. 
>
> Plánovač už není k dispozici na webu Azure Portal, ale rutiny [ROZHRANÍ REST API](/rest/api/scheduler) a Azure [Scheduler PowerShell](scheduler-powershell-reference.md) zůstávají k dispozici v tuto chvíli, takže můžete spravovat své úlohy a kolekce úloh.

Úlohy Plánovače Azure může mít volání služeb, které vyžadují ověřování, jako jsou jiné služby Azure, Salesforce.com, Facebook a zabezpečené vlastní weby. Volaná služba může určit, zda úloha Plánovač může přistupovat k požadovaným prostředkům. 

Plánovač podporuje tyto modely ověřování: 

* *Ověření klientského certifikátu* při použití klientských certifikátů SSL/TLS
* *Základní* ověřování
* *Ověřování o službě Active Directory OAuth*

## <a name="add-or-remove-authentication"></a>Přidání nebo odebrání ověřování

* Chcete-li přidat ověřování do úlohy Plánovače, při `authentication` vytváření nebo aktualizaci úlohy přidejte do `request` elementu podřízený prvek JavaScript Object Notation (JSON). 

  Odpovědi nikdy vrátit tajné klíče, které jsou předány službě Plánovač prostřednictvím `authentication` PUT, PATCH nebo POST požadavek v objektu. 
  Odpovědi nastavit tajné informace na hodnotu null nebo může použít veřejný token, který představuje ověřenou entitu. 

* Chcete-li odebrat ověřování z úlohy plánovače, explicitně spusťte požadavek PUT nebo PATCH na úloze a nastavte objekt na hodnotu `authentication` null. Odpověď nebude obsahovat žádné vlastnosti ověřování.

## <a name="client-certificate"></a>Klientský certifikát

### <a name="request-body---client-certificate"></a>Tělo požadavku - Klientský certifikát

Při přidávání `ClientCertificate` ověřování pomocí modelu zadejte tyto další prvky v těle požadavku.  

| Element | Požaduje se | Popis |
|---------|----------|-------------|
| **ověřování** (nadřazený prvek) | Ověřovací objekt pro použití klientského certifikátu SSL/TLS |
| **Typ** | Ano | Typ ověřování. Pro klientské certifikáty SSL/TLS `ClientCertificate`je hodnota . |
| **Pfx** | Ano | Obsah souboru PFX kódovaný na základně 64 |
| **heslo** | Ano | Heslo pro přístup k souboru PFX |
||| 

### <a name="response-body---client-certificate"></a>Tělo odpovědi - Klientský certifikát 

Při odeslání požadavku s ověřovacími informacemi odpověď obsahuje tyto prvky ověřování.

| Element | Popis | 
|---------|-------------| 
| **ověřování** (nadřazený prvek) | Ověřovací objekt pro použití klientského certifikátu SSL/TLS |
| **Typ** | Typ ověřování. Pro klientské certifikáty SSL/TLS `ClientCertificate`je hodnota . |
| **certificateThumbprint** |Kryptografický otisk certifikátu |
| **název předmětu certifikátu** |Rozlišující název subjektu certifikátu |
| **vypršení platnosti certifikátu** | Datum vypršení platnosti certifikátu |
||| 

### <a name="sample-rest-request---client-certificate"></a>Ukázkový požadavek REST – klientský certifikát

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

### <a name="sample-rest-response---client-certificate"></a>Ukázková odpověď REST – klientský certifikát

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

## <a name="basic"></a>Základní

### <a name="request-body---basic"></a>Tělo požadavku - základní

Při přidávání `Basic` ověřování pomocí modelu zadejte tyto další prvky v těle požadavku.

| Element | Požaduje se | Popis |
|---------|----------|-------------|
| **ověřování** (nadřazený prvek) | Objekt ověřování pro použití základního ověřování | 
| **Typ** | Ano | Typ ověřování. Pro základní ověřování je `Basic`hodnota . | 
| **Username** | Ano | Uživatelské jméno k ověření | 
| **heslo** | Ano | Heslo k ověření |
|||| 

### <a name="response-body---basic"></a>Tělo odezvy - základní

Při odeslání požadavku s ověřovacími informacemi odpověď obsahuje tyto prvky ověřování.

| Element | Popis | 
|---------|-------------|
| **ověřování** (nadřazený prvek) | Objekt ověřování pro použití základního ověřování |
| **Typ** | Typ ověřování. Pro základní ověřování je `Basic`hodnota . |
| **Username** | Ověřené uživatelské jméno |
||| 

### <a name="sample-rest-request---basic"></a>Ukázkový požadavek REST - základní

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

### <a name="sample-rest-response---basic"></a>Ukázková odpověď REST - základní

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

## <a name="active-directory-oauth"></a>Active Directory OAuth

### <a name="request-body---active-directory-oauth"></a>Tělo požadavku - Active Directory OAuth 

Při přidávání `ActiveDirectoryOAuth` ověřování pomocí modelu zadejte tyto další prvky v těle požadavku.

| Element | Požaduje se | Popis |
|---------|----------|-------------|
| **ověřování** (nadřazený prvek) | Ano | Ověřovací objekt pro použití ověřování ActiveDirectoryOAuth |
| **Typ** | Ano | Typ ověřování. Pro ověřování ActiveDirectoryOAuth je `ActiveDirectoryOAuth`hodnota . |
| **Nájemce** | Ano | Identifikátor klienta pro klienta Azure AD. Pokud chcete najít identifikátor klienta pro `Get-AzureAccount` klienta Azure AD, spusťte v Azure PowerShellu. |
| **Publikum** | Ano | Tato hodnota je `https://management.core.windows.net/`nastavena na . | 
| **Clientid** | Ano | Identifikátor klienta pro aplikaci Azure AD | 
| **Tajemství** | Ano | Tajný klíč pro klienta, který požaduje token | 
|||| 

### <a name="response-body---active-directory-oauth"></a>Tělo odpovědi – Active Directory OAuth

Při odeslání požadavku s ověřovacími informacemi odpověď obsahuje tyto prvky ověřování.

| Element | Popis |
|---------|-------------|
| **ověřování** (nadřazený prvek) | Ověřovací objekt pro použití ověřování ActiveDirectoryOAuth |
| **Typ** | Typ ověřování. Pro ověřování ActiveDirectoryOAuth je `ActiveDirectoryOAuth`hodnota . | 
| **Nájemce** | Identifikátor klienta pro klienta Azure AD |
| **Publikum** | Tato hodnota je `https://management.core.windows.net/`nastavena na . |
| **Clientid** | Identifikátor klienta pro aplikaci Azure AD |
||| 

### <a name="sample-rest-request---active-directory-oauth"></a>Ukázkový požadavek REST – služba Active Directory OAuth

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

### <a name="sample-rest-response---active-directory-oauth"></a>Ukázková odpověď REST – služba Active Directory OAuth

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