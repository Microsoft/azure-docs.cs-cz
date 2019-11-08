---
title: Načíst rozhraní API operací | Azure Marketplace
description: Načte všechny operace v nabídce nebo získá konkrétní operaci pro zadaný operationId.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: c3eb77744d61322ca0aed20bb2b3f486cc02ac70
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819594"
---
<a name="retrieve-operations"></a>Načtení operací
===================

Načte všechny operace v nabídce nebo získá konkrétní operaci pro zadaný operationId. Klient může pomocí parametrů dotazů filtrovat spuštěné operace.

``` https

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/submissions/?api-version=2017-10-31&status=<filteredStatus>

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/operations/<operationId>?api-version=2017-10-31

```


<a name="uri-parameters"></a>Parametry identifikátoru URI
--------------

|  **Název**          |      **Popis**                                                                                           | **Datový typ** |
|  ----------------  |     --------------------------------------------------------------------------------------------------------   |  -----------  |
|  publisherId       |  Identifikátor vydavatele, například `Contoso`                                                                   |  Řetězec       |
|  Hodnotami OfferId           |  Identifikátor nabídky                                                                                              |  Řetězec       |
|  operationId       |  Identifikátor GUID, který jedinečně identifikuje operaci na nabídce OperationId může být načten pomocí tohoto rozhraní API a také se vrátí v hlavičce HTTP odpovědi pro jakoukoli dlouhodobou operaci, jako je například rozhraní API [nabídky publikovat](./cloud-partner-portal-api-publish-offer.md) .  |   Guid   |
|  filteredStatus    | Volitelný parametr dotazu použitý k filtrování podle stavu (například `running`) v kolekci vrácené tímto rozhraním API.  |   Řetězec |
|  verze API-Version       | Nejnovější verze rozhraní API                                                                                           |    Datum      |
|  |  |  |


<a name="header"></a>Hlavička
------

|  **Název**          |  **Hodnota**           |
|  ---------------   | -------------------- |
|  Typ obsahu      | `application/json`   |
|  Autorizace     | `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Příklad textu
------------

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
                "notificationEmails": "jondoe@contoso.com"
            } 
        }
    ]
```


### <a name="response-body-properties"></a>Vlastnosti textu odpovědi

|  **Název**                    |  **Popis**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
|  id                          | Identifikátor GUID, který jedinečně identifikuje operaci                                                       |
|  submissionType              | Určuje typ operace hlášené pro nabídku, například `Publish/GGoLive`      |
|  createdDateTime             | Datum a čas UTC při vytvoření operace                                                       |
|  lastActionDateTime          | Datum a čas UTC, kdy byla provedena poslední aktualizace na operaci                                       |
|  status                      | Stav operace, buď `not started` \| `running` \| `failed` \| `completed`. V jednom okamžiku může mít stav `running` jenom jedna operace. |
|  error                       | Chybová zpráva pro neúspěšné operace                                                               |
|  |  |


### <a name="response-status-codes"></a>Stavové kódy odpovědí

| **Znakovou**  |   **Popis**                                                                                  |
|  -------- |   -------------------------------------------------------------------------------------------------|
|  200      | `OK` – požadavek byl úspěšně zpracován a byly vráceny požadované operace.        |
|  400      | `Bad/Malformed request` – tělo chybové odpovědi může obsahovat další informace.                    |
|  403      | `Forbidden` – klient nemá přístup k zadanému oboru názvů.                          |
|  404      | `Not found` – zadaná entita neexistuje.                                                 |
|  |  |
