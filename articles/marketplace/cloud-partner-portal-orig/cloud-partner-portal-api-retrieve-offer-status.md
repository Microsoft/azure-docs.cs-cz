---
title: Načíst stav nabídky | Azure Marketplace
description: Rozhraní API načte aktuální stav nabídky.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 5ce546d79497f462f6c262de738036d7e3a30226
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819671"
---
<a name="retrieve-offer-status"></a>Načtení stavu nabídky 
=====================

Načte aktuální stav nabídky.

  `GET  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/status?api-version=2017-10-31`

<a name="uri-parameters"></a>Parametry identifikátoru URI
--------------

|  **Název**       |   **Popis**                            |  **Datový typ** |
|  -------------  |  ------------------------------------------  |  ------------  |
|  publisherId    | Identifikátor vydavatele, například `Contoso`  |     Řetězec     |
|  Hodnotami OfferId        | Identifikátor GUID, který jedinečně identifikuje nabídku      |     Řetězec     |
|  verze API-Version    | Nejnovější verze rozhraní API                        |     Datum       |
|  |  |


<a name="header"></a>Hlavička
------

|  Name (Název)           |  Hodnota               |
|  -------------  | -------------------  |
|  Typ obsahu   |  `application/json`  |
|  Autorizace  | `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Příklad textu
------------

### <a name="response"></a>Odpověď

``` json
  {
      "status": "succeeded",
      "messages": [],
      "steps": [
      {
          "estimatedTimeFrame": "< 15 min",
          "id": "displaydummycertify",
          "stepName": "Validate Pre-Requisites",
          "description": "Offer settings provided are validated.",
          "status": "complete",
          "messages": [
              {
                  "messageHtml": "Step completed.",
                  "level": "information",
                  "timestamp": "2018-03-16T17:50:45.7215661Z"
              }
          ],       
          "progressPercentage": 100
      },
      {
          "estimatedTimeFrame": "~2-3 days",
          "id": "displaycertify",
          "stepName": "Certification",
          "description": "Your offer is analyzed by our certification systems for issues.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 day",
          "id": "displayprovision",
          "stepName": "Provisioning",
          "description": "Your virtual machine is being replicated in our production systems.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 hour",
          "id": "displaypackage",
          "stepName": "Packaging and Lead Generation Registration",
          "description": "Your virtual machine is being packaged for customers. Additionally, lead systems are being configured and set up.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 hour",
          "id": "publisher-signoff",
          "stepName": "Publisher signoff",
          "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "~2-5 days",
          "id": "live",
          "stepName": "Live",
          "description": "Offer is publicly visible and is available for purchase.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      }
      ],
      "previewLinks": [],
      liveLinks": [],
      "notificationEmails": "jdoe@contoso.com"
  } 
```


### <a name="response-body-properties"></a>Vlastnosti textu odpovědi

|  **Název**             |    **Popis**                                                                             |
| --------------------  |   -------------------------------------------------------------------------------------------- |
|  status               | Stav nabídky Seznam možných hodnot najdete v tématu o [stavu nabídky](#offer-status) níže. |
|  cloud-zařízení             | Pole zpráv přidružených k této nabídce                                                    |
|  kroky                | Pole kroků, pomocí kterých tato nabídka projde během publikování nabídky                      |
|  estimatedTimeFrame   | Odhad doby provedení tohoto kroku v popisném formátu                       |
|  id                   | Identifikátor kroku                                                                         |
|  Step             | Název kroku                                                                               |
|  description          | Popis kroku                                                                        |
|  status               | Stav kroku. Seznam možných hodnot najdete v tématu [stav kroku](#step-status) níže.    |
|  cloud-zařízení             | Pole zpráv souvisejících s krokem                                                          |
|  processPercentage    | Procentuální podíl dokončení kroku                                                              |
|  previewLinks         | *Není aktuálně implementováno*                                                                    |
|  liveLinks            | *Není aktuálně implementováno*                                                                    |
|  notificationEmails   | Čárkami oddělený seznam e-mailových adres, které se mají upozornit na průběh operace        |
|  |  |


### <a name="response-status-codes"></a>Stavové kódy odpovědí

| **Znakovou** |   **Popis**                                                                                 |
| -------  |   ----------------------------------------------------------------------------------------------- |
|  200     |  `OK` – požadavek byl úspěšně zpracován a byl vrácen aktuální stav nabídky. |
|  400     | `Bad/Malformed request` – tělo chybové odpovědi může obsahovat další informace.                 |
|  404     | `Not found` – zadaná entita neexistuje.                                                |
|  |  |


### <a name="offer-status"></a>Stav nabídky

|  **Název**                    |    **Popis**                                       |
|  --------------------------  |  ------------------------------------------------------  |
|  NeverPublished              | Nabídka nebyla nikdy publikována.                          |
|  NotStarted                  | Nabídka je nová a není spuštěná.                            |
|  WaitingForPublisherReview   | Nabídka čeká na schválení vydavatele.                 |
|  Spuštěno                     | Zpracovává se odeslání nabídky.                     |
|  Úspěch                   | Bylo dokončeno zpracování příspěvku nabídky.               |
|  Zrušeno                    | Odeslání nabídky se zrušilo.                           |
|  Selhalo                      | Odeslání nabídky se nezdařilo.                                 |
|  |  |


### <a name="step-status"></a>Stav kroku

|  **Název**                    |    **Popis**                           |
|  -------------------------   |  ------------------------------------------  |
|  NotStarted                  | Krok nebyl spuštěn.                        |
|  InProgress                  | Krok je spuštěn.                             |
|  WaitingForPublisherReview   | Krok čeká na schválení vydavatele.      |
|  WaitingForApproval          | Krok čeká na schválení procesu.        |
|  Blokované                     | Krok je blokován.                             |
|  Odmítnutí                    | Krok je odmítnutý.                            |
|  Dokončit                    | Krok je dokončený.                            |
|  Zrušeno                    | Krok byl zrušen.                           |
|  |  |
