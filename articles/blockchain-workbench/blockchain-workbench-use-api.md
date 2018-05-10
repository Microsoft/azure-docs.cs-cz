---
title: Pomocí rozhraní REST API Azure Blockchain Workbench
description: Scénáře použití rozhraní REST API Azure Blockchain Workbench
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/2/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 27ed94b3ce14c57e369b0c80d4c53b72a5ae40a8
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="using-the-azure-blockchain-workbench-rest-api"></a>Pomocí rozhraní REST API Azure Blockchain Workbench 

Azure Blockchain Workbench REST API poskytuje vývojářům a informační pracovníci, způsob, jak vytvářet bohaté integrace blockchain aplikace. Tento dokument vás provede několik metod klíče rozhraní API REST Workbench. Představte si třeba situaci, kdy vývojář chce vytvořit vlastní blockchain klienta, které umožňuje přihlášeného uživatelům zobrazení a interakci s jejich přiřazené blockchain aplikace. Klient umožňuje uživatelům zobrazit instance kontraktu a provést akce v inteligentní smluv. Klient používá rozhraní API REST Workbench v kontextu přihlášeného uživatele k postupujte takto:

* Seznam aplikací
* Seznam pracovních postupů pro aplikaci
* Seznam instancí inteligentní kontrakt pro pracovní postup
* Seznam dostupné akce pro kontraktu
* Provést akci pro kontraktu

## <a name="list-applications"></a>Seznam aplikací

Jakmile uživatel má přihlášeni blockchain klienta, je první úlohou načíst všechny Blockchain Workbench blockchain aplikace pro uživatele. V tomto scénáři má uživatel přístup do dvou aplikací:

1.  Převod majetku
2.  Chladicí Transport

Použití [aplikace GET API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationsget):

``` http
GET /api/v1/applications 
Authorization : Bearer {access token}
```

Odpověď obsahuje seznam všech blockchain aplikací, ke kterým má uživatel přístup v Blockchain Workbench. Blockchain Workbench správci získat všechny blockchain aplikace, při bez Workbench správci získat všechny blockchains, pro které mají alespoň jeden přidružené aplikace nebo role instance přidružené inteligentní kontrakt.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications?skip=2",
    "applications": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
            "displayName": "Asset Transfer",
            "createdByUserId": 1,
            "createdDtTm": "2018-04-28T05:59:14.4733333",
            "enabled": true,
            "applicationRoles": null
        },
        {
            "id": 2,
            "name": "RefrigeratedTransportation",
            "description": "Application to track end-to-end transportation of perishable goods.",
            "displayName": "Refrigerated Transportation",
            "createdByUserId": 7,
            "createdDtTm": "2018-04-28T18:25:38.71",
            "enabled": true,
            "applicationRoles": null
        }
    ]
}
```

## <a name="list-workflows-for-an-application"></a>Seznam pracovních postupů pro aplikaci

Jakmile uživatel vybere blockchain příslušné aplikace, v takovém případě Asset přenosu, blockchain klient načte všechny pracovní postupy blockchain konkrétní aplikace. Uživatelé pak můžete vybrat použít pracovní postup před se zobrazí všechny instance inteligentní kontrakt pro pracovní postup. Každá aplikace blockchain má jeden nebo více pracovních postupů a každý pracovní postup má nula nebo inteligentní kontrakt instancí. Při sestavování blockchain klientské aplikace, se doporučuje přeskočit umožníte uživatelům vyberte odpovídající pracovní postup, když je jenom jeden pracovní postup pro aplikace blockchain toku činnost koncového uživatele. V takovém případě Asset přenosu má jenom jeden pracovní postup, označované taky jako Asset přenosu.

Použití [pracovní postupy aplikace GET API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/workflowsget):

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

Odpověď obsahuje seznam všech pracovních postupů zadaný blockchain aplikace, ke které má uživatel přístup Blockchain Workbench. Správci Blockchain Workbench získat všechny pracovní postupy blockchain, zatímco jiné Workbench správci získat všechny pracovní postupy, pro které mají alespoň jednu roli přidružené aplikace nebo je přidružen k instanci roli inteligentní kontrakt.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications/1/workflows?skip=1",
    "workflows": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Handles the business logic for the asset transfer scenario",
            "displayName": "Asset Transfer",
            "applicationId": 1,
            "constructorId": 1,
            "startStateId": 1
        }
    ]
}
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>Seznam instancí inteligentní kontrakt pro pracovní postup

Jakmile uživatel vybere použít pracovní postup, tento případu Asset přenosu, blockchain klient načte všechny instance inteligentní kontrakt pro zadaný pracovní postup. Tyto informace můžete zobrazit všechny instance inteligentní kontrakt pro pracovní postup a povolit uživatelům podrobné informace do jakéhokoli z instancí uvedené inteligentní kontrakt. V tomto příkladu zvažte, že uživatel chtěli komunikovat s jednou instancí inteligentní kontrakt provádět akci.

Použití [měnící GET API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contracts/contractsget):

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

Odpověď obsahuje seznam všech instancí inteligentní kontrakt zadaný pracovního postupu. Správci Workbench získat všechny instance inteligentní kontrakt, během bez Workbench správci získat všechny inteligentní kontrakt instancí, pro které mají alespoň jednu roli přidružené aplikace, nebo je přidružen k instanci roli inteligentní kontrakt.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts?skip=3&workflowId=1",
    "contracts": [
        {
            "id": 1,
            "provisioningStatus": 2,
            "connectionID": 1,
            "ledgerIdentifier": "0xbcb6127be062acd37818af290c0e43479a153a1c",
            "deployedByUserId": 1,
            "workflowId": 1,
            "contractCodeId": 1,
            "contractProperties": [
                {
                    "workflowPropertyId": 1,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 2,
                    "value": "My first car"
                },
                {
                    "workflowPropertyId": 3,
                    "value": "54321"
                },
                {
                    "workflowPropertyId": 4,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 5,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 6,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 7,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 8,
                    "value": "0xd882530eb3d6395e697508287900c7679dbe02d7"
                }
            ],
            "transactions": [
                {
                    "id": 1,
                    "connectionId": 1,
                    "transactionHash": "0xf3abb829884dc396e03ae9e115a770b230fcf41bb03d39457201449e077080f4",
                    "blockID": 241,
                    "from": "0xd882530eb3d6395e697508287900c7679dbe02d7",
                    "to": null,
                    "value": 0,
                    "isAppBuilderTx": true
                }
            ],
            "contractActions": [
                {
                    "id": 1,
                    "userId": 1,
                    "provisioningStatus": 2,
                    "timestamp": "2018-04-29T23:41:14.9333333",
                    "parameters": [
                        {
                            "name": "Description",
                            "value": "My first car"
                        },
                        {
                            "name": "Price",
                            "value": "54321"
                        }
                    ],
                    "workflowFunctionId": 1,
                    "transactionId": 1,
                    "workflowStateId": 1
                }
            ]
        }
    ]
}
```

## <a name="list-available-actions-for-a-contract"></a>Seznam dostupné akce pro kontraktu

Jakmile uživatel se rozhodl podrobné informace do jedné smluv, můžete klienta blockchain pak zobrazíte všechny dostupné akce zadaná stav kontrakt uživatelem. V tomto příkladu je uživatel vyhledávání na všechny dostupné akce pro inteligentní nové smlouvy, které se vytvářely:

* Upravte: Umožňuje uživateli upravit popis a cenu prostředek.
* Ukončit: Umožňuje uživatelům ukončit kontrakt prostředku.

Použití [smlouvy akce GET API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contracts/contractactionget):

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

Odpověď obsahuje seznam na které může uživatel provést všechny akce zadané aktuální stav instance zadaný inteligentní kontrakt. Uživatelé získají všechny příslušné akce, pokud uživatel má přidružené aplikaci role nebo je přidružen k instanci roli inteligentní kontrakt pro aktuální stav instance zadaný inteligentní kontrakt.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts/1/actions?skip=2",
    "workflowFunctions": [
        {
            "id": 2,
            "name": "Modify",
            "description": "Modify the description/price attributes of this asset transfer instance",
            "displayName": "Modify",
            "parameters": [
                {
                    "id": 1,
                    "name": "description",
                    "description": "The new description of the asset",
                    "displayName": "Description",
                    "type": {
                        "id": 2,
                        "name": "string",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                },
                {
                    "id": 2,
                    "name": "price",
                    "description": "The new price of the asset",
                    "displayName": "Price",
                    "type": {
                        "id": 3,
                        "name": "money",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                }
            ],
            "workflowId": 1
        },
        {
            "id": 3,
            "name": "Terminate",
            "description": "Used to cancel this particular instance of asset transfer",
            "displayName": "Terminate",
            "parameters": [],
            "workflowId": 1
        }
    ]
}
```

## <a name="execute-an-action-for-a-contract"></a>Provést akci pro kontraktu

Uživatel potom se můžete rozhodnout k provést akci pro zadaný inteligentní kontrakt instance. V takovém případě vezměte v úvahu scénář, kde uživatel chtěli upravit popis a cenu prostředek takto:

* Popis: "Moje aktualizované auto"
* Cena: 54321

Použití [smlouvy akce POST API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contracts/contractactionpost):

``` http
POST /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
actionInformation: {
    "workflowFunctionId": 2,
    "workflowActionParameters": [
        {
            "name": "description",
            "value": "My updated car"
        },
        {
            "name": "price",
            "value": "54321"
        }
    ]
}
```

Uživatelé mohou pouze provést akci při aktuálním stavu instance zadaný inteligentní kontraktu a uživatele přidružené aplikace nebo inteligentní kontrakt instance role. Pokud je požadavek post úspěšné, vrátí se s žádné odpovědi odpověď HTTP 200 OK.

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>Další postup

* [Azure Blockchain Workbench REST API – referenční informace](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)