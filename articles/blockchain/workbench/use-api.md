---
title: Using Azure Blockchain Workbench REST APIs
description: Scenarios for how to use the Azure Blockchain Workbench Preview REST API
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 188bbb9a9f6d289a7950ff74596352dff36e79f2
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74324201"
---
# <a name="using-the-azure-blockchain-workbench-preview-rest-api"></a>Using the Azure Blockchain Workbench Preview REST API

Azure Blockchain Workbench Preview REST API provides developers and information workers a way to build rich integrations to blockchain applications. Tento dokument popisuje několik klíčových metod rozhraní REST API služby Workbench. For example, suppose a developer wants to create a custom blockchain client. This blockchain client allows signed in users to view and interact with their assigned blockchain applications. Klient umožňuje uživatelům zobrazit instance kontraktů a provádět akce s chytrými kontrakty. The client uses the Workbench REST API in the context of the signed-in user to do the following actions:

* Výpis aplikací
* Výpis pracovních postupů pro aplikaci
* Výpis instancí chytrých kontraktů pro pracovní postup
* Výpis dostupných akcí pro kontrakt
* Provedení akce pro kontrakt

The example blockchain applications used in the scenarios, can be [downloaded from GitHub](https://github.com/Azure-Samples/blockchain).

## <a name="blockchain-workbench-api-endpoint"></a>Blockchain Workbench API endpoint

Blockchain Workbench APIs are accessed through an endpoint for your deployment. To get the API endpoint URL for your deployment:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. In the left-hand navigation pane, select **Resource groups**.
1. Choose the resource group name your deployed Blockchain Workbench.
1. Select the **TYPE** column heading to sort the list alphabetically by type.
1. There are two resources with type **App Service**. Select the resource of type **App Service** *with* the "-api" suffix.
1. In the App Service **Overview**, copy the **URL** value, which represents the API endpoint URL to your deployed Blockchain Workbench.

    ![App service API endpoint URL](media/use-api/app-service-api.png)

## <a name="list-applications"></a>Výpis aplikací

Once a user has signed into the blockchain client, the first task is to retrieve all Blockchain Workbench applications for the user. V tomto scénáři má uživatel přístup do dvou aplikací:

1. [Asset transfer](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
2. [Refrigerated transportation](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

Použití rozhraní [Applications GET API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationsget):

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

The response lists all blockchain applications to which a user has access in Blockchain Workbench. Blockchain Workbench administrators get every blockchain application. Non-Workbench administrators get all blockchains for which they have at least one associated application role or an associated smart contract instance role.

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

Once a user selects the applicable blockchain application (such as **Asset Transfer**), the blockchain client retrieves all workflows of the specific blockchain application. Uživatelé si pak můžou vybrat vhodný pracovní postup před zobrazením všech instancí chytrých kontraktů pro daný pracovní postup. Každá blockchainová aplikace má jeden nebo více pracovních postupů a každý pracovní postup má nula nebo více instancí chytrých kontraktů. For a blockchain client application that has only one workflow, we recommend skipping the user experience flow that allows users to select the appropriate workflow. In this case, **Asset Transfer** has only one workflow, also called **Asset Transfer**.

Použití rozhraní [Applications Workflows GET API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/workflowsget):

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

Vrátí seznam všech pracovních postupů zadané blockchainové aplikace, ke kterým má uživatel přístup ve službě Blockchain Workbench. Blockchain Workbench administrators get every blockchain workflow. Non-Workbench administrators get all workflows for which they have at least one associated application role or is associated with a smart contract instance role.

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

Once a user selects the applicable workflow, this case **Asset Transfer**, the blockchain client will retrieve all smart contract instances for the specified workflow. You can use this information to show all smart contract instances for the workflow. Or you can allow users to deep dive into any of the shown smart contract instances. V tomto příkladu předpokládejme, že by uživatel chtěl komunikovat s některou z instancí chytrých kontraktů kvůli provedení akce.

Použití rozhraní [Contracts GET API](/rest/api/azure-blockchain-workbench/contractsv2/contractsget):

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

Vrátí seznam všech instancí chytrých kontraktů zadaného pracovního postupu. Workbench administrators get all smart contract instances. Non-Workbench administrators get every smart contract instance for which they have at least one associated application role or is associated with a smart contract instance role.

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

Once a user decides to deep dive into a contract, the blockchain client can then show the available user actions given the state of the contract. V tomto příkladu uživatel hledá všechny dostupné akce pro nový chytrý kontrakt, který vytvořil:

* Modify: Umožňuje uživateli upravit popis a cenu majetku.
* Terminate: Allows the user to end the contract of the asset.

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

Uživatel se potom může rozhodnout, že provede akci pro zadanou instanci chytrého kontraktu. In this case, consider the scenario where a user would like to modify the description and price of an asset to the following action:

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

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Rozhraní REST API služby Azure Blockchain Workbench](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)