---
title: Odchozí ověření scheduleru
description: Odchozí ověření scheduleru
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: 6707f82b-7e32-401b-a960-02aae7bb59cc
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/15/2016
ms.author: deli
ms.openlocfilehash: e345b2e22daae5b24c23645f7d2636f66df630ff
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23926505"
---
# <a name="scheduler-outbound-authentication"></a>Odchozí ověření scheduleru
Plánovač úloh možná muset volat na služby, které vyžadují ověřování. Tímto způsobem názvem služby můžete určit Pokud úlohy plánovače může přistupovat k prostředkům. Některé z těchto služeb zahrnují dalším službám Azure, Salesforce.com, Facebook a zabezpečené vlastní weby.

## <a name="adding-and-removing-authentication"></a>Přidávání a odebírání ověřování
Přidání ověřování do úlohy plánovače je jednoduchá – přidání podřízený element JSON `authentication` k `request` element při vytváření nebo aktualizaci úlohy. Tajné klíče předaný služby plánovače v požadavku PUT, PATCH nebo POST – jako součást `authentication` objektu – nikdy jsou vráceny v odpovědi. V odpovědi, tajné informace nastavena na hodnotu null, nebo může mít veřejné token, který představuje ověřený entitu.

Pokud chcete odebrat ověřování, PUT nebo PATCH úlohu explicitně, nastavení `authentication` objekt, který má hodnotu null. Neuvidíte žádné vlastnosti ověřování zpět v odpovědi.

V současné době jsou typy podporované jenom ověřování `ClientCertificate` (pro použití protokolu SSL/TLS klientské certifikáty), `Basic` (pro základní ověřování) a `ActiveDirectoryOAuth` (pro ověřování Active Directory OAuth.)

## <a name="request-body-for-clientcertificate-authentication"></a>Text žádosti pro ClientCertificate ověřování
Při přidávání ověřování pomocí `ClientCertificate` model, zadejte následující další prvky v textu požadavku.  

| Element | Popis |
|:--- |:--- |
| *ověřování (nadřazený element)* |Objekt ověřování pro použití klientský certifikát SSL. |
| *Typ* |Povinná hodnota. Typ ověřování. Pro klientské certifikáty protokolu SSL, hodnota musí být `ClientCertificate`. |
| *soubor PFX* |Povinná hodnota. Kódování Base64 obsah souboru PFX. |
| *heslo* |Povinná hodnota. Heslo pro přístup k souboru PFX. |

## <a name="response-body-for-clientcertificate-authentication"></a>Text odpovědi pro ClientCertificate ověřování
Když přijde požadavek je s informacemi ověřování, odpověď obsahuje následující prvky souvisejících s ověřováním.

| Element | Popis |
|:--- |:--- |
| *ověřování (nadřazený element)* |Objekt ověřování pro použití klientský certifikát SSL. |
| *Typ* |Typ ověřování. Pro klientské certifikáty protokolu SSL, je hodnota `ClientCertificate`. |
| *certificateThumbprint* |Kryptografický otisk certifikátu. |
| *certificateSubjectName* |Rozlišující název subjektu certifikátu. |
| *certificateExpiration* |Datum vypršení platnosti certifikátu. |

## <a name="sample-rest-request-for-clientcertificate-authentication"></a>Ukázka REST žádost o ověření ClientCertificate
```
PUT https://management.azure.com/subscriptions/1fe0abdf-581e-4dfe-9ec7-e5cb8e7b205e/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
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
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-clientcertificate-authentication"></a>Ukázková odpověď REST pro ClientCertificate ověřování
```
HTTP/1.1 200 OK
Cache-Control: no-cache
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
  "id": "/subscriptions/1fe0abdf-581e-4dfe-9ec7-e5cb8e7b205e/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
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

## <a name="request-body-for-basic-authentication"></a>Text žádosti pro základní ověřování
Při přidávání ověřování pomocí `Basic` model, zadejte následující další prvky v textu požadavku.

| Element | Popis |
|:--- |:--- |
| *ověřování (nadřazený element)* |Objekt ověřování pro použití základního ověřování. |
| *Typ* |Povinná hodnota. Typ ověřování. Pro základní ověřování, musí být hodnota `Basic`. |
| *uživatelské jméno* |Povinná hodnota. Uživatelské jméno k ověření. |
| *heslo* |Povinná hodnota. Heslo k ověření. |

## <a name="response-body-for-basic-authentication"></a>Text odpovědi pro základní ověřování
Když přijde požadavek je s informacemi ověřování, odpověď obsahuje následující prvky souvisejících s ověřováním.

| Element | Popis |
|:--- |:--- |
| *ověřování (nadřazený element)* |Objekt ověřování pro použití základního ověřování. |
| *Typ* |Typ ověřování. Pro základní ověřování, je hodnota `Basic`. |
| *uživatelské jméno* |Ověřené uživatelské jméno. |

## <a name="sample-rest-request-for-basic-authentication"></a>Ukázková žádost REST pro základní ověřování
```
PUT https://management.azure.com/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
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
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-basic-authentication"></a>Ukázková odpověď REST pro základní ověřování
```
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
   "id":"/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
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
         "type":"http"
      },
      "recurrence":{  
         "frequency":"minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"enabled",
      "status":{  
         "nextExecutionTime":"2016-03-16T19:06:00Z",
         "executionCount":0,
         "failureCount":0,
         "faultedCount":0
      }
   }
}
```

## <a name="request-body-for-activedirectoryoauth-authentication"></a>Text žádosti pro ActiveDirectoryOAuth ověřování
Při přidávání ověřování pomocí `ActiveDirectoryOAuth` model, zadejte následující další prvky v textu požadavku.

| Element | Popis |
|:--- |:--- |
| *ověřování (nadřazený element)* |Objekt ověřování pro použití ActiveDirectoryOAuth ověřování. |
| *Typ* |Povinná hodnota. Typ ověřování. ActiveDirectoryOAuth ověřování, musí být hodnota `ActiveDirectoryOAuth`. |
| *klienta* |Povinná hodnota. Identifikátor klienta pro klienta Azure AD. |
| *Cílová skupina* |Povinná hodnota. To je nastavena na https://management.core.windows.net/. |
| *clientId* |Povinná hodnota. Zadejte identifikátor klienta aplikace Azure AD. |
| *tajný klíč* |Povinná hodnota. Tajný klíč klienta, který vyžaduje token. |

### <a name="determining-your-tenant-identifier"></a>Určení ID vašeho klienta
Identifikátor klienta pro klienta služby Azure AD můžete najít spuštěním `Get-AzureAccount` v prostředí Azure PowerShell.

## <a name="response-body-for-activedirectoryoauth-authentication"></a>Text odpovědi pro ActiveDirectoryOAuth ověřování
Když přijde požadavek je s informacemi ověřování, odpověď obsahuje následující prvky souvisejících s ověřováním.

| Element | Popis |
|:--- |:--- |
| *ověřování (nadřazený element)* |Objekt ověřování pro použití ActiveDirectoryOAuth ověřování. |
| *Typ* |Typ ověřování. Pro ověřování ActiveDirectoryOAuth hodnota je `ActiveDirectoryOAuth`. |
| *klienta* |Identifikátor klienta pro klienta Azure AD. |
| *Cílová skupina* |To je nastavena na https://management.core.windows.net/. |
| *clientId* |Identifikátor klienta aplikace Azure AD. |

## <a name="sample-rest-request-for-activedirectoryoauth-authentication"></a>Ukázka REST žádost o ověření ActiveDirectoryOAuth
```
PUT https://management.azure.com/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
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
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-activedirectoryoauth-authentication"></a>Ukázková odpověď REST pro ActiveDirectoryOAuth ověřování
```
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
   "id":"/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
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
               "tenant":"microsoft.onmicrosoft.com",
               "audience":"https://management.core.windows.net/",
               "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
               "type":"ActiveDirectoryOAuth"
            }
         },
         "type":"http"
      },
      "recurrence":{  
         "frequency":"minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"enabled",
      "status":{  
         "lastExecutionTime":"2016-03-16T19:10:00.3762123Z",
         "nextExecutionTime":"2016-03-16T19:11:00Z",
         "executionCount":5,
         "failureCount":5,
         "faultedCount":1
      }
   }
}
```

## <a name="see-also"></a>Viz také
 [Co je Scheduler?](scheduler-intro.md)

 [Koncepty, terminologie a hierarchie entit Azure Scheduleru](scheduler-concepts-terms.md)

 [Úvod do používání Scheduleru na portálu Azure Portal](scheduler-get-started-portal.md)

 [Plány a fakturace v Azure Scheduleru](scheduler-plans-billing.md)

 [REST API Azure Scheduleru – referenční informace](https://msdn.microsoft.com/library/mt629143)

 [Rutiny PowerShellu pro Azure Scheduler – referenční informace](scheduler-powershell-reference.md)

 [Vysoká dostupnost a spolehlivost Azure Scheduleru](scheduler-high-availability-reliability.md)

 [Omezení, výchozí hodnoty a chybové kódy Azure Scheduleru](scheduler-limits-defaults-errors.md)

