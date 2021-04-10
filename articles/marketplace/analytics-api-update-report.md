---
title: Rozhraní API pro aktualizaci sestav
description: Pomocí tohoto rozhraní API můžete nahlásit parametr pro sestavy analytického komerčního tržiště.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 38680eb291417ded4c2f93539e8d1ae091b1d560
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583725"
---
# <a name="update-report-api"></a>Rozhraní API pro aktualizaci sestav

Toto rozhraní API vám pomůže upravit parametr sestavy.

**Syntaxe žádosti**

| Metoda | Identifikátor URI žádosti |
| ------------ | ------------- |
| PUT | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/{Report ID}` |
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

**Datová část požadavku**

```json
{
  "ReportName": "string",
  "Description": "string",
  "StartTime": "string",
  "RecurrenceInterval": 0,
  "RecurrenceCount": 0,
  "Format": "string",
  "CallbackUrl": "string"
}
```

**Glosář**

Tato tabulka uvádí definice klíčů prvků v datové části požadavku.

| Parametr | Povinné | Popis | Povolené hodnoty |
| ------------ | ------------- | ------------- | ------------- |
| `ReportName` | Yes | Název, který se má přiřadit k sestavě | řetězec |
| `Description` | No | Popis vytvořené sestavy | řetězec |
| `StartTime` | Yes | Časové razítko, po kterém bude zahájeno generování sestavy | řetězec |
| `RecurrenceInterval` | No | Frekvence, s jakou se má sestava generovat v hodinách Minimální hodnota je 4. | integer |
| `RecurrenceCount` | No | Počet sestav, které mají být vygenerovány. Výchozí hodnota je neomezená. | integer |
| `Format` | Yes | Formát souboru exportovaného souboru Výchozí hodnota je CSV. | CSV/TSV |
| `CallbackUrl` | Yes | Adresa URL zpětného volání https, která se má volat při generování sestavy | řetězec |
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
| `ReportId` | Univerzálně jedinečný identifikátor (UUID) sestavy, která se aktualizuje |
| `ReportName` | Název zadaný pro sestavu v datové části požadavku |
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
