---
title: Načtení nabízí rozhraní API | Dokumentace Microsoftu
description: Rozhraní API načte souhrnný seznam nabídky v rámci oboru názvů vydavatele.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 3429917fcee520ae932253f1fdfead4ffb6535e6
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809184"
---
<a name="retrieve-offers"></a>Nabízí načtení
===============

Načte souhrnný seznam nabídky v rámci oboru názvů vydavatele.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`


<a name="uri-parameters"></a>Parametry identifikátoru URI
--------------

| ** Název **        |  **Popis**                         |  **Datový typ** |
| -------------    |  ------------------------------------    |  -----------   |
|  publisherId     | Identifikátor vydavatel, například `contoso` |   Řetězec    |
|  verze API-version     | Nejnovější verzi rozhraní API                    |    Datum        |
|  |  |


<a name="header"></a>Záhlaví
------

|  **Název**        |         **Hodnota**       |
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

### <a name="response-body-properties"></a>Vlastnosti textu odpovědi

|  **Název**       |       **Popis**                                                                                                  |
|  -------------  |      --------------------------------------------------------------------------------------------------------------    |
|  offerTypeId    | Určuje typ nabídky                                                                                           |
|  publisherId    | Identifikátor, který jednoznačně identifikuje vydavatele                                                                      |
|  status         | Stav nabídky. Seznam možných hodnot najdete v tématu [stav nabídky](#offer-status) níže.                         |
|  id             | Identifikátor GUID, který jednoznačně identifikuje nabídky v oboru názvů vydavatele.                                                    |
|  version        | Aktuální verze nabídky. Klient nemůže upravit vlastnost version. Se zvýší po jednotlivých publikování. |
|  Definice     | Obsahuje souhrnné zobrazení skutečné definice úlohy. Pokud chcete získat podrobné definice, použijte [načtení konkrétní nabídky] (. / cloud-partner-portal-api-retrieve-specific-offer.md) rozhraní API. |
|  changedTime    | Čas UTC poslední úpravy nabídky                                                                              |
|  |  |


### <a name="response-status-codes"></a>Stavové kódy odezvy

| **kód**  |  **Popis**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK` -Žádost byla úspěšně zpracována a všechny nabídky v části vydavatele vrácených do klienta.  |
|  400      | `Bad/Malformed request` Text – Chyba odpovědi může obsahovat další informace.                                    |
|  403      | `Forbidden` -Klienta nemá přístup k určený obor názvů.                                          |
|  404      | `Not found` -Zadaná entita neexistuje.                                                                 |
|  |  |


### <a name="offer-status"></a>Stav nabídky

|  **Název**                    | **Popis**                                  |
|  ------------------------    | -----------------------------------------------  |
|  NeverPublished              | Nabídka se nikdy nepublikoval.                  |
|  NotStarted                  | Nabídka je nový, ale není spuštěna.                 |
|  WaitingForPublisherReview   | Nabídka je čekání na schválení vydavatele.         |
|  Spuštěno                     | Nabídka odeslání se zpracovává.             |
|  Úspěch                   | Nabídka odeslání dokončil zpracování.       |
|  Zrušeno                    | Odeslání nabídka byla zrušena.                   |
|  Selhalo                      | Nabídka odeslání se nezdařilo.                         |
|  |  |
