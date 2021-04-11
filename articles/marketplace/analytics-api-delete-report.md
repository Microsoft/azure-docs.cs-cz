---
title: Odstranit rozhraní API sestav
description: Pomocí tohoto rozhraní API můžete odstranit všechny záznamy o spouštění sestav a sestav pro sestavy analytického komerčního tržiště.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 7c39f8bc0db44f1d8aa885969ca09d90b0dcd332
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583881"
---
# <a name="delete-report-api"></a>Odstranit rozhraní API sestav

Při spuštění toto rozhraní API odstraní všechny záznamy o spuštění sestavy a sestavy.

**Syntaxe žádosti**

| Metoda | Identifikátor URI žádosti |
| ------------ | ------------- |
| DELETE | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/{Report ID}` |
|||

**Hlavička žádosti**

| Hlavička | Typ | Description |
| ------------ | ------------- | ------------- |
| Autorizace | řetězec | Povinná hodnota. Přístupový token Azure AD ve formuláři `Bearer <token>` |
| Typ obsahu | řetězec | `Application/JSON` |
||||

**Parametr cesty**

Žádné

**Parametr dotazu**

| Název parametru | Vyžadováno | řetězec | Popis |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | Ano | řetězec | ID upravované sestavy |
|||||

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
      "RecurrenceCount": 0,
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

| Parametr | Popis |
| ------------ | ------------- |
| `ReportId` | Jedinečný identifikátor UUID odstraněné sestavy |
| `ReportName` | Název zadaný pro sestavu během vytváření |
| `Description` | Popis zadaný při vytváření sestavy |
| `QueryId` | ID dotazu předané v okamžiku vytvoření sestavy |
| `Query` | Text dotazu, který se spustí pro tuto sestavu |
| `User` | ID uživatele, které se použilo k vytvoření sestavy |
| `CreatedTime` | Čas, kdy byla sestava vytvořena. Formát času je RRRR-MM-ddTHH: mm: ssZ |
| `ModifiedTime` | Čas poslední změny sestavy Formát času je RRRR-MM-ddTHH: mm: ssZ |
| `StartTime` | Čas, kdy se spustí spuštění sestavy. Formát času je RRRR-MM-ddTHH: mm: ssZ |
| `ReportStatus` | Stav provádění sestavy Možné hodnoty jsou pozastavené, aktivní a neaktivní. |
| `RecurrenceInterval` | Interval opakování zadaný při vytváření sestavy |
| `RecurrenceCount` | Počet opakování poskytnutý během vytváření sestavy |
| `CallbackUrl` | Adresa URL zpětného volání poskytnutá v žádosti |
| `Format` | Formát souborů sestav |
|||
