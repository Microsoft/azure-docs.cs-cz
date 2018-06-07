---
title: Vytvoření aplikace blockchain v Azure Blockchain Workbench
description: Postup vytvoření aplikace blockchain v Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/17/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: a4b704f433f02afcff7b94f98c19a478caaa02b2
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808056"
---
# <a name="create-a-blockchain-application-in-azure-blockchain-workbench"></a>Vytvoření aplikace blockchain v Azure Blockchain Workbench

Azure Blockchain Workbench můžete použít k vytvoření blockchain aplikací, které představují více stran pracovní postupy definované konfigurací a kódem inteligentní kontrakt.

Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Nakonfigurovat aplikaci blockchain
> * Vytvořte soubor inteligentní kontrakt kódu
> * Přidání aplikace blockchain k Blockchain Workbench
> * Přidání členů do aplikace blockchain

## <a name="prerequisites"></a>Požadavky

* Nasazení Blockchain Workbench. Další informace najdete v tématu [nasazení Azure Blockchain Workbench](blockchain-workbench-deploy.md) podrobnosti o nasazení.
* Azure Active Directory uživatelů v klientovi, přidružené Blockchain Workbench. Další informace najdete v tématu [přidat uživatele Azure AD v Azure Blockchain Workbench](blockchain-workbench-manage-users.md#add-azure-ad-users).
* Účet správce Blockchain Workbench. Další informace najdete v tématu Přidání [Blockchain Workbench správci v Azure Blockchain Workbench](blockchain-workbench-manage-users.md#manage-blockchain-workbench-administrators).

## <a name="hello-blockchain"></a>Hello, Blockchain!

Umožňuje vytvořit základní aplikaci, ve kterém žadatele o odešle požadavek a respondér odeslání odpovědi na požadavek. Například může být žádost, "Hello, jak jste?" a odpovědi může být, "Jsem skvělé!". Na základní blockchain se zaznamenávají požadavku a odpovědi. 

Postupujte podle kroků pro vytvoření souborů aplikace, nebo můžete [ukázku stáhnout z webu GitHub](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/hello-blockchain). 

## <a name="configuration-file"></a>Konfigurační soubor

Konfigurace metadata definuje vysoké úrovně pracovní postupy a interakce modelu blockchain aplikace. Konfigurace metadata představuje fází pracovního postupu a interakce modelu blockchain aplikace.

1. Ve svém oblíbeném editoru vytvořte soubor s názvem `HelloBlockchain.json`.
2. Přidejte následující kód JSON definice konfigurace blockchain aplikace.

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

V části role aplikace definuje role uživatele, kteří mohou fungovat nebo účast v rámci blockchain aplikace. Můžete definovat sadu odlišné role v závislosti na funkcích. Ve scénáři, požadavků a odpovědí není rozdíl mezi funkce žadatele o jako entita, která vytváří požadavky a respondér jako entita, která vytváří odpovědi.

### <a name="workflows"></a>Pracovní postupy

Pracovní postupy definují jeden nebo více fází a akce kontraktu. Ve scénáři, požadavků a odpovědí první fázi (stav) pracovního postupu je, že žadatel (role) provede akci (Přechod) k odeslání požadavku (funkce). Další fáze (stav) je, že provede akci (Přechod) odeslání odpovědi (funkce) respondér (role). Pracovní postup aplikace může zahrnovat vlastnosti, funkce, a popisují požadované stavy toku kontraktu. 

Další informace o obsahu konfiguračních souborů najdete v tématu [Azure Blockchain pracovního postupu konfigurace](blockchain-workbench-configuration-overview.md).

## <a name="smart-contract-code-file"></a>Inteligentní kontrakt souboru kódu

Inteligentní kontrakty představují obchodní logiku aplikace blockchain. V současné době Blockchain Workbench podporuje Ethereum pro hlavní knihy blockchain. Používá Ethereum [hustoty](https://solidity.readthedocs.io) jako programovací jazyk pro zápis samoobslužné vynucení obchodní logiku pro inteligentní smlouvy.

Inteligentní kontrakty hustoty jsou podobná třídy v objektově orientovaný jazyků. Každá smlouva obsahuje stav a funkce pro implementaci fázích a akce inteligentní kontraktu.

Ve svém oblíbeném editoru vytvořte soubor s názvem `HelloBlockchain.sol`.

### <a name="version-pragma"></a>Verze – Direktiva pragma

Jako osvědčený postup znamenat verzi hustoty cílení. Určení verze pomáhá zabránit problémům s kompatibilitou s novějšími verzemi hustoty. 

Přidejte následující – direktiva pragma verze v horní části `HelloBlockchain.sol` souboru kódu inteligentní kontrakt.


  ``` solidity
  pragma solidity ^0.4.20;
  ```

### <a name="base-class"></a>Base – třída

**WorkbenchBase** základní třída umožňuje Blockchain Workbench k vytváření a aktualizaci kontrakt. Základní třída je vyžadována pro kód konkrétní inteligentní smluv Blockchain Workbench. Smlouva musí dědit z **WorkbenchBase** základní třídy.

V `HelloBlockchain.sol` inteligentní souboru kódu kontrakt, přidejte **WorkbenchBase** třídy na začátku souboru. 

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

|Funkce základní třídy  | Účel  | Při volání  |
|---------|---------|---------|
| ContractCreated() | Upozorní Blockchain Workbench vytvořil kontraktu | Před ukončením konstruktoru sady kontraktu |
| ContractUpdated() | Upozorní Blockchain Workbench kontrakt stavu se aktualizovala. | Před ukončením funkce kontraktu |

### <a name="configuration-and-smart-contract-code-relationship"></a>Konfigurace a inteligentní kontrakt kód relace

Blockchain Workbench používá k vytvoření aplikace blockchain konfigurační soubor a soubor kódu inteligentní kontrakt. Existuje vztah mezi co je definována v konfiguraci a kód v inteligentní kontraktu. Podrobnosti smlouvy, funkce, parametry a typy musí odpovídat k vytvoření aplikace. Blockchain Workbench zkontroluje soubory před vytvoření aplikace. 

### <a name="contract"></a>Kontrakt

Pro Blockchain Workbench, třeba kontrakty dědí **WorkbenchBase** základní třídy. Deklarování kontrakt, musíte předat název aplikace a název pracovního postupu jako argumenty.

Přidat **kontrakt** hlavičky k vaší `HelloBlockchain.sol` souboru kódu inteligentní kontrakt. 

```
contract HelloBlockchain is WorkbenchBase('HelloBlockchain', 'HelloBlockchain') {
```

Smlouva musí dědit z **WorkbenchBase** základní třídy a předat parametry **ApplicationName** a pracovní postup **název** definovaným v konfiguraci soubor. V tomto případě název aplikace a její název pracovního postupu jsou stejné.

### <a name="state-variables"></a>Proměnné stavu

Proměnné stavu ukládání hodnot stavu pro každou instanci kontrakt. Proměnné stavu v smlouva musí odpovídat vlastnosti pracovního postupu, která je definována v konfiguračním souboru.

Přidání proměnné stavu vaše smlouvy ve vaší `HelloBlockchain.sol` souboru kódu inteligentní kontrakt. 

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

V konstruktoru definuje vstupní parametry pro novou instanci pracovního postupu inteligentní kontrakt. Konstruktor je deklarován jako funkce se stejným názvem jako kontrakt. Požadované parametry pro konstruktor jsou definovány jako parametry konstruktor v konfiguračním souboru. Počet, pořadí a typ parametry musí odpovídat na oba soubory.

V konstruktoru funkce zápisu veškeré obchodní logiky, že kterou chcete provést před vytvořením kontrakt. Například inicializujte proměnné stavu s počáteční hodnoty.

Před ukončením funkci konstruktoru, volání `ContractCreated()` funkce. Tato funkce upozorní Blockchain Workbench vytvořil kontraktu.

Přidání funkce konstruktor vaše smlouvy v vaší `HelloBlockchain.sol` souboru kódu inteligentní kontrakt. 

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

Funkce jsou spustitelné jednotky obchodní logiky v rámci kontraktu. Požadované parametry pro funkci jsou definovány jako parametry funkce v konfiguračním souboru. Počet, pořadí a typ parametry musí odpovídat na oba soubory. Funkce a jsou přidruženy ke přechody v pracovním postupu Blockchain Workbench v konfiguračním souboru. Přechod je akce provést přesunout do další fáze pracovního postupu aplikace určeného kontrakt.

Zápis veškeré obchodní logiky, že kterou chcete provést ve funkci. Například úpravy hodnotu proměnné stavu.

Před ukončením funkce, volání `ContractUpdated()` funkce. Funkce upozorní Blockchain Workbench kontrakt stavu se aktualizovala. Pokud chcete vrátit zpět změny stavu ve funkci, zavolejte revert(). Vrátit zpět změny provedené od posledního volání ContractUpdated() stavu zahození.

1. Přidejte následující funkce do vaší kontrakt vaší `HelloBlockchain.sol` souboru kódu inteligentní kontrakt. 

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

2. Uložit vaše `HelloBlockchain.sol` souboru kódu inteligentní kontrakt.

## <a name="add-blockchain-application-to-blockchain-workbench"></a>Přidat aplikaci blockchain Blockchain Workbench

Přidat aplikaci blockchain k Blockchain Workbench, nahrajte konfigurace a soubory inteligentního kontrakt k definování aplikace.

1. Ve webovém prohlížeči přejděte na webovou adresu Blockchain Workbench. Například `https://{workbench URL}.azurewebsites.net/` webové aplikace se vytvoří, když nasazujete Blockchain Workbench. Informace o tom, jak najít webovou adresu vaší Blockchain Workbench najdete v tématu [Blockchain Workbench webové adresy URL](blockchain-workbench-deploy.md#blockchain-workbench-web-url)
2. Přihlaste se jako [Blockchain Workbench správce](blockchain-workbench-manage-users.md#manage-blockchain-workbench-administrators).
3. Vyberte **aplikace** > **nové**. **Novou aplikaci** podokně se zobrazí.
4. Vyberte **nahrát konfigurace kontrakt** > **Procházet** najít **HelloBlockchain.json** konfigurační soubor, které jste vytvořili. Konfigurační soubor je automaticky ověřit. Vyberte **zobrazit** odkaz na zobrazení chyb ověřování. Před nasazením aplikace, opravte chyby ověření.
5. Vyberte **nahrát kód kontraktu** > **Procházet** najít **HelloBlockchain.sol** souboru kódu inteligentní kontrakt. K souboru kódu je automaticky ověřit. Vyberte **zobrazit** odkaz na zobrazení chyb ověřování. Před nasazením aplikace, opravte chyby ověření.
6. Vyberte **nasadit** k vytvoření aplikace blockchain na základě konfigurace a soubory inteligentního kontrakt.

Nasazení aplikace blockchain trvá několik minut. Po dokončení nasazení se nová aplikace se zobrazí v **aplikace**. 

> [!NOTE]
> Můžete také vytvořit blockchain aplikace pomocí [REST API služby Azure Blockchain Workbench](https://docs.microsoft.com/rest/api/azure-blockchain-workbench). 

## <a name="add-blockchain-application-members"></a>Přidání členů blockchain aplikace

Přidání členů aplikace do aplikace spustit a provést akce v kontraktech. Chcete-li přidat členy aplikace, musíte být [Blockchain Workbench správce](blockchain-workbench-manage-users.md#manage-blockchain-workbench-administrators).

1. Vyberte **aplikace** > **Hello, Blockchain!**.
2. Počet členů, které jsou přidružené k aplikaci se zobrazí v pravém horním rohu stránky. Pro novou aplikaci bude počet členů nula.
3. Vyberte **členy** odkaz v pravém horním rohu stránky. Zobrazí se aktuální seznam členů pro aplikaci.
4. Vyberte v seznamu členství **přidat členy**.
5. Vyberte nebo zadejte název člena, který chcete přidat. Pouze jsou uvedeny Azure AD Uživatelé, kteří existují v klientovi Blockchain Workbench. Pokud uživatel není nalezen, budete muset [přidat uživatele Azure AD](blockchain-workbench-manage-users.md#add-azure-ad-users).
6. Vyberte **Role** pro člena. První člen, vyberte **žadatele** jako roli.
7. Vyberte **přidat** o přidání člena k roli přidružené k aplikaci.
8. Aplikace s přidat jiného člena **respondér** role.

Další informace o správě uživatelů v Blockchain Workbench najdete v tématu [Správa uživatelů v Azure Blockchain Workbench](blockchain-workbench-manage-users.md)

## <a name="next-steps"></a>Další postup

V tomto článku postupy: vytvoření základní aplikace žádostí a odpovědí. Další informace o použití aplikace, pokračujte další postupy článek.

> [!div class="nextstepaction"]
> [Pomocí aplikace blockchain](blockchain-workbench-use.md)
