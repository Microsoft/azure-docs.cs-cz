---
title: Použití rozhraní REST API služby Azure blockchain Workbench
description: Scénáře použití REST API Azure blockchain Workbench Preview
ms.date: 03/05/2020
ms.topic: how-to
ms.reviewer: brendal
ms.openlocfilehash: 696f1f2f96034f7a044f6a39182774c02804518f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004836"
---
# <a name="using-the-azure-blockchain-workbench-preview-rest-api"></a>Používání Azure blockchain Workbench Preview REST API

Azure blockchain Workbench Preview REST API poskytuje vývojářům a informačním pracovníkům způsob, jak vytvářet bohatou integraci aplikací blockchain. Tento článek popisuje několik scénářů použití REST API Workbench. Předpokládejme například, že chcete vytvořit vlastního klienta blockchain, který umožňuje přihlášeným uživatelům zobrazení a interakci s přiřazenými aplikacemi blockchain. Klient může použít rozhraní API blockchain Workbench k zobrazení instancí kontraktů a provedení akcí v rámci inteligentních smluv.

## <a name="blockchain-workbench-api-endpoint"></a>Koncový bod rozhraní API pro blockchain Workbench

K rozhraním API aplikace blockchain Workbench se dostanete prostřednictvím koncového bodu pro vaše nasazení. Pro získání adresy URL koncového bodu rozhraní API pro vaše nasazení:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V levém navigačním podokně vyberte **skupiny prostředků**.
1. Vyberte název skupiny prostředků, kterou jste nasadili blockchain Workbench.
1. Vyberte záhlaví sloupce **typ** k seřazení seznamu abecedně podle typu.
1. Existují dva prostředky s typem **App Service**. Vyberte prostředek typu **App Service** *s* příponou "-API".
1. V **přehledu** App Service Zkopírujte hodnotu **URL** , která představuje adresu URL koncového bodu rozhraní API pro nasazenou aplikaci blockchain Workbench.

    ![Adresa URL koncového bodu rozhraní API služby App Service](media/use-api/app-service-api.png)

## <a name="authentication"></a>Authentication

Požadavky na REST API Workbench blockchain jsou chráněné pomocí Azure Active Directory (Azure AD).

Aby bylo možné provést ověřený požadavek na rozhraní REST API, kód klienta vyžaduje ověření pomocí platných přihlašovacích údajů před voláním rozhraní API. Ověřování je sladěné mezi různými aktéry Azure AD a poskytuje vašemu klientovi [přístupový token](../../active-directory/develop/developer-glossary.md#access-token) jako ověření platnosti. Token se pak pošle v hlavičce autorizace HTTP REST API požadavků. Další informace o ověřování Azure AD najdete v tématu [Azure Active Directory pro vývojáře](../../active-directory/develop/index.yml).

Příklady ověřování najdete v tématu [ukázky REST API](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples) .

## <a name="using-postman"></a>S využitím Postmana

Pokud chcete testovat nebo experimentovat s rozhraními API aplikace Workbench, můžete použít [metodu post](https://www.postman.com) k zajištění volání rozhraní API pro vaše nasazení. [Stáhněte si ukázku post z kolekce požadavky rozhraní API Workbench](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples/postman) z GitHubu. Podrobnosti o ověřování a používání ukázkových požadavků rozhraní API najdete v souboru READme.

## <a name="create-an-application"></a>Vytvoření aplikace

K vytvoření aplikace blockchain Workbench použijete dvě volání rozhraní API. Tuto metodu můžou provádět jenom uživatelé, kteří jsou správci aplikace Workbench.

Pro nahrání souboru JSON aplikace a získání ID aplikace použijte [rozhraní API](/rest/api/azure-blockchain-workbench/applications/applicationspost) pro odeslání aplikace.

### <a name="applications-post-request"></a>Žádosti po odeslání

Pomocí parametru **appFile** můžete konfigurační soubor odeslat jako součást textu žádosti.

``` http
POST /api/v1/applications
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="appFile"; filename="/C:/smart-contract-samples/HelloWorld.json"
Content-Type: application/json
```

### <a name="applications-post-response"></a>Aplikace po odpovědi

V odpovědi se vrátí vytvořené ID aplikace. K přidružení konfiguračního souboru k souboru s kódem při volání dalšího rozhraní API potřebujete ID aplikace.

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
1
```

### <a name="contract-code-post-request"></a>Požadavek POST kódu kontraktu

Použijte [rozhraní API pro kód kontraktu](/rest/api/azure-blockchain-workbench/applications/contractcodepost) , a to předáním ID aplikace a nahráním souboru kódu pro záhustotu aplikace. Datovou částí může být jeden soubor s pevnou hustotou nebo soubor. zip obsahující soubory.

Nahraďte následující hodnoty:

| Parametr | Hodnota |
|-----------|-------|
| ApplicationId | Návratová hodnota z rozhraní API pro odeslání aplikace |
| {ledgerId} | Index hlavní knihy Hodnota je obvykle 1. Můžete také v [tabulce hlavní knihy](data-sql-management-studio.md) vyhledat hodnotu. |

``` http
POST /api/v1/applications/{applicationId}/contractCode?ledgerId={ledgerId}
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="contractFile"; filename="/C:/smart-contract-samples/HelloWorld.sol"
```

### <a name="contract-code-post-response"></a>Odpověď po odeslání kódu kontraktu

V případě úspěchu odpověď obsahuje ID vytvořeného kódu kontraktu z [tabulky ContractCode](data-sql-management-studio.md).

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
2
```

## <a name="assign-roles-to-users"></a>Přiřazení rolí uživatelům

Pomocí [přiřazení rolí aplikace předejte rozhraní API](/rest/api/azure-blockchain-workbench/applications/roleassignmentspost) předáním ID aplikace, ID uživatele a ID aplikační role a vytvořte tak mapování uživatele na role v zadané aplikaci blockchain. Tuto metodu můžou provádět jenom uživatelé, kteří jsou správci aplikace Workbench.

### <a name="role-assignments-post-request"></a>Přiřazení rolí – požadavek po odeslání

Nahraďte následující hodnoty:

| Parametr | Hodnota |
|-----------|-------|
| ApplicationId | Návratová hodnota z rozhraní API pro odeslání aplikace |
| UserID | Hodnota ID uživatele z [tabulky uživatelů](data-sql-management-studio.md) |
| {applicationRoleId} | Hodnota ID aplikační role přidružená k ID aplikace z [tabulky ApplicationRole](data-sql-management-studio.md) |

``` http
POST /api/v1/applications/{applicationId}/roleAssignments
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "userId": {userId},
  "applicationRoleId": {applicationRoleId}
}
```

### <a name="role-assignments-post-response"></a>Přiřazení rolí po odpovědi

V případě úspěchu bude odpověď zahrnovat ID vytvořeného přiřazení role z [tabulky RoleAssignment](data-sql-management-studio.md).

``` http
HTTP/1.1 200
1
```

## <a name="list-applications"></a>Výpis aplikací

K načtení všech aplikací blockchain Workbench pro uživatele použijte [aplikace získat rozhraní API](/rest/api/azure-blockchain-workbench/applications/applicationsget) . V tomto příkladu má přihlášený uživatel přístup ke dvěma aplikacím:

- [Přenos prostředků](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
- [Chladicí přeprava](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

### <a name="applications-get-request"></a>Žádosti o získání

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

### <a name="applications-get-response"></a>Aplikace obdrží odpověď.

V odpovědi se zobrazí všechny aplikace blockchain, ke kterým má uživatel přístup v blockchain Workbench. Správci blockchain Workbench získají každou aplikaci blockchain. Správci mimo aplikaci Workbench získají všechny aplikace blockchain, pro které mají alespoň jednu přidruženou aplikační roli nebo přidruženou roli instance inteligentního kontraktu.

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

Použijte [pracovní postupy aplikací získat rozhraní API](/rest/api/azure-blockchain-workbench/applications/workflowsget) k vypsání všech pracovních postupů zadané aplikace blockchain, ke kterým má uživatel přístup v blockchain Workbench. Každá blockchainová aplikace má jeden nebo více pracovních postupů a každý pracovní postup má nula nebo více instancí chytrých kontraktů. Pro klientskou aplikaci blockchain, která má jenom jeden pracovní postup, doporučujeme přeskočit tok uživatelského prostředí, který uživatelům umožní vybrat příslušný pracovní postup.

### <a name="application-workflows-request"></a>Žádost o pracovní postupy aplikace

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

### <a name="application-workflows-response"></a>Odpověď pracovních postupů aplikace

Správci blockchain Workbench získají každý pracovní postup blockchain. Správci mimo aplikaci Workbench získají všechny pracovní postupy, pro které mají alespoň jednu přidruženou roli aplikace nebo která je přidružena k roli instance inteligentního kontraktu.

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

## <a name="create-a-contract-instance"></a>Vytvoření instance kontraktu

K vytvoření nové instance inteligentních kontraktů pro pracovní postup použijte [kontrakty verze V2 post API](/rest/api/azure-blockchain-workbench/contractsv2/contractpost) . Pokud je uživatel přidružený k roli aplikace, může vytvořit pouze novou instanci inteligentního kontraktu, pokud je uživatel přidružen k aplikační roli, která může iniciovat instanci inteligentního kontraktu pro pracovní postup.

> [!NOTE]
> V tomto příkladu se používá verze 2 rozhraní API. Rozhraní API kontraktu verze 2 poskytují pro přidružená pole ProvisioningStatus větší členitost.

### <a name="contracts-post-request"></a>Kontrakt po požadavku

Nahraďte následující hodnoty:

| Parametr | Hodnota |
|-----------|-------|
| WorkflowId | Hodnota ID pracovního postupu je ConstructorID kontraktu z [tabulky pracovního postupu](data-sql-management-studio.md). |
| {contractCodeId} | Hodnota ID kódu kontraktu z [tabulky ContractCode](data-sql-management-studio.md) Proveďte korelaci ID aplikace a ID hlavní knihy pro instanci kontraktu, kterou chcete vytvořit. |
| ConnectionID | Hodnota ID připojení z [tabulky připojení](data-sql-management-studio.md). |

U textu žádosti nastavte hodnoty pomocí následujících informací:

| Parametr | Hodnota |
|-----------|-------|
| workflowFunctionID | ID z [tabulky WorkflowFunction](data-sql-management-studio.md) |
| workflowActionParameters | Páry název hodnoty dvojic parametrů předaných konstruktoru. Pro každý parametr použijte hodnotu workflowFunctionParameterID z tabulky [WorkflowFunctionParameter](data-sql-management-studio.md) . |

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

### <a name="contracts-post-response"></a>Kontrakt po odpovědi

V případě úspěchu vrátí rozhraní API přiřazení rolí ContractActionID z [tabulky ContractActionParameter](data-sql-management-studio.md).

``` http
HTTP/1.1 200 OK
4
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>Výpis instancí chytrých kontraktů pro pracovní postup

Pomocí [kontraktů získat rozhraní API](/rest/api/azure-blockchain-workbench/contractsv2/contractsget) můžete zobrazit všechny instance inteligentních kontraktů pro pracovní postup. Nebo můžete uživatelům dovolit hluboko podrobně do kterékoli ze zobrazených instancí inteligentních kontraktů.

### <a name="contracts-request"></a>Žádost o smlouvy

V tomto příkladu předpokládejme, že by uživatel chtěl komunikovat s některou z instancí chytrých kontraktů kvůli provedení akce.

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

### <a name="contracts-response"></a>Odpověď smluv

Odpověď obsahuje seznam všech instancí inteligentních kontraktů určeného pracovního postupu. Správci aplikace Workbench získají všechny instance inteligentních kontraktů. Správci mimo Workbench získají každou instanci inteligentních kontraktů, pro kterou mají alespoň jednu přidruženou aplikační roli nebo přidruženou k roli instance inteligentního kontraktu.

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

Pomocí [akce získat rozhraní API](/rest/api/azure-blockchain-workbench/contractsv2/contractactionget) pro zobrazení dostupných uživatelských akcí podle stavu smlouvy. 

### <a name="contract-action-request"></a>Žádost o akci smlouvy

V tomto příkladu si uživatel prohlíží všechny dostupné akce pro novou vytvářenou vydanou smlouvu.

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

### <a name="contract-action-response"></a>Odezva akce kontraktu

Vrátí seznam všech akcí, které může uživatel provést za aktuálního stavu zadané instance chytrého kontraktu.

* Modify: Umožňuje uživateli upravit popis a cenu majetku.
* Ukončit: umožňuje uživateli ukončit kontrakt assetu.

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

K provedení akce pro zadanou instanci inteligentního kontraktu použijte [akci post API pro akci kontraktu](/rest/api/azure-blockchain-workbench/contractsv2/contractactionpost) .

### <a name="contract-action-post-request"></a>Požadavek POST akce smlouvy

V takovém případě Zvažte situaci, kdy uživatel chce změnit popis a cenu assetu.

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

### <a name="contract-action-post-response"></a>Odpověď po odeslání akce smlouvy

Pokud je požadavek úspěšný, vrátí se odpověď HTTP 200 OK bez dalších informací.

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>Další kroky

Referenční informace o rozhraních API blockchain Workbench najdete v [referenčních informacích k Azure blockchain workbench REST API](/rest/api/azure-blockchain-workbench).
