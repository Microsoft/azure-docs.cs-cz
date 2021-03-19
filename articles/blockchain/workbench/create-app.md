---
title: Vytvoření aplikace v blockchain – Azure blockchain Workbench
description: Kurz týkající se vytvoření aplikace blockchain pro Azure blockchain Workbench Preview
ms.date: 08/24/2020
ms.topic: tutorial
ms.reviewer: ravastra
ms.openlocfilehash: add790a069d2e0ea66d84bbd632825cf9331fd38
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88784024"
---
# <a name="tutorial-create-a-blockchain-application-for-azure-blockchain-workbench"></a>Kurz: Vytvoření aplikace blockchain pro Azure blockchain Workbench

Pomocí služby Azure blockchain Workbench můžete vytvářet aplikace blockchain, které reprezentují pracovní postupy s více stranami definované pomocí konfigurace a kódu inteligentní smlouvy.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Konfigurace aplikace blockchain
> * Vytvořit soubor kódu inteligentního kontraktu
> * Přidání aplikace blockchain do aplikace blockchain Workbench
> * Přidání členů do aplikace blockchain

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Předpoklady

* Nasazení aplikace blockchain Workbench Další informace najdete v tématu [nasazení Azure blockchain Workbench](deploy.md) pro podrobnosti o nasazení.
* Azure Active Directory uživatelů v tenantovi přidružených k blockchain Workbench. Další informace najdete v tématu [Přidání uživatelů Azure AD v Azure blockchain Workbench](manage-users.md#add-azure-ad-users).
* Účet správce blockchain Workbench. Další informace najdete v tématu Přidání [správců blockchain Workbench do Azure blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).

## <a name="hello-blockchain"></a>Dobrý den, blockchain!

Vytvoříme základní aplikaci, ve které žadatel pošle žádost a partnerovi pošle odpověď na požadavek.
Například žádost může být "Hello, jak jste?" a odpověď může být "Skvělé!". Požadavek i odpověď se zaznamenávají na podkladové blockchainy.

Použijte postup vytvoření souborů aplikace nebo si ukázku si můžete [stáhnout z GitHubu](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/hello-blockchain).

## <a name="configuration-file"></a>Konfigurační soubor

Metadata konfigurace definují pracovní postupy vysoké úrovně a model interakce aplikace blockchain. Metadata konfigurace představují fáze pracovního postupu a model interakce aplikace blockchain. Další informace o obsahu konfiguračních souborů najdete v referenčních informacích k [Azure blockchain Workflow Configuration](configuration.md).

1. V oblíbeném editoru vytvořte soubor s názvem `HelloBlockchain.json` .
2. Přidejte následující JSON pro definování konfigurace aplikace blockchain.

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

Konfigurační soubor obsahuje několik oddílů. Podrobnosti o jednotlivých oddílech jsou následující:

### <a name="application-metadata"></a>Metadata aplikace

Začátek konfiguračního souboru obsahuje informace o aplikaci, včetně názvu a popisu aplikace.

### <a name="application-roles"></a>Aplikační role

Oddíl role aplikace definuje role uživatelů, kteří můžou pracovat s aplikací blockchain nebo se do ní zúčastnit. Můžete definovat sadu různých rolí založenou na funkcích. Ve scénáři požadavek-odpověď existuje rozdíl mezi funkcemi žadatele jako entita, která vytváří žádosti a respondér jako entita, která vytváří odpovědi.

### <a name="workflows"></a>Pracovní postupy

Pracovní postupy definují jednu nebo více fází a akcí smlouvy. Ve scénáři požadavek-odpověď je první fází (stav) pracovního postupu žadatelem (role), který odešle požadavek (funkce), provede akci (přechod). Další fáze (stav) je respondér (role), který provádí akci (přechod) k odeslání odpovědi (funkce). Pracovní postup aplikace může zahrnovat vlastnosti, funkce a stavy, které jsou nutné k popisu toku kontraktu.

## <a name="smart-contract-code-file"></a>Soubor kódu inteligentního kontraktu

Inteligentní kontrakty reprezentují obchodní logiku aplikace blockchain. V současné době blockchain Workbench podporuje Ethereem pro hlavní knihu blockchain. Ethereem využívá jako svůj programovací jazyk [plnou](https://solidity.readthedocs.io) zárukou pro vytváření samoobslužných obchodních logik pro inteligentní kontrakty.

Inteligentní kontrakty v hustotě jsou podobné třídám v objektově orientovaném jazyce. Každá smlouva obsahuje stav a funkce pro implementaci fází a akcí inteligentního kontraktu.

V oblíbeném editoru vytvořte soubor s názvem `HelloBlockchain.sol` .

### <a name="version-pragma"></a>Direktiva pragma verze

Osvědčeným postupem je označit verzi, na kterou cílíte. Určení verze pomáhá vyhnout se nekompatibilitě s budoucími verzemi.

Přidejte následující direktivu pragma verze v horní části `HelloBlockchain.sol` souboru kódu inteligentního kontraktu.

``` solidity
pragma solidity >=0.4.25 <0.6.0;
```

### <a name="configuration-and-smart-contract-code-relationship"></a>Vztah kódu konfigurace a inteligentního kontraktu

Blockchain Workbench používá konfigurační soubor a soubor kódu inteligentního kontraktu k vytvoření aplikace blockchain. Existuje vztah mezi tím, co je definováno v konfiguraci a kódem v rámci inteligentního kontraktu. Aby bylo možné vytvořit aplikaci, je nutné, aby odpovídaly podrobnosti kontraktu, funkce, parametry a typy. Blockchain Workbench ověřuje soubory před vytvořením aplikace.

### <a name="contract"></a>Kontrakt

Přidejte hlavičku **kontraktu** do `HelloBlockchain.sol` souboru kódu inteligentního kontraktu.

``` solidity
contract HelloBlockchain {
```

### <a name="state-variables"></a>Proměnné stavu

Proměnné stavu ukládají hodnoty stavu pro každou instanci smlouvy. Proměnné stavu ve vaší smlouvě musí odpovídat vlastnostem pracovního postupu, které jsou definovány v konfiguračním souboru.

Do svého kontraktu v `HelloBlockchain.sol` souboru kódu inteligentního kontraktu přidejte proměnné stavu.

``` solidity
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

Konstruktor definuje vstupní parametry pro novou instanci inteligentního kontraktu pracovního postupu. Požadované parametry pro konstruktor jsou definovány jako parametry konstruktoru v konfiguračním souboru. Počet, pořadí a typ parametrů se musí shodovat v obou souborech.

Ve funkci konstruktoru napíšete jakoukoli obchodní logiku, kterou chcete provést před vytvořením smlouvy. Například inicializujte proměnné stavu pomocí počátečních hodnot.

Přidejte funkci konstruktoru do svého kontraktu do `HelloBlockchain.sol` souboru kódu inteligentního kontraktu.

``` solidity
    // constructor function
    constructor(string memory message) public
    {
        Requestor = msg.sender;
        RequestMessage = message;
        State = StateType.Request;
    }
```

### <a name="functions"></a>Functions

Funkce jsou spustitelné jednotky obchodní logiky v rámci smlouvy. Požadované parametry pro funkci jsou definovány jako parametry funkce v konfiguračním souboru. Počet, pořadí a typ parametrů se musí shodovat v obou souborech. Funkce jsou přidruženy k přechodům v pracovním postupu blockchain Workbench v konfiguračním souboru. Přechod je akce prováděná za účelem přechodu na další fázi pracovního postupu aplikace, kterou Určuje smlouva.

Napište libovolnou obchodní logiku, kterou chcete ve funkci provádět. Například úprava hodnoty proměnné stavu.

1. Do svého kontraktu do `HelloBlockchain.sol` souboru kódu inteligentního kontraktu přidejte následující funkce.

    ``` solidity
        // call this function to send a request
        function SendRequest(string memory requestMessage) public
        {
            if (Requestor != msg.sender)
            {
                revert();
            }
    
            RequestMessage = requestMessage;
            State = StateType.Request;
        }
    
        // call this function to send a response
        function SendResponse(string memory responseMessage) public
        {
            Responder = msg.sender;
    
            ResponseMessage = responseMessage;
            State = StateType.Respond;
        }
    }
    ```

2. Uložte `HelloBlockchain.sol` soubor kódu inteligentního kontraktu.

## <a name="add-blockchain-application-to-blockchain-workbench"></a>Přidání aplikace blockchain do aplikace blockchain Workbench

Pokud chcete přidat aplikaci blockchain do aplikace blockchain Workbench, nahrajete soubory konfigurace a inteligentních smluv pro definování aplikace.

1. Ve webovém prohlížeči přejděte na webovou adresu blockchain Workbench. Například `https://{workbench URL}.azurewebsites.net/` Webová aplikace je vytvořena při nasazení aplikace blockchain Workbench. Informace o tom, jak najít webovou adresu blockchain Workbench, najdete v tématu [Webová adresa URL služby blockchain Workbench](deploy.md#blockchain-workbench-web-url) .
2. Přihlaste se jako [správce aplikace blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).
3. Vyberte **aplikace**  >  **nové**. Zobrazí se podokno **Nová aplikace** .
4. Vyberte možnost **nahrát konfiguraci smlouvy**  >  **Procházet** a vyhledejte **HelloBlockchain.js** konfiguračního souboru, který jste vytvořili. Konfigurační soubor je automaticky ověřen. Vyberte odkaz **Zobrazit** pro zobrazení chyb ověřování. Před nasazením aplikace opravte chyby ověřování.
5. Vyberte **Odeslat kód kontraktu**  >   a vyhledejte soubor kódu inteligentní kontraktu **HelloBlockchain. Sol** . Soubor kódu je automaticky ověřen. Vyberte odkaz **Zobrazit** pro zobrazení chyb ověřování. Před nasazením aplikace opravte chyby ověřování.
6. Vyberte **nasadit** a vytvořte tak aplikaci blockchain založenou na konfiguračních a inteligentních souborech smluv.

Nasazení aplikace blockchain trvá několik minut. Po dokončení nasazení se v **aplikacích** zobrazí nová aplikace. 

> [!NOTE]
> Blockchain aplikace můžete vytvářet také pomocí [REST API Azure blockchain Workbench](/rest/api/azure-blockchain-workbench).

## <a name="add-blockchain-application-members"></a>Přidat členy aplikace blockchain

Přidejte do své aplikace členy aplikace, abyste mohli iniciovat a provádět akce s kontrakty. Chcete-li přidat členy aplikace, musíte být [správcem aplikace blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).

1. Vyberte **aplikace**  >  **Hello, blockchain!**.
2. Počet členů přidružených k aplikaci se zobrazí v pravém horním rohu stránky. U nové aplikace bude počet členů nula.
3. V pravém horním rohu stránky vyberte odkaz **Členové** . Zobrazí se aktuální seznam členů pro aplikaci.
4. V seznamu členství vyberte **přidat členy**.
5. Vyberte nebo zadejte název člena, který chcete přidat. V seznamu jsou uvedeni pouze uživatelé Azure AD, kteří existují v tenantovi blockchain Workbench. Pokud uživatel není nalezen, je nutné [Přidat uživatele Azure AD](manage-users.md#add-azure-ad-users).
6. Vyberte **roli** člena. Jako první člen vyberte **žadatele** jako roli.
7. Výběrem **Přidat** přidejte člena s přidruženou rolí do aplikace.
8. Přidejte do aplikace jiného člena s rolí **odpovídajícího** .

Další informace o správě uživatelů v blockchain Workbench najdete v tématu [Správa uživatelů v Azure blockchain Workbench](manage-users.md) .

## <a name="next-steps"></a>Další kroky

V tomto článku se naučíte vytvořit základní aplikaci pro žádosti a odpověď. Pokud se chcete dozvědět, jak aplikaci používat, přejděte k dalšímu článku s postupem.

> [!div class="nextstepaction"]
> [Použití aplikace v blockchain](use.md)
