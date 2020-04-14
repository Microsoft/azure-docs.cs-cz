---
title: Načíst nabídky API | Azure Marketplace
description: Rozhraní API načte souhrnný seznam nabídek v oboru názvů vydavatele.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 0413bc71c113bf1eef9f761dbeed4c0d0afe735c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255953"
---
<a name="retrieve-offers"></a>Načtení nabídek
===============

> [!NOTE]
> Api portálu pro partnery cloudu jsou integrovaná s Partnerským centrem a budou fungovat i po migraci nabídek do Centra partnerů. Integrace přináší malé změny. Zkontrolujte změny uvedené v [referenčním rozhraní API portálu cloudových partnerů a](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) ujistěte se, že váš kód bude fungovat i po migraci do Centra partnerů.

Načte souhrnný seznam nabídek v oboru názvů vydavatele.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`

<a name="uri-parameters"></a>Parametry identifikátoru URI
--------------

| **Název**         |  **Popis**                         |  **Datový typ** |
| -------------    |  ------------------------------------    |  -----------   |
|  id vydavatele     | Identifikátor vydavatele, například`contoso` |   Řetězec    |
|  verze-api     | Nejnovější verze rozhraní API                    |    Datum        |
|  |  |


<a name="header"></a>Hlavička
------

|  **Název**        |         **Hodnotu**       |
|  --------------- |       ----------------  |
|  Typ obsahu    | `application/json`      |
|  Autorizace   | `Bearer YOUR_TOKEN`     |
|  |  |


<a name="body-example"></a>Příklad těla
------------

### <a name="response"></a>Odpověď

``` json
  200 OK 
  [ 
      {  
          "offerTypeId": "microsoft-azure-virtualmachines",
          "publisherId": "contoso",
          "status": "published",
          "id": "059afc24-07de-4126-b004-4e42a51816fe",
          "version": 1,
          "definition": {
              "displayText": "Contoso Virtual Machine"
          },
          "changedTime":"2017-05-23T23:33:47.8802283Z"
      }
  ]
```

### <a name="response-body-properties"></a>Vlastnosti těla odezvy

|  **Název**       |       **Popis**                                                                                                  |
|  -------------  |      --------------------------------------------------------------------------------------------------------------    |
|  offerTypeId    | Identifikuje typ nabídky                                                                                           |
|  id vydavatele    | Identifikátor, který jednoznačně identifikuje vydavatele                                                                      |
|  status         | Stav nabídky. Seznam možných hodnot naleznete níže v [tématu Stav nabídky.](#offer-status)                         |
|  id             | Identifikátor GUID, který jednoznačně identifikuje nabídku v oboru názvů vydavatele.                                                    |
|  version        | Aktuální verze nabídky. Vlastnost version nemůže být klientem změněna. Po každém publikování se to zintáží. |
|  definice     | Obsahuje souhrnné zobrazení skutečné definice pracovního vytížení. Chcete-li získat podrobnou definici, použijte [rozhraní API pro konkrétní nabídku.](./cloud-partner-portal-api-retrieve-specific-offer.md) |
|  changedTime    | Čas UTC, kdy byla nabídka naposledy změněna                                                                              |
|  |  |


### <a name="response-status-codes"></a>Stavové kódy odpovědi

| **kód**  |  **Popis**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK`- Požadavek byl úspěšně zpracován a všechny nabídky pod vydavatelem byly vráceny klientovi.  |
|  400      | `Bad/Malformed request`- Tělo odpovědi na chybu může obsahovat více informací.                                    |
|  403      | `Forbidden`- Klient nemá přístup k zadanému oboru názvů.                                          |
|  404      | `Not found`- Zadaná entita neexistuje.                                                                 |
|  |  |


### <a name="offer-status"></a>Stav nabídky

|  **Název**                    | **Popis**                                  |
|  ------------------------    | -----------------------------------------------  |
|  Nikdy publikováno              | Nabídka nebyla nikdy zveřejněna.                  |
|  Notstarted                  | Nabídka je nová, ale není spuštěna.                 |
|  WaitingForPublisherReview   | Nabídka čeká na schválení vydavatele.         |
|  Spuštěno                     | Probíhá zpracování odesílání nabídky.             |
|  Úspěch                   | Odeslání nabídky bylo dokončeno zpracování.       |
|  Zrušeno                    | Odeslání nabídky bylo zrušeno.                   |
|  Failed                      | Odeslání nabídky se nezdařilo.                         |
|  |  |
