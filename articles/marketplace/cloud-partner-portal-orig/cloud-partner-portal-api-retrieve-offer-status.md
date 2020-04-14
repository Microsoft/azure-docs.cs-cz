---
title: Načíst stav nabídky | Azure Marketplace
description: ROZHRANÍ API načte aktuální stav nabídky.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 9cf6ca27101a08ff58f32dcd31413256762490a2
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255906"
---
# <a name="retrieve-offer-status"></a>Načtení stavu nabídky

> [!NOTE]
> Api portálu pro partnery cloudu jsou integrovaná s Partnerským centrem a budou fungovat i po migraci nabídek do Centra partnerů. Integrace přináší malé změny. Zkontrolujte změny uvedené v [referenčním rozhraní API portálu cloudových partnerů a](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) ujistěte se, že váš kód bude fungovat i po migraci do Centra partnerů.

Načte aktuální stav nabídky.

  `GET  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/status?api-version=2017-10-31`

## <a name="uri-parameters"></a>Parametry identifikátoru URI

|  **Název**       |   **Popis**                            |  **Datový typ** |
|  -------------  |  ------------------------------------------  |  ------------  |
|  id vydavatele    | Identifikátor vydavatele, například`Contoso`  |     Řetězec     |
|  offerId        | IDENTIFIKÁTOR GUID, který jednoznačně identifikuje nabídku      |     Řetězec     |
|  verze-api    | Nejnovější verze rozhraní API                        |     Datum       |
|  |  |


## <a name="header"></a>Hlavička


|  Name (Název)           |  Hodnota               |
|  -------------  | -------------------  |
|  Typ obsahu   |  `application/json`  |
|  Autorizace  | `Bearer YOUR_TOKEN`  |
|  |  |

## <a name="body-example"></a>Příklad těla


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


### <a name="response-body-properties"></a>Vlastnosti těla odezvy

|  **Název**             |    **Popis**                                                                             |
| --------------------  |   -------------------------------------------------------------------------------------------- |
|  status               | Stav nabídky. Seznam možných hodnot naleznete níže v [tématu Stav nabídky.](#offer-status) |
|  cloud-zařízení             | Pole zpráv přidružených k nabídce                                                    |
|  kroky                | Pole kroků, které nabídka prochází během publikování nabídky                      |
|  odhadTimeFrame   | Odhad času, který by trvalo dokončení tohoto kroku, v přátelském formátu                       |
|  id                   | Identifikátor kroku                                                                         |
|  název kroku             | Název kroku                                                                               |
|  description          | Popis kroku                                                                        |
|  status               | Stav kroku. Seznam možných hodnot naleznete níže v [tématu Stav kroku.](#step-status)    |
|  cloud-zařízení             | Pole zpráv souvisejících s krokem                                                          |
|  processProcento    | Procentuální dokončení kroku                                                              |
|  náhledOdkazy         | *Není aktuálně implementováno*                                                                    |
|  liveOdkazy            | *Není aktuálně implementováno*                                                                    |
|  oznámeníEee   | Zastaralé pro nabídky migrované do Centra partnerů. E-maily s oznámením pro migrované nabídky budou odeslány na e-mail uvedený v části Kontaktní údaje prodejce v nastavení účtu.<br><br>U nemigrovaných nabídek je třeba o průběhu operace upozornit seznam e-mailových adres oddělených čárkami.        |
|  |  |

### <a name="response-status-codes"></a>Stavové kódy odpovědi

| **kód** |   **Popis**                                                                                 |
| -------  |   ----------------------------------------------------------------------------------------------- |
|  200     |  `OK`- Požadavek byl úspěšně zpracován a aktuální stav nabídky byl vrácen. |
|  400     | `Bad/Malformed request`- Tělo odpovědi na chybu může obsahovat více informací.                 |
|  404     | `Not found`- Zadaná entita neexistuje.                                                |
|  |  |

### <a name="offer-status"></a>Stav nabídky

|  **Název**                    |    **Popis**                                       |
|  --------------------------  |  ------------------------------------------------------  |
|  Nikdy publikováno              | Nabídka nebyla nikdy zveřejněna.                          |
|  Notstarted                  | Nabídka je nová a není zahájena.                            |
|  WaitingForPublisherReview   | Nabídka čeká na schválení vydavatele.                 |
|  Spuštěno                     | Probíhá zpracování odesílání nabídky.                     |
|  Úspěch                   | Odeslání nabídky bylo dokončeno zpracování.               |
|  Zrušeno                    | Odeslání nabídky bylo zrušeno.                           |
|  Failed                      | Odeslání nabídky se nezdařilo.                                 |
|  |  |

### <a name="step-status"></a>Stav kroku

|  **Název**                    |    **Popis**                           |
|  -------------------------   |  ------------------------------------------  |
|  Notstarted                  | Krok nebyl spuštěn.                        |
|  Probíhá                  | Krok běží.                             |
|  WaitingForPublisherReview   | Krok čeká na schválení vydavatele.      |
|  Čekánína schválení          | Krok čeká na schválení procesu.        |
|  Blokované                     | Krok je blokován.                             |
|  Rejected                    | Krok je odmítnut.                            |
|  Dokončit                    | Krok je dokončen.                            |
|  Zrušeno                    | Krok byl zrušen.                           |
|  |  |
