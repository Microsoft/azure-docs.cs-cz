---
title: 'Kurz: ověřování pomocí Azure Functions – signál Azure'
description: V tomto kurzu se naučíte ověřovat klienty služby Azure Signal Service pro Azure Functions vazby.
author: sffamily
ms.service: signalr
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: zhshang
ms.custom: devx-track-js
ms.openlocfilehash: 6df47d3fd62083a5d0940a1d6da50ac5d7d955f4
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150906"
---
# <a name="tutorial-azure-signalr-service-authentication-with-azure-functions"></a>Kurz: Ověřování služby Azure SignalR s využitím služby Azure Functions

Podrobný kurz vytvoření chatovací místnosti s ověřováním a zasíláním soukromých zpráv s využitím služeb Azure Functions a SignalR a ověřování pomocí služby App Service.

## <a name="introduction"></a>Úvod

### <a name="technologies-used"></a>Používané technologie

* [Azure Functions](https://azure.microsoft.com/services/functions/?WT.mc_id=serverlesschatlab-tutorial-antchu) – Back-endové rozhraní API pro ověřování uživatelů a odesílání zpráv chatu
* [Azure SignalR Service](https://azure.microsoft.com/services/signalr-service/?WT.mc_id=serverlesschatlab-tutorial-antchu) – Všesměrové vysílání nových zpráv připojeným chatovacím klientům
* [Azure Storage](https://azure.microsoft.com/services/storage/?WT.mc_id=serverlesschatlab-tutorial-antchu) – Hostování statického webu pro uživatelské rozhraní chatovacího klienta

### <a name="prerequisites"></a>Předpoklady

Pro tento kurz se vyžaduje následující software.

* [Git](https://git-scm.com/downloads)
* [Node.js](https://nodejs.org/en/download/) (verze 10.x)
* [.NET SDK](https://www.microsoft.com/net/download) (verze 2.x, vyžaduje se pro rozšíření Functions)
* [Nástroje Azure Functions Core](https://github.com/Azure/azure-functions-core-tools) (verze 2)
* [Visual Studio Code](https://code.visualstudio.com/) (VS Code) s následujícími rozšířeními
  * [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) – umožňuje pracovat se službou Azure Functions ve VS Code
  * [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) – místně obsluhuje webové stránky pro účely testování

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/asrs/qsauth)

## <a name="sign-into-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přejděte na web [Azure Portal](https://portal.azure.com/) a přihlaste se pomocí svých přihlašovacích údajů.

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/asrs/qsauth)

## <a name="create-an-azure-signalr-service-instance"></a>Vytvoření instance služby Azure SignalR Service

Místně sestavíte a otestujete aplikaci Azure Functions. Tato aplikace bude mít přístup k instanci služby SignalR v Azure, kterou je potřeba vytvořit předem.

1. Klikněte na tlačítko **vytvořit prostředek** ( **+** ) pro vytvoření nového prostředku Azure.

1. Vyhledejte a vyberte **službu SignalR**. Klikněte na **Vytvořit**.

    ![Nová služba SignalR](media/signalr-tutorial-authenticate-azure-functions/signalr-quickstart-new.png)

1. Zadejte následující informace.

    | Název | Hodnota |
    |---|---|
    | Název prostředku | Jedinečný název instance služby SignalR |
    | Skupina prostředků | Vytvořte novou skupinu prostředků s jedinečným názvem. |
    | Umístění | Vyberte umístění, které je blízko vás. |
    | Cenová úroveň | Free |

1. Klikněte na **Vytvořit**.

1. Po nasazení instance ho otevřete na portálu a najděte jeho stránku nastavení. Změňte nastavení režimu služby na možnost bez *serveru*.

    ![Režim služby Signal](media/signalr-concept-azure-functions/signalr-service-mode.png)
    
[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/asrs/qsauth)

## <a name="initialize-the-function-app"></a>Inicializace aplikace funkcí

### <a name="create-a-new-azure-functions-project"></a>Vytvoření nového projektu Azure Functions

1. V novém okně VS Code pomocí položky nabídky `File > Open Folder` (Soubor > Otevřít složku) vytvořte ve vhodném umístění prázdnou složku a otevřete ji. Toto bude hlavní složka projektu pro aplikaci, kterou vytvoříte.

1. Pomocí rozšíření Azure Functions ve VS Code v hlavní složce projektu inicializujte aplikaci funkcí.
   1. Ve VS Code otevřete paletu příkazů tím, že v nabídce vyberete **View > Command Palette** (Zobrazit > Paleta příkazů) (klávesová zkratka `Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).
   1. Vyhledejte a vyberte příkaz **Azure Functions: Create New Project** (Azure Functions: Vytvořit nový projekt).
   1. Měla by se zobrazit hlavní složka projektu. Vyberte ji (nebo ji vyhledejte pomocí možnosti Browse (Procházet)).
   1. Ve výzvě k výběru jazyka vyberte **JavaScript**.

      ![Vytvoření aplikace funkcí](media/signalr-tutorial-authenticate-azure-functions/signalr-create-vscode-app.png)

### <a name="install-function-app-extensions"></a>Instalace rozšíření aplikace funkcí

V tomto kurzu se k interakci se službou Azure SignalR používají vazby služby Azure Functions. Podobně jako většina ostatních vazeb jsou vazby služby SignalR dostupné jako rozšíření, které je před použitím potřeba nainstalovat pomocí rozhraní příkazového řádku nástrojů Azure Functions Core.

1. Kliknutím na **zobrazit > terminálu** v nabídce (CTRL-) otevřete terminál v vs Code \` .

1. Ujistěte se, že je aktuálním adresářem hlavní složka projektu.

1. Nainstalujte rozšíření aplikace funkcí pro službu SignalR.

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.SignalRService -v 1.0.0
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
            "WEBSITE_NODE_DEFAULT_VERSION": "10.14.1",
            "FUNCTIONS_WORKER_RUNTIME": "node"
        },
        "Host": {
            "LocalHttpPort": 7071,
            "CORS": "http://127.0.0.1:5500",
            "CORSCredentials": true
        }
    }
    ```

   * Do nastavení `AzureSignalRConnectionString` zadejte připojovací řetězec služby Azure SignalR. Tuto hodnotu získáte na stránce **Klíče** v prostředku služby Azure SignalR na webu Azure Portal. Můžete použít primární nebo sekundární připojovací řetězec.
   * Nastavení `WEBSITE_NODE_DEFAULT_VERSION` se místně nepoužívá, ale vyžaduje se při nasazení do Azure.
   * V části `Host` se konfigurují nastavení portu a CORS pro místního hostitele služby Functions (při spuštění v Azure nemá toto nastavení žádný vliv).

       > [!NOTE]
       > Živý Server je obvykle nakonfigurovaný pro poskytování obsahu z `http://127.0.0.1:5500` . Pokud zjistíte, že používá jinou adresu URL nebo používáte jiný server HTTP, změňte `CORS` nastavení tak, aby odráželo správný počátek.

     ![Získání klíče služby SignalR](media/signalr-tutorial-authenticate-azure-functions/signalr-get-key.png)

1. Soubor uložte.

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/asrs/qsauth)

## <a name="create-a-function-to-authenticate-users-to-signalr-service"></a>Vytvoření funkce pro ověřování uživatelů ve službě SignalR

Při prvním otevření chatovací aplikace v prohlížeči se vyžadují platné přihlašovací údaje pro připojení ke službě Azure SignalR. Ve vaší aplikaci Function App vytvoříte funkci aktivovanou protokolem HTTP s názvem *Negotiate* a vrátíte tyto informace o připojení.

> [!NOTE]
> Tato funkce musí být pojmenována jako *Negotiate* , protože klient signalizace vyžaduje koncový bod, který končí `/negotiate` .

1. Otevřete paletu příkazů VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Vyhledejte a vyberte příkaz **Azure Functions: Create Function** (Azure Functions: Vytvořit funkci).

1. Po zobrazení výzvy zadejte tyto informace.

    | Název | Hodnota |
    |---|---|
    | Složka aplikace funkcí | Vyberte hlavní složku projektu. |
    | Šablona | Trigger HTTP |
    | Název | negotiate |
    | Úroveň autorizace | Anonymní |

    Vytvoří se složka s názvem **Negotiate** , která obsahuje novou funkci.

1. Otevřete **dohadování/function.js** pro konfiguraci vazeb pro funkci. Následujícím způsobem upravte obsah souboru. Tím se přidá vstupní vazba, která vygeneruje platné přihlašovací údaje pro připojení klienta k centru služby Azure SignalR `chat`.

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

1. Otevřete **Negotiate/index.js** a zobrazte tělo funkce. Následujícím způsobem upravte obsah souboru.

    ```javascript
    module.exports = async function (context, req, connectionInfo) {
        context.res.body = connectionInfo;
    };
    ```

    Tato funkce převezme ze vstupní vazby informace o připojení ke službě SignalR a vrátí je klientovi v textu odpovědi HTTP. Klient nástroje Signal použije tyto informace pro připojení k instanci služby signalizace.

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/asrs/qsauth)

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
                "name": "$return",
                "hubName": "chat",
                "direction": "out"
            }
        ]
    }
    ```
    U původní funkce se provedou dvě změny:
    * Trasa se změní na `messages` a trigger HTTP se omezí na metodu HTTP **POST**.
    * Přidá výstupní vazbu služby signalizace, která odešle zprávu vrácenou funkcí všem klientům připojeným k centru služby signalizace s názvem `chat` .

1. Soubor uložte.

1. Otevřete soubor **SendMessage/index.js** a prohlédněte si tělo funkce. Následujícím způsobem upravte obsah souboru.

    ```javascript
    module.exports = async function (context, req) {
        const message = req.body;
        message.sender = req.headers && req.headers['x-ms-client-principal-name'] || '';

        let recipientUserId = '';
        if (message.recipient) {
            recipientUserId = message.recipient;
            message.isPrivate = true;
        }

        return {
            'userId': recipientUserId,
            'target': 'newMessage',
            'arguments': [ message ]
        };
    };
    ```

    Tato funkce převezme text z požadavku HTTP a odešle ho klientům připojeným ke službě SignalR, a tím v každém klientovi vyvolá funkci `newMessage`.

    Tato funkce může přečíst identitu odesilatele a přijímat v textu zprávy hodnotu *recipient*, která umožňuje soukromě odeslat zprávu jedinému uživateli. Tyto funkce použijete v pozdější části kurzu.

1. Soubor uložte.

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/asrs/qsauth)

## <a name="create-and-run-the-chat-client-web-user-interface"></a>Vytvoření a spuštění webového uživatelského rozhraní chatovacího klienta

Uživatelské rozhraní chatovací aplikace je jednoduchá jednostránková aplikace (SPA) vytvořená s využitím rozhraní JavaScriptu Vue. Tato aplikace se bude hostovat odděleně od aplikace funkcí. Místně spustíte webové rozhraní pomocí rozšíření Live Server pro VS Code.

1. Ve VS Code vytvořte v kořenovém adresáři hlavní složky projektu novou složku **content**.

1. Ve složce **content** vytvořte nový soubor **index.html**.

1. Zkopírujte a vložte do něj obsah souboru **[index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/2720a9a565e925db09ef972505e1c5a7a3765be4/docs/demo/chat-with-auth/index.html)**.

1. Soubor uložte.

1. Stisknutím **F5** místně spusťte aplikaci funkcí a připojte k ní ladicí program.

1. Když máte otevřený soubor **index.html**, spusťte Live Server tím, že otevřete paletu příkazů VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`) a vyberete **Live Server: Open with Live Server** (Live Server: Otevřít pomocí rozšíření Live Server). Live Server otevře aplikaci v prohlížeči.

1. Aplikace se otevře. Zadejte do okna chatu zprávu a stiskněte Enter. Aktualizujte aplikaci, aby se zobrazily nové zprávy. Vzhledem k tomu, že není nakonfigurované žádné ověřování, budou se všechny zprávy odesílat jako anonymní.

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/asrs/qsauth)

## <a name="deploy-to-azure-and-enable-authentication"></a>Nasazení do Azure a povolení ověřování

Spustili jste aplikaci funkcí a chatovací aplikaci v místním prostředí. Teď je nasadíte do Azure a povolíte v aplikaci ověřování a zasílání soukromých zpráv.

### <a name="log-into-azure-with-vs-code"></a>Přihlášení k Azure pomocí VS Code

1. Otevřete paletu příkazů VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Vyhledejte a vyberte příkaz **Azure: Sign in** (Azure: Přihlásit se).

1. Postupujte podle pokynů a dokončete proces přihlášení v prohlížeči.

### <a name="create-a-storage-account"></a>Vytvoření účtu úložiště

Aplikace Function App běžící v Azure vyžaduje účet Azure Storage. Také budete hostovat webovou stránku pro uživatelské rozhraní chatu pomocí funkce static websites služby Azure Storage.

1. V Azure Portal klikněte na tlačítko **vytvořit prostředek** ( **+** ) pro vytvoření nového prostředku Azure.

1. Vyberte kategorii **úložiště** a pak vyberte **účet úložiště**.

1. Zadejte následující informace.

    | Název | Hodnota |
    |---|---|
    | Předplatné | Vyberte předplatné, které obsahuje instanci služby signalizace. |
    | Skupina prostředků | Vyberte stejnou skupinu prostředků. |
    | Název prostředku | Jedinečný název pro účet úložiště |
    | Umístění | Vyberte stejné umístění jako ostatní prostředky. |
    | Výkon | Standard |
    | Druh účtu | StorageV2 (obecné účely V2) |
    | Replikace | Místně redundantní úložiště (LRS) |
    | Úroveň přístupu | Horká |

1. Klikněte na tlačítko **zkontrolovat + vytvořit**a pak **vytvořit**.

### <a name="configure-static-websites"></a>Konfigurace statických webů

1. Po vytvoření účtu úložiště ho otevřete v Azure Portal.

1. Vyberte možnost **statický web**.

1. Pokud chcete povolit funkci statického webu, vyberte **povoleno** .

1. Do **název dokumentu indexu**zadejte *index.html*.

1. Klikněte na **Uložit**.

1. Zobrazí se **primární koncový bod** . Poznamenejte si tuto hodnotu. Bude se muset nakonfigurovat aplikace Function App.

### <a name="configure-function-app-for-authentication"></a>Konfigurace ověřování v aplikaci funkcí

Chatovací aplikace zatím funguje anonymně. V Azure k ověření uživatele použijete [ověřování pomocí služby App Service](../app-service/overview-authentication-authorization.md). ID nebo uživatelské jméno ověřeného uživatele je možné předat vazbě *SignalRConnectionInfo*, která vygeneruje informace o připojení ověřeném jako uživatel.

Při odesílání zprávy může aplikace určit, jestli se má zpráva odeslat všem připojeným klientům, nebo pouze klientům ověřeným pro daného uživatele.

1. V VS Code otevřete **Negotiate/function.jsna**.

1. Do vlastnosti *userId* vazby *SignalRConnectionInfo* vložte [výraz vazby](../azure-functions/functions-triggers-bindings.md): `{headers.x-ms-client-principal-name}`. Tím se hodnota vlastnosti nastaví na uživatelské jméno ověřeného uživatele. Atribut by teď měl vypadat takto:

    ```json
    {
        "type": "signalRConnectionInfo",
        "name": "connectionInfo",
        "userId": "{headers.x-ms-client-principal-name}",
        "hubName": "chat",
        "direction": "in"
    }
    ```

1. Soubor uložte.


### <a name="deploy-function-app-to-azure"></a>Nasazení aplikace Function App do Azure

1. Otevřete paletu příkazů VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`) a vyberte **Azure Functions: Deploy to Function App** (Azure Functions: Nasadit do aplikace Function App).

1. Po zobrazení výzvy zadejte tyto informace.

    | Název | Hodnota |
    |---|---|
    | Složka k nasazení | Vyberte hlavní složku projektu. |
    | Předplatné | Vyberte své předplatné. |
    | Aplikace funkcí | Vyberte **Vytvořit novou aplikaci funkcí**. |
    | Název aplikace funkcí | Zadejte jedinečný název. |
    | Skupina prostředků | Vyberte stejnou skupinu prostředků jako pro instanci služby SignalR. |
    | Účet úložiště | Vyberte účet úložiště, který jste vytvořili dříve. |

    V Azure se vytvoří nová aplikace funkcí a spustí se nasazení. Počkejte, až se nasazení dokončí.

### <a name="upload-function-app-local-settings"></a>Nahrání místního nastavení aplikace funkcí

1. Otevřete paletu příkazů VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Vyhledejte a vyberte příkaz **Azure Functions: Upload local settings** (Azure Functions: Nahrát místní nastavení).

1. Po zobrazení výzvy zadejte tyto informace.

    | Název | Hodnota |
    |---|---|
    | Soubor místního nastavení | local.settings.json |
    | Předplatné | Vyberte své předplatné. |
    | Aplikace funkcí | Vyberte dříve nasazenou aplikaci funkcí. |

Místní nastavení se nahraje do aplikace funkcí v Azure. Pokud se zobrazí výzva k potvrzení přepsání stávajícího nastavení, vyberte **Ano všem**.


### <a name="enable-app-service-authentication"></a>Povolení ověřování pomocí služby App Service

Ověřování pomocí služby App Service podporuje ověřování přes Azure Active Directory, Facebook, Twitter, účet Microsoft a Google.

1. Otevřete paletu příkazů VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Vyhledejte a vyberte příkaz **Azure Functions: Open in portal** (Azure Functions: Otevřít na portálu).

1. Výběrem předplatného a názvu aplikace funkcí otevřete aplikaci funkcí na webu Azure Portal.

1. V aplikaci Function App, která se otevřela na portálu, najděte kartu **funkce platformy** , vyberte **ověřování/autorizace**.

1. Nastavte ověřování pomocí služby App Service na **Zapnuto**.

1. V části **Akce, která se má provést, když požadavek nebude ověřený** vyberte Přihlásit se přes {zprostředkovatel přihlašování, kterého jste vybrali dříve}.

1. V části **Povolené externí adresy URL pro přesměrování** zadejte adresu URL primárního webového koncového bodu vašeho účtu úložiště, kterou jste si poznamenali dříve.

1. Dokončete konfiguraci podle pokynů v dokumentaci vybraného zprostředkovatele přihlašování.

    - [Azure Active Directory](../app-service/configure-authentication-provider-aad.md)
    - [Facebook](../app-service/configure-authentication-provider-facebook.md)
    - [Twitter](../app-service/configure-authentication-provider-twitter.md)
    - [účet Microsoft](../app-service/configure-authentication-provider-microsoft.md)
    - [Google](../app-service/configure-authentication-provider-google.md)

### <a name="update-the-web-app"></a>Aktualizace webové aplikace

1. Na webu Azure Portal přejděte na stránku Přehled aplikace funkcí.

1. Zkopírujte adresu URL aplikace funkcí.

    ![Získání adresy URL](media/signalr-tutorial-authenticate-azure-functions/signalr-get-url.png)

1. Ve VS Code otevřete soubor **index.html** a nahraďte hodnotu `apiBaseUrl` adresou URL aplikace funkcí.

1. Pro aplikaci je možné nakonfigurovat ověřování přes Azure Active Directory, Facebook, Twitter, účet Microsoft nebo Google. Nastavením hodnoty `authProvider` vyberte zprostředkovatele ověřování, kterého budete používat.

1. Soubor uložte.

### <a name="deploy-the-web-application-to-blob-storage"></a>Nasazení webové aplikace do úložiště objektů blob

K hostování webové aplikace využijeme funkci statického webu ve službě Azure Blob Storage.

1. Otevřete paletu příkazů VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Vyhledejte a vyberte příkaz **Azure Functions: Deploy to Static Website** (Azure Functions: Nasadit statický web).

1. Zadejte tyto hodnoty:

    | Název | Hodnota |
    |---|---|
    | Předplatné | Vyberte své předplatné. |
    | Účet úložiště | Vyberte účet úložiště, který jste vytvořili dříve. |
    | Složka k nasazení | Vyberte **Procházet** a vyberte složku *obsahu* . |

Soubory ve složce *obsahu* by nyní měly být nasazeny na statický Web.

### <a name="enable-function-app-cross-origin-resource-sharing-cors"></a>Povolení sdílení prostředků mezi zdroji (CORS) v aplikaci funkcí

Přestože soubor **local.settings.json** obsahuje nastavení CORS, toto nastavení se nerozšíří do aplikace funkcí v Azure. Musíte ho nastavit samostatně.

1. Otevřete aplikaci funkcí na webu Azure Portal.

1. Na kartě **funkce platformy** vyberte **CORS**.

    ![Vyhledání CORS](media/signalr-tutorial-authenticate-azure-functions/signalr-find-cors.png)

1. V části *Povolené zdroje* přidejte položku s *primárním koncovým bodem* statického webu jako hodnotu (Odebrat koncovou */* ).

1. Aby sada funkcí Signal JavaScript SDK volala aplikaci Function App z prohlížeče, musí být povolená podpora pro přihlašovací údaje v CORS. Zaškrtněte políčko Povolit přístup-řízení – povolit-pověření.

    ![Povolení přístupu-řízení-povolit-pověření](media/signalr-tutorial-authenticate-azure-functions/signalr-cors-credentials.png)

1. Kliknutím na **Uložit** uložte nastavení CORS.

### <a name="try-the-application"></a>Vyzkoušení aplikace

1. V prohlížeči přejděte na primární webový koncový bod účtu úložiště.

1. Vyberte **Login** (Přihlášení) a ověřte se přes vybraného zprostředkovatele ověřování.

1. Veřejné zprávy můžete odesílat zadáním zprávy do hlavního okna chatu.

1. Soukromé zprávy můžete odesílat kliknutím na uživatelské jméno v historii chatu. Tyto zprávy obdrží pouze vybraný příjemce.

Blahopřejeme! Nasadili jste chatovací aplikaci bez serveru, která funguje v reálném čase.

![Ukázka](media/signalr-tutorial-authenticate-azure-functions/signalr-serverless-chat.gif)

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/asrs/qsauth)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit prostředky vytvořené v tomto kurzu, pomocí webu Azure Portal odstraňte skupinu prostředků.

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/asrs/qsauth)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak používat Azure Functions se službou Azure SignalR. Přečtěte si více o vytváření bezserverových aplikací fungujících v reálném čase s využitím vazeb služby SignalR pro Azure Functions.

> [!div class="nextstepaction"]
> [Sestavování aplikací v reálném čase pomocí Azure Functions](signalr-concept-azure-functions.md)

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/asrs/qsauth)