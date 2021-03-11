---
title: Obnovit rozhraní API pro spouštění sestav
description: Pomocí tohoto rozhraní API můžete pokračovat v naplánovaném spuštění sestavy analytického obchodu s pozastaveným komerčním Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 4a11783b28352cb62c5a3c0d38e45dcdc47a8d86
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102583727"
---
# <a name="resume-report-executions-api"></a>Obnovit rozhraní API pro spouštění sestav

Toto rozhraní API při spuštění pokračuje v naplánovaném provádění pozastavené sestavy analýzy komerčních tržišť.

**Syntaxe žádosti**

| Metoda | Identifikátor URI žádosti |
| ------------ | ------------- |
| PUT | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/resume/{reportId}` |
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
| `ReportId` | Univerzálně jedinečný identifikátor (UUID) obnovené sestavy |
| `ReportName` | Název zadaný pro sestavu během vytváření |
| `Description` | Popis zadaný při vytváření sestavy |
| `QueryId` | ID dotazu předané v okamžiku vytvoření sestavy |
| `Query` | Text dotazu, který se spustí pro tuto sestavu |
| `User` | ID uživatele, které se používá k vytvoření sestavy |
| `CreatedTime` | Čas, kdy byla sestava vytvořena. Formát času je RRRR-MM-ddTHH: mm: ssZ |
| `ModifiedTime` | Čas poslední změny sestavy Formát času je RRRR-MM-ddTHH: mm: ssZ |
| `StartTime` | Čas, kdy se spustí spuštění sestavy Formát času je RRRR-MM-ddTHH: mm: ssZ |
| `ReportStatus` | Stav provádění sestavy Možné hodnoty jsou pozastavené, aktivní a neaktivní. |
| `RecurrenceInterval` | Interval opakování zadaný při vytváření sestavy |
| `RecurrenceCount` | Počet opakování poskytnutý během vytváření sestavy |
| `CallbackUrl` | Adresa URL zpětného volání poskytnutá v žádosti |
| `Format` | Formát souborů sestav |
|||
