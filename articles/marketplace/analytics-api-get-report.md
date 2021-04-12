---
title: Získat rozhraní API pro sestavy
description: Pomocí tohoto rozhraní API získáte analytické sestavy, které byly naplánovány v partnerském centru.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 3383af447f40ea984bce9cbc956f22ee6c5af200
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583762"
---
# <a name="get-report-api"></a>Získat rozhraní API pro sestavy

Toto rozhraní API získá všechny plánované sestavy.

**Syntaxe žádosti**

| **Metoda** | **Identifikátor URI žádosti** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport?reportId={Report ID}&reportName={Report Name}&queryId={Query ID} ` |

**Hlavička žádosti**

| **Hlavička** | **Typ** | **Popis** |
| --- | --- | --- |
| Autorizace | řetězec | Povinná hodnota. Přístupový token Azure Active Directory (Azure AD) ve formuláři `Bearer <token>` |
| Typ obsahu | řetězec | `Application/JSON` |

**Parametr cesty**

Žádné

**Parametr dotazu**

| **Název parametru** | **Povinné** | **Typ** | **Popis** |
| --- | --- | --- | --- |
| `reportId` | No | řetězec | Filtr pro získání podrobností o pouze sestavách, které jsou `reportId` uvedeny v tomto argumentu |
| `reportName` | No | řetězec | Filtr pro získání podrobností o pouze sestavách, které jsou `reportName` uvedeny v tomto argumentu |
| `queryId` | No | boolean | Zahrnout předdefinované systémové dotazy do odpovědi |

**Glosář**

Žádné

**Response** (Odpověď)

Datová část odpovědi je strukturována ve formátu JSON následujícím způsobem:

Kód odpovědi: 200, 400, 401, 403, 404, 500

Datová část odpovědi:

```json
{
  "Value": [
    {
      "ReportId": "string",
      "ReportName": "string",
      "Description": "string",
      "QueryId": "string",
      "Query": "string",
      "User": "string",
      "CreatedTime": "string",
      "ModifiedTime": "string",
      "StartTime": "string",
      "ReportStatus": "string",
      "RecurrenceInterval": 0,
      " RecurrenceCount": 0,
      "CallbackUrl": "string",
      "Format": "string"
    }
  ],
  "TotalCount": 0,
  "Message": "string",
  "StatusCode": 0
}
```

**Glosář**

V této tabulce jsou uvedeny klíčové definice prvků v odpovědi.

| **Parametr** | **Popis** |
| --- | --- |
| `ReportId` | Jedinečný identifikátor UUID sestavy, která byla vytvořena |
| `ReportName` | Název zadaný pro sestavu v datové části požadavku |
| `Description` | Popis zadaný při vytvoření sestavy |
| `QueryId` | ID dotazu předané v okamžiku vytvoření sestavy |
| `Query` | Text dotazu, který se spustí pro tuto sestavu |
| `User` | ID uživatele, které se použilo k vytvoření sestavy |
| `CreatedTime` | Čas, kdy byla sestava vytvořena. Formát času je RRRR-MM-ddTHH: mm: ssZ |
| `ModifiedTime` | Čas poslední změny sestavy Formát času je RRRR-MM-ddTHH: mm: ssZ |
| `StartTime` | Čas, kdy se spustí spuštění. Formát času je RRRR-MM-ddTHH: mm: ssZ |
| `ReportStatus` | Stav provádění sestavy Možné hodnoty jsou pozastavené, aktivní a neaktivní. |
| `RecurrenceInterval` | Interval opakování zadaný při vytváření sestavy |
| `RecurrenceCount` | Počet opakování poskytnutý během vytváření sestavy |
| `CallbackUrl` | Adresa URL zpětného volání poskytnutá v žádosti |
| `Format` | Formát souborů sestav |
