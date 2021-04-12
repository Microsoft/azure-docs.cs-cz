---
title: Získat rozhraní API pro dotazy sestav
description: Pomocí tohoto rozhraní API získáte všechny dotazy, které jsou k dispozici pro použití v komerčních sestavách webu Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: e2be43e8402e5179fb62d810fe7b9f41e704c49d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583869"
---
# <a name="get-report-queries-api"></a>Získat rozhraní API pro dotazy sestav

Rozhraní API pro dotazy Get Report získá všechny dotazy, které jsou k dispozici pro použití v sestavách. Ve výchozím nastavení získá všechny dotazy definované systémem a uživatelem.

**Syntaxe žádosti**

| **Metoda** | **Identifikátor URI žádosti** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries?queryId={QueryID}&queryName={QueryName}&includeSystemQueries={include_system_queries}&includeOnlySystemQueries={include_only_system_queries}` |
|||

**Hlavička žádosti**

| **Hlavička** | **Typ** | **Popis** |
| --- | --- | --- |
| Autorizace | řetězec | Povinná hodnota. Přístupový token Azure Active Directory (Azure AD) ve formuláři `Bearer <token>` |
| Typ obsahu | řetězec | `Application/JSON` |
||||

**Parametr cesty**

Žádné

**Parametr dotazu**

| **Název parametru** | **Typ** | **Povinné** | **Popis** |
| --- | --- | --- | --- |
| `queryId` | řetězec | No | Filtr pro získání podrobností o dotazech s ID uvedeným v argumentu |
| `queryName` | řetězec | No | Filtr, který získá podrobné informace o dotazech s názvem zadaným v argumentu |
| `IncludeSystemQueries` | boolean | No | Zahrnout předdefinované systémové dotazy do odpovědi |
| `IncludeOnlySystemQueries` | boolean | No | Zahrnout do odpovědi pouze systémové dotazy |
|||||

**Datová část požadavku**

Žádné

**Glosář**

Žádné

**Response** (Odpověď)

Datová část odpovědi je strukturována takto:

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

Tato tabulka popisuje klíčové definice prvků v odpovědi.

| **Parametr** | **Popis** |
| --- | --- |
| `QueryId` | Jedinečný identifikátor UUID dotazu |
| `Name` | Název zadaný pro dotaz v době vytváření dotazu |
| `Description` | Popis zadaný při vytváření dotazu |
| `Query` | Řetězec dotazu sestavy |
| `Type` | Nastaví se na `userDefined` dotazy vytvořené uživatelem a `system` předdefinované systémové dotazy. |
| `User` | ID uživatele, který vytvořil dotaz |
| `CreatedTime` | Čas vytvoření dotazu |
| `TotalCount` | Počet datových sad v poli hodnot |
| `StatusCode` | Kód výsledku. Možné hodnoty jsou 200, 400, 401, 403, 500 |
|||
