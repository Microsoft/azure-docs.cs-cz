---
title: Načíst stav nabídky | Dokumentace Microsoftu
description: Rozhraní API získá aktuální stav nabídky.
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
ms.openlocfilehash: 9233a5919ad86adcbb7947cd095945654ed015a7
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809499"
---
<a name="retrieve-offer-status"></a>Načíst stav nabídky 
=====================

Načte aktuální stav nabídky.

  `GET  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/status?api-version=2017-10-31`

<a name="uri-parameters"></a>Parametry identifikátoru URI
--------------

|  **Název**       |   **Popis**                            |  **Datový typ** |
|  -------------  |  ------------------------------------------  |  ------------  |
|  publisherId    | Identifikátor vydavatel, například `Contoso`  |     Řetězec     |
|  offerId        | Identifikátor GUID, který jednoznačně identifikuje nabídky      |     Řetězec     |
|  verze API-version    | Nejnovější verzi rozhraní API                        |     Datum       |
|  |  |


<a name="header"></a>Záhlaví
------

|  Název           |  Hodnota               |
|  -------------  | -------------------  |
|  Typ obsahu   |  `application/json`  |
|  Autorizace  | `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Příklad těla
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
|  status               | Stav nabídky. Seznam možných hodnot najdete v tématu [stav nabídky](#offer-status) níže. |
|  cloud-zařízení             | Pole zpráv přidružených k nabídce                                                    |
|  kroky                | Pole s kroky uvedenými nabídky prochází během publikování nabídky                      |
|  estimatedTimeFrame   | Odhadovaný čas potřebný k dokončení tohoto kroku, popisný formát                       |
|  id                   | Identifikátor kroku                                                                         |
|  stepName             | Název kroku                                                                               |
|  description          | Popis kroku                                                                        |
|  status               | Stav kroku. Seznam možných hodnot najdete v tématu [krok stav](#step-status) níže.    |
|  cloud-zařízení             | Pole zprávy týkající se na krok                                                          |
|  processPercentage    | Procento dokončení kroku                                                              |
|  previewLinks         | *Není aktuálně implementováno*                                                                    |
|  liveLinks            | *Není aktuálně implementováno*                                                                    |
|  notificationEmails   | Čárkami oddělený seznam e-mailové adresy, která vás upozorní na průběh operace        |
|  |  |


### <a name="response-status-codes"></a>Stavové kódy odezvy

| **kód** |   **Popis**                                                                                 |
| -------  |   ----------------------------------------------------------------------------------------------- |
|  200     |  `OK` -Žádost byla úspěšně zpracována a aktuální stav nabídka byla vrácena. |
|  400     | `Bad/Malformed request` Text – Chyba odpovědi může obsahovat další informace.                 |
|  404     | `Not found` -Zadaná entita neexistuje.                                                |
|  |  |


### <a name="offer-status"></a>Stav nabídky

|  **Název**                    |    **Popis**                                       |
|  --------------------------  |  ------------------------------------------------------  |
|  NeverPublished              | Nabídka se nikdy nepublikoval.                          |
|  NotStarted                  | Nabídka je nová a není spuštěné.                            |
|  WaitingForPublisherReview   | Nabídka je čekání na schválení vydavatele.                 |
|  Spuštěno                     | Nabídka odeslání se zpracovává.                     |
|  Úspěch                   | Nabídka odeslání dokončil zpracování.               |
|  Zrušeno                    | Odeslání nabídka byla zrušena.                           |
|  Selhalo                      | Nabídka odeslání se nezdařilo.                                 |
|  |  |


### <a name="step-status"></a>Stav kroku

|  **Název**                    |    **Popis**                           |
|  -------------------------   |  ------------------------------------------  |
|  NotStarted                  | Krok nebyl spuštěn.                        |
|  Probíhá zpracování.                  | Krok běží.                             |
|  WaitingForPublisherReview   | Krok je čekání na schválení vydavatele.      |
|  WaitingForApproval          | Krok je čekání na schválení procesu.        |
|  Blokováno                     | Krok blokovaný.                             |
|  Zamítnuto                    | Krok byl odmítnut.                            |
|  Dokončit                    | Krok je dokončen.                            |
|  Zrušeno                    | Krok byl zrušen.                           |
|  |  |
