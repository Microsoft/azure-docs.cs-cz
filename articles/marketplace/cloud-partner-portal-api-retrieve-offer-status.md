---
title: Načíst stav nabídky – Azure Marketplace
description: Rozhraní API pro načtení aktuálního stavu nabídky
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 336f23f83c33bcee1887d0e41710e686b794a663
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87272007"
---
# <a name="retrieve-offer-status"></a>Načtení stavu nabídky

> [!NOTE]
> Rozhraní API pro portál partnerů cloudu jsou integrovaná s a budou pokračovat v práci v partnerském centru. Přechod přináší malé změny. Projděte si změny uvedené v části [portál partnerů cloudu rozhraní API](./cloud-partner-portal-api-overview.md) , abyste zajistili, že kód pokračuje v práci po přechodu do partnerského centra. Rozhraní API CPP by se mělo používat jenom pro existující produkty, které už jsou integrované před přechodem do partnerského centra; nové produkty by měly používat rozhraní API pro odesílání v partnerském centru.

Načte aktuální stav nabídky.

  `GET  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/status?api-version=2017-10-31`

## <a name="uri-parameters"></a>Parametry identifikátoru URI

|  **Název**       |   **Popis**                            |  **Datový typ** |
|  -------------  |  ------------------------------------------  |  ------------  |
|  publisherId    | Identifikátor vydavatele, například `Contoso`  |     Řetězec     |
|  Hodnotami OfferId        | Identifikátor GUID, který jedinečně identifikuje nabídku      |     Řetězec     |
|  verze-api    | Nejnovější verze rozhraní API                        |     Date (Datum)       |
|  |  |

## <a name="header"></a>Hlavička


|  Name           |  Hodnota               |
|  -------------  | -------------------  |
|  Typ obsahu   |  `application/json`  |
|  Autorizace  | `Bearer YOUR_TOKEN`  |
|  |  |

## <a name="body-example"></a>Příklad textu

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
|  notificationEmails   | Zastaralé pro nabídky migrované do partnerského centra. E-maily s oznámením pro migrované nabídky se odešlou e-mailem uvedenému na kontaktní údaje prodejce v nastavení účtu.<br><br>V případě nemigrovaných nabídek se seznam e-mailových adres oddělených čárkami upozorní na průběh operace.        |
|  |  |

### <a name="response-status-codes"></a>Stavové kódy odpovědí

| **Kód** |   **Popis**                                                                                 |
| -------  |   ----------------------------------------------------------------------------------------------- |
|  200     |  `OK` -Požadavek byl úspěšně zpracován a byl vrácen aktuální stav nabídky. |
|  400     | `Bad/Malformed request` – Tělo chybové odpovědi může obsahovat více informací.                 |
|  404     | `Not found` -Zadaná entita neexistuje.                                                |
|  |  |

### <a name="offer-status"></a>Stav nabídky

|  **Název**                    |    **Popis**                                       |
|  --------------------------  |  ------------------------------------------------------  |
|  NeverPublished              | Nabídka nebyla nikdy publikována.                          |
|  NotStarted                  | Nabídka je nová a není spuštěná.                            |
|  WaitingForPublisherReview   | Nabídka čeká na schválení vydavatele.                 |
|  Spuštěno                     | Zpracovává se odeslání nabídky.                     |
|  Úspěšný                   | Bylo dokončeno zpracování příspěvku nabídky.               |
|  Zrušeno                    | Odeslání nabídky se zrušilo.                           |
|  Neúspěšný                      | Odeslání nabídky se nezdařilo.                                 |
|  |  |

### <a name="step-status"></a>Stav kroku

|  **Název**                    |    **Popis**                           |
|  -------------------------   |  ------------------------------------------  |
|  NotStarted                  | Krok nebyl spuštěn.                        |
|  InProgress                  | Krok je spuštěn.                             |
|  WaitingForPublisherReview   | Krok čeká na schválení vydavatele.      |
|  WaitingForApproval          | Krok čeká na schválení procesu.        |
|  Blokované                     | Krok je blokován.                             |
|  Zamítnuto                    | Krok je odmítnutý.                            |
|  Dokončit                    | Krok je dokončený.                            |
|  Zrušeno                    | Krok byl zrušen.                           |
|  |  |
