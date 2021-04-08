---
title: Odstranit rozhraní API pro dotazy sestav
description: Pomocí tohoto rozhraní API můžete odstranit uživatelsky definované dotazy na komerční analýzy na webu Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 4fc3479f1e35970a97684396a7a2e0c0c2582128
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583911"
---
# <a name="delete-report-queries-api"></a>Odstranit rozhraní API pro dotazy sestav

Toto rozhraní API odstraní uživatelsky definované dotazy.

**Syntaxe žádosti**

| **Metoda** | **Identifikátor URI žádosti** |
| --- | --- |
| DELETE | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/{queryId}` |

**Hlavička žádosti**

| **Hlavička** | **Typ** | **Popis** |
| --- | --- | --- |
| Autorizace | řetězec | Povinná hodnota. Přístupový token Azure Active Directory (Azure AD) ve formuláři `Bearer <token>` |
| Typ obsahu | řetězec | `Application/JSON` |

**Parametr cesty**

| **Název parametru** | **Typ** | **Popis** |
| --- | --- | --- |
| `queryId` | řetězec | Filtr, který získá podrobné informace o dotazech s ID daným v tomto argumentu |

**Parametr dotazu**

Žádné

**Datová část požadavku**

Žádné

**Glosář**

Žádné

**Response** (Odpověď)

Datová část odpovědi je strukturována následujícím způsobem ve formátu JSON.

Kód odpovědi: 200, 400, 401, 403, 404, 500

Datová část odpovědi:

```json
{
  "Value": [
    {
      "QueryId": "string",
      "Name": "string",
      "Description": "string",
      "Query": "string",
      "Type": "string",
      "User": "string",
      "CreatedTime": "string",
      "ModifiedTime": "string"
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
| `QueryId` | Jedinečný identifikátor UUID dotazu, který byl odstraněn. |
| `Name` | Název dotazu, který byl odstraněn |
| `Description` | Popis odstraněného dotazu |
| `Query` | Řetězec dotazu na sestavu pro odstraněný dotaz |
| `Type` | Definovánouživatelem |
| `User` | ID uživatele, který vytvořil dotaz |
| `CreatedTime` | Čas, kdy byl dotaz vytvořen |
| `ModifiedTime` | Null |
| `TotalCount` | Počet datových sad v poli hodnot |
| `StatusCode` | Kód výsledku. Možné hodnoty jsou 200, 400, 401, 403, 500 |
