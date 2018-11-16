---
title: Použití rozhraní REST API služby Azure Blockchain Workbench
description: Scénáře použití rozhraní REST API služby Azure Blockchain Workbench
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 11/14/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: c1a9b526f08f330d62c30dd1d676e95460aee6c2
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51712343"
---
# <a name="using-the-azure-blockchain-workbench-rest-api"></a>Použití rozhraní REST API služby Azure Blockchain Workbench

Rozhraní REST API služby Azure Blockchain Workbench poskytuje vývojářům a informačním pracovníkům způsob, jak vytvářet výkonné integrace pro blockchainové aplikace. Tento dokument popisuje několik klíčových metod rozhraní REST API služby Workbench. Předpokládejme například, že si vývojář chce vytvořit vlastní blockchainové klienta. Tento klient blockchain umožní přihlášených uživatelů k zobrazení a interakci s jejich přiřazené blockchainové aplikace. Klient umožňuje uživatelům zobrazit instance kontraktů a provádět akce s chytrými kontrakty. Klient používá rozhraní REST API Workbenche v kontextu uživatele přihlášeného k proveďte následující akce:

* Výpis aplikací
* Výpis pracovních postupů pro aplikaci
* Výpis instancí chytrých kontraktů pro pracovní postup
* Výpis dostupných akcí pro kontrakt
* Provedení akce pro kontrakt

Příklad blockchain používá ve scénářích, může se jednat o [stáhnou z Githubu](https://github.com/Azure-Samples/blockchain).

## <a name="list-applications"></a>Výpis aplikací

Po přihlášení uživatele klienta blockchain, je první úkol k načtení všech Blockchain Workbench aplikací pro uživatele. V tomto scénáři má uživatel přístup do dvou aplikací:

1. [Převod majetku](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
2. [Chladicí doprava](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

Použití rozhraní [Applications GET API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationsget):

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

Odpověď obsahuje všechny blockchainové aplikace, ke kterým má uživatel přístup Blockchain Workbench. Správci Blockchain Workbench umožňuje získat všechny blockchainové aplikace. Správci non-Workbench získat všechny blockchainy, ke kterým mají alespoň jednu roli přidružené aplikace nebo roli instance přidružené inteligentní kontraktu.

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

## <a name="list-workflows-for-an-application"></a>Výpis pracovních postupů pro aplikaci

Když uživatel vybere příslušné blockchainové aplikace (například **Asset přenos**), blockchain klient načte všechny pracovní postupy konkrétní blockchainové aplikace. Uživatelé si pak můžou vybrat vhodný pracovní postup před zobrazením všech instancí chytrých kontraktů pro daný pracovní postup. Každá blockchainová aplikace má jeden nebo více pracovních postupů a každý pracovní postup má nula nebo více instancí chytrých kontraktů. Pro klientskou aplikaci blockchain, který má pouze jeden pracovní postup doporučujeme, přeskakuje se tok zkušenosti uživatele, který umožňuje uživatelům vybrat odpovídající pracovního postupu. V takovém případě **Asset přenos** má jenom jeden pracovní postup, také nazývané **přenos Asset**.

Použití rozhraní [Applications Workflows GET API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/workflowsget):

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

Vrátí seznam všech pracovních postupů zadané blockchainové aplikace, ke kterým má uživatel přístup ve službě Blockchain Workbench. Blockchain Workbench umožňuje správcům získat každý pracovní postup blockchain. Správci non-Workbench získat všechny pracovní postupy, které mají alespoň jednu roli přidružené aplikace nebo je přidružený k instanci role inteligentní kontraktu.

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

## <a name="list-smart-contract-instances-for-a-workflow"></a>Výpis instancí chytrých kontraktů pro pracovní postup

Když uživatel vybere příslušné pracovní postup, takovém **Asset přenos**, klient blockchain se načtou všechny instance inteligentní smlouvy zadaného pracovního postupu. Tyto informace můžete zobrazit všechny inteligentní smlouvy instance pracovního postupu. Nebo můžete umožnit uživatelům Ponořte se do některé z instancí zobrazených inteligentní kontraktu. V tomto příkladu předpokládejme, že by uživatel chtěl komunikovat s některou z instancí chytrých kontraktů kvůli provedení akce.

Použití rozhraní [Contracts GET API](/rest/api/azure-blockchain-workbench/contractsv2/contractsget):

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

Vrátí seznam všech instancí chytrých kontraktů zadaného pracovního postupu. Správci aplikace Workbench získat všechny instance inteligentní kontraktu. Správci non-Workbench získat všechny instance inteligentní smlouvy pro kterou mají alespoň jednu roli přidružené aplikace nebo je přidružený k instanci role inteligentní kontraktu.

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

## <a name="list-available-actions-for-a-contract"></a>Výpis dostupných akcí pro kontrakt

Jakmile se uživatel rozhodne podrobné informace o kontrakt, blockchain klienta můžete zobrazit pak k dispozici uživatelské akce zadaný stav smlouvy. V tomto příkladu uživatel hledá všechny dostupné akce pro nový chytrý kontrakt, který vytvořil:

* Modify: Umožňuje uživateli upravit popis a cenu majetku.
* Ukončit: Umožňuje uživateli ukončení smlouvy assetu.

Použití rozhraní [Contract Action GET API](/rest/api/azure-blockchain-workbench/contractsv2/contractactionget):

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

Vrátí seznam všech akcí, které může uživatel provést za aktuálního stavu zadané instance chytrého kontraktu. Uživatelé získají všechny použitelné akce, pokud má uživatel přidruženou roli aplikace nebo má přidruženou roli instance chytrého kontraktu pro aktuální stav zadané instance chytrého kontraktu.

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

## <a name="execute-an-action-for-a-contract"></a>Provedení akce pro kontrakt

Uživatel se potom může rozhodnout, že provede akci pro zadanou instanci chytrého kontraktu. V takovém případě vezměte v úvahu scénář, ve kterém uživatel chtěli upravit popis a cena prostředku, abyste tuto akci:

* Popis: „Moje vylepšené auto“
* Cena: 54321

Použití rozhraní [Contract Action POST API](/rest/api/azure-blockchain-workbench/contractsv2/contractactionpost):

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

Uživatelé mohou provést pouze akci dostupnou pro aktuální stav zadané instance chytrého kontraktu a pro jim přidruženou roli aplikace nebo přidruženou roli instance chytrého kontraktu. Pokud je požadavek úspěšný, vrátí se odpověď HTTP 200 OK bez dalších informací.

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Rozhraní REST API služby Azure Blockchain Workbench](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)