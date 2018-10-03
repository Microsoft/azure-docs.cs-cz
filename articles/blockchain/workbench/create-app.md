---
title: Vytvoření blockchainové aplikace v Azure Blockchain Workbench
description: Postup vytvoření blockchainové aplikace v Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/1/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: a7ca3f42874bc844bc0036e37a790ffebdc5f8d8
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48242403"
---
# <a name="create-a-blockchain-application-in-azure-blockchain-workbench"></a>Vytvoření blockchainové aplikace v Azure Blockchain Workbench

Azure Blockchain Workbench můžete použít k vytvoření blockchainové aplikace, které představují více stran pracovní postupy definované konfigurace a inteligentní kontraktu kódu.

Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Konfigurace blockchainové aplikace
> * Vytvořte soubor inteligentní kontraktu kódu
> * Přidání aplikace blockchain k Blockchain Workbench
> * Přidání členů do blockchainové aplikace

## <a name="prerequisites"></a>Požadavky

* Blockchain Workbench umožňuje nasazení. Další informace najdete v tématu [Azure Blockchain Workbench umožňuje nasazení](deploy.md) podrobnosti o nasazení.
* Uživatelé Azure Active Directory v tenantovi přidružené Blockchain Workbench Další informace najdete v tématu [přidat uživatele Azure AD v aplikaci Azure Blockchain Workbench](manage-users.md#add-azure-ad-users).
* Účet správce Blockchain Workbench. Další informace najdete v tématu Přidání [Blockchain Workbench umožňuje správci v aplikaci Azure Blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).

## <a name="hello-blockchain"></a>Dobrý den, Blockchain!

Vytvořme základní aplikace 00Z žadatele odešle žádost a respondér odeslání odpovědi na požadavek. Například může být žádost, "Ahoj, jak se máte?" a odpovědi může být "Jsem skvělé!". Žádost i odpověď se zaznamenávají v podkladové blockchainu. 

Postupujte podle kroků k vytvoření těchto souborů aplikace nebo můžete [Stáhnout ukázku z Githubu](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/hello-blockchain). 

## <a name="configuration-file"></a>Konfigurační soubor

Konfigurace metadat definuje vysoké úrovně pracovních postupů a interakce model blockchainové aplikace. Konfigurace metadat představuje fáze pracovního postupu a interakce model blockchainové aplikace.

1. Ve svém oblíbeném editoru, vytvořte soubor s názvem `HelloBlockchain.json`.
2. Přidejte následující kód JSON pro definování konfigurace blockchainové aplikace.

    ``` json
    {
      "ApplicationName": "HelloBlockchain",
      "DisplayName": "Hello, Blockchain!",
      "Description": "A simple application to send request and get response",
      "ApplicationRoles": [
        {
          "Name": "Requestor",
          "Description": "A person sending a request."
        },
        {
          "Name": "Responder",
          "Description": "A person responding to a request"
        }
      ],
      "Workflows": [
        {
          "Name": "HelloBlockchain",
          "DisplayName": "Request Response",
          "Description": "A simple workflow to send a request and receive a response.",
          "Initiators": [ "Requestor" ],
          "StartState": "Request",
          "Properties": [
            {
              "Name": "State",
              "DisplayName": "State",
              "Description": "Holds the state of the contract.",
              "Type": {
                "Name": "state"
              }
            },
            {
              "Name": "Requestor",
              "DisplayName": "Requestor",
              "Description": "A person sending a request.",
              "Type": {
                "Name": "Requestor"
              }
            },
            {
              "Name": "Responder",
              "DisplayName": "Responder",
              "Description": "A person sending a response.",
              "Type": {
                "Name": "Responder"
              }
            },
            {
              "Name": "RequestMessage",
              "DisplayName": "Request Message",
              "Description": "A request message.",
              "Type": {
                "Name": "string"
              }
            },
            {
              "Name": "ResponseMessage",
              "DisplayName": "Response Message",
              "Description": "A response message.",
              "Type": {
                "Name": "string"
              }
            }
          ],
          "Constructor": {
            "Parameters": [
              {
                "Name": "message",
                "Description": "...",
                "DisplayName": "Request Message",
                "Type": {
                  "Name": "string"
                }
              }
            ]
          },
          "Functions": [
            {
              "Name": "SendRequest",
              "DisplayName": "Request",
              "Description": "...",
              "Parameters": [
                {
                  "Name": "requestMessage",
                  "Description": "...",
                  "DisplayName": "Request Message",
                  "Type": {
                    "Name": "string"
                  }
                }
              ]
            },
            {
              "Name": "SendResponse",
              "DisplayName": "Response",
              "Description": "...",
              "Parameters": [
                {
                  "Name": "responseMessage",
                  "Description": "...",
                  "DisplayName": "Response Message",
                  "Type": {
                    "Name": "string"
                  }
                }
              ]
            }
          ],
          "States": [
            {
              "Name": "Request",
              "DisplayName": "Request",
              "Description": "...",
              "PercentComplete": 50,
              "Value": 0,
              "Style": "Success",
              "Transitions": [
                {
                  "AllowedRoles": ["Responder"],
                  "AllowedInstanceRoles": [],
                  "Description": "...",
                  "Function": "SendResponse",
                  "NextStates": [ "Respond" ],
                  "DisplayName": "Send Response"
                }
              ]
            },
            {
              "Name": "Respond",
              "DisplayName": "Respond",
              "Description": "...",
              "PercentComplete": 90,
              "Value": 1,
              "Style": "Success",
              "Transitions": [
                {
                  "AllowedRoles": [],
                  "AllowedInstanceRoles": ["Requestor"],
                  "Description": "...",
                  "Function": "SendRequest",
                  "NextStates": [ "Request" ],
                  "DisplayName": "Send Request"
                }
              ]
            }
          ]
        }
      ]
    }
    ```

3. Uložte soubor `HelloBlockchain.json`.

Konfigurační soubor obsahuje několik oddílů. Podrobnosti o každé části jsou následující:

### <a name="application-metadata"></a>Metadata aplikace

Začátek konfigurační soubor obsahuje informace o aplikaci, včetně názvu aplikace a popis.

### <a name="application-roles"></a>Aplikační role

V části role aplikace definuje rolí uživatelů, kteří můžou fungovat či k účasti v rámci blockchainové aplikace. Definujete sadu jedinečných rolí závislosti na funkcích. Ve scénáři reakce na žádosti je rozdíl mezi funkcí žadatele o jako entita, která vytváří požadavky a respondér jako entita, která vytváří odpovědi.

### <a name="workflows"></a>Pracovní postupy

Pracovní postupy definují jeden nebo více fází a akce kontraktu. Ve scénáři reakce na žádosti první fázi (stav) pracovního postupu je že žadatel (role) provede akci (Přechod) k odeslání požadavku (funkce). Další fáze (stav) je že respondér (role) provede akci (Přechod) k odeslání odpovědi (funkce). Pracovní postup aplikace může zahrnovat funkce, vlastnosti a požadované stavy popisovat tok kontraktu. 

Další informace o obsahu konfiguračních souborů naleznete v tématu [referenční informace o konfiguraci pracovního postupu Azure Blockchain](configuration.md).

## <a name="smart-contract-code-file"></a>Soubor kódu inteligentní kontraktu

Chytrých kontraktů představují obchodní logiky blockchainové aplikace. Blockchain Workbench v současné době podporuje Etherea pro blockchain účetní knihy. Používá Etherea [Solidity](https://solidity.readthedocs.io) jako programovací jazyk pro zápis svým vynucení obchodní logiku pro chytrých kontraktů.

Chytrých kontraktů v Solidity jsou podobné třídy v jazyce objektově orientovaný. Každá smlouva obsahuje stav a funkce pro implementaci fází a akce inteligentní kontraktu.

Ve svém oblíbeném editoru, vytvořte soubor s názvem `HelloBlockchain.sol`.

### <a name="version-pragma"></a>Verze – Direktiva pragma

Jako osvědčený postup označuje verzi Solidity cílíte. Určení verze pomáhá zabránit nekompatibilitu s budoucí verze hustoty. 

Přidejte následující direktivy pragma verze v horní části `HelloBlockchain.sol` inteligentní smlouvy soubor kódu.


  ``` solidity
  pragma solidity ^0.4.20;
  ```

### <a name="base-class"></a>Základní třída

**WorkbenchBase** základní třída umožňuje Blockchain Workbench a jak vytvářet a aktualizovat kontrakt. Základní třída je povinné pro Blockchain Workbench konkrétních smart kontraktu kódu. Kontrakt musí dědit z **WorkbenchBase** základní třídy.

V `HelloBlockchain.sol` inteligentní smlouvy soubor kódu, přidejte **WorkbenchBase** třídy na začátku souboru. 

```
contract WorkbenchBase {
    event WorkbenchContractCreated(string applicationName, string workflowName, address originatingAddress);
    event WorkbenchContractUpdated(string applicationName, string workflowName, string action, address originatingAddress);

    string internal ApplicationName;
    string internal WorkflowName;

    function WorkbenchBase(string applicationName, string workflowName) internal {
        ApplicationName = applicationName;
        WorkflowName = workflowName;
    }

    function ContractCreated() internal {
        WorkbenchContractCreated(ApplicationName, WorkflowName, msg.sender);
    }

    function ContractUpdated(string action) internal {
        WorkbenchContractUpdated(ApplicationName, WorkflowName, action, msg.sender);
    }
}
```
Základní třída zahrnuje dvě důležité funkce:

|Funkci základní třídy  | Účel  | Kdy se má volat  |
|---------|---------|---------|
| ContractCreated() | Upozorní byl vytvořen kontrakt Blockchain Workbench | Před ukončením konstruktoru kontraktu |
| ContractUpdated() | Upozorní Blockchain Workbench se aktualizoval stav smlouvy | Před ukončením funkce kontraktu |

### <a name="configuration-and-smart-contract-code-relationship"></a>Konfigurace a vztah inteligentní kontraktu kódu

K vytvoření blockchainové aplikace používá Blockchain Workbench konfigurační soubor a soubor kódu inteligentní kontraktu. Existuje vztah mezi definovaná v konfiguraci a kód v inteligentní kontraktu. Podrobnosti o kontraktu, funkce, parametry a typy musí odpovídat k vytvoření aplikace. Blockchain Workbench programu ověří soubory před vytvářením aplikací. 

### <a name="contract"></a>Kontrakt

Blockchain Workbench, třeba dědit z kontraktů **WorkbenchBase** základní třídy. Při deklarování smlouvy, musíte předat název aplikace a název pracovního postupu jako argumenty.

Přidat **kontraktu** záhlaví vaše `HelloBlockchain.sol` inteligentní smlouvy soubor kódu. 

```
contract HelloBlockchain is WorkbenchBase('HelloBlockchain', 'HelloBlockchain') {
```

Kontrakt musí dědit z **WorkbenchBase** základní třídy a předat parametry **ApplicationName** a pracovní postup **název** definované v konfiguraci soubor. V tomto případě název aplikace a název pracovního postupu jsou stejné.

### <a name="state-variables"></a>Stav proměnné

Proměnné stavu ukládání hodnot stavu pro každou instanci kontraktu. Proměnné stavu ve vaší smlouvě musí odpovídat vlastnosti pracovního postupu, která je definována v konfiguračním souboru.

Přidejte do vaší smlouvy v proměnné stavu vašich `HelloBlockchain.sol` inteligentní smlouvy soubor kódu. 

```
    //Set of States
    enum StateType { Request, Respond}
    
    //List of properties
    StateType public  State;
    address public  Requestor;
    address public  Responder;
    
    string public RequestMessage;
    string public ResponseMessage;
```

### <a name="constructor"></a>Konstruktor

Konstruktor definuje vstupní parametry pro novou inteligentní smlouvy instanci pracovního postupu. Konstruktor je deklarovaný jako funkce se stejným názvem jako kontrakt. Požadované parametry pro konstruktor se definují jako parametry konstruktoru v konfiguračním souboru. Počet, pořadí a typ parametrů musí odpovídat na oba soubory.

Napište veškeré obchodní logiky v funkce konstruktoru, že který chcete provést před vytvořením smlouvy. Například inicializujte proměnné stavu s počáteční hodnoty.

Před ukončením funkce konstruktoru, zavolejte `ContractCreated()` funkce. Tato funkce upozorní Blockchain Workbench byl vytvořen kontrakt.

Přidání funkce konstruktoru do vaší smlouvy v vaše `HelloBlockchain.sol` inteligentní smlouvy soubor kódu. 

```
    // constructor function
    function HelloBlockchain(string message) public
    {
        Requestor = msg.sender;
        RequestMessage = message;
        State = StateType.Request;
    
        // call ContractCreated() to create an instance of this workflow
        ContractCreated();
    }
```

### <a name="functions"></a>Functions

Funkce jsou jednotky spustitelného souboru obchodní logiky v rámci smlouvy. Požadované parametry pro funkci jsou definovány jako parametry funkce v konfiguračním souboru. Počet, pořadí a typ parametrů musí odpovídat na oba soubory. Funkce jsou přidružené k přechodů v pracovním postupu Blockchain Workbench v konfiguračním souboru. Přechod je akce provést, přejděte do další fáze aplikace pracovního postupu, jak určuje smlouva.

Zapisovat všechny obchodní logiku, že kterou chcete provést ve funkci. Například změně hodnoty proměnné stavu.

Před ukončením funkce, zavolejte `ContractUpdated()` funkce. Funkce upozorní Blockchain Workbench se aktualizoval stav smlouvy. Pokud chcete vrátit zpět stav změny ve funkci, zavolejte revert(). Vrátit zpět změny provedené od posledního volání ContractUpdated() stavu zahození.

1. Přidejte následující funkce do vaší smlouvy vaší `HelloBlockchain.sol` inteligentní smlouvy soubor kódu. 

    ```
        // call this function to send a request
        function SendRequest(string requestMessage) public
        {
            if (Requestor != msg.sender)
            {
                revert();
            }
    
            RequestMessage = requestMessage;
            State = StateType.Request;
    
            // call ContractUpdated() to record this action
            ContractUpdated('SendRequest');
        }
    
        // call this function to send a response
        function SendResponse(string responseMessage) public
        {
            Responder = msg.sender;
    
            // call ContractUpdated() to record this action
            ResponseMessage = responseMessage;
            State = StateType.Respond;
            ContractUpdated('SendResponse');
        }
    }
    ```

2. Uložit vaše `HelloBlockchain.sol` inteligentní smlouvy soubor kódu.

## <a name="add-blockchain-application-to-blockchain-workbench"></a>Přidat aplikaci blockchain Blockchain Workbench

Přidání aplikace blockchain Blockchain Workbench, nahrajete, konfigurace a soubory inteligentního smlouvy k definování aplikace.

1. Ve webovém prohlížeči přejděte na webovou adresu Blockchain Workbench. Například `https://{workbench URL}.azurewebsites.net/` webové aplikace se vytvoří při nasazení Blockchain Workbench. Informace o tom, jak najít webovou adresu Blockchain Workbench najdete v tématu [adresy URL webového Blockchain Workbench](deploy.md#blockchain-workbench-web-url)
2. Přihlaste se jako [Blockchain Workbench správce](manage-users.md#manage-blockchain-workbench-administrators).
3. Vyberte **aplikací** > **nové**. **Novou aplikaci** zobrazí podokno.
4. Vyberte **nahrát konfiguraci smlouvy** > **Procházet** vyhledejte **HelloBlockchain.json** konfiguračního souboru, které jste vytvořili. Konfigurační soubor se automaticky ověří. Vyberte **zobrazit** odkaz k zobrazení chyb ověřování. Před nasazením aplikace, opravte chyby ověřování.
5. Vyberte **nahrajte kód kontraktu** > **Procházet** vyhledejte **HelloBlockchain.sol** inteligentní smlouvy soubor kódu. Soubor kódu se automaticky ověří. Vyberte **zobrazit** odkaz k zobrazení chyb ověřování. Před nasazením aplikace, opravte chyby ověřování.
6. Vyberte **nasadit** k vytvoření blockchainové aplikace závislosti na konfiguraci a soubory inteligentního kontraktu.

Nasazení blockchainové aplikace trvá několik minut. Až se nasazení dokončí, zobrazí se nová aplikace v **aplikací**. 

> [!NOTE]
> Můžete také vytvořit blockchainové aplikace s použitím [rozhraní REST API služby Azure Blockchain Workbench](https://docs.microsoft.com/rest/api/azure-blockchain-workbench). 

## <a name="add-blockchain-application-members"></a>Přidat členy blockchainové aplikace

Přidáte členy aplikace do vaší aplikace k zahájení a provádět s nimi akce smluv. Pokud chcete přidat členy aplikace, musíte být [Blockchain Workbench správce](manage-users.md#manage-blockchain-workbench-administrators).

1. Vyberte **aplikací** > **Hello, Blockchain!**.
2. Počet členů, které jsou přidružené k aplikaci se zobrazí v pravém horním rohu stránky. Počet členů pro novou aplikaci, bude nula.
3. Vyberte **členy** odkaz v pravém horním rohu stránky. Zobrazí se aktuální seznam členů pro aplikaci.
4. Vyberte v seznamu členství **přidat členy**.
5. Vyberte nebo zadejte název člena, který chcete přidat. Pouze uživatelé Azure AD, které existují v tenantovi Blockchain Workbench patří. Pokud uživatel není nalezen, je potřeba [přidat uživatele Azure AD](manage-users.md#add-azure-ad-users).
6. Vyberte **Role** pro člena. První člen vyberte **žadatele** roli.
7. Vyberte **přidat** se přidat člena role přidružené k aplikaci.
8. Přidat jiného člena pro aplikaci **respondér** role.

Další informace o správě uživatelů v Blockchain Workbench najdete v tématu [Správa uživatelů v Azure Blockchain Workbench](manage-users.md)

## <a name="next-steps"></a>Další postup

V tomto článku s postupy co vytvoříte základní aplikaci v požadavku a odpovědi. Další informace o použití aplikace, pokračujte na další článek.

> [!div class="nextstepaction"]
> [Použití blockchainové aplikace](use.md)
