---
title: Načtení rozhraní API operací – Azure Marketplace
description: Rozhraní API pro načtení všech operací na této nabídce nebo pro získání konkrétní operace pro zadaný operationId.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: cb44d977407a7e854603e6bbacf3591752b109c2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87271939"
---
# <a name="retrieve-operations"></a>Načtení operací

> [!NOTE]
> Rozhraní API pro portál partnerů cloudu jsou integrovaná s a budou pokračovat v práci v partnerském centru. Přechod přináší malé změny. Projděte si změny uvedené v části [portál partnerů cloudu rozhraní API](./cloud-partner-portal-api-overview.md) , abyste zajistili, že kód pokračuje v práci po přechodu do partnerského centra. Rozhraní API CPP by se mělo používat jenom pro existující produkty, které už jsou integrované před přechodem do partnerského centra; nové produkty by měly používat rozhraní API pro odesílání v partnerském centru.

Načte všechny operace v nabídce nebo získá konkrétní operaci pro zadaný operationId. Klient může pomocí parametrů dotazů filtrovat spuštěné operace.

``` https

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/submissions/?api-version=2017-10-31&status=<filteredStatus>

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/operations/<operationId>?api-version=2017-10-31

```

## <a name="uri-parameters"></a>Parametry identifikátoru URI

|  **Název**          |      **Popis**                                                                                           | **Datový typ** |
|  ----------------  |     --------------------------------------------------------------------------------------------------------   |  -----------  |
|  publisherId       |  Identifikátor vydavatele, například `Contoso`                                                                   |  Řetězec       |
|  Hodnotami OfferId           |  Identifikátor nabídky                                                                                              |  Řetězec       |
|  operationId       |  Identifikátor GUID, který jedinečně identifikuje operaci na nabídce OperationId může být načten pomocí tohoto rozhraní API a také se vrátí v hlavičce HTTP odpovědi pro jakoukoli dlouhodobou operaci, jako je například rozhraní API [nabídky publikovat](./cloud-partner-portal-api-publish-offer.md) .  |   Identifikátor GUID   |
|  verze-api       | Nejnovější verze rozhraní API |    Date (Datum)      |
|  |  |  |

## <a name="header"></a>Hlavička

|  **Název**          |  **Hodnota**           |
|  ---------------   | -------------------- |
|  Typ obsahu      | `application/json`   |
|  Autorizace     | `Bearer YOUR_TOKEN`  |
|  |  |

## <a name="body-example"></a>Příklad textu

### <a name="response"></a>Odpověď

#### <a name="get-operations"></a>ZÍSKAT operace

``` json
    [
        {
            "id": "5a63deb5-925b-4ee0-938b-7c86fbf287c5",
            "offerId": "56615b67-2185-49fe-80d2-c4ddf77bb2e8",
            "offerVersion": 1,
            "offerTypeId": "microsoft-azure-virtualmachines",
            "publisherId": "contoso",
            "submissionType": "publish",
            "submissionState": "running",
            "publishingVersion": 2,
            "slot": "staging",
            "version": 636576975611768314,
            "definition": {
                "metadata": {
                    "emails": "jdoe@contoso.com"
                }
            },
            "changedTime": "2018-03-26T21:46:01.179948Z"
        }
    ]
```

#### <a name="get-operation"></a>Operace GET

``` json
    [
        {
            "status" : "running",
            "messages" : [],
            "publishingVersion" : 2,
            "offerVersion" : 1,
            "cancellationRequestState": "canCancel",
            "steps": [
                        {
                            "estimatedTimeFrame": "< 15 min",
                            "id": "displaydummycertify",
                            "stepName": "Validate Pre-Requisites",
                            "description": "Offer settings provided are validated",
                            "status": "complete",
                            "messages": 
                            [
                                {
                                    "messageHtml": "Step completed.",
                                    "level": "information",
                                    "timestamp": "2017-03-28T19:50:36.500052Z"
                                }
                            ],
                            "progressPercentage": 100
                        },
                        {
                            "estimatedTimeFrame": "< 5 day",
                            "id": "displaycertify",
                            "stepName": "Certification",
                            "description": "Your offer is analyzed by our certification systems for issues.",
                            "status": "blocked",
                            "messages": 
                            [
                                {
                                    "messageHtml": "No virtual machine image was found for the plan contoso.",
                                    "level": "error",
                                    "timestamp": "2017-03-28T19:50:39.5506018Z"
                                },
                                {
                                    "messageHtml": "This step has not started yet.",
                                    "level": "information",
                                    "timestamp": "2017-03-28T19:50:39.5506018Z"
                                }
                            ],
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
                            "description": "Your virtual machine is packaged for being shown to your customers. Additionally, we hookup our lead generation systems to send leads for your offer.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "id": "publisher-signoff",
                            "stepName": "Publisher signoff",
                            "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "~2-5 days",
                            "id": "live",
                            "stepName": "Live",
                            "description": "Offer is publicly visible and is available for purchase.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        }
                    ],
                "previewLinks": [],
                "liveLinks": [],
            }
        }
    ]
```

### <a name="response-body-properties"></a>Vlastnosti textu odpovědi

|  **Název**                    |  **Popis**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
|  id                          | Identifikátor GUID, který jedinečně identifikuje operaci                                                       |
|  submissionType              | Určuje typ operace hlášené pro nabídku, například `Publish/GoLive`      |
|  createdDateTime             | Datum a čas UTC při vytvoření operace                                                       |
|  lastActionDateTime          | Datum a čas UTC, kdy byla provedena poslední aktualizace na operaci                                       |
|  status                      | Stav operace, buď `not started` \| `running` \| `failed` \| `completed` . V jednom okamžiku může být stav jenom jedna operace `running` . |
|  error                       | Chybová zpráva pro neúspěšné operace                                                               |
|  |  |

### <a name="response-step-properties"></a>Vlastnosti kroku odpovědi

|  **Název**                    |  **Popis**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
| estimatedTimeFrame | Odhadovaná doba trvání této operace |
| id | Jedinečný identifikátor procesu kroku |
| description | Popis kroku |
| Step | Popisný název kroku |
| status | Stav kroku, `notStarted` \| `running` \| `failed` buď \|`completed` |
| cloud-zařízení | Všechna oznámení nebo upozornění zjištěná v průběhu tohoto kroku. Pole řetězců |
| progressPercentage | Celé číslo od 0 do 100 označující průběh kroku |
| | |

### <a name="response-status-codes"></a>Stavové kódy odpovědí

| **Kód**  |   **Popis**                                                                                  |
|  -------- |   -------------------------------------------------------------------------------------------------|
|  200      | `OK` -Požadavek byl úspěšně zpracován a byly vráceny požadované operace.        |
|  400      | `Bad/Malformed request` – Tělo chybové odpovědi může obsahovat více informací.                    |
|  403      | `Forbidden` – Klient nemá přístup k zadanému oboru názvů.                          |
|  404      | `Not found` -Zadaná entita neexistuje.                                                 |
|  |  |
