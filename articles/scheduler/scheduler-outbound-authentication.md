---
title: Ověřování odchozích spojení - Azure Scheduleru
description: Zjistěte, jak nastavit nebo odebrat odchozí ověřování pro Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 6707f82b-7e32-401b-a960-02aae7bb59cc
ms.topic: article
ms.date: 08/15/2016
ms.openlocfilehash: 88f2fe0781bad4b652826b6a8d1961dd39b063e1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993325"
---
# <a name="outbound-authentication-for-azure-scheduler"></a>Ověřování odchozích připojení pro Azure Scheduler

> [!IMPORTANT]
> [Služba Azure Logic Apps](../logic-apps/logic-apps-overview.md) nahrazuje Azure Scheduleru, která se vyřazuje. K plánování úloh, [místo toho vyzkoušet Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

Azure Scheduleru úlohy může být nutné volat služby, které vyžadují ověřování, jako je například jiných služeb Azure, Salesforce.com, Facebook a zabezpečené vlastní weby. Volané služby můžete určit, jestli úloha Scheduleru můžete přistupovat k požadovaným prostředkům. 

Scheduler podporuje tyto modely ověřování: 

* *Klientský certifikát* ověřování při použití klientské certifikáty SSL/TLS.
* *Základní* ověřování
* *Active Directory OAuth* ověřování

## <a name="add-or-remove-authentication"></a>Přidání nebo odebrání ověřování

* Chcete-li přidat ověřování do úlohy Scheduleru, při vytváření nebo aktualizace úlohy, přidejte `authentication` podřízený element zápis JSON (JavaScript Object) do `request` element. 

  Nikdy nevracet tajné kódy, které se předávají služby Plánovač prostřednictvím požadavku PUT, PATCH nebo příspěvek v odpovědi `authentication` objektu. 
  Odpovědi tajných informací nastavena na hodnotu null, nebo použít veřejné token, který představuje ověřený entity. 

* Pokud chcete odebrat ověřování z úlohy Scheduleru, explicitně požadavek PUT nebo PATCH spouštět úlohy a nastavte `authentication` objekt má hodnotu null. Odpověď nebude obsahovat všechny vlastnosti ověřování.

## <a name="client-certificate"></a>Klientský certifikát

### <a name="request-body---client-certificate"></a>Text požadavku - klientského certifikátu

Při přidání pomocí ověřování `ClientCertificate` model, určit tyto další prvky v textu požadavku.  

| Element | Požaduje se | Popis |
|---------|----------|-------------|
| **ověřování** (nadřazený element) | Objekt ověřování pro použití klientský certifikát SSL |
| **type** | Ano | Typ ověřování. Pro klientské certifikáty SSL, je hodnota `ClientCertificate`. |
| **PFX** | Ano | Obsah souboru PFX s kódováním base64 |
| **Heslo** | Ano | Heslo pro přístup k souboru PFX |
||| 

### <a name="response-body---client-certificate"></a>Text odpovědi - klientského certifikátu 

Žádost o odeslání se informace o ověřování, odpověď obsahuje tyto prvky ověřování.

| Element | Popis | 
|---------|-------------| 
| **ověřování** (nadřazený element) | Objekt ověřování pro použití klientský certifikát SSL |
| **type** | Typ ověřování. Pro klientské certifikáty SSL, je hodnota `ClientCertificate`. |
| **Miniatura certifikátu** |Kryptografický otisk certifikátu |
| **CertificateSubjectName** |Rozlišující název subjektu certifikátu |
| **certificateExpiration** | Datum vypršení platnosti certifikátu |
||| 

### <a name="sample-rest-request---client-certificate"></a>Ukázka požadavku REST - klientského certifikátu

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

### <a name="sample-rest-response---client-certificate"></a>Ukázková odpověď REST - klientského certifikátu

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

### <a name="request-body---basic"></a>Text – Basic žádosti

Při přidání pomocí ověřování `Basic` model, určit tyto další prvky v textu požadavku.

| Element | Požaduje se | Popis |
|---------|----------|-------------|
| **ověřování** (nadřazený element) | Objekt ověřování pro použití základního ověřování | 
| **type** | Ano | Typ ověřování. Pro základní ověřování, je hodnota `Basic`. | 
| **uživatelské jméno** | Ano | Uživatelské jméno pro ověřování | 
| **Heslo** | Ano | Heslo k ověření |
|||| 

### <a name="response-body---basic"></a>Text odpovědi – Basic

Žádost o odeslání se informace o ověřování, odpověď obsahuje tyto prvky ověřování.

| Element | Popis | 
|---------|-------------|
| **ověřování** (nadřazený element) | Objekt ověřování pro použití základního ověřování |
| **type** | Typ ověřování. Pro základní ověřování, je hodnota `Basic`. |
| **uživatelské jméno** | Ověřené uživatelské jméno |
||| 

### <a name="sample-rest-request---basic"></a>Ukázka požadavku REST – Basic

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

### <a name="sample-rest-response---basic"></a>Ukázková odpověď REST – Basic

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

### <a name="request-body---active-directory-oauth"></a>Text – Active Directory OAuth žádosti 

Při přidání pomocí ověřování `ActiveDirectoryOAuth` model, určit tyto další prvky v textu požadavku.

| Element | Požaduje se | Popis |
|---------|----------|-------------|
| **ověřování** (nadřazený element) | Ano | Objekt ověřování pro použití ověřování ActiveDirectoryOAuth |
| **type** | Ano | Typ ověřování. ActiveDirectoryOAuth ověřování, je hodnota `ActiveDirectoryOAuth`. |
| **tenanta** | Ano | Identifikátor tenanta pro tenanta Azure AD. Chcete-li najít identifikátor tenanta pro tenanta Azure AD, spusťte `Get-AzureAccount` v prostředí Azure PowerShell. |
| **Cílová skupina** | Ano | Tato hodnota nastavená na `https://management.core.windows.net/`. | 
| **ID klienta** | Ano | Identifikátor klienta pro aplikaci Azure AD | 
| **Tajný klíč** | Ano | Tajný klíč pro klienta, který žádá token | 
|||| 

### <a name="response-body---active-directory-oauth"></a>Text odpovědi – Active Directory OAuth

Žádost o odeslání se informace o ověřování, odpověď obsahuje tyto prvky ověřování.

| Element | Popis |
|---------|-------------|
| **ověřování** (nadřazený element) | Objekt ověřování pro použití ověřování ActiveDirectoryOAuth |
| **type** | Typ ověřování. ActiveDirectoryOAuth ověřování, je hodnota `ActiveDirectoryOAuth`. | 
| **tenanta** | Identifikátor tenanta pro tenanta Azure AD |
| **Cílová skupina** | Tato hodnota nastavená na `https://management.core.windows.net/`. |
| **ID klienta** | Identifikátor klienta pro aplikaci Azure AD |
||| 

### <a name="sample-rest-request---active-directory-oauth"></a>Ukázka požadavku REST - Active Directory OAuth

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

### <a name="sample-rest-response---active-directory-oauth"></a>Ukázková odpověď REST - Active Directory OAuth

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

## <a name="see-also"></a>Další informace najdete v tématech

* [Co je Azure Scheduler?](scheduler-intro.md)
* [Koncepty, terminologie a hierarchie entit Azure Scheduleru](scheduler-concepts-terms.md)
* [Omezení, výchozí hodnoty a chybové kódy Azure Scheduleru](scheduler-limits-defaults-errors.md)
* [Rozhraní REST API služby Azure Scheduler](https://msdn.microsoft.com/library/mt629143)
* [Rutiny PowerShellu pro Azure Scheduler – referenční informace](scheduler-powershell-reference.md)
