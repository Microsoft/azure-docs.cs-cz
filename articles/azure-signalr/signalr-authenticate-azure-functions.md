---
title: 'Kurz: Ověřování služby Azure SignalR s využitím služby Azure Functions | Microsoft Docs'
description: V tomto kurzu se naučíte ověřovat klienty služby Azure SignalR.
services: signalr
documentationcenter: ''
author: sffamily
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: signalr
ms.workload: tbd
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/18/2018
ms.author: zhshang
ms.openlocfilehash: 8af657c39217f3edcadef6ec0981a31ec7e89aa6
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978413"
---
# <a name="tutorial-azure-signalr-service-authentication-with-azure-functions"></a>Kurz: Ověřování služby Azure SignalR s využitím služby Azure Functions

Podrobný kurz vytvoření chatovací místnosti s ověřováním a zasíláním soukromých zpráv s využitím služeb Azure Functions a SignalR a ověřování pomocí služby App Service.

## <a name="introduction"></a>Úvod

### <a name="technologies-used"></a>Použité technologie

* [Azure Functions](https://azure.microsoft.com/services/functions/?WT.mc_id=serverlesschatlab-tutorial-antchu) – Back-endové rozhraní API pro ověřování uživatelů a odesílání zpráv chatu
* [Azure SignalR Service](https://azure.microsoft.com/services/signalr-service/?WT.mc_id=serverlesschatlab-tutorial-antchu) – Všesměrové vysílání nových zpráv připojeným chatovacím klientům
* [Azure Storage](https://azure.microsoft.com/services/storage/?WT.mc_id=serverlesschatlab-tutorial-antchu) – Hostování statického webu pro uživatelské rozhraní chatovacího klienta

### <a name="prerequisites"></a>Požadavky

Pro tento kurz se vyžaduje následující software.

* [Git](https://git-scm.com/downloads)
* [Node.js](https://nodejs.org/en/download/) (verze 10.x)
* [.NET SDK](https://www.microsoft.com/net/download) (verze 2.x, vyžaduje se pro rozšíření Functions)
* [Nástroje Azure Functions Core](https://github.com/Azure/azure-functions-core-tools) (verze 2)
* [Visual Studio Code](https://code.visualstudio.com/) (VS Code) s následujícími rozšířeními
    * [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) – umožňuje pracovat se službou Azure Functions ve VS Code
    * [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) – místně obsluhuje webové stránky pro účely testování


## <a name="sign-into-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přejděte na web [Azure Portal](https://portal.azure.com/) a přihlaste se pomocí svých přihlašovacích údajů.


## <a name="create-an-azure-signalr-service-instance"></a>Vytvoření instance služby Azure SignalR

Místně sestavíte a otestujete aplikaci Azure Functions. Tato aplikace bude mít přístup k instanci služby SignalR v Azure, kterou je potřeba vytvořit předem.

1. Klikněte na tlačítko **Vytvořit prostředek** (**+**) a vytvořte nový prostředek Azure.

1. Vyhledejte a vyberte **službu SignalR**. Klikněte na možnost **Vytvořit**.

    ![Nová služba SignalR](media/signalr-authenticate-azure-functions/signalr-quickstart-new.png)

1. Zadejte následující informace.

    | Název | Hodnota |
    |---|---|
    | Název prostředku | Jedinečný název instance služby SignalR |
    | Skupina prostředků | Vytvoření nové skupiny prostředků |
    | Umístění | Vyberte umístění, které je blízko vás. |
    | Cenová úroveň | Free |
    
1. Klikněte na možnost **Vytvořit**.


## <a name="initialize-the-function-app"></a>Inicializace aplikace funkcí

### <a name="create-a-new-azure-functions-project"></a>Vytvoření nového projektu Azure Functions

1. V novém okně VS Code pomocí položky nabídky `File > Open Folder` (Soubor > Otevřít složku) vytvořte ve vhodném umístění prázdnou složku a otevřete ji. Toto bude hlavní složka projektu pro aplikaci, kterou vytvoříte.

1. Pomocí rozšíření Azure Functions ve VS Code v hlavní složce projektu inicializujte aplikaci funkcí.
    1. Ve VS Code otevřete paletu příkazů tím, že v nabídce vyberete **View > Command Palette** (Zobrazit > Paleta příkazů) (klávesová zkratka `Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).
    1. Vyhledejte a vyberte příkaz **Azure Functions: Create New Project** (Azure Functions: Vytvořit nový projekt).
    1. Měla by se zobrazit hlavní složka projektu. Vyberte ji (nebo ji vyhledejte pomocí možnosti Browse (Procházet)).
    1. Ve výzvě k výběru jazyka vyberte **JavaScript**.

    ![Vytvoření Function App](media/signalr-authenticate-azure-functions/signalr-create-vscode-app.png)


### <a name="install-function-app-extensions"></a>Instalace rozšíření aplikace funkcí

V tomto kurzu se k interakci se službou Azure SignalR používají vazby služby Azure Functions. Podobně jako většina ostatních vazeb jsou vazby služby SignalR dostupné jako rozšíření, které je před použitím potřeba nainstalovat pomocí rozhraní příkazového řádku nástrojů Azure Functions Core.

1. Ve VS Code otevřete terminál tím, že v nabídce vyberete **View > Integrated Terminal** (Zobrazit > Integrovaný terminál) (Ctrl-`).

1. Ujistěte se, že je aktuálním adresářem hlavní složka projektu.

1. Nainstalujte rozšíření aplikace funkcí pro službu SignalR.
    ```
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.SignalRService -v 1.0.0-preview1-10002
    ```

### <a name="configure-application-settings"></a>Konfigurace nastavení aplikace

Při místním spouštění a ladění modulu runtime služby Azure Functions se nastavení aplikace načítají ze souboru **local.settings.json**. Aktualizujte v tomto souboru připojovací řetězec instance služby SignalR, kterou jste vytvořili dříve.

1. Ve VS Code v podokně Explorer (Průzkumník) vyberte soubor **local.settings.json** a otevřete ho.

1. Obsah souboru nahraďte následujícím kódem:
    ```json
    {
        "IsEncrypted": false,
        "Values": {
            "AzureSignalRConnectionString": "<signalr-connection-string>",
            "WEBSITE_NODE_DEFAULT_VERSION": "10.0.0",
            "FUNCTIONS_WORKER_RUNTIME": "node"
        },
        "Host": {
            "LocalHttpPort": 7071,
            "CORS": "*"
        }
    }
    ```

    * Do nastavení `AzureSignalRConnectionString` zadejte připojovací řetězec služby Azure SignalR. Tuto hodnotu získáte na stránce **Klíče** v prostředku služby Azure SignalR na webu Azure Portal. Můžete použít primární nebo sekundární připojovací řetězec.
    * Nastavení `WEBSITE_NODE_DEFAULT_VERSION` se místně nepoužívá, ale vyžaduje si při nasazení do Azure.
    * V části `Host` se konfigurují nastavení portu a CORS pro místního hostitele služby Functions (při spuštění v Azure nemá toto nastavení žádný vliv).

    ![Získání klíče služby SignalR](media/signalr-authenticate-azure-functions/signalr-get-key.png)

1. Uložte soubor.

    ![Aktualizace místního nastavení](media/signalr-authenticate-azure-functions/signalr-update-local-settings.png)


## <a name="create-a-function-to-authenticate-users-to-signalr-service"></a>Vytvoření funkce pro ověřování uživatelů ve službě SignalR

Při prvním otevření chatovací aplikace v prohlížeči se vyžadují platné přihlašovací údaje pro připojení ke službě Azure SignalR. Ve své aplikaci funkcí vytvoříte funkci *SignalRInfo* aktivovanou protokolem HTTP, která vrátí tyto informace o připojení.

1. Otevřete paletu příkazů VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Vyhledejte a vyberte příkaz **Azure Functions: Create Function** (Azure Functions: Vytvořit funkci).

1. Po zobrazení výzvy zadejte tyto informace.

    | Název | Hodnota |
    |---|---|
    | Složka aplikace funkcí | Vyberte hlavní složku projektu. |
    | Šablona | Trigger HTTP |
    | Název | SignalRInfo |
    | Úroveň autorizace | Anonymní |
    
    Vytvoří se složka **SignalRInfo** obsahující novou funkci.

1. Otevřete soubor **SignalRInfo/function.json** a nakonfigurujte pro funkci vazby. Následujícím způsobem upravte obsah souboru. Tím se přidá vstupní vazba, která vygeneruje platné přihlašovací údaje pro připojení klienta k centru služby Azure SignalR `chat`.

    ```json
    {
        "disabled": false,
        "bindings": [
            {
                "authLevel": "anonymous",
                "type": "httpTrigger",
                "direction": "in",
                "name": "req"
            },
            {
                "type": "http",
                "direction": "out",
                "name": "res"
            },
            {
                "type": "signalRConnectionInfo",
                "name": "connectionInfo",
                "userId": "",
                "hubName": "chat",
                "direction": "in"
            }
        ]
    }
    ```

    Vlastnost `userId` ve vazbě `signalRConnectionInfo` slouží k vytvoření ověřeného připojení ke službě SignalR. Pro účely místního vývoje ponechte tuto vlastnost prázdnou. Použijete ji při nasazování aplikace funkcí do Azure.

1. Otevřete soubor **SignalRInfo/index.js** a prohlédněte si tělo funkce. Následujícím způsobem upravte obsah souboru.

    ```javascript
    module.exports = function (context, req, connectionInfo) {
        context.res = { body: connectionInfo };
        context.done();
    };
    ```

    Tato funkce převezme ze vstupní vazby informace o připojení ke službě SignalR a vrátí je klientovi v textu odpovědi HTTP.


## <a name="create-a-function-to-send-chat-messages"></a>Vytvoření funkce pro odesílání zpráv chatu

Webová aplikace k odesílání zpráv chatu vyžaduje také rozhraní HTTP API. Vytvoříte funkci *SendMessage* aktivovanou protokolem HTTP, která bude pomocí služby SignalR odesílat zprávy všem připojeným klientům.

1. Otevřete paletu příkazů VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Vyhledejte a vyberte příkaz **Azure Functions: Create Function** (Azure Functions: Vytvořit funkci).

1. Po zobrazení výzvy zadejte tyto informace.

    | Název | Hodnota |
    |---|---|
    | Složka aplikace funkcí | Vyberte hlavní složku projektu. |
    | Šablona | Trigger HTTP |
    | Název | SendMessage |
    | Úroveň autorizace | Anonymní |
    
    Vytvoří se složka **SendMessage** obsahující novou funkci.

1. Otevřete soubor **SendMessage/function.json** a nakonfigurujte pro funkci vazby. Následujícím způsobem upravte obsah souboru.
    ```json
    {
        "disabled": false,
        "bindings": [
            {
                "authLevel": "anonymous",
                "type": "httpTrigger",
                "direction": "in",
                "name": "req",
                "route": "messages",
                "methods": [
                    "post"
                ]
            },
            {
                "type": "http",
                "direction": "out",
                "name": "res"
            },
            {
                "type": "signalR",
                "name": "signalRMessages",
                "hubName": "chat",
                "direction": "out"
            }
        ]
    }
    ```
    U původní funkce se provedou dvě změny:
    * Trasa se změní na `messages` a trigger HTTP se omezí na metodu HTTP **POST**.
    * Přidá se výstupní vazba služby SignalR, která bude odesílat zprávy všem klientům připojeným k centru služby SignalR `chat`.

1. Uložte soubor.

1. Otevřete soubor **SendMessage/index.js** a prohlédněte si tělo funkce. Následujícím způsobem upravte obsah souboru.
    ```javascript
    module.exports = function (context, req) {
        const message = req.body;
        message.sender = req.headers && req.headers['x-ms-client-principal-name'] || '';
            
        let recipientUserId = '';
        if (message.recipient) {
            recipientUserId = message.recipient;
            message.isPrivate = true;
        }
    
        context.bindings.signalRMessages = [{
            'userId': recipientUserId,
            'target': 'newMessage',
            'arguments': [ message ]
        }];
        context.done();
    };
    ```
    Tato funkce převezme text z požadavku HTTP a odešle ho klientům připojeným ke službě SignalR, a tím v každém klientovi vyvolá funkci `newMessage`.

    Tato funkce může přečíst identitu odesilatele a přijímat v textu zprávy hodnotu *recipient*, která umožňuje soukromě odeslat zprávu jedinému uživateli. Tyto funkce použijete v pozdější části kurzu.

1. Uložte soubor.


## <a name="create-and-run-the-chat-client-web-user-interface"></a>Vytvoření a spuštění webového uživatelského rozhraní chatovacího klienta

Uživatelské rozhraní chatovací aplikace je jednoduchá jednostránková aplikace (SPA) vytvořená s využitím rozhraní JavaScriptu Vue. Tato aplikace se bude hostovat odděleně od aplikace funkcí. Místně spustíte webové rozhraní pomocí rozšíření Live Server pro VS Code.

1. Ve VS Code vytvořte v kořenovém adresáři hlavní složky projektu novou složku **content**.

1. Ve složce **content** vytvořte nový soubor **index.html**.

1. Zkopírujte a vložte do něj obsah souboru **[index.html](https://raw.githubusercontent.com/Azure-Samples/signalr-service-quickstart-serverless-chat/master/docs/demo/chat-with-auth/index.html)**.

1. Uložte soubor.

1. Stisknutím **F5** místně spusťte aplikaci funkcí a připojte k ní ladicí program.

1. Když máte otevřený soubor **index.html**, spusťte Live Server tím, že otevřete paletu příkazů VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`) a vyberete **Live Server: Open with Live Server** (Live Server: Otevřít pomocí rozšíření Live Server). Live Server otevře aplikaci v prohlížeči.

1. Aplikace se otevře. Zadejte do okna chatu zprávu a stiskněte Enter. Aktualizujte aplikaci, aby se zobrazily nové zprávy. Vzhledem k tomu, že není nakonfigurované žádné ověřování, budou se všechny zprávy odesílat jako anonymní.


## <a name="deploy-to-azure-and-enable-authentication"></a>Nasazení do Azure a povolení ověřování

Spustili jste aplikaci funkcí a chatovací aplikaci v místním prostředí. Teď je nasadíte do Azure a povolíte v aplikaci ověřování a zasílání soukromých zpráv.


### <a name="log-into-azure-with-vs-code"></a>Přihlášení k Azure pomocí VS Code

1. Otevřete paletu příkazů VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Vyhledejte a vyberte příkaz **Azure: Sign in** (Azure: Přihlásit se).

1. Postupujte podle pokynů a dokončete proces přihlášení v prohlížeči.


### <a name="configure-function-app-for-authentication"></a>Konfigurace ověřování v aplikaci funkcí

Chatovací aplikace zatím funguje anonymně. V Azure k ověření uživatele použijete [ověřování pomocí služby App Service](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview). ID nebo uživatelské jméno ověřeného uživatele je možné předat vazbě *SignalRConnectionInfo*, která vygeneruje informace o připojení ověřeném jako uživatel.

Při odesílání zprávy může aplikace určit, jestli se má zpráva odeslat všem připojeným klientům, nebo pouze klientům ověřeným pro daného uživatele.

1. Ve VS Code otevřete soubor **SendMessage/function.json**.

1. Do vlastnosti *userId* vazby *SignalRConnectionInfo* vložte [výraz vazby](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings#binding-expressions-and-patterns): `{headers.x-ms-client-principal-name}`. Tím se hodnota vlastnosti nastaví na uživatelské jméno ověřeného uživatele. Atribut by teď měl vypadat takto:

    ```json
    {
        "type": "signalRConnectionInfo",
        "name": "connectionInfo",
        "userId": "{headers.x-ms-client-principal-name}",
        "hubName": "chat",
        "direction": "in"
    }
    ```

1. Uložte soubor.


### <a name="deploy-function-app"></a>Nasazení aplikace funkcí

1. Otevřete paletu příkazů VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`) a vyberte **Azure Functions: Deploy to Function App** (Azure Functions: Nasadit do aplikace Function App). 

1. Po zobrazení výzvy zadejte tyto informace.

    | Název | Hodnota |
    |---|---|
    | Složka k nasazení | Vyberte hlavní složku projektu. |
    | Předplatné | Vyberte své předplatné. |
    | Function App | Vyberte **Vytvořit novou aplikaci funkcí**. |
    | Název aplikace funkcí | Zadejte jedinečný název. |
    | Skupina prostředků | Vyberte stejnou skupinu prostředků jako pro instanci služby SignalR. |
    | Účet úložiště | Vyberte **Vytvořit nový účet úložiště**. |
    | Název účtu úložiště | Zadejte jedinečný název (3 až 24 znaků, pouze alfanumerické znaky). |
    | Umístění | Vyberte umístění, které je blízko vás. |
    
    V Azure se vytvoří nová aplikace funkcí a spustí se nasazení. Počkejte, než se nasazení dokončí.


### <a name="upload-function-app-local-settings"></a>Nahrání místního nastavení aplikace funkcí

1. Otevřete paletu příkazů VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Vyhledejte a vyberte příkaz **Azure Functions: Upload local settings** (Azure Functions: Nahrát místní nastavení).

1. Po zobrazení výzvy zadejte tyto informace.

    | Název | Hodnota |
    |---|---|
    | Soubor místního nastavení | local.settings.json |
    | Předplatné | Vyberte své předplatné. |
    | Function App | Vyberte dříve nasazenou aplikaci funkcí. |
    | Název aplikace funkcí | Zadejte jedinečný název. |

Místní nastavení se nahraje do aplikace funkcí v Azure. Pokud se zobrazí výzva k potvrzení přepsání stávajícího nastavení, vyberte **Ano všem**.


### <a name="enable-function-app-cross-origin-resource-sharing-cors"></a>Povolení sdílení prostředků mezi zdroji (CORS) v aplikaci funkcí

Přestože soubor **local.settings.json** obsahuje nastavení CORS, toto nastavení se nerozšíří do aplikace funkcí v Azure. Musíte ho nastavit samostatně.

1. Otevřete paletu příkazů VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Vyhledejte a vyberte příkaz **Azure Functions: Open in portal** (Azure Functions: Otevřít na portálu).

1. Výběrem předplatného a názvu aplikace funkcí otevřete aplikaci funkcí na webu Azure Portal.

1. Na otevřeném portálu na kartě **Funkce platformy** vyberte **CORS**.

    ![Vyhledání CORS](media/signalr-authenticate-azure-functions/signalr-find-cors.png)

1. Přidejte položku s hodnotou `*`.

1. Odeberte všechny ostatní existující položky.

1. Kliknutím na **Uložit** uložte nastavení CORS.

    ![Nastavení CORS](media/signalr-authenticate-azure-functions/signalr-set-cors.png)

> [!NOTE]
> V reálné aplikaci byste místo povolení CORS pro všechny zdroje (`*`) měli využít bezpečnější přístup, kterým je zadání konkrétních položek CORS pro každou doménu, která to potřebuje.


### <a name="update-the-web-app"></a>Aktualizace webové aplikace

1. Na webu Azure Portal přejděte na stránku Přehled aplikace funkcí.

1. Zkopírujte adresu URL aplikace funkcí.

    ![Získání adresy URL](media/signalr-authenticate-azure-functions/signalr-get-url.png)


1. Ve VS Code otevřete soubor **index.html** a nahraďte hodnotu `apiBaseUrl` adresou URL aplikace funkcí.

1. Pro aplikaci je možné nakonfigurovat ověřování přes Azure Active Directory, Facebook, Twitter, účet Microsoft nebo Google. Nastavením hodnoty `authProvider` vyberte zprostředkovatele ověřování, kterého budete používat.

1. Uložte soubor.


### <a name="deploy-the-web-application-to-blob-storage"></a>Nasazení webové aplikace do úložiště objektů blob

K hostování webové aplikace využijeme funkci statického webu ve službě Azure Blob Storage.

1. Klikněte na tlačítko **Nový** (**+**) a vytvořte nový prostředek Azure.

1. V části **Úložiště** vyberte **Účet úložiště**.

1. Zadejte následující informace.

    | Název | Hodnota |
    |---|---|
    | Název | Jedinečný název účtu úložiště objektů blob |
    | Account kind (Druh účtu) | StorageV2 (obecné účely V2) |
    | Umístění | Vyberte stejnou oblast jako pro ostatní prostředky. |
    | Replikace | Místně redundantní úložiště (LRS) |
    | Výkon | Standard |
    | Access tier (Vrstva přístupu) | Hot |
    | Skupina prostředků | Vyberte stejnou skupinu prostředků jako pro ostatní prostředky v tomto kurzu. |

1. Klikněte na možnost **Vytvořit**.

    ![Vytvoření úložiště](media/signalr-authenticate-azure-functions/signalr-create-storage.png)

1. Po vytvoření účtu úložiště ho otevřete na webu Azure Portal.

1. Na levém navigačním panelu vyberte **Statický web (Preview)**.

1. Vyberte **Povolit**.

1. Jako **Název dokumentu indexu** zadejte `index.html`.

1. Klikněte na **Uložit**.

    ![Konfigurace statických webů](media/signalr-authenticate-azure-functions/signalr-static-websites.png)

1. Kliknutím na odkaz **$web** na stránce otevřete kontejner objektů blob.

1. Klikněte na **Nahrát** a nahrajte soubor **index.html** do složky **content**.

1. Vraťte se na stránku **Statický web**. Poznamenejte si **Primární koncový bod**. Toto je adresa URL webové aplikace.


### <a name="enable-app-service-authentication"></a>Povolení ověřování pomocí služby App Service

Ověřování pomocí služby App Service podporuje ověřování přes Azure Active Directory, Facebook, Twitter, účet Microsoft a Google.

1. Když máte na portálu stále otevřenou aplikaci funkcí, vyhledejte kartu **Funkce platformy** a vyberte **Ověřování/autorizace**.

1. Nastavte ověřování pomocí služby App Service na **Zapnuto**.

1. V části **Akce, která se má provést, když požadavek nebude ověřený** vyberte Přihlásit se přes {zprostředkovatel přihlašování, kterého jste vybrali dříve}.

1. V části **Povolené externí adresy URL pro přesměrování** zadejte adresu URL primárního webového koncového bodu vašeho účtu úložiště, kterou jste si poznamenali dříve.

1. Dokončete konfiguraci podle pokynů v dokumentaci vybraného zprostředkovatele přihlašování.

    - [Azure Active Directory](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-active-directory-authentication)
    - [Facebook](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-facebook-authentication)
    - [Twitter](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-twitter-authentication)
    - [Účet Microsoft](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-microsoft-authentication)
    - [Google](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-google-authentication)


### <a name="try-the-application"></a>Vyzkoušení aplikace

1. V prohlížeči přejděte na primární webový koncový bod účtu úložiště.

1. Vyberte **Login** (Přihlášení) a ověřte se přes vybraného zprostředkovatele ověřování.

1. Veřejné zprávy můžete odesílat zadáním zprávy do hlavního okna chatu.

1. Soukromé zprávy můžete odesílat kliknutím na uživatelské jméno v historii chatu. Tyto zprávy obdrží pouze vybraný příjemce.

Blahopřejeme! Nasadili jste chatovací aplikaci bez serveru, která funguje v reálném čase.

![Ukázka](media/signalr-authenticate-azure-functions/signalr-serverless-chat.gif)


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit prostředky vytvořené v tomto kurzu, pomocí webu Azure Portal odstraňte skupinu prostředků.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak používat Azure Functions se službou Azure SignalR. Přečtěte si více o vytváření aplikací bez serveru fungujících v reálném čase s využitím vazeb služby SignalR pro Azure Functions.

> [!div class="nextstepaction"]
> [Vytváření aplikací v reálném čase s využitím Azure Functions](signalr-overview-azure-functions.md)
