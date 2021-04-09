---
title: Načíst rozhraní API nabídek – Azure Marketplace
description: Rozhraní API pro načtení souhrnného seznamu nabídek v rámci oboru názvů vydavatele.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 39d07751c708d5555799ecbb3b3bc66d3f44f43a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87271956"
---
# <a name="retrieve-offers"></a>Načtení nabídek

> [!NOTE]
> Rozhraní API pro portál partnerů cloudu jsou integrovaná s a budou pokračovat v práci v partnerském centru. Přechod přináší malé změny. Projděte si změny uvedené v části [portál partnerů cloudu rozhraní API](./cloud-partner-portal-api-overview.md) , abyste zajistili, že kód pokračuje v práci po přechodu do partnerského centra. Rozhraní API CPP by se mělo používat jenom pro existující produkty, které už jsou integrované před přechodem do partnerského centra; nové produkty by měly používat rozhraní API pro odesílání v partnerském centru.

Načte souhrnný seznam nabídek v oboru názvů vydavatele.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`

## <a name="uri-parameters"></a>Parametry identifikátoru URI

| **Název**         |  **Popis**                         |  **Datový typ** |
| -------------    |  ------------------------------------    |  -----------   |
|  publisherId     | Identifikátor vydavatele, například `contoso` |   Řetězec    |
|  verze-api     | Nejnovější verze rozhraní API                    |    Date (Datum)        |
|  |  |

## <a name="header"></a>Hlavička

|  **Název**        |         **Hodnota**       |
|  --------------- |       ----------------  |
|  Typ obsahu    | `application/json`      |
|  Autorizace   | `Bearer YOUR_TOKEN`     |
|  |  |

## <a name="body-example"></a>Příklad textu

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
|  verze        | Aktuální verze nabídky Vlastnost Version nemůže změnit klient. Po každém publikování se zvýší. |
|  definice     | Obsahuje souhrnné zobrazení skutečné definice úlohy. Chcete-li získat podrobnou definici, použijte rozhraní API [nabídky načíst konkrétní](./cloud-partner-portal-api-retrieve-specific-offer.md) . |
|  changedTime    | Čas UTC, kdy se nabídka naposledy změnila                                                                              |
|  |  |

### <a name="response-status-codes"></a>Stavové kódy odpovědí

| **Kód**  |  **Popis**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK` -Požadavek byl úspěšně zpracován a do klienta byly vráceny všechny nabídky pod vydavatelem.  |
|  400      | `Bad/Malformed request` – Tělo chybové odpovědi může obsahovat více informací.                                    |
|  403      | `Forbidden` – Klient nemá přístup k zadanému oboru názvů.                                          |
|  404      | `Not found` -Zadaná entita neexistuje.                                                                 |
|  |  |

### <a name="offer-status"></a>Stav nabídky

|  **Název**                    | **Popis**                                  |
|  ------------------------    | -----------------------------------------------  |
|  NeverPublished              | Nabídka nebyla nikdy publikována.                  |
|  NotStarted                  | Nabídka je nová, ale není spuštěná.                 |
|  WaitingForPublisherReview   | Nabídka čeká na schválení vydavatele.         |
|  Spuštěno                     | Zpracovává se odeslání nabídky.             |
|  Úspěšný                   | Bylo dokončeno zpracování příspěvku nabídky.       |
|  Zrušeno                    | Odeslání nabídky se zrušilo.                   |
|  Neúspěšný                      | Odeslání nabídky se nezdařilo.                         |
|  |  |
