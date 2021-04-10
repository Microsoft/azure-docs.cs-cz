---
title: Pozastavit rozhraní API pro spouštění sestav
description: Pomocí tohoto rozhraní API můžete pozastavit plánované provádění sestavy analýzy komerčního tržiště.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 39b535278fef42818f572631cfa1cb1f923930a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583755"
---
# <a name="pause-report-executions-api"></a>Pozastavit rozhraní API pro spouštění sestav

Toto rozhraní API při spuštění pozastaví naplánované provádění sestav.

**Syntaxe žádosti**

| Metoda | Identifikátor URI žádosti |
| ------------ | ------------- |
| PUT | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/pause/{Report ID}` |
|||

**Hlavička žádosti**

| Hlavička | Typ | Description |
| ------------ | ------------- | ------------- |
| Autorizace | řetězec | Povinná hodnota. Přístupový token Azure Active Directory (Azure AD) ve formuláři `Bearer <token>` |
| Typ obsahu | řetězec | `Application/JSON` |
||||

**Parametr cesty**

Žádné

**Parametr dotazu**

| Název parametru | Požaduje se | Typ | Popis |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | Ano | řetězec | ID upravované sestavy |
|||||

**Glosář**

Žádné

**Response** (Odpověď)

Datová část odpovědi je strukturována takto ve formátu JSON:

Kód odpovědi: 200, 400, 401, 403, 404, 500 – datová část odezvy:

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
| `ReportId` | Univerzálně jedinečný identifikátor (UUID) odstraněné sestavy |
| `ReportName` | Název zadaný pro sestavu během vytváření |
| `Description` | Popis zadaný při vytváření sestavy |
| `QueryId` | ID dotazu předané v okamžiku vytvoření sestavy |
| `Query` | Text dotazu, který se spustí pro tuto sestavu |
| `User` | ID uživatele, které se používá k vytvoření sestavy |
| `CreatedTime` | Čas, kdy byla sestava vytvořena. Formát času je RRRR-MM-ddTHH: mm: ssZ |
| `ModifiedTime` | Čas poslední změny sestavy Formát času je RRRR-MM-ddTHH: mm: ssZ |
| `StartTime` | Čas, kdy se spustí spuštění sestavy. Formát času je RRRR-MM-ddTHH: mm: ssZ |
| `ReportStatus` | Stav provádění sestavy Možné hodnoty jsou pozastavené, aktivní a neaktivní. |
| `RecurrenceInterval` | Interval opakování zadaný při vytváření sestavy |
| `RecurrenceCount` | Počet opakování poskytnutý během vytváření sestavy |
| `CallbackUrl` | Adresa URL zpětného volání poskytnutá v žádosti |
| `Format` | Formát souborů sestav |
|||
