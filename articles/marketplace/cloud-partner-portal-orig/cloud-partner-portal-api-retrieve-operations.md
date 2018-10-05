---
title: Načtení operací rozhraní API | Dokumentace Microsoftu
description: Načte všechny operace týkající se nabídky nebo na konkrétní operace pro zadané ID operace get elementu.
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
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 3f0f087c98f2b6594ab7e841f92ffac7ffe4003e
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809320"
---
<a name="retrieve-operations"></a>Načtení operací
===================

Načte všechny operace týkající se nabídky nebo na konkrétní operace pro zadané ID operace get elementu. Klient může používat parametry dotazu k filtrování běžící operace.

``` https

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/submissions/?api-version=2017-10-31&status=<filteredStatus>

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/operations/<operationId>?api-version=2017-10-31

```


<a name="uri-parameters"></a>Parametry identifikátoru URI
--------------

|  **Název**          |      **Popis**                                                                                           | **Datový typ** |
|  ----------------  |     --------------------------------------------------------------------------------------------------------   |  -----------  |
|  publisherId       |  Identifikátor vydavatel, například `Contoso`                                                                   |  Řetězec       |
|  offerId           |  Identifikátor nabídky                                                                                              |  Řetězec       |
|  operationId       |  Identifikátor GUID, který jednoznačně identifikuje operace týkající se nabídky. OperationId může načíst s použitím tohoto rozhraní API a také dochází v hlavičce HTTP odpovědi pro jakékoli dlouho spuštěné operace, jako [nabídka publikovat](./cloud-partner-portal-api-publish-offer.md) rozhraní API.  |   Guid   |
|  filteredStatus    | Nepovinný dotaz parametr slouží k filtrování podle stavu (třeba `running`) na kolekci vrácené poskytovatelem tohoto rozhraní API.  |   Řetězec |
|  verze API-version       | Nejnovější verzi rozhraní API                                                                                           |    Datum      |
|  |  |  |


<a name="header"></a>Záhlaví
------

|  **Název**          |  **Hodnota**           |
|  ---------------   | -------------------- |
|  Typ obsahu      | `application/json`   |
|  Autorizace     | `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Příklad těla
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

#### <a name="get-operation"></a>Operace GET elementu

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
|  id                          | Identifikátor GUID, který jednoznačně identifikuje operace                                                       |
|  submissionType              | Určuje typ operace ohlašovaný nabídky, například `Publish/GGoLive`      |
|  createdDateTime             | Datum a čas UTC při vytvoření operaci                                                       |
|  lastActionDateTime          | Datum a čas UTC při byla provedena poslední aktualizace na operaci                                       |
|  status                      | Stav operace, buď "nebylo zahájeno | spuštěno | neúspěšné | dokončení`. Only one operation can have status `systémem "v čase. |
|  error                       | Chybová zpráva pro neúspěšné operace                                                               |
|  |  |


### <a name="response-status-codes"></a>Stavové kódy odezvy

| **kód**  |   **Popis**                                                                                  |
|  -------- |   -------------------------------------------------------------------------------------------------|
|  200      | `OK` -Žádost byla úspěšně zpracována a byly vráceny požadované operace.        |
|  400      | `Bad/Malformed request` Text – Chyba odpovědi může obsahovat další informace.                    |
|  403      | `Forbidden` -Klienta nemá přístup k určený obor názvů.                          |
|  404      | `Not found` -Zadaná entita neexistuje.                                                 |
|  |  |
