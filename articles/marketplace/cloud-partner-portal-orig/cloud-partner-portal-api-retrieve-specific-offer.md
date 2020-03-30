---
title: Načtení specifického rozhraní API nabídky | Azure Marketplace
description: Rozhraní API načte zadanou nabídku v oboru názvů vydavatele.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: a83b664bb770a88f3c4c13a672655e736a46ca75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280452"
---
<a name="retrieve-a-specific-offer"></a>Načtení konkrétní nabídky
=========================

Načte zadanou nabídku v oboru názvů vydavatele.  

Můžete také načíst konkrétní verzi nabídky nebo načíst nabídku v konceptu, zobrazení nebo produkčních slotech. Pokud není zadán a patice, výchozí je `draft`. Pokus o načtení nabídky, která nebyla zobrazena náhled `404 Not Found` nebo publikována, bude mít za následek chybu.

> [!WARNING]
> Tajné hodnoty pro pole tajného typu nebudou tímto rozhraním API načteny.

``` http
    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/versions/<version>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/slot/<slotId>?api-version=2017-10-31

```


<a name="uri-parameters"></a>Parametry identifikátoru URI
--------------


| **Název**    | **Popis**                                                                          | **Datový typ** |
|-------------|------------------------------------------------------------------------------------------|---------------|
| id vydavatele | publisherId. Například společnost Contoso                                                        | Řetězec        |
| offerId     | Guid, který jednoznačně identifikuje nabídku.                                                 | Řetězec        |
| version     | Verze nabídky, která je načítána. Ve výchozím nastavení je načtena nejnovější verze nabídky. | Integer       |
| slotId      | Slot, ze kterého má být nabídka načtena, může být jedním z:      <br/>  - `Draft`(výchozí) načte verzi nabídky, která je aktuálně v konceptu.  <br/>  -  `Preview`načte verzi nabídky, která je aktuálně ve verzi preview.     <br/>  -  `Production`načte verzi nabídky, která je aktuálně v produkčním prostředí.          |      enum |
| verze-api | Nejnovější verze rozhraní API                                                                    | Datum          |
|  |  |  |


<a name="header"></a>Hlavička
------

|  **Název**          |   **Hodnotu**            |
|  ---------------   |  --------------        |
|  Typ obsahu      | `application/json`     |
|  Autorizace     | `Bearer YOUR_TOKEN`    |
|  |  |


<a name="body-example"></a>Příklad těla
------------

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
            "microsoft-azure-marketplace.categories": [
                "devService",
                "networking",
                "database",
                "cache",
                "security"
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


### <a name="response-body-properties"></a>Vlastnosti těla odezvy

|  **Název**       |   **Popis**                                                                                                               |
|  -------------  |   -----------------------------------------------------------------------------------------------------                         |
|  offerTypeId    | Identifikuje typ nabídky                                                                                                    |
|  id vydavatele    | Jedinečný identifikátor vydavatele                                                                                              |
|  status         | Stav nabídky. Seznam možných hodnot naleznete níže v [tématu Stav nabídky.](#offer-status)                                  |
|  ID             | IDENTIFIKÁTOR GUID, který jednoznačně identifikuje nabídku                                                                                         |
|  version        | Aktuální verze nabídky. Vlastnost version nemůže být klientem změněna. Po každém publikování se to zintáží.    |
|  definice     | Skutečná definice pracovní zátěže                                                                                               |
|  changedTime    | Čas data času u času u změny nabídky                                                                                   |
|  |  |


### <a name="response-status-codes"></a>Stavové kódy odpovědi

| **kód**  | **Popis**                                                                                                                 |
|  ------   | ------------------------------------------------------------------------------------------------------------------------------- |
|  200      | `OK`- Požadavek byl úspěšně zpracován a všechny nabídky pod vydavatelem byly vráceny klientovi.               |
|  400      | `Bad/Malformed request`- Tělo odpovědi na chybu může obsahovat více informací.                                                 |
|  403      | `Forbidden`- Klient nemá přístup k zadanému oboru názvů.                                                        |
|  404      | `Not found`- Zadaná entita neexistuje. Klient by měl zkontrolovat publisherId, offerId a verze (pokud je zadán).      |
|  |  |


### <a name="offer-status"></a>Stav nabídky

|  **Název**                   |   **Popis**                             |
| --------------------------- |  -------------------------------------------- |
|  Nikdy publikováno             | Nabídka nebyla nikdy zveřejněna.               |
|  Notstarted                 | Nabídka je nová, ale není spuštěna.              |
|  WaitingForPublisherReview  | Nabídka čeká na schválení vydavatele.      |
|  Spuštěno                    | Probíhá zpracování odesílání nabídky.          |
|  Úspěch                  | Odeslání nabídky bylo dokončeno zpracování.    |
|  Zrušeno                   | Odeslání nabídky bylo zrušeno.                |
|  Failed                     | Odeslání nabídky se nezdařilo.                      |
|  |  |
