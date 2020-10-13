---
title: Načtení konkrétního rozhraní API nabídky – Azure Marketplace
description: Rozhraní API pro načtení zadané nabídky v rámci oboru názvů vydavatele.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 9f3ba6b2f13b9f2bb1d538db84723e3a9baaef12
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87271837"
---
# <a name="retrieve-a-specific-offer"></a>Načíst konkrétní nabídku

> [!NOTE]
> Rozhraní API pro portál partnerů cloudu jsou integrovaná s a budou pokračovat v práci v partnerském centru. Přechod přináší malé změny. Projděte si změny uvedené v části [portál partnerů cloudu rozhraní API](./cloud-partner-portal-api-overview.md) , abyste zajistili, že kód pokračuje v práci po přechodu do partnerského centra. Rozhraní API CPP by se mělo používat jenom pro existující produkty, které už jsou integrované před přechodem do partnerského centra; nové produkty by měly používat rozhraní API pro odesílání v partnerském centru.

Načte zadanou nabídku v rámci oboru názvů vydavatele.  

Můžete také načíst konkrétní verzi nabídky nebo načíst nabídku v konceptu, zobrazení nebo produkčních slotech. Pokud není zadaný slot, výchozí hodnota je `draft` . Při pokusu o načtení nabídky, která nebyla v náhledu nebo publikována, bude výsledkem `404 Not Found` Chyba.

> [!WARNING]
> Toto rozhraní API nebude načíst tajné hodnoty pro pole tajného typu.

``` http
    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/versions/<version>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/slot/<slotId>?api-version=2017-10-31

```

## <a name="uri-parameters"></a>Parametry identifikátoru URI

| **Název**    | **Popis**                                                                          | **Datový typ** |
|-------------|------------------------------------------------------------------------------------------|---------------|
| publisherId | publisherId. Například contoso                                                        | Řetězec        |
| Hodnotami OfferId     | Identifikátor GUID, který jedinečně identifikuje nabídku                                                 | Řetězec        |
| verze     | Verze získané nabídky Ve výchozím nastavení se načte nejnovější verze nabídky. | Integer       |
| slotId      | Slot, ze kterého má být nabídka načtena, může být jedním z těchto:      <br/>  - `Draft` (výchozí) načte verzi nabídky, která je aktuálně v konceptu.  <br/>  -  `Preview` Načte verzi nabídky, která je aktuálně ve verzi Preview.     <br/>  -  `Production` Načte verzi nabídky, která je aktuálně v produkčním prostředí.          |      enum |
| verze-api | Nejnovější verze rozhraní API                                                                    | Datum          |
|  |  |  |

## <a name="header"></a>Záhlaví

|  **Název**          |   **Hodnota**            |
|  ---------------   |  --------------        |
|  Typ obsahu      | `application/json`     |
|  Autorizace     | `Bearer YOUR_TOKEN`    |
|  |  |

## <a name="body-example"></a>Příklad textu

### <a name="response"></a>Odpověď

``` json
{
    "offerTypeId": "microsoft-azure-virtualmachines",
    "publisherId": "contoso",
    "status": "failed",
    "id": "059afc24-07de-4126-b004-4e42a51816fe",
    "version": 5,
    "definition": {
        "displayText": "Contoso Virtual Machine Offer",
        "offer": {
            "microsoft-azure-marketplace-testdrive.enabled": false,
            "microsoft-azure-marketplace-testdrive.videos": [],
            "microsoft-azure-marketplace.title": "Contoso App",
            "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
            "microsoft-azure-marketplace.allowedSubscriptions": [
                "59160c40-2e25-4dcf-a2fd-6514cb08bf08"
            ],
            "microsoft-azure-marketplace.usefulLinks": [
            {
                "linkTitle": "Contoso App for Azure",
                "linkUrl": "https://azuremarketplace.microsoft.com"
            }
            ],
                "microsoft-azure-marketplace.categoryMap": [
                {
                "categoryL1": "analytics",
                "categoryL2-analytics": [
                "visualization-and-reporting"
                ]
                },
                {
                "categoryL1": "ai-plus-machine-learning",
                "categoryL2-ai-plus-machine-learning": [
                "bot-services",
                "cognitive-services",
                "other"
                ]
                }
            ],
            "microsoft-azure-marketplace.smallLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/6218c455-9cbc-450c-9920-f2e7a69ee132.png?sv=2014-02-14&sr=b&sig=6O8MM9dgiJ48VK0MwddkyVbprRAnBszyhVkVHGShhkI%3D&se=2019-03-28T19%3A46%3A50Z&sp=r",
            "microsoft-azure-marketplace.mediumLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/557e714b-2f31-4e12-b0cc-e48dd840edf4.png?sv=2014-02-14&sr=b&sig=NwL67NTQf9Gc9VScmZehtbHXpYmxhwZc2foy3o4xavs%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.largeLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/142485da-784c-44cb-9523-d4f396446258.png?sv=2014-02-14&sr=b&sig=xaMxhwx%2FlKYfz33mJGIg8UBdVpsOwVvqhjTJ883o0iY%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.wideLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/48af9013-1df7-4c94-8da8-4626e5039ce0.png?sv=2014-02-14&sr=b&sig=%2BnN7f2tprkrqb45ID6JlT01zXcy1PMTkWXtLKD6nfoE%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.screenshots": [],
            "microsoft-azure-marketplace.videos": [],
            "microsoft-azure-marketplace.leadDestination": "None",
            "microsoft-azure-marketplace.tableLeadConfiguration": {},
            "microsoft-azure-marketplace.blobLeadConfiguration": {},
            "microsoft-azure-marketplace.salesForceLeadConfiguration": {},
            "microsoft-azure-marketplace.crmLeadConfiguration": {},
            "microsoft-azure-marketplace.httpsEndpointLeadConfiguration": {},
            "microsoft-azure-marketplace.marketoLeadConfiguration": {},
            "microsoft-azure-marketplace.privacyURL": "https://azuremarketplace.microsoft.com",
            "microsoft-azure-marketplace.termsOfUse": "Terms of use",
            "microsoft-azure-marketplace.engineeringContactName": "Jon Doe",
            "microsoft-azure-marketplace.engineeringContactEmail": "jondoe@outlook.com",
            "microsoft-azure-marketplace.engineeringContactPhone": "555-555-5555",
            "microsoft-azure-marketplace.supportContactName": "Jon Doe",
            "microsoft-azure-marketplace.supportContactEmail": "jondoe@outlook.com",
            "microsoft-azure-marketplace.supportContactPhone": "555-555-5555",
            "microsoft-azure-marketplace.publicAzureSupportUrl": "",
            "microsoft-azure-marketplace.fairfaxSupportUrl": ""
            },
            "plans": [
            {
                "planId": "contososkuidentifier",
                "microsoft-azure-virtualmachines.skuTitle": "Contoso App",
                "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
                "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
                "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
                "microsoft-azure-virtualmachines.cloudAvailability": [
                    "PublicAzure"
                ],
                "microsoft-azure-virtualmachines.certificationsFairfax": [],
                "virtualMachinePricing": {
                    "isByol": true,
                    "freeTrialDurationInMonths": 0
                },
                "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
                "microsoft-azure-virtualmachines.windowsOSType": "Other",
                "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
                "microsoft-azure-virtualmachines.recommendedVMSizes": [
                    "a0-basic",
                    "a0-standard",
                    "a1-basic",
                    "a1-standard",
                    "a2-basic",
                    "a2-standard"
                ],
                "microsoft-azure-virtualmachines.openPorts": [],
                "microsoft-azure-virtualmachines.vmImages": {
                    "1.0.1": {
                    "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                    "lunVhdDetails": []
                    }
                },
                "regions": [
                    "DZ",
                    "AR"
                ]
            }
            ]
        },
        "changedTime": "2017-06-07T06:15:39.7349221Z"
    }
}
```

### <a name="response-body-properties"></a>Vlastnosti textu odpovědi

|  **Název**       |   **Popis**                                                                                                               |
|  -------------  |   -----------------------------------------------------------------------------------------------------                         |
|  offerTypeId    | Identifikuje typ nabídky.                                                                                                    |
|  publisherId    | Jedinečný identifikátor vydavatele                                                                                              |
|  status         | Stav nabídky Seznam možných hodnot najdete v tématu o [stavu nabídky](#offer-status) níže.                                  |
|  Id             | Identifikátor GUID, který jedinečně identifikuje nabídku                                                                                         |
|  verze        | Aktuální verze nabídky Vlastnost Version nemůže změnit klient. Po každém publikování se zvýší.    |
|  definice     | Skutečná definice úlohy                                                                                               |
|  changedTime    | UTC – datum poslední změny nabídky                                                                                   |
|  |  |

### <a name="response-status-codes"></a>Stavové kódy odpovědí

| **Kód**  | **Popis**                                                                                                                 |
|  ------   | ------------------------------------------------------------------------------------------------------------------------------- |
|  200      | `OK` -Požadavek byl úspěšně zpracován a do klienta byly vráceny všechny nabídky pod vydavatelem.               |
|  400      | `Bad/Malformed request` – Tělo chybové odpovědi může obsahovat více informací.                                                 |
|  403      | `Forbidden` – Klient nemá přístup k zadanému oboru názvů.                                                        |
|  404      | `Not found` -Zadaná entita neexistuje. Klient by měl kontrolovat publisherId, hodnotami OfferId a verzi (Pokud je zadaný).      |
|  |  |

### <a name="offer-status"></a>Stav nabídky

|  **Název**                   |   **Popis**                             |
| --------------------------- |  -------------------------------------------- |
|  NeverPublished             | Nabídka nebyla nikdy publikována.               |
|  NotStarted                 | Nabídka je nová, ale není spuštěná.              |
|  WaitingForPublisherReview  | Nabídka čeká na schválení vydavatele.      |
|  Spuštěno                    | Zpracovává se odeslání nabídky.          |
|  Úspěšný                  | Bylo dokončeno zpracování příspěvku nabídky.    |
|  Zrušeno                   | Odeslání nabídky se zrušilo.                |
|  Neúspěšný                     | Odeslání nabídky se nezdařilo.                      |
|  |  |
