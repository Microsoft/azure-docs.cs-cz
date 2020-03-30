---
title: Přejít na živo | Azure Marketplace
description: Go Live API iniciuje nabídku live výpis procesu.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: bf7bebf6e72e373811879a311d70255c29988ed6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288576"
---
<a name="go-live"></a>Přejít na živo
=======

Toto rozhraní API spustí proces odesílání aplikace do produkčního prostředí. Tato operace je obvykle dlouhotrvající. Toto volání používá seznam e-mailů s oznámením z operace [publikovat](./cloud-partner-portal-api-publish-offer.md) rozhraní API.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

<a name="uri-parameters"></a>Parametry identifikátoru URI
--------------

|  **Název**      |   **Popis**                                                           | **Datový typ** |
|  --------      |   ---------------                                                           | ------------- |
| id vydavatele    | Identifikátor vydavatele pro nabídku k načtení, například`contoso`       |  Řetězec       |
| offerId        | Identifikátor nabídky k načtení                                   |  Řetězec       |
| verze-api    | Nejnovější verze rozhraní API                                                   |  Datum         |
|  |  |  |


<a name="header"></a>Hlavička
------

|  **Název**       |     **Hodnotu**       |
|  ---------      |     ----------      |
| Typ obsahu    | `application/json`  |
| Autorizace   | `Bearer YOUR_TOKEN` |
|  |  |


<a name="body-example"></a>Příklad těla
------------

### <a name="response"></a>Odpověď

`Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Hlavička odpovědi

|  **Název**             |      **Hodnotu**                                                            |
|  --------             |      ----------                                                           |
| Umístění operace    |  Adresa URL dotazu k určení aktuálního stavu operace            |
|  |  |


### <a name="response-status-codes"></a>Stavové kódy odpovědi

| **kód** |  **Popis**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted`- Požadavek byl úspěšně přijat. Odpověď obsahuje umístění pro sledování stavu operace. |
|  400     | `Bad/Malformed request`- Další informace o chybě se nachází v těle odezvy. |
|  404     |  `Not found`- Zadaná entita neexistuje.                                       |
|  |  |
