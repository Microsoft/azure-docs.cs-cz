---
title: Načíst rozhraní API nabídky | Azure Marketplace
description: Rozhraní API načte souhrnný seznam nabídek v oboru názvů vydavatele.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 5c94c03a63936be2b086085a1e52064dedf214b0
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819636"
---
<a name="retrieve-offers"></a>Načtení nabídek
===============

Načte souhrnný seznam nabídek v oboru názvů vydavatele.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`


<a name="uri-parameters"></a>Parametry identifikátoru URI
--------------

| **Název**         |  **Popis**                         |  **Datový typ** |
| -------------    |  ------------------------------------    |  -----------   |
|  publisherId     | Identifikátor vydavatele, například `contoso` |   Řetězec    |
|  verze API-Version     | Nejnovější verze rozhraní API                    |    Datum        |
|  |  |


<a name="header"></a>Hlavička
------

|  **Název**        |         **Hodnota**       |
|  --------------- |       ----------------  |
|  Typ obsahu    | `application/json`      |
|  Autorizace   | `Bearer YOUR_TOKEN`     |
|  |  |


<a name="body-example"></a>Příklad textu
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
|  offerTypeId    | Identifikuje typ nabídky.                                                                                           |
|  publisherId    | Identifikátor, který jednoznačně identifikuje vydavatele                                                                      |
|  status         | Stav nabídky Seznam možných hodnot najdete v tématu o [stavu nabídky](#offer-status) níže.                         |
|  id             | Identifikátor GUID, který jedinečně identifikuje nabídku v oboru názvů vydavatele.                                                    |
|  version        | Aktuální verze nabídky Vlastnost Version nemůže změnit klient. Po každém publikování se zvýší. |
|  Definition     | Obsahuje souhrnné zobrazení skutečné definice úlohy. Chcete-li získat podrobnou definici, použijte rozhraní API [nabídky načíst konkrétní](./cloud-partner-portal-api-retrieve-specific-offer.md) . |
|  changedTime    | Čas UTC, kdy se nabídka naposledy změnila                                                                              |
|  |  |


### <a name="response-status-codes"></a>Stavové kódy odpovědí

| **Znakovou**  |  **Popis**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK` – požadavek byl úspěšně zpracován a do klienta byly vráceny všechny nabídky pod vydavatelem.  |
|  400      | `Bad/Malformed request` – tělo chybové odpovědi může obsahovat další informace.                                    |
|  403      | `Forbidden` – klient nemá přístup k zadanému oboru názvů.                                          |
|  404      | `Not found` – zadaná entita neexistuje.                                                                 |
|  |  |


### <a name="offer-status"></a>Stav nabídky

|  **Název**                    | **Popis**                                  |
|  ------------------------    | -----------------------------------------------  |
|  NeverPublished              | Nabídka nebyla nikdy publikována.                  |
|  NotStarted                  | Nabídka je nová, ale není spuštěná.                 |
|  WaitingForPublisherReview   | Nabídka čeká na schválení vydavatele.         |
|  Spuštěno                     | Zpracovává se odeslání nabídky.             |
|  Úspěch                   | Bylo dokončeno zpracování příspěvku nabídky.       |
|  Zrušeno                    | Odeslání nabídky se zrušilo.                   |
|  Selhalo                      | Odeslání nabídky se nezdařilo.                         |
|  |  |
