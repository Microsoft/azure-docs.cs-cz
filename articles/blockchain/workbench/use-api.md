---
title: Použití api azure blockchain workbench rest
description: Scénáře použití rozhraní REST API Azure Blockchain Workbench Preview
ms.date: 03/05/2020
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 3084fcf343bc42fe01bf352b6791916d62f63540
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672736"
---
# <a name="using-the-azure-blockchain-workbench-preview-rest-api"></a>Použití rozhraní REST Preview azure blockchainworkbench

Rozhraní REST API Azure Blockchain Workbench Preview poskytuje vývojářům a informačním pracovníkům způsob, jak vytvářet bohaté integrace do blockchainových aplikací. Tento článek upozorňuje na několik scénářů, jak používat rozhraní WORKBENCH REST API. Předpokládejme například, že chcete vytvořit vlastní klienta blockchainu, který umožňuje přihlášeným uživatelům zobrazit a pracovat s přiřazenými blockchainovými aplikacemi. Klient může pomocí rozhraní Blockchain Workbench API zobrazit instance smluv a provést akce na inteligentní smlouvy.

## <a name="blockchain-workbench-api-endpoint"></a>Koncový bod rozhraní API blockchainworkbench

Blockchain Workbench API jsou přístupné prostřednictvím koncového bodu pro vaše nasazení. Jak získat adresu URL koncového bodu rozhraní API pro vaše nasazení:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V levém navigačním podokně vyberte **položku Skupiny prostředků**.
1. Zvolte název skupiny prostředků, který jste nasadili blockchain workbench.
1. Vyberte záhlaví sloupce **TYP,** chcete-li seznam seřadit abecedně podle typu.
1. Existují dva prostředky s typem **App Service**. Vyberte zdroj typu **App Service** *s* příponou "-api".
1. V **přehledu**služby App Service zkopírujte hodnotu **URL,** která představuje adresu URL koncového bodu rozhraní API, do nasazeného blockchainworkbench.

    ![Adresa URL koncového bodu rozhraní API služby Aplikace](media/use-api/app-service-api.png)

## <a name="authentication"></a>Ověřování

Požadavky na rozhraní REST blockchain workbench jsou chráněné pomocí Služby Azure Active Directory (Azure AD).

Chcete-li provést ověřený požadavek na rozhraní REST API, kód klienta vyžaduje před voláním rozhraní API ověření s platnými přihlašovacími údaji. Ověřování je koordinováno mezi různými aktéry podle Azure AD a poskytuje klientovi [přístupový token](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#access-token) jako důkaz ověřování. Token je pak odeslán v hlavičce autorizace HTTP požadavků rozhraní REST API. Další informace o ověřování Azure AD najdete v [tématu Azure Active Directory pro vývojáře](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

Viz [REST API ukázky](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples) příklady, jak ověřit.

## <a name="using-postman"></a>S využitím Postmana

Pokud chcete otestovat nebo experimentovat s rozhraními WORKBENCH API, můžete použít [Postman](https://www.postman.com) k volání rozhraní API pro vaše nasazení. [Stáhněte si ukázkovou kolekci Postman požadavků rozhraní Workbench API](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples/postman) z GitHubu. Podrobnosti o ověřování a použití ukázkových požadavků rozhraní API najdete v souboru README.

## <a name="create-an-application"></a>Vytvoření aplikace

Dvě volání rozhraní API slouží k vytvoření aplikace Blockchain Workbench. Tuto metodu mohou provádět pouze uživatelé, kteří jsou správci workbenchu.

Pomocí [rozhraní API APLIKACE POST](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationspost) nahrajte soubor JSON aplikace a získejte ID aplikace.

### <a name="applications-post-request"></a>Žádost post aplikace

Pomocí parametru **appFile** odešlete konfigurační soubor jako součást těla požadavku.

``` http
POST /api/v1/applications
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="appFile"; filename="/C:/smart-contract-samples/HelloWorld.json"
Content-Type: application/json
```

### <a name="applications-post-response"></a>Aplikace POST odpověď

ID vytvořené aplikace je vráceno v odpovědi. ID aplikace potřebujete k přidružení konfiguračního souboru k souboru kódu při volání dalšího rozhraní API.

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
1
```

### <a name="contract-code-post-request"></a>Požadavek na kód smlouvy POST

Pomocí [rozhraní POST KÓD smlouvy aplikace](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/contractcodepost) předáním ID aplikace k nahrání souboru kódu Solidity aplikace. Datovou část může být jeden soubor Solidity nebo zip soubor obsahující solidity soubory.

Nahraďte následující hodnoty:

| Parametr | Hodnota |
|-----------|-------|
| {applicationId} | Vrácená hodnota z aplikací POST API. |
| {ledgerId} | Index hlavní knihy. Hodnota je obvykle 1. Můžete také zkontrolovat hodnotu v [tabulce Hlavní kniha.](data-sql-management-studio.md) |

``` http
POST /api/v1/applications/{applicationId}/contractCode?ledgerId={ledgerId}
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="contractFile"; filename="/C:/smart-contract-samples/HelloWorld.sol"
```

### <a name="contract-code-post-response"></a>Odpověď POST kódu smlouvy

Pokud je úspěšná, odpověď zahrnuje id kódu smlouvy vytvořené z [tabulky ContractCode](data-sql-management-studio.md).

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
2
```

## <a name="assign-roles-to-users"></a>Přiřazení rolí uživatelům

Pomocí [přiřazení rolí Aplikace post api](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/roleassignmentspost) předáním ID aplikace, ID uživatele a ID role aplikace k vytvoření mapování uživatele role v zadané aplikaci blockchain. Tuto metodu mohou provádět pouze uživatelé, kteří jsou správci workbenchu.

### <a name="role-assignments-post-request"></a>Požadavek NA Přiřazení rolí POST

Nahraďte následující hodnoty:

| Parametr | Hodnota |
|-----------|-------|
| {applicationId} | Vrácená hodnota z rozhraní API APLIKACE POST. |
| {userId} | Hodnota ID uživatele z [tabulky Uživatel](data-sql-management-studio.md). |
| {applicationRoleId} | Hodnota ID role aplikace přidružená k ID aplikace z [tabulky ApplicationRole](data-sql-management-studio.md). |

``` http
POST /api/v1/applications/{applicationId}/roleAssignments
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "userId": {userId},
  "applicationRoleId": {applicationRoleId}
}
```

### <a name="role-assignments-post-response"></a>Odpověď POST přiřazení rolí

Pokud je úspěšná, odpověď obsahuje ID přiřazení vytvořené role z [tabulky RoleAssignment](data-sql-management-studio.md).

``` http
HTTP/1.1 200
1
```

## <a name="list-applications"></a>Výpis aplikací

Pomocí [rozhraní API APLIKACE GET](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationsget) načtěte všechny aplikace Blockchain Workbench pro uživatele. V tomto příkladu má přihlášený uživatel přístup ke dvěma aplikacím:

- [Převod majetku](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
- [Chladírenská doprava](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

### <a name="applications-get-request"></a>Aplikace GET požadavek

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

### <a name="applications-get-response"></a>Aplikace GET odpověď

Odpověď uvádí všechny blockchainové aplikace, ke kterým má uživatel přístup v Blockchain Workbench. Správci Blockchain Workbench dostanou každou blockchainovou aplikaci. Správci bez workbenchu získají všechny blockchainové aplikace, pro které mají alespoň jednu přidruženou roli aplikace nebo přidruženou roli instance inteligentní smlouvy.

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

Pomocí [rozhraní API GET APLIKACE](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/workflowsget) můžete vypsat všechny pracovní postupy zadané blockchainové aplikace, ke které má uživatel přístup v Blockchain Workbench. Každá blockchainová aplikace má jeden nebo více pracovních postupů a každý pracovní postup má nula nebo více instancí chytrých kontraktů. Pro klientskou aplikaci blockchain, která má pouze jeden pracovní postup, doporučujeme vynechat tok uživatelského prostředí, který uživatelům umožňuje vybrat příslušný pracovní postup.

### <a name="application-workflows-request"></a>Požadavek na pracovní postupy aplikace

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

### <a name="application-workflows-response"></a>Odpověď pracovních postupů aplikace

Správci Blockchain Workbench získají každý pracovní postup blockchainu. Správci mimo workbench získat všechny pracovní postupy, pro které mají alespoň jednu roli přidružené aplikace nebo je spojena s rolí instance inteligentní smlouvy.

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

## <a name="create-a-contract-instance"></a>Vytvoření instance smlouvy

Pomocí [rozhraní Contracts V2 POST API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contractsv2/contractpost) vytvořte novou instanci inteligentní smlouvy pro pracovní postup. Uživatelé mohou vytvořit novou instanci inteligentní smlouvy pouze v případě, že je uživatel přidružen k roli aplikace, která může iniciovat instanci inteligentní smlouvy pro pracovní postup.

> [!NOTE]
> V tomto příkladu se používá verze 2 rozhraní API. Kontraktová úložiště kontraktu verze 2 poskytují podrobnější informace pro přidružená pole ProvisioningStatus.

### <a name="contracts-post-request"></a>Požadavek NA SMLOUVY POST

Nahraďte následující hodnoty:

| Parametr | Hodnota |
|-----------|-------|
| {workflowId} | Hodnota ID pracovního postupu je ConstructorID smlouvy z [tabulky Pracovní postup](data-sql-management-studio.md). |
| {contractCodeId} | Hodnota ID kódu smlouvy z [tabulky ContractCode](data-sql-management-studio.md). Korelujte ID aplikace a ID hlavní knihy pro instanci smlouvy, kterou chcete vytvořit. |
| {connectionId} | Hodnota ID připojení z [tabulky Připojení](data-sql-management-studio.md). |

Pro tělo požadavku nastavte hodnoty pomocí následujících informací:

| Parametr | Hodnota |
|-----------|-------|
| workflowFunctionID | ID z [tabulky WorkflowFunction](data-sql-management-studio.md). |
| workflowActionParameters | Dvojice hodnot název parametrů předaných konstruktoru. Pro každý parametr použijte hodnotu workflowFunctionParameterID z tabulky [WorkflowFunctionParameter.](data-sql-management-studio.md) |

``` http
POST /api/v2/contracts?workflowId={workflowId}&contractCodeId={contractCodeId}&connectionId={connectionId}
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "workflowFunctionID": 2,
  "workflowActionParameters": [
    {
      "name": "message",
      "value": "Hello, world!",
      "workflowFunctionParameterId": 3
    }
  ]
}
```

### <a name="contracts-post-response"></a>Reakce smlouvy POST

Pokud je úspěšná, vrátí rozhraní API přiřazení rolí hodnotu ContractActionID z [tabulky ContractActionParameter](data-sql-management-studio.md).

``` http
HTTP/1.1 200 OK
4
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>Výpis instancí chytrých kontraktů pro pracovní postup

Pomocí [rozhraní CONTRACTS GET API](/rest/api/azure-blockchain-workbench/contractsv2/contractsget) zobrazte všechny instance inteligentních kontraktů pro pracovní postup. Nebo můžete uživatelům umožnit, aby se hlouběji ponořili do některé z zobrazených inteligentních smluvních instancí.

### <a name="contracts-request"></a>Žádost o smlouvy

V tomto příkladu předpokládejme, že by uživatel chtěl komunikovat s některou z instancí chytrých kontraktů kvůli provedení akce.

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

### <a name="contracts-response"></a>Odpověď na smlouvy

Odpověď obsahuje seznam všech inteligentních instancí kontraktu zadaného pracovního postupu. Správci pracovního stolu získají všechny instance inteligentních smluv. Správci mimo workbench získat každou instanci inteligentní smlouvy, pro které mají alespoň jednu roli přidružené aplikace nebo je spojena s rolí instance inteligentní smlouvy.

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

Pomocí [rozhraní API GET akce smlouvy](/rest/api/azure-blockchain-workbench/contractsv2/contractactionget) zobrazte dostupné akce uživatele vzhledem ke stavu smlouvy. 

### <a name="contract-action-request"></a>Žádost o akci smlouvy

V tomto příkladu se uživatel dívá na všechny dostupné akce pro novou inteligentní smlouvu, kterou vytvořili.

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

### <a name="contract-action-response"></a>Odpověď na akci smlouvy

Vrátí seznam všech akcí, které může uživatel provést za aktuálního stavu zadané instance chytrého kontraktu.

* Modify: Umožňuje uživateli upravit popis a cenu majetku.
* Ukončit: Umožňuje uživateli ukončit smlouvu datového zdroje.

Uživatelé získají všechny použitelné akce, pokud má uživatel přidruženou roli aplikace nebo má přidruženou roli instance chytrého kontraktu pro aktuální stav zadané instance chytrého kontraktu.

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

Pomocí [rozhraní API akce smlouvy POST](/rest/api/azure-blockchain-workbench/contractsv2/contractactionpost) můžete provést akci pro zadanou instanci inteligentní smlouvy.

### <a name="contract-action-post-request"></a>Požadavek na akci smlouvy POST

V takovém případě zvažte scénář, kdy by uživatel chtěl změnit popis a cenu majetku.

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

Uživatelé mohou provést pouze akci dostupnou pro aktuální stav zadané instance chytrého kontraktu a pro jim přidruženou roli aplikace nebo přidruženou roli instance chytrého kontraktu.

### <a name="contract-action-post-response"></a>Odpověď AKCE Smlouvy POST

Pokud je požadavek úspěšný, vrátí se odpověď HTTP 200 OK bez dalších informací.

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>Další kroky

Referenční informace o rozhraních API blockchainworkbench najdete v [tématu Azure Blockchain Workbench REST REFERENCE](https://docs.microsoft.com/rest/api/azure-blockchain-workbench).
