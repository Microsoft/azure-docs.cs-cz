---
title: Načtení konkrétní nabídky API | Dokumentace Microsoftu
description: Rozhraní API načte zadaný nabídky v rámci oboru názvů vydavatele.
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
ms.openlocfilehash: 9484cf0f549db94be8f1ac2363addca952a3cff3
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809498"
---
<a name="retrieve-a-specific-offer"></a>Načtení konkrétní nabídky
=========================

Načte zadaný nabídky v rámci oboru názvů vydavatele.  

Můžete také načíst konkrétní verzi nabídky nebo načíst nabídky v návrhu, zobrazení nebo slot na produkční. Pokud se slot nezadá, výchozí hodnota je `draft`. Bude výsledkem pokusu o načtení, který nebyl zobrazen nebo publikované nabídky `404 Not Found` chyby.

> [!WARNING]
> Hodnoty tajných kódů pro pole typu tajného kódu se nebudou získávat tímto rozhraním API.

``` http
    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/versions/<version>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/slot/<slotId>?api-version=2017-10-31

```


<a name="uri-parameters"></a>Parametry identifikátoru URI
--------------


| **Název**    | **Popis**                                                                          | **Datový typ** |
|-------------|------------------------------------------------------------------------------------------|---------------|
| publisherId | publisherId. Například Contoso                                                        | Řetězec        |
| offerId     | Identifikátor GUID, který jedinečně identifikuje tuto nabídku.                                                 | Řetězec        |
| version     | Verze nabídky načítají. Ve výchozím nastavení se načte nejnovější nabídky. | Integer       |
| ID      | Slot, ze kterého má být načtena, nabídka může být jedna z:      <br/>  - `Draft` (výchozí) načte verzi nabídky aktuálně v návrhu.  <br/>  -  `Preview` načte verzi nabídky aktuálně ve verzi preview.     <br/>  -  `Production` načte verzi nabídky aktuálně v produkčním prostředí.          |      Výčet |
| verze API-version | Nejnovější verzi rozhraní API                                                                    | Datum          |
|  |  |  |


<a name="header"></a>Záhlaví
------

|  **Název**          |   **Hodnota**            |
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


### <a name="response-body-properties"></a>Vlastnosti textu odpovědi

|  **Název**       |   **Popis**                                                                                                               |
|  -------------  |   -----------------------------------------------------------------------------------------------------                         |
|  offerTypeId    | Určuje typ nabídky                                                                                                    |
|  publisherId    | Jedinečný identifikátor vydavatele                                                                                              |
|  status         | Stav nabídky. Seznam možných hodnot najdete v tématu [stav nabídky](#offer-status) níže.                                  |
|  ID             | Identifikátor GUID, který jednoznačně identifikuje nabídky                                                                                         |
|  version        | Aktuální verze nabídky. Klient nemůže upravit vlastnost version. Se zvýší po jednotlivých publikování.    |
|  Definice     | Skutečné definice úloh                                                                                               |
|  changedTime    | Datum a čas UTC, kdy se naposledy změnil tuto nabídku                                                                                   |
|  |  |


### <a name="response-status-codes"></a>Stavové kódy odezvy

| **kód**  | **Popis**                                                                                                                 |
|  ------   | ------------------------------------------------------------------------------------------------------------------------------- |
|  200      | `OK` -Žádost byla úspěšně zpracována a všechny nabídky v části vydavatele vrácených do klienta.               |
|  400      | `Bad/Malformed request` Text – Chyba odpovědi může obsahovat další informace.                                                 |
|  403      | `Forbidden` -Klienta nemá přístup k určený obor názvů.                                                        |
|  404      | `Not found` -Zadaná entita neexistuje. Klient by měl zkontrolovat publisherId, offerId a verze (Pokud je zadaný).      |
|  |  |


### <a name="offer-status"></a>Stav nabídky

|  **Název**                   |   **Popis**                             |
| --------------------------- |  -------------------------------------------- |
|  NeverPublished             | Nabídka se nikdy nepublikoval.               |
|  NotStarted                 | Nabídka je nový, ale není spuštěna.              |
|  WaitingForPublisherReview  | Nabídka je čekání na schválení vydavatele.      |
|  Spuštěno                    | Nabídka odeslání se zpracovává.          |
|  Úspěch                  | Nabídka odeslání dokončil zpracování.    |
|  Zrušeno                   | Odeslání nabídka byla zrušena.                |
|  Selhalo                     | Nabídka odeslání se nezdařilo.                      |
|  |  |
