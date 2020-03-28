---
title: Vytvoření blockchainové aplikace – Azure Blockchain Workbench
description: Návod, jak vytvořit blockchainovou aplikaci pro Azure Blockchain Workbench Preview.
ms.date: 10/14/2019
ms.topic: tutorial
ms.reviewer: brendal
ms.openlocfilehash: 33a9e9c10c07d0808626353a7edfd505e0f60bc9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74324811"
---
# <a name="tutorial-create-a-blockchain-application-for-azure-blockchain-workbench"></a>Kurz: Vytvoření blockchainové aplikace pro Azure Blockchain Workbench

Azure Blockchain Workbench můžete použít k vytváření blockchainových aplikací, které představují vícestranné pracovní postupy definované konfigurací a inteligentním kódem smlouvy.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Konfigurace blockchainové aplikace
> * Vytvoření inteligentního souboru kódu smlouvy
> * Přidání blockchainové aplikace do Blockchain Workbench
> * Přidání členů do blockchainové aplikace

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Nasazení blockchainworkbench. Další informace najdete v [tématu nasazení Azure Blockchain Workbench podrobnosti](deploy.md) o nasazení.
* Uživatelé Azure Active Directory v tenantovi přidruženém k Blockchain Workbench. Další informace najdete v tématu [přidání uživatelů Azure AD v Azure Blockchain Workbench](manage-users.md#add-azure-ad-users).
* Účet správce Blockchain Workbench. Další informace najdete v tématu přidání [správců Blockchain Workbench v Azure Blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).

## <a name="hello-blockchain"></a>Dobrý den, Blockchain!

Pojďme vytvořit základní aplikaci, ve kterém žadatel odešle požadavek a respondér odeslat odpověď na požadavek.
Například požadavek může být: "Dobrý den, jak se máš?", a odpověď může být: "Jsem skvělý!". Požadavek i odpověď jsou zaznamenány na podkladovém blockchainu.

Postupujte podle pokynů k vytvoření souborů aplikace nebo si můžete [stáhnout ukázku z GitHubu](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/hello-blockchain).

## <a name="configuration-file"></a>Konfigurační soubor

Metadata konfigurace definují pracovní postupy na vysoké úrovni a model interakce blockchainové aplikace. Metadata konfigurace představují fáze pracovního postupu a model interakce blockchainové aplikace.

1. V oblíbeném editoru vytvořte soubor s názvem `HelloBlockchain.json`.
2. Přidejte následující JSON a definujte konfiguraci blockchainové aplikace.

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

Konfigurační soubor má několik částí. Podrobnosti o jednotlivých oddílech jsou následující:

### <a name="application-metadata"></a>Metadata aplikace

Začátek konfiguračního souboru obsahuje informace o aplikaci včetně názvu a popisu aplikace.

### <a name="application-roles"></a>Aplikační role

Část role aplikace definuje role uživatelů, kteří mohou jednat nebo se účastnit v rámci aplikace blockchain. Definujete sadu různých rolí na základě funkcí. Ve scénáři požadavek odpověď je rozdíl mezi funkcemi žadatele jako entita, která vytváří požadavky a respondér jako entita, která vytváří odpovědi.

### <a name="workflows"></a>Pracovní postupy

Pracovní postupy definují jednu nebo více fází a akcí smlouvy. Ve scénáři požadavek odpověď první fáze (stav) pracovního postupu je žadatel (role) provede akci (přechod) odeslat požadavek (funkce). Další fáze (stav) je respondér (role) provede akci (přechod) k odeslání odpovědi (funkce). Pracovní postup aplikace může zahrnovat vlastnosti, funkce a stavy požadované popisují tok smlouvy.

Další informace o obsahu konfiguračních souborů najdete v [tématu Azure Blockchain Workflow configuration reference](configuration.md).

## <a name="smart-contract-code-file"></a>Soubor kódu inteligentní smlouvy

Chytré kontrakty představují obchodní logiku blockchainové aplikace. V současné době Blockchain Workbench podporuje ethereum pro blockchainovou knihu. Ethereum používá [Solidity](https://solidity.readthedocs.io) jako svůj programovací jazyk pro psaní self-prosazování obchodní logiky pro inteligentní smlouvy.

Inteligentní kontrakty v soliditě jsou podobné třídám v objektově orientovaných jazycích. Každá smlouva obsahuje stav a funkce pro implementaci fází a akcí inteligentní smlouvy.

V oblíbeném editoru vytvořte soubor s názvem `HelloBlockchain.sol`.

### <a name="version-pragma"></a>Verze pragma

Jako osvědčený postup uveďte verzi solidity, na kterou cílíte. Zadání verze pomáhá vyhnout se nekompatibilitám s budoucími verzemi Solidity.

V horní části souboru `HelloBlockchain.sol` kódu inteligentní smlouvy přidejte následující verzi pragma.

``` solidity
pragma solidity >=0.4.25 <0.6.0;
```

### <a name="configuration-and-smart-contract-code-relationship"></a>Konfigurace a vztah inteligentního kódu smlouvy

Blockchain Workbench používá konfigurační soubor a inteligentní soubor kódu smlouvy k vytvoření blockchainové aplikace. Existuje vztah mezi co je definováno v konfiguraci a kód v inteligentní smlouvy. Podrobnosti smlouvy, funkce, parametry a typy jsou nutné k vytvoření aplikace. Blockchain Workbench ověřuje soubory před vytvořením aplikace.

### <a name="contract"></a>Kontrakt

Přidejte **contract** hlavičku `HelloBlockchain.sol` smlouvy do souboru kódu inteligentní smlouvy.

``` solidity
contract HelloBlockchain {
```

### <a name="state-variables"></a>Proměnné stavu

Proměnné stavu ukládají hodnoty stavu pro každou instanci smlouvy. Proměnné stavu ve smlouvě se musí shodovat s vlastnostmi pracovního postupu definovanými v konfiguračním souboru.

Přidejte proměnné stavu do smlouvy `HelloBlockchain.sol` v souboru kódu inteligentní smlouvy.

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

Konstruktor definuje vstupní parametry pro novou instanci inteligentní smlouvy pracovního postupu. Požadované parametry pro konstruktor uvázají jako parametry konstruktoru v konfiguračním souboru. Počet, pořadí a typ parametrů se musí shodovat v obou souborech.

Do funkce konstruktoru napište libovolnou obchodní logiku, kterou chcete provést před vytvořením smlouvy. Například inicializovat proměnné stavu s počáteční hodnoty.

Přidejte funkci konstruktoru do `HelloBlockchain.sol` smlouvy v souboru kódu inteligentní smlouvy.

``` solidity
    // constructor function
    constructor(string memory message) public
    {
        Requestor = msg.sender;
        RequestMessage = message;
        State = StateType.Request;
    }
```

### <a name="functions"></a>Funkce

Funkce jsou spustitelné jednotky obchodní logiky v rámci smlouvy. Požadované parametry funkce jsou definovány jako parametry funkce v konfiguračním souboru. Počet, pořadí a typ parametrů se musí shodovat v obou souborech. Funkce jsou přidruženy k přechodům v pracovním postupu Blockchain Workbench v konfiguračním souboru. Přechod je akce provedená k přesunutí do další fáze pracovního postupu aplikace určeného smlouvou.

Napište libovolnou obchodní logiku, kterou chcete provést ve funkci. Například úprava hodnoty proměnné stavu.

1. Přidejte do smlouvy následující `HelloBlockchain.sol` funkce v souboru kódu inteligentní smlouvy.

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

2. Uložte `HelloBlockchain.sol` soubor inteligentního kódu smlouvy.

## <a name="add-blockchain-application-to-blockchain-workbench"></a>Přidání blockchainové aplikace do Blockchain Workbench

Chcete-li přidat blockchainovou aplikaci do Blockchain Workbench, nahrajete konfigurační a inteligentní soubory smluv, abyste definovali aplikaci.

1. Ve webovém prohlížeči přejděte na webovou adresu Blockchain Workbench. Například `https://{workbench URL}.azurewebsites.net/` webová aplikace se vytvoří při nasazení Blockchain Workbench. Informace o tom, jak najít webovou adresu Blockchain Workbench, najdete v [tématu Blockchain Workbench Web URL](deploy.md#blockchain-workbench-web-url)
2. Přihlaste se jako [správce Blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).
3. Vyberte **možnost Nové aplikace** > **.** Zobrazí se podokno **Nová aplikace.**
4. Vyberte **Nahrát konfiguraci** > smlouvy**Procházet** a vyhledejte konfigurační soubor **HelloBlockchain.json,** který jste vytvořili. Konfigurační soubor je automaticky ověřen. Chcete-li zobrazit chyby ověření, vyberte odkaz **Zobrazit.** Před nasazením aplikace opravte chyby ověření.
5. Vyberte **Nahrát kód** > smlouvy**Procházet** a vyhledejte soubor kódu inteligentní smlouvy **HelloBlockchain.sol.** Soubor kódu je automaticky ověřen. Chcete-li zobrazit chyby ověření, vyberte odkaz **Zobrazit.** Před nasazením aplikace opravte chyby ověření.
6. Vyberte **Nasadit** a vytvořte blockchainovou aplikaci na základě konfigurace a inteligentních souborů smluv.

Nasazení blockchainové aplikace trvá několik minut. Po dokončení nasazení se nová aplikace zobrazí v **aplikaci Aplikace**. 

> [!NOTE]
> Blockchainové aplikace můžete vytvářet taky pomocí [rozhraní Azure Blockchain Workbench REST API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench).

## <a name="add-blockchain-application-members"></a>Přidání členů blockchainové aplikace

Přidejte členy aplikace do aplikace k zahájení a provádění akcí na smlouvy. Chcete-li přidat členy aplikace, musíte být [správcem Blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).

1. Vyberte **aplikace** > **Hello, Blockchain!**.
2. Počet členů přidružených k aplikaci se zobrazí v pravém horním rohu stránky. Pro novou aplikaci bude počet členů nulový.
3. Vyberte odkaz **pro členy** v pravém horním rohu stránky. Zobrazí se aktuální seznam členů aplikace.
4. V seznamu členství vyberte **Přidat členy**.
5. Vyberte nebo zadejte jméno člena, které chcete přidat. Jsou uvedeni jenom uživatelé Azure AD, kteří existují v tenantovi Blockchain Workbench. Pokud uživatel není nalezen, je třeba [přidat uživatele Azure AD](manage-users.md#add-azure-ad-users).
6. Vyberte **roli** pro člena. Pro prvního člena vyberte jako roli **žadatel.**
7. Vyberte **Přidat,** chcete-li přidat člena s přidruženou rolí do aplikace.
8. Přidejte do aplikace dalšího člena s rolí **Respondéru.**

Další informace o správě uživatelů v Blockchain Workbench najdete [v tématu správa uživatelů v Azure Blockchain Workbench](manage-users.md)

## <a name="next-steps"></a>Další kroky

V tomto článku s návody jste vytvořili základní aplikaci pro požadavky a odpovědi. Chcete-li se dozvědět, jak používat aplikaci, pokračujte dalším článkem s postupem.

> [!div class="nextstepaction"]
> [Použití blockchainové aplikace](use.md)
