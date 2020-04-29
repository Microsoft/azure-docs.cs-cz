---
title: Načíst rozhraní API nabídky | Azure Marketplace
description: Rozhraní API načte souhrnný seznam nabídek v oboru názvů vydavatele.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 0413bc71c113bf1eef9f761dbeed4c0d0afe735c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81255953"
---
<a name="retrieve-offers"></a>Načtení nabídek
===============

> [!NOTE]
> Rozhraní API pro portál partnerů cloudu jsou integrovaná do partnerského centra a budou fungovat i po migraci nabídek do partnerského centra. Integrace přináší malé změny. Projděte si změny uvedené v části [portál partnerů cloudu rozhraní API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) , abyste zajistili, že váš kód bude i nadále fungovat po migraci do partnerského centra.

Načte souhrnný seznam nabídek v oboru názvů vydavatele.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`

<a name="uri-parameters"></a>Parametry identifikátoru URI
--------------

| **Název**         |  **Popis**                         |  **Datový typ** |
| -------------    |  ------------------------------------    |  -----------   |
|  publisherId     | Identifikátor vydavatele, například`contoso` |   Řetězec    |
|  verze-api     | Nejnovější verze rozhraní API                    |    Datum        |
|  |  |


<a name="header"></a>Hlavička
------

|  **Název**        |         **Osa**       |
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
|  definice     | Obsahuje souhrnné zobrazení skutečné definice úlohy. Chcete-li získat podrobnou definici, použijte rozhraní API [nabídky načíst konkrétní](./cloud-partner-portal-api-retrieve-specific-offer.md) . |
|  changedTime    | Čas UTC, kdy se nabídka naposledy změnila                                                                              |
|  |  |


### <a name="response-status-codes"></a>Stavové kódy odpovědí

| **kód**  |  **Popis**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK`-Požadavek byl úspěšně zpracován a do klienta byly vráceny všechny nabídky pod vydavatelem.  |
|  400      | `Bad/Malformed request`– Tělo chybové odpovědi může obsahovat více informací.                                    |
|  403      | `Forbidden`– Klient nemá přístup k zadanému oboru názvů.                                          |
|  404      | `Not found`-Zadaná entita neexistuje.                                                                 |
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
|  Failed                      | Odeslání nabídky se nezdařilo.                         |
|  |  |
