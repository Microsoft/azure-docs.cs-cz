---
title: Vyzkoušet rozhraní API dotazů sestav
description: Pomocí tohoto rozhraní API spustíte dotaz na sestavu pro sestavy analytického komerčního tržiště.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 0db212be06182128bbd8a3bf694a2f893ce82eae
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102583726"
---
# <a name="try-report-queries-api"></a>Vyzkoušet rozhraní API dotazů sestav

Toto rozhraní API spustí příkaz dotazu sestavy. Rozhraní API vrátí jenom 10 záznamů, které jste jako partneři mohli použít k ověření, jestli jsou data podle očekávání.

> [!IMPORTANT]
> Toto rozhraní API má časový limit spuštění dotazu 100 sekund. Pokud si všimnete, že rozhraní API trvá déle než 100 sekund, je velmi vhodné, aby dotaz byl syntakticky správný nebo jinak byl obdržen chybový kód jiný než 200. Pokud je syntaxe dotazu správná, vytvoří se skutečná generace sestavy.

**Syntaxe žádosti**

| **Metoda** | **Identifikátor URI žádosti** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/testQueryResult?exportQuery={query text}` |
|||

**Hlavička žádosti**

| **Hlavička** | **Typ** | **Popis** |
| --- | --- | --- |
| Autorizace | řetězec | Povinná hodnota. Přístupový token Azure Active Directory (Azure AD) ve formuláři `Bearer <token>` |
| Typ obsahu | řetězec | `Application/JSON` |
|||

**QueryParameter**

| **Název parametru** | **Typ** | **Popis** |
| --- | --- | --- |
| `exportQuery` | řetězec | Řetězec dotazu sestavy, který musí být proveden |
| `queryId` | řetězec | Platné existující ID dotazu |
|||

**Parametr** cesty  

Žádné

**Datová část požadavku**

Žádné

**Glosář**

Žádné

**Response** (Odpověď)

Datová část odpovědi je strukturována takto:

Kód odpovědi: 200, 400, 401, 403, 404, 500

Datová část odpovědi: prvních 10 řádků spuštění dotazu
